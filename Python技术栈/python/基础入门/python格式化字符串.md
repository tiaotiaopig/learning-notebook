# 格式化字符串

> 格式化字符串天天用,今天小结一下:
>
> ```python
> print('{name:s} is approximately {pia:8.6f}'.format(name='pi', pia=pi))
> ```
>
> 1. 使用字符串的`str.format()`模板字符串里面需要代替的位置使用大括号占位
>
> 2. 大括号里面默认为空,则按照位置关系一一对应,也可以手动指定**索引**或者**参数名**
>
> 3. 例如上面就使用了参数名,冒号后面设置字符串格式:
>
>    `{[name]:[width].[len][type]}`,name 参数名, width 字符宽度,不够长时会补充空格; 小数点后面设置精度, len 表示精确到小数点后第几位, type 表示类型 s 字符串 f 浮点数 d 整数 