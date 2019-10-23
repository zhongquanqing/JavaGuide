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



    

    