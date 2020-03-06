







# Events

- Node.js allows us to create and handle custom events easily by using events module.
- `const EventEmitter = require('events');`
- Event module includes EventEmitter class which can be used to raise and handle custom events.

```js
var emitter = require('events').EventEmitter;
var em = new emitter();

//Subscribe FirstEvent
em.addListener('FirstEvent', function (data) {
    console.log('First subscriber: ' + data);
});

//Subscribe SecondEvent
em.on('SecondEvent', function (data) {
    console.log('First subscriber: ' + data);
});

// Raising FirstEvent
em.emit('FirstEvent', 'This is my first Node.js event emitter example.');

// Raising SecondEvent
em.emit('SecondEvent', 'This is my second Node.js event emitter example.');
```

## EventEmitter

- 需要先透過 `on/addListeners` 註冊好監聽事件
- 再透過 `emit` 觸發事件

### Common Patterns

1. Return EventEmitter from a function

   ```js
   var emitter = require('events').EventEmitter;
   
   function LoopProcessor(num) {
       var e = new emitter();
       
       setTimeout(function () {
           
           for (var i = 1; i <= num; i++) {
               e.emit('BeforeProcess', i);
               
               console.log('Processing number:' + i);
               
               e.emit('AfterProcess', i);
           }
       }
       , 2000)
       
       return e;
   }
   var lp = LoopProcessor(3);
   
   //因為有setTime OUT 造成時間延遲，因此on的執行時間比emit快所以沒有問題
                                   
   lp.on('BeforeProcess', function (data) {
       console.log('About to start the process for ' + data);
   });
   
   lp.on('AfterProcess', function (data) {
       console.log('Completed processing ' + data);
   });
   
   /*
   About to start the process for 1
   Processing number:1
   Completed processing 1
   About to start the process for 2
   Processing number:2
   Completed processing 2
   About to start the process for 3
   Processing number:3
   Completed processing 3
   */
   ```

   ```js
   var emitter = require('events').EventEmitter;
   var e = new emitter();
   
   function LoopProcessor(num) {    
         
       for (var i = 1; i <= num; i++) {
           e.emit('BeforeProcess', i);
           
           console.log('Processing number:' + i);
           
           e.emit('AfterProcess', i);
       }
       
       return e;
   }
   var lp = LoopProcessor(3);
   
   //因為在執行emit時，還沒有註冊到事件導致emit沒有執行
   e.on('BeforeProcess', function (data) {
       console.log('About to start the process for ' + data);
   });
   
   e.on('AfterProcess', function (data) {
       console.log('Completed processing ' + data);
   });
   
   /*
   Processing number:1
   Processing number:2
   Processing number:3
   */
   ```

2. Extend the EventEmitter class

```js
var emitter = require('events').EventEmitter;
//繼承時, 需要用到的模組 
var util = require('util');
 
function LoopProcessor(num) {
    var me = this;
 
    setTimeout(function () {
 
        for (var i = 1; i <= num; i++) {
            me.emit('BeforeProcess', i);
 
            console.log('Processing number:' + i);
 
            me.emit('AfterProcess', i);
        }
    }
    , 2000)
 
    return this;
}

//直接讓LoopProcessor 繼承 emitter 類別
util.inherits(LoopProcessor, emitter)
 
var lp = new LoopProcessor(3);
 
lp.on('BeforeProcess', function (data) {
    console.log('About to start the process for ' + data);
});
 
lp.on('AfterProcess', function (data) {
    console.log('Completed processing ' + data);
});
```



