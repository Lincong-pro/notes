[toc]

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project(myproj LANGUAGES CXX C)
```

##### 添加option结合if else语句进行使用

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-05 LANGUAGES CXX)

# expose options to the user
option(USE_LIBRARY "Compile sources into a library" OFF)

message(STATUS "Compile sources into a library? ${USE_LIBRARY}")

include(CMakeDependentOption)

# 当USE_LIBRARY条件设置为ON的时候，MAKE_STATIC_LIBRARY设置为OFF，否则强制设置为ON
# 
# second option depends on the value of the first
cmake_dependent_option(
  MAKE_STATIC_LIBRARY "Compile sources into a static library" OFF
  "USE_LIBRARY" ON
  )
# If USE_BAR is true and USE_ZOT is false, this provides an option called USE_FOO that defaults to ON.
# Otherwise, it sets USE_FOO to OFF and hides the option from the user. If the status of USE_BAR or 
# USE_ZOT ever changes, any value for the USE_FOO option is saved so that when the option is re-enabled it retains its old value. 

# cmake_dependent_option(USE_FOO "Use Foo" ON
# "USE_BAR;NOT USE_ZOT" OFF)

# third option depends on the value of the first
# 当USE_LIBRARY为真的时候设置为ON，否则强制设置为ON
cmake_dependent_option(
  MAKE_SHARED_LIBRARY "Compile sources into a shared library" ON
  "USE_LIBRARY" ON
  )

# 下面这个值是默认的属性，当创建exe或者lib的时候初始化属性
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# list sources
# 下面的list语句就相当于把后面的hpp和cpp添加到_source变量中
list(APPEND _sources Message.hpp Message.cpp)

if(USE_LIBRARY)
  # 这么分析下来应该是使用的动态库
  message(STATUS "Compile sources into a STATIC library? ${MAKE_STATIC_LIBRARY}")
  message(STATUS "Compile sources into a SHARED library? ${MAKE_SHARED_LIBRARY}")

  if(MAKE_SHARED_LIBRARY)
    add_library(message SHARED ${_sources})

    add_executable(hello-world hello-world.cpp)

    target_link_libraries(hello-world message)
  endif()

  if(MAKE_STATIC_LIBRARY)
    add_library(message STATIC ${_sources})

    add_executable(hello-world hello-world.cpp)

    target_link_libraries(hello-world message)
  endif()
else()
  # 如果不使用库的形式就将所有的源文件编译为一个exe
  add_executable(hello-world hello-world.cpp ${_sources})
endif()

```

###### list属性的使用

```cmake
# list sources
# 下面的list语句就相当于把后面的hpp和cpp添加到_source变量中
list(APPEND _sources Message.hpp Message.cpp)
# 注意如果在下面再次使用_sources变量的时候直接使用即可（只针对于list语句 foreach语句，并不需要将其使用${_source}包含）
# 举例1
list(APPEND _source main.cpp) #此时我们的_source就含有3个源文件了
# 举例2，使用foreach循环显示我们的源文件
foreach(_source_variable IN LISTS _source)
	message(STATUS "source name is ${_source_variable}")
endforeach()
# 但是值得注意的一点就是add_library(mylib ${_source})还是需要${}的变量标准引入格式
```

###### 二次选项的使用方法

```cmake
# 要使用下面的二次选项的功能必须先include这个头文件
include(CMakeDependentOption)
# 当USE_LIBRARY条件设置为ON的时候，MAKE_STATIC_LIBRARY设置为OFF，否则强制设置为ON
# 
# second option depends on the value of the first
cmake_dependent_option(
  MAKE_STATIC_LIBRARY "Compile sources into a static library" OFF
  "USE_LIBRARY" ON
  )
# example
# If USE_BAR is true and USE_ZOT is false, this provides an option called USE_FOO that defaults to ON.
# Otherwise, it sets USE_FOO to OFF and hides the option from the user. If the status of USE_BAR or 
# USE_ZOT ever changes, any value for the USE_FOO option is saved so that when the option is re-enabled it retains its old value. 

## cmake_dependent_option(USE_FOO "Use Foo" ON
# "USE_BAR;NOT USE_ZOT" OFF)
```

###### CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS属性

```cmake
# 下面这个值是默认的属性，当创建exe或者lib的时候初始化属性
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
```

###### 将所有源文件编译为一个exe

```cmake
  # 如果不使用库的形式就将所有的源文件编译为一个exe
  add_executable(hello-world hello-world.cpp ${_sources})
```

##### 检测编译器

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-06 LANGUAGES C CXX)

message(STATUS "Is the C++ compiler loaded? ${CMAKE_CXX_COMPILER_LOADED}")
if(CMAKE_CXX_COMPILER_LOADED)
  message(STATUS "The C++ compiler ID is: ${CMAKE_CXX_COMPILER_ID}")
  if(CMAKE_COMPILER_IS_GNUCXX)
  message(STATUS "Is the C++ from GNU? ${CMAKE_COMPILER_IS_GNUCXX}")
  else()
    message(STATUS "The C++ is not from GNU!")
  endif()
    message(STATUS "The C++ compiler version is: ${CMAKE_CXX_COMPILER_VERSION}")
endif()

message(STATUS "Is the C compiler loaded? ${CMAKE_C_COMPILER_LOADED}")
if(CMAKE_C_COMPILER_LOADED)
  message(STATUS "The C compiler ID is: ${CMAKE_C_COMPILER_ID}")
  if(CMAKE_COMPILER_IS_GNUCC)
    message(STATUS "Is the C from GNU? ${CMAKE_COMPILER_IS_GNUCC}")
  else()
    message(STATUS "The C is not from GNU!")
  endif()
  message(STATUS "The C compiler version is: ${CMAKE_C_COMPILER_VERSION}")
endif()
```

###### CMAKE_CXX_COMPILER_ID的类别

```properties
Absoft = Absoft Fortran (absoft.com)
ADSP = Analog VisualDSP++ (analog.com)
AppleClang = Apple Clang (apple.com)
ARMCC = ARM Compiler (arm.com)
ARMClang = ARM Compiler based on Clang (arm.com)
Bruce = Bruce C Compiler
CCur = Concurrent Fortran (ccur.com)
Clang = LLVM Clang (clang.llvm.org)
Cray = Cray Compiler (cray.com)
Embarcadero, Borland = Embarcadero (embarcadero.com)
Flang = Flang LLVM Fortran Compiler
G95 = G95 Fortran (g95.org)
GNU = GNU Compiler Collection (gcc.gnu.org)
GHS = Green Hills Software (www.ghs.com)
HP = Hewlett-Packard Compiler (hp.com)
IAR = IAR Systems (iar.com)
Intel = Intel Compiler (intel.com)
IntelLLVM = Intel LLVM-Based Compiler (intel.com)
MSVC = Microsoft Visual Studio (microsoft.com)
NVHPC = NVIDIA HPC SDK Compiler (nvidia.com)
NVIDIA = NVIDIA CUDA Compiler (nvidia.com)
OpenWatcom = Open Watcom (openwatcom.org)
PGI = The Portland Group (pgroup.com)
PathScale = PathScale (pathscale.com)
SDCC = Small Device C Compiler (sdcc.sourceforge.net)
SunPro = Oracle Solaris Studio (oracle.com)
TI = Texas Instruments (ti.com)
TinyCC = Tiny C Compiler (tinycc.org)
XL, VisualAge, zOS = IBM XL (ibm.com)
XLClang = IBM Clang-based XL (ibm.com)
```

###### CMAKE_COMPILER_IS_GNUCXX的作用

```
True if the C++ compiler is GNU. Use CMAKE_C_COMPILER_ID instead.
CMAKE_COMPILER_IS_GNUCC的作用
CMAKE_COMPILER_IS_GNUCC
```

###### CMAKE_CXX_COMPILER_LOADED的作用

```cmake
判断是否这个语言相关的编译器是否被加载
CMAKE_C_COMPILER_LOADED
当然上面的必须和下面的语句一起使用
project(recipe-06 LANGUAGES C CXX)
```

###### build.bat中指定编译器

```shell
rmdir build /s/q
mkdir build 

pushd %VS2019_INSTALLED%\VC\Auxiliary\Build\vcvars64.bat
popd

cmake -GNinja ^
    -S . -B build ^
```

##### 切换build type类型

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-07 LANGUAGES C CXX)

# we default to Release build type
if(NOT CMAKE_BUILD_TYPE)
  # CACHE STRING的作用就是能够作为一个你能在CMake-gui设置的环境变量，Force就是强制让其在gui上显示·
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()

message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

message(STATUS "C flags, Debug configuration: ${CMAKE_C_FLAGS_DEBUG}")
message(STATUS "C flags, Release configuration: ${CMAKE_C_FLAGS_RELEASE}")
message(STATUS "C flags, Release configuration with Debug info: ${CMAKE_C_FLAGS_RELWITHDEBINFO}")
message(STATUS "C flags, minimal Release configuration: ${CMAKE_C_FLAGS_MINSIZEREL}")

message(STATUS "C++ flags, Debug configuration: ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "C++ flags, Release configuration: ${CMAKE_CXX_FLAGS_RELEASE}")
message(STATUS "C++ flags, Release configuration with Debug info: ${CMAKE_CXX_FLAGS_RELWITHDEBINFO}")
message(STATUS "C++ flags, minimal Release configuration: ${CMAKE_CXX_FLAGS_MINSIZEREL}")
```

###### CACHE STRING的用法

该方法能够使Build Type这个字作为一个选项强制在GUI界面上显示，同时Release作为一个值，Build Type作为一个键

```cmake
# 大概的用处就是CACHE STRING作为一种将该option在屏幕上进行显示，在comment后面加上FORCE选项就是强制改变之前的状态
set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
```

