# Spring Boot打jar包

如图所示，使用maven 插件，先 **clean** 后 **package**，在**LifeCycle**中

![image-20210630192306489](https://i.loli.net/2021/06/30/jxnuR4bs93cyBNp.png)

Maven pom 设置如下

```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                # 自己添加的，作用有待验证
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
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

