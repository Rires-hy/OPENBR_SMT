# visual studio 2019

during installation, Choose the **desktop development  with C++** and **universal windows platform development** 

# mingw64 

adding mingw64 to the system environment path, **C:\mingw64\bin** and **C:\mingw64\x86_64-w64-mingw32**. In windows command type:

```
gcc -v
```

to check the installation of mingw64 gcc, g++.

# cmake 3.20

after installation, adding **C:\Program Files\CMake\bin** to system environment path. 

# download opencv 4.x

Here I use <u>opencv 4.1.0</u>. Remember to add the path：**C:\opencv-4.1.0\build-msvc2019\install\x64\vc16\bin**  to system environment path. ![image-20210426085339035](C:\Users\10024\AppData\Roaming\Typora\typora-user-images\image-20210426085339035.png)

Open the correct **Cross Tools Command Prompt**: **X64_X86** typing:

```
cd C:\opencv-4.1.0
```

```
mkdir build-msvc2019
```

the folder "msvc2019" will be generate

```
cd build-msvc2019
```

```
cmake -G "NMake Makefiles" -DBUILD_PERF_TESTS=OFF -DBUILD_TESTS=OFF -DWITH_FFMPEG=OFF -DCMAKE_BUILD_TYPE=Debug ..
```

cmake would build opencv with vs 2019, and generate the build file in above folder. 

```
nmake
nmake install
cmake -DCMAKE_BUILD_TYPE=Release ..
nmake
nmake install
nmake clean
```

# Qt 5.4.1

download Qt5.4.1 from official site. The version is the same as **build instruction**. finding the path:

```
C:\Qt\Qt5.4.1\5.4\msvc2013_64_opengl
```

# Git

download **git bash** to **clone** the responsory from Github.  Also check the version and **get update. **

```
$ cd /c
$ git clone https://github.com/biometrics/openbr.git
$ cd openbr
$ git checkout v1.1.0
$ git submodule init
$ git submodule update
```

if no update, the following error may occur:

```
“CMake Error at aten/src/ATen/CMakeLists.txt:226 (set_property): set_property could not”
```

# Openbr

do the same as before(generate the install folder):

```
cd C:\openbr
mkdir build-msvc2019
cd build-msvc2019
```

get the path:**C:\opencv-4.1.0\build-msvc2019\install** and also the previous QT path in command:

```
cmake -G "CodeBlocks - NMake Makefiles" -DCMAKE_PREFIX_PATH="C:\opencv-4.1.0\build-msvc2019\install;C:\Qt\Qt5.4.1\5.4\msvc2013_64_opengl" -DCMAKE_INSTALL_PREFIX="./install" -DBR_INSTALL_DEPENDENCIES=ON -DCMAKE_BUILD_TYPE=Release ..
```

delete the following file according to the **Todo.md**. ![image-20210426092948868](C:\Users\10024\AppData\Roaming\Typora\typora-user-images\image-20210426092948868.png)

**Cmakelist.txt** file should be modified. In "**"#Find Opnecv**": adding :

```
# Find OpenCV
# FIND_PACKAGE( OpenCV 4.1.0 REQUIRED )

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH}  
                         "C:/opencv-4.1.0/build-msvc2019/bin"
)
set(OpenCV_DIR  "C:/opencv-4.1.0/build-msvc2019/bin")
FIND_PACKAGE( OpenCV 4.1.0 REQUIRED )
```

link the directories of Opencv:

```
Link_directories(C:\opencv-4.1.0\build-msvc2019\install\include\opencv2)
```

If BUILD_SHARED_LIB is set to off, the generation of dynamic so is turned off and the default is static lib files.

```
add_library(<target > STATIC <>)
```

**to understand the detail and modify the cmakelist.txt, the basic element of cmakelist.txt should be familiar**, the following are some syntax definition. 

1#project name

```
project(study_case)
```

2#cmake minimum requirement

```
cmake_minimum_required(VERSION 2.8.3)
```

3#Add the required libraries

```
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "/usr/local/share/OpenCV")

find_package(OpenCV 3.2.0 REQUIRED)
```

4#Determine the required header files

```
include_directories( include)
```

5#Determine the compilation language

```
set(CMAKE_CXX_STANDARD 11
```



6#Set the binary file directory

```
SET(BIN_DESTINATION ${PROJECT_SOURCE_DIR}/bin)

SET(cmake_runtime_output_directory ${bin_destination})
```

7#Add source code if multiple files are related (optional)

