# mongodb 权限
- 默认没有管理员帐号
- 切换admin添加的帐号才是管理员
- 只能在用户所在的数据库登录 宝库管理员
- 管理员可以管理所有数据库,但是需要在admin中认证

## 添加用户
1. `show dbs`
  - 查看是否含有admin 数据库
  - `use admin`
    - 创建或切换到admin
  -  `show collections`
    - 查看表(含有`system.version`或者还有`system.user`)
  - `db.system.users.find()`
    - 查询这张表
2. 创建用户并指定角色和数据库
```JavaScript
  db.createUser({
    user: "root",
    pwd: "root",
    customData:{description: "管理员"},
    roles:[{role:"userAdminAnyDatabase",db:"admin"}]
    })
```
> user:用户名,pwd:密码,customData:描述,roles: 指定用户的角色
- `db.system.users.find()`或`show users`
  - 看到是否成功

3. 开启权限认证
- 修改`mongodb.conf`添加`auth=true`
  - 前提是你配置了,否则就使用指令`--auth`
-  重启服务

- 验证
  - `use admin`
    - 切换数据库
  - `db.system.users.find()`
    - 出现 `Error`则配置成功了
  - `db.auth("{用户名}","{密码}")`
    -  进行授权
  - `db.system.users.find()`
    - 出现数据授权成功
> 那里创建那里授权那里认证--那里指的是同一个库

4. 读写权限
  - 以上我们只是创建了一个用户管理员,并不能对所有的数据库进行操作
  - `use test_user`
    - 创建一个测试库
    - 添加一个读写账户和一个读权限账户(`role:read`)
  ```JavaScript
db.createUser({
  user: "test_user_rw",
  pwd: "test_user_rw",
  customData:{description: "test_user_rw"},
  roles:[{role:"readWrite",db:"test_user"}]
  })
  ```
  - `db.auth("test_user_rw","test_user_rw")`
    - 切换到 读写账户
  - `db.test.insert({"id":1})`
    - 插入
  - `show collections`
    - 查所有集合
  - `db.test.find()`
      - 查询
  > 都没有问题
  - `db.auth("test_user_r","test_user_r")`
    - 切换 读账户
  - `db.test2.insert({"id":2})`
    - 插入(提示没有权限)
  - `show collections`
    - 查询所有集合
  - `db.test.find()`
    - 查询
- [具体角色定义](http://www.cnblogs.com/SamOk/p/5162767.html)


# 额外的一切补充
## 删除集合
- `db.collection.drop() `
## 更新用户
- `db.updateUser('test',{user:'test',pwd:'admin',roles:[{role:'read',db:'testDB'}]})`
## 删除用户
- `db.dropUser('test')`

## 关闭服务
- 登录shell
  - `mongo`
  - `use admin`
  - `db.shutdownServer()`
- mongod 指令
  - `mongod  --shutdown`

> 不建议kill

# crud

## 增
- `db.{collection}.insertOne()`
  - 在集合中插入一个文档
    - `db.test.insertOne({time: 2018年5月3日 14:35:38})`
- `db.{collection}.insertMany() `
  - 在集合中插入多个文档
    - `db.test.insertMany([{time:"2018年5月3日 14:42:40"},{time:"2018年5月3日 14:42:58"}])`
- `db.{conllection}.insert()`
  - 在集合中插入一个或多个文档
- 说明
  - 写入操作在单个文档级别上都是原子级别的
  - 每个文档都有一个_id字段作为主键,没有指定时,自动生成`ObjectId("ccccccc")`

## 查
- `db.{conllection}.fnd()`
  - 查询某个集合所有
    - `db.test.find()`
  - 过滤器
    - `db.{conllection}.fnd({find}:{value})`
      - `db.test.fnd({t:1})`
        - 表示查询t为1的文档
  -  `$in`
    - `db.test.find({t:{ $in :[1,2,3]}})`
      - 筛选 t为 1,2,3的文档
  - and
    - `db.test.find({t:2,q:{$lt:3}})`
      - 筛选t=2并且q<3的文档
  - $or
    - `db.test.find({$or:[{t:2},{q:2}]})`
      - 筛选t=2或者1=2的稳定度
 - `db.{collection}.findOne()`
   - 也可以使用查询,多个文档时返回磁盘顺序为1的文档

  - 嵌套文档
    -  `db.test.find({c:{j:1}})`
    - 嵌套条件查询
      - `db.test.find({$or:[{"c.j":1},{"c.j":2}]})`
        - 使用`.` 引出嵌套属性,必须在引号中
      - `db.test.find({"c.j":{$lt:2}})`
        - 查询c.j小于2的文档
  - 数组匹配
    - `db.test.find({index:[1,2]})`
      - 查询index数组中只含有1和2元素的文档
    - `db.test.find({index:{$all:[1,2]}})`
      - 查询idnex数组中包含1和2元素的文档
    - `db.test.find({index:{$gt:6}})`
      - 查询index数组中所有元素都比6的文档
    - `db.test.find({index:{$size:3}})`
      - 查询index数组中元素个数为3的文档
    - `db.test.find({index:{$gt:1,$lt:3}})`
      - 查询数组中满足此条件的文档
        - 一个元素何以同时满足,两个元素分别满足
    - `db.test.find({index:{$elemMatch:{$gt:1,$lt:3}}})`
      - 必须有一个数同时满足条件
    - `db.test.find({"index.1":2})`
      - 使用`.`来匹配数组具体的下标
  - 数组文档查询
    - `db.test.find({data:{c:4}})`
      - 查询data数组中c含有4的文档
        - 多个自动匹配字段顺序
    - `db.test.find({"data.0.c":{$lte :3}})`
      - 查询data[0].c <=3的文档
      - 至少有一个
    - `db.test.find({"data.0.t":{$lte:3}})`
      - 指定某个标
    - `db.test.find({data:{$elemMatch:{c:5,t:3}}})`
      - 至少有一个满足
    - `db.test.find({data:{$elemMatch:{t:{$gt:1,$lte:5}}}})`
      - 查询data数组中 t>1 and <=5 的元素
      > `$elemMatch`运算符筛选其至少满足一个的文档
    - 筛选显示字段
      - `db.test.find({q:2},{q:1})`
        - 返回q字段是2的q字段的值
      - `db.test.find({q:2},{q:1})`
        - 排除_ID字段不显示
        > 可以字段全屏蔽
      - `db.test.find({"data.c":1},{"data.c":0})`
        - 嵌入式文档特定副段屏蔽
      - `db.test.find({"index.0":6},{index:{$slice:-2}})`
        - 显示满足条件的文档数组后两个
        - 屏蔽字段时,不能使用`.`
        > 详细翻看文档
      - `db.test.find(c:null)`
        - 查询含有c字段或不含有c字段的文档
      - `db.test.find({c:{$type:10}})`
        - 至返回c为null的字段
        - 10为null
        - [类型](https://docs.mongodb.com/manual/reference/bson-types/)
      - `b.test.find({c:{$exists:false}})`
        - 查出不存在c字段的文档
      > [游标](https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/) 不做介绍

  ## 改
  - `db.test.updateOne({update:1},{$set:{t:11111111}}, $ currentDate ： {  lastModified ： true  } )`
    - 更新
  - `db.test.updateOne({update:1},{$set:{t:222222}},{$currentDate:{lastModified:true}})`
    - 记录更新时间爱你
  - 批量`
    - `db.collection.updateMany() `
  - 替换
    -  `db.collection.replaceOne()`