###### CMAKE_BUILD_TYPE的用法

```shell
# 我们完全可以在build.bat中进行外部手动设置,^是windows中手动命令换行的方法
cmake -DCMAKE_BUILD_TYPE=Release ^
# 使用这个变量需要注意的一点就是这个变量是不区分大小写的
```

###### CMAKE_BUILD_TYPE可以指定的类型

1. Debug
2. RelWithDebInfo
3. Release
4. MinSizeRel

##### CXX_FLAGS深入研究

###### CMAKE_C_FLAGS_DEBUG

```cmake
message(STATUS "C flags, Debug configuration: ${CMAKE_C_FLAGS_DEBUG}")
# 如果我们将CMAKE_C_FLAGS_DEBUG 输出，本机上的FLAGS就是（注意到我们是set(CMAKE_BUILD_TYPE Release CACHE STRING "BUILD TYPE" FORCE),然后CXX和C的FLAGS是一致的）
/MDd /Zi /Ob0 /Od /RTC1
message(STATUS "C flags, Release configuration: ${CMAKE_C_FLAGS_RELEASE}")
/MD /O2 /Ob2 /DNDEBUG
message(STATUS "C flags, Release configuration with Debug info: ${CMAKE_C_FLAGS_RELWITHDEBINFO}")
/MD /Zi /O2 /Ob1 /DNDEBUG
message(STATUS "C flags, minimal Release configuration: ${CMAKE_C_FLAGS_MINSIZEREL}")
/MD /O1 /Ob1 /DNDEBUG
# 设置为Release之后的输出,后面继续输出之后好像并没有发生改变，内容都是一致的

```

###### 为编译器全局添加flag的方式

```cmake
# Add a single flag
add_c_compiler_flags(CMAKE_C_FLAGS "-Wall")

# Add to the flags used for Release builds
add_c_compiler_flags(CMAKE_C_FLAGS_RELEASE "-fomit-frame-pointer")

# Add a list of flags
add_c_compiler_flags(CMAKE_C_FLAGS "-Wall;-Wextra;-pedantic")

# Add a long list of flags
add_c_compiler_flags(CMAKE_C_FLAGS
  -Wall
  -Wextra
  -pedantic
  -Wshadow
  -Wpointer-arith
  -Wcast-qual
  -Wcast-align
  -Wstrict-prototypes
  -Wmissing-prototypes
)
```

###### 工作中设置CXX_FLAGS和C_FLAGS的通用方法

```cmake
# https://stackoverflow.com/questions/2368811/how-to-set-warning-level-in-cmake/50882216#50882216
# 在实际中配置target的时候换为真正的target_name即可
# 版本一
if(MSVC)
  target_compile_options(${TARGET_NAME} PRIVATE /W4 /WX)
else()
  target_compile_options(${TARGET_NAME} PRIVATE -Wall -Wextra -pedantic -Werror)
endif()
# 版本二
target_compile_options(${TARGET_NAME} PRIVATE
  $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
  $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -pedantic -Werror>
)
# 示例
# 编译的target设置编译器选项，对于这种target开头的函数一般都是放在target生成之后再进行设置
# 比如target_link_libraries或者target_include_directories
target_compile_options(geometry
  PRIVATE
    ${flags}
  )
```

###### 控制compiler flag

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-08 LANGUAGES CXX)

message("C++ compiler flags: ${CMAKE_CXX_FLAGS}")

list(APPEND flags "-fPIC" "-Wall")
if(NOT WIN32)
  list(APPEND flags "-Wextra" "-Wpedantic")
endif()

# 下面的东西是我在Stack Overflow寻找到的关于cxx_flag设置的东西，设置warning 级别
if(MSVC)
  list(APPEND flags /W4 /WX)
  # target_compile_options(${TARGET_NAME} PRIVATE /W4 /WX)
else()
  list(APPEND flags -Wall -Wextra -pedantic -Werror)
  # target_compile_options(${TARGET_NAME} PRIVATE -Wall -Wextra -pedantic -Werror)
endif()


add_library(geometry
  STATIC
    geometry_circle.cpp
    geometry_circle.hpp
    geometry_polygon.cpp
    geometry_polygon.hpp
    geometry_rhombus.cpp
    geometry_rhombus.hpp
    geometry_square.cpp
    geometry_square.hpp
  )
message(STATUS "CXX flags:${flags}")
# 编译的target设置编译器选项，对于这种target开头的函数一般都是放在target生成之后再进行设置
# 比如target_link_libraries或者target_include_directories
target_compile_options(geometry
  PRIVATE
    ${flags}
  )

add_executable(compute-areas compute-areas.cpp)


## target_compile_options(compute-areas
#  PRIVATE
#     "-fPIC"
#   )

# 之所以要将上述代码注释，是因为如果不进行注释，即便我们的编译并不会出现问题，但是会产生如下警告
# 你完全可以打开sln文件去看看warning 
# 在Stack Overflow中大概的意思就是如下https://stackoverflow.com/questions/966960/what-does-fpic-mean-when-building-a-shared-library
# Warning	D9002	ignoring unknown option '-fPIC'	geometry

# 根据前面章节学到的东西，大概就是判断当前的编译器是不是GNU
if(CMAKE_COMPILER_IS_GNUCXX)	
  target_compile_options(compute-areas
    PRIVATE
    "-fPIC"
  )
endif()

target_link_libraries(compute-areas geometry)
```

###### 控制compiler flag的第二种方式

```
我们可以清楚的知道的一点就是add_compile_definitions是在target生成之前进行的操作，而target_compile_definitions是在target生成之后进行的操作。实际上add_compile_options和target_compile_options也是同样的操作。都可以用来是设置编译器的规则,但是实质有一点点不同，接下来就带你看区别。
```

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-02 LANGUAGES CXX)

# 验证一下add_compile_definitions()的作用（target_compile_definitions()）
if(MSVC)
# 但这个有个坏处就是应该所有的target都会拥有该属性
add_compile_definitions("IS_LINCONG")
# 而使用下面这种方法很好的指明某一target具有某些#define IS_WINDOWS
target_compile_definitions(hello-world PUBLIC "IS_WINDOWS")
endif()

# define executable and its source file
add_executable(hello-world hello-world.cpp)

# let the preprocessor know about the system name
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
  target_compile_definitions(hello-world PUBLIC "IS_LINUX")
endif()
if(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
  target_compile_definitions(hello-world PUBLIC "IS_MACOS")
endif()
if(CMAKE_SYSTEM_NAME STREQUAL "Windows")
  target_compile_definitions(hello-world PUBLIC "IS_WINDOWS")
endif()
```

```c++
// 对应的源文件中进行的判断
#include <cstdlib>
#include <iostream>
#include <string>

std::string say_hello() {
#ifdef IS_LINCONG
  std::cout << "Hello" << std::endl;
#endif // IS_LINCONG
#ifdef IS_WINDOWS
  return std::string("Hello from Windows!");
#elif IS_LINUX
  return std::string("Hello from Linux!");
#elif IS_MACOS
  return std::string("Hello from macOS!");
#else
  return std::string("Hello from an unknown system!");
#endif
}
int main() {
  std::cout << say_hello() << std::endl;
  return EXIT_SUCCESS;
}
```

###### -fPIC选项详解

[参考网址](https://stackoverflow.com/questions/966960/what-does-fpic-mean-when-building-a-shared-library)

```
参考网址：https://stackoverflow.com/questions/966960/what-does-fpic-mean-when-building-a-shared-library
The f is the gcc prefix for options that "control the interface conventions used in code generation"

The PIC stands for "Position Independent Code", it is a specialization of the fpic for m68K and SPARC.

This option only makes sense for shared libraries and you're telling the OS you're using a Global Offset Table, GOT. This means all your address references are relative to the GOT, and the code can be shared accross multiple processes.
```

###### 在target生成之前进行编译器定义的方法

```cmake
# 全局设置编码的写法（该方法）
set(CMAKE_CXX_FLAGS "/utf-8 ${CMAKE_CXX_FLAGS}")  # use UTF-8 source files
set(CMAKE_CXX_FLAGS "/MP ${CMAKE_CXX_FLAGS}")  # build with object level parallelism
# 第一种全局设置target生成的标准的方法（根据编译器类型进行指定option）
if (MSVC)
  # 对于这种option的设置问题一般以/开头
  add_compile_options(/std:c++17)
  add_compile_definitions(_HAS_STD_BYTE=0)
  # 设置MSVC编译器模式下解码方式为utf-8的形式，主要应用于代码中含有中文的情况（注释也会出现问题）
  add_compile_options(/utf-8)
endif()
```

CMAKE_CXX_FLAG和add_compile_option作用相似

##### 为不同的target设置独立的标准（局部）

###### 示例

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-09 LANGUAGES CXX)

# 前面已经提到，这个是CMAKE初始化target的默认设置
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# 将下面的源文件编译为动态库
add_library(animals
  SHARED
    Animal.cpp
    Animal.hpp
    Cat.cpp
    Cat.hpp
    Dog.cpp
    Dog.hpp
    Factory.hpp
  )

# 其实之前没有系统学习的时候，我只会简简单单的全局设置，比如set(CMAKE_CXX_STANDARD 14)
# 这里介绍一下全局使用CMAKE的方法
## set(CMAKE_CXX_STANDARD 14)
## set(CMAKE_CXX_STANDARD_REQUIRED ON)
## set(CMAKE_CXX_EXTENSIONS OFF)
# 你可以注意到的一点就是设置一个target的属性的方法实际上在add_library()之后才进行设置
set_target_properties(animals
  PROPERTIES 
    CXX_STANDARD 14
    CXX_EXTENSIONS OFF
    CXX_STANDARD_REQUIRED ON
    POSITION_INDEPENDENT_CODE 1
  )
