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

   