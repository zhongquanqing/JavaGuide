#   第一章 ORCLE的概述
##  1.1.数据基本配置信息
    jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
    jdbc.url=jdbc:oracle:thin:@//192.168.10.66:1521/orcl
    jdbc.urlParam=
    jdbc.validationQuery=select 1 from dual
    hibernate.dialect=org.hibernate.dialect.Oracle10gDialect
    hibernate.schema=IBASE_DEV_TEST
    jdbc.user=IBASE_DEV_TEST 
    jdbc.pass=ibase_dev
##  1.2.数据库的导入导出
    导出：expdp zhjg_job/sa123@orcl dumpfile= zhjg_job1017.dmp   directory=DATA_PUMP_DIR
    导导入:
    impdp zhjg_job/sa123@192.168.10.32/orcl directory=DMPPATH  dumpfile=ZHJG_JOB0219.DMP table_exists_action=replace REMAP_SCHEMA=zhjg_job:zhjg_job
    备注：select * from dba_directories 查看导入的位置
    导入时候F:\DATABASE_BACKUP   将导出的数据库ZHJG_JOB0219.DMP放在这个目录下面，在导入
## 1.3.查询表的所有字段名和数据类型
    查询语法：
    select A.COLUMN_NAME,A.DATA_TYPE  from user_tab_columns Awhere TABLE_NAME='表名'
## 1.4.分页
    select * 
    from (select t.*, rownum rn
            from (select *
                    from T_BASE_PROVINCE
                   order by id asc) t
            where rownum <= page*size)
    where rn > (page-1)*size;
## 1.5数据库字段数据备份
    第一步：把原字段换个名字
    alter table JOBA_SMSMSG rename column  MTITLE to MTITLE1;
    第二步：在表中添加一个原字段名字C_009700010003 ，并把类型定义自己想改变的类型， 此条是定义VARCHAR2类型
    alter table JOBA_SMSMSG add MTITLE VARCHAR2(200);
    第三步(可省略)：将字段名称进行备注，以免以后忘记字段名称。
    comment on column T_00970001.C_009700010003 is '处罚事由';
    第四步：这条语句是把备份的C_0097000100031字段内容 添加到新建字段C_009700010003 中来，这条语句就是把CLOB类型的数据转换成varchar2类型在插入到新定义的C_009700010003
    update JOBA_SMSMSG set MTITLE = dbms_lob.substr(MTITLE1,200);
    第五步：把备份字段C_0097000100031去掉
    alter table JOBA_SMSMSG drop column MTITLE1;


