---
layout: post
title: python中mongdb的基本使用
toc: true
cover: 
tags: ['python','mongdb','pymongo']
---
包括简单的增删改查和一些高级查询。
<!-- more -->
## 使用MongoClient建立连接
1. 默认主机和端口的方式
```python
from pymongo import MongoClient
client = MongoClient()
```
2. 明确指定主机和端口
```python
from pymongo import MongoClient
#client = MongoClient()
client = MongoClient('localhost', 27017)
```
3. 使用MongoDB URI格式
```python
client = MongoClient('mongodb://localhost:27017/')
```

## 获取数据库
MongoDB的一个实例可以支持多个独立的数据库
1. 使用MongoClient实例上的属性的方式来访问数据库
```python
db = client.pythondb
```
2. 使用字典样式访问
```python
db = client['pythondb']
```

## 获取集合
集合是存储在MongoDB中的一组文档，可以类似于关系数据库中的表。在PyMongo中获取集合的工作方式与获取数据库相同：
1. 属性的方式
```python
collection = db.collection
```
2. 字典方式
```python
collection = db['collection']
```
## 文档
MongoDB中的数据使用JSON方式来表示文档(并存储)。在PyMongo中使用字典来表示文档。例如，以下字典可能用于表示博客文章:  

```python
import datetime
from pymongo import MongoClient
client = MongoClient()

dict = {"author": "Mike",
         "text": "My first blog post!",
         "tags": ["mongodb", "python", "pymongo"],
         "date": datetime.datetime.utcnow()}
```
## 插入文档
### 插入单个文档
使用 insert_one() 方法，该方法的第一参数是字典 name => value 对。  

```python
import datetime
from pymongo import MongoClient
client = MongoClient()

db = client.pythondb
dict = {"author": "Maxsu",
         "text": "My first blog post!",
         "tags": ["mongodb", "python", "pymongo"],
         "date": datetime.datetime.utcnow()}
col = db.collection
result = col.insert_one(dict) 
print(result)
```
```
out:
<pymongo.results.InsertOneResult object at 0x00000000049A1408>
```
返回 _id 字段:  
insert_one() 方法返回 InsertOneResult 对象，该对象包含 inserted_id 属性，它是插入文档的 id 值。可用如下方式获得属性_id:  
`result.inserted_id`  
**MongoDB 中存储的文档必须有一个"_id" 键。这个键的值可以是任何类型的，默认是个ObjectId 对象。如果我们在插入文档时没有指定 _id，MongoDB 会为每个文档添加一个唯一的 id。**


### 插入多个文档
集合中插入多个文档使用 insert_many() 方法，该方法的第一参数是字典列表。
```python
from pymongo import MongoClient
 
client = MongoClient()
db = client.pythondb
col = db.collection
 
mylist = [
  { "name": "Taobao", "alexa": "100", "url": "https://www.taobao.com" },
  { "name": "QQ", "alexa": "101", "url": "https://www.qq.com" },
  { "name": "Facebook", "alexa": "10", "url": "https://www.facebook.com" },
  { "name": "知乎", "alexa": "103", "url": "https://www.zhihu.com" },
  { "name": "Github", "alexa": "109", "url": "https://www.github.com" }
]
 
result = col.insert_many(mylist)
 
# 输出插入的所有文档对应的 _id 值
print(result.inserted_ids)
```
```
out:
[ObjectId('5b9d02028b2f361a6cfc4855'), ObjectId('5b9d02028b2f361a6cfc4856'), ObjectId('5b9d02028b2f361a6cfc4857'), ObjectId('5b9d02028b2f361a6cfc4858'), ObjectId('5b9d02028b2f361a6cfc4859')]
```  

insert_many() 方法返回 InsertManyResult 对象，该对象包含 inserted_ids 属性，该属性保存着所有插入文档的 id 值。

**在插入文档的时候也可以自己指定_id**

