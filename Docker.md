# Docker 說明文件

##  Image

- Docker 映像檔就是一個唯讀的模板
- 例如：一個映像檔可以包含一個完整的 ubuntu 作業系統環境，裡面僅安裝了 Apache 或使用者需要的其它應用程式。
- 映像檔可以用來建立 Docker 容器。
- Docker 提供了一個很簡單的機制來建立映像檔或者更新現有的映像檔，使用者甚至可以直接從其他人那裡下載一個已經做好的映像檔來直接使用。



### List

```powershell
docker images
docker image ls
```



### Build

```powershell
# ImageName 需小寫
# 須注意 Dockerfile 的路徑
docker build -t [ImageName]:[Tag] -f [Dockerfile Name] .

#1. 準備好DockerFile
#2. 建立image
    # 啟動 Powershell 或 cmd
    # 移至專案 Dockerfile 所在位置的上層資料夾
    # 執行 Docker build 指令, 建立 Image, 參考指令如下
    # -t: Image 名稱及 Tag
    # -f: Dockerfile 檔案路徑
 注意：確保在名稱後面放置一個空格和句點 - 很容易錯過！
 
 docker build -t icw_biosecurity_api:latest -f .\ICW.BioSecurity.Api\Dockerfile .
  
#OR直接只到DockerFile那層

docker build -t icw_biosecurity_api:latest .

```

### Remove

```powershell
docker rmi [ImageID]
docker image rm [ImageID]
docker rmi [ImageName]:[Tag]

# remove all images
docker rmi $(docker images -q) -f

# remove all none images (None Image 是在 build 過程中產生)
docker rmi $(docker images -f "dangling=true" -q)
```

## Container

- Docker 利用容器來執行應用

- 容器是從映像檔建立的執行實例。它可以被啟動、開始、停止、刪除。每個容器都是相互隔離的、保證安全的平台。

- 可以把容器看做是一個簡易版的 Linux 環境（包括root使用者權限、程式空間、使用者空間和網路空間等）和在其中執行的應用程式。

- 映像檔是唯讀的，容器在啟動的時候建立一層可寫層作為最上層

  

### List

```powershell
docker container ls
# show all container (includes stopped container)
docker container ls -all
```

### Stop

```powershell
docker stop $(docker ps -a -q)

docker stop <CONTAINER ID>
```

### Remove

```powershell
# remove all stopped container
docker container prune
# remove container by container id
docker container rm [ContainerID]
# remove ALL
docker rm $(docker ps -a -q)
```

### Run

```powershell
# -it: 啟動互動式容器
# --rm: exit 後刪除 container
# -p: Port 對應 [外部 IP]:[內部 IP] (可設立多個 mapping)
# -d: 背景執行
docker run --name [容器名稱] -p [外部 ip]: [內部 ip] -d --rm [RepositoryName]:[Tag]
docker run -p 80:80 --rm -d --name test icw_dev_idsvr

docker run -d -p 8888:80 --name biosecurity_api icw_biosecurity_api:latest
```

### Start

```powershell
docker start icw_cloud_mysql
```

### IP

```powershell
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [ContainerName or ContainerID]
```

### Copy

```powershell
docker cp icw_cloud:/etc/nginx  D:\idsvr4\tmp

docker cp  D:\idsvr4\tmp\nginx icw_cloud:/etc/
```

### Exec

```
docker exec -it [ContainerID] /bin/sh
```

### Remove

```powershell
# remove all stopped container
docker container prune
# remove container by container id
docker container rm [ContainerID]
```

### Run

```powershell
# -it: 啟動互動式容器
# --rm: exit 後刪除 container
# -p: Port 對應 [外部 IP]:[內部 IP] (可設立多個 mapping)
# -d: 背景執行
docker run --name [容器名稱] -p [外部 ip]: [內部 ip] -d --rm [RepositoryName]:[Tag]
docker run -p 80:80 --rm -d --name test icw_dev_idsvr
```

### IP

```powershell
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [ContainerName or ContainerID]
```

Exec

```
docker exec -it [ContainerID] /bin/sh
```



## Docker Hub