| EventEmitter Methods                                         | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [emitter.addListener(event, listener)](https://nodejs.org/api/events.html#events_emitter_addlistener_event_listener) | Adds a listener to the end of the listeners array for the specified event. No checks are made to see if the listener has already been added. |
| [emitter.on(event, listener)](https://nodejs.org/api/events.html#events_emitter_on_event_listener) | Adds a listener to the end of the listeners array for the specified event. No checks are made to see if the listener has already been added. It can also be called as an alias of emitter.addListener() |
| [emitter.once(event, listener)](https://nodejs.org/api/events.html#events_emitter_once_event_listener) | Adds a one time listener for the event. This listener is invoked only the next time the event is fired, after which it is removed. |
| [emitter.removeListener(event, listener)](https://nodejs.org/api/events.html#events_emitter_removelistener_event_listener) | Removes a listener from the listener array for the specified event. Caution: changes array indices in the listener array behind the listener. |
| [emitter.removeAllListeners([event\])](https://nodejs.org/api/events.html#events_emitter_removealllisteners_event) | Removes all listeners, or those of the specified event.      |
| [emitter.setMaxListeners(n)](https://nodejs.org/api/events.html#events_emitter_setmaxlisteners_n) | By default EventEmitters will print a warning if more than 10 listeners are added for a particular event. |
| [emitter.getMaxListeners()](https://nodejs.org/api/events.html#events_emitter_getmaxlisteners) | Returns the current maximum listener value for the emitter which is either set by emitter.setMaxListeners(n) or defaults to EventEmitter.defaultMaxListeners. |
| [emitter.listeners(event)](https://nodejs.org/api/events.html#events_emitter_listeners_event) | Returns a copy of the array of listeners for the specified event. |
| [emitter.emit(event[, arg1\][, arg2][, ...])](https://nodejs.org/api/events.html#events_emitter_emit_event_arg1_arg2) | Raise the specified events with the supplied arguments.      |
| [emitter.listenerCount(type)](https://nodejs.org/api/events.html#events_emitter_listenercount_type) | Returns the number of listeners listening to the type of event. |



------

# Module

- Consider modules to be the same as JavaScript libraries.

- A set of functions you want to include in your application.

- To include a module, use the `require()` function with the name of the module

  `var http = require('http');`

  ### Built-in Modules

  - Node包含內建的Modules供使用(ex.http、express)

  - 不需要特別引用即可使用

    ```javascript
    var http = require('http');
    
    http.createServer(function (req, res) {
      res.writeHead(200, {'Content-Type': 'text/html'});
      res.end('Hello World!');
    }).listen(8080);
    ```

    

  ## Create  Modules

  1. Create module file as `.js` file

  2. Use the `exports` keyword to make properties and methods available outside the module file.

     ```javascript
     // myfirstmodule.js
     
     var basic = {
         myDateTime :function () {
             return Date();
         },
     
         Hello : function () {
          return "Hello YOYO! ";
         }
     }
     
     module.exports = basic 
     ```

     

  3. include and use the module in any of your Node.js files.

  ```javascript
  var http = require('http');
  var dt = require('./modules/myfirstmodule');
  
  http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.write(dt.Hello() + dt.myDateTime());
    res.end();
  }).listen(8080);
  ```

# Express

## 安裝

```powershell
$ npm install express
```

## 測試

```javascript
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`))
```

## Express application generator

- Use `express-generator` to quickly create an application skeleton.

  ```powershell
  $ npx express-generator
  or
  $ npm install  express-generator -g
  $ express
  ```

# Environment variable

- 可以根據不同的環境建立不同的設定(config設定檔)
- you can run your app anywhere by modifying the environment variables without changing your code and without rebuilding it
- In short, **any place in your code that will change based on the environment**.
- Examples 
  1. Which HTTP port to listen on
  2. What path and folder your files are located in, that you want to serve
  3. Pointing to a development, staging, test, or production database
- A popular solution to how you can organize and maintain your environment variables is to use a `.env` file.
- 建立環境變數
  1. install the dotenv npm package
  2. write the code to read the `.env`
  3. 引用dotenv

```js
//.env
NODE_ENV=development
PORT=6789
```

```js
const dotenv = require('dotenv');
dotenv.config();
console.log(`Your port is ${process.env.PORT}`);
```

- 建立.js檔取得所有變數

  ```js
  const dotenv = require('dotenv');
  dotenv.config();
  
  var config ={
      Env :function () {
          return process.env.NODE_ENV;
      }, 
      Port :function () {
          return process.env.PORT;
      }, 
  }
  
  module.exports = config; 
  ```



# Middleware 

- 建置的時候不會觸發，會在Call API時啟動
- 基本上唯一個接受 req並返回resp的function ex. API後方的callback
- 或將資料進行處理後轉交給下一個middle (ex.`express.json()` req的值若為JSON則進行處理)

![1579184868433](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1579184868433.png)

```js
const express = require('express');
const app = express();

//使用express的內建middleware，用來轉換body內的植
app.use(express.json());

//如果沒有next，function不會將使用權交給下一個fun。進而導致server無回應
app.use(function (req, res,next) {	
    console.log("middleware1");
    next();	
});

//使用module帶進來的middleware

const mid = require('./middle.js');
app.use(mid);

const users =[
    {name:"Tom",age:18},
    {name:"Sean",age:18},
    {name:"Jack",age:18},
    {name:"Gim",age:20},
];

app.get('/api/users', function (req, res) {	
	  res.send(users);	
});
```

```js
//middle.js
function middle(req, res,next) {	
    console.log("middleware 2");
    next();	
}

module.exports = middle;
```



------

# API



## 建立順序

1. 建立專案資料夾

   ```powershell
   mkdir icw.api
   cd icw.api
   code .
   ```

2. 安裝 Express

   ```powershell
   yarn add express
   ```

3. 安裝 nodemon ，可不需要重啟Server直接變更異動

4. 可選擇安裝 `dotenv` 建立環境設定檔

   ```javascript
   //建立 .env檔案，將環境設定寫在裡面
   NODE_ENV = production
   PORT= 8888
   
   //透過 process.env 取出設定內容
   //console.log(`port: ${port}, Env: ${process.env.NODE_ENV}`);
   
   //需再第一頁最上層引用，才會吃到變數
   const dotenv = require('dotenv');
   dotenv.config();
   ```

5. 安裝Config檔: 針對NODE_ENV 需要在config下建立對應的檔案 `config/{NODE_ENV}.json`

   ```javascript
   //config/production.json
   {
       "Customer": {
           "dbConfig": {
               "host": "mongodb://localhost:27017/",
                   "DataBase":"organization"
           },
               "jwt": {
                   "secret": "jwtsecretkey"
               }
       }
   }
   
   //透過下列方法取得對應環境下的資料
   const config = require('config');
   const dbConfig = config.get('Customer.dbConfig');
   console.log(dbConfig)
   ```

   

6. 也可不安裝`dotenv` 則 ENV= Undefined，第五步驟 config 會執行default.json設定。可等到DockerFile內再指定環境

7. 建立連線資訊

   ```javascript
   const mongoose = require('mongoose');
   
   //以middle的方式在CALL API時建立連線
   function connect(req, res,next) {	
       mongoose.connect('mongodb://localhost:27017/myapp');
       next();	
   }
   
   module.exports = connect;
   ```

8. 新增 index.js作為初始連線位置，再進行後續導向

9. 新增 router(類似controller)

   ```javascript
   //user.js
   const express = require('express');
   const router = express.Router();
   
   const users =[
       {name:"Tom",age:18},
       {name:"Sean",age:18},
       {name:"Jack",age:18},
       {name:"Gim",age:20},
   ];
   
   router.get('/', function (req, res) {	
   	  res.send(users);	
   });
   
   router.post('/', function (req, res) {
       let user = {
           name : req.body.name,
           age : req.body.age
       };
       users.push(user);
       res.send(user);
   });
   
   module.exports = router;
   ```

10. 在 index中加入 router設定，就能實現類似controller功能

   ```javascript
   //index.js
   const connect = require('./middle.js');
   const config = require('./config.js');
   const express = require('express');
   const user = require('./routes/user.js');
   const comp = require('./routes/company.js');
   const app = express();
   
   app.use(express.json());
   app.use(connect);
   
   //透過router實現Restful API
   app.use('/api/users', user);
   app.use('/api/company', comp);
   
   const port = process.env.PORT || 5555
   
   app.listen(port,()=>{
       console.log(`Listening on port ${config.Port()}`);
   })
   ```

11. 建立`moongose-schema`類似model，可定義欄位內容及型態。並可直接透過model進行操作

   ```javascript
   //schemas/site.js
   const mongoose = require('mongoose');
   
   var Schema = new mongoose.Schema({
       code:{
           type: String,
           required: '場別代碼不得為空'
       },
       name: {
           type: String,
           required: '場別名稱不得為空'
       },
       postalCode: String,
       address: String,
       phones:[String],
       Create_Date:{
           type:Date,
           default: Date.now
       }
   });
   
   module.exports = mongoose.model('Site', Schema);
   ```

11. `router`加入schema操作

   ```javascript
   const express = require('express');
   const SiteModel = require('../schemas/site.js');
   const router = express.Router();
   
   router.get('/', function (req, res) {	
       SiteModel.find()
       .sort({code: -1})
       .then(result => res.send(result))
       .catch(error => console.log(error.message));
   });
   
   router.get('/:code', function (req, res) {	
       SiteModel.find({code : req.params.code})
       .then(result => res.send(result))
       .catch(error => console.log(error.message));
   });
   
   router.post('/', function (req, res) {
       let site = new SiteModel({
           code : req.body.code,
           name: req.body.name,
           address: req.body.address,
           phones: req.body.phones,
           postalCode: req.body.postalCode
       });
       
       site.save()
       .then(res.send(site))
       .catch(error => console.log(error.message));
   });
   
   module.exports = router;
   ```

   

11. 



## Route

1. 根據不同的URL建立個別 JS檔

2. 建立REST API 並export

   ```js
   const express = require('express');
   const router = express.Router();
   
   const companys =[
       {name:"長穩",phone:"02-xxxxxx"},
       {name:"全穩",phone:"02-xxxxxx"}
   ];
   
   router.get('/', function (req, res) {	
   	  res.send(companys);	
   });
   
   module.exports = router;
   ```
   
3. 在需要使用的地方引用出來

4. 因為在引用的時候已經標記該 URL 下方的 API 全部導向引用的 `Module`

5. 在Module的地方URL只需要標示 `'/'`

```js
const express = require('express');
const comp = require('./routes/company.js');
const app = express();

app.use('/api/company', comp);

const port = process.env.PORT || 5555

app.listen(port,()=>{
    console.log(`Listening on port ${config.Port()}`);
})
```





## Get

- `req.params` 用以取得URL後方之必要參數 ex. /api/user/ ***sean***
- `req.query`  用以取得URL ?後方之變數 ex. /api/user/sean?***age = 18***
- Return值只能回傳String，否則會報錯
  - `The "chunk" argument must be one of type string or Buffer. Received type object`

```javascript
const express = require('express');
const SiteModel = require('../schemas/site.js');

const router = express.Router();


router.get('/', function (req, res) {	
    SiteModel.find()
    .sort({code: -1}) // -1 DESC , 1 ASC
    //.sort('-code')
    .then(result => res.send(result))
    .catch(error => console.log(error.message));
});

router.get('/:code', function (req, res) {	
     SiteModel.find({code : req.params.code}) 
    .then(result => res.send(result))
    .catch(error => console.log(error.message));
});
```

- 上述條件判斷式也可以改用Moongose提供的另一種寫法

```javascript
router.get('/:code', function (req, res) {	
    SiteModel.find()
    .where('code').equals(req.params.code)
    .then(result => res.send(result))
    .catch(error => console.log(error.message));
});
/* 其他可用
eq : equal
ne : not equal
gt : great then
lt : less then
gte: 大於等於
lte: 小於等於
nin: not in
*/
```

### Regular  Expressions

```javascript
/*
 /^CW/ 代表 CW%
 /CW$/ 代表 %CW
 /.*CW.*/ 代表 %CW%     
*/

router.get('/', function (req, res) {	
SiteModel.find({code: /.*cw*./i}) //加入i代表不分大小寫
    .sort({code: -1})
    .then(result => res.send(result))
    .catch(error => console.log(error.message));
});
```



## Post

- router指定js檔名，資料以body方式傳入
- 取得傳入內容以 `req.body`
- `app.use(express.json())` 將Body內部資料以JSON格式傳送
- console內容postman無法取得，僅會顯示Shell上

```javascript
const express = require('express');
const SiteModel = require('../schemas/site.js');

const router = express.Router();

router.post('/', function (req, res) {
    let site = new SiteModel({
        code : req.body.code,
        name: req.body.name,
        address: req.body.address,
        phones: req.body.phones,
        postalCode: req.body.postalCode
    });

    site.save()
    .then(res.send(site))
    .catch(error => console.log(error.message));
});

module.exports = router;
```

## Put

```javascript
router.put('/:code', (req, res) => {  
    SiteModel.update(
        {code: req.params.code},
        {
            $set:{
                address: req.body.address,
                phones: "req.body.phones"
            }
        },
        {useNewUrlParser: true}
    )
    .then(result => res.send(result))
    .catch(error => console.log(error.message));    
});

//OR


router.put('/:id', (req, res) => {  
    SiteModel.findByIdAndUpdate(req.params.id,
        {
            $set: req.body
        },
        {New: true}
    )
    .then(result => res.send(result))
    .catch(error => console.log(error.message));    
});

```



## Delete

```javascript
outer.delete('/:id', function (req, res) {	
    SiteModel.deleteMany({_id: req.params.id})
    .then(result => res.send(result))
    .catch(error => console.log(error.message));
});

//OR
router.delete('/:id', function (req, res) {	
    SiteModel.findByIdAndRemove(req.params.id)
    .then(result => res.send(result))
    .catch(error => console.log(error.message));
});
```

## Remove try_catch Block

1. 建置共用方法處理API發生的錯誤事件

2. 引用MiddleWare進行處理

   ```javascript
   module.exports = function(event){
       return async (req, res) =>
       {
           try {
               console.log("Try Excute Func")
               await event(req, res);
           } catch (ex) {
               console.log("Error Occured!!!!!!")
               return res.status(500).send(ex.message);
           }
       };
   }
   
   // IN Router
   
   const handler = require('../helpers/apiErrorHandler');
   router.post('/', handler(async (req, res) => {
       let user  = await User.findOne({mail: req.body.mail});
       if(!user)
           return res.status(400).send("Invaild Email");
       let valid =await bcrypt.compare(req.body.password, user.password)
       if(!valid)
           return res.status(400).send("Invaild Email OR Password");
   
       const token = user.getAuthToken();
       return res.send(token)
   }));
   ```

   

3. 或是使用插件 

   ```javascript
   yarn add express-async-errors
   
   // index.js 
   require('express-async-errors');
   
   //寫一個MiddleWare 專門處理錯誤
   function error(err, req, res, next){
       console.log("Error: ",err)
       res.status(500).send('Something Errors');
       next();
   }
   ```

## Logger

1. 安裝`winston`

2. 在錯誤處理的地方加入

   ```javascript
   //middleWare/error.js
   const winston = require('winston')
   
   module.exports = function (err, req, res, next){
       winston.error(err)
       res.status(500).send('Something Errors');
       next();
   }
   
   //console: {"message":"Error Occured12345","level":"error"}
   ```

3. 也可以建立Logger File記錄錯誤內容

   ```javascript
   //index.js
   
   const logger = require('winston')
   logger.add(new  logger.transports.File({ filename: 'logger.log' }));
   
   /* IN logger.log
   {"message":"Error Occured12345","level":"error"}
   {"message":"Error Occured!!!!","level":"error"}
   {"level":"error","message":"Error Occured!!!!"}*/
   
   ```

4. 上述middleware只有在Call API時會產生，若要處理建置時的錯誤

   ```javascript
   //index.js
   logger.exceptions.handle(
       new logger.transports.File({ filename: './logs/exceptions.log' })
   );
   
   //處理promise reject
   process.on('unhandledRejection',(err)=>{
       console.log("Promise Rejection Occured");
       throw err;
   });
   ```

   

## Refractoring 

1. 將index.js中的app相關內容移置單獨資料夾

   ```javascript
   // startup/route.js
   const express = require('express');
   const homeRouter = require('../routers/homeRouter');
   const userRouter = require('../routers/userRouter');
   const authRouter = require('../routers/authRouter');
   const siteRouter = require('../routers/siteRouter');
   const empRouter = require('../routers/employeeRouter');
   const error = require('../middle/error');
   
   module.exports = function(){
       const app = express();
       app.use(express.json());
       app.use('/', homeRouter);
       app.use('/api/user', userRouter);
       app.use('/api/site', siteRouter);
       app.use('/api/auth', authRouter);
       app.use('/api/employee', empRouter);
       app.use(error);
       return app;
   }
   ```

2. 將logger 移除

   ```javascript
   const logger = require('winston');
   
   module.exports = function(){
       logger.add(new logger.transports.File({ filename: './logs/logger.log' }));
   
       logger.exceptions.handle(
           new logger.transports.File({ filename: './logs/exceptions.log' })
       );
       process.on('unhandledRejection',(err)=>{
           console.log("Promise Rejection Occured");
           throw err;
       });
   }
   ```

   

3. 調整index.js

   ```javascript
   require('dotenv').config();
   require('./startup/logger')();
   require('express-async-errors');	
   require('./middle/connect')();
   
   const config = require('config');
   const app = require('./startup/route')();
   
   let port = config.get('Customer.port');
   
   app.listen(port,()=>{
       console.log(`Now ENV ${process.env.NODE_ENV}, Port : ${port}`);
   })
   ```

   

## Docker File

```powershell
FROM node:12-alpine

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN yarn install
# If you are building your code for production
# RUN npm ci --only=production

# Bundle app source
COPY . .

EXPOSE 5001
CMD [ "node", "server.js" ]
```



# MongoDB

1. 啟動Shell並移至專案目錄下方

   ```powershell
    npm install mongodb
   ```

2. 使用 `var mongo = require('mongodb');` 操作mongodb模組

## Creating  Database

1. To create a database in MongoDB, start by creating a MongoClient object
2. Then specify a connection URL with the correct ip address and the name of the database you want to create
3. MongoDB will create the database if it does not exist, and make a connection to it.

```javascript
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/mydb";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  console.log("Database created!");
  db.close();
});
```



## Creating a Collection

- **Important:** In MongoDB, a collection is not created until it gets content!
- MongoDB waits until you have inserted a document before it actually creates the collection.

```javascript
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.createCollection("customers", function(err, res) {
    if (err) throw err;
    console.log("Collection created!");
    db.close();
  });
});
```

## Insert

- A **document** in MongoDB is the same as a **record** in MySQL
- The first parameter of the `insertOne()` method is an object containing the name(s) and value(s) of each field in the document you want to insert.
- It also takes a callback function where you can work with any errors, or the result of the insertion

```javascript
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
    
  var myobj = { name: "Company Inc", address: "Highway 37" };
  dbo.collection("customers").insertOne(myobj, function(err, res) {
    if (err) throw err;
    console.log("1 document inserted");
    db.close();
  });
});
```

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
    
  var myobj = [
    { name: 'John', address: 'Highway 71'},
    { name: 'Peter', address: 'Lowstreet 4'},
    { name: 'Amy', address: 'Apple st 652'},
    { name: 'Hannah', address: 'Mountain 21'},
    { name: 'Michael', address: 'Valley 345'},
    { name: 'Sandy', address: 'Ocean blvd 2'},
    { name: 'Betty', address: 'Green Grass 1'},
    { name: 'Richard', address: 'Sky st 331'},
    { name: 'Susan', address: 'One way 98'},
    { name: 'Vicky', address: 'Yellow Garden 2'},
    { name: 'Ben', address: 'Park Lane 38'},
    { name: 'William', address: 'Central st 954'},
    { name: 'Chuck', address: 'Main Road 989'},
    { name: 'Viola', address: 'Sideway 1633'}
  ];
  dbo.collection("customers").insertMany(myobj, function(err, res) {
    if (err) throw err;
    console.log("Number of documents inserted: " + res.insertedCount);
    db.close();
  });
});
```

## Find

- Find()內的第一個參數代表查詢的條件(query object)
- {} 代表沒有條件

```javascript
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.collection("customers").find({}, { projection: { _id: 0, name: 1, address: 1 } }).toArray(function(err, result) {
    if (err) throw err;
    console.log(result);
    db.close();
  });
});
```

### 找尋特定欄位

- The second parameter of the `find()` method is the `projection` object that describes which fields to include in the result.
- 除了 _id 欄位外，無法同時存在 0 跟 1的設定
- 定義了某些欄位為0後，其餘欄位會顯示出來

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.collection("customers").find({}, { projection: { _id: 0, name: 1, address: 1 } }).toArray(function(err, result) {
    if (err) throw err;
    console.log(result);
    db.close();
  });
});

