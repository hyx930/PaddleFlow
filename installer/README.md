# paddleFlow部署
PaddleFLow部署分为客户端和服务端两个部分。
## 1.客户端
### 1.1 环境依赖
- pip3

### 1.2 执行部署/卸载

**安装**paddleflow客户端有两种方式：

1. 从[release](https://github.com/PaddlePaddle/PaddleFlow/releases)下载最新版, 执行`pip3 install PaddleFlow-1.4.2-py3-none-any.whl`
2. 通过编译包安装，编译包有两处来源，分别是分支的最新产出、执行编译命令`cd client && python3 setup.py bdist_wheel`。得到编译包后执行`pip3 install PaddleFlow-1.4.2-py3-none-any.whl`

**卸载**paddleflow

`pip3 uninstall paddleflow`

## 2.服务端
部署组件说明
PaddleFlow运行依赖以下几个组件：</br>
* paddleflow-server
* paddleflow-storage
* volcano

**paddleflow-server**

paddleflow-server提供的功能包括存储资源管理、作业资源调度和工作流编排。这是一个服务入口。详细配置参考文档（TODO）。</br>

**paddleflow-storage**

paddleflow-storage提供的功能主要包括存储资源管理。</br>

**volcano**

本模块基于开源调度器[volcano](https://volcano.sh/zh/docs/architecture/)改造,新增弹性资源队列elastic quota以及映射k8s namespace等能力。若用户已使用原生volcano，请勿执行`2.2`的快速部署,而是在自定义部署中单独执行`server`和`storage`的安装命令.

### 2.1 环境依赖
- kubernetes集群 或 k3s集群
- mysql (可选)

### 2.2 快速部署

```shell
# 执行部署
kubectl create -f https://raw.githubusercontent.com/PaddlePaddle/PaddleFlow/release-0.14.2/installer/paddleflow-deployment.yaml
```
### 2.3 自定义安装
#### 2.3.1 安装paddleflow-server
**指定数据库**
```shell
# paddleflow默认使用SQLite配置
export DB_DRIVER='sqlite'
# 如需切换成mysql，需完善配置如下
export DB_DRIVER='mysql'
export DB_HOST=127.0.0.1
export DB_PORT=3306
export DB_USER=paddleflow
export DB_PW=paddleflow
export DB_DATABASE='paddleflow'
```
**安装paddleflow-server**
```shell
kubectl create -f https://raw.githubusercontent.com/PaddlePaddle/PaddleFlow/release-0.14.2/installer/deploys/paddleflow-server/paddleflow-server-deploy.yaml
```

#### 2.3.2 安装paddleflow-storage
```shell
kubectl create -f https://raw.githubusercontent.com/PaddlePaddle/PaddleFlow/release-0.14.2/installer/deploys/pf-storage/paddleflow-storage-deploy.yaml
```

#### 2.3.3 安装volcano
```shell
kubectl create -f https://raw.githubusercontent.com/PaddlePaddle/PaddleFlow/release-0.14.2/installer/deploys/pf-volcano/pf-volcano-deploy.yaml
```

### 2.4 服务端部署包说明
部署包位于installer下, 包结构如下：

```
.
├── README.md
├── paddleflow-deployment.yaml
├── database
│   ├── README.md
│   ├── paddleflow.sql
│   └── execute.sh
├── deploys
│   ├── paddleflow-server
│   ├── pf-storage
│   └── pf-volcano
└── dockerfile
    ├── README.md
    ├── build_latest.sh
    ├── paddleflow_csiplugin
    │   ├── Dockerfile
    │   └── Dockerfile_base
    └── paddleflow_server
        ├── Dockerfile
        └── Dockerfile_base

3 directories, many files
```