# 你需要注意的一点就是在windows上并没有PIC(POSITION_INDEPENDENT_CODE的概念)
# please see this website https://github.com/BVLC/caffe/issues/5992
# set(CMAKE_POSITION_INDEPENDENT_CODE TRUE) 全局设置PIC的方法，但是实际上为Windows设置了也不影响
# 之所以windows上不适用PIC是因为它使用链接器去加载库文件，dll实际的内存地址也是由连接器解决 position实际上就被patched in

add_executable(animal-farm animal-farm.cpp)
# 同样在这里可以注意到的一点就是仍然是在我们的target生成之后才进行属性的连接的
set_target_properties(animal-farm
  PROPERTIES
    CXX_STANDARD 14
    CXX_EXTENSIONS OFF
    CXX_STANDARD_REQUIRED ON
  )

target_link_libraries(animal-farm animals)
```

###### 全局设置CXX标准的方法

```cmake
# 其实之前没有系统学习的时候，我只会简简单单的全局设置，比如set(CMAKE_CXX_STANDARD 14)
# 这里介绍一下全局使用CMAKE的方法
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
```

###### 为某个target设置标准（target属性）的方法

```cmake
# 写cmake代码的时候请注意设置target属性和生成exe文件的先后顺序，这个非常重要
add_executable(animal-farm animal-farm.cpp)
# 同样在这里可以注意到的一点就是仍然是在我们的target生成之后才进行属性的连接的
set_target_properties(animal-farm
  PROPERTIES
    CXX_STANDARD 14
    CXX_EXTENSIONS OFF
    CXX_STANDARD_REQUIRED ON
  )
```

##### cmake中的foreach语句（循环控制流）

###### 详细网址

 loop construct [`foreach-endforeach`](https://cmake.org/cmake/help/latest/command/foreach.html) in CMake.

###### foreach循环的第一种方式（IN LISTS）

```cmake
# 将下面两个源文件使用更低的flag优化（通过foreach()循环实现）
list(
  APPEND sources_with_lower_optimization
    geometry_circle.cpp
    geometry_rhombus.cpp
  )
# 使用IN LISTS foreach语法设置源文件属性
# we use the IN LISTS foreach syntax to set source properties
message(STATUS "Setting source properties using IN LISTS syntax:")
foreach(_source IN LISTS sources_with_lower_optimization)
  # 调用源文件属性设置的函数
  set_source_files_properties(${_source} PROPERTIES COMPILE_FLAGS -O2)
  message(STATUS "Appending -O2 flag for ${_source}")
endforeach()
```

###### foreach循环的第二种方式（plain foreach）

```cmake
# 使用最普通的foreach循环方式显示源文件属性
# we demonstrate the plain foreach syntax to query source properties
# which requires to expand the contents of the variable
message(STATUS "Querying sources properties using plain syntax:")
foreach(_source ${sources_with_lower_optimization})
  # 将获取到的属性放入_flags以便显示
  get_source_file_property(_flags ${_source} COMPILE_FLAGS)
  message(STATUS "Source ${_source} has the following extra COMPILE_FLAGS: ${_flags}")
endforeach()
```

###### set_source_files_properties（设置源文件的属性）

[`参考网址`](https://cmake.org/cmake/help/latest/command/set_source_files_properties.html)

```cmake
  # 调用源文件属性设置的函数
  # 参数类型为：  源文件 PROPERTIES 属性(key) 值(value)
  set_source_files_properties(${_source} PROPERTIES COMPILE_FLAGS -O2)
```

[`参考网址`](https://cmake.org/cmake/help/latest/command/get_source_file_property.html#get-source-file-property)

###### get_source_files_properties（获取源文件的属性）

```cmake
 # 将获取到的属性放入_flags以便显示
  # 参数类型为：  承载值的变量  源文件 属性
  get_source_file_property(_flags ${_source} COMPILE_FLAGS)
```

##### 胡老师2020年新生讲解中的要点内容

install dir prefixset(CMAKE_INSTALL_PREFIX ${CMAKE_CURRENT_SOURCE_DIR}/install)install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include FILES_MATCHING PATTERN "*.h")install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib/ DESTINATION lib)install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin/ DESTINATION bin)

###### 输出%CONDA_PREFIX%

```SHEll
echo %CONDA_PREFIX%
```

###### 自动拷贝依赖的dll到安装目录（超级重要）

出现这种依赖库被安装的时候并没有从依赖环境中进行拷贝相关的依赖问题只会出现在Windows平台上，在Linux中并不会出现该种问题。

```shell
# 第一步，首先激活虚拟环境，该环境为编译项目的依赖库环境
# 第二步，拷贝胡老师的qtdeploy.ps1和applocal.ps1两个powershell脚本能够在安装的时候将依赖的dll\lib文件一起拷贝到安装目录文件夹下（下面的toolchains在胡老师的AnacondaRecipes仓库下），我验证了下ninja是失败的。
cd toolchains
robocopy ./ %CONDA_PREFIX% h2o.cmake
robocopy ./ %CONDA_PREFIX%/Library/plugins applocal.ps1
robocopy ./ %CONDA_PREFIX%/Library/plugins qtdeploy.ps1
robocopy ./ %CONDA_PREFIX% applocal.ps1
# 第三步，进行项目的编译安装（切换到自己的要编译的路径下）
# 根据我自己的亲自验证，胡老师的这种做法也仅仅是将所依赖的所有的dll、lib文件放在生成的编译目录下，但是我使用cmake_install去安装的时候仍然没有帮我拷贝到安装目录下
```

###### 设置编译器节码格式

```cmake
# 第一种全局设置target生成的标准的方法（根据编译器类型进行指定option）
if (MSVC)
  # 对于这种option的设置问题一般以/开头
  add_compile_options(/std:c++17)
  add_compile_definitions(_HAS_STD_BYTE=0)
  # 设置MSVC编译器模式下解码方式为utf-8的形式，主要应用于代码中含有中文的情况（注释也会出现问题）
  add_compile_options(/utf-8)
endif()
# 胡老师采用第二种方式进行定义
if(MSVC)
	add_compile_definitions("/utf-8")
	# 此方法是在exe生成之前进行定义
endif()
```

###### 对于一个引入Qt5的新认识

```
find
```

#### componets如何寻找

###### conda库安装的位置

1. 动态库：%CONDA_PREFIX%/Library/bin
2. 静态库：%CONDA_PREFIX%/Library/lib
3. 头文件：%CONDA_PREFIX%/Library/include，该头文件下面应该是我们所需要的所有模块，CMakeLists.txt的写法也可以参照这个。

###### Qt链接的示例代码

```cmake
# 寻找到指定模块
find_package(Qt5 CONFIG REQUIRED 
    COMPONENTS Widgets 
        Core 
        Gui
        Network
        3DQuick)
# 引入Qt库的lib dll文件
target_link_libraries(${PROJECT_NAME} 
    Qt5::Widgets 
    Qt5::Core
    Qt5::Gui
    Qt5::Network
    Qt5::3DQuick)
```

###### Boost链接的示例代码

```cmake
find_package(Boost 1.72.0 REQUIRED COMPONENTS 
    program_options
    filesystem
    graph
    regex
    system
    unit_test_framework)
 # 引入Boost库的头文件 和 GDAL的头文件
target_include_directories(${PROJECT_NAME} 
    PUBLIC ${Boost_INCLUDE_DIR} ${GDAL_INCLUDE_DIR}
)
# 引入Boost库的lib文件 和 GDAL的lib文件
target_link_libraries(lincong ${Boost_LIBRARIES} ${GDAL_LIBRARY})
# 至于上面的Boost库的两个变量名怎么来的，你可以直接在FindBoost.cmake中进行寻找这两个变量，应该在comment中明确写道了的
```

###### Qt和Boost的COMPONENTS的疑问

​		不知道你是否会有这个疑问，反正我疑问挺多的，我怎么知道这个components有哪一些呢？上一次我曾经问过胡老师，他给我的回答真的是说这个是什么标准模板写法，貌似在给我介绍components的作用，实际上我是想问后面的奇奇怪怪的components我该如何得到？

```cmake
实际上根据conda库安装的位置，我总结出了，后面这些模块名可以通过include下的目录找到，不知道你还有没有印象，头文件：%CONDA_PREFIX%/Library/include。点击进去选择qt或者Boost文件夹，下面就是一堆components，意味着这些所有的文件夹都是一个模块名，模块名在Qt中使用 Qt::模块名 的方法进行链接，Boost直接就是自定义的变量。之所以要在后面components的指定，很大一部分原因就是我可以排除掉一些我不想引入的模块，这样我们自己写的程序在安装的时候就不需要安装一大堆dll。
```

##### file函数的作用

###### 查找CMAKE_CURRENT_BINARY_DIR所在文件夹下匹配文件

##### 根据操作系统和编译器分别进行代码执行

###### CMAKE_SYSTEM_NAME返回STR类型

上面这个值是一个字符串，用其和Linux\Windows\Darwin\AIX做比较判断该电脑是什么类型的操作系统

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name, in this case no language required
project(recipe-01 LANGUAGES NONE)

# print custom message depending on the operating system
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
  message(STATUS "Configuring on/for Linux")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
  message(STATUS "Configuring on/for macOS")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
  message(STATUS "Configuring on/for Windows")
elseif(CMAKE_SYSTEM_NAME STREQUAL "AIX")
  message(STATUS "Configuring on/for IBM AIX")
else()
  message(STATUS "Configuring on/for ${CMAKE_SYSTEM_NAME}")
endif()
```

###### 根据不同的编译器执行不同部分的代码

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-03 LANGUAGES CXX)

# define executable and its source file
add_executable(hello-world hello-world.cpp)
# 下面的diamagnetic相当于执行了#define COMPILER_NAME="MSVC" 宏定义而已
target_compile_definitions(hello-world PUBLIC "COMPILER_NAME=\"${CMAKE_CXX_COMPILER_ID}\"")

