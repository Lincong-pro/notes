## CMake 官方文档学习

### build targets

```cmake
add_library(archive MODULE 7Z.cpp) //不生成.so 或者.a 文件
# 模块的特点就是不需要链接，只有在运行的时候才会选择性的去加载，所以不需要 target_link_libraries()
add_library(archive SHARED 7Z.cpp) //生成.so 动态文件
add_library(archive STATIC 7Z.cpp) //生成.a 静态库
# 除了上面的选项，我们仍然可以给我们编译的库指定框架（即哪一个系统下才能使用这个库）
add_library(MyFramework SHARED MyFramework.cpp)
set_target_properties(MyFramework PROPERTIES
FRAMEWORK TRUE
FRAMEWORK_VERSION A # Version "A" is macOS convention
MACOSX_FRAMEWORK_IDENTIFIER org.cmake.MyFramework
)
# 当然我们还有一个选项就是将我们所编译的库连接到另外一个库上面
# 前提条件就是我们所编译的库必须指定编译类型为 OBJECT 类型，然后
# 在另外一个库使用$<TARGET_OBJECT:objectlibname>进行连接，当然
# 也可以直接使用 target_link_libraries 进行连接
add_library(archive OBJECT archive.cpp zip.cpp lzma.cpp)
add_library(archiveExtras STATIC $<TARGET_OBJECTS:archive> extras.cpp)
add_executable(test_exe $<TARGET_OBJECTS:archive> test.cpp)

# 当然除了库可以连接 OBJECT 类型的 object 库之外，其他的 executable target
# 仍然可以连接这个库，即可以多次连接
add_library(archive OBJECT archive.cpp zip.cpp lzma.cpp)
add_library(archiveExtras STATIC extras.cpp)
target_link_libraries(archiveExtras PUBLIC archive)
add_executable(test_exe test.cpp)
target_link_libraries(test_exe archive)
```

- target_include_directories、target_compile_definitions、target_compile_options 分别为 include_directories、compile_definitions、compile_options 的补充（扩展）

- 上面的每一条命令都有访问项，分别为 PRIVATE、PUBLIC、INTERFACE，其定义就是 PRIVATE 模式仅仅只填充非接口类型的变量、INTERFACE 就单纯只填充接口变量、PUBLIC 模式两种变量

  ```cmake
  target_compile_definitions(archive
    PRIVATE BUILDING_WITH_LZMA
    INTERFACE USING_ARCHIVE_LIB
  )
  ```

- 我们在胡老师的脚本 sh 脚本中经常会看见-D、-I 之类的，实际上官方文档的解释就是-D 就是 COMILE_DEFINITIONS.-I 代表 INCLUDE_DIRECTORIES

  ```cmake
  # 将后面的cpp编译为源文件
  set(srcs archive.cpp zip.cpp)
  # 如果找到了这个库就向上面的srcs增加一个编译的cpp
  if (LZMA_FOUND)
    list(APPEND srcs lzma.cpp)
  endif()
  # 将src编译为动态库.so结尾
  add_library(archive SHARED ${srcs})
  if (LZMA_FOUND)
    # 库文件被编译使用编译器指定compile_define
    # The archive library sources are compiled with -DBUILDING_WITH_LZMA
    # 指定库的compile_define
    target_compile_definitions(archive PRIVATE BUILDING_WITH_LZMA)
  endif()
  # 指定库的属性为接口类型的USING_ARCHIVE_LIB
  target_compile_definitions(archive INTERFACE USING_ARCHIVE_LIB)
  # 编译为可执行文件
  add_executable(consumer)
  # Link consumer to archive and consume its usage requirements. The consumer
  # executable sources are compiled with -DUSING_ARCHIVE_LIB.
  target_link_libraries(consumer archive)
  ```

  target_link_libraries 实际上做了许多事情，该命令能将当前的源文件目录和 build 文件目录联系在一起 cmake_include_current_dir 能够很方便实现上面的要求，当然如果是 interface_include_directories 目录就要使用 cmake_include_current_dir_in_interface 去连接 include 目录，但是 target_link_libraries 能够实现上面的事。

