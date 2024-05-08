# 概念

## 物件導向

### 類別(Class) VS 物件(Object)

- ###### 物件(Object)就是類別(Class)的實體，類別(Class)就是物件(Object)的定義

- ###### 類別的定義包含了資料的形式(屬性, Field)以及對資料的操作(方法, Method)

### 三大特性

- ##### 封裝

  - ###### 物件導向有著對物體內部用的定義，以及可以給外部的定義

  - ###### 將物件內部的資料隱藏起來，只能透過物件本身所提供的介面(interface)取得物件內部屬性或者方法，物件內部的細節資料或者邏輯則隱藏起來，其他物件即無法瞭解此物件的內部細節，若不經過允許之窗口(即此物件提供之方法)便無從更動此物件內之資料

  - **對一件事情只需要理解他的外在就好，不需要了解裡面內部的構造**。

    - ###### 例如：我們可以透過getInformation()取得車子資訊，然而我們並不需要知道他是如何取得資訊的。

- ##### 繼承

  - ###### 繼承是一種機制，可以保留某個類別或物件的實作方法。奠基在既有類別上創立一個新的類別，並建立階層的關係。

  - 物件繼承 parent 物件時，可以

    - 獲得了 parent 物件的所有屬性和方法
    - 用新的方式實現、但同時維持繼承而來的 parent 物件的原有行為
    - 重複使用或延伸原有的程式碼

- ##### 多型

  - ###### 「多型」代表提供不同類型的實體一個統一的介面，或使用一個單一的符號來表示多個不同的類型。

  - ###### 原有的類別提供一個上層**介面**或**抽象類別**，而目的是為了消除了類別與類別之間的耦合性，使程式更容易維護與擴充**。**

- ##### 多載(Overloading) 

  -  是指說在**相同類別**中，定義**名稱相同**，但是**參數個數不同**，或是**參數型態不同**的函式，這樣就可以利用參數個數或者參數型態，呼叫到對應的方法

### 介面 VS 抽象類別

- ###### 介面可以被理解為「**接口」**，讓外部可以透過這個接口與內部作溝通。**規範了方法能被外部使用的規則**

  - 定義不同種類的物件中，所擁有的相同屬性功能(ex. 飛機、鳥都會飛但是飛的方式不同)

  - 透過介面可以定義功能接口(涵式)及規範(參數)，讓所有繼承的物件都必須遵守規範實作該功能

    - Ex.戰士、法師、補師都有"攻擊、防守"的功能，但彼此的實作方式都不同。
    - 透過介面定義功能、防守等涵式(街口)，定義所有繼承的腳色都必須實做這些功能

  - ###### 介面是對行為的抽象

- ##### 抽像類別是**不能直接實例化**的類別。

  - ###### 用來定義相同種類的物件，所需要具備的共通特性；例如動物(Animal)，是所有動物的基底，所有動物具有吃、喝、睡覺等等共同的特性

  - ###### **一個抽象類可以提供沒有實現，或不完整的實現。**

  - ###### 抽像類型的每個實例都是某個具體子類型的實例

  - ###### 抽象類別是對類別的抽象

## Process 

- ###### 行程，亦可稱作處理程序、進程

- ###### Process 意旨已經執行並且 load 到記憶體中的 Program，行程中的每一行程式碼隨時都有可能被 CPU 執行。在實際生活中，點開應用程式就是將 Program 活化成 Process，我們在活動監控器 (mac) 或 jobs (linux) 中看到 PID，也就是執行中的 Process ID。Program 是工廠藍圖，Process 就是照著設計藍圖所完成的實體工廠。

- ##### 組成：

  - ###### 一個 Memory Space。相當於 Object 的 variable，不同 Process 的 Memory Space 也不同，彼此看不到對方的 Memory Space。

  - ###### 一個以上的 Thread。

- ###### 多工作業系統 (Multitasking Operating System) 可以同時執行數個 Process，然而一個 CPU 一次只能執行一個 Process (因此才有現在的多核處理器)，CPU 的總量又總是少於 Process 的運行總量，且 Process 會佔用記憶體。因此如何排程 (*Scheduling*)、如何有效管理記憶體 (*Memory Management*) 是作業系統 (OS) 所關注的事。

- #####  總結

  - ###### Process 是電腦中已執行 Program 的實體，每一個 Process 互相獨立。

  - ###### Process 需要一些資源才能完成工作，如 CPU、記憶體、檔案以及 I/O 裝置。

  - ###### Process 不是基本執行單位，而是 Thread (執行緒) 的容器。

  - ###### 每個 Process 由一個 Memory Space 和一個以上的 Thread 所組成。



## Thread

- ###### 同一個 Process 中會有很多個 Thread，每一個 Thread 負責某一項功能。以聊天室 Process 為例，可以同時接受對方傳來的訊息以及發送自己的訊息給對方，就是同個 Process 中不同 Thread 的功勞。Thread 就是實體工廠內的工人，確保工廠的每項功能，並且共享工廠內的每一項資源。

- ##### 組成：

  - ###### Stack：紀錄從某個起始點開始 (例如main)，到目前為止所有函數的呼叫路徑，以及在這些呼叫路徑上所用到的區域變數。

  - ###### 紀錄 CPU 內部的暫存器 (如 Program Counter, Stack Pointer, Program Status Word 等) 的狀態。

- ###### 多執行緒中 (Multithreading)，兩個執行緒若同時存取或改變全域變數 (Global Variable)，可能會發生同步 (*Synchronization*) 問題。若執行緒之間互搶資源，則可能產生死結 (*Deadlock*)，如何避免 (*Prevent*) 或預防 (*Avoid*) 上述兩種情況的發生，仍是作業系統 (OS) 所關注的。

## Stack

- ###### 疊是限制插入元素和刪除元素只能在同一個位置的表(list)，該位置一般來說稱為棧頂(Top)。對堆疊的基本操作有 Push(推入，將資料加到棧頂) 和 Pop(彈出，將棧頂的資料移出) 這兩種操作。

- ###### 堆疊有時候也會被稱為先進後出表(LIFO，Last In First Out)，最先進入的資料會最後被彈出，如同函式呼叫，最先呼叫的函式會最後彈出記憶體。

- ###### 堆疊在軟體中是相當常見的應用，像是瀏覽器的上一頁就是一種應用，會按照你所存取的網頁，依照堆疊的順序進行存取(事實上，上一頁其實並非邏輯上的上一頁，如果現在在yahoo，上一頁是google，我們按下上一頁後會回到google，但再按上一頁事實上並不是回到yahoo...要不然就是死循環了...)

- ###### Stack的pop跟push之時間複雜度皆為常數, 即**O(1)**, 不涉及複製和移動操作

### 基本操作:

1. ##### push: 塞東西到stack

2. ##### pop: 把最上面的東西彈出來

3. ##### peek: 只觀看最上面的東西, 不要彈出來

### 實作方式

- ###### 陣列實作

  1. ###### 透過全域變數紀錄最後儲存的Index值，只要有Push 就Insert 進`arr[index++]`中

  2. ###### PoP透過Index固定取得最後一個塞入的值 `arr[index--]`

     ```C#
     public class Stack {
     
         private int[] data;
         private int top = -1; //陣列索引
     
         public Stack(int length) {
             data = new int[length];
         }
     
         public void push(int element) {
             if (top < this.data.length - 1) {
                 top++;
                 this.data[top] = element;
             }
         }
     
         public int pop() {
             return data[top--];
         }
     
         public int peek() {
             return data[top];
         }
     
         public boolean isEmpty() {
             return top == -1;
         }
     
         public boolean isFull() {
             return top == data.length - 1;
         }
     
         public int size() {
             return top + 1;
         }
     
     }
     ```


### Implement Stack using Queues

- ##### 本題僅可使用 Queue 資料結構作為來源 ，而在 Queue 當中有兩種標準定義的方法：

  - ###### shift：取出最左邊（最早被加入）的元素

  - ###### push：新加入的元素會邊加入到最右邊

- ##### 作法

  - ##### 使用兩個Queue(queue1、queue2)

  - ##### Push:

    - ###### 將資料先推至q2

    - ###### 將q1資料由前至後依序推往q2

    - ###### 將q2 asign q1，並且初始化q2

  - ##### Pop:

    - ###### 若rear>0代表裡面有值

      - ###### 將第一個元素取出，後面的元素往前搬
  
      - ###### rear-1
  
  - ```C#
     public class Stack
        {
            public int[] q1 = new int[100];
            public int[] q2 = new int[100];
            public int rear = 0;
      
            public void push(int x)
            {
                int index = 0;
      
                q2[index++] = x;
      
                if (rear > 0)
                {
                    for (var i = 0; i < rear; i++)
                        q2[index++] = q1[i];
                }
      
                q1 = q2;
                q2 = new int[100];
                rear++;
            }
      
            public void pop()
            {
                if (rear > 0)
                {
                    for (var i = 1; i < rear; i++)
                    {
                        q1[i - 1] = q1[i];
                        q1[i] = 0;
                    }
                    rear--;
                }
      
            }
      
            public int top()
            {
                if (rear > 0)
                    return q1[0];
      
                return -1;
            }
      
            public int size()
            {
                return rear;
            }
        }
    ```

## Queue

- ###### Queue是一種特殊的線性表, 限定只能在表的一端進行插入(隊尾), 而在另一端進行刪除操作(隊頭), 特點是"先進先出"(FIFO).

### 基本操作:

1. ###### insert:在隊尾插入資料

2. ###### remove: 從隊頭移走資料

3. ###### peek: 查看隊頭的資料

### 陣列實作

- ###### Insert: 透過index紀錄陣列最後的值，每次將資料存入最後一筆 `arr[rear++]`

  - ###### 其實我們所進行的事情，就只是改變隊尾(rear = rear + 1)，沒有涉及到需要移動到整個陣列的操作，因此，時間複雜度為O(1)。就像是排隊一樣，來的人只要接在原來排隊人群的後面就可以了。

