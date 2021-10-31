# jupyter 远程服务

## 启用安全服务

> 1. 生成配置文件
>
>    ```bash
>    jupyter notebook --generate-config
>    ```
>
>    文件默认在
>
>    - Windows: `C:\Users\USERNAME\.jupyter\jupyter_notebook_config.py`
>    - OS X: `/Users/USERNAME/.jupyter/jupyter_notebook_config.py`
>    - Linux: `/home/USERNAME/.jupyter/jupyter_notebook_config.py`
>
> 2. 设置密码
>
>    ```bash
>    jupyter notebook password
>    ```
>
> 3. SSL 加密
>
>    ```bash
>    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mykey.key -out mycert.pem
>    ```
>
>    生成自己的证书，没有CA颁发，会被浏览器提示不安全，不过自己用没啥事，强迫症就从CA那搞一个
>
> 4. 修改配置文件
>
>    ```bash
>    cd ~/.jupyter
>    vim jupyter_notebook_config.py
>    ```
>
>    内容如下：
>
>    ```python
>    # Set options for certfile, ip, password, and toggle off
>    # browser auto-opening
>    c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'
>    c.NotebookApp.keyfile = u'/absolute/path/to/your/certificate/mykey.key'
>    # Set ip to '*' to bind on all interfaces (ips) for the public server
>    c.NotebookApp.ip = '*'
>    c.NotebookApp.password = u'sha1:bcd259ccf...<your hashed password here>'
>    c.NotebookApp.open_browser = False
>    
>    # It is a good idea to set a known, fixed port for server access
>    c.NotebookApp.port = 9999
>    ```
>
>    vim 命令模式下，`/关键字`搜索
>
> 5. 启动jupyter服务
>
>    ```bash
>    jupyter notebook
>    ```

