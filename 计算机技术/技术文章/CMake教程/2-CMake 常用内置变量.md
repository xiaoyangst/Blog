CMake变量有很多，下面列举一些常用且可能会用到的变量

再者，CMake变量通过 set 来设置参数

## 基本变量

| 变量名                   | 功能描述                               |
| ------------------------ | -------------------------------------- |
| CMAKE_SOURCE_DIR         | 顶层CMakeLists.txt所在的目录           |
| CMAKE_CURRENT_SOURCE_DIR | 当前处理的CMakeLists.txt文件所在的目录 |
| PROJECT_SOURCE_DIR       | 项目顶层目录                           |
| CMAKE_BINARY_DIR         | 运行cmake命令的目录，即构建目录        |
| CMAKE_CURRENT_BINARY_DIR | 当前处理的CMakeLists.txt文件的构建目录 |
| PROJECT_BINARY_DIR       | 项目的构建目录                         |
| CMAKE_INSTALL_PREFIX     | 指定安装路径                           |

在编译第三方库并导入到一个文件夹中，以后需要用到所需库文件，只需要拷贝这个文件夹到特定项目中即可，CMAKE_INSTALL_PREFIX 就指定此输出文件夹的

## 编译器相关变量

控制和定制编译器的行为

| 变量名                  | 功能描述                          |
| ----------------------- | --------------------------------- |
| CMAKE_C_COMPILER        | C编译器的路径                     |
| CMAKE_C_FLAGS           | 添加到C编译器的标志               |
| CMAKE_C_FLAGS_DEBUG     | 为调试构建配置添加的C编译器标志   |
| CMAKE_C_FLAGS_RELEASE   | 为发布构建配置添加的C编译器标志   |
| CMAKE_CXX_COMPILER      | C++编译器的路径                   |
| CMAKE_CXX_FLAGS         | 添加到C++编译器的标志             |
| CMAKE_CXX_FLAGS_DEBUG   | 为调试构建配置添加的C++编译器标志 |
| CMAKE_CXX_FLAGS_RELEASE | 为发布构建配置添加的C++编译器标志 |

## 构建选项

| 变量名                 | 功能描述                                                 |
| ---------------------- | -------------------------------------------------------- |
| CMAKE_BUILD_TYPE       | 构建类型（如Debug、Release、RelWithDebInfo、MinSizeRel） |
| CMAKE_VERBOSE_MAKEFILE | 设置为ON时，生成详细的Makefile                           |

## 链接器相关变量

| 变量名                    | 功能描述                           |
| ------------------------- | ---------------------------------- |
| CMAKE_EXE_LINKER_FLAGS    | 为生成可执行文件传递的额外链接标志 |
| CMAKE_SHARED_LINKER_FLAGS | 为生成共享库传递的额外链接标志     |
| CMAKE_MODULE_LINKER_FLAGS | 为生成模块库传递的额外链接标志     |

## 安装相关变量

| 变量名                   | 功能描述               |
| ------------------------ | ---------------------- |
| CMAKE_INSTALL_PREFIX     | 安装路径的前缀         |
| CMAKE_INSTALL_BINDIR     | 安装可执行文件的子目录 |
| CMAKE_INSTALL_LIBDIR     | 安装库文件的子目录     |
| CMAKE_INSTALL_INCLUDEDIR | 安装头文件的子目录     |

如果我们的项目是设计可打包的库文件（动态库或静态库），在CMake中配置好这些参数，非常有利于使用者导入我们的库文件

通常情况下bin目录存放动态库和可执行文件，lib目录存放静态库

## 查找库和包

| 变量名             | 功能描述                     |
| ------------------ | ---------------------------- |
| CMAKE_PREFIX_PATH  | 用于指定查找包和库的前缀路径 |
| CMAKE_MODULE_PATH  | 用于指定查找CMake模块的路径  |
| CMAKE_LIBRARY_PATH | 用于指定查找库的路径         |
| CMAKE_INCLUDE_PATH | 用于指定查找头文件的路径     |

这些变量名往往要结合其它方法才能实际找到第三方库，毕竟动作不是变量来完成的

