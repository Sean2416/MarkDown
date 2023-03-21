# Docker

##  Image

### 基本指令

- ##### `docker pull nginx:1.20.0`

- ##### `docker inspect {Image ID}`

  - ###### 查看image 完整訊息

- #### 刪除

  - ##### `docker rmi [Image ID}]`

  - ###### `docker rmi [ImageName]:[Tag]`

- ##### `docker image ls -q`

  - ###### 列出所有image ID

### Image 產生方式

#### 檔案匯入/ 匯出(可離線運作)

- ##### `docker image save ubuntu:latest -o ubuntu.image`

  - ###### 將 image匯出至系統位置

  - ###### -o : output

- ##### `docker image load -i .\ubuntu.image`

  - ###### 將指定目錄印象檔匯入至docker image

  - ###### -i : input

#### Docker File

##### 基本語法

- ##### FROM

  - ###### 指定基礎環境Image (如.mcr.microsoft.com/dotnet/core/sdk:3.1)

- ##### WORKDIR

  - ###### 切換至目標路徑(不存在則建立)

- ##### 複製文件至Image中

  - ###### 包含Copy、Add兩種選擇

    - ###### `COPY <源路径> <目标路径>`

    - ###### ex. `COPY *.csproj .` 將.csproj複製到目錄中

  - ###### 兩者都可以把local的文件複製到Image內，如果指定的目錄不存在則會自動創建

  - ###### Add相較於Copy再複製壓縮文件時會自動解壓縮

    - `ADD <源路径> <目标路径>`

    - ###### 從 URL、tar 文件和壓縮文件中提取文件，并複製到 Docker 镜像中

    - ###### ex.`ADD http://example.com/app.tar.gz /app`

  - ##### 盡可能使用 `COPY` 指令，只有在必要时才使用 `ADD` 指令

- ##### CMD

  - ###### 容器啟動时運行的默認命令和參數。使用 `docker run` 命令启动一个容器时，如果没有指定要运行的命令和参数，Docker 将使用 `CMD` 指令中指定的默认命令和参数。

  - ###### 使用 JSON 的形式指定要运行的命令和参数。

  - ###### 第一个元素是可执行文件或命令，其余元素是该命令的参数

  - ###### 在 Dockerfile 中，只能有一个 `CMD` 指令。如果在 Dockerfile 中指定了多个 `CMD` 指令，则只有最后一个指令会生效。

  - ###### 当使用 `docker run` 命令时，可以通过传递参数来覆盖 `CMD` 指令中指定的默认命令和参数。

  - ###### 例如，以下命令将在容器启动时运行 `ls -l` 命令，覆盖 `CMD` 指令中的默认命令

    - `docker run my_image ls -l`

  - ```dockerfile
    # CMD ["executable", "param1", "param2"]
    #
    CMD ["nginx", "-g", "daemon off;"]
    ```

    

##### 基本架構

- ```dockerfile
  # 指定基本環境映像檔
  FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
  
  # 設定工作目錄
  WORKDIR /app
  
  # 複製 .csproj 和 nuget.config 檔案到工作目錄
  COPY *.csproj .
  COPY nuget.config .
  
  # 安裝依賴套件
  RUN dotnet restore
  
  # 複製所有原始程式碼到工作目錄
  COPY . .
  
  # 建置應用程式
  RUN dotnet publish -c Release -o /app/publish /p:EnvironmentName=Production
  
  # 指定運行時映像檔
  FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
  
  # 設定工作目錄
  WORKDIR /app
  
  # 複製已經建置的應用程式到運行時映像檔
  COPY --from=build-env /app/publish .
  
  # 設定環境變數
  ARG MY_ARG = my_value
  ENV ASPNETCORE_ENVIRONMENT=$MY_ARG
  
  # 暴露 5000 端口
  EXPOSE 5000
  
  # 指定要運行的應用程式
  ENTRYPOINT ["dotnet", "YourApplicationName.dll"]
  ```

  

- #### `docker build -t [ImageName]:[Tag] -f .\Dockerfile .   ` 

  - ###### -f: 對應docker file位置

  - ###### 若在cmd 根目錄 則給定`.`


##### 環境變數傳遞

