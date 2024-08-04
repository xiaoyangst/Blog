如果你经常使用CMake管理项目，一路艰辛，自不必多言。我从来没写过Makefile，在预备学习它之前就知道CMake管理工具的存在了，那是第一次和CMake相识。等到真正需要用CMake管理项目的时候，在网上找教程对其简单学习，可是没过多久就在需求增多的情况下深感不足。下定决心对CMake深入学习，可因为语法的理解不够透彻，以及Clion本身的缓存问题影响到我的判断，这让我一度觉得CMake是个恶心工具（网上骂的人确实不少）。但是，越来越多的人使用CMake，如果能够熟练掌握它对后续项目的管理和第三方库的引入会相当方便。我企图通过手写CMake教程来再次梳理CMake本身，这对我而言是个挑战。

要把CMake弄明白？如果不这样的话，真的很难和CMake好好的交往。但是CMake内容很多，这个教程也应该是个持续更新的状态，所记录的是当前我所用到的内容，而非以求全而梳理的教程。

暂定目录：

1. CMake管理多个目录和多个源文件
2. CMake 构建和链接静态库和动态库
3. CMake 添加第三方依赖库
4. CMake install 部署项目
5. Cmake 构建 Qt 项目



完成本教程可能会用到的网址：

https://cmake-doc.readthedocs.io/zh-cn/latest/guide/tutorial/index.html

https://github.com/xiaoweiChen/Modern-CMake-for-Cpp/tree/main