```python
from pymongo import MongoClient
 
client = MongoClient()
db = client.pythondb
col = db.collection
mylist = [
  { "_id": 1, "name": "RUNOOB", "cn_name": "菜鸟教程"},
  { "_id": 2, "name": "Google", "address": "Google 搜索"},
  { "_id": 3, "name": "Facebook", "address": "脸书"},
  { "_id": 4, "name": "Taobao", "address": "淘宝"},
  { "_id": 5, "name": "Zhihu", "address": "知乎"}
]
result = col.insert_many(mylist)
 
# 输出插入的所有文档对应的 _id 值
print(result.inserted_ids)
```
```
out:
[1, 2, 3, 4, 5]
```


## 查询文档
MongoDB 中使用了 find 和 find_one 方法来查询集合中的数据，它类似于 SQL 中的 SELECT 语句。

### 查询单条
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection
doc = col.find_one()
print(doc)
```
```
out:
{'_id': ObjectId('5b9cff618b2f361a6cfc4853'), 'author': 'Maxsu', 'text': 'My first blog post!', 'tags': ['mongodb', 'python', 'pymongo'], 'date': datetime.datetime(2018, 9, 15, 12, 47, 29, 121000)}
```
### 查询所有
find() 方法可以查询集合中的所有数据，类似 SQL 中的 SELECT * 操作。
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

docs = col.find()
for doc in docs:
    print(doc)
```
```
out:
{'_id': ObjectId('5b9cff618b2f361a6cfc4853'), 'author': 'Maxsu', 'text': 'My first blog post!', 'tags': ['mongodb', 'python', 'pymongo'], 'date': datetime.datetime(2018, 9, 15, 12, 47, 29, 121000)}
{'_id': ObjectId('5b9d02028b2f361a6cfc4855'), 'name': 'Taobao', 'alexa': '100', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4856'), 'name': 'QQ', 'alexa': '101', 'url': 'https://www.qq.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4857'), 'name': 'Facebook', 'alexa': '10', 'url': 'https://www.facebook.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4858'), 'name': '知乎', 'alexa': '103', 'url': 'https://www.zhihu.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4859'), 'name': 'Github', 'alexa': '109', 'url': 'https://www.github.com'}
{'_id': 1, 'name': 'RUNOOB', 'cn_name': '菜鸟教程'}
{'_id': 2, 'name': 'Google', 'address': 'Google 搜索'}
{'_id': 3, 'name': 'Facebook', 'address': '脸书'}
{'_id': 4, 'name': 'Taobao', 'address': '淘宝'}
{'_id': 5, 'name': 'Zhihu', 'address': '知乎'}
```

### 根据指定条件查询

我们可以在 find() 中设置参数来过滤数据。
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "name": "Google" }
docs = col.find(myquery)
for doc in docs:
    print(doc)
```
```
out:
{'_id': 2, 'name': 'Google', 'address': 'Google 搜索'}
```


### 查询指定字段的数据
我们可以使用 find() 方法来查询指定字段的数据，将要返回的字段对应值设置为 1。
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

docs = col.find({},{"_id": 0, "name": 1, "alexa": 1 })#第一个{}是查询条件
for doc in docs:
    print(doc)
```
```
out:
{}
{'name': 'Taobao', 'alexa': '100'}
{'name': 'QQ', 'alexa': '101'}
{'name': 'Facebook', 'alexa': '10'}
{'name': '知乎', 'alexa': '103'}
{'name': 'Github', 'alexa': '109'}
{'name': 'RUNOOB'}
{'name': 'Google'}
{'name': 'Facebook'}
{'name': 'Taobao'}
{'name': 'Zhihu'}
```
1. 不能在一个条件{}中同时含有0和1（_id除外）。  
2. 如果{}中只有一个key为 0，则其他都为 1，反之亦然。  
3. 发现：_id设为0之后，{}中其它key同时可以设为0（这时显示文档中所有剩下的key-value），也可同时设为1（显示设为1的key-value）。
4. _id设为1之后，{}中其它key只能设为1，否则报错 database error: Can't canonicalize query:
5. 综合3、4，_id为0，默认会显示其它key-value，此时再缩小范围，额外指定{}中的key，不会冲突；_id为1，默认不显示其它key-value，此时再指定某些key为0，则会被认为运算错误。
6. 暂没有看源码的打算。