- ##### ARG、ENV

  - ###### 設定IMage內環境變數

  - ##### ENV

    - ```dockerfile
      # 用于设置环境变量，可以在 Dockerfile 中的任何地方使用。
      ENV <key> <value>
      # ex. ENV MY_VAR my_value
      
      FROM ubuntu:latest
      ENV MY_VAR my_value
      RUN echo $MY_VAR
      ```

  - ##### ARG

    - ###### 用於從外部傳遞参数到 Docker 镜像中

      - `docker image build -t my_image:latest --build-arg MY_ARG=new_value .`

    - ###### 只有在建置Dokcer file時才能取得ARG的變數值

    - ```dockerfile
      #<key> 是参数的名称，<default value> 是可选的默认值
      ARG <key>[=<default value>]
      
      #定义一个名为 MY_ARG 的参数，設定預設值為my_value。
      # 使用 ENV 指令设置一个名为 MY_VAR 的环境变量，其值为 MY_ARG 参数的值
      FROM ubuntu:latest
      ARG MY_ARG = my_value
      # 若後續要在容器中使用環境變數，需要再透過ENV儲存
      ENV MY_VAR $MY_ARG
      RUN echo $MY_VAR
      
      #建置Image時可透過 --build-arg取代ARG預設值
      docker image build -t ubuntu:latest -f .\Dockerfile --build-arg MY_ARG=new_value
      ```

      

- ##### 使用以下命令將jekins環境變數傳遞给 Docker 容器

  - ###### `VAR_NAME` 是要传递给 Docker 容器的环境变量的名称，`$VAR_NAME` 是 Jenkins 中定义的该环境变量的值。在 Dockerfile 中，可以使用 `ARG` 指令定义这个环境变量

  - ###### `docker build --build-arg VAR_NAME=$VAR_NAME -t myapp:latest .`

  - ##### 在docker file中加入

    - 使用 `ARG` 指令定義 `VAR_NAME` 的環境變數，使用 `ENV` 指令将其值设置为 Jenkins 中传递的值。

    - 使用 `--build-arg` 参数时，Jenkins 中的环境变量名称和 Dockerfile 中的环境变量名称应该是一致的。

    - ```dockerfile
      ARG VAR_NAME
      ENV VAR_NAME $VAR_NAME
      ```

- ##### 在起Container時指定環境變數

  - ###### -e : 帶入環境變數

  - `docker run -e VAR1=value1 -e VAR2=value2 my_image`

  - ```powershell
    docker run -e ASPNETCORE_ENVIRONMENT=Development -d -p 8088:8088 my_image
    ```

    

- ##### 在 .NET Core中，使用 `Environment.GetEnvironmentVariable()` 方法来获取这些环境变量的值。

  - ```C#
    string varName = Environment.GetEnvironmentVariable("VAR_NAME");
    ```

- #### CMD

##### 

#### Registry

- ##### publish

  - ###### docker hub

  - ###### Quay

- ##### private

- ##### Push

  1. ###### 建置Image檔

     - `docker tag [ImageName]:[Tag]`

  2. ###### 登入docker hub

     - `docker login`

  3. ###### 推上registry

     - `docker push [ImageName]`

#### 從Container Commit 

- ###### `docker commit [Container.ID] [ImageName]:[Tag]`

- ###### ex. docker commit [Container.ID] nginxNew:v1





## Container

### Attach / Detach

- ##### Attach -幕前執行

  - `docker attach {ID}`
  - 讓在背景執行的Container回到前台，要注意退出時可能會使Container也跟著被關閉。
  - 在使用`docker attach`一個Container之後，就會進入這個Container的操作終端命令列，但視你之前執行`docker run`的參數，離開這個Container時，會不會中止Container的執行。

- ##### Detach- 背景執行

  - `docker run -d nginx` 在背景執行

### Exec

- ##### 進入容器內部

  - `docker exec -ti [id] bash`

### 常用指令

```powershell
docker run -d -p 80:80 nginx -- 啟動Container (P:port, d:detached)
docker attach {ID} --轉換Container 為 attach模式

dokcer ps -a --顯示出所有的Container清單(沒寫-a 只會顯示運行中)
docker ps -aq --顯示所有Container ID

docker stop {container ID} --停止Container
docker stop $(docker ps -q) --停止所有Container

docker rm {container ID} --刪除Container
docker rm {ID} -f --強制刪除，即使還在運行中

```



## Volume

## Compose