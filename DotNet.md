# 資料結構

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

## Queue

## Hash

## Sort



## 通訊協定

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



# Application

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

### 參考

- [ASP.NET Core 註冊 StackExchange.Redis 的方式](https://blog.yowko.com/stackexchange-redis-in-aspdotnet-core/)





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

      