- ###### POP: 透過front紀錄表頭位置，並且每次取出`arr[front]`並刪除後將陣列往前搬

  - ###### 針對出隊(Dequeue)進行分析，出隊為將頭(front)元素移除的操作，就像是排隊一樣，排頭的人走了，那其餘的人就必須往前進一格，也因此出隊會涉及到整個陣列的移動，時間複雜度為O(N)

    ![img](https://i.imgur.com/idLKa7f.png)

- ###### 如果想要維持O(1)，可以透過不搬移陣列的方式僅刪除頭部元素即可。但這樣會造成假溢位問題

  ![img](https://i.imgur.com/gOtSVFy.png)

#### 假溢出(False overflow)

- ###### 如下圖所示，由於我們在Pop出前面的元素時沒有進行陣列的搬移。導致前面的空間浪費無法插入，此時可以透過`Circular Quee`

- ![img](https://i.imgur.com/IQ16QUm.png)

#### Circular Queue

- ###### 要解決假溢出的問題，其實我們只要將rear跑到陣列界線時，再往下走一格會回到陣列的頭就可以解決了，也就是使陣列的頭尾相接，這種陣列我們稱之為循環陣列(circular array)。

- ![img](https://i.imgur.com/q7rI6ZC.png)

###  Implement Queue using Stacks

- ##### 本題僅可使用 Stack 資料結構作為來源 ，而在 Stack 當中有兩種標準定義的方法：

  - ###### pop：取出最上邊（最新被加入）的元素

  - ###### push：新加入的元素會邊加入到最上方

- ###### 準備兩個Stack

  - ###### Push:

    - ###### 每次使用時，會先把 stack2 倒過來放進 stack1 中

    - ###### 確定 stack2 空了以後，再放入新資料

    - ![https://ithelp.ithome.com.tw/upload/images/20210920/201413363IHUqH8HPq.png](https://ithelp.ithome.com.tw/upload/images/20210920/201413363IHUqH8HPq.png)

  - ##### Pop: (達到先進先出)

    - ###### 每次使用時，會先把 stack1 倒過來放進 stack2 中(達到先進先出)

    - ###### 確定 stack1 空了以後，在從stack2取出資料

    - ![https://ithelp.ithome.com.tw/upload/images/20210920/20141336JsOBStPg0W.png](https://ithelp.ithome.com.tw/upload/images/20210920/20141336JsOBStPg0W.png)

  - ##### peek: 查看最開頭資料

    - ###### 若資料在stack1，取得最前面的資料

    - ###### 若資料在stack2，取得最後面的資料

    - ![https://ithelp.ithome.com.tw/upload/images/20210920/20141336GmtRSBuGza.png](https://ithelp.ithome.com.tw/upload/images/20210920/20141336GmtRSBuGza.png)
  
  - ```C#
    public class Queue
    {
        public int[] s1 = new int[100];
        public int[] s2 = new int[100];
        public int index = 0;
    
        public void EnQueue(int x)
        {
            if (index == 0)
                s1[0] = x;
            else
            {
                var tmpIndex = 0;
    
                for (var i = index - 1; i >= 0; i--)
                    s2[tmpIndex++] = s1[i];
    
                tmpIndex = 0;
                s1[tmpIndex++] = x;
    
                for (var i = index - 1; i >= 0; i--)
                    s1[tmpIndex++] = s2[i];
            }
    
            index++;
        }
    
        // Dequeue an item from the queue
        public int DeQueue()
        {
            if (index == 0)
            {
                Console.WriteLine("Q is Empty");
            }
    
            var result = s1[index-1];
            s1[index - 1] = 0;
            index--;
            return result;
        }
    }
    ```

## Hash

- ###### 電腦科學中一種對資料的處理方法，通過某種特定的函式/演算法（稱為雜湊函式/演算法）將要檢索的項與用來檢索的索引（稱為雜湊，或者雜湊值）關聯起來，生成一種便於搜尋的資料結構

- ###### 雜湊演算法也被用來加密存在資料庫中的密碼（password）字串，由於雜湊演算法所計算出來的雜湊值（Hash Value）具有不可逆（無法逆向演算回原本的數值）的性質，因此可有效的保護密碼。

- ###### 一種資料儲存與擷取之技術，當要存取 Data X 之前，必須先經過 Hashing Function 計算求出 Hashing Address，再到 Hash Table 中對應的 Bucket 中存取 Data X，而 Hash Table 結構是由 B 個 buckets 組成，每個 bucket 有 S 個 Slots，每個 Slot 可存一筆 Data

- ##### Hashing 優點

  1. ###### 搜尋 Data 之前，無需事先排序過

  2. ###### 再沒有 Collision 情況下，Search X 之 time 為: O (1)，與資料量 n 無關

  3. ###### 保密 / 安全性高，在不知道 Hashing Function 下，很難取得 Data

  4. ###### 可作為 Data 壓縮之用途

     - ###### 例如：Unix 之 password 採用一對一，不可逆之 Hashing

- ##### Collision

  - ###### 不同的 Data，例如 (x,y)，經過 Hashing function 計算後得出相同的 Hashing Address 稱之，也就是 H (x) = H (y)

- #####  Overflow 

  - ###### Collision 發生後，且對應的 Bucket 已滿，則稱為 Overflow

  - ##### 處理方法

    - ##### Linear Probing (線性探測)

      - ###### 當兩筆資 x 與 y，代入雜湊函式 H(x) 與 H(y) 之後，若得到相同的雜湊值，則會發生溢位，此時可以將雜湊值依序 + 1，一格一格往後尋找有沒有其它空位，直到找到空位，或是儲存空間皆存滿為止

      - ![https://ithelp.ithome.com.tw/upload/images/20200930/20129841owOclTmA1g.png](https://ithelp.ithome.com.tw/upload/images/20200930/20129841owOclTmA1g.png)

      - 優點：

        1. 簡單、易於實施
        2. 保證 Table 空間可以充分利用

      - ##### 缺點：

        ##### 容易發生 Primary Clustering 現象，造成 Search/Insert/Delete X 等時間大幅增加之問題

        ##### Primary Clustering ：具有相同 Hashing Address 之 Data 容易占用相鄰的 Buckets 存放，形成群聚現象

    - #####  Quadratic Probing (二次方探測)

      - ###### 當 H (x) 發生 overflow 時，則探測![https://chart.googleapis.com/chart?cht=tx&chl=(%20H(x)%20%5Cpm%20i%5E2%20)%20%25%20b](https://chart.googleapis.com/chart?cht=tx&chl=(%20H(x)%20%5Cpm%20i%5E2%20)%20%25%20b) 

      - ###### B 為 Bucket 數目，i 是逐一遞增 i = 1,2,3,4,....，套入公式後，即可找到新的儲存位置，若還是發生溢位，則 i 繼續加 1 ，並代入公式

      - ![https://ithelp.ithome.com.tw/upload/images/20200930/20129841w9Oce294uH.png](https://ithelp.ithome.com.tw/upload/images/20200930/20129841w9Oce294uH.png)

    - **再雜湊 (Rehashing)**

      - ###### 準備多個雜湊函式，當第一種雜湊函式遇到溢位時，就換第二種雜湊函式，如果第二種雜湊函式遇到溢位時，就算第三種，直到沒有發生溢位為止

    - ##### Chaining or Link List (鏈結串列)

      - ###### 具有相同的 Hashing Address 的 Data 均置入同一個 Bucket 去，而 Bucket 內之 Data 彼此透過 Link List 結構串連在一起，而這種情況就作 Closed Address Mode

      - ![https://ithelp.ithome.com.tw/upload/images/20200930/20129841JnoYoG0rLp.png](https://ithelp.ithome.com.tw/upload/images/20200930/20129841JnoYoG0rLp.png)

### **雜湊函數 (Hash function)**

- ##### 將不定長度訊息的輸入，演算成固定長度雜湊值的輸出，且所計算出來的雜湊值必須符合兩個主要條件：

  - ###### 由雜湊值是無法反推出原來的訊息

  - ###### 雜湊值必須隨明文改變而改變。

- ##### 好的 Hashing Function 設計，要滿足下列三個 criteria：

  - ###### 計算宜簡單，不要過度複雜

  - ###### Collision 要少

  - ###### 不要造成 Hash Table 局部儲存之情況，要夠均勻才好

- ##### 常見的 Hashing Function 設計

  - ##### Middle Square

    - ###### 將鍵值平方後，取中間適當位數作為 Hashing Address 

      - 例如：鍵值 = 8125，平方後， = 66015625 取中間三個位數，156 作為 Hashing Address

  - ##### Mod (or Divide) 取餘數

    - ###### H(X) = X % M

  - ##### Folding Addition

    - ##### 將鍵值切成幾個相同長度之片段 (P1~Pn)，再將這些片段加總，即得 Hash Address。而相加的方法有兩種：

      - ###### shift (位移)

      - ###### bounding (邊界)

    - ![https://ithelp.ithome.com.tw/upload/images/20200930/20129841QkZJaaipBw.png](https://ithelp.ithome.com.tw/upload/images/20200930/20129841QkZJaaipBw.png)

    - ```C#
      例如：x = 12320324111220
      
      切成這樣：
      P1 = 123
      P2 = 203
      P3 = 241
      P4 = 112
      P5 = 020
          
      shift 作法
      將 P1~Pn 相加 = 699
      
      bounding 作法
      偶數的片段反向，所以：
      P2 = 302
      P4 = 211
      再加總 P1~Pn = 897
      ```

  - ##### **數位分析法 (Digits Analysis)**

    - ###### 如果已知資料的相似度很高，則可將重覆的資料捨去，例如手機號碼，都是 09 開頭，則 09 的部分就不需要參與計算，可挑選某一段具特殊意義的數字來做運算

### **雜湊表 (Hash table)**

- ###### 用雜湊函數運算出來的雜湊值，根據 **鍵 (key)** 來儲存在數據結構中

- ex

  - ```C#
    //舉例來說，我們有一筆資料用字典的形式表示，每個名字都搭配性別：
    {Joe:'M', Sue:'F', Dan:'M', Nell:'F', Ally:'F', Bob:'M'}
    
    //將每個名字經過雜湊函數的運算。
    (Key)                 (hash value)     (stored index)
    Joe  → (Hash function) →   4928   mod 5   =   3
    Sue  → (Hash function) →   7291   mod 5   =   1
    Dan  → (Hash function) →   1539   mod 5   =   4
    Nell → (Hash function) →   6276   mod 5   =   1
    Ally → (Hash function) →   9143   mod 5   =   3
    Bob  → (Hash function) →   5278   mod 5   =   3
    hash value 是獨一無二的，用 mod 5 來得到餘數並儲存才記憶體中。
    0： 
    1： [ Sue, F ] → [ Nell, F ]
    2： 
    3： 
    4： [ Joe, M ] → [ Ally, F ] → [ Bob, M ]
    5： [ Dan, M ]
    ```

- 



## Sort

###  Bubble Sort

- ###### 又稱`交換`排序法，顧名思故就是與`相鄰`的資料`兩兩比較`，如果數字較大者在前面，則需交換位置到後面，這樣一來可確保，數字最大者會排在最後面

- ![https://ithelp.ithome.com.tw/upload/images/20201003/20129841rCEYFJhmaM.png](https://ithelp.ithome.com.tw/upload/images/20201003/20129841rCEYFJhmaM.png)

- ### 時間複雜度

  - 最壞的情況
    資料是反序排序，如上述例子 ![https://chart.googleapis.com/chart?cht=tx&chl=O(n%5E2)](https://chart.googleapis.com/chart?cht=tx&chl=O(n%5E2))最好的情況
    資料原先就已經排列好，則只需做 n-1 次的比較，發現沒有交換情況，時間為 O(n)

### Selection Sort

- ###### 給定陣列N，從元素0開始每次與最小值互換位置

- ![https://ithelp.ithome.com.tw/upload/images/20201003/20129841CFBMlzn4Wc.png](https://ithelp.ithome.com.tw/upload/images/20201003/20129841CFBMlzn4Wc.png)

- ###### 複雜度: ![https://chart.googleapis.com/chart?cht=tx&chl=O(n%5E2)](https://chart.googleapis.com/chart?cht=tx&chl=O(n%5E2))

### Insertion sort

- 給定未排序陣列{ ![https://chart.googleapis.com/chart?cht=tx&chl=a_1%2Ca_2%2C...%2Ca_n](https://chart.googleapis.com/chart?cht=tx&chl=a_1%2Ca_2%2C...%2Ca_n) },每次都跟前面的陣列進行比較

  - 若現有值比前面陣列小，則將前面陣列往後移動

  - 一直到出現第一個比現有值小的元素，則現有值插入再該元素+1位置

  - ![img](https://www.runoob.com/wp-content/uploads/2019/03/insertionSort.gif)

  - ```c#
    public static void InsertSort(int[] array)
    {
        for(int i = 1;i < array.length;i++)
        {
            int temp = array[i];
            for(int j = i - 1;j >= 0;j--)
            {
                if(array[j] > temp)
                {
                    array[j + 1] = array[j];
                    array[j] = temp;
                }
                else
                    break;
            }
        }
    }
    ```

  - #### 複雜度

    | 項目           | 值    | 備註                       |
    | -------------- | ----- | -------------------------- |
    | 最差時間複雜度 | O(n2) | 排序已經反向排序過的序列。 |
    | 最佳時間複雜度 | O(n)  | 排序已經正向排序過的序列。 |
    | 平均時間複雜度 | O(n2) |                            |
    | 最差空間複雜度 | O(1)  |                            |

### 分治法(Divide-and-Conquer)

有許多演算法使用了遞迴的結構進行設計 : 為了解決一個問題，將問題拆分後經過多次遞迴解決問題的子問題，最終合併結果並解決問題。這種演算法就是分治法的想法。分治法在每一層的遞迴會有三個步驟:

- 分解(Divide) : 將原問題分解成許多個子問題，每一個子問題都是原問題規模較小的實例。
- 解決(Conquer) : 遞迴的方式去求解各子問題，當子問題的規模夠小時，則直接求解。
- 合併(Combine) : 將子問題的解合併成原問題的解。

### Quick Sort

- ###### `快速排序法`採用分而治之法(Divide-and-conquer)，把`大問題切割成小問題`，會於資料中找到一個`基準值(Pivot)`，並將資料逐一與這個值相比較，最後可以得到兩個部分，分別為`大於這個值`與`小於這個值`的數值，大於基準值的數值放在`右邊`，小於基準值的數值放`左邊`，`基準值`就很像是我們使用的`篩子`，篩完之後物品就會被`區分`成兩塊

- ###### 先找一個基準點，然後派兩個代理人分別從資料的兩邊開始往中間找，如果右邊找到一個值比基準點小，左邊找到一個值比基準點大，就讓他們互換。反覆找並互換，直到兩個人相遇。然後再將相遇的點跟基準點互換。第一輪結束。

- ##### 操作方式

  1. ###### 挑選基準值(Pivot)

     - ###### 取亂數

     - ###### 頭數、尾數、中間數排序後取中間值

       ###### 例如：1,5,7,9,8,6,3，第一個數為 1，最後一個數為 3 ，中間數為 9，由小至大排序後得到 1, 3, 9 可以取 3 做為基準值

     - ###### 取數值的最左邊或做右邊

  2. ###### 數值的比較

     - ###### 有一陣列稱為 array，數值共有 n 個，例如：`6, 4, 9, 3, 5, 0, 7, 2, 8, 1`，數值共有 10 個，需做兩件事情來與基準值做比較，才能將`大於`與`小於`基準時的數值分類出來，假設基準值是 `6`

     - 由左往右找出第一個大於基準值的索引位置 = i

     - 由右往左找出第一個小於基準值的索引位置 = j

  3. ###### 交換位置

     - ###### 若i < j代表此輪排序還沒完成，將i元素與j元素互換

     - ###### 若i >= j 代表此輪排序已經完成，將基準值與j元素互換

  4. ###### 各別處理左區與右區

     - ###### 再針對左區或右區，在重新挑選基準值，例如左區有 2, 4, 1, 3, 5，可在此區重新挑基準值，例如以 2 為基準值，再重覆 1 ~ 3 步驟即可(遞迴)，等到最後切到只剩單一元素時，即可完成所有數值的排序


  - ```C#
    //array[left] 做基準
    static void QuickSort1(int[] array, int left, int right)
    {
        if (left < right)
        {
            int i = left;
            int j = right + 1;
            while (true)
            {
                while (i + 1 < array.Length && array[++i] < array[left]) ;
                while (j - 1 > -1 && array[--j] > array[left]) ;
                if (i >= j)
                    break;
                Swap(array, i, j);
            }
            Swap(array, left, j);
            QuickSort1(array, left, j - 1);
            QuickSort1(array, j + 1, right);
        }
    }
    ```

  - 最壞的情況
    ![https://chart.googleapis.com/chart?cht=tx&chl=O(n%5E2)](https://chart.googleapis.com/chart?cht=tx&chl=O(n%5E2))，每次挑基準值的時候，都剛好挑到最大值或最小值

  - 最好的情況
    O(n log n)，挑選到好的基準值，可將資料切為一半，再根據其中一半再切一半，再分別去處理小的子區塊，切 log n 次，所以會花O(log n)時間

### 合併排序法(merge sort)

- ##### 合併排序法(merge sort)就是一種由分治法實現的演算法，直觀上也是符合分治法的三個步驟

  - ##### 分解(Divide) : 分解待排序長度為![https://chart.googleapis.com/chart?cht=tx&chl=n](https://chart.googleapis.com/chart?cht=tx&chl=n)的陣列，變成長度為![https://chart.googleapis.com/chart?cht=tx&chl=n%2F2](https://chart.googleapis.com/chart?cht=tx&chl=n%2F2)長度的兩個子陣列。

  - ##### 解決(Conquer) : 使用合併排序法遞迴的排序兩個子陣列。

  - ##### 合併(Combine) : 合併兩個已經排序的子陣列產生出已經完成排序的原陣列。

- ![img](https://www.runoob.com/wp-content/uploads/2019/03/mergeSort.gif)

- ```C#
  public static List<int> sort(List<int> lst) {
      if (lst.Count <= 1)
          return lst;
      int mid = lst.Count / 2;
      List<int> left = new List<int>();  // 定义左侧List
      List<int> right = new List<int>(); // 定义右侧List
      // 以下兩個循環把 lst 分為左右兩個 List
      for (int i = 0; i < mid; i++)
          left.Add(lst[i]);
      for (int j = mid; j < lst.Count; j++)
          right.Add(lst[j]);
      left = sort(left);
      right = sort(right);
      return merge(left, right);
  }
  /// <summary>
  /// 合併兩個已經排好序的List
  /// </summary>
  /// <param name="left">左側List</param>
  /// <param name="right">右側List</param>
  /// <returns></returns>
  static List<int> merge(List<int> left, List<int> right) {
      List<int> temp = new List<int>();
      while (left.Count > 0 && right.Count > 0) {
          if (left[0] <= right[0]) {
              temp.Add(left[0]);
              left.RemoveAt(0);
          } else {
              temp.Add(right[0]);
              right.RemoveAt(0);
          }
      }
      if (left.Count > 0) {
          for (int i = 0; i < left.Count; i++)
              temp.Add(left[i]);
      }
      if (right.Count > 0) {
          for (int i = 0; i < right.Count; i++)
              temp.Add(right[i]);
      }
      return temp;
  }
  ```

- #### 合併排序法的複雜度

  | 項目           | 值       | 備註 |
  | -------------- | -------- | ---- |
  | 最差時間複雜度 | O(nlog⁡n) |      |
  | 最佳時間複雜度 | O(nlog⁡n) |      |
  | 平均時間複雜度 | O(nlog⁡n) |      |
  | 最差空間複雜度 | O(n)     |      |
  | 是否穩定       | 是       |      |

### 堆積排序法 Heap Sort

- ![heap-sort](https://img.magiclen.org/albums/heap-sort/animation-361x351.gif)



## 網路協定

### TCP

1. ###### TCP 為每個封包分配一個唯一的識別碼和一個序號，這些號碼能讓接收端識別封包的完整性，以及封包的順序

2. ###### 當接收端收到封包後，如果順序正確，會向發送端傳送一個確認信號（Acknowledgement），以此確認接收端已經收到封包。

3. ###### 發送端傳送另一個封包

4. ###### 如果封包遺失或發送順序錯誤，接收端會保持沈默，不發送確認信號。這表示發送端需要重新傳送封包。

   ![TCP 如何運作](https://nordvpn.com/wp-content/uploads/2020/07/TCP_vs_UDP_01_ZH-TW.gif)

- ##### 因為資料是按照順序發送的，有助於流量控制和解決資料壅塞的問題，並容易發現和修復錯誤。因此，經由 TCP 發送的資料能完全到達目的地。即使網路阻塞，傳輸的資料也不會出問題。

- ##### 不過 TCP 也有缺點，發送端和接收端之間有很多往來的通訊，因此建立連線和交換資料需要更多的時間。

### UDP 

- ##### UDP 不需要唯一識別碼和序號就能完成相同的工作。以串流方式傳送資料，發送端不會等待接收端的確認信號，會繼續不斷發送封包資料。

- ##### UDP 幾乎沒有錯誤修正功能，也不在乎封包遺失，因此很容易出錯，但傳輸速度比 TCP 更快。

- ##### 串流媒體、VoIP 語音、網路遊戲等服務經常使用 UDP 協定，這網路應用不太需要可靠性機制，封包遺失不會導致服務中斷。

  ![UDP 如何運作](https://nordvpn.com/wp-content/uploads/2020/07/TCP_vs_UDP_02_ZH-TW.gif)

- ##### TCP 和 UDP 的比較如下：

|                | TCP                                                  | UDP                                        |
| :------------- | :--------------------------------------------------- | :----------------------------------------- |
| 可靠性         | 可靠                                                 | 不可靠                                     |
| 速度           | 慢                                                   | 快                                         |
| 傳輸方式       | 封包按順序傳輸                                       | 封包以串流方式傳輸                         |
| 錯誤檢查與修正 | 有                                                   | 無                                         |
| 壅塞控制       | 有                                                   | 無                                         |
| 確認           | 有                                                   | 只有檢查碼                                 |
| 適用服務       | 要求可靠傳輸的服務，例如電子郵件、網頁瀏覽、檔案傳輸 | 即時服務，例如串流媒體、網路電話、網路遊戲 |

### HTTP 

- ###### HTTP 全名是 [超文本傳輸協定（HyperText Transfer Protocol）](https://zh.wikipedia.org/wiki/超文本传输协议)，內容只規範了客戶端請求與伺服器回應的標準，實際上是藉由 [TCP](https://zh.wikipedia.org/wiki/传输控制协议) 作為資料的傳輸方式。

- ###### 最早被設計用來「傳送」及「接收」HTML的頁面及內容。HTTP在用戶端(使用者)與伺服器端(網站)之間透過TCP協定來傳遞「請求」與「應答」的要求。

- ###### HTTP協定不使用加密協定，其中原因包含：加密會多消耗許多運算資源，也會佔用更多的傳輸頻寬，而緩存機制跟著會失效。

- ###### 當使用者端以HTTP發起一個請求後，會建立起一個使用者端至伺服器指定連接埠80端的一個TCP連線，而伺服器在收到使用者端的請求後，則會向使用者端返回一個狀態碼，例如"HTTP/1.1 200 OK”

- ##### 問題

  - ###### 明文的方式傳遞訊息，沒有任何的保護措施，因此只要有心人對於傳遞的訊息進行攔截，我們的資料就會被竊取

  - ###### 訊息可能在途中被竄改內容，因此使用者端就很容易暴露在網路的高風險環境下

### HTTPS

- ###### HTTPS 全名 [超文本傳輸安全協定](https://zh.wikipedia.org/wiki/超文本传输安全协议)，那個 S 就是 Secure 的意思；HTTPS 透過 HTTP 進行通訊，但通訊過程使用 [SSL/TLS](https://zh.wikipedia.org/wiki/傳輸層安全性協定) 進行加密，藉由類似於前述的加密方式，在 HTTP 之上定義了相對安全的資料傳輸方法。

- ##### HTTPS 使用加密協議來加密通信。該協議稱為傳輸層安全性 (TLS)，以前叫做 SSL。該協議通過使用所謂的[公鑰基礎設施](https://zh.wikipedia.org/wiki/公開金鑰基礎建設)來保護通信

  - ###### 私鑰: 由網站的所有者控制，它是私有的，這個私鑰位於 Web 伺服器上，用於解密由公鑰加密的信息

  - ###### 公鑰: 每個想要以安全方式與服務器交互的人都可以使用這個公鑰 。用公鑰加密的信息只能用私鑰解密

- ###### 加密：SSL/TLS現在是使用「非對稱式加密」的方式來進行資訊加密的動作，在資料的安全上也比較有保障。

- ###### **驗證：**透過公開加密金鑰的方式(也稱作非對稱式的加密)，讓每個通訊者同時擁有「公鑰」及「私鑰」來解決加密上不安全的問題，不過「公鑰」與「私鑰」擁有者如何證明加密的密文就是正確的，而非被竊取後竄改的呢?此時就須要有公正的第三方機構來頒發數位憑證，以證明配對的「公鑰」與「私鑰」雙方是正確的。

- ###### 完整性：SSL/TLS將身分資訊（比如網路主機名，組織的名稱或個體名稱等）和簽章資訊等內容以數字形式的文件格式做成加密的「公鑰」與「私鑰」密鑰對。任何擁有公鑰的人都可以使用它發送只有私鑰擁有者才能解密的訊息，及確認這個傳遞的訊息是該公鑰對應的私鑰所進行簽章。也因為資料在伺服器端與使用者端傳輸時，兩端的金鑰可以以該組數位簽章確認彼此的身分與內容的完整性，所以我們才能確保在網路上彼此傳輸的資料是安全的。

#### **SSL**

- ###### Secure Sockets Layer，目的除了維持網路連線的安全性外，也是防止兩台電腦間互相傳送敏感資訊和個人資料。

- ###### 確保使用者對網站、伺服器對伺服器間傳送資料時能夠安全無虞，也可以將傳送的資料加密，讓駭客無法竊取，相對來說也是對使用者的一種個資安全保障。

- ##### 如何運作

  - ###### 為了提供高度[隱私](https://www.cloudflare.com/learning/privacy/what-is-data-privacy/)，SSL 會加密在網頁各處傳輸的資料。這表示任何嘗試攔截此資料的人只會看見幾乎無法解密的模糊字元混合。

  - ###### SSL 會在兩個通訊裝置之間啟動稱為[交握](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/)的**身分驗證**流程，以確保兩個裝置確實都符合自行宣稱的身分。

  - ###### SSL 也會數位簽署資料，以便提供**資料整合性**，驗證資料在抵達預定的收件者之前沒有遭到竄改。

- ##### SSL 憑證包含下列資訊：

  - ###### 憑證擁有者的名稱

  - ###### 憑證的序號及到期日期

  - ###### 憑證擁有者公開金鑰的副本

  - ###### 憑證簽發有關單位的電子簽名

### HTTP/2

- ##### **主要目的是透過一些措施改善瀏覽器瀏覽網頁加載的速度(page load)**

- ##### http/2與 http/1.1有著高度的相容信，舉凡request method, http status code, url, header 等等，因此只需要確保你的網站有支援https(因為瀏覽器只支援https 在http/2上)

- ##### 與HTTP差異

  - ###### HTTP/2 協定建置在 HTTPS 安全連線之上，因此在更新 HTTP/2之前，網站必須要擁有 TLS/SSL 安全性憑證來保障安全連線。

  - ###### 在減少多個連線工作次數的情況下，瀏覽器只需單一網路連線就可以與網站伺服器進行連接。

  - ###### 由單一網路連線時達成同時傳輸多個 HTTP Request 和 Response，並擴充增加可以同時請求發送 CSS/JS/Images 等等資源。

  - ###### 優先權設計(Prioritization)，伺服器可以決定例如 CSS 或 JavaScript 檔案，哪些要優先傳送。

  - ###### Header 壓縮，HTTP/2 處理了絕大部份重複的 Headers ，並在傳送前進型壓縮，有效減少了過多重複的資訊也縮短了冗長得傳輸過程。

  - ###### HTTP/2 使用單一Binary 二進位的封包結構設計，對伺服器和瀏覽器來說，可以更快的解析傳輸資料。

  - ###### 伺服器主動推送資源(Server Push)，允許伺服器除了 HTML 之外，連同需要的 CSS/JavaScript/Images 檔案，主動推到瀏覽器的快取之中。

#### Request and response multiplexing (多路複用)

- ###### 在http/1.1中，client端時常會同時發起多個request至server拿取檔案(像是js, css, image等)，以此方式達到快速載入頁面。如下圖在http/1.1中會同時與server建立3個TCP connection，但是瀏覽器通常會限制TCP connection同時建立的數目。因此在http/2協定中，允許client端與同一server建立單一TCP connection並以非同步方式傳輸要的檔案。

- ![img](https://miro.medium.com/max/1050/1*xRIvJ0b_aDgcud2j2Je8JQ.png)

#### Header compression (標頭壓縮)

- ##### 每一個http的傳輸中都會攜帶一組header，在http/1.1中，header會是以明文(plain text)傳輸大小通常會是500-800 bytes，若有攜帶cookie也有可能會更大。因此在http/2中，會將request以及response的header使用HPACK演算法壓縮header的內容，此方法壓縮後可以減少85%-88%的大小

#### Server push (伺服器推送)

- ##### 在http/1.1中，通常client端request甚麼server就會回傳甚麼，例如: 當client request html那麼server將只會回傳html。但在http/2中，允許server主動推送有相關的資料給client，例如: 當client只request html，但是server知道client request此html後續也會request css, js等，因此server就會在client沒有request的情況下主動推送css, js檔給client。那server怎麼知道這些檔案是有相關性的呢? web developer將需要server push的檔案加上特定的描述即可

- ![img](https://miro.medium.com/max/900/1*FSELCfdKHCdoqr5Ejd5MYQ.png)

#### Binary framing layer

- ##### 在http/2中，header與body所挾帶的property與http/1.1相同(ex. verbs, methods)，然而兩者在傳輸上會有不同。在http/2中，會將header以及body編碼成二進制在server 與client端中傳輸，在http/1.1中，則是以明文的方式傳輸。將訊息編碼成二進制進行傳輸，此特性是http/2 的其他特性的根本基礎。

- ![img](https://miro.medium.com/max/1050/1*rFE7nzYYZuUsLss7j6Xguw.png)

### WebSocket 

- ##### `WebSocket` 協定只需透過一次連結便能保持連線，不必再透過一直發送 `Request` 來與 Server 互動

- ##### WebSocket是瀏覽器與伺服器交換資料的方式之一，與HTTP最大的不同是，他是一個持續的雙向的連線，所以沒有重新連線，重新傳送檔頭等多餘的負荷，反應更即時。

- ##### Wiki：「WebSocket是一種網路傳輸協定，可在單個TCP連接上進行全雙工通訊，位於OSI模型的應用層。」

- ![img](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051502.png)

- ##### WebSocket可以選擇ws或是wss通訊協定，ws就相當於一般的http，wss則相當於https，不過需要伺服器可以支援。

- ##### 由於時持續的連線，所以在連線中URI是不會改的，所以每次也只能選擇一個URI，想跟不同URI連線，就需要建立新的WebSocket連線。

  

# Webhook

- ##### 簡單來說就是一種反向API機制，類似於觸發器的一樣。

- ##### 說到底就是一種「事件驅動」的設計，而什麼是「事件驅動」呢？

  - ##### 舉例： 「當(發生什麼事件)時(請主動通知我)」，那被通知的那一方要做什麼事情就跟主動通知方無關了，藉由這樣的設計減少核心系統的複雜程度。

  - ![img](https://s3-ap-northeast-1.amazonaws.com/upload.potatomedia.co/articles/potato_609ab82b-3b04-4776-bb15-36f0ab291d19_8b2f84b9c537127301deb4ff72ecf61c62c63278.png)

  - ![img](https://s3-ap-northeast-1.amazonaws.com/upload.potatomedia.co/articles/potato_609ab82b-3b04-4776-bb15-36f0ab291d19_46387e0c560da578ecf33e6514e286af629ff331.png)

- ##### 消費一個webhook是爲webhook準備一個URL，用於webhook發送請求。

- ##### 多數webhook以兩種數據格式發布數據：JSON或者XML，這需要解釋。另一種數據格式是application/x-www-form-urlencoded or multipart/form-data。




# 設計模式

## SOLID

- ###### 單一職責原則(Single responsibility principle, SRP)

  - ###### 把工作交給負責該職責的類別去做，自己只需要關注在自己正在處理的職責即可。

  - ###### 例如訂單管理，會涉及到訂單處理及會員資料

    - ###### 若全部寫在同一個類別，當訂單處理的商業邏輯、查詢會員資料的邏輯或是通知會員的方式有變更的時候，這個函式都會受到影響，也就是說這個函式同時對多個不同對象負責。這樣的類別或函式就是不穩定的。

    - ###### 解決方法: 會員的處理一律封裝回會員管理類別，我們再藉由會員管理類別去調用其方法取回資料；寄送信件也封裝到通知管理類別，不用去管用什麼方法通知的，我們只需要去要求其通知即可。

  - ###### 當我們並未遵守單一職責原則時，同個類別裡面充斥著不同工作的處理邏輯。也就是不健康的內聚：完全不夠聚，就只是盤散沙。

    - 容易產生重複的邏輯處理，增加維護困難
    - 同時我們在修改時也無法界定邊界，無法確定這次修改影響到的範圍，我們並不知道這些放在一起的東西，或是同一段做的所有事之間是否會相互影響
    - 為了要確保修改沒有問題，我們必須大量閱讀不相關的程式碼，無形中造成開發負擔，降低開發效率。

- ###### 開放封閉原則(Open-Close principle, OCP)

  - ###### 對擴展開放，而對修改封閉

  - ###### 面對需求，對程式碼的改動是透過增加新程式碼進行的，而不是更改現有的程式碼

  - ###### 用擴充的方式去完成變化，而不是用針對內部進行修改的方式來做；希望藉由良好的設計，能迴避上面那串修改困難導致成本高昂的問題。

  - ###### 當需求有異動時，藉由**繼承**、**相依性注入**等方式，增加新的程式碼，以實作新的需求。

- ##### 里氏替換原則(Liskov substitution principle, LSP)

  - ###### 當實作繼承了 interface 或 base-class的 sub-class，那麼在程式中，只要出現該 interface 或 base-class 的部份，都可以用 sub-class 替換。

    - ###### 子類別替換父類別後，不需要改變，也不會發生任何錯誤或異常。父類別能做到的事情，子類別也要能做到

  - 簡單說明，繼承後的子類別應該要能達到父類別原先的功能以符合呼叫該涵式人的期望

    - ###### 我們預期了這個函式或類別需要準備的輸入參數，也預期了應該要有的輸出結果。如果某一天替換了子類別，卻不是這麼一回事，就會發生很多意料外的錯誤。

- ##### 介面隔離原則(Interface segregation principle, ISP)

  - ###### 針對不同需求的用戶，開放其對應需求的介面，提拱使用。可避免不相關的需求介面異動，造成被強迫一同面對異動的情況。

  - ###### 當介面規定了太多要求，而我們實作的子類別只需要其中一部份，或是有些要求根本無法達成，就會發生這個困境：放棄實作介面，或是用空實作和錯誤處理去欺騙介面。

  - ###### 最小化類別與類別之間的介面

- #####  依賴反轉原則(Dependency inversion principle, DIP)

  - 高階模組不應該依賴於低階模組。

    - ###### 兩者都應該依賴抽象，不應該直接去依賴，而是必須藉由抽象來隔開。不應該直接去受到實作的影響，而是只要關注在所需要的功能。

    - ###### 如此一來，依賴就「反轉」了。原本是 `高階模組 → 低階模組` 的關係，變成了 `高階模組 → 介面 ← 低階模組`。並不是高階去依賴低階，而是低階去依賴高階要求的功能。

  - ###### 抽象不應該依賴細節；細節應該依賴抽象。

  - 

## 簡單工廠模式(Simple Factory Pattern)

### 定義

- 透過建立一個類別來負責創建實體，這些實體都繼承於同一個父類別
- Product: 抽象的父類別(披薩)
- Factory: 負責建立實體邏輯的類別
- ConcreteProduct: 實體本身(海鮮、夏威夷)

### 實例

- 假設我們是一間我們是一間披薩店提供各種口味的披薩。

- 披薩製作的程序包含AddMaterial(加料)、Bake(烘烤)，當客人要一個海鮮披薩時如下

  ```cs
  public SeaPizza MakePizza()
  {
      SeaPizza pizza= new SeaPizza();
      pizza.AddMaterial(); 
      pizza.Bake(); 
      return pizza;
  }
  ```

   

- 當今天可人要改點夏威夷披薩時

  ```cs
  public HawaiiPizza MakePizza()
  {
      HawaiiPizza pizza= new SeaPizza();
      pizza.AddMaterial(); 
      pizza.Bake(); 
      return pizza;
  }
  ```

   

- 我們可以發現，當今天我們研發出新的口味時我們就必須建立新的類別。可是每個類別執行的動作其實都是一樣的(AddMaterial、Bake)，這時候我們可以將這些行為封裝起來成為一個抽象的Product

  ```cs
  public interface IPizza
  {
      void AddMaterial();
      void Bake();
  }
  ```

   

- 接著，我們可以讓每種口味的披薩繼承IPizza介面並根據每個披薩的種類時做內容

  ```cs
  public class SeaPizza: IPizza
  {
    public void AddMaterial()
    {
     Console.WriteLine("加入海鮮佐料");
    }
  
    public void Bake()
    {
     Console.WriteLine("烘烤海鮮披薩");
    }
  }
  
  public class HawaiiPizza : IPizza
  {
    public void AddMaterial()
    {
     Console.WriteLine("加入火腿片、鳳梨");
    }
  
    public void Bake()
    {
     Console.WriteLine("烘烤夏威夷披薩");
    }
  }
  ```

   

- 最後我們建立一個SimpleFactory來決定何時該生產哪種口味的披薩。

  ```cs
  public class SimpleFactory
  {
      public IPizza CreatePizza(string name)
      {
          if (name== "Sea")
              return new SeaPizza();
  
          if (name== "Hawaii")
              return new HawaiiPizza();
  
          else
              return null;
      }
  }
  ```

   

- 另外，我們可以將整個披薩的製成包裝在一個類別裡

  ```cs
  public class PizzaStore
  {
      private SimpleFactory  _factory;
  
      public PizzaStore(SimpleFactory  pFactory)
      {
          _factory = pFactory;
      }
  
      public IPizza CreateProduct(string name)
      {
          IPizza pizza = _factory.CreatePizza(name);
  
          pizza .AddMaterial();
          pizza .Bake();
  
          return pizza ;
      }
  }
  ```

   

- 終於，我們可以依造客人的需求提供不同的Pizza了

  ```cs
  static void Main(string[] args)
  {
      PizzaStore store = new PizzaStore(new SimpleFactory());
  
      store.CreateProduct("Sea");
  
      store.CreateProduct("Hawaii");
  }
  ```

## 工廠方法模式(Factory Method Pattern)

- ##### 簡單工廠的延伸，不再提供統一的工廠類來建立所有物件，而是將Factory類別抽象化，針對**不同分類的物件有不同的工廠**。

- **優點：** 

  - ###### 將創建物件的邏輯與物件本身的邏輯分離。一個工廠只會對應一個類別，完全遵守了[開放封閉原則](https://ithelp.ithome.com.tw/articles/10233481)。

### 結構

- 抽象工廠角色(IFactory)：定義抽象的方法或介面，讓繼承的實體工廠進行實作
- 具體工廠角色：實作抽象工廠
- 抽象產品角色(IPizza)：定義產品抽象方法或介面
- 具體產品角色：實作抽象產品的方法或介面

### 建立抽象工廠

- 從上次簡單工廠的例子中我們可以看出，當我們需要增加新的口味時，工廠類別的判斷式就會不段增加。

  ```cs
  public class SimpleFactory
  {
      public IPizza CreatePizza(string name)
      {
          if (name== "Sea")
              return new SeaPizza();
  
          if (name== "Hawaii")
              return new HawaiiPizza();
  
          if (name== "Pork")
              return new PorkPizza();
  
          if ...
  
          else
              return null;
      }
  }
  ```

- 這樣的結果造成了工廠類別的複雜度，也導致我們的維護成本提升

- 因此，我們在一次抽象畫工廠類別

  ```cs
  public interface IFactory
  {
    Ipizza  CreatePizza();
  }
  ```

- 接著，我們只須要讓每種口味的披薩自己去時做各自工廠的邏輯

  ```cs
  class PorkPizzaFactory : IFactory
  {
      public Ipizza CreatePizza()
      {
          return new PorkPizza();        
      }
  }
  
  class SeaPizzaFactory : IFactory
  {
      public Ipizza CreatePizza()
      {
          return new SeaPizza(); 
      }
  }
  ```

- 當我們需要新的口味時，只要建立一個新的具體工廠腳色。這樣一來我們在維護上也變得更加輕鬆，程式的相依性也相對降低。

- 最後，我們在來改寫上次的PizzaStore

  ```cs
  public class PizzaStore
  {
      private IFactory _factory;
  
      public PizzaStore(IFactory pFactory)
      {
          _factory = pFactory;
      }
  
      public IPizza CreateOrder()
      {
          IPizza pizza = _factory.CreatePizza();
  
          pizza .AddMaterial();
          pizza .Bake();
  
          return pizza ;
      }
  }
  ```

  ```cs
  static void Main(string[] args)
  {
      PizzaStore seaStore = new PizzaStore(new seaPizzaFactory ());
      seaStore.CreateOrder();
  
      PizzaStore porkStore = new PizzaStore(new PorkPizzaFactory ());
      porkStore.CreateOrder();
  }
  ```



## 策略模式(Strategy Pattern)

- #### 定義`一系列的演算法`，並且把這些算法，`用介面封裝到有公共介面的策略類中`，使他們可以互相替換。

- ##### 策略模式主要是在封裝執行相同行為的類別，這些類別是針對相同的任務但本身的邏輯/內容可能不相同。

- ##### 將這些行為封裝起來便於抽換，以保持彼此間的獨立。

### 情境:

- ##### 假設我們今天要開發一款RPG遊戲，裡面包含戰士、盜賊、弓箭手。

- ##### 每個角色都包含攻擊及防禦兩個行為，這時候建立一個腳色的類別好像是不錯的主意。這樣每個腳色都能繼承類別中的行為。

  ```cs
  public abstract class Role
  {
      public override void Attack()
      {
          Console.WriteLine("進行攻擊");
      }
  
      public void Defence()
      {
          Console.WriteLine("進行防禦");
      }
  }
  
  public class Warrior: Role
  {
  }
  
  public class Assassin : Role
  {
  }
  ```

- ##### 今天我們新開了一個法師的腳色，而且法師的攻擊與戰士的攻擊甚至是盜賊、弓箭手之間的模式應該都是不一樣的，這時候，你可能會想用繼承類別行為的發法來改寫每一個腳色的動作

  ```cs
  public abstract class Role
  {
      public void Attack()
      {
      }
  
      public void Defence()
      {
      }
  
      public void Support()
      {
      }
  }
  
  public class Warrior: Role
  {
  
      public override void Attack()
      {
          Console.WriteLine("進行強力攻擊");
      }
  
      public void Defence()
      {
          Console.WriteLine("進行盾牌防禦");
      }
  }
  
  public class Assassin : Role
  {
  
      public void Attack()
      {
          Console.WriteLine("進行背刺");
      }
  
      public void Defence()
      {
          Console.WriteLine("進行潛行");
      }
  }
  
  public class Magician : Role
  {
  
      public void Attack()
      {
          Console.WriteLine("魔法攻擊");
      }
  
      public void Defence()
      {
          Console.WriteLine("展開分身");
      }
  }
  ```

- ##### 這當然是一種方法，可是當我們又增加 一個行為時又必須調整每一個腳色。

### 定義

- 將變動部分封裝起來
- 針對介面寫程式，不是針對實踐寫程式。
- 多用合成，少用繼承。

### 調整

- 將攻擊、防禦等行為封裝成介面

  ```cs
  public interface AttackBehavior{
      public void Exec();
  }
  
  public interface DefenseBehavior{
      public void Exec();
  }
  ```

- 每一種動作繼承改寫對應的行為

  ```cs
  public WeaponAttack : AttackBehavior
  {
   public void Exec()
   {
    Console.WriteLine("使用弓箭射擊");
   }
  }
  
  public NormalAttack : AttackBehavior
  {
   public void Exec()
   {
    Console.WriteLine("使用重拳攻擊");
   }
  }
  
  public MagicAttack : AttackBehavior
  {
   public void Exec()
   {
    Console.WriteLine("使用魔法攻擊");
   }
  }
  ```

- 最後，我們來調整腳色的類別

  ```cs
  public class Role
  {
      private AttackBehavior _attackBehavior;
      private DefenseBehavior _defenseBehavior ;
      
      public Role(AttackBehavior attackMode, DefenseBehavior defenseMode)
      {
       this._attackBehavior = attackMode;
       this._defenseBehavior = defenseMode;
      }
  
      public override void Attack()
      {
       this._attackBehavior.Exce();
      }
  
      public void Defense()
      {
       this._defenseBehavior .Exce();
      }
  }
  
  static void Main(string[] args)
  {
      Role Worrior = new Role(new NormalAttack(),new NormalDefense());
      Worrior.Attack();
      Worrior.Defense();
  
      Role Magican = new Role(new MagicAttack(),new MagicDefense());
      Magican.Attack();
      Magican.Defense();
  }
  ```

##  觀察者模式 (Observer Pattern)

- ###### 定義物件之間一種一對多的依賴關係，當一個物件狀態發生改變時，所有依賴於他的物件都將自動地得到通知且被更新。

### 前言

- 生活中，我們透過不同的『訂閱』機制，來取得最新資訊。例如，訂閱報紙、部落格、FB粉絲追蹤
- 這種訂閱後就能自動收到更新通知 的概念，即是 觀察者模式。
- 被訂閱、被追蹤、被觀察的，稱為 — 主題/目標 (Subject)
- 而對主題感興趣的則是 — 觀察者 (Observer) ，

### 以訂閱報紙為例

- 首先，我們要定義目標(Subject)類別，要求繼承類別的物件必須實作註冊、移除、發送通知等方法。

- 接著，我們定義一個Observer類別，要求每個繼承類別的物件必須實作update()方法

- ```cs
  public interface ISubject
  {
      void RegisterObserver(IObserver observer);
      void RemoveObserver(IObserver observer);
      void NotifyObservers(string msg);
  }
  
  public interface IObserver
  {
      void Update(string msg);
  }
  ```

   

- 接下來，我們開始實作報社類別並實作註冊、移除、發送通知等方法。

  ```cs
  public class NewspaperOffice : ISubject
  {
      List observerList; // 使用List來存放觀察者名單
  
      public NewspaperOffice()
      {
          observerList= new List();
      }
  
      // 加入觀察者
      public void RegisterObserver(IObserver observer)
      {
          observerList.Add(observer);
      }
  
      // 移除觀察者
      public void RemoveObserver(IObserver observer)
      {
          if (observerList.IndexOf(observer) >= 0)
              observerList.Remove(observer);
      }
  
      // 發送通知給在監聽名單中的觀察者
      public void NotifyObservers(string message)
      {
          foreach (IObserver observer in observerList)
          {
              observer.Update(message);
          }
      }
  
      // 訂閱報紙
      public void SubscribeNewspaper(IObserver customer)
      {
          RegisterObserver(customer);
      }
  
      // 取消訂閱報紙
      public void UnsubscribeNewspaper(IObserver customer)
      {
          RemoveObserver(customer);
      }
  
      // 發送新消息
      public void SendNewspaper(string message)
      {
          Console.WriteLine("Send News..");
          NotifyObservers(message);
      }
  }
  ```

   

- 實作Observer類別

  ```cs
  public class Customer : IObserver
  {
      public string Name { private get; set; }
  
      public Customer(string pName)
      {
          Name= pName;
      }
      
      // 更新最新消息
      public void Update(string message)
      {
          Console.WriteLine("   {0} receive a new message:{1}", Name , message);
      }
  }
  
  static void Main(string[] args)
  {
      // 產生一間報社
      NewspaperOffice office = new Newsp
  
      Customer AA = new Customer("AA");
      Customer BB = new Customer("BB");
  
      office.SubscribeNewspaper(AA);
      office.SubscribeNewspaper(BB);
  
      // 報社發送了第一則新聞
      office.SendNewspaper("News One.......");
  
      office.UnsubscribeNewspaper(AA);
  
      office.SendNewspaper("News Two.......");
  }
  ```

## 樣板方法模式(Template Method Pattern)

- ##### 把重複性的部分封裝至父類別，讓各類別不同的方法由子類別繼承實踐

### 定義

- ##### 將一個演算法的骨架定義在一個方法中，而演算法本身會用到的一些方法，則是定義在次類別中

- ##### 樣板方法讓次類別在不改變演算法架構的情況下，重新定義演算法中的某些步驟

- ##### 樣板方法定義了一個演算法的步驟，並允許次類別為一個或多個步驟，提供其實踐方式

- ##### 樣板就是一個方法；更具體地說，這個方法將演算法定義成一組步驟。其中的任何步驟都可以是抽象方法，而由次類別負責實踐這些抽象方法。這可以確保演算法的結構維持不變，同時由次類別提供部份實踐方式

- ##### 策略模式與樣版方法模式都是用來封裝演算法。策略模式是用合成；樣板方法模式是用繼承

### 實作

- 定義父類別及封裝共用方法

  ```cs
  abstract class DataAccessObject
  
    {
      protected string connectionString;
      protected DataSet dataSet;
   
      public virtual void Connect()
      {
        connectionString =
          "provider=Microsoft.JET.OLEDB.4.0; " +
          "data source=..\\..\\..\\db1.mdb";
      }
   
      public virtual void Disconnect()
      {
        connectionString = "";
      }
   
   
      public abstract void Select();
      public abstract void Process();
  
      public void Run()
      {
        Connect();
        Select();
        Process();
        Disconnect();
      }
    }
  ```

   

- 子類別繼承父類別，並改寫各自的內容

  ```cs
    class Categories : DataAccessObject
    {
      public override void Select()
      {
        string sql = "select CategoryName from Categories";
        OleDbDataAdapter dataAdapter = new OleDbDataAdapter(
          sql, connectionString);
   
        dataSet = new DataSet();
        dataAdapter.Fill(dataSet, "Categories");
      }
   
      public override void Process()
      {
        Console.WriteLine("Categories ---- ");
   
        DataTable dataTable = dataSet.Tables["Categories"];
        foreach (DataRow row in dataTable.Rows)
        {
          Console.WriteLine(row["CategoryName"]);
        }
        Console.WriteLine();
      }
    }
   
  
    class Products : DataAccessObject
    {
      public override void Select()
      {
        string sql = "select ProductName from Products";
        OleDbDataAdapter dataAdapter = new OleDbDataAdapter(
          sql, connectionString);
   
        dataSet = new DataSet();
        dataAdapter.Fill(dataSet, "Products");
      }
   
      public override void Process()
      {
        Console.WriteLine("Products ---- ");
        DataTable dataTable = dataSet.Tables["Products"];
        foreach (DataRow row in dataTable.Rows)
        {
          Console.WriteLine(row["ProductName"]);
        }
        Console.WriteLine();
      }
    }
  ```

   

# DDD

- ##### 以領域知識為核心建立的模型，領域專家與開發人員，可以透過這個**模型**進行交流。

- ##### 優點

  - ###### 專注在核心業務上

  - ###### 保護業務邏輯，不會因技術細節 (如 db 、框架、基礎設施)而影響。

  - ###### 邏輯復用方便

  - ###### 把所有的Domain Logic 模組化更便於進行個別測試

  - ###### 關注點分離更便於偵錯，邏輯都切成各別的核心Domain

  - ###### 利用切分成微服務，Core Domain已經切開了可以個別獨立

  - ###### 程式碼的意圖更加明確，職責分離

- ##### 缺點

  - ###### 架構複雜較難快速開發

  - ###### 沒有領域專家很難很好切分

  - ###### 學習成本高

  - ![socre board](https://i.imgur.com/RE3pZc1.png)


## Domain

- ##### 應用程式內業務邏輯及知識所涵蓋的範圍，也就是在真實世界( Real World )中的問題，需要被軟體解決的所有範圍。

- ##### Problem Domain

  - ###### 現實世界的 Domain

- ##### Solution Domain

  - ###### 軟體世界的 Domain

- ![img](https://miro.medium.com/v2/resize:fit:700/1*SOKB1vR_Co_AprQW0vXTUA.png)

- ##### Sub Domain

  - ###### Core (sub) Domain — 涵蓋最重要的業務邏輯。ex. EC 中的購物車系統

  - ###### Generic (sub) Domain — 可能廣泛地被使用。 ex. 會員

  - ###### Supporting (Sub) Domain — 能使整體 Domain 帶來幫助，但不是主要的。 ex. 權限系統

- ##### Domain Modeling

  - ###### 將 Problem Domain 轉換成 Solution Domain，白話一點就是將真實世界的業務範圍轉換為程式碼。Domain Modeling 應該與領域專家( Domain Expert )共同討論。

  - ###### Modeling 可遵守以下幾點：

    - ###### 必須按照 Domain Model 的行為來建行為模型

    - ###### Model 不需要和真實世界的行為一模一樣，夠用就好

    - ###### 不用一次建到位，可以循序漸進

    - ###### 可以**解決問題**才是重點

    - ![img](https://miro.medium.com/v2/resize:fit:700/1*NHzN_NqzGy9kxkxPU96biQ.png)

      - Form 代表 Domain Model 最終會呈現的模樣
      - Force 代表一種驅動力，使 Domain Model 的行為能符合我們得期望

    - ###### 以一個星巴克的杯子( 大杯冰美式 )為例：

      - Form 就代表整個已產生出的杯子
      - Force 則有多個，例如：必須能裝大杯的容量、不需要吸管即可直接喝或是只能限定裝冰飲

    - 

## Bounded Context

- #####  為解決方案的 Domain Models 建立了一個語意的邊界，讓在其中的 Domain Models 在資料與行為上都能符合 Ubiquitous Language 以及業務需求。

- ##### 主要以**語意**與**業務能力**來重點。可以從找出那些使用相近概念的名詞與在不同情境下的會有歧異的名詞下手，同時也可以藉由分析系統功能的步驟來解析出 Bounded Context。

- ##### Subdomain 你將商業需求拆解與分類的結果；而 Bounded Context 代表你實際解決問題的系統拆解與分類。

- ##### 可以做為微服務拆分的依據

- ##### 電商為例，以下分成三個 Bounded Context，分別為 Catalog (商品目錄)、Purchase (購買)、Identity (身份管理)。

  - ![https://ithelp.ithome.com.tw/upload/images/20190921/20111997mqmU8asR5L.png](https://ithelp.ithome.com.tw/upload/images/20190921/20111997mqmU8asR5L.png)

  - ```
    e-commerce
    ├── catalog
    │   ├── applicationService
    │   ├── domain
    │   └── infrastructure
    ├── identity
    │   ├── applicationService
    │   ├── domain
    │   └── infrastructure
    └── purchase
        ├── applicationService
        ├── domain
        └── infrastructure
    ```

    

## 系統架構

### User Interface(Presentation Layer)

- ##### 負責向使用者顯示資訊和解釋使用者的指令。

- ##### 從字面意思上理解，就是 UI 介面或Controller。

### 應用層 (Application Layer)

- ##### 定義軟體要完成的任務，並且指揮表達領域概念的對象來解決問題。

- ##### 可以操作 Domain Model 去完成業務需求，並把計算與業務邏輯交給 Domain Model。

- ##### 整套協調任務、分配工作的流程，就是「應用」

- ##### 串聯領域層的所有事件，但各別邏輯收在領域層

- ##### 只需要知道，要完成他的流程，需要調用領域層的哪個物件與服務。

  - ###### 「應用層」只管流程步驟，本身並不介入任務的實際執行。

### 領域層 (Domain Layer)

- ##### 負責實現業務邏輯

- #### 實體(Entity)

  - ##### 能被識別出來的物件 有 id。 Entity 的狀態會在其生命週期中持續追蹤其變化。

  - ##### 包含ID的物件，通常代表資料庫的實體

- #### 值對象(Value Object)

  - ##### 無 identity 概念、狀態不可變更的物件 object，用於描述某個事物的特徵。

  - ##### 類似Entity，不包含ID

- #### **聚合 Aggregate**

  - ##### 相關業務目標的物件 包含實體與值物件 所組成，一個聚合即為一個 Transaction 的邊界。並且會在其中選擇一個實體 作為聚合根 Aggregate Root ，所有與外界的溝通都必須交由聚合根來負責。

  - ##### 比如一張訂單由訂單品項、折扣、金流、物流、發票等要素組成，但一旦你修改了一個訂單品項，你必須連帶地重新計算折扣(滿額折扣)、金流(刷退重新付款)、物流(金額限制)、發票(廢除後重新開立)，甚至可能還會影響會員等級的升降。

    - ![https://ithelp.ithome.com.tw/upload/images/20191002/20111997oBvOQAM28D.png](https://ithelp.ithome.com.tw/upload/images/20191002/20111997oBvOQAM28D.png)

    - ##### 若是我們將這些物件都放入 Aggregate 的邊界內，然後指定 `Order` 這個 Entity 作為 Aggregate Root，我們的模型會變成:

    - ![https://ithelp.ithome.com.tw/upload/images/20191002/20111997CpU3Q8hRGE.png](https://ithelp.ithome.com.tw/upload/images/20191002/20111997CpU3Q8hRGE.png)

    - ##### 可以看到，不論是何種狀態變更，都必須先經過 Aggregate Root `Order` 才能進入跟裡面的物件做互動，而 Aggregate Root 不但可以很好地處理這項需求，也可以同時遵守「資料變化時必須保持一致的規則」

    - ##### 假如今天訂單品項修改數量僅會單純地影響庫存變化

      - ###### 原始流程OrderItem.update() → Order.check() → Order.update() → Inventory.update()，

      - ###### 使用 Aggregate，那就會變成 Order.updateItem() → Inventory.update()

- #### 服務(Service)

  - ##### 當有某個業務邏輯職責**無法被歸類**到任何一個領域物件上時，會將以領域服務承載這個職責。處於應用服務與領域物件之間。

  - ##### 領域中的動作，不屬於任何對象，卻代表了一個重要行為，此種行為通常會跨越若干的對象。將此行為加入到任一對象中會破壞對象。

- #### **倉儲 Repository**

  - ##### 保存領域物件的狀態的設計模式，可以轉接資料庫、 ORM 或檔案系統。一般使用上會考慮一個聚合對上一個倉儲。

  - ##### **分離領域模型與資料模型**，你可以更輕鬆的擴展領域模型或是優化資料庫操作的效能，而不用怕互相影響。

  - 

- ### **領域事件 Domain Event**

  - ##### 某件領域專家在乎的事件，通常用於聚合間的溝通。


### 範例

- ##### 以訂單流程為例

  1. ###### 使用者透過Web進行下單動作

  2. ###### 建立訂單、檢查商品、扣庫存、檢查優惠活動....

- ##### User Interface

  - ###### 透過Web進行下單動作

- ##### 應用層

  - ###### 串聯領域層中的各項邏輯

- ##### 領域層

  - ###### 檢查商品: 檢核庫存是否充足、扣除對應數量商品

  - ###### 計算優惠活動: 依據訂單內容計算折扣金額

  - ###### 成立訂單: 檢查各項資料是否充足、成立訂單

  



# NET

## Dependency Injection

- ###### DI 是指將一個程式的相依關係改由呼叫它的外部程式來決定的方法。保留抽象介面，讓組件依賴於抽象介面，當組件要與其它實際的物件發生依賴關係時，藉過抽象介面來注入依賴的實際物件。

- ### 依賴反轉原則(Dependency-Inversion Principle)

  - ###### 高階模組不應該依賴於低階模組。

    - ###### 兩者都應該依賴抽象，不應該直接去依賴，而是必須藉由抽象來隔開。不應該直接去受到實作的影響，而是只要關注在所需要的功能。

    - ###### 如此一來，依賴就「反轉」了。原本是 `高階模組 → 低階模組` 的關係，變成了 `高階模組 → 介面 ← 低階模組`。並不是高階去依賴低階，而是低階去依賴高階要求的功能。

  - ###### 抽象不應該依賴細節；細節應該依賴抽象。

- ### 控制反轉 (Inversion of Control, IoC)

  - ###### 把實例的建立和實例的使用切分開來，不再是由高階模組去建立並控制低階模組，而是我們讓一個控制反轉中心去建立低階模組，然後高階模組要使用的時候再把這個低階模組交給高階模組使用。

  - ###### 如此一來，控制權也跟著反轉過來了，高階模組從主動建立低階模組，變成被動接收低階模組；也就是從原先的 `高階模組 —(建立)→ 低階模組`，變成了 `高階模組 ←(傳遞低階模組)— 控制反轉中心`。

  - ###### 高階模組再也不需要關心如何建立，該建立哪個實體，只專注於使用功能，真正達到介面的精神。低階模組也只需要等待控制反轉中心分發，到了崗位就把份內事做好，專心在自己的職責身上即可。如此一來就能解除兩者之間的耦合。

- ##### 好處

  - ###### 低耦合，高內聚
  
  - ###### 更清晰的意圖
  
  - ###### 降低邏輯變更造成改動程式碼的幅度
  
  - ###### 關注點分離，提高可測試性
  
- 在沒有使用 DI Framework 的情況下，假設在 UserController 要呼叫 UserLogic，會直接在 UserController 實例化 UserLogic，如下：

  ```C#
  public class MyDependency
  {
      public MyDependency()
      {
      }
  
      public Task WriteMessage(string message)
      {
          Console.WriteLine(
              $"MyDependency.WriteMessage called. Message: {message}");
  
          return Task.FromResult(0);
      }
  }
  
  public class IndexModel : PageModel
  {
      MyDependency _dependency = new MyDependency();
  
      public async Task OnGetAsync()
      {
          await _dependency.WriteMessage(
              "IndexModel.OnGetAsync created this message.");
      }
  }
  ```

- 上述程式碼可正常運作，但會出現下列問題

  - 若要將 `MyDependency` 取代為不同的實作，必須修改該類別。
  - 實作`MyDependency` 的地方越多，未來在置換或維護上越不便。
  - 實作難以進行單元測試。 應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。

- 相依性插入可透過下列方式解決這些問題：

  - 使用介面或基底類別來將相依性資訊抽象化
  - 在服務容器中註冊相依性。 ASP.NET Core 提供內建服務容器 [IServiceProvider](https://docs.microsoft.com/dotnet/api/system.iserviceprovider)。 服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊
  - 將服務「插入」到服務使用位置之類別的建構函式。 架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。

- 建構抽象類別

  ```C#
  public interface IMyDependency
  {
      Task WriteMessage(string message);
  }
  ```

- 實作類別

  ```C#
  public class MyDependency : IMyDependency
  {
      private readonly ILogger<MyDependency> _logger;
  
      public MyDependency(ILogger<MyDependency> logger)
      {
          _logger = logger;
      }
  
      public Task WriteMessage(string message)
      {
          _logger.LogInformation(
              "MyDependency.WriteMessage called. Message: {MESSAGE}", 
              message);
  
          return Task.FromResult(0);
      }
  }
  ```

- 注入

  ```C#
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddRazorPages();
  
      services.AddScoped<IMyDependency, MyDependency>();
      services.AddTransient<IOperationTransient, Operation>();
      services.AddScoped<IOperationScoped, Operation>();
      services.AddSingleton<IOperationSingleton, Operation>();
      services.AddSingleton<IOperationSingletonInstance>(new Operation(Guid.Empty));
  
      // OperationService depends on each of the other Operation types.
      services.AddTransient<OperationService, OperationService>();
      
      //沒有使用介面宣告時
      services.AddScoped<UserManager>();
      
      //宣告實體 帶入參數
       services.AddScoped(provider => new UserManager(configManager.IdSvrConfig));
  }
  ```

### lifetimes

- **Transient**: 每次注入時，都重新 `new` 一個新的實例。
- **Scoped**: 每個 **Request** 都重新 `new` 一個新的實例，同一個 **Request** 不管經過多少個 Pipeline 都是用同一個實例。上例所使用的就是 **Scoped**。
- **Singleton**: 被實例化後就不會消失，程式運行期間只會有一個實例。

![[鐵人賽 Day04] ASP.NET Core 2 系列 - 依賴注入(Dependency Injection) - 實例產生動畫](https://blog.johnwu.cc/images/pasted-209.gif)

### View

- View 注入 Service 的方式，直接在 `*.cshtml` 使用 `@inject`：

  ```C#
  @using MyWebsite
  
  @inject ISampleTransient transient
  @inject ISampleScoped scoped
  @inject ISampleSingleton singleton
  
  <table border="1">
      <tr><td colspan="3">Cotroller</td></tr>
      <tr><td>Lifetimes</td><td>Id</td><td>Hash Code</td></tr>
      <tr><td>Transient</td><td>@ViewBag.TransientId</td><td>@ViewBag.TransientHashCode</td></tr>
      <tr><td>Scoped</td><td>@ViewBag.ScopedId</td><td>@ViewBag.ScopedHashCode</td></tr>
      <tr><td>Singleton</td><td>@ViewBag.SingletonId</td><td>@ViewBag.SingletonHashCode</td></tr>
  </table>
  <hr />
  <table border="1">
      <tr><td colspan="3">View</td></tr>
      <tr><td>Lifetimes</td><td>Id</td><td>Hash Code</td></tr>
      <tr><td>Transient</td><td>@transient.Id</td><td>@transient.GetHashCode()</td></tr>
      <tr><td>Scoped</td><td>@scoped.Id</td><td>@scoped.GetHashCode()</td></tr>
      <tr><td>Singleton</td><td>@singleton.Id</td><td>@singleton.GetHashCode()</td></tr>
  </table>
  ```

### Service

```C#
public class CustomService
{
    public ISample Transient { get; private set; }
    public ISample Scoped { get; private set; }
    public ISample Singleton { get; private set; }

    public CustomService(ISampleTransient transient,
        ISampleScoped scoped,
        ISampleSingleton singleton)
    {
        Transient = transient;
        Scoped = scoped;
        Singleton = singleton;
    }
}
```



## Middleware

- SP.NET Core 在 Middleware 的官方說明中，使用了 Pipeline 這個名詞，意旨 Middleware 像水管一樣可以串聯在一起，所有的 Request 及 Response 都會層層經過這些水管。

  ![[鐵人賽 Day03] ASP.NET Core 2 系列 - Middleware - 概念](https://blog.johnwu.cc/images/i03-1.png)

  ![[鐵人賽 Day03] ASP.NET Core 2 系列 - Middleware](https://blog.johnwu.cc/images/pasted-114.gif)

### App.Use

- Middleware 的註冊方式是在 *Startup.cs* 的 `Configure` 對 `IApplicationBuilder` 使用 `Use` 方法註冊。
  大部分擴充的 Middleware 也都是以 **Use** 開頭的方法註冊，例如：
  - **UseMvc()** ：MVC 的 Middleware
  - **UseRewriter()** ：URL rewriting 的 Middleware

```C#
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("First Middleware in. \r\n");
            await next.Invoke();
            await context.Response.WriteAsync("First Middleware out. \r\n");
        });

        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("Second Middleware in. \r\n");
            await next.Invoke();
            await context.Response.WriteAsync("Second Middleware out. \r\n");
        });

        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("Third Middleware in. \r\n");
            // 水管阻塞，封包不往後送
            //await next.Invoke();
            await context.Response.WriteAsync("Third Middleware out. \r\n");
        });

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello World! \r\n");
        });
    }
}

/*
First Middleware in. 
Second Middleware in. 
Second Middleware out. 
First Middleware out. 
*/
```

### App.Run

- `Run` 是 Middleware 的最後一個行為，以上面圖例來說，就是最末端的 Action。它不像 `Use` 能串聯其他 Middleware，但 `Run` 還是能完整的使用 Request 及 Response。

### App.Map

- `Map` 是能用來處理一些簡單路由的 Middleware，可依照不同的 URL 指向不同的 `Run` 及註冊不同的 `Use`。

```C#
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("First Middleware in. \r\n");
            await next.Invoke();
            await context.Response.WriteAsync("First Middleware out. \r\n");
        });

        //進到 http://localhost:5000/second 才會執行
        app.Map("/second", mapApp =>
        {
            mapApp.Use(async (context, next) => 
            {
                await context.Response.WriteAsync("Second Middleware in. \r\n");
                await next.Invoke();
                await context.Response.WriteAsync("Second Middleware out. \r\n");
            });
            mapApp.Run(async context =>
            {
                await context.Response.WriteAsync("Second. \r\n");
            });
        });

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello World! \r\n");
        });
    }
}

/*
https://localhost:5001/
    First Middleware in. 
    Hello World! 
    First Middleware out. 

https://localhost:5001/second
    First Middleware in. 
    Second Middleware in. 
    Second. 
    Second Middleware out. 
    First Middleware out. 
*/
```



### Extract Middleware

- 新增類別檔，將各Middleware獨立出來

```C#
//FirstMiddleware.cs
public class FirstMiddleware
{
    private readonly RequestDelegate _next;

    public FirstMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        await context.Response.WriteAsync($"{nameof(FirstMiddleware)} in. \r\n");

        await _next(context);

        await context.Response.WriteAsync($"{nameof(FirstMiddleware)} out. \r\n");
    }
}
```

#### 全域註冊

- 在 `Startup.Configure` 註冊 Middleware 就可以套用到所有的 Request。如下：

```C#
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app)
    {
        app.UseMiddleware<FirstMiddleware>();
        // ...
    }
}
```

#### 區域註冊

- Middleware 也可以只套用在特定的 Controller 或 Action。註冊方式如下：

```C#
[MiddlewareFilter(typeof(FirstMiddleware))]
public class HomeController : Controller
{
    // ...

    [MiddlewareFilter(typeof(SecondMiddleware))]
    public IActionResult Index()
    {
        // ...
    }
}
```

#### Extensions

- 建立IApplicationBuilder Extension Method

  ```C#
  // Extensions\CustomMiddlewareExtensions.cs
  public static class CustomMiddlewareExtensions
  {
      public static IApplicationBuilder UseFirstMiddleware(this IApplicationBuilder builder)
      {
          return builder.UseMiddleware<FirstMiddleware>();
      }
  }
  ```

  ```C#
  public class Startup
  {
      // ...
      public void Configure(IApplicationBuilder app)
      {
          app.UseFirstMiddleware();
          // ...
      }
  }
  ```

  

## Filter

- ##### Filter可以在進入 Action 與離開 Action 時對 Requset 進行檢查或加工。而將部分檢查放在 Filter 中，除了可以將部分重複的邏輯抽離出來，也可以透過不同種類 Filter 來增加檢查的精確性。

- ##### Middleware 只能存取 HttpContext，而 Filter 可以存取整個 MVC context，所以 Filter 可以存取 Routing data 和 model binding 的資訊。

- ![img](https://miro.medium.com/v2/resize:fit:425/1*p78ahSmAmZZk4dTm2jyOhg.png)

###  Authorization filters

- ##### 檢查使用者是否有授權，若無授權在此階段就可先擋下，擋下後將不再執行之後的流程。

- ##### 以 cookie 中帶 token 傳入驗證為例，若 token 驗證不過則回傳 401 未授權

  - ```c#
    public class AuthorizationFilter : IAsyncAuthorizationFilter
    {
        public async Task OnAuthorizationAsync(AuthorizationFilterContext context)
        {
            var cookies = context.HttpContext.Request.Cookies;
    
            cookies.TryGetValue("token", out string token);
    
            if (token.Equals("123456"))
            {
                var response = new FailResultViewModel
                {
                    CorrelationId = Guid.NewGuid().ToString(),
                    Method = $"{context.HttpContext.Request.Path}.{context.HttpContext.Request.Method}",
                    Status = "UnAuthorized",
                    Version = "1.0",
                    Error = new FailInformation()
                    {
                        Domain = "ProjectName",
                        Message = "未授權",
                        Description = "授權驗證失敗"
                    }
                };
    
                context.Result = new ObjectResult(response)
                {
                    // 401
                    StatusCode = (int)HttpStatusCode.Unauthorized
                };
            }
        }
    }
    ```

  

### Resource filters

- #####  Authorization Filter 後，Model Binding 前執行，以及 Pipeline 中其餘部分都完成後執行。

- ##### 用來處理快取或 Model Binding，如果 Cache 中已經有需要的值那就不需要再繼續執行剩下的步驟了，也可以限制檔案上傳大小等等。

- ##### 以 Cahce 為例，當傳入相同的 Request 時直接從 Cache 回傳結果

  - ```C#
    public class CacheResourceFilter : IAsyncResourceFilter
    {
        private static readonly Dictionary<string, ObjectResult> _cache = new Dictionary<string, ObjectResult>();
    
        public async Task OnResourceExecutionAsync(ResourceExecutingContext context, ResourceExecutionDelegate next)
        {
            var cacheKey = context.HttpContext.Request.Path.ToString();
    
            if (_cache != null && _cache.ContainsKey(cacheKey))
            {
                var cacheValue = _cache[cacheKey];
                if (cacheValue != null)
                {
                    context.Result = cacheValue;
                }
            }
            else
            {
                var executedContext = await next();
    
                var result = executedContext.Result as ObjectResult;
                if (result != null)
                {
                    _cache.Add(cacheKey, result);
                }
            }
        }
    }
    ```

    

### Action filters

- ##### 跟Resource Filters 的差異主要在於進出時機的不同，可以在執行Action前或Action執行後進行檢查。

- ##### 對執行前傳入的參數檢查，若檢查未通過則回傳參數驗證失敗

  - ```C#
    public class ValidationActionFilter : IAsyncActionFilter, IOrderedFilter
    {
        public int Order { get; set; } = 0;
        public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
        {
            var parameter = context.ActionArguments.SingleOrDefault();
            if (parameter.Value is null)
            {
                var response = new FailResultViewModel
                {
                    CorrelationId = Guid.NewGuid().ToString(),
                    Method = $"{context.HttpContext.Request.Path}.{context.HttpContext.Request.Method}",
                    Status = "Error",
                    Version = "1.0",
                    Error = new FailInformation
                    {
                        Domain = "ProjectName",
                        Message = "參數驗證失敗",
                        Description = "傳入參數為null"
                    }
                };
    
                context.Result = new ObjectResult(response)
                {
                    // 400
                    StatusCode = (int)HttpStatusCode.BadRequest
                };
            }
            else
            {
                await next();
            }
        }
    }
    ```

- #####  將執行後的結果再包裝成特定格式後回傳

  - ```C#
    public class MessageActionFilter : IAsyncActionFilter, IOrderedFilter
    {
        public int Order { get; set; } = 0;
        public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
        {
            var executedContext = await next();
    
            var result = executedContext.Result as ObjectResult;
            
            if (result != null
             && !(result.Value is HttpResponseMessage)
             && !(result.Value is SuccessResultViewModel<object>)
             && !(result.Value is FailResultViewModel)
             && !(result.Value is SuccessResultViewModel<ModelStateDictionary>))
            {
                var responseModel = new SuccessResultViewModel<object>
                {
                    Version = "1.0",
                    Method = $"{context.HttpContext.Request.Path}.{context.HttpContext.Request.Method}",
                    Status = "Success",
                    CorrelationId = Guid.NewGuid().ToString(),
                    Data = result.Value
                };
    
                executedContext.Result = new ObjectResult(responseModel)
                {
                    // 200
                    StatusCode = (int)HttpStatusCode.OK
                };
            }
        }
    }
    ```

    

### Exception filters

- ##### 發生 Exception 後會進入的 Filter。

- ##### 針對 Action 拋出的例外狀況進行攔截並包裝成特定格式後回傳

  - ```C#
    public class ExceptionFilter : IAsyncExceptionFilter
    {
        public Task OnExceptionAsync(ExceptionContext context)
        {
            var response = new FailResultViewModel
            {
                CorrelationId = Guid.NewGuid().ToString(),
                Method = $"{context.HttpContext.Request.Path}.{context.HttpContext.Request.Method}",
                Status = "Error",
                Version = "1.0",
                Error = new FailInformation
                {
                    Domain = "ProjectName",
                    ErrorCode = 40000,
                    Message = context.Exception.Message,
                    Description = context.Exception.ToString()
                }
            };
    
            context.Result = new ObjectResult(response)
            {
                // 500
                StatusCode = (int)HttpStatusCode.InternalServerError
            };
    
            // Exceptinon Filter只在ExceptionHandled=false時觸發
            // 所以處理完Exception要標記true表示已處理
            context.ExceptionHandled = true;
    
            return Task.CompletedTask;
        }
    }
    ```

###  Result filters

- ##### Action 執行完畢後執行，若是在 Action 中發生 Exception 則不會經過這個Filter。

- ##### 將訊息加入 Header 回傳

  - ```C#
    public class ResultFilter : IAsyncResultFilter
    {
        public async Task OnResultExecutionAsync(ResultExecutingContext context, ResultExecutionDelegate next)
        {
            if (!(context.Result is EmptyResult))
            {
                var headerName = "OnResultExecuting";
    
                var headerValue = new string[] { "ResultExecuting Successfully" };
    
                context.HttpContext.Response.Headers.Add(headerName, headerValue);
    
                await next();
    
                // 無法在執行後加入 Header，因為 Response 已經開始，此時 Response 可能已經到 Client 端那便無法修改了
            }
            else
            {
                // 若已經被其他 Filter 攔截回傳或是接收到的 context 是空的，則取消 Result 回傳
                // 但是若提前被攔截則並不會進到 ResultFilter
                context.Cancel = true;
            }
        }
    }
    ```

    

### 全域註冊

- ##### 全域註冊的方式所註冊的Filters將會被套用到所有的Request，若 Filter 中有透過DI容器注入服務的話，透過全域註冊的方式其相依性會由DI容器滿足。

- ```C#
  public static class FilterExtensions
  {
      /// <summary>
      /// Adds the message filter.
      /// </summary>
      /// <param name="options">The options.</param>
      public static void AddMessageFilter(this MvcOptions options)
      {
          options.Filters.Add<AuthorizationFilter>();
          options.Filters.Add<CacheResourceFilter>();
          options.Filters.Add<ExceptionFilter>();
          options.Filters.Add(new ValidationActionFilter() { Order = 0 });
          options.Filters.Add(new MessageActionFilter() { Order = 1 });
          options.Filters.Add<ResultFilter>();
      }
  }
  ```

### 區域註冊

- ##### 透過 TypeFilterAttribute 進行註冊。在 Controller 或 Action 上加上 [TypeFilter(typeof(YourFilter)] 的方式進行區域註冊。

- ##### TypeFilterAttribute 參考的類型不需要向DI容器註冊，但所參考的類型中的相依性會由DI容器滿足，若是在 Filter 中透過DI容器注入服務，用此註冊方式可以正常的執行。

- ```C#
  [TypeFilter(typeof(ActionFilter))]
  [HttpGet("test")]
  public async Task<IActionResult> Test()
  {
      return Ok();
  }
  ```

### 透過Attribute註冊

- ##### Filter 也可以透過繼承 Attribute的方式讓自己可以作為附加屬性執行。

- ##### 透過繼承 Attribute的方式來使用的 Filter中的相依性不會透過 DI 容器滿足，故若在 Filter 中有透過 DI 容器注入服務的話，還是要透過另外兩種註冊方式才能正常運行。

- ```C#
  using Microsoft.AspNetCore.Http;
  using Microsoft.AspNetCore.Mvc.Filters;
  using System;
  
  namespace FilterTest.Infars.Filters
  {
      public class ActionFilter : Attribute, IActionFilter
      {
          public void OnActionExecuting(ActionExecutingContext context)
          {
              //Action 執行前執行
              context.HttpContext.Response.WriteAsync($"進入Action Filter。 \r\n");
          }
  
          public void OnActionExecuted(ActionExecutedContext context)
          {
              //Action 執行後執行
              context.HttpContext.Response.WriteAsync($"離開 Action Filter。 \r\n");
          }
      }
  }
  
  //-------------------------------------------------------------------------------------
   [ActionFilter]
  [HttpGet("test")]
  public async Task<IActionResult> Test()
  {
      return Ok();
  }
  ```

  





## Appsettings.json

1. 若執行環境為 *Production* ，只讀 *appsettings.json* 。

2. 若執行環境為 *Development* ，先讀取 *appsettings.json* 再讀取 *appsettings.Development.json* 。並合併兩檔的組態。若組態項目重複，則以後來者覆蓋先前設定，亦即以 *appsettings.Development.json* 為準。

3. #### 如何決定執行環境

   - *launchSettings.json* 內 **ASPNETCORE_ENVIRONMENT**值
   - 如果沒有指定 ASPNETCORE_ENVIRONMENT 環境變數，則預設的執行環境是 *Production* 

4. 將Settings內的



## Attribute

- ###### Attribute 提供一個有用的方法，使中繼資料 (或宣告式資訊) 與程式碼 (組件、型別、方法和屬性 (Property) 等) 產生關聯。 當屬性 (Attribute) 與程式實體 (Entity) 產生關聯之後，即可在執行階段使用稱為「反映」(Reflection) 的技術來加以查詢。

- ###### 透過Attribute定義Enum或類別描述，可透過Reflection取得

  - ###### 定義一個客製化Attribute，並設定`Description`屬性

    - ```C#
      public class ReportDescriptionAttribute : Attribute
      {
          public string Description { get; set; }
      
          public ReportDescriptionAttribute(string Description)
          {
              this.Description = Description;
          }
      
          public override string ToString()
          {
              return this.Description.ToString();
          }
      }
      ```

  - ###### 針對類別內的各屬性加入`Attribute`描述

    - ```C#
      public class SNCode
      {
          /// <summary>
          /// 里程碑主檔
          /// </summary>
          [ReportDescription("里程碑主檔")]
          public const string Milestone = "Milestone_SN";
      
          /// <summary>
          /// 里程碑產出檔案
          /// </summary>
          [ReportDescription("里程碑產出檔案")]
          public const string MilestoneProd = "Milestone_Prod_SN";
      
          /// <summary>
          /// 專案風險檔
          /// </summary>
          [ReportDescription("專案風險檔")]
          public const string ProjectRisk = "Project_Risk_SN";
      
          /// <summary>
          /// 專案風險檔
          /// </summary>
          [ReportDescription("專案風險檔")]
          public const string ProjectMeasure = "Project_Measure_SN";
      }
      ```

  - ###### 透過Reflection取得內容

    - ```c#
      var members = typeof(SNCode).GetMember("MilestoneProd");
      var attributes = members[0].GetCustomAttributes(typeof(ReportDescriptionAttribute), false);
      var description = ((ReportDescriptionAttribute)attributes[0]).Description;
      ```


## Json Web Token

- ##### JWT是一種『有限時間內可利用認證令牌要求對應的操作權限』的一種方法

- ##### 使用者登入後產生Token，後續針對Request內夾帶的Token進行驗證。

  - ##### 客戶端從伺服器端取得認證簽名後，伺服器端只對認證簽名進行驗證，從認證簽名來判斷是不是具備指定的操作資格．除此之外還會判斷令牌是否過期、是否是黑名單等狀況。

- ##### 主要驗證內容為產生Token時的資訊內容。Ex.

  - ###### 相同的 **Issuer **設定值

  - ###### 相同的 **Audience **設定值

  - ###### 驗證 **Token **有效期限

    - ###### 符合對稱式加密的簽章

  - ###### Token內可包含自定義資訊，但驗證時不會針對使用者進行驗證，因為驗證時不會再連接DB。

  - ###### 因此，只要產生Token的設定一致，不同Service也可以透過相同Token驗證

- ##### 包含了 **header**、**payload**和**signature** 三個部分

  - **Header** 

    - ##### 包含了兩個主要資訊：使用的加密演算法和token的類型(基本上就是JWT)

    - ##### 這些內容通過 [Base64](https://zh.wikipedia.org/wiki/Base64) 轉化就能夠得到我們的第一段字串。

      - ```
        eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
        ```

    ```C#
    // 使用HS256演算法來產生JWT token
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```

  - **Payload**

    - ##### 包含了聲明的資料，帶有欲存放的資訊（例如用戶資訊）

    - ##### Payload 又可以被稱為 claims

    - ##### 定義上有 3 種聲明 (Claims)

      - ###### Reserved (註冊聲明)

      - ###### Public (公開聲明)

      - ###### Private (私有聲明)

    - ##### Reserved (註冊聲明)

      - ###### iss (Issuer) - JWT的發行人

      - ###### sub (Subject) - JWT的主題（用戶）

      - ###### aud（audience）：JWT的目標收件人

      - ###### exp（expiration time）：JWT到期的時間

      - ###### nbf（not before time）：不得接受JWT處理的時間

      - ###### iat（issued at time）：發布JWT的時間; 可用於確定JWT的年齡

      - ###### jti（JWT ID）：唯一標識符; 可用於防止JWT被重放（允許令牌僅使用一次）

    - base64 加密（該加密是可以對稱解密的)

    ```C#
    /*
        sub就是RFC7519中定義的基本資訊，
        age則是我們自己加上去的
    */
    
    {
      "sub": "wellwind",
      "age": 30
    }
    ```

  - **Signature**

    - ###### 將被轉換成 Base64 編碼的 Header、Payload 與自己定義的密鑰，透過在 Header 設定的雜湊演算法方式所產生的

    - ###### 用來確保資料完整性的一個雜湊簽章

    - ###### 可以選用任何雜湊演算法來進行處理

- 驗證流程

  ![img](https://dotblogsfile.blob.core.windows.net/user/wellwind/c93407af-3191-48e8-9029-2e02a7e03b1e/1479955047_94613.png)

- 流程

  1. 使用者登入進行身分驗證後產生Token

     ```C#
     [Route("api/[controller]")]
     [ApiController]
     public class AuthController : ControllerBase
     {
         private readonly IConfiguration _config;
     
         public AuthController(IConfiguration configuration)
         {
             _config = configuration;
         }
     
         // GET api/auth/login
         [HttpGet, Route("login")]
         public IActionResult Login(string name)
         {
             // STEP0: 在產生 JWT Token 之前，可以依需求做身分驗證
     
             // STEP1: 建立使用者的 Claims 聲明，這會是 JWT Payload 的一部分
             var userClaims = new ClaimsIdentity(new[] {
                 new Claim(JwtRegisteredClaimNames.NameId, name),
                 new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
                 new Claim("CustomClaim", "Anything You Like")
             });
             // STEP2: 取得對稱式加密 JWT Signature 的金鑰
             // 這部分是選用，但此範例在 Startup.cs 中有設定 ValidateIssuerSigningKey = true 所以這裡必填
             var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
             // STEP3: 建立 JWT TokenHandler 以及用於描述 JWT 的 TokenDescriptor
             var tokenHandler = new JwtSecurityTokenHandler();
             var tokenDescriptor = new SecurityTokenDescriptor
             {
                 Issuer = _config["Jwt:Issuer"],
                 Audience = _config["Jwt:Issuer"],
                 Subject = userClaims,
                 Expires = DateTime.Now.AddMinutes(30),
                 SigningCredentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256)
             };
             // 產出所需要的 JWT Token 物件
             var securityToken = tokenHandler.CreateToken(tokenDescriptor);
             // 產出序列化的 JWT Token 字串
             var serializeToken = tokenHandler.WriteToken(securityToken);
     
             return new ContentResult() { Content = serializeToken };
         }
     }
     ```

  2. 專案內加入驗證

     ```C#
     public void ConfigureServices(IServiceCollection services)
     {
         services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     
         // STEP1: 設定用哪種方式驗證 HTTP Request 是否合法
         services
         // 檢查 HTTP Header 的 Authorization 是否有 JWT Bearer Token
         .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
         // 設定 JWT Bearer Token 的檢查選項
         .AddJwtBearer(options =>
          {
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidIssuer = Configuration["Jwt:Issuer"],
                ValidateAudience = true,
                ValidAudience = Configuration["Jwt:Issuer"],
                ValidateLifetime = true,
                ValidateIssuerSigningKey = true,
                IssuerSigningKey = new SymmetricSecurityKey
                    (Encoding.UTF8.GetBytes(Configuration["Jwt:Key"]))
            };
          });
     }
     ```

  3. 驗證API

     ```C#
     [Route("api/[controller]")]
     [ApiController]
     public class ValuesController : ControllerBase
     {
         // GET api/values/anonymous
         /// <summary>使用匿名登入，無視於身分驗證</summary>
         [AllowAnonymous]
         [HttpGet, Route("anonymous")]
         public IActionResult Anonymous()
         {
             return new ContentResult() { Content = $@"For all anonymous." };
         }
     
         // GET api/values/authorize
         /// <summary>使用身分驗證，HTTP 的 Authorization Header 必須設定合法的 JWT Bearer Token 才能使用</summary>
         [Authorize]
         [HttpGet, Route("authorize")]
         public IActionResult All()
         {
             return new ContentResult() { Content = $@"For all client who authorize." };
         }
     }
     ```

### JwtRegisteredClaimNames 屬性

- 在建立使用者的 Claims 聲明時，我們會用到很多 `JwtRegisteredClaimNames` 結構型別，來取得是先定義好的字串

- |   Claim    | 說明                                                 | 連結                                         |
  | :--------: | :--------------------------------------------------- | -------------------------------------------- |
  |    Jti     | 表示 JWT ID，Token 的唯一識別碼                      | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Iss     | 表示 Issuer，發送 Token 的發行者                     | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Iat     | 表示 Issued At，Token 的建立時間                     | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Exp     | 表示 Expiration Time，Token 的逾期時間               | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Sub     | 表示 Subject，Token 的主體內容                       | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Aud     | 表示 Audience，接收 Token 的觀眾                     | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Typ     | 表示 Token 的類型，例如 JWT 表示 JSON Web Token 類型 | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Nbf     | 表示 Not Before，定義在什麼時間之前，不可用          | http://tools.ietf.org/html/rfc7519#section-4 |
  |   Actort   | 識別執行授權的代理是誰                               | http://tools.ietf.org/html/rfc7519#section-4 |
  |   NameId   | 使用者識別碼                                         | http://tools.ietf.org/html/rfc7519#section-4 |
  | FamilyName | 使用者姓氏                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  | GivenName  | 使用者名字                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  |   Gender   | 使用者性別                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  |   Email    | 使用者的電子郵件                                     | http://tools.ietf.org/html/rfc7519#section-4 |
  | Birthdate  | 使用者生日                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  |  Website   | 使用者的網站                                         | http://tools.ietf.org/html/rfc7519#section-4 |


### Bearer Token 

- HTTP 的認證「Authorization」方案有許多種格式，而 Bearer 就是其中一種且被定義在 Header 中的驗證方案，通常搭配於 JWT 上

- Resource server 是資源服務器，即後端存放用戶生成的 API Token 的服務器。 
- Authorization server 的意思是認證服務器，即後端專門用來處理認證的服務器
- 這些東西都是由 [OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html) 中所定義出來的，在定義中說明了 Client 如何取得 Access Token 的方法



## ORM

### EF Core

- ##### 優點

  - 將Table抽象畫成物件，
  - 抽象化資料存取層，提供對象關聯映射（ORM）功能，讓開發人員可以使用物件模型而不是 SQL。
  - 具有 LINQ（Language Integrated Query）等強大的查詢功能，使得資料存取更加簡單和直觀。
  - 提供 CRUD 操作的自動生成和維護，減少了重複的程式碼。
  - 支援不同的資料庫供應商，並提供了一定程度的跨平台支援。

- 缺點

  - 生成的 SQL 查詢可能不是最佳的，有時可能會導致性能問題。
  - 雖然 EF Core 已經做了很多改進，但在某些情況下，仍然可能出現性能比較差的情況。
  - 對於大型資料集，或者需要高度優化的查詢，可能需要手動調整生成的 SQL 或使用原生 SQL。

- ##### 基本範例

  - ##### 創建Entity

    - ```c#
      public interface BaseEntity
      {
          DateTime CreatedAt { get; set; }
          DateTime ModifiedAt { get; set; }
      }
      
      public class User : BaseEntity
      {
          public int Id { get; set; }
          public string Name { get; set; }
          public DateTime CreatedAt { get; set; }
          public DateTime ModifiedAt { get; set; }
      }
      
      public class Role : BaseEntity
      {
          public int Id { get; set; }
          public string Name { get; set; }
          public DateTime CreatedAt { get; set; }
          public DateTime ModifiedAt { get; set; }
      }
      ```

  - ##### 建立Repository

    - ```C#
      using Microsoft.EntityFrameworkCore;
      using System;
      using System.Threading.Tasks;
      
      public class BaseRepository<TEntity> where TEntity : class, ITrackable
      {
          protected readonly DbContext _context;
          protected readonly DbSet<TEntity> _dbSet;
      
          public BaseRepository(DbContext context)
          {
              _context = context ?? throw new ArgumentNullException(nameof(context));
              _dbSet = context.Set<TEntity>();
          }
      
          public virtual async Task<TEntity> InsertAsync(TEntity entity)
          {
              if (entity is ITrackable trackableEntity)
              {
                  trackableEntity.CreatedAt = DateTime.UtcNow;
                  trackableEntity.ModifiedAt = DateTime.UtcNow;
              }
      
              _context.Entry(entity).State = EntityState.Added;
              await _dbSet.AddAsync(entity);
              await _context.SaveChangesAsync();
              return entity;
          }
      }
      
      public class UserRepository : BaseRepository<User>
      {
          private readonly IUnitOfWork _unitOfWork;
      
          public UserRepository(DbContext context, IUnitOfWork unitOfWork) : base(context)
          {
              _unitOfWork = unitOfWork;
          }
      
          public override async Task<User> InsertAsync(User entity)
          {
              return await base.InsertAsync(entity);
          }
      }
      
      public class RoleRepository : BaseRepository<Role>
      {
          private readonly IUnitOfWork _unitOfWork;
      
          public RoleRepository(DbContext context, IUnitOfWork unitOfWork) : base(context)
          {
              _unitOfWork = unitOfWork;
          }
      
          public override async Task<Role> InsertAsync(Role entity)
          {
            return await base.InsertAsync(entity);
          }
      }
      ```

  - ##### 建立UnitOfWork 管理Transaction

    - ```C#
      public interface IUnitOfWork : IDisposable
      {
          Task BeginTransactionAsync();
          Task CommitAsync();
          Task RollbackAsync();
      }
      
      public class UnitOfWork : IUnitOfWork
      {
          private readonly DbContext _dbContext;
          private DbContextTransaction _transaction;
      
          public UnitOfWork(DbContext dbContext)
          {
              _dbContext = dbContext ?? throw new ArgumentNullException(nameof(dbContext));
          }
      
          public async Task BeginTransactionAsync()
          {
              _transaction = await _dbContext.Database.BeginTransactionAsync();
          }
      
          public async Task CommitAsync()
          {
              try
              {
                  await _dbContext.SaveChangesAsync();
                  await _transaction.CommitAsync();
              }
              catch
              {
                  await RollbackAsync();
                  throw;
              }
          }
      
          public async Task RollbackAsync()
          {
              await _transaction.RollbackAsync();
          }
      
          public void Dispose()
          {
              _transaction?.Dispose();
              _dbContext.Dispose();
          }
      }
      ```

  - ##### 使用

    - ```C#
      // 在Startup.cs中的ConfigureServices方法中配置服務
      services.AddDbContext<MetaDbContext>(options => options.UseSqlServer(connectionString));
      services.AddScoped<IUnitOfWork, UnitOfWork>();
      services.AddScoped<UserRepository>();
      services.AddScoped<RoleRepository>();
      
      // 在需要使用存儲庫和工作單元的地方注入相應的服務
      public class YourService
      {
          private readonly UserRepository _userRepository;
          private readonly RoleRepository _roleRepository;
          private readonly IUnitOfWork _unitOfWork;
      
          public YourService(UserRepository userRepository, RoleRepository roleRepository, IUnitOfWork unitOfWork)
          {
              _userRepository = userRepository;
              _roleRepository = roleRepository;
              _unitOfWork = unitOfWork;
          }
      
          public async Task AddUserAndRoleAsync(User user, Role role)
          {
              await _unitOfWork.BeginTransactionAsync();
      
              try
              {
                  await _userRepository.InsertAsync(user);
                  await _roleRepository.InsertAsync(role);
      
                  await _unitOfWork.CommitAsync();
              }
              catch (Exception ex)
              {
                  await _unitOfWork.RollbackAsync();
                  throw ex;
              }
          }
      }
      ```



### Dapper

- ##### 範例

  - ```C#
    using Dapper;
    using System;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    
    public class UserRepository
    {
        private readonly string _connectionString;
    
        public UserRepository(string connectionString)
        {
            _connectionString = connectionString ?? throw new ArgumentNullException(nameof(connectionString));
        }
    
        public async Task<int> InsertUserAsync(User user, SqlConnection connection, SqlTransaction transaction)
        {
            var query = "INSERT INTO Users (Name) VALUES (@Name); SELECT SCOPE_IDENTITY();";
            return await connection.ExecuteScalarAsync<int>(query, user, transaction);
        }
    }
    
    public class RoleRepository
    {
        private readonly string _connectionString;
    
        public RoleRepository(string connectionString)
        {
            _connectionString = connectionString ?? throw new ArgumentNullException(nameof(connectionString));
        }
    
        public async Task<int> InsertRoleAsync(Role role, SqlConnection connection, SqlTransaction transaction)
        {
            var query = "INSERT INTO Roles (Name) VALUES (@Name); SELECT SCOPE_IDENTITY();";
            return await connection.ExecuteScalarAsync<int>(query, role, transaction);
        }
    }
    
    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
    
    public class Role
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
    
    class Program
    {
        static async Task Main(string[] args)
        {
            var connectionString = "YourConnectionString";
    
            using var connection = new SqlConnection(connectionString);
            await connection.OpenAsync();
    
            using var transaction = connection.BeginTransaction();
    
            var userRepository = new UserRepository(connectionString);
            var roleRepository = new RoleRepository(connectionString);
    
            try
            {
                // 創建User和Role實體
                var newUser = new User { Name = "John Doe" };
                var newRole = new Role { Name = "Admin" };
    
                // 插入User和Role
                var userId = await userRepository.InsertUserAsync(newUser, connection, transaction);
                var roleId = await roleRepository.InsertRoleAsync(newRole, connection, transaction);
    
                // 提交交易
                transaction.Commit();
    
                Console.WriteLine($"New user ID: {userId}");
                Console.WriteLine($"New role ID: {roleId}");
            }
            catch (Exception)
            {
                // 回滾交易
                transaction.Rollback();
                throw;
            }
        }
    }
    ```




# Struct

- |                   | class                    | struct               |
  | ----------------- | ------------------------ | -------------------- |
  | 繼承(inheritance) | 可以                     | 不可以               |
  | 介面(interface)   | 可以                     | 可以                 |
  | NULL              | 可以為null               | 不可以為null         |
  | 型態              | 參考型態(reference type) | 實值型態(value type) |
  | Memory Allocation | Heap                     | Stack                |

- #### 實值型別(value type)

  - ##### 如常用的內建資料型別，例如: int、float、bool都是實值型別 (object和string除外)。簡而言之，我們在使用這些實值型別時，例如傳遞參數，是複製一份相同的資料後，再對資料進行處理。

- #### 參考型別(reference type)

  - ##### 直接對記憶體的位置。進行處理。例如: 上述除外的object和string都是參考型別。

- #### Equals

  - ##### Struct為實質型別，比較內部屬性值是否相同

  - ```C#
     public struct StructType
     {
         public int Id { get; set; }
         public string Name { get; set; }
         public ClassA a { get; set; }
     }
    
       StructType s2 = new StructType();
       s2.Name = "Mike";
       s2.Id = 2;
       StructType s1 = new StructType();
       s1.Name = "Mike";
       s1.Id = 2;
    
       var s = s1.Equals(s2); //true
    ```

  - #### Class為參考型別，比較記憶體位置

    - ```C#
      public struct StructType
      {
         public int Id { get; set; }
         public string Name { get; set; }
         public ClassA a { get; set; }
      }
      
      StructType s2 = new StructType();
      s2.Name = "Mike";
      s2.Id = 2;
      s2.a = new ClassA();
      StructType s1 = new StructType();
      s1.Name = "Mike";
      s1.Id = 2;
      s1.a = new ClassA();
      var s = s1.Equals(s2);
      //因為兩個Class對應記憶體位置不同，故結果為False
      ```

    - ![img](https://dotblogsfile.blob.core.windows.net/user/%E4%B9%9D%E6%A1%83/dc60b613-5aab-4031-b07e-ba95b3eb8c59/1519278342_30764.png)

# Record

- #### C#9 新增，主要用途是封裝資料，特別是不可改變的資料（immutable data）。

- #### 宣告: ` public record Student(int Id, string Name);`

  - ##### 參數會由編譯器自動建立對應的屬性，而且還會加入一個帶有相同參數列的建構式

  - ##### 建立執行個體的時候也可以加上初始設定式，但是傳入建構式的參數依然不可少

    - ```C#
      Student stu1 = new (1, "Mike") { Id=2 }; // OK!
      Student stu2 = new ();              // 編譯失敗! 
      Student stu3 = new () { Id=3 };     // 編譯失敗! 
      ```

  - ##### 編譯器產生的程式碼(編譯時會轉換成Class)

    - ```C#
      //宣告型別的地方（第 1 行），record 變成了 class，而且實作了 IEquatable<T> 介面，以便比對兩個物件是否相等
      class Student : IEquatable<Student>
      {
          public int Id { get; init; } 
          public string Name { get; init; }
          
          //加入一個基礎建構式
          public Student(int Id, string Name)
          {
              this.Id = Id;
              this.Name = Name;
          }
          
          //加入一個拷貝建構式（copy constructor）
          protected Student(Student orginal)
          {
              this.Id = original.Id;
              this.Name = original.Name;
          }
          
          //加入一個特殊命名的 Clone 方法
          public virtual Student <Clone>$() 
              => new Student(this);
              
          //分解式
          public void Deconstruct(...) { ... }
          
          public override string ToString() { ... }
          // 省略其他方法，包括改寫的 Equals、GetHashCode 等等。
      }
      ```

    - #### ToString

      - ##### Record編譯後會產生ToString方法，將整個物件的內容——包括型別名稱、公開屬性的名稱與值——全兜成一個容易閱讀的字串，方便我們隨時觀察或除錯物件的屬性值

      - ###### Class的ToString()會傳回該類別的名稱

        - `DotNet8.Controllers.WeatherForecastController+MyClass`

      - ###### Record.Tostring

        - `Student { Id = 1, Name = Mike }`

    - #### Equals

      - ##### .NET 參考型別所提供的預設 `Equals` 方法僅只是單純比較兩個變數是否指向同一個物件（即記憶體為址是否相同），因此兩個類別雖然內容相同但因為記憶體位置不同因此判斷為不同物件

      - ##### Record在編譯時會自動改寫了 `Equals` 方法，而且只要兩個比較對象的內容完全一樣（所有屬性值皆相等）即視為相等。

        - ```C#
          MyClass obj1 = new() { Id=1, Name="Mike" };
          MyClass obj2 = new() { Id=1, Name="Mike" };
          Console.WriteLine($"兩物件是否相等: {obj1.Equals(obj2)}");//false
          
          Student stu1 = new (1, "Mike");
          Student stu2 = new(2, "AAA") { Id=1,Name = "Mike"};
          Console.WriteLine($"兩物件是否相等: {stu1.Equals(stu2)}");//true
          ```

    - #### Deconstruct

      - ```C#
        class Student : IEquatable<Student>
        {     
            public void Deconstruct(out int Id, out string Name)
            {
                Id = this.Id;
                Name = this.Name;
            }
            // 其餘省略
        }
        
        Student stu = new (1, "Mike");
        
        var (id, name) = stu;		
        Console.WriteLine($"id={id}, name={name}");
        //id=1, name=Mike
        ```





# 金流

## 藍新金流

### 支付流程

1. ##### 測試環境申請一組帳號。資料可以為虛假(測試用)

2. ##### 建立店家，並取得相關資訊(商店代號、API串接金鑰)

3. ##### 前端呼叫訂單API，API將所需資訊組合成Form表單回傳給前端

   - ```C#
     //接收前端傳過來的訂單資料，並組出藍新所需表單內容
     public IActionResult SendToNewebPay(SendToNewebPayIn inModel)
     {
         SendToNewebPayOut outModel = new SendToNewebPayOut();
     
         //建立藍新所需資料Model
         List<KeyValuePair<string, string>> TradeInfo = new List<KeyValuePair<string, string>>();
         // 商店代號，步驟2取得
         TradeInfo.Add(new KeyValuePair<string, string>("MerchantID", inModel.MerchantID));
         // 回傳格式
         TradeInfo.Add(new KeyValuePair<string, string>("RespondType", "String"));
         // TimeStamp
         TradeInfo.Add(new KeyValuePair<string, string>("TimeStamp", DateTimeOffset.Now.ToOffset(new TimeSpan(8, 0, 0)).ToUnixTimeSeconds().ToString()));
         // 串接程式版本
         TradeInfo.Add(new KeyValuePair<string, string>("Version", "2.0"));
         // 商店訂單編號
         TradeInfo.Add(new KeyValuePair<string, string>("MerchantOrderNo", inModel.MerchantOrderNo));
         // 訂單金額
         TradeInfo.Add(new KeyValuePair<string, string>("Amt", inModel.Amt));
         // 商品資訊
         TradeInfo.Add(new KeyValuePair<string, string>("ItemDesc", inModel.ItemDesc));
         // 繳費有效期限(適用於非即時交易)
         TradeInfo.Add(new KeyValuePair<string, string>("ExpireDate", inModel.ExpireDate));
         
         // 藍新支付頁面失敗或完成後，系統導回的路徑位置(這邊會回傳到CallbackReturn API)
         TradeInfo.Add(new KeyValuePair<string, string>("ReturnURL", "https://1d18-1-162-108-143.jp.ngrok.io/Home/CallbackReturn"));
         // 當使用者支付成功後，藍新系統會透過幕後方式回傳給商店相關支付結果資料
         TradeInfo.Add(new KeyValuePair<string, string>("NotifyURL", "https://1d18-1-162-108-143.jp.ngrok.io/Home/CallbackNotify"));
         // 系統取號後以 form post 方式將結果導回商店指定的網址
         TradeInfo.Add(new KeyValuePair<string, string>("CustomerURL", "https://1d18-1-162-108-143.jp.ngrok.io/Home/CallbackCustomer"));
         // 支付取消返回商店網址
         TradeInfo.Add(new KeyValuePair<string, string>("ClientBackURL", inModel.ClientBackURL));
             
         
         // 付款人電子信箱
         TradeInfo.Add(new KeyValuePair<string, string>("Email", inModel.Email));
         // 付款人電子信箱 是否開放修改(1=可修改 0=不可修改)
         TradeInfo.Add(new KeyValuePair<string, string>("EmailModify", "0"));
     
         //開啟信用卡一次付清選項
         TradeInfo.Add(new KeyValuePair<string, string>("CREDIT", "1"));
     
         //ATM 付款
         if (inModel.ChannelID == "VACC")
         {
             TradeInfo.Add(new KeyValuePair<string, string>("VACC", "1"));
         }
         string TradeInfoParam = string.Join("&", TradeInfo.Select(x => $"{x.Key}={x.Value}"));
     
         // API 傳送欄位
         // 商店代號步驟2取得
         outModel.MerchantID = inModel.MerchantID;
         // 串接程式版本
         outModel.Version = "2.0";
         //交易資料 AES 加解密
         IConfiguration Config = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build();
         string HashKey = Config.GetSection("HashKey").Value;//API 串接金鑰
         string HashIV = Config.GetSection("HashIV").Value;//API 串接密碼
         string TradeInfoEncrypt = EncryptAESHex(TradeInfoParam, HashKey, HashIV);
         
         //交易資料加密
         outModel.TradeInfo = TradeInfoEncrypt;
         //交易資料 SHA256 加密
         outModel.TradeSha = EncryptSHA256($"HashKey={HashKey}&{TradeInfoEncrypt}&HashIV={HashIV}");
     
         // 將model 轉換為List<KeyValuePair<string, string>>, null值不轉
         List<KeyValuePair<string, string>> postData = LambdaUtil.ModelToKeyValuePairList<SendToNewebPayOut>(outModel);
     
         Response.Clear();
     
         StringBuilder s = new StringBuilder();
         s.Append("<html>");
         s.AppendFormat("<body onload='document.forms[\"form\"].submit()'>");
         s.AppendFormat("<form name='form' action='{0}' method='post'>", "https://ccore.newebpay.com/MPG/mpg_gateway");
         foreach (KeyValuePair<string, string> item in postData)
         {
             s.AppendFormat("<input type='hidden' name='{0}' value='{1}' />", item.Key, item.Value);
         }
     
         StringBuilder s = new StringBuilder();
         s.AppendFormat("<form id='payForm' action='{0}' method='post'>", "https://ccore.newebpay.com/MPG/mpg_gateway");
         foreach (KeyValuePair<string, string> item in postData)
         {
             s.AppendFormat("<input type='hidden' name='{0}' value='{1}' />", item.Key, item.Value);
         }
     
         s.Append("</form>");
     
         return Json(s.ToString());
     }
     ```

4. ##### 前端直接執行回傳的Form Submit，呼叫藍新API開始導入付款頁面

   - ```javascript
     $.ajax({
        url: '@Url.Content("~/Home/SendToNewebPay")',
        method: 'POST',
        dataType: 'json',
        data: { inModel: postData, __RequestVerificationToken: $('@Html.AntiForgeryToken()').val() },
        success: function(returnObj) {
            /*呼叫付款API後，取得API所彙整之付款form表單
            直接執行表單submit事件觸發導頁*/
            $("#divContent").html(returnObj);
            $("#payForm").submit();
        },
        error: function(err) {
        alert(err.status + " " + err.statusText + '\n' + err.responseText);
        }
     });
     ```

5. ##### 轉至付款頁面進行處理，付款選項會根據步驟3中所建立的參數決定(如VACC,CREDIT)

   ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208132334128.png)

6. ##### 加入支付完成返回商店網址方法

   - ###### 送出訂單 API 時會跳轉至藍新金流付款畫面，當使用者完成付款動作時，藍新金流會回傳呼叫我們的「支付完成返回商店網址」，這裡會接收使用者付款的狀態，我們可以從回傳資訊內查詢使用者是否已付款。

   - ###### 接收方法裡面要做的就是解密資料，我們傳送前有加密後再傳送，接收時也需要解密才能看到資料，解密方法是 AES

   - ```C#
     /// <summary>
     /// 支付完成返回網址
     /// </summary>
     /// <returns></returns>
     public IActionResult CallbackReturn()
     {
     	StringBuilder receive = new StringBuilder();
     	foreach (var item in Request.Form)
     	{
     		receive.AppendLine(item.Key + "=" + item.Value + "<br>");
     	}
         //接收參數
     	ViewData["ReceiveObj"] = receive.ToString();
      
     	IConfiguration Config = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build();
     	string HashKey = Config.GetSection("HashKey").Value;//API 串接金鑰
     	string HashIV = Config.GetSection("HashIV").Value;//API 串接密碼
        
     	string TradeInfoDecrypt = DecryptAESHex(Request.Form["TradeInfo"], HashKey, HashIV);
     	NameValueCollection decryptTradeCollection = HttpUtility.ParseQueryString(TradeInfoDecrypt);
     	receive.Length = 0;
     	foreach (String key in decryptTradeCollection.AllKeys)
     	{
     		receive.AppendLine(key + "=" + decryptTradeCollection[key] + "<br>");
     	}
         
     	// 交易訊息
     	ViewData["TradeInfo"] = receive.ToString();
      
     	return View();
     }
     ```

7. ##### 加入商店取號網址方法

   - ###### 商店取號網址會用在使用者選擇 ATM 付款時，藍新金流會呼叫這個網址，告知我們使用者要匯款的銀行代碼和帳號是多少，我們可以顯示銀行代碼和帳號給使用者知道

   - ###### 我們可以從 BankCode 和 CodeNo 知道使用者要匯款的銀行代碼和帳號。

   - ```C#
     /// <summary>
     /// 商店取號網址
     /// </summary>
     /// <returns></returns>
     public IActionResult CallbackCustomer()
     {
     	// 接收參數
     	StringBuilder receive = new StringBuilder();
     	foreach (var item in Request.Form)
     	{
     		receive.AppendLine(item.Key + "=" + item.Value + "<br>");
     	}
     	ViewData["ReceiveObj"] = receive.ToString();
      
     	// 解密訊息
     	IConfiguration Config = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build();
     	string HashKey = Config.GetSection("HashKey").Value;//API 串接金鑰
     	string HashIV = Config.GetSection("HashIV").Value;//API 串接密碼
     	string TradeInfoDecrypt = DecryptAESHex(Request.Form["TradeInfo"], HashKey, HashIV);
     	NameValueCollection decryptTradeCollection = HttpUtility.ParseQueryString(TradeInfoDecrypt);
     	receive.Length = 0;
     	foreach (String key in decryptTradeCollection.AllKeys)
     	{
     		receive.AppendLine(key + "=" + decryptTradeCollection[key] + "<br>");
     	}
     	ViewData["TradeInfo"] = receive.ToString();
     	return View();
     }
     ```

8. ##### 加入支付通知網址方法

   - ###### 當使用者實際付款完成時，不管是信用卡、ATM 或超商付款，會收到的通知

   - ###### 例如使用者選擇 ATM 付款時，任何時間在 ATM 完成付款，我們從這方法收到通知後，寫入資料庫內紀錄使用者已付款就好。

     ```C#
     /// <summary>
     /// 支付通知網址
     /// </summary>
     /// <returns></returns>
     public IActionResult CallbackNotify()
     {
     	// 接收參數
     	StringBuilder receive = new StringBuilder();
     	foreach (var item in Request.Form)
     	{
     		receive.AppendLine(item.Key + "=" + item.Value + "<br>");
     	}
     	ViewData["ReceiveObj"] = receive.ToString();
      
     	// 解密訊息
     	IConfiguration Config = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build();
     	string HashKey = Config.GetSection("HashKey").Value;//API 串接金鑰
     	string HashIV = Config.GetSection("HashIV").Value;//API 串接密碼
     	string TradeInfoDecrypt = DecryptAESHex(Request.Form["TradeInfo"], HashKey, HashIV);
     	NameValueCollection decryptTradeCollection = HttpUtility.ParseQueryString(TradeInfoDecrypt);
     	receive.Length = 0;
     	foreach (String key in decryptTradeCollection.AllKeys)
     	{
     		receive.AppendLine(key + "=" + decryptTradeCollection[key] + "<br>");
     	}
     	ViewData["TradeInfo"] = receive.ToString();
      
     	return View();
     }
     ```

9. 流程圖

   - ![img](https://ithelp.ithome.com.tw/upload/images/20220314/201394871haWaGe4iD.png)

- ### 其他

  - #### 測試用卡號: 4000-2211-1111-1111

  - ##### 參考文件

    - [藍新金流 NewebPay API 串接教學](https://blog.hungwin.com.tw/core-mvc-newebpay/)
    -  [藍新金流API 文件下載區](https://www.newebpay.com/website/Page/content/download_api)
    - **[[C#\] 智付通SPGateway(藍新)金流串接](https://harry-lin.blogspot.com/2019/01/c-spgateway.html)** 
    - [.NET 前後分離 Web API 藍新金流串接](https://ithelp.ithome.com.tw/articles/10284330)



### 訂閱流程

- #### 系統流程說明

  1. #### Client發起定期定額支付請求

  2. #### 系統依據訂單內容建置藍新發送格式(Form格式)

     - ###### RespondType: JSON/ String

     - ###### TimeStamp: 系統時間搓記

     - ###### Version: 串接版本

       - 帶入 1.0 版本，則[背面末三碼]將為 必填欄位。
       - 帶入 1.1 版本，則[背面末三碼]將為 非必填
       - 帶入 1.2 版本，則增加 『PeriodFirstdate』欄位，此欄位為非 必填 
       - 帶入 1.3 版本，『Paymentmethod』 欄位新增 UNIONPAY = 銀聯卡回傳參數

     - ###### LangType: 語系，預設中文

     - ###### MerOrderNo: 系統訂單編號

     - ###### ProdDesc: 產品名稱

     - ###### PeriodAmt: 委託金額(每期付款金額)

     - ###### PeriodType: 週期類別

       - D=固定天期制 (2-999 天)
       - W=每週 
       - M=每月
       - Y=每年

     - ###### PeriodPoint: 交易週期授權時間

       - 當週期參數為 D 時，此欄位值限為 數字 2~999，以授權日期隔日起算
         -  例：數值為 2，則表示每隔兩天會執 行一次委託單。
       - 當週期參數為 W 時，此欄位值限為 數字 1~7
         - 數字 1~7 表示每週一~ 週日。
       - 當週期參數為 M 時，此欄位值限為 數字 01~31
         - 數字 01~31 表示每月 1 號~31 號
         - 若當月沒該日期則由該 月的最後一天做為扣款日。
       - 5.當週期參數為 Y 時，此欄位值格式為 MMDD。

     - ###### PeriodStartType: 檢查卡號模式

       - １=立即執行十元授權 

         - （因部分發卡銀行會阻擋一元交 易，因此調整為十元）
         - 委託建立當下系統將發動一筆十 元信用卡授權交易，此交易授權 成功後，系統將立即自動取消授 權，付款人將不會被扣款

       - ２=立即執行委託金額授權

         - 委託單建立當下，立即執行委託 金額授權。

         - ###### 如果執行日恰好等於第一期授權日，則視為第一期授權

         - ###### 若執行日不等於第一期授權日時，則自動曾為第一次授權期數會多一期

       - ３=不檢查信用卡資訊，不授權

         - 委託單自動成立。
         - 首期授權時授權失敗，則系統會 自動終止該張委託單。

     - ```C#
       public string GetPeriodCallBack(SendToNewebPayIn inModel)
       {
           // 藍新金流線上付款
       
           var TradeInfo = new Dictionary<string, object> {
               // 回傳格式
               { "RespondType", "JSON"},
               // TimeStamp
               { "TimeStamp", DateTimeOffset.Now.ToOffset(new TimeSpan(8, 0, 0)).ToUnixTimeSeconds().ToString()},
               // 串接程式版本
               { "Version", "1.0"},
               // 訂單編號
               { "MerOrderNo", inModel.MerchantOrderNo},
               // 商品資訊
               { "ProdDesc", inModel.ItemDesc},
               // 定期金額
               { "PeriodAmt", inModel.Amt},
               // 週期類別 年/月/日
               { "PeriodType", "M"},
               // 交易週期授權時間 ex.每周禮拜幾、每月幾號、每隔幾天、每年的MMDD
               {"PeriodPoint", DateTime.Now.Day.ToString() },
               // 檢查卡號模式
               { "PeriodStartType", "2"},
               // 授權期數
               { "PeriodTimes", "10"},
               // 付款人電子信箱
               { "PayerEmail", inModel.Email},
               // 是否開啟付款人資訊
               { "PaymentInfo", 'N'},
               // 是否開啟付款人資訊
               {"OrderInfo", 'N' },
               // 支付通知網址
               {"NotifyURL", $"{Config.GetSection("HostURL").Value}/Notify/NewebPayPeriodNotify" },
               // 是否開放使用者修改Mail
               {"EmailModify", "0" }
           };
       
           string TradeInfoParam = string.Join("&", TradeInfo.Select(x => $"{x.Key}={x.Value}"));
       
       
           SendToNewebPayPeriod outModel = new SendToNewebPayPeriod();
           //交易資料 AES 加解密
           string HashKey = Config.GetSection("HashKey").Value;//API 串接金鑰
           string HashIV = Config.GetSection("HashIV").Value;//API 串接密碼
           string TradeInfoEncrypt = EncryptAESHex(TradeInfoParam, HashKey, HashIV);
       
       
           StringBuilder s = new StringBuilder();
           s.AppendFormat("<form id='payForm' action='{0}' method='post'>", "https://ccore.spgateway.com/MPG/period");
           s.AppendFormat("<input type='hidden' name='{0}' value='{1}' />", "MerchantID_", Config.GetSection("MerchantID").Value);
           s.AppendFormat("<input type='hidden' name='{0}' value='{1}' />", "PostData_", EncryptAESHex(TradeInfoParam, HashKey, HashIV));
           s.Append("</form>");
       
           return s.ToString();
       }
       ```

  3. #### Client接收Server端回傳格式後開始呼叫藍新支付(商店以前端「Form Post」方式傳送交易資料至藍新金流進行交易。)

     - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208211452855.png)

  4. #### 藍新依據設定開始進行信用卡授權測試

  5. #### 信用卡端回傳信用卡驗證結果

  6. #### 藍新依據步驟2 內設定的`NotifyURL`將結果回傳給Server端

     - ###### 當付款人每期執行信用卡授權交易完 成後，以 Form Post 方式通知商店授權結果。

     - ###### 回傳結果會以AES加密的形式夾帶在`Period`字串中，需透過商店的`HashKey`、`HashIV`進行解密

     - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208211425705.png)

     - ##### 回傳內容包含`PeriodNo` 代表藍新的委託單號，後續須透過此單號進行狀態修改(暫停、取消訂閱)

       - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208211427382.png)

  7. #### 藍新依據步驟2內設定的`ReturnURL`將頁面進行導頁

     - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208211428112.png)

- #### 流程圖

  - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208211429154.png)

### 訂閱變更流程

- #### 系統流程

  1. ##### 向提出商務人員或藍新金流客戶服務中心提出申請，由藍新金流審核並設 定完成後，商店始得使用本功能

  2. ##### 透過api向藍新發送狀態變更參數(暫停、取消、重新開始)

     - ###### 透過訂單編號`MerOrderNo`、委託單號`委託單號`進行該委託單狀態變更

     - ```c#
       public async Task<string> GetUpdatePeriodCallBackAsync(string orderNo, string PeriodNo)
       {
           // 藍新金流線上付款
       
           var TradeInfo = new Dictionary<string, object> {
               // 回傳格式
               { "RespondType", "JSON"},
               // TimeStamp
               { "TimeStamp", DateTimeOffset.Now.ToOffset(new TimeSpan(8, 0, 0)).ToUnixTimeSeconds().ToString()},
               // 固定帶 1.0
               { "Version", "1.0"},
               // 訂單編號
               { "MerOrderNo", orderNo},
               // 委託單號
               { "PeriodNo", PeriodNo},
               // 委託狀態
               { "AlterType", "terminate"},
           };
       
           string TradeInfoParam = string.Join("&", TradeInfo.Select(x => $"{x.Key}={x.Value}"));
       
           SendToNewebPayPeriod outModel = new SendToNewebPayPeriod();
           //交易資料 AES 加解密
           string HashKey = Config.GetSection("HashKey").Value;//API 串接金鑰
           string HashIV = Config.GetSection("HashIV").Value;//API 串接密碼
       
           var content = new FormUrlEncodedContent(new[]
           {
               new KeyValuePair<string, string>("MerchantID_", Config.GetSection("MerchantID").Value),
               new KeyValuePair<string, string>("PostData_", EncryptAESHex(TradeInfoParam, HashKey, HashIV))
           });
       
           using (HttpClient client = new HttpClient())
           {
               HttpResponseMessage response = await client.PostAsync("https://ccore.newebpay.com/MPG/period/AlterStatus", content);
               var result = await response.Content.ReadAsStringAsync();
               var obj = JsonConvert.DeserializeObject<PeriodReturn>(result);
               return DecryptAESHex(obj.Period, HashKey, HashIV);
           }
       
           return "";
       }
       ```

  3. #####  回傳變更結果

     - ###### 回傳結果會以AES加密的形式夾帶在`Period`字串中，需透過商店的`HashKey`、`HashIV`進行解密

     - ###### 若成功則回傳"SUCCESS"，失敗則回傳錯誤代碼

       - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202208211512611.png)

       - ###### PER10074: 本 API 需由藍新金流審核通過後才得以使用，若有串接需求請聯繫客戶服務中心或商務 經理。

- #### 流程圖

  - ![image-20220821150534205](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20220821150534205.png)



## 綠界

- ![img](https://developers.ecpay.com.tw/wp-content/uploads/2022/01/%E5%85%A8%E6%96%B9%E4%BD%8D%E4%BB%98%E6%AC%BE%E4%BA%A4%E6%98%93%E6%B5%81%E7%A8%8B2022-5-1024x727.png)

- ### 可用測試帳號

  - ```tex
    特店測試資料： 模擬銀行3D驗證
    特店編號(MerchantID)：3002607
    特店後台登入帳號：stagetest3
    特店後台登入密碼：test1234
    特店統一編號：00000000
    串接金鑰HashKey：pwFHCqoQZGmho4w6
    串接金鑰HashIV：EkRm7iFT261dpevs
    
    平台商測試資料：
    特店編號(MerchantID)：3002599
    特店後台登入帳號：stagetest2
    特店後台登入密碼：test1234
    身分證末四碼：3609
    串接金鑰HashKey：spPjZn66i0OhqJsQ
    串接金鑰HashIV：hT5OJckN45isQTTs
    
    一般信用卡測試卡號 : 4311-9522-2222-2222 安全碼 : 222
    圓夢彈性分期信用卡測試卡號 : 4938-1777-7777-7777 安全碼 : 222　(此組信用卡用來測試圓夢分期服務，圓夢分期服務請參考)
    ```

- ### 流程

  1. ##### 前端呼叫訂單API，API將所需資訊組合成Form表單回傳給前端

     - ```C#
       public string GetCallBack(SendToNewebPayIn inModel)
       {
           var orderId = Guid.NewGuid().ToString().Replace("-", "").Substring(0, 20);
           var order = new Dictionary<string, string>
           {
               //特店交易編號
               { "MerchantTradeNo",  orderId},
       
               //特店交易時間 yyyy/MM/dd HH:mm:ss
               { "MerchantTradeDate",  DateTime.Now.ToString("yyyy/MM/dd HH:mm:ss")},
       
               //交易金額
               { "TotalAmount",  inModel.Amt},
       
               //交易描述
               { "TradeDesc",  inModel.ItemDesc},
       
               //商品名稱
               { "ItemName", inModel.ItemDesc},
       
               //允許繳費有效天數(付款方式為 ATM 時，需設定此值)
               { "ExpireDate",  "3"},
       
               //自訂名稱欄位1
               { "Email",  inModel.Email},
       
               //自訂名稱欄位2
               { "CustomField2",  ""},
       
               //自訂名稱欄位3
               { "CustomField3",  ""},
       
               //自訂名稱欄位4
               { "CustomField4",  ""},
       
               //完成後發通知
               { "ReturnURL",  $"{Config.GetSection("HostURL").Value}/Home/CallbackNotify?option=ECPay"},
       
               //付款完成後導頁
               { "OrderResultURL", $"{Config.GetSection("HostURL").Value}/Home/CallbackReturn?option=ECPay"},
       
       
               //付款方式為 ATM 時，當使用者於綠界操作結束時，綠界回傳 虛擬帳號資訊至 此URL
               { "PaymentInfoURL",$"{Config.GetSection("HostURL").Value}/Home/CallbackCustomer?option=ECPay"},
       
               //付款方式為 ATM 時，當使用者於綠界操作結束時，綠界會轉址至 此URL。
               { "ClientRedirectURL",  $"{Config.GetSection("HostURL").Value}/Home/CallbackCustomer?option=ECPay"},
       
               //特店編號， 2000132 測試綠界編號
               { "MerchantID",  "3002599"},
       
               //忽略付款方式
               { "IgnorePayment",  "GooglePay#WebATM#CVS#BARCODE"},
       
               //交易類型 固定填入 aio
               { "PaymentType",  "aio"},
       
               //選擇預設付款方式 固定填入 ALL
               { "ChoosePayment",  "ALL"},
       
               //CheckMacValue 加密類型 固定填入 1 (SHA256)
               { "EncryptType",  "1"},
           };
       
           //檢查碼
           order["CheckMacValue"] = GetCheckMacValue(order);
       
           StringBuilder s = new StringBuilder();
           s.AppendFormat("<form id='payForm' action='{0}' method='post'>", "https://payment-stage.ecpay.com.tw/Cashier/AioCheckOut/V5");
           foreach (KeyValuePair<string, string> item in order)
           {
               s.AppendFormat("<input type='hidden' name='{0}' value='{1}' />", item.Key, item.Value);
           }
       
           s.Append("</form>");
       
           return s.ToString();
       }
       ```

  2. ##### 前端直接執行回傳的Form Submit，呼叫藍新API開始導入付款頁面

  3. ##### 付款結束後進入`CallbackReturn`

  4. ##### 卻入付款後支付平台呼叫`CallbackNotify`，此時可針對後續訂單進行處理

  5. ##### ATM付款選項時，金流呼叫`CallbackCustomer` 回傳虛擬帳戶

- ### 其他

  - #### [綠界官網](https://www.ecpay.com.tw/Service/API_Dwnld)

  - #### [「綠界(Ecpay)」金流介接教學](https://weitechshare.blogspot.com/2020/11/ecpay.html)
  
  - #### [測試後台](https://vendor-stage.ecpay.com.tw/Frame/Index)
