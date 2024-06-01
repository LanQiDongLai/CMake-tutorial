## 注释

```cmake
# 行注释

#[[ 
块注释
]]
```

## 变量

CMake中变量都是赋值于声明一体，与Python一致

CMake中变量都是字符串类型，但是CMake会根据字符串内容对他们进行分类

``````

``````

## 条件分支与循环

### ```if```条件

```cmake
if(condition1)
	statement1
	statement2
elseif(condition2)
	statement3
else()
	statement4
endif()
```

### ```foreach```循环

```cmake
# 遍历列表1
foreach(item IN LISTS list)
	statement
endforeach()
# 遍历列表2
foreach(item IN ITEMS item1 item2 item3)
	statement
endforeach()

# 遍历数字[0..end]
foreach(num RANGE end)
	statement
endforeach()
# 遍历数字[start..end] 步长step
foreach(num RANGE start end step)
	statement
endforeach()
```

### ```while```循环

```cmake
while(condition)
	statement
endwhile()
```

### 布尔运算

#### 布尔值

如果是1, ON, YES, TRUE, Y, 非零值，非空字符串时，条件判断返回True
如果是 0, OFF, NO, FALSE, N, IGNORE, NOTFOUND，空字符串时，条件判断返回False

#### 逻辑运算

NOT为非，OR为或，AND为与

#### 比较

数值比较：LESS，GREATER，EQUAL，LESS_EQUAL，GREATER_EQUAL

字符串比较：STRLESS，STRGREATER，STREQUAL，STRLESS_EQUAL，STRGREATER_EQUAL

#### 文件操作

文件或目录存在：EXISTS \<path\>

路径为目录：IS_DIRECTORY \<path\>

路径为符号链接：IS_SYMLINK \<path\>

是否为绝对路径：IS_ABSOLUTE \<path\>

#### 其他

在列表中 \<variable\> IN_LIST \<variable\>

路径相同 \<path\> PATH_EQUAL \<path\>

## 常用方法

### 赋值

```cmake
# 为变量VAR赋值为val
set(VAR val)
# 为变量VAR赋值为列表val1;val2;val3
set(VAR val1;val2;val3)
set(VAR val1 val2 val3)
```

### 列表操作

```cmake
# 向VAR列表添加元素val4 val5 val6，现在VAR为val1;val2;val3;val4;val5;val6
list(APPEND VAR val4 val5 val6)
# 向VAR列表删除元素val4，现在VAR为val1;val2;val3;val5;val6
list(REMOVE_ITEM VAR val4)
# 向VAR列表中获取长度
list(LENGTH VAR list_length)
# 向VAR列表中获取第三个元素
list(GET VAR 3 item)
```

### 文件搜索

```cmake
# 搜索 src 目录下的源文件，赋值给SRC_LIST，第一个参数为路径，第二个参数为要赋值的变量
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SRC_LIST)
# 搜索 src 目录下的cpp源文件，赋值给SRC_LIST，第一个参数为搜索模式（GLOB/GLOB_RECURSE 第二个会递归搜索目标目录的所有子目录），第二个参数为要赋值的变量，第三个参数为目录文件名（可包含通配符）
file(GLOB SRC_LIST ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
```

### 日志

```cmake
message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] "message to display" ...)
```

- (无)：重要消息
- STATUS：非重要消息
- WARNING：警告消息
- AUTHOR_WARNING：CMake警告消息
- SEND_ERROR：CMake错误，继续执行，跳过生成步骤
- FATAL_ERROR：CMake错误，终止所有处理过程

## 构建方法

### 版本检查

```cmake
cmake_minimum_required(VERSION 3.0)
```

### 项目命名

```cmake
project(APP)
```

### 生成可执行文件

```cmake
add_executable(app main.cpp)
```

### 包含头文件

```cmake
include_directory(${INCLUDE_PATH})
```

### 链接静态库

```cmake
# 在Linux中可以只写库的名称而不是libxxx.a
# 对于系统库或在环境变量中的静态库
link_libraries(sfml)
# 对于自定义库，需要指定库目录
link_directories(${LIB_PATH})
```

### 链接动态库

```cmake
target_link_libraries(app PRIVATE Qt::Gui Qt::Core Qt::Widgets)
```

原型：```target_link_libraries(
    <target> 
    <PRIVATE|PUBLIC|INTERFACE> <item>... 
    [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)```

target: 加载动态库的文件名称，可以是源文件，可以是其他动态库文件，也可以是项目生成的可执行文件，一般为可执行文件

PRIVATE|PUBLIC|INTERFACE：动态库访问权限，默认为PUBLIC

PUBLIC：在PUBLIC后面的库会被Link到前面的target中，并且里面的符号也会被导出，提供给第三方使用。
PRIVATE：在PRIVATE后面的库仅被link到前面的target中，并且终结掉，第三方不能感知你调了啥库
INTERFACE：在INTERFACE后面引入的库不会被链接到前面的target中，只会导出符号。

```cmake
# 对于自定义库，需要指定库目录
link_directories(${DLL_PATH})
```

### 生成静态库

```cmake
add_library(calc STATIC calc.c add.c sub.c div.c mult.c)
```

### 生成动态库

```cmake
add_library(calc SHARED calc.c add.c sub.c div.c mult.c)
```

## 内置变量

- LIBRARY_OUTPUT_PATH：库目录输出路径
- EXECUTABLE_OUTPUT_PATH：项目可执行文件输出路径，动态库目录输出路径
- CMAKE_C_COMPILER：C编译器
- CMAKE_CXX_COMPILER：C++编译器
- CMAKE_CXX_FLAGS：C++编译额外选项
- CMAKE_CURRENT_SOURCE_DIR：当前CMakeLists.txt文件所在目录
- CMAKE_PROJECT_NAME：当前项目名称

## 嵌套CMake

```cmake
add_subdirectories(${CMAKE_CURRENT_SOURCE_DIR}/test)
```

每个子节点的CMakeLists.txt都是一个单独的生成任务，父节点只是负责写明生成顺序和生成路径（变量赋值），因为在父节点中声明的变量在子节点中是有效的

