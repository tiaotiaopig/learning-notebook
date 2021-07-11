# JNI 快速入门

JNI的全称就是Java Native Interface，顾名思义，就是Java和C/C++相互通信的接口

## 入门案例(java调用c++)

1. 创建 `JNIDemo.java` 文件

   ```java
   public class JNIDemo {
       //定义一个方法，该方法在C中实现
       public native void sayHelloC(String msg);
       public static void main(String[] args) {
           Runtime.getRuntime().loadLibrary("testJNI");
           JNIDemo jniDemo = new JNIDemo();
           jniDemo.sayHelloC("你大爷的终于好了");
       }
   }
   ```

2. 使用这个类生成 C 头文件

   ```bash
   # 有包名(还要指定编码)
   javah -encoding utf-8 -classpath . -jni com.insight.test.JNIDemo
   # 没有包名
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
   # linux 下编译
   g++ -shared  testJNI.cpp -o testJNI.so -fPIC
   # windows 下编译（-I指定头文件路径）
    g++ -shared  testJNI.cpp -I "C:\Program Files\Java\jdk1.8.0_271/include" -I "C:\Program Files\Java\jdk1.8.0_271/include\win32"  -o testJNI.dll
   ```

   windows下动态连接库文件 ***.dll**，linux下动态链接库文件***.so**

   头文件中需要引入`#include <jni.h>`这个头文件在jdk目录下，也可以不指定，将文件粘贴到当前目录`jni.h和jni_md.h`

   Linux下生成相对应的文件，后缀一般为so。编译指令与windows下类似，只是必须指定参数-fPIC，即“地址无关代码Position Independent Code”

5. 运行Java类`java -cp . com.insight.JNIDemo`,在根目录执行,可以看到输出为cpp文件所定义的方法内容
