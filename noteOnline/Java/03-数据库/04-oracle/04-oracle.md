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

#### 1.6.2在dba用户下创建定时任务（如system）
      
      --增加一个job
        declare  
          tm_job number;  
        begin  
          sys.dbms_job.submit(tm_job, --任务名称 自动生成 是整形
                              'AUTO_EXTEND_TABLESPACE;',--执行的过程名 
                              sysdate,--执行时间  
                              'TRUNC(sysdate+1)+22/24');--下次执行时间 每天晚上10点
        end;  
    

    