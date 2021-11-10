# Kubernetes

容器自动管理工具,持续部署工具

谷歌的开源工具,开源之前在谷歌内部使用了几年时间,管理上千万容器



**安装搭建Kubernetes集群**

安装过程非常复杂,即使对专业运维难度也非常大,有开源工具辅助安装K8s集群

- 全自动安装/一键安装
- 一步步手动安装

**第一台服务器**

1. 克隆centos-8-2105: k1
2. 设置ip

```
./ip-static
ip:192.168.64.191

ifconfig
```

3. kuberasz-3.1.0.zip解压缩
4. 上传文件
   - images.gz上传到/root/
   - kuberasz-3.1.0/ezdown上传到/root/
   - kuberasz-3.1.0/kuberasz文件夹上传到/etc/
5. 设置cpu 内存
6. 重启服务器

**准备安装环境**

```
cd ~/
chmod +x ./ezdown
./ezdown -D
docker info
docker load -i images.gz
docker images
```

克隆k1 ,克隆出k2 k3

- 先关机

- 方案1,从k1克隆出k2和k3
- 方案2,从k1只克隆k2

- 修改k2 k3的ip
  - 192.168.64.192
  - 192.168.64.193

191继续配置安装环境

```shell
./ezdown -S
sed -i 's/^INSTALL_SOURCE.*$/INSTALL_SOURCE: "offline"/g' /etc/kubeasz/example/config.yml 
#生成公钥和私钥
ssh-keygen -t rsa -b 2048 -N '' -f ~/.ssh/id_rsa
ssh-copy-id 192.168.64.191
ssh-copy-id 192.168.64.192
ssh-copy-id 192.168.64.193
#使用离线安装工具
cd /etc/kubeasz
chmod +x ezctl
./ezctl new cs1
#配置服务器地址
vim /etc/kubeasz/clusters/cs1/hosts
#执行一键安装
cd /etc/kubeasz
./ezctl setup cs1 all
#设置 kubectl 命令的别名
# 设置 kubectl 命令别名 k
echo "alias k='kubectl'" >> ~/.bashrc

# 使设置生效
source ~/.bashrc
```

![a](https://raw.githubusercontent.com/zhuzhuking11/LearningNote/main/img/20210617104046405.png)

Kubernetes的三个核心对象

- Pod

  Pod是K8s的容器,是K8s对docker容器的封装对象

  一个Pod可以封装多个docker容器,

  Pod是对底层容器化技术的解耦工具,底层容器可以使用Docker或Containerd

- 控制器

  控制容器的自动部署和自动销毁

- Service

  提供一个不变的访问入口,Service可以向所有容器已负载均衡的方式,转发调用

```shell
cat <<EOF > kubia-manual.yml 
apiVersion: v1               # k8s api版本
kind: Pod                    # 该部署文件用来创建pod资源
metadata:                
  name: kubia-manual         # pod名称前缀,后面会追加随机字符串
spec:
  containers:                # 对pod中容器的配置
  - image: luksa/kubia       # 镜像名
    name: kubia              # 容器名
EOF
```

token:

```
eyJhbGciOiJSUzI1NiIsImtpZCI6Ii1CWWZoemdXTXBycTdOUThqM2NTQ1JvWVpkMzU5RVVUZXBIMFJIREliS00ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLTRuMnpmIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI3MGU3YzZmZi04YzgwLTQ3ZGYtOGI5Yy01NDYyNTM4MGU1ODAiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.yUwLZWFf1LguRcjW-GKgXXmp7PNMEbYBDnyaYBMh-JQbjb-9-wPdE-9LBBYfG_XQZlX0qlg15buNn2aV7yDbkaYeDcvRlQURrqaUpHaMjnUlS0sOBI4qBSflszchPs1S9ZlCMm_qUFKPO3cldoZRw5UnYNLSLxfk1w-75k8KDlmqtFyVpujPHSqpZH8Rv7hC7B5dYujzfPb9dQLeNCytLSI9APajKrsjibaYbr-mEZWDocFR2H_XL4-50JuFpcfmwHVT5YVHBO-NZZJxxB9x2DpZNQ84EE1ELOx0k6dzdBA4keGgVoqj_HS6vBRvzr6vcRWbjy6zNR_gO4IH3U1YSQ
```

## K8s部署springCloud1微服务模块

### 构建镜像

1. 上传文件到/root/
   - DevOps