# let the preprocessor know about the compiler vendor
if(CMAKE_CXX_COMPILER_ID MATCHES Intel)
  target_compile_definitions(hello-world PUBLIC "IS_INTEL_CXX_COMPILER")
endif()
if(CMAKE_CXX_COMPILER_ID MATCHES GNU)
  target_compile_definitions(hello-world PUBLIC "IS_GNU_CXX_COMPILER")
endif()
if(CMAKE_CXX_COMPILER_ID MATCHES PGI)
  target_compile_definitions(hello-world PUBLIC "IS_PGI_CXX_COMPILER")
endif()
if(CMAKE_CXX_COMPILER_ID MATCHES XL)
  target_compile_definitions(hello-world PUBLIC "IS_XL_CXX_COMPILER")
endif()
if(CMAKE_CXX_COMPILER_ID MATCHES MSVC)
  target_compile_definitions(hello-world PUBLIC "IS_MSVC_CXX_COMPILER")
endif()

# etc ...
```

```c++
#include <cstdlib>
#include <iostream>
#include <string>

std::string say_hello() {
#ifdef IS_INTEL_CXX_COMPILER
  // only compiled when Intel compiler is selected
  // such compiler will not compile the other branches
  return std::string("Hello Intel compiler!");
#elif IS_GNU_CXX_COMPILER
  // only compiled when GNU compiler is selected
  // such compiler will not compile the other branches
  return std::string("Hello GNU compiler!");
#elif IS_PGI_CXX_COMPILER
  // etc.
  return std::string("Hello PGI compiler!");
#elif IS_XL_CXX_COMPILER
  return std::string("Hello XL compiler!");
#elif IS_MSVC_CXX_COMPILER
  return std::string("Hello MSVC compiler!");
#else
  return std::string("Hello unknown compiler - have we met before?");
#endif
}

int main() {
  std::cout << say_hello() << std::endl;
  //编译器名字输出就是之前CMake宏定义中的内容
  std::cout << "compiler name is " COMPILER_NAME << std::endl;
  return EXIT_SUCCESS;
}
```

###### 检测主机架构执行不同代码块

```
CMAKE_HOST_SYSTEM_PROCESSOR获取主机处理器架构的属性，返回的是str类型，使用MATCHES进行操作
其值有
 1.i386
 2.i686
 3.x86_64
```

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-04 LANGUAGES CXX)

# define executable and its source file
add_executable(arch-dependent arch-dependent.cpp)

# 方法一
# let the preprocessor know about the size of void *
# 根据空指针的大小判断架构的类型（32位机器还是64位机器）
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
  # 如果成立就执行 #define IS_64_BIT_ARCH
  target_compile_definitions(arch-dependent PUBLIC "IS_64_BIT_ARCH")
  message(STATUS "Target is 64 bits")
else()
  # 如果成立就执行 #define IS_32_BIT_ARCH
  target_compile_definitions(arch-dependent PUBLIC "IS_32_BIT_ARCH")
  message(STATUS "Target is 32 bits")
endif()

# 方法二
# let the preprocessor know about the host processor architecture
if(CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "i386")
  message(STATUS "i386 architecture detected")
elseif(CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "i686")
  message(STATUS "i686 architecture detected")
elseif(CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "x86_64")
  message(STATUS "x86_64 architecture detected")
else()
  message(STATUS "host processor architecture is unknown")
endif()

# 宏定义架构#define ARCHITECTURE=${CMAKE_HOST_SYSTEM_PROCESSOR}
target_compile_definitions(arch-dependent
  PUBLIC "ARCHITECTURE=${CMAKE_HOST_SYSTEM_PROCESSOR}"
  )

```

```c++
#include <cstdlib>
#include <iostream>
#include <string>

#define STRINGIFY(x) #x
#define TOSTRING(x) STRINGIFY(x)

std::string say_hello() {
  std::string arch_info(TOSTRING(ARCHITECTURE));
  arch_info += std::string(" architecture. ");
#ifdef IS_32_BIT_ARCH
  return arch_info + std::string("Compiled on a 32 bit host processor.");
#elif IS_64_BIT_ARCH
  return arch_info + std::string("Compiled on a 64 bit host processor.");
#else
  return arch_info + std::string("Neither 32 nor 64 bit, puzzling ...");
#endif
}

int main() {
  std::cout << say_hello() << std::endl;
  return EXIT_SUCCESS;
}
```

###### 获取到主机的硬件相关之类的信息并通过配置文件显示

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.10 FATAL_ERROR)

# project name and language
project(recipe-05 LANGUAGES CXX)

# define executable
add_executable(processor-info "")

# and its source file
target_sources(processor-info
  PRIVATE
    processor-info.cpp
  )

# and its include directories
target_include_directories(processor-info
  PRIVATE
    ${PROJECT_BINARY_DIR}
  )

foreach(key
  IN ITEMS
    NUMBER_OF_LOGICAL_CORES
    NUMBER_OF_PHYSICAL_CORES
    TOTAL_VIRTUAL_MEMORY
    AVAILABLE_VIRTUAL_MEMORY
    TOTAL_PHYSICAL_MEMORY
    AVAILABLE_PHYSICAL_MEMORY
    IS_64BIT
    HAS_FPU
    HAS_MMX
    HAS_MMX_PLUS
    HAS_SSE
    HAS_SSE2
    HAS_SSE_FP
    HAS_SSE_MMX
    HAS_AMD_3DNOW
    HAS_AMD_3DNOW_PLUS
    HAS_IA64
    OS_NAME
    OS_RELEASE
    OS_VERSION
    OS_PLATFORM
  )
  # cmake_host_system_information(RESULT <variable> QUERY <key> ...)
  # 这个函数就是查询主机系统的指定信息
  cmake_host_system_information(RESULT _${key} QUERY ${key})
