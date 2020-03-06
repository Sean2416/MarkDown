# CallBack function

## 說明

-  當一個函式執行完畢後所執行的那個函式就是所謂的 callback 函式.
- 「**把函式當作另一個函式的參數，透過另一個函式來呼叫它**」

## Example

```javascript
function do_a(){
  console.log( '`do_a`: 這會先出現');
}
 
function do_b(){
  console.log( '`do_b`: 跟著才是這個' );
}
 
do_a();
do_b();
```

- 照理來說，會先出現a再出現b的內容。

- 但是因為 javascript 是一個事件驅動的語言. 所以如果當 `do_a` 所花的時間比 `do_b` 久的話, `do_b` 的結果就會比 `do_a` 早出來.

- 為了要讓程式執行順序按照我們的預期執行，這時候就可以使用 CallBack function

  ```javascript
  function do_a(callBack){
    console.log( '`do_a`: 這會先出現');
    callBack();
  }
   
  function do_b(){
    console.log( '`do_b`: 跟著才是這個' );
  } 
  do_a(do_b);
  ```

  

# Promise

## 定義

- 簡單來說，Promise 就是「承諾」，可以想像成 A 承諾 B 要去辦事，辦完之後才會回報結果，而這個結果只有兩種狀況：成功與失敗，不會有處於成功失敗不明的中間狀況。

## 狀態

![promise_1](C:\Users\Administrator\Downloads\promise_1.png)

- pending(等待中)
  1. Promise初始狀態
  2. 可能會轉變到不是 fulfilled(已實現) 就是 rejected(已拒絕) 狀態
- fulfilled(已實現)
  1. 最後狀態，不會再變更為其他狀態
  2. 有一個不能變動的回傳值
- rejected(已拒絕) 
  1. 最後狀態，不會再變更為其他狀態
  2. 有一個不能變動的回傳值reason

## 基本語法

ES6 Promise 的實作中，會確保 Promise 物件一經實體化後就會固定住狀態，要不就是"已實現"，要不就是"已拒絕"

```javascript
const promise = new Promise(function(resolve, reject) {
  // 成功時
  resolve(value)
  // 失敗時
  reject(reason)
})

promise.then(
  function(value) {
    // on fulfillment(已實現時)
  },
  function(reason) {
    // on rejection(已拒絕時)
  }
)
```

## 實例

```javascript
  var promise = new Promise(function (resolve, reject) {
      if (vi.moveOrderHistoryMIList.length > 0) {
          resolve(vi.master.FormID)
      } else
          reject(reason)
  }).then(function (value) {
      vi.getSwineMoveInDetailList({
          FormID: value
      });
  }).catch(function (error) {
      vi.dlgQueryMI = false;
  });
```



# async / await

- async可以視為是new一個新的promise

- await則可以看做.then的意思。Promise 中完成會透過 then 來回傳，在 await 中他則是會等待這段函式完成後在往下繼續執行。

- 比較下面程式碼的差別即可了解

  ```javascript
  function resolveAfter2Seconds(x) {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve(x);
      }, 2000);
    });
  }
  
  async function add1() {
    console.log(2)
    const a = this.resolveAfter2Seconds(20);
    console.log(3)
    const b = this.resolveAfter2Seconds(30);
    console.log(4)
    let c =  a +  b;
    console.log(c);
  }
  
  add1();
  console.log(1)
  
  //不等帶回傳值，直接往下執行導致回傳結果尚未產生
  /*
  2
  3
  4
  [object Promise][object Promise]
  1
  */
  ```

  ```javascript
  async function add1() {
    console.log(2)
    const a = await this.resolveAfter2Seconds(20);
    console.log(3)
    const b = await this.resolveAfter2Seconds(30);
    console.log(4)
    let c =  a +  b;
    console.log(c);
  }
  
  add1();
  console.log(1)
  
  /*
  2
  1
  暫停兩秒等帶回傳值
  3
  暫停兩秒等帶回傳值
  4
  50
  */
  ```

  ```javascript
  async function add1() {
    console.log(2)
    const a =  this.resolveAfter2Seconds(20);
    console.log(3)
    const b =  this.resolveAfter2Seconds(30);
    console.log(4)
    let c = await a +await  b;
    console.log(c);
  }
  
  add1();
  console.log(1)
  
  /*
  2
  3
  4
  1
  暫停兩秒等帶回傳值
  50
  */
  ```

  

# Session









# Fetch 



































































































