# 第一章 Monogdb
##  1.1. Monogdb数据库迁移

### 1.1.1.Liunx服务器monogdb数据库迁移
    1.root用户登录服务器；
    2.切换到monogdb的bin目录：cd /usr/local/mongodb，该目录按照实际目录进行调整；
    3.执行导出命令，执行命令前，需切换到monogdb的bin目录：mongodump -d mongodb -o /opt/mongo_ibase20181101
    备注：mongodump导出命令，mongodb是数据库名称（数据库名称要区分大小写），/opt/mongo_ibase20181101导出文件路径。
    4.执行导入命令，执行命令前，需切换到monogdb的bin目录（导入前，必须数据库已存在）：mongorestore -d mongodb /opt/mongo_ibase20181101
    备注：mongorestore导入命令，mongodb是数据库名称（数据库名称要区分大小写），/opt/mongo_ibase20181101导出文件路径。导入前，一定要确保mongodb已存在
    
### 1.1.2.Windows服务器monogdb数据库迁移
    1.administrator用户登录服务器；
    2.cmd进入dos环境；
    3.切换到monogdb的bin目录：切换到monogdb的bin目录，该目录按照实际目录进行调整；
    执行导出命令，执行命令前，需切换到monogdb的bin目录：mongodump -d ysl -o D:\opt\20190117
    备注：mongodump导出命令，ysl是数据库名称（数据库名称要区分大小写），D:\opt\20190117导出文件路径。
    4.执行导入命令，执行命令前，需切换到monogdb的bin目录（导入前，必须数据库已存在）：mongorestore -d ysl D:\opt\20190117
    备注：mongodump导出命令，ysl是数据库名称（数据库名称要区分大小写），D:\opt\20190117导出文件路径。
    
    
### 1.1.3.mongoDB创建用户（替换中文地方即可）
        db.createUser( {user: "用户名",pwd: "密码",roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]})
### 1.1.4.修改用户密码：
    方法1：db.changeUserPassword("IBASE_DEV_YC","sa123");

    方法2：db.updateUser("usertest",{pwd:"changepass1"})；
### 1.1.5单条导入mongdb数据    
    mongoimport --db ibase_dev_yc --collection job_create --file  
    C:\Users\Administrator\Desktop\茂名备份0423\mongodb\ibase_mm\job_create.json       
### 1.1.6多条导入一窗mongdb数据   


## 1.2. mongodb语句练习
### 1.2.1. 数据库练习脚本，将此脚本插入到mongodb数据库中，供下面语句的练习
    db.users.drop();
    var user1 = {
            "username" : "lison",
            "country" : "china",
            "address" : {
                    "aCode" : "411000",
                    "add" : "长沙"
            },
            "favorites" : {
                    "movies" : ["杀破狼2","战狼","雷神1"],
                    "cites" : ["长沙","深圳","上海"]
            },
            "age" : 18,
    	   "salary":NumberDecimal("18889.09"),
           "lenght" :1.79
    	    
    };
    var user2 = {
            "username" : "james",
            "country" : "English",
            "address" : {
                    "aCode" : "311000",
                    "add" : "地址"
            },
            "favorites" : {
                    "movies" : ["复仇者联盟","战狼","雷神1"],
                    "cites" : ["西安","东京","上海"]
            },
            "age" : 24,
           "salary":NumberDecimal("7889.09"),
           "lenght" :1.35
    };
    var user3 ={
            "username" : "deer",
            "country" : "japan",
            "address" : {
                    "aCode" : "411000",
                    "add" : "长沙"
            },
            "favorites" : {
                    "movies" : ["肉蒲团","一路向西","倩女幽魂"],
                    "cites" : ["东莞","深圳","东京"]
            },
            "age" : 22,
           "salary":NumberDecimal("6666.66"),
           "lenght" :1.85
    };
    var user4 =
    {
            "username" : "mark",
            "country" : "USA",
            "address" : {
                    "aCode" : "411000",
                    "add" : "长沙"
            },
            "favorites" : {
                    "movies" : ["蜘蛛侠","钢铁侠","蝙蝠侠"],
                    "cites" : ["青岛","东莞","上海"]
            },
            "age" : 20,
           "salary":NumberDecimal("6398.22"),
           "lenght" :1.77
    };
    
    var user5 =
    {
            "username" : "peter",
            "country" : "UK",
            "address" : {
                    "aCode" : "411000",
                    "add" : "TEST"
            },
            "favorites" : {
                    "movies" : ["蜘蛛侠","钢铁侠","蝙蝠侠"],
                    "cites" : ["青岛","东莞","上海"]
            },
           "salary":NumberDecimal("1969.88")
    };
    
    db.users.insert(user1);
    db.users.insert(user2);
    db.users.insert(user3);
    db.users.insert(user4);
    db.users.insert(user5);

