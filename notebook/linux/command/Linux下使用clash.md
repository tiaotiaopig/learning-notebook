# Linux下使用clash

## 下载clash

> [github下载地址](https://github.com/Dreamacro/clash/releases)
>
> 选择`clash-linux-amd64.gz`进行下载

## 配置

1. ​	解压

    ```shell
    gunzip clash-linux-amd64-v0.18.0.gz
    mv clash-linux-amd64-v0.18.0.gz clash
    ```

2. 赋予执行权限

    ```shell
    sudo chmod +x clash
    ```

3. 启动程序

    ```shell
    ./clash
    ```

    注:没有图像化界面,在命令行`Ctrl + C`退出程序

    第一次启动创建`~/.config/clash`文件夹

    里面的有两个默认的配置文件(config.yaml,country.mmdb)

4. 替换配置文件

    在Windows端Clash将机场的配置文件导出,命名为config.yaml

    下载新的country.mmdb(链接: https://pan.baidu.com/s/1oEbscsfIB9pKVrCvwYauvw 提取码: rwje)

    在Linux下`~/.config/clash`替换配置文件

5. 开启系统代理

    HTTP 和 HTTPS 代理为 `127.0.0.1:7890`

    Socks 主机为 `127.0.0.1:7891`，即可启用系统代理

    再次启动即可

    注:退出clash后,关闭系统代理(否则无法正常上网)

6. 策略组设置

    启动后:

    [策略组设置]: http://clash.razord.top


## 使用代理

为了方便，防止每次都要输入代理地址，将代理地址设为zsh的环境变量，以后使用时，直接引用

```bash
# 编辑~/.zshrc添加变量
vim ~/.zshrc
PROXY=127.0.0.1:8890

# curl使用代理
curl -x $PROXY https://google.com

# wget使用代理

```

