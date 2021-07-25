# JNI 快速入门

JNI的全称就是Java Native Interface，顾名思义，就是Java和C/C++相互通信的接口

## 入门案例(java调用c++)

1. 创建 `JNIDemo.java` 文件

   ```java
   public class JNIDemo {
       //定义一个方法，该方法在C中实现
       public native void sayHelloC(String msg);
       public static void main(String[] args) {
           // 有三种加载方式
           Runtime.getRuntime().loadLibrary("testJNI");
           System.loadLibrary("testJNI");
           // 以上两种方式,都是在 java.library.path下寻找testJNI.so
           // 第三种指定绝对路径
           tring filename;
           String osName = System.getProperty("os.name");
           // 项目根目录(src所在的目录)
           // 或者是jar 运行的目录
           String runDir = System.getProperty("user.dir");
           if (osName.startsWith("Windows")) {
               filename = runDir + "\\KeyNode.dll";
           } else {
               // 路径分隔符还不一样
               filename = runDir + "/KeyNode.so";
           }
           Runtime.getRuntime().load(filename);
           JNIDemo jniDemo = new JNIDemo();
           jniDemo.sayHelloC("你大爷的终于好了");
       }
   }
   ```

2. 使用这个类生成 C++ 头文件

   ```bash
   # 有包名(还要指定编码)
   # 需要在src/main/java下执行,而不是项目根目录
   # java/ 下一级目录就是edu
   javah -encoding utf-8 -classpath . -jni edu.scu.csaserver.utils.KeyNode
   # 没有包名
   # 直接在文件所在目录执行
   javah -encoding utf-8 -classpath . -jni JNIDemo
   ```

   这个头文件就和接口很类似，我们接下来就要使用c++实现这个头文件

3. native代码，编写c++文件

   ```c++
   #include "JNIDemo.h"
   #include <iostream>
   #include <stdio.h>
   
   JNIEXPORT void JNICALL Java_JNIDemo_sayHelloC
   (JNIEnv* env, jobject thix, jstring jstr) {
       // jni string ==> c++ str
       const char *str=env->GetStringUTFChars(jstr,NULL);
       printf("Hello %s", str);
   }
   ```

4. 生成动态链接库文件

   ```bash
   # linux 下编译(是在Linux下运行而不是Windows,我蠢了)
   g++ -shared  KeyNode.cpp -I "/usr/local/develop/jdk1.8.0_251/include" -I "/usr/local/develop/jdk1.8.0_251/include/linux" -o KeyNode.so -fPIC
   # windows 下编译（-I指定头文件路径）
    g++ -shared  testJNI.cpp -I "C:\Program Files\Java\jdk1.8.0_271/include" -I "C:\Program Files\Java\jdk1.8.0_271/include\win32"  -o testJNI.dll
   ```

   **Windows下只能编译.dll,就算命名为.so,使用-fPIC也不行**

   windows下动态连接库文件 ***.dll**，linux下动态链接库文件***.so**

   头文件中需要引入`#include <jni.h>`这个头文件在jdk目录下，也可以不指定，将文件粘贴到当前目录`jni.h和jni_md.h`

   Linux下生成相对应的文件，后缀一般为so。编译指令与windows下类似，只是必须指定参数-fPIC，即“地址无关代码Position Independent Code”

5. 运行Java类`java -cp . com.insight.JNIDemo`,在根目录执行,可以看到输出为cpp文件所定义的方法内容

6. 如果要在C中使用，所有的env->都要被替换成(*env)->，而 且后面的函数中需要增加一个参数env