- 倉庫是集中存放映像檔檔案的場所。有時候會把倉庫和倉庫註冊伺服器（Registry）混為一談，並不嚴格區分。實際上，倉庫註冊伺服器上往往存放著多個倉庫，每個倉庫中又包含了多個映像檔，每個映像檔有不同的標籤（tag）。
- 倉庫分為公開倉庫（Public）和私有倉庫（Private）兩種形式
- 當使用者建立了自己的映像檔之後就可以使用 `push` 命令將它上傳到公有或者私有倉庫，這樣下次在另外一台機器上使用這個映像檔時候，只需要從倉庫上 `pull` 下來就可以了。
- Docker 倉庫的概念跟 [Git](http://git-scm.com/) 類似，註冊伺服器可以理解為 GitHub 這樣的託管服務。

```powershell
docker container ls (列出所有存活的容器)

docker container ls --all (列出全部容器)
```



## 微服務佈署流程說明

### 	1. 製作 Dockerfile 檔案

  * #### .net core

    ```powershell
    # Get Base Image (Full .NET Core SDK)
    FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build-env
    WORKDIR /app
    
    # Copy csproj and restore
    COPY *.csproj ./
    RUN dotnet restore
    
    # Copy everything else and build
    COPY . ./
    RUN dotnet publish -c Release -o out
    
    # Generate runtime image
    FROM mcr.microsoft.com/dotnet/core/aspnet:2.2
    WORKDIR /app
    EXPOSE 80
    COPY --from=build-env /app/out .
    ENTRYPOINT ["dotnet", "ICW.BioSecurity.Api.dll"]
    ```

  * #### Vue.js

```javascript
# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN yarn install
COPY . .
RUN yarn build

# production stage
FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
COPY default.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- default .conf是 vue佈署在Docker上面的時候因為 router history-Mode再 `nginx` 導頁會產生錯誤的解決方式

```javascript
server {
    listen       80;
    server_name  localhost;

  root   /usr/share/nginx/html;

  index index.html;


  location / {
    try_files $uri $uri/ @rewrites;
  }

  location @rewrites {
    rewrite ^(.+)$ /index.html last;
  }

  location ~* \.(?:ico|css|js|gif|jpe?g|png)$ {
    # Some basic cache-control for static files to be sent to the browser
    expires max;
    add_header Pragma public;
    add_header Cache-Control "public, must-revalidate, proxy-revalidate";
  }

}
```

### 2. 建立 Image檔案

```powershell
docker build -t icw_cloud:latest .
```

### 3. 建立Container

```powershell
docker run -d -p  80:80 --name icw_cloud icw_cloud
```



  	# build stage

  	FROM node:lts-alpine as build-stage
  	WORKDIR /app
  	COPY package*.json ./
  	RUN yarn install
  	COPY . .
  	RUN yarn build
  	
  	# production stage
  	FROM nginx:stable-alpine as production-stage
  	COPY --from=build-stage /app/dist /usr/share/nginx/html
  	EXPOSE 80
  	CMD ["nginx", "-g", "daemon off;"]
  	```

2. 製作 Image

  ```powershell
  # 啟動 Powershell 或 cmd
  # 移至專案 Dockerfile 所在位置的上層資料夾
  # 執行 Docker build 指令, 建立 Image, 參考指令如下
  # -t: Image 名稱及 Tag
  # -f: Dockerfile 檔案路徑
  
  docker build -t icw_developer_identityserver:latest -f .\ICW.Developer.IdentityServer\Dockerfile .
  # example
  # docker build -t icw_dev_id4svr:v1.0.19060301 -f .\ICW.Developer.IdentityServer\Dockerfile .
  ```
3. Push 至私人註冊所

	```powershell
	docker tag [ImageName] [DockerUserID]/[ImageName]:[Tag]
	# example
	# docker tag icw_dev_id4svr:v1.0.19060301 192.168.1.102:5000/icw_dev_id4svr:v1.0.19060301
	
	docker push [ImageName]
	# example
	# docker push 192.168.1.102:5000/icw_dev_id4svr:v1.0.19060301
	```

4. 至遠端主機啟動 container

	```powershell
	# 方法一. 使用 docker run
	docker pull [ImageName]
	docker run --name [容器名稱] -p [外部 ip]: [內部 ip] -d --rm [RepositoryName]:[Tag]
	
	# 方法二. 使用 Docker-compose
	docker-compose up -d
	```

	

## MySql 安裝

```powershell
docker search mysql

docker pull mysql
# docker run --name [MySql 名稱] -e MYSQL_ROOT_PASSWORD=my-password -d -p 3306:3306 mysql
# MYSQL_ROOT_PASSWORD = root 的密碼
docker run --name mysql -e MYSQL_ROOT_PASSWORD=root -d -p 3306:3306 mysql
```

## Private registry 安裝

### crceate private registry

```powershell
# 下載 registry image
docker pull registry
# 建立 container
# ex: docker run --name [私人註冊庫名稱] -p [外部 IP]:[內部 IP] -v [實體檔案路徑]:[Container 路徑]
docker run --name icw-registry --restart always -p 5000:5000 -v D:/docker/registry:/var/lib/registry -d registry

# 建立 WebUI 管理介面
# docker run -d -p 8080:8080 --name registry-web --link [Name] -e REGISTRY_URL=http://[IP]:5000/v2 hyper/docker-registry-web
docker run -d -p 8080:8080 --name registry-web --link icw-registry -e REGISTRY_URL=http://192.168.1.177:5000/v2 hyper/docker-registry-web

# 需開啟防火牆 Port: 5000, 8080
```

### 本機(開發)端設定 (錯誤訊息: server gave HTTP response to HTTPS client)

* docker config 設定
	* 進入 Docker 的 settings. 選擇 Daemon.
	* 於 Insecure registries 新增 registry 的 ip:port
* restart docker

## 常用指令說明

### Container



### Image

#### List

```powershell
docker images
docker image ls
```

#### Build

```powershell
# ImageName 需小寫
# 須注意 Dockerfile 的路徑
docker build -t [ImageName]:[Tag] -f [Dockerfile Name] .
```

#### Remove

```powershell
docker rmi [ImageID]
docker image rm [ImageID]
docker rmi [ImageName]:[Tag]

# remove all images
docker rmi $(docker images -q) -f

# remove all none images (None Image 是在 build 過程中產生)
docker rmi $(docker images -f "dangling=true" -q)
```

#### Pull

```powershell
docker pull [ImageName]:[Tag]
```

#### Push

```powershell
# login to docker hub (如果要 push 到 DockerHub, 若是 Private docker registry 則不需 login)
docker login
# 確認 image 的前置詞需是自己的 docker user id (若是 Private docker registry 則需定義 domain ip 以及 Port)
# ex. docker tag icw_developer 192.168.1.102/icw_developer_identityserver
docker tag [ImageName] [DockerUserID]/[ImageName]:[Tag]
# push image to docker hub
docker push [ImageName]
```

#### Search

```powershell
# 搜尋 Image
docker search [ImageName]
```

### Docker-Compose

#### up (Startup)

```powershell
# 啟動 docker container (移動至 docker-compose 檔案目錄)
docker-compose up -d
```

stop

```powershell
# 停止 docker container (移動至 docker-compose 檔案目錄)
docker-compose stop
```