- PUBLIC PRIVATE INTERFACE 各自的作用

  ```cmake
  add_library(archive archive.cpp)
  # 定义接口属性，只要引入了该接口就代表该引入该库
  target_compile_definitions(archive INTERFACE USING_ARCHIVE_LIB)
  add_library(serialization serialization.cpp)
  # 让生成的serialization target依赖archive库，但是由于显示的属性是INTERFACE,所以就是我只需要使用你的头文件即.h文件，其余的部分我都不想要
  target_compile_definitions(serialization INTERFACE USING_SERIALIZATION_LIB)

  add_library(archiveExtras extras.cpp)
  # 下面的PBLIC属性的依赖就是就相当于直接就继承了其头文件和源文件即.h和.cpp，后面的接着继承的就会一直拥有该库
  target_link_libraries(archiveExtras PUBLIC archive)
  # 该属性不会传递给子代，即私有依赖属性不传递给子代
  target_link_libraries(archiveExtras PRIVATE serialization)
  # archiveExtras is compiled with -DUSING_ARCHIVE_LIB
  # and -DUSING_SERIALIZATION_LIB

  add_executable(consumer consumer.cpp)
  # consumer is compiled with -DUSING_ARCHIVE_LIB
  target_link_libraries(consumer archiveExtras)
  ```

  由于库与库之间有继承的依赖关系，尤其是 PUBLIC、PRIVATE、INTERFACE 类型，所以 link 的先后关系必须小心，尤其是头文件问题和库继承不一致的时候。比如我的 target myExe 必须按照 lib1 lib2 lib3 的关系进行继承，但是头文件目录必须以 lib3 lib1 lib2

  ```cmake
  # 库的连接顺序
  target_link_libraries(myExe lib1 lib2 lib3)
  # 头文件的连接顺序
  target_include_directories(myExe
    PRIVATE $<TARGET_PROPERTY:lib3,INTERFACE_INCLUDE_DIRECTORIES>)
  ```

- 兼容的接口特性

  ```cmake
  add_executable(exe1 exe1.cpp)
  # 设置target exe1是完全独立编译
  set_property(TARGET exe1 PROPERTY POSITION_INDEPENDENT_CODE ON)
  # 将lib2.cpp编译为动态库，同样是完全独立编译
  add_library(lib1 SHARED lib1.cpp)
  set_property(TARGET lib1 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)
  add_executable(exe2 exe2.cpp)
  # target exe2连接动态库lib1
  target_link_libraries(exe2 lib1)
  对于compile_definition中的position_independent_code对于编译的动态库而言是默认设置的。
  
  ```

  接下来演示不兼容的情况

  ```cmake
  add_library(lib1 SHARED lib1.cpp)
  set_property(TARGET lib1 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)
  
  add_library(lib2 SHARED lib2.cpp)
  set_property(TARGET lib2 PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE OFF)
  
  add_executable(exe1 exe1.cpp)
  target_link_libraries(exe1 lib1)
  set_property(TARGET exe1 PROPERTY POSITION_INDEPENDENT_CODE OFF)
  
  add_executable(exe2 exe2.cpp)
  # 同时连接到两个不相互兼容的库
  target_link_libraries(exe2 lib1 lib2)
  
  ```

  ```shell
  # 假设我们的lib1和lib2不兼容，其中的lib1要求独立编译的方式进行编译，lib2要求非独立编译，然后当我们的exe2连接到两个属性要求不一致的库之后就出现了如下问题
  CMake Error: The INTERFACE_POSITION_INDEPENDENT_CODE property of "lib2" does
  not agree with the value of POSITION_INDEPENDENT_CODE already determined
  for "exe2".
  ```

#### 头文件包含

##### 同时包含源目录和生成目录

```cmake
# add the build directory and source directory
set(CMAKE_INCLUDE_CURRENT_DIR ON)
```

> 效果如下所示

![](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210823104540679.png)

##### 包含当前Source目录

```cmake
# 在生成target之前使用以下命令
include_directories (${CMAKE_SOURCE_DIR})

# 在生成target之后使用以下命令
# 链接添加当前文件夹以便于包含头文件
target_include_directories(${exeName}
  PUBLIC
    ${CMAKE_CURRENT_LIST_DIR} #or ${CMAKE_CURRENT_SOURCE_DIR}
  )
```

![](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210823105122780.png)

#### 包含当前Binary目录

```shell
# 在生成target之前使用以下命令
include_directories (${CMAKE_BINARY_DIR})

# 在生成target之后使用以下命令
# 链接添加当前文件夹以便于包含头文件
target_include_directories(${exeName}
  PUBLIC
    ${CMAKE_CURRENT_BINARY_DIR}
  )
```

#### GDAL 库链接

```cmake
# 引入Boost库的头文件 和 GDAL的头文件
target_include_directories(${PROJECT_NAME}
    PUBLIC ${GDAL_INCLUDE_DIR}
)
# 引入Boost库的lib文件 和 GDAL的lib文件
target_link_libraries(lincong ${GDAL_LIBRARY})
```

#### 检索库模块

> tbb库 ttb/ttb_stddef.h was removed from ttbb 2021.1. Replace ttb/ttb_stddef.h with oneapi/tbb/version.h in FindTBB.cmake

