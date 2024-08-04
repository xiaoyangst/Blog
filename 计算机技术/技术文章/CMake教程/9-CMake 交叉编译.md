之前学习过交叉编译，但一直缺乏实践，因为我也不接触嵌入式开发。就在昨天和网络上一个朋友解决CMake相关的问题，恰好就有机会实践交叉编译

如果你在 Linux 系统上，你希望编译出的可执行程序在 Arm 系统上执行，这就需要交叉编译

整个可执行程序链接的库也必须是在 Arm 系统上的，不要编译出 Linux 系统上的库，这是不要弄混的，因为你最终要在 Arm 系统执行你的程序（通过 file 命令能够查看相关信息）

首先，需要安装适用于 ARM 的交叉编译工具链，可以根据自己的情况选择，下面简单举例：

```bash
sudo apt-get install gcc-arm-none-eabi
```

然后在你的根CMakeLists.txt文件中添加如下参数的配置，记得更换相关程序的路径

```cmake
# 指定目标系统名称和处理器类型
set(CMAKE_SYSTEM_NAME Linux) # 设置目标系统名称为 Linux
set(CMAKE_SYSTEM_PROCESSOR arm) # 设置目标处理器类型为 ARM

# 设置交叉编译器路径（填绝对路径）
set(CMAKE_C_COMPILER /usr/bin/arm-none-eabi-gcc) # 设置 C 编译器为 ARM 交叉编译器
set(CMAKE_CXX_COMPILER /usr/bin/arm-none-eabi-g++) # 设置 C++ 编译器为 ARM 交叉编译器

# 设置交叉编译器的 sysroot 路径（填绝对路径）
# 假设 sysroot 位于 /path/to/arm/sysroot
set(CMAKE_SYSROOT /path/to/arm/sysroot) # 指定交叉编译器使用的 sysroot 路径，通常包含目标系统的库和头文件

# 配置查找路径模式
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER) # 指定 CMake 查找程序时不使用 sysroot 路径
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY) # 指定 CMake 查找库时仅使用 sysroot 路径
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY) # 指定 CMake 查找头文件时仅使用 sysroot 路径
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY) # 指定 CMake 查找包时仅使用 sysroot 路径
```

当然你可以选择把这些参数单独放在一个cmake文件中（例如 `arm-toolchain.cmake`），这样你需要交叉编译的时候再指定这个文件进行交叉编译也不迟

```bash
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=../arm-toolchain.cmake
```

最后再次强调，如果你的目标是Arm系统，那么在你本机Linux系统上编译的库必须是针对Arm架构编译得到的

这个库文件的编译也是用的交叉编译工具，你只需要在编译库文件时指定前面创建的`arm-toolchain.cmake`文件即可（在你所编译的库文件的源码下创建即可），步骤都差不多的

```bash
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=../arm-toolchain.cmake
```