endforeach()
# 其实下面我写的语句仍然能够访问到主机的内存量，实际上就是通过cmake的函数获取到foreach中的ITEM中，然后config.h.in再通过@_TOTAL_PHYSICAL_MEMORY@获取到对应的_TOTAL_PHYSICAL_MEMORY的值
message(STATUS "TOTAL_PHYSICAL_MEMORY:${_TOTAL_PHYSICAL_MEMORY}")
# 添加配置文件，实际上就是target_compile_definitions多定义了几个宏而已
configure_file(config.h.in config.h @ONLY)
```

##### Eigen Library向量优化

###### 2-6示例代码

如果电脑支持Windows电脑支持 -xHost就进行优化，优化之后的Eigen库运行效率相对更快

```cmake
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
# 这个项目的目的就是为了解决时间花费的问题，分别比较优化版本和非优化版本所耗费的
project(recipe-06 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 这个教程竟然还教你配置Eigen库
# find_package的语法 库 最低版本 强制要求(如果不满足就报错) 
find_package(Eigen3 3.3 REQUIRED CONFIG)

# 要使用编译器检查必须引入这个头文件路径
# 引入的是CheckCXXCompilerFlag.cmake标准模块文件
include(CheckCXXCompilerFlag)

# 检查 -march=nativate 编译器标志是否有效 -------- 用于GNU
check_cxx_compiler_flag("-march=native" _march_native_works)
# 检查 -xHost 编译器标志是否有效 ------- 用于Windows
check_cxx_compiler_flag("-xHost" _xhost_works)

# 我们设置了一个空变量，即_CXX_FLAGS，来保存我们刚检查过的两个编译器中的一个编译器标志。
# 如果我们看到_march_native_works，我们将_CXX_FLAGS设置为-march=native。
# 如果我们看到xhost_works，我们将_CXX_FLAGS设置为-xHost。如果它们都不起作用，我们将保留_CXX_FLAGS为空，并且矢量化将被禁用：
set(_CXX_FLAGS)
if(_march_native_works)
  message(STATUS "Using processor's vector instructions (-march=native compiler flag set)")
  set(_CXX_FLAGS "-march=native")
elseif(_xhost_works)
  message(STATUS "Using processor's vector instructions (-xHost compiler flag set)")
  set(_CXX_FLAGS "-xHost")
else()
  message(STATUS "No suitable compiler flag found for vectorization")
endif()
# Eigen非优化的版本
add_executable(linear-algebra-unoptimized linear-algebra.cpp)

target_link_libraries(linear-algebra-unoptimized
  PRIVATE
    Eigen3::Eigen
  )
# Eigen优化的版本
add_executable(linear-algebra linear-algebra.cpp)

target_compile_options(linear-algebra
  PRIVATE
    ${_CXX_FLAGS}
  )
# 私有继承于Eigen库
target_link_libraries(linear-algebra
  PRIVATE
    Eigen3::Eigen
  )
```

###### 设置空变量然后保存后续值的方法

```cmake
# 我们设置了一个空变量，即_CXX_FLAGS，来保存我们刚检查过的两个编译器中的一个编译器标志。
# 如果我们看到_march_native_works，我们将_CXX_FLAGS设置为-march=native。
# 如果我们看到xhost_works，我们将_CXX_FLAGS设置为-xHost。如果它们都不起作用，我们将保留_CXX_FLAGS为空，并且矢量化将被禁用：
set(_CXX_FLAGS)
if(_march_native_works)
  message(STATUS "Using processor's vector instructions (-march=native compiler flag set)")
  set(_CXX_FLAGS "-march=native")
elseif(_xhost_works)
  message(STATUS "Using processor's vector instructions (-xHost compiler flag set)")
  set(_CXX_FLAGS "-xHost")
else()
  message(STATUS "No suitable compiler flag found for vectorization")
endif()
```

##### 检测Python编译器的方法

###### 3-1示例代码

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-01 LANGUAGES NONE)

# detect python
# 固定的语句找到python.cmake之类的（虚拟环境中的东西）
find_package(PythonInterp REQUIRED)

# Execute a tiny Python script
# 执行python脚本，其中的 RESULT_VARIABLE / OUTPUT_VARIABLE / ERROR_QUIET / OUTPUT_STRIP_TRAILING_WHITESPACE
# 是execute_process 函数的相关变量，RESULT_VARIABLE输出执行状态信息 OUTPUT_VARIABLE代表输出信息
execute_process(
  COMMAND
    ${PYTHON_EXECUTABLE} "-c" "print('Hello, world!')"
  RESULT_VARIABLE _status
  OUTPUT_VARIABLE _hello_world
  ERROR_QUIET
  OUTPUT_STRIP_TRAILING_WHITESPACE
  )

message(STATUS "RESULT_VARIABLE is: ${_status}")
message(STATUS "OUTPUT_VARIABLE is: ${_hello_world}")

# compare the "manual" messages with the following handy helper
# 这个作用就是引入CMakePrintHelpers模块然后按照键值对的方式输出格式
include(CMakePrintHelpers)
cmake_print_variables(_status _hello_world)
```

###### 查找虚拟环境中是否存在python

```cmake
# detect python
# 固定的语句找到python.cmake之类的（虚拟环境中的东西）
find_package(PythonInterp REQUIRED)
```

###### 调用CMake模块进行变量标准化输出

```cmake
# compare the "manual" messages with the following handy helper
# 这个作用就是引入CMakePrintHelpers模块然后按照键值对的方式输出格式
include(CMakePrintHelpers)
cmake_print_variables(_status _hello_world)
```

###### 使用cpython进行编译（内嵌式的python）

```cmake
# C语言的标准为C 99，下面的语句是标准的全局指明模式的方法

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_EXTENSIONS OFF)
set(CMAKE_C_STANDARD_REQUIRED ON)

# 找到环境中的python编译器

find_package(PythonInterp REQUIRED)

# 下面这个语句是指定固定的python版本（但是这里又是用的cmake系统的环境变量，所以肯定并不会出问题）

# FIND_PACKAGE(PythonLibs ${ARGN})

# MESSAGE(STATUS "PythonLibraries: ${PYTHON_LIBRARIES}")

# IF (NOT ${PYTHONLIBS_FOUND} STREQUAL TRUE)

#    MESSAGE(FATAL_ERROR "Python has not been found.")

# ENDIF (NOT ${PYTHONLIBS_FOUND} STREQUAL TRUE)

# STRING(REPLACE "." ";" _PYTHONLIBS_VERSION ${PYTHONLIBS_VERSION_STRING})

# LIST(GET _PYTHONLIBS_VERSION 0 PYTHONLIBS_VERSION_MAJOR)

# LIST(GET _PYTHONLIBS_VERSION 1 PYTHONLIBS_VERSION_MINOR)

# IF (NOT ${PYTHON_VERSION_MAJOR} EQUAL ${PYTHONLIBS_VERSION_MAJOR} OR

#     NOT ${PYTHON_VERSION_MINOR} EQUAL ${PYTHONLIBS_VERSION_MINOR})

#   MESSAGE(FATAL_ERROR "Python interpreter and libraries are in different version: ${PYTHON_VERSION_STRING} vs ${PYTHONLIBS_VERSION_STRING}")

# ENDIF (NOT ${PYTHON_VERSION_MAJOR} EQUAL ${PYTHONLIBS_VERSION_MAJOR} OR

#        NOT ${PYTHON_VERSION_MINOR} EQUAL ${PYTHONLIBS_VERSION_MINOR})

# 您可以将下面的奇怪的语句直接搜索就可以得到相关的Python库库的寻找方式

message(STATUS "Python的大版本：${PYTHON_VERSION_MAJOR}")
message(STATUS "Python的小版本：${PYTHON_VERSION_MINOR}")

# 找到确定的版本 EXACT REQUIRED

find_package(PythonLibs ${PYTHON_VERSION_MAJOR}.${PYTHON_VERSION_MINOR} EXACT REQUIRED)

add_executable(hello-embedded-python hello-embedded-python.c)

# 在target生成之后链接头文件

target_include_directories(hello-embedded-python
  PRIVATE
    ${PYTHON_INCLUDE_DIRS}
  )

# 在target生成之后链接库文件

target_link_libraries(hello-embedded-python
  PRIVATE
    ${PYTHON_LIBRARIES}
  )
```

###### cpython的示例代码

```c
#include <Python.h>

// 下面这个就是CPython的写法
int main(int argc, char *argv[]) {
  Py_SetProgramName(argv[0]); /* optional but recommended */
  Py_Initialize();
  PyRun_SimpleString("from time import time,ctime\n"
                     "print('Today is', ctime(time()))\n");
  Py_Finalize();
  return 0;
}
```

###### 包模块的处理方法(find_package)的实质

```cmake
# 检查环境中是否有Numpy,通过Python命令行和execute_process命令打印numpy位置
# Find NumPy location
execute_process(
  COMMAND
    ${PYTHON_EXECUTABLE} "-c" "import re, numpy; print(re.compile('/__init__.py.*').sub('',numpy.__file__))"
  RESULT_VARIABLE _numpy_status
  OUTPUT_VARIABLE _numpy_location
  ERROR_QUIET
  OUTPUT_STRIP_TRAILING_WHITESPACE
  )
#没有报错，就打印numpy位置
if(NOT _numpy_status)
  # 在gui中显示Location of Numpy选项
  set(NumPy ${_numpy_location} CACHE STRING "Location of NumPy")
endif()
# 通过numpy.___version__获取numpy版本
# Find NumPy version
execute_process(
  COMMAND
    ${PYTHON_EXECUTABLE} "-c" "import numpy; print(numpy.__version__)"
  OUTPUT_VARIABLE _numpy_version
  ERROR_QUIET
  OUTPUT_STRIP_TRAILING_WHITESPACE
  )
# 包模块的处理，下面就是我们的find_package的内容，Found Numpy;numpy的位置；numpy的版本，正常情况下你只需要按照这个模板进行替换即可
include(FindPackageHandleStandardArgs)
find_package_handle_standard_args(NumPy
  FOUND_VAR NumPy_FOUND
  REQUIRED_VARS NumPy
  VERSION_VAR _numpy_version
  )
```

##### 自定义命令函数的使用

###### 案例一

```cmake
# 进行文件的拷贝（其实后面的文件安装也是用了这个）
add_custom_command(
  # 指定输入文件
  OUTPUT
    ${CMAKE_CURRENT_BINARY_DIR}/use_numpy.py
  # 下面可以有多个COMMAND命令
  COMMAND
  # 下面这个语句使用cmake -E copy_if_different 源文件 目标文件进行拷贝
  # 下面指明了-E是CMake的命令模式，你只需要cmake --help即可观察到
    ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_CURRENT_SOURCE_DIR}/use_numpy.py
                                          ${CMAKE_CURRENT_BINARY_DIR}/use_numpy.py
  # 指定必须下面的依赖项存在
  DEPENDS
    ${CMAKE_CURRENT_SOURCE_DIR}/use_numpy.py
  )
```

###### 案例二

[`参考网址`](https://www.jianshu.com/p/e6552ac81a7f)

```cmake
set(THRIFT_SRCS
    ${PROJECT_SOURCE_DIR}/thrift/xxx.h
    ${PROJECT_SOURCE_DIR}/thrift/xxx.cpp
    ...)
#定义生成命令，注意将skeleton.cpp文件要删除的
add_custom_command(
    OUTPUT ${THRIFT_SRCS}
    COMMAND thrift --gen cpp ${PROJECT_SOURCE_DIR}/thrift/my_server.thrift -out ${PROJECT_SOURCE_DIR}/thrift
    COMMAND rm -f {PRJJECT_SOURCE_DIR}/thrift/xxx.skeleton.cpp
    DEPENDS ${PROJECT_SOURCE_DIR}/thrift/my_server.thrift
    COMMENT "Now Generating thrift files..."
)
```

##### 给exe添加源文件进行编译的方法

```cmake
add_executable(pure-embedding "")
# 即便我们普通的认为源文件必须要有cpp，但是也可以通过target_sources方法去赋值
target_sources(pure-embedding
  PRIVATE
    Py${PYTHON_VERSION_MAJOR}-pure-embedding.cpp
  )
# 将exe私有连接python头文件
target_include_directories(pure-embedding
  PRIVATE
    ${PYTHON_INCLUDE_DIRS}
  )
# 将exe私有连接python库
target_link_libraries(pure-embedding
  PRIVATE
    ${PYTHON_LIBRARIES}
  )
# 进行文件的拷贝（其实后面的文件安装也是用了这个）
add_custom_command(
  # 指定输入文件
  OUTPUT
    ${CMAKE_CURRENT_BINARY_DIR}/use_numpy.py
  # 下面可以有多个COMMAND命令
  COMMAND
  # 下面这个语句使用cmake -E copy_if_different 源文件 目标文件昂是进行拷贝
  # 下面指明了-E是CMake的命令模式，你只需要cmake --help即可观察到
    ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_CURRENT_SOURCE_DIR}/use_numpy.py
                                          ${CMAKE_CURRENT_BINARY_DIR}/use_numpy.py
  # 指定必须下面的依赖项存在
  DEPENDS
    ${CMAKE_CURRENT_SOURCE_DIR}/use_numpy.py
  )
# 构造一个完全嵌入式的
# make sure building pure-embedding triggers the above
# custom command
target_sources(pure-embedding
  PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}/use_numpy.py
  )
```

##### OpenMP库多线程支持

###### OpenMP介绍

**OpenMP**（Open Multi-Processing）是一套支持跨平台[共享内存](https://zh.wikipedia.org/wiki/共享内存)方式的多线程并发的编程[API](https://zh.wikipedia.org/wiki/API)，使用[C](https://zh.wikipedia.org/wiki/C),[C++](https://zh.wikipedia.org/wiki/C%2B%2B)和[Fortran](https://zh.wikipedia.org/wiki/Fortran)语言，可以在大多数的处理器体系和操作系统中运行，包括[Solaris](https://zh.wikipedia.org/wiki/Solaris), [AIX](https://zh.wikipedia.org/wiki/AIX), [HP-UX](https://zh.wikipedia.org/wiki/HP-UX), [GNU/Linux](https://zh.wikipedia.org/wiki/GNU/Linux), [Mac OS X](https://zh.wikipedia.org/wiki/Mac_OS_X), 和[Microsoft Windows](https://zh.wikipedia.org/wiki/Microsoft_Windows)。包括一套编译器指令、库和一些能够影响运行行为的环境变量。

###### OpenMP的引入方法

```cmake
# 寻找OpenMP的库依赖(这个库是一个多线程并发比较有用的一个库)
find_package(OpenMP REQUIRED)

add_executable(example example.cpp)
# 找到之后链接OpenMP_CXX模块
# 你也许会问OpenMP_CXX模块怎么找到的，为什么会这么写，那必然是FindOpenMP.cmake中详细介绍的
# The module exposes the components ``C``, ``CXX``, and ``Fortran``.
# Each of these controls the various languages to search OpenMP support for.
# 上述名字简述了库的模块
##   if(NOT TARGET OpenMP::OpenMP_${LANG})
##   add_library(OpenMP::OpenMP_${LANG} INTERFACE IMPORTED)
## endif()
## if(OpenMP_${LANG}_FLAGS)
##   separate_arguments(_OpenMP_${LANG}_OPTIONS NATIVE_COMMAND "${OpenMP_${LANG}_FLAGS}")
##   set_property(TARGET OpenMP::OpenMP_${LANG} PROPERTY
#     INTERFACE_COMPILE_OPTIONS "$<$<COMPILE_LANGUAGE:${LANG}>:${_OpenMP_${LANG}_OPTIONS}>")
##   unset(_OpenMP_${LANG}_OPTIONS)
## endif()
## if(OpenMP_${LANG}_INCLUDE_DIRS)
##   set_property(TARGET OpenMP::OpenMP_${LANG} PROPERTY
#     INTERFACE_INCLUDE_DIRECTORIES "$<BUILD_INTERFACE:${OpenMP_${LANG}_INCLUDE_DIRS}>")
## endif()
## if(OpenMP_${LANG}_LIBRARIES)
##   set_property(TARGET OpenMP::OpenMP_${LANG} PROPERTY
#     INTERFACE_LINK_LIBRARIES "${OpenMP_${LANG}_LIBRARIES}")
## endif()
target_link_libraries(example
  PUBLIC
    OpenMP::OpenMP_CXX
  )
# 下面这个变量你问我怎么知道的，那必然是直接通过FindOpenMP.cmake中所描述进行输出（相当于调用了别人现成的变量而已）
message(STATUS "${OpenMP_CXX_VERSION_MAJOR}")
# 设置编译器编译的规则-openmp
target_compile_options(example
  PUBLIC
    ${OpenMP_CXX_FLAGS}
  )
# 设置库链接规则-openmp
set_target_properties(example
  PROPERTIES
    LINK_FLAGS ${OpenMP_CXX_FLAGS}
  )
# 上面的设置完毕之后就会在C/C++的Language一个的OpenMP选项中的value中显示（Yes/openmp）默认就是exe会添加openmp标志
```

##### MPI库多线程支持

###### MPI介绍

与OpenMP并行程序不同，MPI是一种基于信息传递的并行编程技术。消息传递接口是一种编程接口标准，而不是一种具体的编程语言。简而言之，MPI标准定义了一组具有可移植性的编程接口。

###### MPI引入

##### Blas库

###### Blas库的安装

```
conda install -c conda-forge -n xxx_env openblas
```

###### 在Eigen库中嵌入BLAS

记住Blas模块和和lapack模块都存在于openblas中

```cmake
# 开启OpenMP支持
find_package(OpenMP REQUIRED)
# 开启Eigen支持
find_package(Eigen3 3.3 REQUIRED CONFIG)
# if查找到target，就打印版本和包含目录
if(TARGET Eigen3::Eigen)
  message(STATUS "Eigen3 v${EIGEN3_VERSION_STRING} found in ${EIGEN3_INCLUDE_DIR}")
endif()

add_executable(linear-algebra linear-algebra.cpp)

# 对于blas库这个环境，其库名为openblas
find_package(BLAS)

if(BLAS_FOUND)
  message(STATUS "Eigen will use some subroutines from BLAS.")
  message(STATUS "See: http://eigen.tuxfamily.org/dox-devel/TopicUsingBlasLapack.html")
  # 让eigen库使用blas
  target_compile_definitions(linear-algebra
    PRIVATE
      EIGEN_USE_BLAS
    )
  target_link_libraries(linear-algebra
    PUBLIC
      ${BLAS_LIBRARIES}
    )
else()
  message(STATUS "BLAS not found. Using Eigen own functions")
endif()

target_link_libraries(linear-algebra
  PUBLIC
    Eigen3::Eigen
    OpenMP::OpenMP_CXX
  )
```

##### Boost库

###### Boost库的安装

```cmake
Boost库一般具有Boost和Boost-cpp两个组成部分，但是你只需conda isntall Boost后cmake会自动寻找到相关的依赖
```

##### find_package实质

###### 添加自定义模块所在的路径（.cmake文件所在的位置）

```cmake
# 添加一个自己所写的模块路径（注意这个CMAKE_MODULE_PATH需要特别注意一下，它并没有被${}包裹）
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR})
```

###### 使用find_package函数查找自定义模块所在路径

其中的ZeroMQ ZeroMQ_FOUND ZeroMQ_INCLUDE_DIRS ZeroMQ_LIBRARIES都是自定义的选项

```cmake
# 查找自定义的模块
find_package(ZeroMQ REQUIRED)
if(ZeroMQ_FOUND)
add_executable(hwserver hwserver.c)

# 头目录
target_include_directories(hwserver
  PRIVATE
    ${ZeroMQ_INCLUDE_DIRS}
  )
# 库目录
target_link_libraries(hwserver
  PRIVATE
    ${ZeroMQ_LIBRARIES}
  )

add_executable(hwclient hwclient.c)

target_include_directories(hwclient
  PRIVATE
    ${ZeroMQ_INCLUDE_DIRS}
  )

target_link_libraries(hwclient
  PRIVATE
    ${ZeroMQ_LIBRARIES}
  )
```

###### 自定义模块配置文件

```cmake
# Find ZeroMQ Headers/Libs
# Adapted from: https://github.com/zeromq/azmq/blob/master/config/FindZeroMQ.cmake

# Variables
# ZeroMQ_ROOT - set this to a location where ZeroMQ may be found
#
# ZeroMQ_FOUND - True of ZeroMQ found
# ZeroMQ_INCLUDE_DIRS - Location of ZeroMQ includes
# ZeroMQ_LIBRARIES - ZeroMQ libraries

if(NOT ZeroMQ_ROOT)
  set(ZeroMQ_ROOT "$ENV{ZeroMQ_ROOT}")
endif()

if(NOT ZeroMQ_ROOT)
  find_path(_ZeroMQ_ROOT NAMES include/zmq.h)
else()
  set(_ZeroMQ_ROOT "${ZeroMQ_ROOT}")
endif()

find_path(ZeroMQ_INCLUDE_DIRS NAMES zmq.h HINTS ${_ZeroMQ_ROOT}/include)

if(ZeroMQ_INCLUDE_DIRS)
  set(_ZeroMQ_H ${ZeroMQ_INCLUDE_DIRS}/zmq.h)

  function(_zmqver_EXTRACT _ZeroMQ_VER_COMPONENT _ZeroMQ_VER_OUTPUT)
    set(CMAKE_MATCH_1 "0")
    set(_ZeroMQ_expr "^[ \\t]*#define[ \\t]+${_ZeroMQ_VER_COMPONENT}[ \\t]+([0-9]+)$")
    file(STRINGS "${_ZeroMQ_H}" _ZeroMQ_ver REGEX "${_ZeroMQ_expr}")
    string(REGEX MATCH "${_ZeroMQ_expr}" ZeroMQ_ver "${_ZeroMQ_ver}")
    set(${_ZeroMQ_VER_OUTPUT} "${CMAKE_MATCH_1}" PARENT_SCOPE)
  endfunction()

  _zmqver_EXTRACT("ZMQ_VERSION_MAJOR" ZeroMQ_VERSION_MAJOR)
  _zmqver_EXTRACT("ZMQ_VERSION_MINOR" ZeroMQ_VERSION_MINOR)
  _zmqver_EXTRACT("ZMQ_VERSION_PATCH" ZeroMQ_VERSION_PATCH)

  # We should provide version to find_package_handle_standard_args in the same format as it was requested,
  # otherwise it can't check whether version matches exactly.
  if(ZeroMQ_FIND_VERSION_COUNT GREATER 2)
    set(ZeroMQ_VERSION "${ZeroMQ_VERSION_MAJOR}.${ZeroMQ_VERSION_MINOR}.${ZeroMQ_VERSION_PATCH}")
  else()
    # User has requested ZeroMQ version without patch part => user is not interested in specific patch =>
    # any patch should be an exact match.
    set(ZeroMQ_VERSION "${ZeroMQ_VERSION_MAJOR}.${ZeroMQ_VERSION_MINOR}")
  endif()

  if(NOT ${CMAKE_C_PLATFORM_ID} STREQUAL "Windows")
    find_library(ZeroMQ_LIBRARIES 
        NAMES 
          zmq 
        HINTS 
          ${_ZeroMQ_ROOT}/lib
          ${_ZeroMQ_ROOT}/lib/x86_64-linux-gnu
        )
  else()
    find_library(ZeroMQ_LIBRARIES
        NAMES
          libzmq
          "libzmq-mt-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"
          "libzmq-${CMAKE_VS_PLATFORM_TOOLSET}-mt-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"
          libzmq_d
          "libzmq-mt-gd-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"
          "libzmq-${CMAKE_VS_PLATFORM_TOOLSET}-mt-gd-${ZeroMQ_VERSION_MAJOR}_${ZeroMQ_VERSION_MINOR}_${ZeroMQ_VERSION_PATCH}"
        HINTS
          ${_ZeroMQ_ROOT}/lib
        )
  endif()
endif()
# 引入标准库的 find_package模块
include(FindPackageHandleStandardArgs)
# 输出zeromq的头文件路径的库文件路径，以及版本
find_package_handle_standard_args(ZeroMQ
  # 下面就是自定义模块别人调用的时候是否找到我们的包的标志 
  # 我们通常使用if(ZeroMQ_FOUND)判断是否找到，找到的前提就是ZeroMQ_INCLUDE_DIRS ZeroMQ_LIBRARIES必须有值
  # 只要找到，该函数应该就会隐式调用某个函数进行打印
  FOUND_VAR
    ZeroMQ_FOUND
  REQUIRED_VARS
    ZeroMQ_INCLUDE_DIRS
    ZeroMQ_LIBRARIES
  VERSION_VAR
    ZeroMQ_VERSION
  )
```

##### 胡老师测试模块的编写思路

###### 测试函数部分(学生自己编写)

```c++
#include "sum_integers.hpp"

#include <vector>

int sum_integers(const std::vector<int> integers) {
  auto sum = 0;
  for (auto i : integers) {
    sum += i;
  }
  return sum;
}
```

###### 学生用于调试的部分

```C++
#include "sum_integers.hpp"

#include <iostream>
#include <string>
#include <vector>

// we assume all arguments are integers and we sum them up
// for simplicity we do not verify the type of arguments
int main(int argc, char *argv[]) {

  std::vector<int> integers;
  for (auto i = 1; i < argc; i++) {
    integers.push_back(std::stoi(argv[i]));
  }
  auto sum = sum_integers(integers);

  std::cout << sum << std::endl;
}
```

###### 服务器上运行的部分

```c++
#include "sum_integers.hpp"

// this tells catch to provide a main()
// only do this in one cpp file
#define CATCH_CONFIG_MAIN
#include "catch2/catch.hpp"

#include <vector>

TEST_CASE("Sum of integers for a short vector", "[short]") {
  auto integers = {1, 2, 3, 4, 5};
  //测试数组的函数部分是否逻辑正确
  REQUIRE(sum_integers(integers) == 15);
}

TEST_CASE("Sum of integers for a longer vector", "[long]") {
  std::vector<int> integers;
  for (int i = 1; i < 1001; ++i) {
    integers.push_back(i);
  }
  //测试vector的函数部分是否逻辑正确
  REQUIRE(sum_integers(integers) == 500500);
}
```

###### CMakeLists中引入Catch2

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-02 LANGUAGES CXX)

# require C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 如果编译器是MSVC就进行相关的编译器设置（十分重要）
if(MSVC)
  set(
    CMAKE_CXX_FLAGS
    "${CMAKE_CXX_FLAGS} /bigobj /MP /wd4005 /wd4996 /wd4819 /wd4251 /wd4267 /wd4244 /wd4275 /wd4530 /wd4334 /wd4018"
    )
  add_definitions(-D_ENABLE_EXTENDED_ALIGNED_STORAGE)
  add_compile_options("/utf-8")
endif(MSVC)

# 引入我们必须的测试类
find_package(Catch2 REQUIRED)

# example library
add_library(sum_integers sum_integers.cpp)

# main code
add_executable(sum_up main.cpp)
target_link_libraries(sum_up sum_integers)

# testing binary
# 对于用于测试的target必须引入Catch2::Catch2的库，同时还需要将需要测试的函数编译为lib作为库引入才能进行计算值是否相等的问题
add_executable(cpp_test test.cpp)
target_link_libraries(cpp_test sum_integers Catch2::Catch2)


# enable testing functionality
enable_testing()

# define test
add_test(
  NAME catch_test
  COMMAND $<TARGET_FILE:cpp_test> --success
  )
```

##### 进阶版本之谷歌测试

思路和上面的Catch2的测试思路是一样的，只不过不一样的一点就是验证手段和效果。

###### 谷歌测试主函数

```c++
#include "sum_integers.hpp"
#include "gtest/gtest.h"

#include <vector>

int main(int argc, char **argv) {
  ::testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}

TEST(example, sum_zero) {
  auto integers = {1, -1, 2, -2, 3, -3};
  auto result = sum_integers(integers);
  ASSERT_EQ(result, 0);
}

TEST(example, sum_five) {
  auto integers = {1, 2, 3, 4, 5};
  auto result = sum_integers(integers);
  ASSERT_EQ(result, 15);
}
```

谷歌测试和Catch测试区别就是谷歌测试通过调用RUN_ALL_TESTS()方法执行方法的测试，但是对于Catch版本的测试来讲它实际上是顺序执行测试，并没有主函数可言。

###### CMakeLists中的引入方法

```c++
# set minimum cmake version
cmake_minimum_required(VERSION 3.11 FATAL_ERROR)

# project name and language
project(recipe-03 LANGUAGES CXX)

# require C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# example library
add_library(sum_integers sum_integers.cpp)

# main code
add_executable(sum_up main.cpp)
target_link_libraries(sum_up sum_integers)

# we will use the network to fetch Google Test sources
# make it possible to disable unit tests when not on network
option(ENABLE_UNIT_TESTS "Enable unit tests" ON)
message(STATUS "Enable testing: ${ENABLE_UNIT_TESTS}")

if(ENABLE_UNIT_TESTS)
  # the following code to fetch googletest
  # is inspired by and adapted after:
  #   - https://cmake.org/cmake/help/v3.11/module/FetchContent.html
  # 引入我们的FetchContent模块去clone一个仓库作为我们自己的仓库
  include(FetchContent)

  FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        release-1.8.0
  )
  # 获取我们克隆仓库的属性
  FetchContent_GetProperties(googletest)
  # 如果不是最新版本的googletest
  if(NOT googletest_POPULATED)
    FetchContent_Populate(googletest)

    # Prevent GoogleTest from overriding our compiler/linker options
    # when building with Visual Studio
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
    # Prevent GoogleTest from using PThreads
    set(gtest_disable_pthreads ON CACHE BOOL "" FORCE)

    # adds the targers: gtest, gtest_main, gmock, gmock_main
    add_subdirectory(
      ${googletest_SOURCE_DIR}
      ${googletest_BINARY_DIR}
      )

    # Silence std::tr1 warning on MSVC
    if(MSVC)
      foreach(_tgt gtest gtest_main gmock gmock_main)
        target_compile_definitions(${_tgt}
          PRIVATE
            "_SILENCE_TR1_NAMESPACE_DEPRECATION_WARNING"
          )
      endforeach()
    endif()
  endif()

  add_executable(cpp_test "")

  # 后置exe添加源文件
  target_sources(cpp_test
    PRIVATE
      test.cpp
    )
  # 后置exe链接库目录 sum_integers(自己编写的函数库) gtesst_main(谷歌的测试库)
  target_link_libraries(cpp_test
    PRIVATE
      sum_integers
      gtest_main
    )
  # 启动原生测试
  enable_testing()
  # 添加测试，该测试的target名字为google_test
  add_test(
    NAME google_test
    COMMAND $<TARGET_FILE:cpp_test>
    )
endif()
```

###### 更简便的方法

```cmake
# GTest
enable_testing()
find_package(GTest REQUIRED)
include_directories(${GTEST_INCLUDE_DIRS})
# Unit Tests
# Add test cpp file
add_executable( runUnitTests tests.cpp)
# Link test executable against gtest & gtest_main
target_link_libraries(runUnitTests ${GTEST_BOTH_LIBRARIES})
add_test( runUnitTests runUnitTests )
```



##### 进阶版本之Boost库自带的测试

###### Boost测试函数



###### CMakeLists的引入方法

```cmake
# example library
add_library(sum_integers sum_integers.cpp)

# main code
add_executable(sum_up main.cpp)
target_link_libraries(sum_up sum_integers)

# find boost library
find_package(Boost 1.54 REQUIRED COMPONENTS unit_test_framework)

add_executable(cpp_test test.cpp)

target_link_libraries(cpp_test
  PRIVATE
    sum_integers
    Boost::unit_test_framework
  )

# avoid undefined reference to "main" in test.cpp
target_compile_definitions(cpp_test
  PRIVATE
    BOOST_TEST_DYN_LINK
  )
```

```c++
#include "sum_integers.hpp"
#include <vector>
#define BOOST_TEST_MODULE example_test_suite
// 引入单元测试头文件

#include <boost/test/unit_test.hpp>

BOOST_AUTO_TEST_CASE(add_example) {

 auto integers = {1, 2, 3, 4, 5};

 auto result = sum_integers(integers);

 BOOST_REQUIRE(result == 15);

}
```

##### 使用CMake自带的Test函数进行测试

不知道你有没有这个疑惑，反正我是挺疑惑的，在Windows平台上即便我enable_testing()并且使用add_test()进行测试项目的构建，但是在使用VS打开之后的构建项目一直不能生成，等我观察了之后，发现RUN_TEST下面等本就没有所谓的文件，所以VS是找不到东西给我们运行的。于是我想了想，会不会是因为根本没法在窗口中运行，只能在命令行运行，因为在build目录下有个cmaketest.cmake文件，那么ctest就是利用这个文件对项目进行测试。但是直接输入ctest的后果就是一堆错误。具体解决该错误的过程你可以参考[`网址`](https://gitlab.kitware.com/cmake/cmake/-/issues/20283)。

###### CTest不能使用的解决办法

```cmake
include(CTest)
# define tests
enable_testing()

# we expect this test to run for 2 seconds
add_test(NAME example 
    COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test.py)
# 记住一定要设置LABELS属性为label，这样CTest才能正确使用
# timeout for this test set to 10 seconds
set_tests_properties(example PROPERTIES TIMEOUT 10 LABELS label)
# # set_tests_properties(example PROPERTIES LABELS label)
```

###### CTest使用方法

```
ninja模式下
ctest即可
vs模式下,下面的Debug可以更改为其他模式（当然下面的label选项你可以随便选择，但是记住一定要在set_test_properties中将Label进行修改
ctest -C Debug -L label
```

###### 分组测试的方法

有了上面的ctest的先例，即如果你想使用vs构建的项目，那么请你一定要-C指定编译类型和-L指定标签，如果你懒得指定标签那么就直接指定test target的名字即可。

```cmake
# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name
project(recipe-09 LANGUAGES NONE)

# detect python
find_package(PythonInterp REQUIRED)

# define tests
enable_testing()

add_test(
  NAME feature-a
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/feature-a.py
  )
add_test(
  NAME feature-b
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/feature-b.py
  )
add_test(
  NAME feature-c
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/feature-c.py
  )
add_test(
  NAME feature-d
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/feature-d.py
  )

add_test(
  NAME benchmark-a
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/benchmark-a.py
  )
add_test(
  NAME benchmark-b
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/benchmark-b.py
  )
# 设置的第一组 -L 指定quick就可以
set_tests_properties(
  feature-a
  feature-b
  feature-c
  PROPERTIES
    LABELS "quick"
  )
# 设置的第二组 -L 指定long
set_tests_properties(
  feature-d
  benchmark-a
  benchmark-b
  PROPERTIES
    LABELS "long"
  )
```

###### 集成测试的方法(适用于大项目)

```cmake
# define tests
enable_testing()

add_test(
  NAME setup
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/setup.py
  )
set_tests_properties(
  setup
  PROPERTIES
    FIXTURES_SETUP my-fixture
  )

add_test(
  NAME feature-a
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/feature-a.py
  )
add_test(
  NAME feature-b
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/feature-b.py
  )
set_tests_properties(
  feature-a
  feature-b
  PROPERTIES
    FIXTURES_REQUIRED my-fixture
  )

add_test(
  NAME cleanup
  COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/test/cleanup.py
  )
set_tests_properties(
  cleanup
  PROPERTIES
    FIXTURES_CLEANUP my-fixture
  )
```

执行上面命令的方法：ctest -C Debug my-fixture

##### 使用CMAKE执行终端命令和自带的一些命令对文件进行操作

###### 解压文件操作

```cmake
# 只能说下面这个add_custom_target特别的方便，可以将我们的指令当作target进行执行，而且还可以执行cmd终端命令
add_custom_target(unpack-eigen
  ALL
  COMMAND
    ${CMAKE_COMMAND} -E tar xzf ${CMAKE_CURRENT_SOURCE_DIR}/eigen-eigen-5a0156e40feb.tar.gz
  COMMAND
    ${CMAKE_COMMAND} -E rename eigen-eigen-5a0156e40feb eigen-3.3.4
    # 指定的工作目录目的就是上述的解压文件夹命令和重命名都是在build目录下执行
  WORKING_DIRECTORY
    ${CMAKE_CURRENT_BINARY_DIR}
  COMMENT
    "Unpacking Eigen3 in ${CMAKE_CURRENT_BINARY_DIR}/eigen-3.3.4"
  )
add_executable(linear-algebra linear-algebra.cpp)
# 执行linear-algebra的时候先执行uppack-eigen的解压操作
add_dependencies(linear-algebra unpack-eigen)

# 添加build目录下解压的eigen库到头目录
target_include_directories(linear-algebra
  PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}/eigen-3.3.4
  )