/*
[
  { name: 'John', address: 'Highway 71'},
  { name: 'Peter', address: 'Lowstreet 4'},
  { name: 'Amy', address: 'Apple st 652'},
  { name: 'Hannah', address: 'Mountain 21'},
  { name: 'Michael', address: 'Valley 345'},
  { name: 'Sandy', address: 'Ocean blvd 2'},
  { name: 'Betty', address: 'Green Grass 1'},
  { name: 'Richard', address: 'Sky st 331'},
  { name: 'Susan', address: 'One way 98'},
  { name: 'Vicky', address: 'Yellow Garden 2'},
  { name: 'Ben', address: 'Park Lane 38'},
  { name: 'William', address: 'Central st 954'},
  { name: 'Chuck', address: 'Main Road 989'},
  { name: 'Viola', address: 'Sideway 1633'}
]
*/
```

### 

```javascript
ongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var query = { address: "Park Lane 38" };
  dbo.collection("customers").find(query).toArray(function(err, result) {
    if (err) throw err;
    console.log(result);
    db.close();
  });
});
```

### Filter With Regular Expressions

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var query = { address: /^S/ };
  dbo.collection("customers").find(query).toArray(function(err, result) {
    if (err) throw err;
    console.log(result);
    db.close();
  });
});
```

### limit

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.collection("customers")
  .find()
  .limit(5)
  .toArray(function(err, result) {
    if (err) throw err;
    console.log(result);
    db.close();
  });
});
```

### Sort

- { name: 1 } // ascending
- { name: -1 } // descending

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
    
  dbo.collection("customers")
      .find({}, { projection: { _id: 0} })
      .sort({ name: -1 })
      .toArray(function(err, result) {
        if (err) throw err;
        console.log(result);
        db.close();
  });
});
```

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var mysort = { name: -1 };
    
  dbo.collection("customers")
      .find()
      .sort(mysort)
      .toArray(function(err, result) {
        if (err) throw err;
        console.log(result);
        db.close();
  });
});
```

## Delete 

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var myquery = { address: 'Mountain 21' };
    
  dbo.collection("customers")
      .deleteOne(myquery, function(err, obj) {
        if (err) throw err;
        console.log("1 document deleted");
        db.close();
  });
});
```

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var myquery = { address: /^O/ };
  dbo.collection("customers")
      .deleteMany(myquery, function(err, obj) {
        if (err) throw err;
        console.log(obj.result.n + " document(s) deleted");
        db.close();
  });
});
```

## Drop

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.collection("customers")
      .drop(function(err, delOK) {
        if (err) throw err;
        if (delOK) console.log("Collection deleted");
        db.close();
  });
});
```

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.dropCollection("customers", function(err, delOK) {
    if (err) throw err;
    if (delOK) console.log("Collection deleted");
    db.close();
  });
});
```

## Update

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var myquery = { address: "Valley 345" };
  var newvalues = { $set: {name: "Mickey", address: "Canyon 123" } };
    
  dbo.collection("customers")
      .updateOne(myquery, newvalues, function(err, res) {
        if (err) throw err;
        console.log("1 document updated");
        db.close();
  });
});
```

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  var myquery = { address: /^S/ };
  var newvalues = {$set: {name: "Minnie"} };
    
  dbo.collection("customers")
      .updateMany(myquery, newvalues, function(err, res) {
        if (err) throw err;
        console.log(res.result.nModified + " document(s) updated");
        db.close();
  });
});
```

## Join 

- MongoDB is not a relational database, but you can perform a left outer join by using the `$lookup` stage.
- `$lookup` stage lets you specify which collection you want to join with the current collection, and which fields that should match.

![img](C:\Users\sean2\AppData\Local\LINE\Cache\tmp/1561987036706.jpg)

```javascript
MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("mydb");
  dbo.collection('orders').aggregate([
    { $lookup:
       {
         from: 'products',
         localField: 'product_id',
         foreignField: '_id',
         as: 'orderdetails'
       }
     }
    ]).toArray(function(err, res) {
    if (err) throw err;
    console.log(JSON.stringify(res));
    db.close();
  });
});
/*
    [
      { "_id": 1, "product_id": 154, "status": 1, "orderdetails": [
        { "_id": 154, "name": "Chocolate Heaven" } ]
      }
    ]
*/
```

## Mongoose

### Schema

- mongoose 的 Schema 概念就是用 schema-based 的方式，定義一個 collection 的組成結構

  ```javascript
  var Schema = mongoose.Schema
  var UserSchema = new Schema(
    {
       name: {
          type: String,
          required: '請輸入名字', 
        },
      login:     { type: String, unique: true },
      email:     { type: String, unique: true, required: true }, //unique index
      age: { 
          type: Number, 
          min: 18, 
          max: 65, 
          required: true 
      },
      create_at: { type: Date, default: Date.now },
    	status: {
          type: [{
            type: String,
            enum: ['pending', 'ongoing', 'completed']
          }],
          default: ['pending']
        }
    }
  )
  
  schema.index({name:1, mail:1}, {name:'mailnameindex', unique: true}) //建立複合索引
  //Here options refers MongoDB index options like 'unique','sparse','name','expireAfterSeconds' etc.
  ```

- 因為 MongoDB 是 schema-less 相當有彈性，所以如果上面這個 schema 某些「欄位」沒有賦值，那麼在 MongoDB 裡就不會有那個「欄位」

- ##### 欄位屬性

  - `required`: boolean or function, if true adds a [required validator](https://mongoosejs.com/docs/validation.html#built-in-validators) for this property
  - `default`: Any or function, sets a default value for the path. If the value is a function, the return value of the function is used as the default.
  - `select`: boolean, specifies default [projections](https://docs.mongodb.com/manual/tutorial/project-fields-from-query-results/) for queries
  - `validate`: function, adds a [validator function](https://mongoosejs.com/docs/validation.html#built-in-validators) for this property
  - `get`: function, defines a custom getter for this property using [`Object.defineProperty()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty).
  - `set`: function, defines a custom setter for this property using [`Object.defineProperty()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty).
  - `alias`: string, mongoose >= 4.10.0 only. Defines a [virtual](https://mongoosejs.com/docs/guide.html#virtuals) with the given name that gets/sets this path.

- ##### 索引

  - `index`: boolean, whether to define an [index](https://docs.mongodb.com/manual/indexes/) on this property.
  - `unique`: boolean, whether to define a [unique index](https://docs.mongodb.com/manual/core/index-unique/) on this property.
  - `sparse`: boolean, whether to define a [sparse index](https://docs.mongodb.com/manual/core/index-sparse/) on this property.

- ##### String

  - `lowercase`: boolean, whether to always call `.toLowerCase()` on the value
  - `uppercase`: boolean, whether to always call `.toUpperCase()` on the value
  - `trim`: boolean, whether to always call `.trim()` on the value
  - `match`: RegExp, creates a [validator](https://mongoosejs.com/docs/validation.html) that checks if the value matches the given regular expression
  - `enum`: Array, creates a [validator](https://mongoosejs.com/docs/validation.html) that checks if the value is in the given array.
  - `minlength`: Number, creates a [validator](https://mongoosejs.com/docs/validation.html) that checks if the value length is not less than the given number
  - `maxlength`: Number, creates a [validator](https://mongoosejs.com/docs/validation.html) that checks if the value length is not greater than the given number

- ##### Number

  - `min`: Number, creates a [validator](https://mongoosejs.com/docs/validation.html) that checks if the value is greater than or equal to the given minimum.
  - `max`: Number, creates a [validator](https://mongoosejs.com/docs/validation.html) that checks if the value is less than or equal to the given maximum.

- ##### Date

  - `min`: Date
  - `max`: Date

### Model

- mongoose 的 Model 概念，則是對一個 collection 結構定義與操作方法的集合

- 也就是用 Schema 定義了一個 collection 的結構，加上其他對這個 collection 的驗證設定、操作方法等等，便構成了一個 Model

  ```javascript
  mongoose.model('user', UserSchema) //第一個參數對應資料表名稱
  ```

- 當要使用這個 model 只要用 `mongoose.model()` 將 model 讀出來

  ```javascript
  var User = mongoose.model('User')
  
  User.getUserByLogin(login, function(err, user) {
    // here we have a user...
  })
  ```



### Validation

#### Require Validators

```javascript
const courseSchema = new mongoose.Schema({
    name: {
        type: String , 
        required: '請輸入名字'
    },
    author: String,
    tags :[String],
    date : Date,
    isPublished:Boolean,
    price:Number
});

const Course = mongoose.model('course',courseSchema)

async function CreateCourse(){
    try{
        const course = new Course({
            author: "sean",
            tags : ["C#","backend"]
        });

        let result = await course.save();

        console.log(result)
    }catch(ex)
    {
        console.error(ex.message)
    }
}

CreateCourse();

//console: course validation failed: name: Path `name` is required.
```

#### Validators with condition

```javascript
const courseSchema = new mongoose.Schema({
    name: {type: String ,unique: true, required: true},
    author: String,
    tags :[String],
    date : Date,
    isPublished:Boolean,
    price: {
        type: Number,
        required : function(){return this.isPublished} //當isPubluish為True時，才是必填
    }
});

async function CreateCourse(){
    try{
        const course = new Course({
            author: "sean",
            tags : ["C#","backend"],
            isPublished: true
        });

        let result = await course.save();

        console.log(result)
    }catch(ex)
    {
        console.error(ex.message)
    }
}

//console:  course validation failed: 
//price: Path `price` is required., 
//name: Path `name` is required.
```

#### Built-In Validators

```javascript
const courseSchema = new mongoose.Schema({
    name: {
        type: String , 
        required: true,
        minlength: 5,	//最小長度
        maxlength: 20	//最大長度
    },
    author: String,
    tags :{
        type: [String],
        enum: ["frontEnd","backend",".Net"]	//限定內容必須為陣列內資料
    },
    date : Date,
    isPublished:Boolean,
     price: {
        type: Number,
        required : function(){return this.isPublished},
        min: 100,
        max: 500
    }
});

async function CreateCourse(){
    try{
        const course = new Course({
            name: ".Net",
            author: "sean",
            tags : [".Net#","#backend"],
            isPublished: false
        });

        let result = await course.save();

        console.log(result)
    }catch(ex)
    {
        console.error(ex.message)
    }
}

//console:  course validation failed: 
// Path `name` (`.Net`) is shorter than the minimum allowed length (5)., 
// tags.0: `.Net#` is not a valid enum value for path `tags.0`.
// tags.1: `#backend` is not a valid enum value for path `tags.1`.
```

#### Custom Validators

```javascript
const courseSchema = new mongoose.Schema({
    name: {
        type: String , 
        required: true,
        minlength: 5,
        maxlength: 20
    },
    author: String,
    tags :{
        type: Array,
        validate:{
            validator: function(value){	//驗證Function
                return value.length > 0
            },
            message: 'Every Course should hava at less one tag' //回傳訊息
        }
    },
    date : Date,
    isPublished:Boolean,
    price: {
        type: Number,
        required : function(){return this.isPublished},
        min: 100,
        max: 500
    }
});

async function CreateCourse(){
    try{
        const course = new Course({
            name: ".Net Core",
            author: "sean",
            tags : [],
            isPublished: false
        });

        let result = await course.save();

        console.log(result)
    }catch(ex)
    {
        console.error(ex.message)
    }
}

//console:  course validation failed: 
// tags: Every Course should hava at less one tag
```



#### SchemaType Options

```javascript
const courseSchema = new mongoose.Schema({
    name: {
        type: String , 
        required: true,
        minlength: 5,
        maxlength: 20
    },
    author: String,
    tags :{
        type: Array,
        validate:{
            validator: function(value){
                return value.length > 0
            },
            message: 'Every Course should hava at less one tag' 
        }
    },
    date : Date,
    isPublished:Boolean,
    price: {
        type: Number,
        required : function(){return this.isPublished},
        min: 100,
        max: 500,
        get: v => `NT.${v}`,	// 設定取得時處理
        set: v=> Math.round(v) // 設定儲存時四捨五入
    }
});

async function CreateCourse(){
    try{
        const course = new Course({
            name: ".Net Core",
            author: "sean",
            tags : ["backEnd"],
            isPublished: false,
            price: 152.8
        });

        let result = await course.save();

        console.log(result)
    }catch(ex)
    {
        console.error(ex.message)
    }
}
//Save
//{ tags: [ 'backEnd' ],
// _id: 5e2aa3f5eb371033b41a4476,
// name: '.Net Core',
// author: 'sean',
//  isPublished: false,
//  price: 153,
//  __v: 0 }
```

### Methods

- Schema 可內嵌methods供Model使用


```javascript
const schema = new mongoose.Schema({
    name: {
        type: String , 
        required: '請輸入名字',
        maxlength: 20,
    },
    password: {
        type: String , 
        required: true,
        minlength: 6
    },
    mail :{
        type: String , 
        required: true,
        unique: true,
        validate:{
            validator: function(value){
                return ValidateEmail(value)
            },
            message: 'E-Mail 格式錯誤'
        }
    }
});

schema.methods.getAuthToken = function(){
  return jwt.sign({mail: this.mail, name: this.name, password : this.password}, config.get('Customer.jwt.secret'))
}

module.exports = mongoose.model('User', schema);

//use
router.post('/', async (req, res) => {
    try{
        let user  = await User.findOne({mail: req.body.mail});
        const token = user.getAuthToken();
        res.send(token)
    }catch(ex)
    {
        res.send(ex.message)
    }  
});
```



### Connections

#### Operation Buffering

- Mongoose lets you start using your models immediately, without waiting for mongoose to establish a connection to MongoDB

```javascript
var MyModel = mongoose.model('Test', new Schema({ name: String }));
// Will just hang until mongoose successfully connects
MyModel.findOne(function(error, result) { /* ... */ });

setTimeout(function() {
  mongoose.connect('mongodb://localhost:27017/myapp', {useNewUrlParser: true});
}, 60000);
```

- To disable buffering, turn off the [`bufferCommands` option on your schema](https://mongoosejs.com/docs/guide.html#bufferCommands).

```javascript
mongoose.set('bufferCommands', false);
```

#### Options

- The `connect` method also accepts an `options` object which will be passed on to the underlying MongoDB driver

```javascript
mongoose.connect(uri, options);
```

- `bufferCommands` - This is a mongoose-specific option (not passed to the MongoDB driver) that disables [mongoose's buffering mechanism](http://mongoosejs.com/docs/faq.html#callback_never_executes)
- `user`/`pass` - The username and password for authentication. These options are mongoose-specific, they are equivalent to the MongoDB driver's `auth.user` and `auth.password` options.
- `autoIndex` - By default, mongoose will automatically build indexes defined in your schema when it connects. This is great for development, but not ideal for large production deployments, because index builds can cause performance degradation. If you set `autoIndex` to false, mongoose will not automatically build indexes for **any** model associated with this connection.
- `dbName` - Specifies which database to connect to and overrides any database specified in the connection string. If you're using the `mongodb+srv` syntax to connect to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas), you [should use `dbName` to specify the database](https://stackoverflow.com/questions/48917591/fail-to-connect-mongoose-to-atlas/48917626#48917626) because you currently cannot in the connection string.
- `useNewUrlParser` - The underlying MongoDB driver has deprecated their current [connection string](https://docs.mongodb.com/manual/reference/connection-string/) parser. Because this is a major change, they added the `useNewUrlParser` flag to allow users to fall back to the old parser if they find a bug in the new parser. You should set `useNewUrlParser: true` unless that prevents you from connecting. Note that if you specify `useNewUrlParser: true`, you **must** specify a port in your connection string, like `mongodb://localhost:27017/dbname`. The new url parser does *not* support connection strings that do not have a port, like `mongodb://localhost/dbname`.
- `useCreateIndex` - False by default. Set to `true` to make Mongoose's default index build use `createIndex()`instead of `ensureIndex()` to avoid deprecation warnings from the MongoDB driver.
- `useFindAndModify` - True by default. Set to `false` to make `findOneAndUpdate()` and `findOneAndRemove()` use native `findOneAndUpdate()` rather than `findAndModify()`.
- `autoReconnect` - The underlying MongoDB driver will automatically try to reconnect when it loses connection to MongoDB. Unless you are an extremely advanced user that wants to manage their own connection pool, do **not** set this option to `false`.
- `reconnectTries` - If you're connected to a single server or mongos proxy (as opposed to a replica set), the MongoDB driver will try to reconnect every `reconnectInterval` milliseconds for `reconnectTries` times, and give up afterward. When the driver gives up, the mongoose connection emits a `reconnectFailed` event. This option does nothing for replica set connections.
- `reconnectInterval` - See `reconnectTries`
- `promiseLibrary` - sets the [underlying driver's promise library](http://mongodb.github.io/node-mongodb-native/2.1/api/MongoClient.html)
- `poolSize` - The maximum number of sockets the MongoDB driver will keep open for this connection. By default, `poolSize` is 5. Keep in mind that, as of MongoDB 3.4, MongoDB only allows one operation per socket at a time, so you may want to increase this if you find you have a few slow queries that are blocking faster queries from proceeding.
- `bufferMaxEntries` - The MongoDB driver also has its own buffering mechanism that kicks in when the driver is disconnected. Set this option to 0 and set `bufferCommands` to `false` on your schemas if you want your database operations to fail immediately when the driver is not connected, as opposed to waiting for reconnection.
- `connectTimeoutMS` - How long the MongoDB driver will wait before killing a socket due to inactivity *during initial connection*. Defaults to 30000. This option is passed transparently to [Node.js' `socket#setTimeout()`function](https://nodejs.org/api/net.html#net_socket_settimeout_timeout_callback).
- `socketTimeoutMS` - How long the MongoDB driver will wait before killing a socket due to inactivity *after initial connection*. A socket may be inactive because of either no activity or a long-running operation. This is set to `30000` by default, you should set this to 2-3x your longest running operation if you expect some of your database operations to run longer than 20 seconds. This option is passed to [Node.js `socket#setTimeout()`function](https://nodejs.org/api/net.html#net_socket_settimeout_timeout_callback) after the MongoDB driver successfully completes.
- `family` - Whether to connect using IPv4 or IPv6. This option passed to [Node.js' `dns.lookup()`](https://nodejs.org/api/dns.html#dns_dns_lookup_hostname_options_callback) function. If you don't specify this option, the MongoDB driver will try IPv6 first and then IPv4 if IPv6 fails. If your `mongoose.connect(uri)` call takes a long time, try `mongoose.connect(uri, { family: 4 })`

```javascript
const options = {
  useNewUrlParser: true,
  useCreateIndex: true,
  useFindAndModify: false,
  autoIndex: false, // Don't build indexes
  reconnectTries: Number.MAX_VALUE, // Never stop trying to reconnect
  reconnectInterval: 500, // Reconnect every 500ms
  poolSize: 10, // Maintain up to 10 socket connections
  // If not connected, return errors immediately rather than waiting for reconnect
  bufferMaxEntries: 0,
  connectTimeoutMS: 10000, // Give up initial connection after 10 seconds
  socketTimeoutMS: 45000, // Close sockets after 45 seconds of inactivity
  family: 4 // Use IPv4, skip trying IPv6
};
mongoose.connect(uri, options);
```

#### Callback

- The `connect()` function also accepts a callback parameter and returns a [promise](https://mongoosejs.com/docs/promises.html).

```javascript
mongoose.connect(uri, options, function(error) {
  // Check error in initial connection. There is no 2nd param to the callback.
});

// Or using promises
mongoose.connect(uri, options).then(
  () => { /** ready to use. The `mongoose.connect()` promise resolves to mongoose instance. */ },
  err => { /** handle initial connection error */ }
);
```

#### Connection String Options

- You can also specify driver options in your connection string as [parameters in the query string](https://en.wikipedia.org/wiki/Query_string) portion of the URI. 
- This only applies to options passed to the MongoDB driver. You **can't** set Mongoose-specific options like `bufferCommands` in the query string.
- 使用query string缺點是不好閱讀

```javascript
mongoose.connect('mongodb://localhost:27017/test?connectTimeoutMS=1000&bufferCommands=false');
// The above is equivalent to:
mongoose.connect('mongodb://localhost:27017/test', {
  connectTimeoutMS: 1000
  // Note that mongoose will **not** pull `bufferCommands` from the query string
});
```

#### Virtual properties

- Virtual properties are document properties that you can get and set but that do not get persisted to MongoDB
- The `getters` are useful for formatting or combining fields
- The `setters` are useful for de-composing a single value into multiple values for storage

```javascript
 // define a schema
  var personSchema = new Schema({
    name: {
      first: String,
      last: String
    }
  });

/*
    Suppose you want to print out the person's full name.
    console.log(axl.name.first + ' ' + axl.name.last); 
*/
    personSchema.virtual('fullName').get(function () {
      return this.name.first + ' ' + this.name.last;
    });
  // compile our model
  var Person = mongoose.model('Person', personSchema);

  // create a document
  var axl = new Person({
    name: { first: 'Axl', last: 'Rose' }
  });

console.log(axl.fullName);

```

- You can also add a custom setter to your virtual that will let you set both first name and last name via the `fullName` virtual.

```javascript
personSchema.virtual('fullName').
  get(function() { return this.name.first + ' ' + this.name.last; }).
  set(function(v) {
    this.name.first = v.substr(0, v.indexOf(' '));
    this.name.last = v.substr(v.indexOf(' ') + 1);
  });

axl.fullName = 'William Rose'; // Now `axl.name.first` is "William"
```

#### Searching for records

- If you specify a callback, as shown above, the query will execute immediately. 
- The callback will be invoked when the search completes.

```javascript
var Athlete = mongoose.model('Athlete', yourSchema);

// find all athletes who play tennis, selecting the 'name' and 'age' fields
Athlete.find({ 'sport': 'Tennis' }, 'name age', function (err, athletes) {
  if (err) return handleError(err);
  // 'athletes' contains the list of athletes that match the criteria.
})
```

- If you don't specify a callback then the API will return a variable of type [Query](http://mongoosejs.com/docs/api.html#query-js). 
- You can use this query object to build up your query and then execute it (with a callback) later using the `exec()` method.

```javascript
// find all athletes that play tennis
var query = Athlete.find({ 'sport': 'Tennis' });

// selecting the 'name' and 'age' fields
query.select('name age');

// limit our results to 5 items
query.limit(5);

// sort by age
query.sort({ age: -1 });

// execute the query at a later time
query.exec(function (err, athletes) {
  if (err) return handleError(err);
  // athletes contains an ordered list of 5 athletes who play Tennis
})
```

- We can also do this using a `where()` function, and we can chain all the parts of our query together using the dot operator (.) rather than adding them separately. 

```javascript
Athlete.
  find().
  where('sport').equals('Tennis').
  where('age').gt(17).lt(50).  //Additional where query
  limit(5).
  sort({ age: -1 }).
  select('name age').
  exec(callback); // where callback is the name of our callback function.
```



## 資料關聯

### Referencing Documents

- 透過欄位關聯的方式取得資料，可確保資料一致性但效能差

- Mongoose 可以透過 _id對應的方式取得關聯資料

  1. 建立關聯欄位

     ```javascript
     const courseSchema = new mongoose.Schema({
         name: {
             type: String , 
             required: true,
             minlength: 5,
             maxlength: 20
         },
         author: { 
             type: Schema.Types.ObjectId, 
             ref: 'Author' 
         }
     });
     // author 為關聯欄位
     //僅能對應objectId , ref代表對應的document
     ```

  2. 透過`populate` 取得關聯資料

     ```javascript
     router.get('/', function (req, res) {    
         Course.find()
         .populate('author','-_id -__v -account') 
      	//.populate('another') 關聯多資料表
         .then(r=> res.status(200).send(r))
         .catch(err => res.status(400).send(err.message));
     });
     // 透過.populate取得資料對應
     // 第二個參數可以決定取得欄位 -代表不要的
     
     //或者可用下列方式呈現
     //match可針對關聯的欄位下select條件
     router.get('/', function (req, res) {    
         Course.find({tags: regularString})
         .populate({
             path: 'author',
             match: { account: "sean" },
             select: '-_id'
           }) 
         .then(r=> res.status(200).send(r))
         .catch(err => res.status(400).send(err.message));
     });
     ```



### Embedding Documents

- 將資料內容直接塞入

- 查詢效能佳，但無法達成資料一致

  ```javascript
  const schoolSchema = new mongoose.Schema({
      name: {
          type: String , 
          required: true,
          maxlength: 20
      },
      course:
      {
          type: courseSchema, 
          required: true      
      }
  });
  
  module.exports = mongoose.model('School',schoolSchema);
  ```

- 此時，引入的Schema驗證依然會存在

  ![1580016752607](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1580016752607.png)

- 內部資料表依然可用關聯

  ```javascript
  router.get('/', function (req, res) {    
      School.find()
      .populate({
          path: 'course.author',
          select: '-_id'
        }) 
      .then(r=> res.status(200).send(r))
      .catch(err => res.status(400).send(err.message));
  });
  ```

  

## Transactions

1. Use Fawn

   ```javascript
   //yarn add fawn
   
   var Fawn = require('fawn');
   const mongoose = require('mongoose');
   Fawn.init(mongoose);
   
   router.post('/', async (req, res) => {
      try{
         let task = new Fawn.Task();
         let school = new School(req.body.school);  
         let author = new Author(req.body);  
   
         task.save(school);
         task.save(author);
   
         if(0 === 1)
          task.update('authors',
          {
             account: "sean"
          },
          {
             name:"987654",
             $inc: {salary: +10}
         })
         else
          return res.status(400).send("Error Occured")
   
         let result = await task.run({useMongoose: true}); //真正執行儲存動作
   
         res.send(result)
      }catch(ex)
      {
          res.status(400).send(ex.message)
      }  
   });
   
   ```

   

# 加密

## bcrypt 加密

- 將一個字串做雜湊加密

-  `saltRounds` 是在密碼學中的加鹽(salt)，加鹽的意思是在要加密的字串中加特定的字符，打亂原始的字符串，使其生成的散列結果產生變化，其參數越高加鹽次數多越安全相對的加密時間就越長。

- 安裝

  ```powershell
  yarn add bcrypt 
  yarn add node-gyp //如果第一行跳錯
  npm install --global --production windows-build-tools //如果上面還是錯誤
  ```

- 進行加密

  ```javascript
  const bcrypt = require('bcrypt');
  
  router.post('/', async (req, res) => {
      try{
          let user = new User(req.body);
          let salt = await bcrypt.genSalt(10); //加入特定字符，增加解析難度
          user.password = await bcrypt.hash(user.password, salt); //將密碼進行加密
  
          let result = await user.save();
          res.send(result)
      }catch(ex)
      {
          res.send(ex.message)
      }  
  });
  ```

  

- 密碼驗證

  ```javascript
  router.post('/', async (req, res) => {
      try{
          let user  = await User.findOne({mail: req.body.mail});
          if(!user)
              return res.status(400).send("Invaild Email OR Password");
          
          let valid =await bcrypt.compare(req.body.password, user.password) //要驗證的密碼與加密過的密碼
          if(!valid)
              return res.status(400).send("Invaild Email OR Password");
          res.send(user)
      }catch(ex)
      {
          res.send(ex.message)
      }  
  });
  ```

  

# Json Web Token

1.  `yarn add jsonwebtoken`

2. ```javascript
   const jwt = require('jsonwebtoken');
   router.post('/', async (req, res) => {
       try{
           let user  = await User.findOne({mail: req.body.mail});
           if(!user)
               return res.status(400).send("Invaild Email");
           
           let valid =await bcrypt.compare(req.body.password, user.password)
           if(!valid)
               return res.status(400).send("Invaild Email OR Password");
   
           const token = jwt.sign({mail: user.mail, name: user.name}, 	 config.get('Customer.jwt.secret'))
           res.send(token)
       }catch(ex)
       {
           res.send(ex.message)
       }  
   });
   ```

   
   
3. 建置token驗證middleware

   ```javascript
   //auth.js
   const jwt = require('jsonwebtoken');
   const config = require('config');
   
   module.exports = function auth(req, res, next){
       const token = req.header('x-auth-token');
       if(!token)
           return res.status(401).send('Access denied. No Token provided');
       
       try {
           const decoded = jwt.verify(token, config.get('Customer.jwt.secret'));
           req.user = decoded;
           next();
       } catch (error) {
           return res.status(401).send('Access denied. Invalid Token');        
       }
   }
   ```

4. router 加入驗證

   ```javascript
   const auth = require('../middle/auth');
   router.get('/', [auth], function (req, res) {
       console.log(req.user) // req.user是從middle取得的資料
       
       User.find()
       .then(r=> res.status(200).send(r))
       .catch(err => res.status(400).send(err.message));
   });
   //get第二個參數為選用的middle
   ```

   

# Integration Test

1. `yarn add jest --save-dev `

2. `yarn add supertest --save-dev`

3. 封裝index的server物件

   ```javascript
   require('dotenv').config();
   require('./startup/logger')();
   require('./startup/connect')();
   
   const config = require('config');
   const app = require('./startup/route')();
   
   let port = config.get('Customer.port');
   
   const server = app.listen(port,()=>{
       console.log(`Now ENV ${process.env.NODE_ENV}, Port : ${port}`);
   });
   
   module.exports = server;
   ```

   

4. 建立test資料夾及測試檔案 (一定要是 .test.js)

   ```javascript
   // ./tests/uset.test.js
   
   const request  = require('supertest');
   const User = require('../models/user');
   let server;
   
   describe('/api/user',()=>{
       beforeEach(() => { server = require('../index') });
       afterEach(() => {server.close();});
   
       describe('Get / ', () =>{
           it('Get User Data',async ()=>{
               const res = await request(server).get('/api/user');
               expect(res.body.some(r=>r.mail==="sean@1.com")).toBeTruthy();
           });
       });
   
         describe('Get /:mail', () =>{
           it('Get User Data from mail', async () => {            
               const token = new User().getAuthToken();
               
               const res = await request(server)
               .get('/api/user/' + "sean@1.com")
               .set('x-auth-token',token);
   
               expect(res.status).toBe(200);
               expect(res.body[0]).toHaveProperty('name',"朱書辰");
           });
       });
   })
   ```

   

5. 顯示測試統計資料 `--coverage`

   ```javascript
     //package.json
   
     "scripts": {
      "test": "jest --watchAll  --verbose --coverage"
     },
   ```

   

6. 執行測試 `npm test`

































------



# 參考

1. [Node.Js Tutorial]: https://www.w3schools.com/nodejs/nodejs_mongodb_insert.asp

2. [Express Turtorial]: http://expressjs.com/

3. [Mongoose]: https://mongoosejs.com/docs/

4. [How to manage secrets and configs using dotenv in Node.js and Docker](https://dev.to/getd/how-to-manage-secrets-and-configs-using-dotenv-in-node-js-and-docker-2214)