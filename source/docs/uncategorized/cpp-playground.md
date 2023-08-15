快速搭建一个 C++ Playground
=========================

如果你是一个 C++新手（on linux），你一定尝试过徒手用 vim 写 cpp 文件，保存并退出，执行 g++编译源文件，执行编译出来的可执行文件，查看输出结果（如果你还没有尝试过，并且觉得自己还是个新手，请务必尝试一下）。久而久之，你愈发熟练，甚至配置了 YCM 大杀器，多开终端（甚至是多开 vim tab 页）同时编写多个 cpp 文件，然后在一个终端里面执行编译，输出结果。你觉得 IDE 太过臃肿，像什么 Visual Studio, Clion 之列的难以入你法眼。而 vim 什么的又太过原始，而且配置起来稍显费力（如果你是高玩，相信此文对你没有帮助），你一直想找一个配置简单，能够专心写一些玩具程序的 demo 开发工具。

恭喜你，此文将解决你的问题。

VSCode 和它的伙伴们
-----------------

首先准备好 VSCode 以及必要插件：

```bash
yychi@~> code --list-extensions
bungcip.better-toml
huacnlee.autocorrect
huizhou.githd
KylinIDETeam.cmake-intellisence
llvm-vs-code-extensions.vscode-clangd #1
mhutchie.git-graph
tomoki1207.pdf
twxs.cmake #2
vadimcn.vscode-lldb #3
vscodevim.vim
```

1. clangd 用于补全提示跳转等常用功能
2. cmake 语法高亮（可选）
3. lldb 调试工具[^a]

开始配置
-------

```bash
mkdir ~/code_scratch # 新建playground目录，随便取个名字：
cd ~/code_scratch
mkdir scratch
touch CMakeLists.txt
touch scratch/hello.cpp
code ~/code_scratch  # vscode打开该目录作为工作目录
```

在工作目录中创建 scratch 目录，用于存放写着玩的 cpp 文件（其实是刷题用的 ;)。然后在主目录下创建文件 CMakeLists.txt.

完成之后的目录结构长这样：
```bash
yychi@~/code_scratch> tree
.
├── CMakeLists.txt
└── scratch
    └── hello.cpp

2 directories, 2 files
```

下面是两个文件的内容：
```cpp
// file: hello.cpp

#include <iostream>
using namespace std;
int main()
{
    cout << "hello playground!" << endl;
    return 0;
}
```

```cmake
# file: CMakeLists.txt

cmake_minimum_required(VERSION 3.20)
project(scratch)

message(STATUS "CMake version: " ${CMAKE_VERSION})
if(NOT ${CMAKE_VERSION} VERSION_LESS "3.2")
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)
else()
    message(STATUS "Checking compiler flags for C++11 support.")
    # Set C++11 support flags for various compilers
    include(CheckCXXCompilerFlag)
    check_cxx_compiler_flag("-std=c++11" COMPILER_SUPPORTS_CXX11)
    check_cxx_compiler_flag("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
    if(COMPILER_SUPPORTS_CXX11)
        message(STATUS "C++11 is supported.")
        if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
            set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -stdlib=libc++")
        else()
            set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
        endif()
    elseif(COMPILER_SUPPORTS_CXX0X)
        message(STATUS "C++0x is supported.")
        if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
            set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x -stdlib=libc++")
        else()
            set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
        endif()
    else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
    endif()
endif()


 
# 禁止 C++ assert terminate 程序
# add_definitions(-DNDEBUG)

# ASIO 不使用 Boost 库
#add_definitions(-DASIO_STANDALONE)

# Spdlog 使用外部 Fmt 库
#add_definitions(-DSPDLOG_FMT_EXTERNAL)

# Jwt-cpp 使用外部 Json 库
# ADD_DEFINITIONS(-DJWT_CPP_JSON_EXTERNAL)

# add_definitions(-DGLOG_ON)

# 生成编译命令文件，给 YCM 使用
# set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

ADD_COMPILE_OPTIONS(-g)

#设置输出目录
# set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
# set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
# set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/build/)

#加入包含目录
#include_directories(${CMAKE_CURRENT_SOURCE_DIR}/log/src) 


#静态库目录
#link_directories(/usr/lib64/mysql)

#logger lib 
#add_subdirectory(log)

file(GLOB_RECURSE demo_srcs scratch/*.cpp)
message(STATUS "listing source...\n" ${demo_srcs})
foreach(srcfile IN LISTS demo_srcs)
    get_filename_component(elfname ${srcfile} NAME_WE)
    message(STATUS "compile ${srcfile} ..to.. ${elfname}")
    add_executable(${elfname} ${srcfile})
endforeach()


# aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SRCS) #加入目录下所有源码
# add_executable(demo ${SRCS}) #生成可执行文件
# target_link_libraries(demo logger)  #链接 logger 库

# add_executable(topk topk.cpp)
# add_executable(write write.cpp addressbook.pb.cc)
# add_executable(read addressbook.pb.cc read.cpp)
# target_link_libraries(write protobuf)
# target_link_libraries(read protobuf)
# aux_source_directory(test_link test_link_src)
# add_executable(test_link ${test_link_src})
```