```

###### python脚本的操作

```cmake
set(_module_name "cffi")
# 下面的语句暂且可以看作是操作python的命令
execute_process(
  COMMAND
    ${PYTHON_EXECUTABLE} "-c" "import ${_module_name}; print(${_module_name}.__version__)"
  OUTPUT_VARIABLE _stdout
  ERROR_VARIABLE _stderr
  OUTPUT_STRIP_TRAILING_WHITESPACE
  ERROR_STRIP_TRAILING_WHITESPACE
  )
# 如果_srderr匹配模块没有发现就显示信息
if(_stderr MATCHES "ModuleNotFoundError")
  message(STATUS "Module ${_module_name} not found")
else()
  message(STATUS "Found module ${_module_name} v${_stdout}")
endif()
```

###### 解压文件拷贝指定文件到指定文件夹下

```cmake
# 设置其为二进制目录下的这四个文件（即便该文件现在不存在）-----相当于指定待复制的文件(如果是全部OUTPUT这个选项直接使用ALL代替)
set(wrap_BLAS_LAPACK_sources
  ${CMAKE_CURRENT_BINARY_DIR}/wrap_BLAS_LAPACK/CxxBLAS.hpp
  ${CMAKE_CURRENT_BINARY_DIR}/wrap_BLAS_LAPACK/CxxBLAS.cpp
  ${CMAKE_CURRENT_BINARY_DIR}/wrap_BLAS_LAPACK/CxxLAPACK.hpp
  ${CMAKE_CURRENT_BINARY_DIR}/wrap_BLAS_LAPACK/CxxLAPACK.cpp
  )

