### Cmake定义

一款高级编译配置工具，所有操作通过编译Cmakelists.txt来完成，使用目的是为了处理大型C/C++/Java项目。

### Cmake语法介绍

#### project关键字

可以用来指定工程名字和支持的语言，默认支持所有语言

如：project(hello)，指定了工程的名字，并支持所有语言。

隐式定义了两个变量：project_binary_dir, project_source_dir

#### set关键字

用来显示指定变量：set(src_list main.cpp)

#### message关键字

像终端用户输出自定义信息

主要包含三种：

+ send_error，产生错误，生成过程被跳过
+ status，输出前缀为—的信息
+ fatal_error，立即终止所有cmake进程

#### add_executable关键字

生成可执行文件

如：add_executable(hello src_list)：生成可执行文件名为hello，源文件读取变量src_list中的内容

### 语法基本原则

+ 变量使用${}方式取值，if控制语句中直接使用变量名
+ 指令(参数1 参数2)：参数使用括号括起来，使用空格或分号隔开
+ 指令大小写无关

 ### 内部构建和外部构建

外部构建就是新建一个build目录，生成的文件全部放在build目录下，与源文件隔开。

### 工程文件目录

+ 子目录src，放置工程源代码
+ 子目录doc，放置工程文档
+ 其它文本文件，如copyright、readme
+ runproject.sh脚本文件，用来调用二进制
+ 构建后的目标文件放入bin子目录
+ doc目录内容以及其他文本文件放入/usr/share/doc/cmake/

### add_subdirectory指令

+ 用于向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置
+ add_subdirectory(src bin)：将src子目录加入工程并指定编译输出路径为bin目录

### Cmake安装指令install

CmakeLists.txt文件编写：

+ install(files copyright readme destination /share/doc/cmake/)
+ install(programs runproject.sh destination bin)
+ install(directory doc/ destination /share/doc/cmake)

安装指令：make install

### 编译指令

+ cmake ..
+ make

### 静态库和动态库

#### 静态库和动态库的区别

+ 静态库扩展名.a/.lib，动态库扩展名.so/.dll
+ 静态库在编译时直接整合到目标程序中，编译成功的可执行文件可独立运行
+ 动态库在编译时不会放到连接的目标程序中，可执行文件无法独立运行

#### add_libary

add_libary(project shared ${libpro_src})

+ project：库名，生成的名字前面会加lib，最终生成libproject.so
+ shared：动态库；static：静态库
+ ${libpro_src}：源文件

#### 同时构建静态库和动态库

set_target_properties

