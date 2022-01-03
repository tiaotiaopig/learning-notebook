# Python的.py与Cython的.pxd.pyx.pyd 文件格式之间的主要区别

## .py 文件

Python 最常用格式就是 .py (另一较常用格式为 .pyw)，由 python.exe 解释，可在控制台下运行。

当然，也可用文本编辑器或其它专用 Python IDE (集成开发环境) 工具进行修改。

常见情形是，用 Python 快速生成程序原型 (有时甚至是程序最终界面)，然后对其中有特别要求的部分，用更合适的语言改写；譬如：3D 游戏中的图形渲染模块，性能要求特别高，就可用 C/C++ 重写，而后封装为 Python 可调用的扩展类库。需要注意的是，在您使用扩展类库时可能需要考虑平台问题，某些扩展类库可能不提供跨平台实现。

此外，还可采用 Cython 为 Python 编写 C 扩展。Cython 是编写、包裹外部 C/C++ 库的胶水代码，将 CPython 嵌入现有应用程序、加速 Python 代码执行的理想 C 模块语言。

![img](https://ask.qcloudimg.com/http-save/yehe-1000017/nkos8wx0q5.jpeg?imageView2/2/w/1620)

## .pxd文件

.pxd 文件是由 Cython 编程语言 "编写" 而成的 Python 扩展模块头文件。

.pxd 文件类似于 C 语言的 .h 头文件，.pxd 文件中有 Cython 模块要包含的 Cython 声明 (或代码段)。

.pxd 文件可共享外部 C 语言声明，也能包含 C 编译器内联函数。.pxd 文件还可为 .pyx 文件模块提供 Cython 接口，以便其它 Cython 模块可使用比 Python 更高效的协议与之进行通信。

可用 cimport 关键字将 .pxd 文件导入 .pyx 模块文件中。

## .pyx文件

.pyx 文件是由 Cython 编程语言 "编写" 而成的 Python 扩展模块源代码文件。

.pyx 文件类似于 C 语言的 .c 源代码文件，.pyx 文件中有 Cython 模块的源代码。

不像 Python 语言可直接解释使用的 .py 文件，.pyx 文件必须先被编译成 .c 文件，再编译成 .pyd (Windows 平台) 或 .so (Linux 平台) 文件，才可作为模块 import 导入使用。

## .pyd文件

.pyd 文件是非 Python，由其它编程语言 "编写-编译" 生成的 Python 扩展模块。

Python 要导入 .pyd 文件，实际上是在 .pyd 文件中封装了一个 module。在 python 中使用时，把它当成 module 来用就可以了，即："import 路径名.modulename" 即可，路径名为 .pyd 文件所在的路径。

基于 Qt/C++ 的 PyQt、PySide GUI (图形用户界面) 开发框架绑定，会为每个 Qt/C++ 库 .dll 文件生成一个中间 .pyd 文件。PyWin32 项目分发包中，也存在很多 MicroSoft Windows OS 相应 .dll 文件的中间 .pyd 文件。

Cython 可将个人基于 Python 语言编写的 Python 模块编译成具有 C 语言特性的 .pyd 文件。