```cmake
# Find each component
  foreach(_comp ${TBB_SEARCH_COMPOMPONENTS})
    if(";${TBB_FIND_COMPONENTS};tbb;" MATCHES ";${_comp};")

      # Search for the libraries
      find_library(TBB_${_comp}_LIBRARY_RELEASE ${_comp}
          HINTS ${TBB_LIBRARY} ${TBB_SEARCH_DIR}
          PATHS ${TBB_DEFAULT_SEARCH_DIR} ENV LIBRARY_PATH
          PATH_SUFFIXES ${TBB_LIB_PATH_SUFFIX})

      find_library(TBB_${_comp}_LIBRARY_DEBUG ${_comp}_debug
          HINTS ${TBB_LIBRARY} ${TBB_SEARCH_DIR}
          PATHS ${TBB_DEFAULT_SEARCH_DIR} ENV LIBRARY_PATH
          PATH_SUFFIXES ${TBB_LIB_PATH_SUFFIX})

      if(TBB_${_comp}_LIBRARY_DEBUG)
        list(APPEND TBB_LIBRARIES_DEBUG "${TBB_${_comp}_LIBRARY_DEBUG}")
      endif()
      if(TBB_${_comp}_LIBRARY_RELEASE)
        list(APPEND TBB_LIBRARIES_RELEASE "${TBB_${_comp}_LIBRARY_RELEASE}")
      endif()
      if(TBB_${_comp}_LIBRARY_${TBB_BUILD_TYPE} AND NOT TBB_${_comp}_LIBRARY)
        set(TBB_${_comp}_LIBRARY "${TBB_${_comp}_LIBRARY_${TBB_BUILD_TYPE}}")
      endif()

      if(TBB_${_comp}_LIBRARY AND EXISTS "${TBB_${_comp}_LIBRARY}")
        set(TBB_${_comp}_FOUND TRUE)
      else()
        set(TBB_${_comp}_FOUND FALSE)
      endif()
      # Mark internal variables as advanced
      mark_as_advanced(TBB_${_comp}_LIBRARY_RELEASE)
      mark_as_advanced(TBB_${_comp}_LIBRARY_DEBUG)
      mark_as_advanced(TBB_${_comp}_LIBRARY) 
    endif()
  endforeach()
```

#### 查找components实质

##### 查找库

```cmake
if (WIN32)
    if(CYGWIN)
        find_library( GLFW_glfw_LIBRARY 
            NAMES
                glfw32
            HINTS
                "${GLFW_LOCATION}/lib"
                "${GLFW_LOCATION}/lib/x64"
                "$ENV{GLFW_LOCATION}/lib"
            PATHS
                "${OPENGL_LIBRARY_DIR}"
                /usr/lib
                /usr/lib/w32api
                /usr/local/lib
                "${GLFW_X11_LIB_DIRS}"
            DOC 
                "The GLFW library"
        )
    else()
        find_library( GLFW_glfw_LIBRARY
            NAMES
            # Windows中查找库的名字
                glfw32 
                glfw32s 
                glfw
                glfw3
                glfw3dll
            HINTS
                "${GLFW_LOCATION}/lib"
                "${GLFW_LOCATION}/lib/x64"
                "${GLFW_LOCATION}/lib-msvc110"
                "${GLFW_LOCATION}/lib-vc2012"
                "$ENV{GLFW_LOCATION}/lib"
                "$ENV{GLFW_LOCATION}/lib/x64"
                "$ENV{GLFW_LOCATION}/lib-msvc110"
                "$ENV{GLFW_LOCATION}/lib-vc2012"
            PATHS
                "$ENV{PROGRAMFILES}/GLFW/lib"
                "${OPENGL_LIBRARY_DIR}"
            DOC 
                "The GLFW library"
        )
    endif()
else ()
    if (APPLE)
        find_library( GLFW_glfw_LIBRARY glfw
            NAMES 
                glfw
                glfw3
            HINTS
                "${GLFW_LOCATION}/lib"
                "${GLFW_LOCATION}/lib/cocoa"
                "$ENV{GLFW_LOCATION}/lib"
                "$ENV{GLFW_LOCATION}/lib/cocoa"
            PATHS
                /usr/local/lib
        )
        set(GLFW_cocoa_LIBRARY "-framework Cocoa" CACHE STRING "Cocoa framework for OSX")
        set(GLFW_corevideo_LIBRARY "-framework CoreVideo" CACHE STRING "CoreVideo framework for OSX")
        set(GLFW_iokit_LIBRARY "-framework IOKit" CACHE STRING "IOKit framework for OSX")
    else ()
        # (*)NIX
        
        find_package(Threads REQUIRED)

        if(NOT NO_GLFW_X11)
            find_package(X11 REQUIRED)

            if(NOT X11_Xrandr_FOUND)
                message(FATAL_ERROR "Xrandr library not found - required for GLFW")
            endif()

            if(NOT X11_xf86vmode_FOUND)
                message(FATAL_ERROR "xf86vmode library not found - required for GLFW")
            endif()

            if(NOT X11_Xcursor_FOUND)
                message(FATAL_ERROR "Xcursor library not found - required for GLFW")
            endif()

            if(NOT X11_Xinerama_FOUND)
                message(FATAL_ERROR "Xinerama library not found - required for GLFW")
            endif()

            if(NOT X11_Xi_FOUND)
                message(FATAL_ERROR "Xi library not found - required for GLFW")
            endif()

            list(APPEND GLFW_x11_LIBRARY "${X11_Xrandr_LIB}" "${X11_Xxf86vm_LIB}" "${X11_Xcursor_LIB}" "${X11_Xinerama_LIB}" "${X11_Xi_LIB}" "${X11_LIBRARIES}" "${CMAKE_THREAD_LIBS_INIT}" -lrt -ldl)
        endif (NOT NO_GLFW_X11)

        find_library( GLFW_glfw_LIBRARY
            NAMES 
                glfw
                glfw3
            HINTS
                "${GLFW_LOCATION}/lib"
                "$ENV{GLFW_LOCATION}/lib"
                "${GLFW_LOCATION}/lib/x11"
                "$ENV{GLFW_LOCATION}/lib/x11"
            PATHS
                /usr/lib64
                /usr/lib
                /usr/lib/${CMAKE_LIBRARY_ARCHITECTURE}
                /usr/local/lib64
                /usr/local/lib
                /usr/local/lib/${CMAKE_LIBRARY_ARCHITECTURE}
                /usr/openwin/lib
                "${GLFW_X11_LIB_DIRS}"
            DOC 
                "The GLFW library"
        )
    endif (APPLE)
endif (WIN32)
```

