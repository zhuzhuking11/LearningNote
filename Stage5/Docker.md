# Docker

**准备docker服务器**

1. 克隆docker-base: docker

2. 设置ip

   ```shell
   ./ip-static
   ip: 192.168.64.150
   ifconfig
   ```

3. 上传到root

   - docker-image.gz
   - tomcat文件夹

4. 导入镜像

   ```shell
   docker load -i docker-images.gz
   docker images
   ```

   

用tomcat:10运行helloworld web应用

```shell
1.准备jsp文件
mkdir /opt/web
vim /opt/web/index.jsp
添加内容
<h1>Hello World</h1>
<h1>Hello ${param.name}!</h1>

2.启动tomcat:10容器
docker run -d --name web --restart=always -p 80:8080 -v /opt/web:/usr/local/apache-tomcat-10.0.6/webapps/ROOT tomcat:10
```

