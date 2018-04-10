---
layout:     post
title:      mongodb安装&极简入门
category: project
description: mongodb安装&极简入门
---

## MongoDB安装（Windows平台）

### 1、MongoDB官网下载
[mongodb官网](https://www.mongodb.com/)

### 2、安装路径问题
MongoDB 的安装路径最好不要太深，因为后面需要经常切换到MongoDB 的 bin 目录执行命令。

### 3、MongoDB的安装、配置
我这边把mongodb安装到 F:\mongodb 目录

cmd切换到F盘，进入mongodb的bin目录： `cd F:\mongodb\bin`
`mongod --logpath "F:\mongodb\data\log\mongodb.log"`
指定数据存放的目录： `mongod --dbpath "F:\mongodb\data\db"`

执行完上述命令，命令行最后会出现类型下面这样的描述，就表示配置成功：
```
2018-04-10T11:53:13.790+0800 I CONTROL  [initandlisten]
2018-04-10T11:53:15.014+0800 I FTDC     [initandlisten] Initializing full-time d
iagnostic data capture with directory 'F:/data/db/diagnostic.data'
2018-04-10T11:53:15.021+0800 I NETWORK  [thread1] waiting for connections on por
t 27017
2018-04-10T11:53:16.032+0800 I FTDC     [ftdc] Unclean full-time diagnostic data
 capture shutdown detected, found interim file, some metrics may have been lost.
 OK
```
接着重新打开一个cmd窗口，进入到mongo的bin目录，输入：mongo
```
F:\mongodb\bin>mongo
MongoDB shell version v3.4.14
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.14
Server has startup warnings:
2018-04-10T11:53:13.786+0800 I CONTROL  [initandlisten]
2018-04-10T11:53:13.787+0800 I CONTROL  [initandlisten] ** WARNING: Access contr
ol is not enabled for the database.
2018-04-10T11:53:13.788+0800 I CONTROL  [initandlisten] **          Read and wri
te access to data and configuration is unrestricted.
2018-04-10T11:53:13.788+0800 I CONTROL  [initandlisten]
2018-04-10T11:53:13.789+0800 I CONTROL  [initandlisten] Hotfix KB2731284 or late
r update is not installed, will zero-out data files.
2018-04-10T11:53:13.790+0800 I CONTROL  [initandlisten]
```
出现上面的内容，说明已经成功连接数据库。

### 4、常见问题（代补充）
如果出现端口占有等情况，在任务管理器中关闭mongo.exe，在bin目录下重新运行：
   `mongod --dbpath "F:\mongodb\data\db"`
   接着，重新打开一个cmd，在bin目录下输入：`mongo`
   就会出现一个shell，大功告成。

## mongodb极简入门
- 创建数据库
`use myshujuku`

- 显示数据库
`show databases`

- 添加一个集合
`db.createCollecton('movie')`

- 显示集合
`show collections`

- 删除集合
`db.movie.drop()`

- 插入数据
***电影名字
导演
主演(可能多个)
类型标签(可能多个)
上映日期
喜欢人数
不喜欢人数
用户评论(可能多个)***
```
db.movie.insert(
 {
   title: 'Forrest Gump', 
   directed_by: 'Robert Zemeckis',
   stars: ['Tom Hanks', 'Robin Wright', 'Gary Sinise'],
   tags: ['drama', 'romance'],
   debut: new Date(1994,7,6,0,0),
   likes: 864367,
   dislikes: 30127,
   comments: [	
      {
         user:'user1',
         message: 'My first comment',
         dateCreated: new Date(2013,11,10,2,35),
         like: 0 
      },
      {
         user:'user2',
         message: 'My first comment too!',
         dateCreated: new Date(2013,11,11,6,20),
         like: 0 
      }
   ]
}
)
```
请注意，这里插入数据之前，我们并不需要先声明movie这个集合里面有哪些项目。我们直接插入就可以了~这一点和SQL不一样，SQL必须先声明一个table里面有哪些列，而MongoDB不需要。
几点需要注意：
1. 不同key-value需要用逗号隔开，而key:value中间是用冒号；
2. 如果一个key有多个value，value要用[]。哪怕当前只有一个value，也加上[]以备后续的添加；
3. 整个“数据块”要用{}括起来；

- 查询全部数据
`db.movie.find().pretty()`
pretty()的作用是规范我们输出的格式，易于阅读。
仔细观察find()的结果，你会发现多了一个叫'_id'的东西，这是数据库自动创建的一个ID号，在同一个数据库里，每个文档的ID号都是不同的。
批量插入的格式是这样的：db.movie.insert([{ITEM1},{ITEM2}])。几部电影的外面需要用[]括起来。

- 条件查询数据
`db.movie.find({'directed_by':'David Fincher'}).pretty()`
`db.movie.find({'directed_by':'David Fincher', 'stars':'Morgan Freeman'}).pretty()`
```
db.movie.find(
{
  $or: 
     [  {'stars':'Robin Wright'}, 
        {'stars':'Morgan Freeman'}
     ]
}).pretty()
```
`db.movie.find({likes:{$lt:200000}}).pretty()`
类似的运算符还有：`$lte`:小于或等于；`$gte`:大于或等于；`$ne`:不等于。

- 如果结果很多而你只想显示其中一部分，可以用limit()和skip()
  前者指明输出的个数，后者指明从第二个结果开始数。比如：
`db.movie.find().limit(2).skip(1).pretty()`
`db.movie.find({'tags':'drama'},{'debut':1,'title':1}).pretty()`

对于符合条件的条目，我们都是返回整个JSON文件的。这类似于SQL里面的SELECT *。有的时候，我们需要的，仅仅是部分数据，这个时候，find的局部查询的功能就派上用场了。先来看一个例子，返回tags为drama的电影的名字和首映日期。
这里find的第二个参数是用来控制输出的，1表示要返回，而0则表示不返回。默认值是0，但_id是例外，因此如果你不想输出_id，需要显式地声明：
`db.movie.find({'tags':'drama'},{'debut':1,'title':1,'_id':0}).pretty()`

- 更新数据
`db.movie.update({title:'Seven'}, {$set:{likes:134371}})`

- 更新数据（增量更新）
`db.movie.update({title:'Seven'}, {$inc:{likes:2}})`
这里用的是`$inc`，在原有的基础上加2.

注意如果有多部符合要求的电影。则默认只会更新第一个。如果要多个同时更新，要设置{multi:true}，像下面这样：
`db.movie.update({}, {$inc:{likes:10}},{multi:true})`

在原有的值得基础上增加一个值的话，则应该用$push：
`db.movie.update({'title':'Seven'}, {$push:{'tags':'popular'}})`

- 删除
`db.movie.remove()`
`db.movie.remove({'tags':'romance'})`
