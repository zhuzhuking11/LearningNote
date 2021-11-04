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



# Spring Data Elasticsearch

### Criteria数据查询

- Criteria 封装搜索条件
- CriteriaQuery 封装上面的条件对象,分页参数
- ElasticsearchOperations 用来执行查询的工具

**分页**

- Pageable-向服务器提交的分页参数.page,size
- Page - 从服务器返回的一页数据,数据列表,分页信息(可选,可以直接使用List<...>)



- Repository

  ```java
  Page<Student> findByName(String key,Pageable pageable);
  ```

- Criteria

  ```java
  Criteria c= ...
  CriteriaQuery q=new CriteriaQuery(c);
  q.setPageable(pageable);
  ...
  ```

  

### 拼多商城商品搜索

1. 添加spring data es依赖
2. yml添加es服务器地址
3. 新建实体类Item,封装从es搜索的数据
4. 新建ItemRepository接口
5. 添加搜索方法: `findByTitleOrSellPoint()`
6. SearchService
7. SearchController
8. search.jsp
