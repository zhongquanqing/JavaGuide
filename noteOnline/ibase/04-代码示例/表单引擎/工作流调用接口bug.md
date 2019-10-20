# 工作流调用接口

<div style="float:right">

|作者|日期|
|----|---|
|钟泉清|2019年4月11日|
禅道(任务)：31194
</div>

##接口描述

[前提]

根据传入的条件更新数据库数据并同步更新对应字段的mongodb集合，支持同时更新多个表的多个字段

/workflowWebService/public/updateDataBaseByCondition?conditionJson={"JOB_BASE.JID":"201807260020"}&&updateDataJson={"JOB_BASE.REGTYPE":"待受理"}

或

/workflowWebService/public/updateDataBaseByCondition?conditionJson=[{"JOB_BASE.JID":"201807260020"},{"JOB_BASE.JID":"201807260022"}]&&updateDataJson={"JOB_BASE.REGTYPE":"待受理"}

conditionJson：需要过滤的数据条件，json格式字符串，可多个字段作为查询条件。

例如：

{"JOB_BASE.JID":"201807260020"}

若需要批量更新多条记录，则需要用json数组格式传入如下:

[{"JOB_BASE.JID":"201807260020"},{"JOB_BASE.JID":"201807260022"}]，

每个{"JOB_BASE.JID":"201807260022"}为一条记录。

updateDataJson:需要更新的数据，json格式字符串，可多个字段同时更新。

例如：

{"JOB_BASE.JID":"201807260020","JOB_BASE.REGTYPE":"待受理"}
 
返回格式：

{"code":代码0表示执行完成,"msg":"提示消息","result":null}

成功：{"code":0,"msg":"执行完成"}

参数格式有误：{"code":-1,"msg":"传入的参数格式不正确"}

更新条件为空：{"code":-2,"msg":"更新条件不允许为空"}

该接口支持根据更新条件更新对应的表字段数据并同步更新对应字段的mongodb集合，其中conditionJson为更新条件，json格式的字符串，

更新单条记录格式例如：

{"JOB_BASE.JID":"201807260020"}

批量更新多条记录如下:

[{"JOB_BASE.JID":"201807260020"},{"JOB_BASE.JID":"201807260022"}]

JOB_BASE为表名，JID为字段名。

updateDataJson为需要更新的数据，也是jsonjson格式的字符串，
格式例如：

{"JOB_BASE.REGTYPE":"已受理"},JOB_BASE为表名，REGTYPE为字段名，

更新到数据库后，会同步更新到对应存在的mongodb记录中，注意，若是要更新日期，则日期格式必须为yyyy-MM-dd HH:mm:ss或yyyy-MM-dd。

## 问题描述
在进度查询模块中，配置了缴费通知书号显示列，但是系统显示不出来问题。
原因：是因为在工作流设计器那里设置摘要字段时（设置了就会在mongdb中新
建一个字段），只添加了缴费通知书号，而1.0传参是传了两个参数，一个jid和
通知单号。

##解决办法
解决办法：在表单中添加一个jid的隐藏控件，然后在工作流设计器中，将这两
个字段全部设置为摘要字段。