### 1.6 oracle数据定时任务检查表空间，总大小超过30G自动附加DBF文件
    一、在dba用户下创建存储过程（如system）
    create or replace procedure auto_extend_tablespace AS
      v_sql         VARCHAR2(500);
      v_n           number(2);
      v_name        VARCHAR2(64);
      hand_char     varchar2(1);
      datafile_path VARCHAR2(64);
      temp_num      varchar2(4);
      temp_filename varchar2(16);
    BEGIN
      for x in (select t.tablespace_name,
                       sum(t.bytes) / 1024 / 1024 totalspace,
                       count(1) v_n
                  from dba_data_files t
                 group by t.tablespace_name) loop
        --表空间总大小大于或者等于30G 自动增加dbf文件 30720
        if (x.totalspace >= 30720 * v_n) then
          --取到文件名的最后编号
          select to_char(count(*) + 1)
            into temp_num
            from dba_data_files
           where tablespace_name = x.tablespace_name;
          --构造数据文件名称的部分，例如要新加的数据文件是 ysjl003.dbf 该语句构造出ysjl003 这部分
          select x.tablespace_name || lpad(temp_num, 2, '0')
            into temp_filename
            from dual;
          select FILE_NAME
            into v_name
            from (select *
                    from dba_data_files
                   where tablespace_name = x.tablespace_name
                   order by file_id desc)
           where rownum = 1;
          --判断第一个字符是否是 "/"--linux下数据文件的开头
          select substr(v_name, 1, 1) into hand_char from dual;
          if hand_char = '/' then
            --linux 下 datafile_path :='/'; --先构造LINUX的文件位置的 开头 '/'
            -- +1 linux下不能有这个+1 否则取到的位置不对 )
            for i in (select regexp_substr(v_name, '([^(\\|\/)]+)', 1, level) part_path
                        from dual
                      connect by level < (length(v_name) -
                                 length(replace(v_name, '/', '')))) loop
              select datafile_path || i.part_path || '/'
                into datafile_path
                from dual;
            end loop;
            v_sql := 'alter tablespace ' || x.tablespace_name ||
                     ' add datafile ''' || datafile_path || temp_filename ||
                     '.dbf' ||
                     ''' SIZE 512M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED';
            --dbms_output.put_line(v_sql);
            execute immediate v_sql;
            --每次内循环完 初始化一次
            datafile_path := '/';
          else
            --window 下
            for i in (select regexp_substr(v_name, '([^(\\|\/)]+)', 1, level) part_path
                        from dual
                      connect by level < (length(v_name) -
                                 length(replace(v_name, '\', ''))) + 1) loop
              select datafile_path || i.part_path || '\'
                into datafile_path
                from dual;
            end loop;
            v_sql := 'alter tablespace ' || x.tablespace_name ||
                     ' add datafile ''' || datafile_path || temp_filename ||
                     '.dbf' ||
                     ''' SIZE 512M AUTOEXTEND ON NEXT 256M MAXSIZE UNLIMITED';
            dbms_output.put_line(v_sql);
            execute immediate v_sql;
            --每次内循环完 初始化一次
            datafile_path := null;
          end if;
        end if;
      end loop;
    exception
      when others then
        rollback;
    END;

#### 1.6.2 在dba用户下创建定时任务（如system）
      
      --增加一个job
        declare  
          tm_job number;  
        begin  
          sys.dbms_job.submit(tm_job, --任务名称 自动生成 是整形
                              'AUTO_EXTEND_TABLESPACE;',--执行的过程名 
                              sysdate,--执行时间  
                              'TRUNC(sysdate+1)+22/24');--下次执行时间 每天晚上10点
        end;  
    

##  2.0 sql（Oracle）语句练习题50道
    create table student(
    sno varchar2(10) primary key,
    sname varchar2(20),
    sage number(2),
    ssex varchar2(5)
    );
    create table teacher(
    tno varchar2(10) primary key,
    tname varchar2(20)
    );
    create table course(
    cno varchar2(10),
    cname varchar2(20),
    tno varchar2(20),
    constraint pk_course primary key (cno,tno)
    );
    create table sc(
    sno varchar2(10),
    cno varchar2(10),
    score number(4,2),
    constraint pk_sc primary key (sno,cno)
    );
    /*******初始化学生表的数据******/
    insert into student values ('s001','张三',23,'男');
    insert into student values ('s002','李四',23,'男');
    insert into student values ('s003','吴鹏',25,'男');
    insert into student values ('s004','琴沁',20,'女');
    insert into student values ('s005','王丽',20,'女');
    insert into student values ('s006','李波',21,'男');
    insert into student values ('s007','刘玉',21,'男');
    insert into student values ('s008','萧蓉',21,'女');
    insert into student values ('s009','陈萧晓',23,'女');
    insert into student values ('s010','陈美',22,'女');
    commit;
    /******************初始化教师表***********************/
    insert into teacher values ('t001', '刘阳');
    insert into teacher values ('t002', '谌燕');
    insert into teacher values ('t003', '胡明星');
    commit;
    /***************初始化课程表****************************/
    insert into course values ('c001','J2SE','t002');
    insert into course values ('c002','Java Web','t002');
    insert into course values ('c003','SSH','t001');
    insert into course values ('c004','Oracle','t001');
    insert into course values ('c005','SQL SERVER 2005','t003');
    insert into course values ('c006','C#','t003');
    insert into course values ('c007','JavaScript','t002');
    insert into course values ('c008','DIV+CSS','t001');
    insert into course values ('c009','PHP','t003');
    insert into course values ('c010','EJB3.0','t002');
    commit;
    /***************初始化成绩表***********************/
    insert into sc values ('s001','c001',78.9);
    insert into sc values ('s002','c001',80.9);
    insert into sc values ('s003','c001',81.9);
    insert into sc values ('s004','c001',60.9);
    insert into sc values ('s001','c002',82.9);
    insert into sc values ('s002','c002',72.9);
    insert into sc values ('s003','c002',81.9);
    insert into sc values ('s001','c003','59');
    commit;
     
     
    练习：
    注意：以下练习中的数据是根据初始化到数据库中的数据来写的SQL 语句，请大家务必注意。
    1、查询“c001”课程比“c002”课程成绩高的所有学生的学号；
    select a.* from
    (select * from sc a where a.cno='c001') a,
    (select * from sc b where b.cno='c002') b
    where a.sno=b.sno and a.score > b.score;
    select * from sc a
    where a.cno='c001'
    and  exists(select * from sc b where b.cno='c002' and a.score>b.score
    and a.sno = b.sno)
    2、查询平均成绩大于60 分的同学的学号和平均成绩；
    select sno,avg(score) from sc  group by sno having avg(score)>60;
    3、查询所有同学的学号、姓名、选课数、总成绩；
    select s.sno,sname,sum(sc.score),count(cno)  from student s left join sc on  s.sno=sc.sno group by s.sno ,sname
    4、查询姓“刘”的老师的个数；
    select count(*) from teacher where tname like '刘%';
    5、查询没学过“谌燕”老师课的同学的学号、姓名；
    ls1:select a.sno,a.sname from student a
    where a.sno
        not in
        (select distinct s.sno
         from sc s,
              (select c.*
               from course c ,
                   (select tno
                    from teacher t
                    where tname='谌燕')t
               where c.tno=t.tno) b
          where s.cno = b.cno )；
        
    ls2: select * from student st where st.sno not in
        (select distinct sno from sc s join course c on s.cno=c.cno
        join teacher t on c.tno=t.tno where tname='谌燕')
    6、查询学过“c001”并且也学过编号“c002”课程的同学的学号、姓名；
    ls1:select s.sno,sname from student s 
       where s.sno  in (select a.sno from sc a join sc b on a.sno=b.sno where a.cno='c001' and  b.cno='c002' )
    
    ls2:select st.* from sc a
        join sc b on a.sno=b.sno
        join student st
        on st.sno=a.sno
        where a.cno='c001' and b.cno='c002' and st.sno=a.sno;
    7、查询学过“谌燕”老师所教的所有课的同学的学号、姓名；
     select st.* from student st join sc s on st.sno=s.sno
        join course c on s.cno=c.cno
        join teacher t on c.tno=t.tno
        where t.tname='谌燕'
    8、查询课程编号“c002”的成绩比课程编号“c001”课程低的所有同学的学号、姓名；
     select * from student st
        join sc a on st.sno=a.sno
        join sc b on st.sno=b.sno
        where a.cno='c002' and b.cno='c001' and a.score < b.score
    9、查询所有课程成绩小于60 分的同学的学号、姓名；
      select st.*,s.score from student st
        join sc s on st.sno=s.sno
        join course c on s.cno=c.cno
        where s.score <60
    10、查询没有学全所有课的同学的学号、姓名；
    select stu.sno,stu.sname,count(sc.cno) from student stu
        left join sc on stu.sno=sc.sno
        group by stu.sno,stu.sname
        having count(sc.cno)<(select count(distinct cno)from course)
        ===================================
        select * from student where sno in
        (select sno from
                (select stu.sno,c.cno from student stu
                cross join course c
                minus
                select sno,cno from sc)
        )
    11、查询至少有一门课与学号为“s001”的同学所学相同的同学的学号和姓名；
    select st.* from student st,
        (select distinct a.sno from
        (select * from sc) a,
        (select * from sc where sc.sno='s001') b
        where a.cno=b.cno) h
        where st.sno=h.sno and st.sno<>'s001'
    12、查询至少学过学号为“s001”同学所有一门课的其他同学学号和姓名；
     select * from sc
        left join student st
        on st.sno=sc.sno
        where sc.sno<>'s001'
        and sc.cno in
        (select cno from sc
        where sno='s001')
    13、把“SC”表中“谌燕”老师教的课的成绩都更改为此课程的平均成绩；
     update sc c set score=(select avg(c.score)  from course a,teacher b
                                    where a.tno=b.tno
                                    and b.tname='谌燕'
                                    and a.cno=c.cno
                                    group by c.cno)
        where cno in(
        select cno from course a,teacher b
        where a.tno=b.tno
        and b.tname='谌燕')
    14、查询和“s001”号的同学学习的课程完全相同的其他同学学号和姓名；
     select* from sc where sno<>'s001'
        minus
        (
        select* from sc
        minus
        select * from sc where sno='s001'
        )
    15、删除学习“谌燕”老师课的SC 表记录；
    delete from sc
        where sc.cno in
        (
        select cno from course c
        left join teacher t on  c.tno=t.tno
        where t.tname='谌燕'
        )
    16、向SC 表中插入一些记录，这些记录要求符合以下条件：没有上过编号“c002”课程的同学学号、“c002”号课的平均成绩；
    insert into sc (sno,cno,score)
        select distinct st.sno,sc.cno,(select avg(score)from sc where cno='c002')
        from student st,sc
        where not exists
        (select * from sc where cno='c002' and sc.sno=st.sno) and sc.cno='c002';
    17、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分
      select cno ,max(score),min(score) from sc group by cno;
    18、按各科平均成绩从低到高和及格率的百分数从高到低顺序
     select cno,avg(score),sum(case when score>=60 then 1 else 0 end)/count(*)
        as 及格率
        from sc group by cno
        order by avg(score) , 及格率desc
    19、查询不同老师所教不同课程平均分从高到低显示
     select max(t.tno),max(t.tname),max(c.cno),max(c.cname),c.cno,avg(score) from sc , course c,teacher t
        where sc.cno=c.cno and c.tno=t.tno
        group by c.cno
        order by avg(score) desc
    20、统计列印各科成绩,各分数段人数:课程ID,课程名称,[100-85],[85-70],[70-60],[ <60]
     select sc.cno,c.cname,
        sum(case  when score between 85 and 100 then 1 else 0 end) AS "[100-85]",
        sum(case  when score between 70 and 85 then 1 else 0 end) AS "[85-70]",
        sum(case  when score between 60 and 70 then 1 else 0 end) AS "[70-60]",
        sum(case  when score <60 then 1 else 0 end) AS "[<60]"
        from sc, course c
        where  sc.cno=c.cno
        group by sc.cno ,c.cname;
    21、查询各科成绩前三名的记录:(不考虑成绩并列情况)
    select * from
        (select sno,cno,score,row_number()over(partition by cno order by score desc) rn from sc)
        where rn<4
    22、查询每门课程被选修的学生数
    select cno,count(sno)from sc group by cno;
    23、查询出只选修了一门课程的全部学生的学号和姓名
     select sc.sno,st.sname,count(cno) from student st
        left join sc
        on sc.sno=st.sno
        group by st.sname,sc.sno having count(cno)=1;
    24、查询男生、女生人数
     select ssex,count(*)from student group by ssex;
    25、查询姓“张”的学生名单
      select * from student where sname like '张%';
    26、查询同名同性学生名单，并统计同名人数
    select sname,count(*)from student group by sname having count(*)>1;
    27、1981 年出生的学生名单(注：Student 表中Sage 列的类型是number)
    select sno,sname,sage,ssex from student t where to_char(sysdate,'yyyy')-sage =1988
    28、查询每门课程的平均成绩，结果按平均成绩升序排列，平均成绩相同时，按课程号降序排列
    select cno,avg(score) from sc group by cno order by avg(score)asc,cno desc;
    29、查询平均成绩大于85 的所有学生的学号、姓名和平均成绩
      select st.sno,st.sname,avg(score) from student st
        left join sc
        on sc.sno=st.sno
        group by st.sno,st.sname having avg(score)>85;
    30、查询课程名称为“数据库”，且分数低于60 的学生姓名和分数
     select sname,score from student st,sc,course c
        where st.sno=sc.sno and sc.cno=c.cno and c.cname='Oracle' and sc.score<60
    31、查询所有学生的选课情况；
      select st.sno,st.sname,c.cname from student st,sc,course c
        where sc.sno=st.sno and sc.cno=c.cno;
    32、查询任何一门课程成绩在70 分以上的姓名、课程名称和分数；
      select st.sname,c.cname,sc.score from student st,sc,course c
        where sc.sno=st.sno and sc.cno=c.cno and sc.score>70
    33、查询不及格的课程，并按课程号从大到小排列
    select sc.sno,c.cname,sc.score from sc,course c
        where sc.cno=c.cno and sc.score<60 order by sc.cno desc;
    34、查询课程编号为c001 且课程成绩在80 分以上的学生的学号和姓名；
     select st.sno,st.sname,sc.score from sc,student st
        where sc.sno=st.sno and cno='c001' and score>80;
    35、求选了课程的学生人数
     select count(distinct sno) from sc;
    36、查询选修“谌燕”老师所授课程的学生中，成绩最高的学生姓名及其成绩
     select st.sname,score from student st,sc ,course c,teacher t
        where
        st.sno=sc.sno and sc.cno=c.cno and c.tno=t.tno
        and t.tname='谌燕' and sc.score=
        (select max(score)from sc where sc.cno=c.cno)
    37、查询各个课程及相应的选修人数
       select cno,count(sno) from sc group by cno;
    38、查询不同课程成绩相同的学生的学号、课程号、学生成绩
    select a.* from sc a ,sc b where a.score=b.score and a.cno<>b.cno
    39、查询每门功课成绩最好的前两名
    select * from (
        select sno,cno,score,row_number()over(partition by cno order by score desc) my_rn from sc t
        )
        where my_rn<=2
    40、统计每门课程的学生选修人数（超过10 人的课程才统计）。要求输出课程号和选修人数
    ，查询结果按人数降序排列，若人数相同，按课程号升序排列
     select cno,count(sno) from sc group by cno
        having count(sno)>10
        order by count(sno) desc,cno asc;
    41、检索至少选修两门课程的学生学号
     select sno from sc group by sno having count(cno)>1;
        ||
     select sno from sc group by sno having count(sno)>1;
    42、查询全部学生都选修的课程的课程号和课程名
     select distinct(c.cno),c.cname from course c ,sc
        where sc.cno=c.cno
        ||
     select cno,cname from course c  where c.cno in
            (select cno from sc group by cno)
    43、查询没学过“谌燕”老师讲授的任一门课程的学生姓名
     select st.sname from student st
        where st.sno not in
        (select distinct sc.sno from sc,course c,teacher t
        where sc.cno=c.cno and c.tno=t.tno and t.tname='谌燕')
    44、查询两门以上不及格课程的同学的学号及其平均成绩
    select sno,avg(score)from sc
        where sno in
        (select sno from sc where sc.score<60
        group by sno having count(sno)>1
        ) group by sno
    45、检索“c004”课程分数小于60，按分数降序排列的同学学号
    select sno from sc where cno='c004' and score<90 order by score desc;
    46、删除“s002”同学的“c001”课程的成绩
     delete from sc where sno='s002' and cno='c001';

     
     
    