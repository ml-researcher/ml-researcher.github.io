查看ubuntu版本：lsb_release -a

# docker

## docker run

* 基本格式：docker run -options image_name command
* -it：交互（是进行attach detach的必要条件）
* -d：后台运行，通过docker logs -f container_id查看输出
* --name：为容器命名
* --rm：退出以后不保存在ps里

## docker ps

查看运行的container，或者docker container ls（docker images查看镜像列表）

docker stop container_id停止，start启动（启动仍然保留原本状态）

docker ps -a

## docker pull

docker pull ubuntu:20.04

## 与容器的交互

* docker attach cid
* ctrl+p, ctrl+q -> detach
* docker exec -it cid command

## 容器的导入导出

* docker export 1e560fca3906 > ubuntu.tar
* cat docker/ubuntu.tar | docker import - test/ubuntu:v1
* docker import http://example.com/exampleimage.tgz example/imagerepo

## docker rm

docker rm 1e560fca3906

docker rmi image_name

## 创建镜像

方法1：使用docker build结合Dockerfile

方法2：基于现有镜像export import

## 其他常用选项

### 端口映射

* -P：将容器的端口随机映射到主机
* -p p_docker:p_host

docker port cid查看端口映射

### 显卡共享

docker run --gpus all

docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864

cuda driver需要依赖于host。但是好像不用驱动也可以运行最新版cuda的python包。

### 文件共享

docker run -v path_host:path_docker

### docker使用代理

这里区分一下docker pull使用代理和docker container使用代理

* https://docs.docker.com/config/daemon/systemd/#httphttps-proxy
* https://docs.docker.com/network/proxy/


