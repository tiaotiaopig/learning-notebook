# git 代码管理规范

## 分支命名

### master 分支

master 为主分支，也是用于部署生产环境的分支，需要确保master分支稳定性。master 分支一般由 release 以及 hotfix 分支合并，任何时间都不能直接修改代码。

### develop 分支

develop 为开发环境分支，始终保持最新完成以及bug修复后的代码，用于前后端联调。一般开发的新功能时，feature分支都是基于develop分支创建的。

### feature 分支

开发新功能时，以develop为基础创建feature分支。

分支命名时以 `feature/` 开头，后面可以加上开发的功能模块， 命名示例：`feature/user_module`、`feature/cart_module`

### test分支

test为测试环境分支，外部用户无法访问，专门给测试人员使用，版本相对稳定。

### release分支

release 为预上线分支（预发布分支），UAT测试阶段使用。一般由 test 或 hotfix 分支合并，不建议直接在 release 分支上直接修改代码。

### hotfix 分支

线上出现紧急问题时，需要及时修复，以master分支为基线，创建hotfix分支。修复完成后，需要合并到 master 分支和 develop 分支。

分支命名以`hotfix/` 开头的为修复分支，它的命名规则与 feature 分支类似。

## 分支与环境对应关系

在系统开发过程中常用的环境：

- DEV 环境（Development environment）：用于开发者调试使用
- FAT环境（Feature Acceptance Test environment）：功能验收测试环境，用于测试环境下的软件测试者测试使用
- UAT环境 （User Acceptance Test environment）：用户验收测试环境，用于生产环境下的软件测试者测试使用
- PRO 环境（Production environment）：生产环境

对应关系：

| 分支    | 功能                      | 环境 | 可访问 |
| :------ | :------------------------ | :--- | :----- |
| master  | 主分支，稳定版本          | PRO  | 是     |
| develop | 开发分支，最新版本        | DEV  | 是     |
| feature | 开发分支，实现新特性      |      | 否     |
| test    | 测试分支，功能测试        | FAT  | 是     |
| release | 预上线分支，发布新版本    | UAT  | 是     |
| hotfix  | 紧急修复分支，修复线上bug |      | 否     |

### 分支合并流程规范

业界常见的两大主分支（master、develop）、三个辅助分支（feature、release、hotfix）的生命周期：

![图片](../.image/git%E4%BB%A3%E7%A0%81%E7%AE%A1%E7%90%86%E8%A7%84%E8%8C%83/640.png)

以上生命周期仅作参考，不同开发团队可能有不同的规范，可自行灵活定义。

例如我们团队在开发时，至少需要保证以下流程：

- develop 分支和 hotfix 分支，必须从 master 分支检出
- 由 develop 分支合并到 test 分支
- 功能测试无误后，由 test 分支合并到 release 分支
- UAT测试通过后，由 release 分支合并到 master分支
- 对于工作量小的功能开发（工时小于1天），可以直接在devolop 分支进行开发，否则由 develop 分支检出 feature 分支进行开发，开发完后合并到develop 分支

## Git Commit Message规范

Git commit message规范指提交代码时编写的规范注释，编写良好的Commit messages可以达到3个重要的目的：

- 加快代码review的流程
- 帮助我们编写良好的版本发布日志
- 让之后的维护者了解代码里出现特定变化和feature被添加的原因

### Angular Git Commit Guidelines

业界应用的比较广泛的是Angular Git Commit Guidelines：

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

- type：提交类型
- scope：可选项，本次 commit 波及的范围
- subject：简明扼要的阐述下本次 commit 的主旨，在`Angular Git Commit Guidelines`中强调了三点。使用祈使句，首字母不要大写，结尾无需添加标点
- body: 同样使用祈使句，在主体内容中我们需要把本次 commit 详细的描述一下，比如此次变更的动机
- footer: 描述下与之关联的 issue 或 break change

### 简易版

项目中实际可以采用简易版规范：

```
<type>(<scope>):<subject>
```

### type规范

`Angular Git Commit Guidelines`中推荐的type类型如下：

- feat: 新增功能
- fix: 修复bug
- docs: 仅文档更改
- style: 不影响代码含义的更改（空白、格式设置、缺失 分号等）
- refactor: 既不修复bug也不添加特性的代码更改
- perf: 改进性能的代码更改
- test: 添加缺少的测试或更正现有测试
- chore: 对构建过程或辅助工具和库（如文档）的更改

除此之外，还有一些常用的类型：

- delete：删除功能或文件
- modify：修改功能
- build：改变构建流程，新增依赖库、工具等（例如webpack、gulp、npm修改）
- test：测试用例的新增、修改
- ci：自动化流程配置修改
- revert：回滚到上一个版本

### 单次提交注意事项

- 提交问题必须为同一类别
- 提交问题不要超过3个
- 提交的commit发现不符合规范，`git commit --amend -m "新的提交信息"`或 `git reset --hard HEAD` 重新提交一次

## 配置.gitignore文件

`.gitignore`是一份用于忽略不必提交的文件的列表，项目中可以根据实际需求统一`.gitignore`文件，减少不必要的文件提交和冲突，净化代码库环境。

通用文件示例：

```
HELP.md
target/
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**/target/
!**/src/test/**/target/

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/
build/
!**/src/main/**/build/
!**/src/test/**/build/

### VS Code ###
.vscode/

# Log file
*.log
/logs*

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.ear
*.zip
*.tar.gz
*.rar
*.cmd
```

## 其他

此外，还有一些其他建议：

- master 分支的每一次更新，都建议打 tag 添加标签，通常为对应版本号，便于管理
- feature分支、hotfix分支在合并后可以删除，避免分支过多管理混乱
- 每次 pull 代码前，提交本地代码到本地库中，否则可能回出现合并代码出错，导致代码丢失