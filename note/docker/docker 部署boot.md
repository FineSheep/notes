# docker 部署boot

1. pom文件中需要，否则无法-jar启动

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

2. Dockerfile

```dockerfile
# 该镜像需要依赖的基础镜像
FROM java:8
# 将当前目录下的jar包复制到docker容器的/目录下
ADD test_docker-1.0-SNAPSHOT.jar /test_docker-1.0-SNAPSHOT.jar
# 运行过程中创建一个mall-tiny-docker-file.jar文件
RUN bash -c 'touch /test_docker-1.0-SNAPSHOT.jar'
# 声明服务运行在8080端口
EXPOSE 8080
# 指定docker容器启动时运行jar包
ENTRYPOINT ["java", "-jar","/test_docker-1.0-SNAPSHOT.jar"]
# 指定维护者的名字
MAINTAINER yang
```

3. mvn打包
4. 上传jar包以及dockerfile（同一目录）

![image-20230509160840846](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20230509160840846.png)

5. 构建镜像

```bash
docker build -t test:latest .
```

6. 启动容器

```bash
 docker run -p 8080:8080 --name test -d test
```

