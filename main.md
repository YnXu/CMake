# CMakeLists笔记

---
- ### ==add_executable== 要求CMake根据指定的源文件生成可执行文件

```
add_executable(
    hello # 生成EXE文件名称
    main.cpp # 需要生成.EXE的文件路径
)
```
**这将从main.cpp源码文件创建一个叫“hello”（Windows下叫“hello.exe”）的可执行文件。你可以根据自己的需要将C和C++文件混合。在同一个CMakeLists.txt可以有多个可执行文件和库。同一个源码文件可以用于不同的目的，源码可以从其他目标中为每个目的独立的编译。**


```
add_executable(
    demo 
    main.cpp main.h main.rc
)
```
**这将使用main.cpp源文件,main.h文件,main.rc文件构造可执行文件。至于如何使用这些文件,CMake比我们都清楚。**

---
- ### ==INCLUDE== 使用标准模块

**比如:**
```
INCLUDE(FindBoost)
```
**一句话,就告诉了CMake“我们的程序需要Boost”。**

---
- ### ==SET== 使用变量
```
SET( MY_SOURCES main.cpp widget.cpp)
MESSAGE(STATUS "my sources: ${MY_SOURCES}")
```
**使用SET()命令来为变量设置值。如果你列出了一个以上的字符串，变量将是串列表。**
**列表是一列由分号隔开的字符串。如果只设置个一项，那么这项只有一个值。**
**可以通过${VAR}获得变量的值。**
**可以使用FOREACH()来迭代一份列表：**
```
FOREACH(next_ITEM ${MY_SOURCES})
MESSAGE(STATUS "next item: ${next_ITEM}")
ENDFOREACH(next_ITEM ${MY_SOURCES})
```
**CMake中的命令是大小写无关的。变量名和参数名是大小写相关的。**

---
- ### 测试平台相关信息
**办法一(这个代码没有检验过) **
```
IF (UNIX)
   MESSAGE("这个是UNIX操作系统")
ENDIF (UNIX)
 
IF (MSVC)
   MESSAGE("这个需要做VC的项目文件")
ENDIF (MSVC)
```
**办法二(这个测试过)**
```
IF (${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
 SET(option WIN32)
 SET(win32_LIBRARIES comctl32.lib shlwapi.lib shell32.lib 
     odbc32.lib odbccp32.lib  kernel32.lib user32.lib gdi32.lib 
     winspool.lib comdlg32.lib advapi32.lib shell32.lib 
     ole32.lib oleaut32.lib uuid.lib odbc32.lib odbccp32.lib)
 #SET(defs -DUNICODE -D_UNICODE)
ENDIF (${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
```
---
- ### ==ADD_LIBRARY== 要求CMake根据指定源文件生成库
**ADD_LIBRARY: Add a library to the project using the specified source files.**
```
  ADD_LIBRARY(libname [SHARED | STATIC | MODULE] [EXCLUDE_FROM_ALL]
              source1 source2 ... sourceN)
```
**Adds a library target. SHARED, STATIC or MODULE keywords are used to set the library type. If the keyword MODULE appears, the library type is set to MH_BUNDLE on systems which use dyld. On systems without dyld, MODULE is treated like SHARED. If no keywords appear as the second argument, the type defaults to the current value of BUILD_SHARED_LIBS. If this variable is not set, the type defaults to STATIC.**

**If EXCLUDE_FROM_ALL is given the target will not be built by default. It will be built only if the user explicitly builds the target or another target that requires the target depends on it.**

---
- ### 添加查找头文件的路径
**INCLUDE_DIRECTORIES: Add include directories to the build.**
```
  INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)
```
**Add the given directories to those searched by the compiler for include files. By default the directories are appended onto the current list of directories. This default behavior can be changed by setting CMAKE_INCLUDE_DIRECTORIES_BEFORE to ON. By using BEFORE or AFTER you can select between appending and prepending, independent from the default. If the SYSTEM option is given the compiler will be told that the directories are meant as system include directories on some platforms.**

---
- ### ==LINK_DIRECTORIES== 添加库文件的搜索路径
```
# LINK_DIRECTORIES: Specify directories in which to search for libraries.
LINK_DIRECTORIES(directory1 directory2 ...) 
```

---
- ### ==TARGET_LINK_LIBRARIES== 显式指定链接时需要的库文件

**为每个目标分别指定需要链接的库文件(指定部分目标专用的库文件)**
 ```
# TARGET_LINK_LIBRARIES: Link a target to given libraries.
TARGET_LINK_LIBRARIES(target library1 <debug | optimized> library2 ...)
```
**Specify a list of libraries to be linked into the specified target. The debug and optimized strings may be used to indicate that the next library listed is to be used only for that specific type of build**

**为所有目标统一指定需要的库文件(指定所有目标都用的库文件)**
```
# LINK_LIBRARIES: Link libraries to all targets added later.
LINK_LIBRARIES(library1 <debug | optimized> library2 ...)
```
**This is an old CMake command for linking libraries. Use TARGET_LINK_LIBRARIES unless you have a good reason for every target to link to the same set of libraries.**

**Specify a list of libraries to be linked into any following targets (typically added with the ADD_EXECUTABLE or ADD_LIBRARY calls). This command is passed down to all subdirectories. The debug and optimized strings may be used to indicate that the next library listed is to be used only for that specific type of build.**

---
- ### ==ADD_DEFINITIONS== 显式实施宏定义
**用法演示一(文本宏):**
```
ADD_DEFINITIONS(-DDEBUG)
```
**用法演示二(常量宏)**
```
ADD_DEFINITIONS(-DVERSION=1)
```
```
# ADD_DEFINITIONS: Adds -D define flags to the command line of C and C++ compilers.
ADD_DEFINITIONS(-DFOO -DBAR ...)
```
**Adds flags to command line of C and C++ compilers. This command can be used to add any flag to a compile line, but the -D flag is accepted most C/C++ compilers. Other flags may not be as portable.**

---
- ### ==OPTION== 让编译者在界面上设置你提供的选项
```
# 增加一个选项,WXWINDOWS_USE_SHARED_LIBS
OPTION(WXWINDOWS_USE_SHARED_LIBS "Use shared versions (.so) of wxWindows libraries" ON)
MARK_AS_ADVANCED(WXWINDOWS_USE_SHARED_LIBS) 
```
**相关知识**
```
# OPTION: Provides an option that the user can optionally select.
OPTION(OPTION_VAR "help string describing option" [initial value])

# Provide an option for the user to select as ON or OFF. 
# If no initial value is provided, OFF is used.
# MARK_AS_ADVANCED: Mark cmake cached variables as advanced.
MARK_AS_ADVANCED([CLEAR|FORCE] VAR VAR2 VAR...)
```
**Mark the named cached variables as advanced. An advanced variable will not be displayed in any of the cmake GUIs unless the show advanced option is on. If CLEAR is the first argument advanced variables are changed back to unadvanced. If FORCE is the first argument, then the variable is made advanced. If neither FORCE nor CLEAR is specified, new values will be marked as advanced, but if the variable already has an advanced/non-advanced state, it will not be changed**

---
- ### ==FIND_PATH== 查找指定软件的安装目录
```
# Wx可能安装的目录(注意斜线的方向)
SET(DirectorOfWxMyBe   c:/
        d:/
        c:/wx
        d:/wx)
# 能代表WxWindows特征的文件

# WxWindows的安装目录        
FIND_PATH(WxRoot NAMES ${CharacteristicFilesOfWx} PATHS ${DirectorOfWxMyBe})

MESSAGE(${WxRoot})
```