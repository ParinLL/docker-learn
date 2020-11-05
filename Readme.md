## 此Lab 目標為了解與測試Dockerfile



#### Dockerfile-1  主要查看一般dockerfile 基本元件

#### Dockerfile-builder 認識builder 作用與好處

#### Dockerfile-builder-git 建議結合git 來做為source code版本控管

#### 

##### 說明:

```
$  git clone https://github.com/harryliu123/docker-learn.git
$  cd docker-learn
$  docker build -t <image_name>:<tag> . --no-cache -f <Dockerfile>
$  docker run -it -d -p <你想要開放的port>:8080 <image_name>:<tag>
$  curl http://127.0.0.1:<你想要開放的port>/ping  會出現  Hello World
```



##### 範例:

```
$  git clone https://github.com/harryliu123/docker-learn.git
$  cd docker-learn
$  docker build -t helloword:v0.1 . --no-cache -f Dockerfile-1
$  docker run -it -d -p 8081:8080 helloword:v0.1
$  curl http://127.0.0.1:8081/ping  會出現  Hello World
```



#### Dockerfile-java

##### 範例:

```
$  git clone https://github.com/harryliu123/docker-learn.git
$  cd docker-learn
$  docker build -t helloword:java . --no-cache -f Dockerfile-java
$  docker run -it  helloword:java

```