```
set(SRC 

    ${PROJECT_SOURCE_DIR}/src/detector.cpp
    
    ${PROJECT_SOURCE_DIR}/src/demo.cpp
    
    ${PROJECT_SOURCE_DIR}/src/test.cpp

)
```

8#If you need to compile dynamic libraries and link library files (optional)

```
link_directories(${PROJECT_SOURCE_DIR})

add_library(overload SHARED ${SRC})

target_link_libraries(overload -llianghao -lpthread -lm -lstdc++)
```

9#Generate executable files

```
link_directories(${PROJECT_SOURCE_DIR})
```

10#Dynamic library

```
link_directories(${PROJECT_SOURCE_DIR}/lib) #
target_link_libraries(project_name -lmxnet ) #libmxnet.so
```

11.#Static Library

```
add_library(mxnet STATIC IMPORTED)
set_property(TARGET mxnet PROPERTY IMPORTED_LOCATION /path/to/libmxnet.a)
target_link_libraries(project_name mxnet ) #libmxnet.a
```

**Modify the cmakelist.txt until the following sentence show up:**![image-20210426092539395](C:\Users\10024\AppData\Roaming\Typora\typora-user-images\image-20210426092539395.png)

then copy the file "**models.tar.gz**" to "**C:\openbr\build-msvc2019\models-prefix\src**" and type in:

```
nmake
```

the following error occur:

![image-20210426095447209](C:\Users\10024\AppData\Roaming\Typora\typora-user-images\image-20210426095447209.png)

**error : db.cpp.obj : error LNK2019: 无法解析的外部符号 "__declspec(dllimport) public**  



**I find some solutions online, there are following explanation:**



1.**The character set of the generated DLL file is Unicode while the character set of the generated exe file is the default ASCII**

![image-20210426101037143](C:\Users\10024\AppData\Roaming\Typora\typora-user-images\image-20210426101037143.png)

```
Add_Definitions(-DUNICODE -D_UNICODE)
```

![img](https://img2020.cnblogs.com/blog/1630599/202101/1630599-20210105081826638-1075481728.png)



2.**The compiled runtime library is of the wrong type, which should be unified:**

![image-20210426100820938](C:\Users\10024\AppData\Roaming\Typora\typora-user-images\image-20210426100820938.png) 

**However,** from the cmakelist.txt, i have no idea on how to change the  setting in txt file instead of open Visual studio 2019?

3.This error means that the linker did not find the class SAASOFEPSocket implementation. It seems that this may be in the DLL you are using. In this case you must find the .lib file associated with this DLL and add the .lib file to your project settings at

Project-> Properties-> Linker-> Input-> Additional Dependencies

Type the name of the lib file here, and then make sure it is in one of the directories where the linker searches for libs.

**Dynamic library export: Lib_DLL is a custom variable name**

```
Add_Definitions(-DLib_DLL) 
```

Use of MFC: If you call MFC functions in your project, you need to configure to use MFC in a dynamic library or use MFC in a static library, you can use the command:

```
Add_Definitions(-D_AFC_DLL)
```

Add preprocessor definitions to the compilation of source files.

```
Add_compile_definitions(<definition> ...)
```

4.The first two cases are general cases, but if they are not, then it may be related to macro definition, which is the main case this blog is trying to explain. From the error message posted at the beginning, we can see that the error occurs in the PointCloudFilter class. So, we open the header file of this class and we can find a few lines of preprocessing code at the beginning, as shown in the figure below. This is an if_else statement, which roughly means that if DLL_IMPLEMENT is defined, the value of the macro DLL_API will be set to output dll file, otherwise it will be set to input dll file. And here, this project is to output the dll file, so we need to define DLL_IMPLEMENT before these codes are run.

![img](https://img-blog.csdnimg.cn/20210328134223117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEyMDAyNQ==,size_16,color_FFFFFF,t_70#pic_center)





another error:

```
error C4430: Missing type specifier - assumed to be int. note: C++ does not support the default int
```

finding solution:

1. （此情况经常出现在大型工程项目中）如果存在两个类的头文件a.h和b.h,在a.h中有这样的语句：#include"b.h",在b.h文件中有这样的语句：#include "a.h"且在一个类中有另一个类的对象时，那么就会出现这样的错误。

2. 没有包含要定义的类的头文件。

3.项目中少加了宏定义，导致头文件重复定义或相应宏无法识别。

4.当有多个头文件时，顺序写反也可能导致相关的错误，其根本是头文件中的预编译语句被隐去了。

5. 可能是函数没有写返回值


The above is what i did in my computer, step by step. The errors are attached, some are solved and some didn't. 