add_custom_command(
  OUTPUT
  # 输出内容到该变量中
    ${wrap_BLAS_LAPACK_sources}
    # 执行解压命令
  COMMAND
    ${CMAKE_COMMAND} -E tar xzf ${CMAKE_CURRENT_SOURCE_DIR}/wrap_BLAS_LAPACK.tar.gz
  COMMAND
  # 执行创建文件任务（指定了复制的文件）
    ${CMAKE_COMMAND} -E touch ${wrap_BLAS_LAPACK_sources}
  WORKING_DIRECTORY
    ${CMAKE_CURRENT_BINARY_DIR}
  DEPENDS
  # 指定必须存在的东西
    ${CMAKE_CURRENT_SOURCE_DIR}/wrap_BLAS_LAPACK.tar.gz
    # 指定显示信息
  COMMENT
    "Unpacking C++ wrappers for BLAS/LAPACK"
  VERBATIM
  )
```

###### CMAKE中执行windows命令（AliceVision编译）

下面命令的场景就是编译alicievision的时候即便我已经通过h2o.cmake将文件拷贝到target生成文件夹下，但是在安装的时候我们所依赖的dll仍然没有拷贝到安装目录。我们就可以使用下面的命令将我Binary目录下的Windows-AMD64的target生成目录中所有的dll重新拷贝到我的安装目录下（大概就是你生生了sln之后生成完所有的target之后再使用该命令进行安装）

```cmake
# 拷贝生成目录中的文件到安装目录
add_custom_target(
      COMMAND for /f "delims==" %a in ('dir /b /s build/Windows-AMD64/*.dll')do copy /-y "%a" D:/aliceVision/bin
      COMMENT "copy dll....."
                  )
