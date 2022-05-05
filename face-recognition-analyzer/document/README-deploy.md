## [Face Recognition Analyzer](../README.md) - 人脸视频分析器项目
[项目介绍](../README-CN.md) | 部署流程 | [接口文档](./README-api.md)

#### 一、环境准备
* 安装系统
    * [镜像下载](https://developer.nvidia.com/zh-cn/embedded/downloads)
    * [镜像刻录](http://rufus.ie)
#### 二、部署命令
```
# --- 配置容器服务 ---
echo "Setup docker:" \
&& sudo docker network create node_network \
&& sudo systemctl enable docker \
&& sudo systemctl restart docker
```
```
# --- 部署数据库 ---
sudo mkdir -p /home/server/databases && \
sudo docker run \
    -d \
    -e MONGO_INITDB_ROOT_USERNAME=admin \
    -e MONGO_INITDB_ROOT_PASSWORD=admin \
    -e TZ=Asia/Shanghai \
    -v /home/server/databases/mongo1:/var/lib/mongo \
    -p 7030:27017 \
    --network node_network \
    --restart always \
    --name fra-middleware-mongo \
    casperz/fra-middleware-mongo:arm64 && \
sudo docker ps 
```
```
# --- 部署日志数据库 ---
sudo mkdir -p /home/server/databases && \
sudo docker run \
    -d \
    -e TZ=Asia/Shanghai \
    -v /home/server/databases/influxdb1:/var/lib/influxdb \
    -p 7080:8086 \
    --network node_network \
    --restart always \
    --name fra-middleware-influx \
    casperz/fra-middleware-influx:arm64 && \
sudo docker ps 
```
```
# --- 部署缓存数据库 ---
sudo docker run \
    -d \
    -e TZ=Asia/Shanghai \
    -p 7070:6379 \
    --network node_network \
    --restart always \
    --name fra-middleware-redis \
    casperz/fra-middleware-redis:arm64 && \
sudo docker ps 
```
```
# --- 部署算法推理服务 ---
sudo docker run \
    --detach \
    --env TZ=Asia/Shanghai \
    --env LANG=C.UTF-8 \
    --env LC_ALL=C.UTF-8 \
    --network node_network \
    --runtime nvidia \
    --ipc host \
    --shm-size 8g \
    --restart always \
    --name fra-component-predictor \
    casperz/fra-component-predictor:arm64 && \
sudo docker ps
```
```
# --- 部署算法通讯服务 ---
sudo docker run \
    --detach \
    --env TZ=Asia/Shanghai \
    --env LANG=C.UTF-8 \
    --env LC_ALL=C.UTF-8 \
    --publish 8802:5042 \
    --network node_network \
    --runtime nvidia \
    --ipc host \
    --shm-size 8g \
    --restart always \
    --name fra-component-trigger \
    casperz/fra-component-trigger:arm64 && \
sudo docker ps
```
```
# --- 部署接口服务 ---
sudo docker run \
    --detach \
    --env TZ=Asia/Shanghai \
    --publish 8891:8000 \
    --network node_network \
    --restart always \
    --name fra-component-interface \
    casperz/fra-component-interface:arm64 && \
sudo docker ps
```
```
# --- 部署界面服务 ---
sudo docker run \
    --detach \
    --env TZ=Asia/Shanghai \
    --publish 8080:8080 \
    --network node_network \
    --restart always \
    --name fra-component-dashboard \
    casperz/fra-component-dashboard:arm64 && \
sudo docker ps
```
#### 三、配置摄像机
```
import requests

# --- get token ---
service_url = 'http://xxx.xxx.xxx.xxx:8891'
url = f'{service_url}/restful/tokens'
data = {
    'username': 'admin',
    'password': 'admin',
}
response = requests.post(url=url, json=data)
code = response.json().get('code')
token = response.headers.get('authorization')
print(code, token)

# --- test 10001 更新关联摄像机地址 ---
url = f'{service_url}/actions'
data = {
    'tag': 'v3',  # 接口版本
    'code': 10001,  # 接口号
    'camera_rtsp': 'rtsp://admin:admin123@192.168.0.106:554/h264/ch1/main/av_stream',
}
response = requests.post(url=url, json=data, headers={'authorization': token})
print(response.json())
```
#### 四、登录界面
* 登录地址: http://xxx.xxx.xxx.xxx:8080
* 默认用户: admin
* 默认密码: admin