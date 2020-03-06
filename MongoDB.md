[TOC]

------

# javascript安裝

## 下載

- <https://www.mongodb.com/download-center/community>

## 建立DB路徑

```
mkdir c:\data\db
mkdir c:\data\log
```

## 啟動並指定存取位置

```
cd C:\Program Files\MongoDB\Server\4.0\bin
mongod --dbpath c:\data\db
```

# 開始

```
cd C:\Program Files\MongoDB\Server\4.0\bin
mongo.exe 
```

## 建立設定檔

- mongod.cfg

```
systemLog:
    destination: file
    path: d:\MongoDB\data\log\mongod.log
storage:
    dbPath: d:\MongoDB\data\db
```

```
cd C:\Program Files\MongoDB\Server\4.0\bin
mongod.exe --config "C:\Program Files\MongoDB\Server\4.0\mongod.cfg" --install

```





------

# 索引

### 優點

- 搜尋速度更快 ~
- 在使用分組或排度時更快 ~

### 缺點

- 每次進行操作(新增、更新、刪除)時，都會更費時，因為也要修改索引。

- 索引需要佔據空間。

  ![img](http://yixiang8780.com/outImg/20161209-1.png)

- 提高查詢的性能

- 以 B-Tre e結構建立類似 NonClustered Index

- B-Tee結構的特點是：查詢每個值所要進行查詢的次數時固定的，最小的值存儲在最左邊的葉子節點上，做大的值存儲在最右邊的葉子節點上

- 認按照「_id」欄位的升序創建index

- 格式:   `db.collection.createIndex(keys,option)` 

- field是doc的欄位，1/-1 表示按照field排序的方向創建index：1表示按照field的升序創建，-1表示按照field的降序創建。

```javascript
//單一索引
db.user.createIndex({"age":1})

//複合索引
db.user.createIndex({"name":1,"age":-1})

//查詢索引
db.user.getIndexes()
/*
[
        {
                "v" : 2,
                "key" : {
                        "_id" : 1
                },
                "name" : "_id_",
                "ns" : "test.user"
        },
        {
                "v" : 2,
                "key" : {
                        "name" : 1,
                        "age" : -1
                },
                "name" : "name_1_age_-1",
                "ns" : "test.user"
        }
]
*/
//刪除索引
db.user.dropIndex(indexName)
```

### 複合索引

- 針對多個欄位建立索引
- 索引的排序有順序性， `{ "name" : 1, "age" :1 }`，它會先依`name`的值進行排序，然後相同的`name`再按`age`進行排序。
- 以下圖為例，不同的index建立方式會影響搜尋的速度

```javascript
db.user.ensureIndex({ "name" : 1 , "age" : 1 })

["mark00",20] -> xxxxxxx 
["mark00",30] -> xxxxxxx 
["mark00",100] -> xxxxxxx 
["mark01",25] -> xxxxxxx  
["mark02",10] -> xxxxxxx  
["mark03",18] -> xxxxxxx  
["mark04",26] -> xxxxxxx  
["mark05",40] -> xxxxxxx  
["mark06",51] -> xxxxxxx  
["mark07",20] -> xxxxxxx  
["mark08",51] -> xxxxxxx 

db.user.ensureIndex({ "age": 1 , "name" : 1 })

[10,"mark02"] -> xxxxxxx
[18,"mark03"] -> xxxxxxx
[20,"mark00"] -> xxxxxxx
[20,"mark07"] -> xxxxxxx
[25,"mark01"] -> xxxxxxx
[26,"mark04"] -> xxxxxxx
[30,"mark00"] -> xxxxxxx
[40,"mark05"] -> xxxxxxx
[51,"mark06"] -> xxxxxxx
[51,"mark08"] -> xxxxxxx
[100,"mark00"] -> xxxxxxx
```





------

# 新增資料

## `Insert`

- `將一個document建立到collection裡`

  ## `單筆`

```
user1 = {
	name : "Mark",
	age : 18,
	bmi : 10
}

db.user.insert(user1);
//return WriteResult({"nInserted" : 1})

db.user.find()
//{ "_id" : ObjectId("5d12171ff10cf2a45e622d92"), "name" : "Mark", "age" : 18, "bmi" : 10 }
```

### `多筆`

- `參數ordered，true時代表如果其中一筆資料有問題，它就會停止下來，後面的資料都不會新增，而false時，則代表不會停下來，後面的資料會繼續新增，預設是true。`

```javascript
var user1 = {
	name : "Mark",
	age : 18,
	bmi : 10
},
count = 1000,
users = [];

for (var i=0;i<count;i++){
	users.push(user1);
}

db.user.insert(users,{ordered:false})
```



## `InsertOne`

- `用法和insert差不多，只有兩點不同，`
  1. `首先是回傳，insertOne會回傳你所建立的document的ObjectId，ObjectId是系統自動生成的，是唯一值。`
  2. `只能一次新增一筆。`

```javascript
user1 = {
	name : "Mark",
	age : 18,
	bmi : 10
}

db.user.insertOne(user1);

/* return
{
        "acknowledged" : true,
        "insertedId" : ObjectId("5d1217bef10cf2a45e622d93")
}
*/
```

## `InsertMany`

- `mongodb 3.2版時新增的，用法也和insert函數差不多，但比較不同的是他的回傳值如下，他會回傳所有所建立的documnet的ObjectId。`

```javascript
var user1 = {
	name : "Mark",
	age : 18,
	bmi : 10
},
count = 10,
users = [];

for (var i=0;i<count;i++){
	users.push(user1);
}

db.user.insertMany(users,{ordered:false})
/* return
{
        "acknowledged" : true,
        "insertedIds" : [
                ObjectId("5d1217e3f10cf2a45e622d94"),
                ObjectId("5d1217e3f10cf2a45e622d95"),
                ObjectId("5d1217e3f10cf2a45e622d96"),
                ObjectId("5d1217e3f10cf2a45e622d97"),
                ObjectId("5d1217e3f10cf2a45e622d98"),
                ObjectId("5d1217e3f10cf2a45e622d99"),
                ObjectId("5d1217e3f10cf2a45e622d9a"),
                ObjectId("5d1217e3f10cf2a45e622d9b"),
                ObjectId("5d1217e3f10cf2a45e622d9c"),
                ObjectId("5d1217e3f10cf2a45e622d9d")
        ]
}
*/
```



## `Bulk Insert`

- 



### `Ordered Operations`

- `在執行列表的寫入操作時，如果其中一個發生錯誤，它就會停止下來，不會在繼續執行列表內的其它寫入操作`

- `前面的操作不會rollback`

  ```javascript
  var bulk = db.user.initializeOrderedBulkOp();
  bulk.insert( { name: "mark"} );
  bulk.insert( { name: "hoho"} );
  bulk.execute();
  
  /* return
  BulkWriteResult({
          "writeErrors" : [ ],
          "writeConcernErrors" : [ ],
          "nInserted" : 2,
          "nUpserted" : 0,
          "nMatched" : 0,
          "nModified" : 0,
          "nRemoved" : 0,
          "upserted" : [ ]
  })
  */
  ```

  

### `Unordered Operations`

- `寫入操作時，如果其中一個發生錯誤，它不會停止下來，會繼續執行列表內的其它寫入操作，速度較快。`

  ```javascript
  var bulk = db.collection.initializeUnorderedBulkOp();
  bulk.insert( { name: "mark"} );
  bulk.insert( { name: "hoho"} );
  bulk.execute();
  ```

  

## `備註`

- `如果需要回傳值和錯誤詳細回傳資料的話，請選擇用InsertMany`
- `如果是要新增例如log之類的可以用Unordered Bulk，因為掉一筆也不一定會死，`
  `但如果是掉一筆會死的請用Ordered Bulk。`



------

# 更新資料

## `	Update`

###  架構

- `query ` - 要尋找更新的目標條件	

- `update` - 要更新的值

- `upsert` : 這個參數如果是`true`，代表如果沒有找到該更新的對像，則新增，反之則否，默認是`false`。

- `multi` : 如果是`false`，則代表你`query`出多筆，他就只會更新第一筆，反之則都更新，默認是`false`( !注意`multi`只能在有`修改器`時才能用 )。

- `writeConcern` : 拋出異常的級別。

  ```javascript
  db.collection.update(
     <query>,
     <update>,
     {
       upsert: <boolean>,
       multi: <boolean>,
       writeConcern: <document>
     }
  )
  ```

### 範例

```javascript
db.user.update({"name":"mark"},{"name":"mark","age":18})
/* return
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
*/

db.user.update({"name":"ss"},{"name":"ss","age":99},{upsert:true})
/*
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 1,
        "nModified" : 0,
        "_id" : ObjectId("5d1227caa37247fcf9e9d4b0")
})
*/

// 取代為替換整個Document，而不是對應欄位
db.user.update({"name":"Mark"},{"age":1})
// WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

db.user.find()
/*
原始:
{ "_id" : ObjectId("5d1216d6f10cf2a45e622d91"), "name" : "Mark", "age" : 18, "bmi" : 10 }
更新:
{ "_id" : ObjectId("5d1216d6f10cf2a45e622d91"), "age" : 1 }
*/
```



## `$set`

- 修改器主要的功用就是用來指定一個字段的值，不用像上面一樣整個替換掉。
- 所以如果只要修改某一個欄位，只要下達下面的指令。

```javascript
db.user.find()
/*
{ "_id" : ObjectId("5d1226a6f10cf2a45e622da2"), "name" : "jj", "age" : 23 }
*/

db.user.update({"name":"jj"},{"$set" : { "age" : 1} })
/*
 "_id" : ObjectId("5d1226a6f10cf2a45e622da2"), "name" : "jj", "age" : 1 }
*/
```



## `$inc`

- `$inc`只能用在數值類型，否則就會提示`Modifier $inc allowed for numbers only`
- 類似於 `+=` 將數值型態進行累加

```javascript
db.user.find()
/*
{ "_id" : ObjectId("5d1226a6f10cf2a45e622da2"), "name" : "jj", "age" : 1 }
*/

db.user.update({"name" : "jj"},{"$inc" : {"age" : 5}})
/*
 "_id" : ObjectId("5d1226a6f10cf2a45e622da2"), "name" : "jj", "age" : 6}
*/
```



## `$push`

- 假如一個`document`中已經有陣列的結構，使用`push`會在陣列的尾末加入一個新元素
- 要是本來就沒有這個陣列，則會自動新建一筆。

```javascript
db.user.insert({
    "name" : "mark",
    "fans" : ["steven","crisis","stanly"]
})
db.user.update({"name":"mark"},{$push:{"fans" : "jack"}})
/*
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
    { "_id" : ObjectId("5d13593ac9635993d91e2b60"), "name" : "mark", "fans" : [ "steven", "crisis", "stanly", "jack" ] }
*/

 db.user.update({"name":"mark"},{$push:{"dad" : "jack"}})
/*
{ "_id" : ObjectId("5d13593ac9635993d91e2b60"), "name" : "mark", "fans" : [ "steven", "crisis", "stanly", "jack" ], "dad" : [ "jack" ] }
*/
```



## `$each`

- `$push`一次新增只能新增一筆元素，而搭配`$each`就可以新增多筆。

```javascript
db.user.insert({"name" : "sean"	})

db.user.update({"name":"sean"},
	{"$push" : {"fans" : {"$each" : ["jack","lnadry","max"]}}}
)

/*
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
{ "_id" : ObjectId("5d135ad8c9635993d91e2b61"), "name" : "sean", "fans" : [ "jack", "lnadry", "max" ] }
*/
```



## `$slice`

- 限制陣列的大小
- 保留最後`n`個元素

```javascript

db.user.insert({
    "name" : "aa",
    "fans" : ["steven","crisis","stanly"]
})

db.user.update({"name":"aa"},
               {"$push" : {"fans" : 
                           {"$each" : ["jack","lnadry","max"],
                            "$slice" : -5 }}}
              )
```

## `$addToSet`

- 新增一個元素到陣列裡，並且保證陣列內的元素不會重複

```javascript
db.user.insert({
		"name" : "mark",
		"fans" : ["steven","crisis","stanly"]
	})
	
db.user.update({"name":"mark"},
               {"$addToSet" : {"fans" : {"$each" : ["steven","jack"] }}}
              )
```



## `$pop`

- 從頭或尾開始刪除n個元素
- 正數代表從陣列尾刪除
- 負數從陣列頭刪除

```javascript

db.user.find()
/*
    { "_id" : ObjectId("5d13593ac9635993d91e2b60"), "name" : "mark", "fans" : [ "steven", "crisis", "stanly", "jack" ]}
*/

db.user.update({"name":"mark"},{"$pop" : {"fans":1}})
/*
最後一個"stanly" 被刪除
{ "_id" : ObjectId("5d13593ac9635993d91e2b60"), "name" : "mark", "fans" : [ "steven", "crisis", "stanly" ]
*/

db.user.update({"name":"mark"},{"$pop" : {"fans":-1}})
/*
刪除第一個 "steven"
{ "_id" : ObjectId("5d13593ac9635993d91e2b60"), "name" : "mark", "fans" : [ "crisis", "stanly" ] }
*/
```

## `$pull`

- 基於特定條件來刪除。

```javascript
db.user.insert({
		"name" : "mark",
		"fans" : ["steven","crisis","stanly"]
	})
	
	db.user.update({"name":"mark"},
		{"$pull" : {"fans":"crisis"}}
	)
```



------

# 刪除資料

### `remove`

- `mongodb`裡最基本的刪除`document`的方法

- 注意就算你刪除了`document`它的`index`與`預分配空間`都不會刪除。

- 架構如下

  ```
  db.collection.remove(
     <query>,
     {
       justOne: <boolean>,
       writeConcern: <document>,
       collation: <document>
     }
  )
  ```

  

- `justOne`預設`false`，代表`query`到幾個就會刪除幾個，`true`則只會刪第一個。

- `witeConecern`為拋出異常的級別。

- `collation`是`3.4`版開始支持的功能，可依照語言定義來針對文字的內容進行解讀，再還沒支持`collation`前一徑依字節來對比。

```javascript
db.user.remove({"name":"mark"})

/*
	WriteResult({ "nRemoved" : 2 })
*/

db.user.find({"name":"steven"})
/*
    { "_id" : ObjectId("5d135f6dc9635993d91e2b64"), "name" : "steven", "age" : 23 }
    { "_id" : ObjectId("5d135f72c9635993d91e2b66"), "name" : "steven", "age" : 23 }
*/

db.user.remove({"name":"jj"},{justOne:true})
/*
	WriteResult({ "nRemoved" : 1 })
*/
```

### 刪除所有資料

- `remove`可以用來刪除`collection`的所有資料
- 另一種方法也是刪除`collection`的所有資料，那就是`drop`，但它同時會將`index`給全部刪除。

```javascript
db.user.remove({})

db.user.drop()
```



### `deleteMany`與`deleteOne`

- 一個是刪除多筆和一個是單筆
- `remove`不同點大概只差在回傳值上

```javascript
db.collection.deleteMany(
   <filter>,
   {
      writeConcern: <document>,
      collation: <document>
   }
)
```



## `bulk delete`

```javascript
//先新增二筆資料
var bulk = db.AA.initializeUnorderedBulkOp();
bulk.insert( { name: "mark"} );
bulk.insert( { name: "hoho"} );
bulk.execute();

//然後再刪除掉mark該筆
var bulk = db.AA.initializeUnorderedBulkOp();
bulk.find( { "name": "mark" } ).remove();
bulk.execute();
```



------

# Find

## 基本說明

- 第一個參數決定要那些資料
- 第二個參數則決定要返回那些`key`
- 使用 "_id":0 就不會回傳該欄位資料

```javascript
db.user.find({"name":"mark"},{"id" :1 })
/*
{ "_id" : ObjectId("5d136e44c9635993d91e2b7d"), "id" : "1" }
*/

db.user.find({"name":"mark"},{"id" : 1,"_id":0})
/*
{ "id" : "5" }
*/

 db.user.find({"name":"sean"},{"id" : 0,"_id":0})
/*
{ "name" : "sean", "age" : 20 }
*/
```



## 搜尋條件

|   條件   |                           操作符號                           |
| :------: | :----------------------------------------------------------: |
|   AND    | `$and`，另一種方法也可以直接在`query`中下`{"key1","value1","key2":"value2"}` |
|    OR    |                            `$or`                             |
|   NOT    |                            `$not`                            |
|   NOR    |                            `$nor`                            |
|   大於   |                            `$gt`                             |
| 大於等於 |                            `$gte`                            |
|   小於   |                            `$lt`                             |
| 小於等於 |                            `$lte`                            |
|   包含   |                            `$in`                             |
|  不包含  |                            `$nin`                            |

- 尋找年紀30歲以上(包含30)，但不滿60歲(不包含60)，fans又有200人以上(包含200)的人

```javascript
//這是第一種
db.user.find(
	{"age":{"$gte":30,"$lt":60},
	 "fans":{"$gte" : 200}})
	 
//這是第二種
db.user.find(
	{"$and":[{"age":{"$gte":30,"$lt":60}},{"fans":{"$gte" : 200}}]})
```

- 尋找`fans`小於等於100，或是`likes`小於100的人。

```javascript
db.user.find(
	{"$or": [{"fans":{"$lte":100}},{"likes":{"$lt":100}}]})
```

- 尋找`age`為`25、60`的人。

```javascript
db.user.find({"age":{"$in":[25,60]}})
```

- 尋找`age`不為`25、60`的人，並且只給我它的`id`就好。

```
db.user.find({"age":{"$nin":[25,60]}},{"id":1})
```

- 尋找`likes`不大於100的人(使用`$not`)

```
db.user.find({"likes":{"$not":{"$gt":100}}})
```

- 同時不滿足`fans`大於100人且`likes`大於500。
- 找出不存在這裡面的集合 (fans<=100 AND likes <=500) = fans > 100 OR likes > 500

```
db.user.find({"$nor":[{"fans":{"$lt":100}},{"likes":{"$lt":500}}]})
```

|   操作符號   |                                                              |
| :----------: | ------------------------------------------------------------ |
|   邏輯符號   | `$and`、`$or`、`$or`、`not`                                  |
| 邏輯符號用法 | `{$xxx: [ { expression1 },{ expression2 }]}` `{ field: { $not: { <operator-expression> } } }` |
|   比較符號   | `$gt`、`$gte`、`$lt`、`$lte`、`$in``$nin`                    |
| 比較符號用法 | `{field: {$gt: value} }` `{ field: { $in($nin): [<value1>, <value2>, ... <valueN> ] } }` |

## 陣列欄位

|    Tables    |                             Are                              |
| :----------: | :----------------------------------------------------------: |
|    `$all`    |      當需要尋找多個元素節合的`document`時，就可以使用它      |
|   `$size`    |            當要尋找特定長度的陣列時，就可以用它~             |
|   `$slice`   | 可以指定回傳的陣列指定的範例 ex. `10`就為前十條，`-10`就為後十條。 |
| `$elemMatch` |              它會只針對陣列，進行多組`query`。               |

```javascript
{"id":"1","name":"mark",
	"fans":["steven","stanly","max"],
	"x":[10,20,30]};

{"id":"2","name":"steven",
	"fans":["max","stanly"],
	"x":[5,6,30]};

{"id":"3","name":"stanly",
	"fans":["steven","max"],
	"x":[15,6,30,40]};

{"id":"4","name":"max",
	"fans":["steven","stanly"],
	"x":[15,26,330,41,1]};
```

- 尋找`fans`中同時有`steven`、`max`

```javascript
db.user.find({"fans":{"$all":["steven","max"]}},{"_id":0})

/*
{ "id" : "1", "name" : "mark", "fans" : [ "steven", "stanly", "max" ], "x" : [ 10, 20, 30 ] }
{ "id" : "3", "name" : "stanly", "fans" : [ "steven", "max" ], "x" : [ 15, 6, 30, 40 ] }
*/
```

- 尋找`fans`總共有三位
- `$size` 無法使用其他判斷條件整有 ==

```javascript
db.user.find({"fans":{"$size" :3}},{"_id":0})

/*
    { id" : "1", "name" : "mark", "fans" : [ "steven", "stanly", "max" ], "x" : [ 10, 20, 30 ] }
*/
```

- 尋找`mark`的第n個`fans`
- 正代表前幾個，負代表後幾個

```javascript
db.user.find({"name":"mark"},{"fans":{"$slice":2}})

/*
{ "_id" : ObjectId("5d137476c9635993d91e2b84"), "id" : "1", "name" : "mark", "fans" : [ "steven", "stanly" ], "x" : [ 10, 20, 30 ] }
*/

db.user.find({"name":"mark"},{"fans":{"$slice":-1}})

/*
{ "_id" : ObjectId("5d137476c9635993d91e2b84"), "id" : "1", "name" : "mark", "fans" : [ "max" ], "x" : [ 10, 20, 30 ] }
*/
```

- 尋找`x`中至少有一個值大於30且小於100

```javascript
db.user.find({"x":{"$elemMatch" : { "$lt" : 100}}})

/*
{ "_id" : ObjectId("5d137476c9635993d91e2b86"), "id" : "3", "name" : "stanly", "fans" : [ "steven", "max" ], "x" : [ 15, 6, 30, 40 ] }
{ "_id" : ObjectId("5d137476c9635993d91e2b87"), "id" : "4", "name" : "max", "fans" : [ "steven", "stanly" ], "x" : [ 15, 26, 330, 41, 1 ] }
*/
```



## 正規表達式

- 尋找`name`為`s`開頭的網紅

```javascript
db.user.find({"name":/^s/})
```



## `limit`

- 限制`find`結果的數量可以用
- 注意`limit`是指定上限而不是指定下限

```javascript
db.test.find().limit(10)
```



## `skip`

- `skip(10)`，代表忽略前十筆，然後在開始回傳
- `skip`如果數量很多時速度會變很慢 

```
db.test.find().skip(10)
```

## `sort`

- 將`find`出的資料，根據條件，進行排序
- 1代表由小到大，-1由大到小

```
db.user.find().sort({age:1})
```

```
db.test.find().skip(10).limit(50).sort({x:1})
```

## cursor

- `Iterator 模式`的實作

```javascript
var cursor = db.user.find();

while (cursor.hasNext()){
	obj = cursor.next();
	print(obj.name)
}

/*
mark
steven
stanly
max
*/
```



------

# Aggregate 

- 將資料進行分析及處理

- `db.collection.aggregate(AGGREGATE_OPERATION)`

- `aggregate framework`主要是建立在聚合管道(`pipeline`)基礎下，而這管道就是可以一連串的處理事件

  ```javascript
  /*
  db.collection.aggregate(
  	[將每篇文章作者與like數抓取出來],
  	[依作者進行分類],
  	[將like數進行加總]
  	[返like數前五多的結果]
  )
  */
  // ex. 找出user內 5 <= count < 30的documents，並依據status進行分類，並將 count 進行加總
  
  db.user.aggregate(
  	{ "$match" : { count : { $gte : 5, $lt : 30 } }},
  	{"$group" : {"_id" : "$status","total" : {"$sum" : "$count"}}
  })
  
  /*
      { "_id" : "Z", "total" : 11 }
      { "_id" : "x", "total" : 21 }
      { "_id" : "o", "total" : 15 }
  */
  ```

|  操作符號  |                    功用                    |
| :--------: | :----------------------------------------: |
| `$project` |     選擇集合中要的欄位，並可進行修改。     |
|  `$match`  |      篩選操作，可以減少不需要的資料。      |
|  `$group`  |             可以欄位進行分組。             |
| `$unwind`  | 拆開，可以將陣列欄位拆開成多個`document`。 |
|  `$sort`   |           可針對欄位進行排序 。            |
|  `$limit`  |        可針對回傳結果進行數量限制。        |
|  `$skip`   |       略過前`n`筆資料，在開始回傳 。       |



## ## project`

- 選取`document`中的欄位
- 還可以在這些欄位上進行一些操作，或是新建欄位。

```javascript
db.user.aggregate({ "$project" : { "id" : 1, "name" : 1 }})

/*
{ 
  "_id" : ObjectId("584e73bd6f4811e2ad965055"), 
  "id" : 1, "name" : "mark" 
}
*/
```

## `$match`

- 用於對`document`的篩選


```javascript
db.user.aggregate({ "$match" : { count : { $gt : 10, $lte : 30 } }})

/*
    { "_id" : ObjectId("5d157967b726c0758acae30c"), "id" : 5, "status" : "x", "count" : 11 }
    { "_id" : ObjectId("5d157967b726c0758acae30d"), "id" : 6, "status" : "Z", "count" : 11 }
*/
```

## `$group`

- 根據條件進行documents分組


```javascript
db.user.aggregate({"$group" : {"_id" : "$status"}})
/*
    { "_id" : "Z" }
    { "_id" : "x" }
    { "_id" : "o" }
*/

db.user.aggregate(
	{"$group" : {"_id" : "$status","total" : {"$sum" : "$count"}}
})

/*
    { "_id" : "Z", "total" : 11 }
    { "_id" : "x", "total" : 21 }
    { "_id" : "o", "total" : 15 }
*/
```

## `$unwind`

- 將陣列欄位的每一個值拆分為單獨的`document`


```javascript
/*
{
	"name" : "mark",
	"fans" : [
		{"name" : "steven","age":20},
		{"name" : "max","age":60},
		{"name" : "stanly","age":30}
	]
}
*/

db.arr.aggregate({"$unwind" : "$fans"})
/*
    { "_id" : ObjectId("5d15bc5cb726c0758acae30e"), "name" : "mark", "fans" : { "name" : "steven", "age" : 20 } }
    { "_id" : ObjectId("5d15bc5cb726c0758acae30e"), "name" : "mark", "fans" : { "name" : "max", "age" : 60 } }
    { "_id" : ObjectId("5d15bc5cb726c0758acae30e"), "name" : "mark", "fans" : { "name" : "stanly", "age" : 30 } }
*/
```

## `$sort`

- 根據任何欄位進行排序
- 如果大量的資料要進行排序，建議在管道的第一節進行排序，因為可以用索引。

```javascript
db.user.aggregate({"$sort" : { "age" : 1 }})
```

## `$limit`

- 限制回傳`document`的數量


```
db.user.aggregate({"$limit" : 5})
```

## `$skip`

- 捨棄前`n`然後在開始回傳結果


```
db.user.aggregate({"$skip" : 5})
```

## 範例

​	按照下面的步驟建立管道(pipeline)，來找出第二年輕的男性。

1. 先篩選出`sex`為`M`的`user`。
2. 將每個`user`的`name`與`age`投射出來。
3. 根據`age`進行排序。
4. 跳過`1`名`user`。
5. 限制輸出結果為`1`。

```javascript
/*
{ 
	"id" : 1,
	"name" : "mark",
	"age" : 20,
	"sex" : "M",
	"fans" : [{"name" : "steven"},{"name" : "max"}],
	"phone" : "xxxxx"
},
{ 
	"id" : 2,
	"name" : "steven",
	"age" : 40,
	"sex" : "M",
	"fans" : [{"name" : "mark"},{"name" : "max"}],
	"phone" : "xxxxx"
},
{ 
	"id" : 3,
	"name" : "marry",
	"age" : 30,
	"sex" : "S",
	"fans" : [{"name" : "mark"},{"name" : "max"},{"name" : "jack"}],
	"phone" : "xxxxx"
},
{ 
	"id" : 4,
	"name" : "jack",
	"age" : 21,
	"sex" : "M",
	"fans" : [{"name" : "mark"}],
	"phone" : "xxxxx"
}
*/

db.user.aggregate(
	{ "$match" : { "sex" : "M"}},
	{ "$project" : { "name" : 1 , "age" : 1 }},
	{ "$sort" : { "age" : 1 }},
	{ "$skip" : 1 },
	{ "$limit" : 1 }
)
/*
{ 
  "_id" : ObjectId("584e98109ea01650ca1f5617"), 
  "name" : "jack", 
  "age" : 21 
 }
*/
```

## 數學表達式

|   表達式    |                     說明                     |
| :---------: | :------------------------------------------: |
|   `$add`    |          接受多個表達式，然後相加。          |
| `$subtract` | 接受兩個表達式，用第一個減去第二個作為結果。 |
| `$multiply` |          接受多個表達式，然後相乘。          |
|  `$divide`  |          接受兩個表達式，然後相除。          |
|   `$mod`    |         接受個表達式，然後相除取餘。         |

- 計算每筆訂單收入 =  price *  count  -  discount 

```javascript
/*
{ "id" : 1 , "price" : 100 , "count" : 20, "discount" : 0 },
{ "id" : 2 , "price" : 200 , "count" : 20, "discount" : 100 },
{ "id" : 3 , "price" : 50 , "count" : 20, "discount" : 100 },
{ "id" : 4 , "price" : 10 , "count" : 210, "discount" : 200 },
{ "id" : 5 , "price" : 100 , "count" : 30, "discount" : 20 }
*/

db.account.aggregate ({ "$project" : { 
	"id" : 1 , 
	"price" : 1 ,  
	"count" : 1 ,  
	"discount" : 1 , 
	"income" : { "$subtract" :[ { $multiply : [ "$price","$count"] } , "$discount"]}}})

{ "id" : 1, "price" : 100, "count" : 20, "discount" : 0, "income" : 2000 }
{ "id" : 2, "price" : 200, "count" : 20, "discount" : 100, "income" : 3900 }
{ "id" : 3, "price" : 50, "count" : 20, "discount" : 100, "income" : 900 }
{ "id" : 4, "price" : 10, "count" : 210, "discount" : 200, "income" : 1900 }
{ "id" : 5, "price" : 100, "count" : 30, "discount" : 20, "income" : 2980 }
```

- 計算每筆訂單收入後，加總總合


```javascript
db.account.aggregate (
    { "$project" : 
        { "id" : 1 ,
          "income" : { "$subtract" :[ { $multiply : [ 	"$price","$count"] } , "$discount"]}}
    },
    {
        "$group" : {"_id" : null,
                    "total" : {"$sum" : "$income"}}
    }
)
/*
{ "_id" : null, "total" : 11680 }
*/
```



## 日期表達式

|    表達式     |                   說明                   |        範圍         |
| :-----------: | :--------------------------------------: | :-----------------: |
|    `$year`    |           轉換成年 ex.2016 。            |         NO          |
|   `$month`    |        轉換成月 ex. 1 (代表一月)         |        1~12         |
|    `$week`    |  轉換成星期 ex. 1 (代表該年第一個星期)   |        0~53         |
| `$dayOfMonth` | 轉換成該月的第n天 ex. 1 (代表該月第一天) |        1-31         |
| `$dayOfYear`  | 轉換成該年的第n天 ex. 1 (代表該年第一天) |        1-366        |
| `$dayOfWeek`  |   轉換成該星期的某一天 ex. 1 (星期一)    | 1(星期日)~7(星期六) |
|    `$hour`    |            提取出該時間的小時            |        0~23         |
|   `$minute`   |            提取出該時間的分鐘            |        0~59         |
|   `$second`   |             提取出該時間的秒             |        0~60         |

```javascript
{ "_id" : 1, "date" : ISODate("2016-01-02T08:10:20.651Z") }

db.test.aggregate({
	$project : {
		"year" : { $year : "$date" },
		"month" : { $month  : "$date"},
		"week" : { $week : "$date"},
		"dayOfMonth" : { "$dayOfMonth" : "$date" },
		"dayOfYear" : { "$dayOfYear" : "$date"},
		"dayOfWeek" : { "$dayOfWeek" : "$date"},
		"hour" : { "$hour" : "$date"},
		"minute" : { "$minute" : "$date"},
		"second" : { "$second" : "$date"}
	}
})
/*
"2016-01-02T08:10:20.651Z"
{
	"_id" : 1,
	"year" : 2016,
	"month" : 1,
	"week" : 0,
	"dayOfMonth" : 2,
	"dayOfYear" : 2,
	"dayOfWeek" : 7,
	"hours" : 8,
	"minute" : 10,
	"second" : 20 

}
*/
```



## `字串表達式`

|    表達式     |                             說明                             |
| :-----------: | :----------------------------------------------------------: |
|   `$substr`   |               可以『只』取得某字串的一個範圍。               |
|   `$concat`   |                    將指定的字串連在一起。                    |
|  `$toLower`   |                           變小寫。                           |
|  `$toUpper`   |                           變大寫。                           |
| `$strcasecmp` | 比較兩個字串是否相等，如果相等為0，如果字串ASCII碼大於另一字串則為1，否則為-1。 |

我們可以根據問題，來將之拆分為以下步驟。

1. 取得每個`item`的第一個值，並存放在`temp`欄位中。
2. 並且每個`temp`與`B`進行比較，比較結果放在`result`欄位中。
3. 篩選出`result`為`0`的`document`。(取出的值為B)
4. 將該`document`的`describe`欄位轉換成小寫。

```javascript
/*
    { "item" : "ABC", "describe":"AAbbcc"},
    { "item" : "BCE" , "describe":"hello WorD"},
    { "item" : "CAA" , "describe":"BBCCaa"}
*/

db.test.aggregate(
{
//1. 取得每個`item`的第一個值，並存放在`temp`欄位中。
	$project : { "describe" : 1, "temp" : {"$substr":["$item",0,1]} }
},
{
//2. 並且每個`temp`與`B`進行比較，比較結果放在`result`欄位中。
	$project : { "describe" : 1, "result" : {"$strcasecmp":["$temp","B"]}}
},
{ 
//3. 篩選出`result`為`0`的`document`。
	$match : { "result" : 0 } 
},
{ 
//4. 將該`document`的`describe`欄位轉換成小寫。
	$project : { "describe" : { "$toLower" : "$describe" } } }
)

/*
    { 
        "_id" : ObjectId("58500f0d7fac2213af387c9c"), 
        "describe" : "hello word" 
    }
*/
```



## 邏輯表達式

|   表達式    |                        說明                         |                  使用                   |
| :---------: | :-------------------------------------------------: | :-------------------------------------: |
|   `$cmp`    |    比較expr1與2，相同為0，1>2為1，相反則為-1 。     |         `"$cmp":[expr1,expr2]`          |
|    `$eq`    | 一樣比較expr1與2，但相同則返回`true`否則為`false`。 |          `"$eq":[expr1,expr2]`          |
| `$lt、$lte` |                   小於和小於等於                    |             `"$lt" : value`             |
| `$gt、$gte` |                   大於和大於等於                    |             `"$gt" : value`             |
|   `$and`    |         所有表達式都為`true`，則回傳`true`          |        `"$and":[expr1,expr2..]`         |
|    `$or`    |        其中一個表達式為`true`，則回傳`true`         |        `"$or" : [expr1,expr2..]`        |
|   `$not`    |                  針對表達示取反值                   |             `"$not" : expr`             |
|   `$cond`   |              就是一般程式裡的`ifelse`               | `"$cond":[boolExpr,trueExpr,falseExpr]` |

- 計算出每筆訂單的實際收入，其中當數量大於200時打八折，最後在依`class`進行分組，算出各組的總收入
- 根據問題，我們將拆分為以下`pipeline`步驟。
  1. 全部的訂單先判斷折扣率，並存放在`discount`裡。
  2. 計算每分訂單的收入，並存放在`total`裡。
  3. 根據`class`進行分組，並計算各組的總收入，存放在`result`裡。

```javascript
/*
    { "id":1,"class" : "1", "price" : 10,"count" : 180},
    { "id":1,"class" : "1" ,"price" : 10,"count" : 350},
    { "id":1,"class" : "2" ,"price" : 10,"count" : 90},
    { "id":1,"class" : "2" ,"price" : 10,"count" : 320},
    { "id":1,"class" : "2","price" : 10,"count" : 150}
*/

db.orders.aggregate(
{	
// 1. 全部的訂單先判斷折扣率，並存放在`discount`裡。
	"$project" : { 
		"class" : 1,
		"price" : 1,
		"count" : 1,
		"discount" : {
			"$cond" : [{ $gte: [ "$count", 200 ] },0.8,1]
		}
	}
},
{
// 2. 計算每分訂單的收入，並存放在`total`裡。
	"$project" : {
		"class" :1,
		"total" : { "$multiply" : ["$price","$count","$discount"] }
	}
},
{ 
// 3. 根據`class`進行分組，並計算各組的總收入，存放在`result`裡。
	"$group" : { "_id" : "$class" , "result" : {"$sum" : "$total"} }
})

/*
    { "_id" : "2", "result" : 4960 },
    { "_id" : "1", "result" : 4600 }
*/
```



------

# MapReduce

- `google`所提出的軟體架構，主要用來處理大量的數據

- `mongodb`根據它的架構建構出可以在`mongodb`中使用的聚合工具

- `MapReduce`它可以將一個複雜的問題拆分為多個小問題(`map`)，然後發送到不同的機器上，完成時再合併為一個解決方案(`reduce`)

  ![img](http://yixiang8780.com/outImg/20161216-1.png)

- 和`aggregate framework`差別 ?

  - `aggregate framework` 提供較優透的性能。
  - `MapReduce`性能較差，但可提供更複雜的聚合功能。

- 使用的方法如下

  ```javascript
  db.collection.mapReduce(
      map,    
      reduce, 
      {
          <out>,    
          <query>, 
          <sort>,   
          <limit>,  
          <finalize>, 
          <scope>
      }
  )
  ```

  

- 參數說明如下

  |    參數    |                    說明                    |
  | :--------: | :----------------------------------------: |
  |   `map`    | `map`函數，主要功能為產生`key`給`reduce`。 |
  |  `reduce`  |               `reduce`函數。               |
  |   `out`    |            輸出結果集合的名稱。            |
  |  `query`   |     在`map`前，可用`query`先進行篩選。     |
  |   `sort`   |      在`map`前，可用`sort`進行排序。       |
  |  `limit`   |          在`map`前，可限制數量。           |
  | `finalize` |   可以將`reduce`的結果，丟給某個`key`。    |
  |  `scope`   |            可以在js中使用變數。            |

- 範例: 我們要根據`class`進行分組，所以我們`map`的拆分基礎就是`class`，而`reduce`要做的工作就是將`map`出的結果進行運算，詳細的`MapReduce`過程請看下圖。

  1. 首先是`map`的工作，它將`colleciton`中的`document`根據`map`函數進行歸納成`Key , Values`的結構。

     ![img](http://yixiang8780.com/outImg/20161216-2.png)

  2. 使用`Reduces`函數，進行運算。

     ![img](http://yixiang8780.com/outImg/20161216-3.png)

     ```javascript
     var result = db.orders.mapReduce(
         function(){ 
         	var total = this.price * this.count
         	emit(this.class,total) 
         },
         function(key,values){ 
           var total = 0 ;
           for(var i=0;i<values.length;i++){
             total += values[i];
           }
            return total;
         },
         { out : "test" }
     )
     ```

     

  3. 如果這時想看執行結果可以輸入下列指令，其中`result`為一個`collection`，是由`Reduces`所產生出的最後結果。

     ```javascript
     result.find()
     
     /*
         { "_id" : "1", "value" : 5300 }
         { "_id" : "2", "value" : 5600 }
     */
     ```

     

- 



------

# 副本集 replica set



------

# Docker







------

# 參考項目

1. [30天之你好MongoDB系列]: https://ithelp.ithome.com.tw/users/20089358/ironman/1064

2. [MongoDB Official documents]: https://docs.mongodb.com/v3.2/introduction/

   