然后执行下列操作：
```bash
yychi@~/code_scrach> mkdir build
yychi@~/code_scrach> cd build
yychi@~/code_scrach/build> cmake ..
-- The C compiler identification is GNU 13.2.1
-- The CXX compiler identification is GNU 13.2.1
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- CMake version: 3.27.1
-- listing source...
/home/yychi/code_scrach/scratch/hello.cpp
-- compile /home/yychi/code_scrach/scratch/hello.cpp ..to.. hello
-- Configuring done (0.9s)
-- Generating done (0.0s)
-- Build files have been written to: /home/yychi/code_scrach/build
yychi@~/code_scrach/build> make hello
[ 50%] Building CXX object CMakeFiles/hello.dir/scratch/hello.cpp.o
[100%] Linking CXX executable hello
[100%] Built target hello
yychi@~/code_scrach/build> ./hello
hello playground!
```

这样，后续需要写一个玩具程序，只要在 scratch 目录下新建一个 cpp 文件，然后在 build 目录下 cmake & make 一下，就得到了编译后的可执行文件。是不是很赞！

调试配置
-------

虽然对于玩具程序，cout, printf 通常足够定位问题，但是对于复杂的程序，我还是希望可以调试的。

这一点，通过 code-lldb 插件也可以做到。在工作目录下创建.vscode/launch.json，或者直接按 F5 就会自动生成该文件。配置如下：

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    // For variable references, visit: https://code.visualstudio.com/docs/editor/variables-reference
    "version": "0.2.0",
    "configurations": [
        {
            "type": "lldb",
            "request": "launch",
            "name": "DebugFile",
            "program": "build/${fileBasenameNoExtension}",
            "args": [],
            "cwd": "${workspaceFolder}"
        }
    ]
}
```

这样，就可以调试当前编辑器 focus 的文件了，当然，首先得编译输出可执行文件。效果如下：

![](/_static/cpp-debug.png)

### 远程调试

此外，lldb 还支持远程调试，将 lldb-server 及相关文件拷贝到目标机器，在远程机器上执行 lldb-server 起监听，然后在本机配置远程调试任务（上述 launch.json 中的第二个就是用于远程调试）。然后，当你执行这个调试任务，其实就是将可执行文件拷贝到远程机器上执行，并启用调试功能。

> 此外远程调试还有一个 bonus，可以 root 权限进行调试！只需使用 root 权限执行 lldb-server 即可。

远程调试的配置：
```json
// file:///.vscode/launch.json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    // for variables see: https://code.visualstudio.com/docs/editor/variables-reference
    "version": "0.2.0",
    "configurations": [
        {
            "type": "lldb",
            "request": "launch",
            "name": "Debug",
            "args": [],
            "cwd": "${workspaceFolder}/service/src/main/cpp",
            "program": "service/src/main/cpp/build/${fileBasenameNoExtension}",
        },
        {
            // see: https://github.com/vadimcn/codelldb/blob/master/MANUAL.md#connecting-to-lldb-server-agent
            // and https://github.com/vadimcn/codelldb/discussions/779
            "type": "lldb",
            "request": "launch",
            "name": "RemoteLaunch",
            "args": [],
            "program": "service/src/main/cpp/build/${fileBasenameNoExtension}",
            "initCommands": [
                "platform select remote-linux",
                "platform connect connect://127.0.0.1:12306", // 此处可更改为远程 ip
            ],
            "expressions": "native",
        }
    ]
}
```

clangd 的配置：
```json
// file:///.vscode/settings.json
{
    "clangd.path": "/usr/bin/clangd",
    "clangd.arguments": [
        // 在后台自动分析文件（基于 complie_commands)
        "--background-index",
        // 标记 compelie_commands.json 文件的目录位置
        // 关于 complie_commands.json 如何生成可见我上一篇文章的末尾
        // https://zhuanlan.zhihu.com/p/84876003
        // "--compile-commands-dir=build",
        // 同时开启的任务数量
        "-j=12",
        // 告诉 clangd 用那个 clang 进行编译，路径参考 which clang++的路径
        "--query-driver=/usr/bin/g++",
        // clang-tidy 功能
        "--clang-tidy",
        "--clang-tidy-checks=performance-*,bugprone-*",
        // 全局补全（会自动补充头文件）
        "--all-scopes-completion",
        // 更详细的补全内容
        "--completion-style=detailed",
        // 补充头文件的形式
        "--header-insertion=iwyu",
        // pch 优化的位置
        "--pch-storage=disk",
    ],
}
```

References
----------

1. https://github.com/vadimcn/codelldb/discussions/779
2. https://github.com/vadimcn/codelldb/blob/master/MANUAL.md#connecting-to-lldb-server-agent
3. https://windowsmacos-vscode-c-llvm-clang-clangd-lldb.readthedocs.io/configure.html

[^a]: 由于 clangd 和微软自带的 c++插件冲突，但调试功能由 ms-c++插件提供，因此不得不保留两个插件，在配置里面禁用掉 ms-c++的智能提示功能，这一段交给 clangd 来做。而使用了 lldb 插件，可以直接干掉 ms-c++插件，省得配置烦人了。