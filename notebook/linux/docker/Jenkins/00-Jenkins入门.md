# Jenkins 入门

## Jenkins 安装

Jenkins就是个服务器程序，可以直接运行，提供了后台管理界面

1. docker安装

   ```bash
   docker run \
     -u root \
     -d \
     --name kenkins-blueocean \
     -p 40080:8080 \
     -p 50000:50000 \
     -v jenkins-data:/var/jenkins_home \
     -v /var/run/docker.sock:/var/run/docker.sock \
     jenkinsci/blueocean
   ```

   > **注意：**这里没有指定容器名称，后续操作很不方便，虽然可以通过容器id进行操作。
   >
   > `--name kenkins-blueocean`
   >
   > `--rm`：容器关闭时会自动删除，看需求开不开吧

2. 访问管理界面，`http://localhost:40080`

   初始登录时，需要设置管理员和密码

   初始密码可以通过`docker logs kenkins-blueocean`查看

   或者`docker exec -it kenkins-blueocean bash`，然后运行`cat /var/jenkins_home/...`

## 创建流水线项目

1. 回到Jenkins，如果有必要的话重新登录，点击 **Welcome to Jenkins!** 下方的 **create new jobs**
   **注意:** 如果你无法看见以上内容，点击左上方的 **New Item** 。
2. 在 **Enter an item name** 域中，为新的流水线项目指定名称（例如 `simple-java-maven-app`）。
3. 向下滚动并单击 **Pipeline**，然后单击页面末尾的 **OK** 。
4. （ *可选* ） 在下一页中，在 **Description** 字段中填写流水线的简要描述 （例如 `一个演示如何使用Jenkins构建Maven管理的简单Java应用程序的入门级流水线。`）
5. 点击页面顶部的 **Pipeline** 选项卡，向下滚动到 **Pipeline** 部分。
6. 在 **Definition** 域中，选择 **Pipeline script from SCM** 选项。此选项指示Jenkins从源代码管理（SCM）仓库获取你的流水线， 这里的仓库就是你clone到本地的Git仓库。
7. 在 **SCM** 域中，选择 **Git**。
8. 在 **Repository URL** 域中，填写github的链接（或者远程仓库的链接）
9. 取消勾选轻量级检出
10. 点击 **Save** 保存你的流水线项目。你现在可以开始创建你的 `Jenkinsfile`，这些文件会被添加到你的本地仓库。

## 编写Jenkinsfile

> 一般的原则是，尽量保持你的流水线代码（即 `Jenkinsfile`）越简洁越好，将更复杂的构建步骤放在多个独立的shell脚本中 （尤其对于那些包含2个以上steps的stage）。这最终会使得维护你的流水线代码变得更容易，特别是当你的流水线变得越来越复杂的时候。

```bash
pipeline {
    agent {
    # 需要安装docker-pipeline 插件
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
            # 这里可能文件没权限，需要在pipeline脚本中给权限
            	sh 'chmod 744 ./jenkins/scripts/deliver.sh'
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}
```

如果使用docker部署springboot，应该是要把springboot的端口暴露出来，在创建容器时，做好端口映射。