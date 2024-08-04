## 演示常见项目的管理方式

很多人讲 CMake 特别喜欢讲单个文件夹和单个文件的处理情况，可这是没有必要的。这种文件结构并不常见，不如上来直接讲如何管理多个目录和多个文件，这样更具实际价值

就当前个人的项目编写情况来看，常用的结构如下：

```markdown
include
src
thirdparty
CMakeLists.txt
```

include 用来存放头文件，src 用来存储源文件，thirdparty 用来存储第三方库

*这里的第三方库还只是个简单的头文件，后续有章节会演示结构复杂的第三方库的引入（用 CMake 管理的第三方库和没用 CMake 管理的第三方库的两种情况的引入）*

CMakeLists.txt 文件是 CMake 的核心文件，用于描述整个项目的构建过程。使用该文件可以方便地管理项目的构建和编译过程。只有源文件存在的文件夹才有可能用到 CMakeLists.txt 文件，而且子 CMakeLists.txt 文件将继承父 CMakeLists.txt 文件的变量，也就是说这里有传递关系

为了方便看清楚每个 CMakeLists.txt 文件的内容，对下面的结构中的 CMakeLists.txt 文件进行序号标识：

```markdown
├── CMakeLists.txt ①
├── include
│   ├── public.h
│   └── server
│       ├── ChatServer.h
│       ├── ChatService.h
│       ├── db
│       │   └── db.h
│       ├── model
│       │   ├── FriendModel.h
│       │   ├── Group.h
│       │   ├── GroupModel.h
│       │   ├── GroupUser.h
│       │   ├── OfflineMsgModel.h
│       │   ├── User.h
│       │   └── UserModel.h
│       └── redis
├── src
│   ├── client
│   │   ├── Client.cpp
│   │   └── CMakeLists.txt ③
│   ├── CMakeLists.txt ②
│   └── server
│       ├── ChatServer.cpp
│       ├── ChatService.cpp
│       ├── CMakeLists.txt ④
│       ├── db
│       │   └── db.cpp
│       ├── model
│       │   ├── FriendModel.cpp
│       │   ├── Group.cpp
│       │   ├── GroupModel.cpp
│       │   ├── OfflineMsgModel.cpp
│       │   ├── User.cpp
│       │   └── UserModel.cpp
│       ├── redis
│       └── Server.cpp
└── thirdparty
    └── json.hpp
```

① CMakeLists.txt 文件（根，也是管理项目的起点，这里设置的变量将会传递给其后代）：

```cmake
# 设置CMake的最低版本要求为3.16
cmake_minimum_required(VERSION 3.16)

# 定义项目名称为“chat”，语言为C++
project(chat CXX)

# 设置C++编译标志，添加-g选项以生成调试信息
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")

# 设置可执行文件的输出目录为项目根目录下的bin目录
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 包含项目的头文件目录
include_directories(${PROJECT_SOURCE_DIR}/include)
include_directories(${PROJECT_SOURCE_DIR}/include/server)
include_directories(${PROJECT_SOURCE_DIR}/include/server/db)
include_directories(${PROJECT_SOURCE_DIR}/include/server/model)
include_directories(${PROJECT_SOURCE_DIR}/include/server/redis)
include_directories(${PROJECT_SOURCE_DIR}/thirdparty)

# 添加src子目录，其中包含项目的源文件和进一步的CMakeLists.txt文件
add_subdirectory(src)
```

include_directories 用于指定在编译期间搜索头文件的目录。通过指定这些目录，编译器在处理`#include`指令时会优先搜索这些目录。但是，你切记这个是无法层级搜索头文件的，只能搜索到指定目录下的头文件。如果你指定的文件夹A下面还有文件夹B，那么文件夹B下面的头文件是无法被搜索到的，你需要再添加一个关于文件夹B的头文件搜索路径。这些头文件搜索的结果会传递给当前文件夹的子文件夹，前提是被 add_subdirectory 包含了

add_subdirectory 命令用于将指定的子目录添加到构建过程中，并处理该子目录中的 CMakeLists.txt 文件。

② CMakeLists.txt 文件：

```cmake
add_subdirectory(client)
add_subdirectory(server)
```

src文件夹下面没有要处理的源文件，但是src文件下的client和server文件夹却有，所以继续利用 add_subdirectory 命令往下递进

 ③和 ④是同层级的目录，优先看 ③ CMakeLists.txt 文件：