### 1.2.2. 基础语句练习
     查询  
     1.查询喜欢的城市包含东莞和东京的user
     select * from users  where favorites.cites has "东莞"、"东京“
     db.users.find({ "favorites.cites" : { "$all" : [ "东莞" , "东京"]}})
     2.查询国籍为英国或者美国，名字中包含s的user
     db.users.find({ "$and" : [ { "username" : { "$regex" : ".*s.*"}} , { "$or" : [ { "country" : "English"} , { "country" : "USA"}]}]})
     修改 
     3.把lison的年龄修改为6岁
     update  users  set age=6 where username = lison' 
     db.users.updateMany({ "username" : "lison"},{ "$set" : { "age" : 6}},true)

     4.喜欢的城市包含东莞的人，给他喜欢的电影加入“小电影2”“小电影3”
     update users  set favorites.movies add "小电影2 ", "小电影3" where favorites.cites  has "东莞"
     db.users.updateMany({ "favorites.cites" : "东莞"}, { "$addToSet" : { "favorites.movies" : { "$each" : [ "小电影2 " , "小电影3"]}}},true)
     
     删除
     5.删除名字为lison的user
     delete from users where username = ‘lison’
     db.users.deleteMany({ "username" : "lison"} )

     6.删除年龄大于8小于25的user
     delete from users where age >8 and age <25
     db.users.deleteMany({"$and" : [ {"age" : {"$gt": 8}} , {"age" : {"$lt" : 25}}]})

### 1.2.3.查询选择器
    <table>
        <tr>
            <td>运算符类型</td>
            <td>运算符</td>
            <td>描述</td>
        <tr>
        <tr>
            <td rowspan="10">范围</td>
        <tr>
        <tr>
            <td>$eq</td>
            <td>等于</td>              		
        </tr>
        <tr>
            <td>$lt</td>
            <td>小于</td>    
        </tr>
        <tr>
            <td>$gt</td>
            <td>大于</td> 
        </tr>
        <tr>
             <td>$lte</td>
             <td>小于等于</td> 
        </tr>
        <tr>
            <td>$gte</td>
            <td>大于等于</td>              		
        </tr>
        <tr>
            <td>$in</td>
            <td>判断元素是否在指定的集合范围里</td>    
        </tr>
        <tr>
            <td>$all</td>
            <td>判断数组中是否包含某几个元素,无关顺序</td> 
        </tr>
        <tr>
            <td>$nin</td>
            <td>判断元素是否不在指定的集合范围里</td> 
        </tr>
    
    </table>

	布尔运算
    $not	不匹配结果
    $or	有一个条件成立则匹配
    $nor	所有条件都不匹配
    $and	所有条件都必须匹配
    $exists	判断元素是否存在
    其他	.	子文档匹配
    $regex	正则表达式匹配
    
#### 1.2.2.2 查询选择器实战

    1.查询姓名为lison、mark和james这个范围的人
    db.users.find({"username":{"$in":["lison", "mark", "james"]}}).pretty()
    2.判断文档有没有length字段
    db.users.find({"lenght":{"$exists":true}}).pretty()
    3.查询高度小于1.77或者没有身高的人
    db.users.find({"lenght":{"$not":{"$gte":1.77}}}).pretty()

#### 1.2.2.2 查询选择
    映射
    1.字段选择并排除其他字段
    db.users.find({},{'username':1})
    2.字段排除
    db.users.find({},{'username':0})
   
    排序
    1.按照username排序
    sort()：db.users.find().sort({"username":1}).pretty()
    1：升序   -1：降序
    
    跳过和限制
    1.skip(n)：跳过n条数据
    2.limit(n)：限制n条数据
    db.users.find().sort({"username":1}).limit(2).skip(2)
    
    查询唯一值
    distinct()：查询指定字段(username)的唯一值
    e.g：db.users.distinct("username")
    