* 之前编译一个库OpenSubdiv就出现了（missing GLFW_LIB,found xxx version）

  > 上面的意思就是我们已经检查到用户已经安装了某个版本的库，但是仍然显示没有查找到该定义的库，于是你就需要查找**find_library()内容以及自己真实编译的库**，比如下面的anaconda安装的库

  ![image-20210826123844225](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210826123844225.png#pic_center)

* ~~~cmake
  ```cmake
   find_library( GLFW_glfw_LIBRARY
              NAMES
              # Windows中查找库的名字,官方所提供的并没有这个名字
                  glfw32 
                  glfw32s 
                  glfw
                  glfw3
              HINTS
                  "${GLFW_LOCATION}/lib"
                  "${GLFW_LOCATION}/lib/x64"
                  "${GLFW_LOCATION}/lib-msvc110"
                  "${GLFW_LOCATION}/lib-vc2012"
                  "$ENV{GLFW_LOCATION}/lib"
                  "$ENV{GLFW_LOCATION}/lib/x64"
                  "$ENV{GLFW_LOCATION}/lib-msvc110"
                  "$ENV{GLFW_LOCATION}/lib-vc2012"
              PATHS
                  "$ENV{PROGRAMFILES}/GLFW/lib"
                  "${OPENGL_LIBRARY_DIR}"
              DOC 
                  "The GLFW library"
          )
  ```
  ~~~

##### 查找可执行程序

```cmake
find_program( RST2HTML_EXECUTABLE
    NAMES
        rst2html.py
        rst2html
        "D:/ProgramData/Miniconda3/pkgs/docutils-0.17.1-py39hcbf5309_0/Scripts/rst2html.py"
    DOC
        "The Python Docutils reStructuredText to HTML converter"
)
```

#### CMake安装变量

> 下面的变量是十分有用的变量，特别是用于程序的发布！

```cmake
CMAKE_INSTALL_PREFIX
CMAKE_INSTALL_INCLUDEDIR
CMAKE_INSTALL_BINDIR
CMAKE_INSTALL_LIIBDIR
```

##### CMake中执行shell脚本的方法

> ```cmake
> # 尽量每一个命令使用不同的脚本
> execute_process(COMMAND sh test.sh WORKING_DIRECTORY test.sh所在目录)
> ```
>
> ```cmake
> # 执行bat脚本,该命令中项目一定要有target且在同一目录
> if(WIN32)
> add_custom_command(TARGET <Your target>
>                        POST_BUILD
>                        COMMAND cmd //C <path to .bat file> <ARGS> )
> endif()
> ```
>
> 