```cmake
# 将当前目录中的所有源文件添加到 CLIENT_LIST 变量中
aux_source_directory(. CLIENT_LIST)

# 将 ../server/model 目录中的所有源文件添加到 MODEL_LIST 变量中
aux_source_directory(../server/model MODEL_LIST)

# 将 ../server/db 目录中的所有源文件添加到 DB_LIST 变量中
aux_source_directory(../server/db DB_LIST)

# 创建一个名为 ChatClient 的可执行文件，并将 CLIENT_LIST、MODEL_LIST 和 DB_LIST 中的源文件添加到目标文件中
add_executable(ChatClient ${CLIENT_LIST} ${MODEL_LIST} ${DB_LIST})

# 链接 ChatClient 可执行文件所需的库
target_link_libraries(ChatClient muduo_net muduo_base mysqlclient hiredis pthread)
```

aux_source_directory 用于将指定目录中的所有源文件添加到一个变量中，且不会递归地搜索指定目录及其子目录中的源文件。它只会在指定的目录中查找源文件，而不会自动搜索子目录中的文件。

add_executable 用于定义一个可执行目标，第一个参数是可执行文件的名称，其后跟的就是源文件（aux_source_directory会把需要的源文件存储到一个变量中，这些变量就需要加入到这里面来）。至于所需的头文件，其父已经帮我们寻找了，就无需再找，因为前面说过父寻找到头文件记录会传递给其后代。

头文件实际上并不会被编译，编译器只会编译源文件。只是在编译之前，会将源文件中#include包含的文件在源文件中展开。所以头文件无需加入到 add_executable 中，只要编译器能找到头文件即可。include_directories 显然就是干这个工作的。

如果我们的可执行文件还需要链接库文件，那就需要用 target_link_libraries 。第一个参数指定需要链接库的可执行程序的名称，后买你的参数就是所需库的名称（到后面讲静态库动态库再细究）

再看④ CMakeLists.txt 文件：

```cmake
# 将当前目录中的所有源文件添加到 SRC_LIST 变量中
aux_source_directory(. SRC_LIST)

# 将 ./db 目录中的所有源文件添加到 DB_LIST 变量中
aux_source_directory(./db DB_LIST)

# 将 ./model 目录中的所有源文件添加到 MODEL_LIST 变量中
aux_source_directory(./model MODEL_LIST)

# 将 ./redis 目录中的所有源文件添加到 REDIS_LIST 变量中
aux_source_directory(./redis REDIS_LIST)

# 创建一个名为 ChatServer 的可执行文件，并将 SRC_LIST、DB_LIST、REDIS_LIST 和 MODEL_LIST 中的源文件添加到目标文件中
add_executable(ChatServer ${SRC_LIST} ${DB_LIST} ${REDIS_LIST} ${MODEL_LIST})

# 链接 ChatServer 可执行文件所需的库
target_link_libraries(ChatServer muduo_net muduo_base mysqlclient hiredis pthread)
```

## 总结

include_directories：搜索头文件。指定文件夹下的头文件编译器就能找到，且最好在根CMakeLists.txt 文件完成这个工作，那么所有子CMakeLists.txt 文件都无需再用该语法了

aux_source_directory ：搜索源文件。用于将指定目录中的所有源文件添加到一个变量中

add_executable：生成可执行程序。第一个参数填写可执行程序名称，后面就跟上需要的源文件（通常用aux_source_directory 搜索到源文件且保存到一个变量中，然后再把这个变量引入进来）

target_link_libraries：链接所需库。第一个参数填写可执行程序名称，后面就跟库文件名称

*include_directories和aux_source_directory都不会递归地搜索指定目录及其子目录中的文件。它只会在指定的目录中查找文件，而不会自动搜索子目录中的文件*

## 不得不介绍的FILE命令

这个命令非常重要，可以替代include_directories和aux_source_directory命令（**能层级搜索**指定目录下的文件）

后面讲Qt项目配置的时候，就需要这个命令，因为ui文件、qss文件、qrc文件等资源没有专门的命令来查找，但是借助file这个命令就能解决。简单来讲，file可以把任何文件保存到一个变量中，然后给别人引用

这里只演示匹配指定模式的文件，并将结果存储在变量中：

```cmake
file(GLOB TEST_RESULT /public/home *.cpp)			#不会层级搜索.cpp文件
file(GLOB_RECURSE TEST_RESULT /public/home *.cpp)	 #会层级搜索.cpp文件
```

但不建议替代include_directories，因为编译器不会知道去哪里寻找头文件。那么导入头文件会有很长的路径，极其不美观。如果你真这么做了，那记得把头文件搜索结果存储的变量加入到add_executable中，因为编译器找不到头文件。

个人觉得，优先选择include_directories和aux_source_directory命令，再考虑file命令辅助。特别是如果只是用来查找头文件而不是要用头文件再去编译（Qt就需要把头文件编译为ui_xxx.h文件，所以不得不用file命令，但尽管如此，我们也还是会用到include_directories来帮我们搜索头文件）

即file即使存在，include_directories也无可替代，aux_source_directory可以被替代