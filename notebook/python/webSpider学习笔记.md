# WebSpider学习笔记
---
## 简单入门

1. F12 或者 选择元素右键“检查”，查看html源码

2. 第一步，根据url,获取网页的HTML信息，主要使用两个库
   + **urllib.request** Python3内置库
   + **requests** 库是第三方库，需要我们自己安装，功能强大
   
3. 我们主要使用`requests.get(url)`方法，获取服务器返回内容，返回response对象

   + ` r = requests.post('http://httpbin.org/post', data = {'key':'value'})`

   + ```python
     import requests
     r = requests.put('http://httpbin.org/put', data = {'key':'value'})
     r = requests.delete('http://httpbin.org/delete')
     r = requests.head('http://httpbin.org/get')
     r = requests.options('http://httpbin.org/get')
     ```

4. 从response中获取响应内容，简单的，我们使用`response.text`,它会根据http响应头的编码信息，推测文本的编码

   我们也能够通过`response.encoding`来查看和改变text的编码方式

5. 你可能希望在使用特殊逻辑计算出文本的编码的情况下来修改编码。比如 HTTP 和 XML 自身可以指定编码。

   这样的话，你应该使用 `r.content` 来找到编码，然后设置 `r.encoding` 为相应的编码。这样就能使用正确的编码解析 `r.text` 了

   `response.content` 返回的是字节形式的响应

6. 解析html信息，从中提取出我们需要的内容，例如使用正则表达式、Xpath、Beautiful Soup，安装`pip install beautifulsoup4`

7. BeautifulSoup推荐使用的解析器是 `lxml`，我们需要在构造函数的`feature="lxml"`中指定

8. ```python
   from bs4 import BeautifulSoup
   soup = BeautifulSoup(html_doc, 'html.parser')
   print(soup.prettify())
   ```

9. 我们主要使用BeautifulSoup的 两个方法

   + `find(name,attrs,recursive,string,**kwargs)` 直接返回对应标签的结果（如果结果唯一用这个）
   + `find_all(name,attrs,recursive,string,**kwargs)`返回的是结果的列表
     + name 参数可以查找所有名字为 `name` 的tag
     + attrs 指定标签的属性，例如 id herf ，如果按照css搜索，需要指定`class_=`为对应的css类选择器
     + 然后我们使用`text`属性，过滤掉所有的标签内容，获取我们想要的结果

10. 使用多线程进行加速

    ```python
        from concurrent.futures import ThreadPoolExecutor
        with ThreadPoolExecutor(max_workers=10) as pool:
            for id_chapter in range(sfn.size):
                pool.submit(sfn.download_novel, "狩魔手记" + str(id_chapter) + ".txt", id_chapter)
    ```

    + 应该把多线程执行的任务封装成一个独立的方法，然后传给线程池的是方法名（方法的地址）
    + 否则就是把方法的返回值作为方法地址提交给线程池，方法的参数不应该是其他方法的返回值