以下实例除了 alexa 字段外，其他都返回：
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

docs = col.find({},{"alexa": 0, })
for doc in docs:
    print(doc)
```
```
out:
{'_id': ObjectId('5b9cff618b2f361a6cfc4853'), 'author': 'Maxsu', 'text': 'My first blog post!', 'tags': ['mongodb', 'python', 'pymongo'], 'date': datetime.datetime(2018, 9, 15, 12, 47, 29, 121000)}
{'_id': ObjectId('5b9d02028b2f361a6cfc4855'), 'name': 'Taobao', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4856'), 'name': 'QQ', 'url': 'https://www.qq.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4857'), 'name': 'Facebook', 'url': 'https://www.facebook.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4858'), 'name': '知乎', 'url': 'https://www.zhihu.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4859'), 'name': 'Github', 'url': 'https://www.github.com'}
{'_id': 1, 'name': 'RUNOOB', 'cn_name': '菜鸟教程'}
{'_id': 2, 'name': 'Google', 'address': 'Google 搜索'}
{'_id': 3, 'name': 'Facebook', 'address': '脸书'}
{'_id': 4, 'name': 'Taobao', 'address': '淘宝'}
{'_id': 5, 'name': 'Zhihu', 'address': '知乎'}
```

### 高级查询
查询的条件语句中，我们还可以使用修饰符。
以下实例用于读取 name 字段中第一个字母 ASCII 值大于 "T" 的数据，大于的修饰符条件为 {"$gt": "T"} :

**其它的高级查询还有很多，用到了再查。**
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "name": { "$gt": "T" } }
docs = col.find(myquery)
for doc in docs:
    print(doc)
```
```
out:
{'_id': ObjectId('5b9d02028b2f361a6cfc4855'), 'name': 'Taobao', 'alexa': '100', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4858'), 'name': '知乎', 'alexa': '103', 'url': 'https://www.zhihu.com'}
{'_id': 4, 'name': 'Taobao', 'address': '淘宝'}
{'_id': 5, 'name': 'Zhihu', 'address': '知乎'}
```

### 使用正则表达式查询
我们还可以使用正则表达式作为修饰符。  
正则表达式修饰符只用于搜索字符串的字段。  
以下实例用于读取 name 字段中第一个字母为 "G" 的数据，正则表达式修饰符条件为 {"$regex": "^G"}:

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "name": { "$regex": "^G"  } }
docs = col.find(myquery)
for doc in docs:
    print(doc)
```
```
out:
{'_id': ObjectId('5b9d02028b2f361a6cfc4859'), 'name': 'Github', 'alexa': '109', 'url': 'https://www.github.com'}
{'_id': 2, 'name': 'Google', 'address': 'Google 搜索'}
```
### 返回指定条数记录

如果我们要对查询结果设置指定条数的记录可以使用 limit() 方法，该方法只接受一个数字参数。以下实例返回 3 条文档记录：  
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

docs = col.find().limit(3)
for doc in docs:
    print(doc)
```

```
out:
{'_id': ObjectId('5b9cff618b2f361a6cfc4853'), 'author': 'Maxsu', 'text': 'My first blog post!', 'tags': ['mongodb', 'python', 'pymongo'], 'date': datetime.datetime(2018, 9, 15, 12, 47, 29, 121000)}
{'_id': ObjectId('5b9d02028b2f361a6cfc4855'), 'name': 'Taobao', 'alexa': '100', 'url': 'https://www.taobao.com'}
{'_id': ObjectId('5b9d02028b2f361a6cfc4856'), 'name': 'QQ', 'alexa': '101', 'url': 'https://www.qq.com'}
```