endif()
```

##### 检查源代码的方法

###### 最传统的方法try_compile

```cmake
  # this will get wiped unless you run cmake with --debug-trycompile
  # 设置这个变量为构建目录下的omp_try_compile文件
  set(_scratch_dir ${CMAKE_CURRENT_BINARY_DIR}/omp_try_compile)
  # 使用try_compile指令进行编译（尝试编译---------用这个指令判断库是否出问题）
  try_compile(
    # 输出结果变量 [out_var]
    omp_taskloop_test_1
    # 生成的target目录 []
    ${_scratch_dir} 
    # 源文件为cpp
    SOURCES
      ${CMAKE_CURRENT_SOURCE_DIR}/taskloop.cpp
    # 链接库
    LINK_LIBRARIES
      OpenMP::OpenMP_CXX
    )
    # 输出编译结果
```

##### 项目结构的搭建

###### 隐藏碍眼的target

```cmake
#  tell CMake to enable IDE folders by adding this line to your top-level CMakeLists.txt
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
# Then, organize your targets into folders, by setting the FOLDER property.
# Put these targets in the 'HiddenTargets' folder in the IDE.
set_target_properties(SillyLib1 SillyLib2 DontCareLib1 PROPERTIES FOLDER HiddenTargets)
# Put these targets in the 'AppTools' folder in the IDE.
set_target_properties(WriterLib1 ReaderLib1 AwesomeLib42 PROPERTIES FOLDER AppTools)
```

###### 隐藏库内部headers方法

```c++
/*
 * @Author: Han Hu
 * @Date: 2019-01-18 17:33:50
 * 包含所有头文件，一般是外部引用
 */

#pragma once

// 必须在第一个
#include <base/common.h>

#include <base/aes.h>
#include <base/camera.h>
#include <base/cuda.h>
#include <base/disk_image.h>
#include <base/file_guard.h>
#include <base/filesystem.h>
#include <base/gdal_wrapper.h>
#include <base/geo_image.h>
#include <base/imgproc.h>
#include <base/kdtree.h>



//在库文件中写入了如下语句之后，最后就可以通过#include <base/base.h>轻松引入所有的头文件，就不需要像所有其它的一样进行<base/xxx.h>一长串的引入
```

###### 自动生成文件注册信息的方法

```
koroFileHeader的VScode插件
插入注释的方法ctrl + alt + t,刷新注释也是这个快捷键
```

