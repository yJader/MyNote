# GoogleTest的简单使用

> [官方demo](https://github.com/bast/gtest-demo)
>
> [C++ 项目 使用 CMake 和 Google Test（傻瓜式教程）_googletest cmake_想做一只开心的菜鸡的博客-CSDN博客](https://blog.csdn.net/Fei20140908/article/details/104344462)

在完成图形学实验时, 想要进行测试, 从巨麻烦的ctest转向gtest, 只能说, 还得是java QAQ

## 项目结构

```shell
.
├── CMakeLists.txt
├── README.md
├── src
│   ├── CMakeLists.txt
│   ├── global.hpp
│   ├── main.cpp
│   ├── rasterizer.cpp
│   ├── rasterizer.hpp
│   ├── Triangle.cpp
│   └── Triangle.hpp
└── tests
    ├── CMakeLists.txt
    ├── main.cpp
    ├── test_basic1.cpp
    └── test_insideTriangle.cpp
```

- 最开始没有/src文件夹, 很麻烦

## 配置

### CMakeLists.txt配置

1. `/CMakeLists.txt` 

   ```cmake
   # cmake version
   cmake_minimum_required(VERSION 3.10)
   # project name
   project(Rasterizer)
   
   find_package(OpenCV REQUIRED)
   # 使用c++17
   set(CMAKE_CXX_STANDARD 17)
   
   include_directories(/usr/include)
   # 添加src文件目录
   include_directories(src)
   # 添加目录
   add_subdirectory(src)
   add_subdirectory(tests)
   # add_subdirectory(lib/googletest)
   
   # 启用测试
   enable_testing()
   ```

2. `/src/CMakeList.txt`

   ```cmake
   # 添加进编译, gtest无关
   add_executable(Rasterizer main.cpp rasterizer.hpp rasterizer.cpp global.hpp Triangle.hpp Triangle.cpp)
   target_link_libraries(Rasterizer ${OpenCV_LIBRARIES})
   
   
   # 为了让单元测试的时候src下的代码能被作为静态链接库使用
   file(GLOB_RECURSE SOURCES LIST_DIRECTORIES true *.h *.cpp)
   set(SOURCES ${SOURCES})
   #设置 BINARY 为项目名IndexProject
   set(BINARY ${CMAKE_PROJECT_NAME})
   add_library(${BINARY}_lib STATIC ${SOURCES})
   ```

3. `/tests/CMakeLists.txt`

   ```cmake
   set(BINARY ${CMAKE_PROJECT_NAME}_test)
   file(GLOB_RECURSE TEST_SOURCES LIST_DIRECTORIES false *.h *.cpp)
   set(SOURCES ${TEST_SOURCES})
   add_executable(${BINARY} ${TEST_SOURCES})
   add_test(NAME ${BINARY} COMMAND ${BINARY})
   # 链接src生成的lib库和gtest库
   target_link_libraries(${BINARY} PUBLIC ${CMAKE_PROJECT_NAME}_lib gtest)
   ```

   

## 基本操作