### 1.2.3 字符串数组选择查询
    1.数组单元素查询
     查询数组中包含"蜘蛛侠"
     db.users.find({"favorites.movies":"蜘蛛侠"})
       
    
    2.数组精确查找
    查询数组等于[ “杀破狼2”, “战狼”, “雷神1” ]的文档，严格按照数量、顺序；
    db.users.find({"favorites.movies":[ "杀破狼2", "战狼", "雷神1" ]},{"favorites.movies":1})
    
    3.数组多元素查询
    1)查询数组包含[“雷神1”, “战狼” ]的文档，跟顺序无关，跟数量有关
    db.users.find({"favorites.movies":{"$all":[ "雷神1", "战狼" ]}},{"favorites.movies":1})
    
    2)查询数组包含[“雷神1”, “战狼” ]中任意一个的文档，跟顺序无关，跟数量无关
    db.users.find({"favorites.movies":{"$in":[ "雷神1", "战狼" ]}},{"favorites.movies":1})
    
    4.索引查询
    1)查询数组中第一个为"妇联4"的文档
    db.users.find({"favorites.movies.0":"妇联4"},{"favorites.movies":1})
    
    5.返回数组子集
    2)$slice可以取两个元素数组,分别表示跳过和限制的条数；
    db.users.find({},{"favorites.movies":{"$slice":[1,2]},"favorites":1})

### 1.2.4 对象数组选择查询

    1. 单元素查询
       db.users.find({"comments":{
                            "author" : "lison6",
                            "content" : "lison评论6","commentTime" : ISODate("2017-06-06T00:00:00Z")}})
       备注：对象数组精确查找
       
    2.查找lison1 或者 lison12评论过的user （$in查找符） 
    db.users.find({"comments.author":{"$in":["lison1","lison12"]}}).pretty()
       备注：跟数量无关，跟顺序无关；
       
    3.查找lison1 和 lison12都评论过的user
    db.users.find({"comments.author":{"$all":["lison12","lison1"]}}).pretty()
       备注：跟数量有关，跟顺序无关；
       
    4.查找lison5评语为包含“苍老师”关键字的user（$elemMatch查找符） 
    db.users.find({"comments":{"$elemMatch":{"author" : "lison5",
     "content" : { "$regex" : ".*苍老师.*"}}}}) .pretty()
        备注：数组中对象数据要符合查询对象里面所有的字段，$全元素匹配，和顺序无关；
        
### 1.2.5 mongoDB实现关联查询

    单个bson文档最大不能超过16M；当文档超过16M的时候，就应该考虑使用引用（DBRef）了，在主表里存储一个id值，指向另一个表中的 id 值。
    DBRef语法：{ "$ref" : <value>, "$id" : <value>, "$db" : <value> }
    $ref：引用文档所在的集合的名称；
    $id：所在集合的_id字段值；
    $db：可选，集合所在的数据库实例；
   
    使用dbref脚本示例：
    var lison = db.users.findOne({"username":"lison"});
    var dbref = lison.comments;
    db[dbref.$ref].findOne({"_id":dbref.$id})

### 1.2.6 mongoDB聚合
    $project：投影，指定输出文档中的字段；
    $match：用于过滤数据，只输出符合条件的文档。$match使用MongoDB的标准查询操作
    $limit：用来限制MongoDB聚合管道返回的文档数。
    $skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。
    $unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
    $group：将集合中的文档分组，可用于统计结果。
    $sort：将输入文档排序后输出
    
    1.查询2015年4月3号之前，每个用户每个月消费的总金额，并按用户名进行排序：
     db.orders.aggregate([
           {"$match":{ "orderTime" : { "$lt" : new Date("2015-04-03T16:00:00.000Z")}}}, 
    {"$group":{"_id":{"useCode":"$useCode","month":{"$month":"$orderTime"}},"total":{"$sum":"$price"}}}, 
    {"$sort":{"_id":1}}    
    ])
    
## 2.1创建数据库


    
    
    
    
    
    
    
    

# 第二章 概述
### 1.1 什么是monodb
    Mon2


    