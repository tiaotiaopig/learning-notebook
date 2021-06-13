# npm 依赖包的安装、更新、删除

## 安装

安装但不写入package.json

```undefined
npm install xxx
```

安装并写入package.json的"dependencies"中

```undefined
npm install xxx –S 
```

安装并写入package.json的"devDependencies"中

```undefined
npm install xxx –D
```

全局安装

```undefined
npm install xxx -g
```

安装指定版本

```css
npm install xxx@1.2.0
```

## 更新

先检查更新

```undefined
npm outdated
```

执行以上命令，可以看到所有可以更新的模块。
我们需要先更新 **package.json**文件：
安装"npm-check-updates"模块

```undefined
npm install -g npm-check-updates
```

检查可更新的模块

```undefined
ncu
```

```undefined
npm-check-updates
```

以上两条命令都可检查可更新模块。接下来更新package.json的依赖包到最新版本：

```undefined
ncu -u
```

以上命令执行，更新全部模块。但在实际开发中不建议一次全部更新，可以根据实际需要，更新指定的模块，并且可以根据作用范围在后面加上 -D、-S 或 -g

```undefined
 npm update xxx
```

注意：指定更新需要提前修改 package.json 中的版本号。

为了保险起见，package.json 更新后，可删除整个node_modules目录并重新初始化项目。

```undefined
npm install
```

## 删除

删除指定模块；

```undefined
npm uninstall xxx 
```

删除全局模块

```undefined
npm uninstall -g xxx
```

## 查看

查看全局安装的包

```cpp
npm list -g --depth 0
```