## 查询结果排序

sort()方法可以指定升序或降序排序。  
第一个参数为要排序的字段，第二个字段指定排序规则，1 为升序，-1 为降序，默认为升序。

按_id升序：  
```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

docs = col.find().sort("_id")
for doc in docs:
    print(doc.get('_id'),type(doc.get('_id')))
```
```
out:
1 <class 'int'>
2 <class 'int'>
3 <class 'int'>
4 <class 'int'>
5 <class 'int'>
5b9cff618b2f361a6cfc4853 <class 'bson.objectid.ObjectId'>
5b9d02028b2f361a6cfc4855 <class 'bson.objectid.ObjectId'>
5b9d02028b2f361a6cfc4856 <class 'bson.objectid.ObjectId'>
5b9d02028b2f361a6cfc4857 <class 'bson.objectid.ObjectId'>
5b9d02028b2f361a6cfc4858 <class 'bson.objectid.ObjectId'>
5b9d02028b2f361a6cfc4859 <class 'bson.objectid.ObjectId'>
```
需要注意的是ObjectId的类型不是传统的int或者str。  
如果要对_id降序排列则  
`docs = col.find().sort("_id", -1)`

## 修改文档

### 修改单条
我们可以在 MongoDB 中使用 update_one() 方法修改文档中的记录。该方法第一个参数为查询的条件，第二个参数为要修改的字段。如果查找到的匹配数据多于一条，则只会修改第一条。

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "_id": 5 }
new_values = { "$set": { "name": 'zhihu_changed' } }

col.update_one(myquery, new_values)
print(col.find_one(myquery))
```
```
out:
{'_id': 5, 'name': 'zhihu_changed', 'address': '知乎'}
```
需要注意的是_id不能自行修改，若修改报错：  
> WriteError: After applying the update to the document {_id: 5 , ...}, the (immutable) field '_id' was found to have been altered to _id: 66

### 修改多条

update_one() 方法只能修匹配到的第一条记录，如果要修改所有匹配到的记录，可以使用 update_many()。  
以下实例将查找所有以 F 开头的 name 字段，并将匹配到所有记录的 alexa 字段修改为 123：  

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "name": { "$regex": "^F" } }
new_values = { "$set": { "alexa": "123" } }
result = col.update_many(myquery, new_values)
print(result.modified_count, "个文档已修改")
```
```
out:
2 个文档已修改
```

## 删除文档

**pymongo中colletion.remove()已经被弃用**
> **DEPRECATED** - Use :meth:`delete_one` or :meth:`delete_many` instead.

### 删除单个文档
我们可以使用 delete_one() 方法来删除一个文档，该方法第一个参数为查询对象，指定要删除哪些数据。

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "author": "Maxsu" }
col.delete_one(myquery)
docs = col.find()
for doc in docs:
    print(doc)
```

### 删除多个文档

我们可以使用 delete_many()方法来删除多个文档，该方法第一个参数为查询对象，指定要删除哪些数据。删除所有 name 字段中以 F 开头的文档:

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

myquery = { "name": {"$regex": "^F"} }
result = col.delete_many(myquery)
print(result.deleted_count, "个文档已删除")
docs = col.find()
for doc in docs:
    print(doc)
```

### 删除集合中的所有文档

delete_many() 方法如果传入的是一个空的查询对象，则会删除集合中的所有文档：

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

result = col.delete_many({})
print(result.deleted_count, "个文档已删除")
docs = col.find()
for doc in docs:
    print(doc)
```

```
out:
7 个文档已删除
```

### 删除集合

我们可以使用 drop() 方法来删除一个集合。下面删除collection集合

```python
from pymongo import MongoClient

client = MongoClient()
db = client.pythondb
col = db.collection

col.drop()
```
drop()方法无返回值，不同于mongdb原生的命令，会返回true或false。



## END





