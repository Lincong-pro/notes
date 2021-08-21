## 黑马面面布局开发

### 一、目的

1. 了解移动端页面开发流程
2. 掌握移动端常见布局思路

#### 1.1 技术方案

```css
1. 弹性盒子 + rem + LESS
4. 最小适配设备为iphone5 320px  最大设配设备为iphone8plus(ipad能正常查看内容即可)
```

#### 1.2 代码规范

```css
1. 类名语义化,尽量精短、明确，必须以字母开头命名，且全部字母为小写，单词之间统一使用下划线“_” 连接
2. 类名嵌套层次尽量不超过三层
3. 尽量避免直接使用元素选择器
4. 属性书写顺序
   布局定位属性：display / position / float / clear / visibility / overflow
   尺寸属性：width / height / margin / padding / border / background
   文本属性：color / font / text-decoration / text-align / vertical-align
   其他属性（CSS3）：content / cursor / border-radius / box-shadow / text-shadow
5. 避免使用id选择器
6. 避免使用通配符*和!important
```

#### 1.2 目录规范

```css
项目文件夹：heimamm
	样式文件夹：css
	业务类图片文件夹：images
	样式类图片文件夹： icons
	字体类文件夹： fonts
```

### 二、流程开发

#### 2.1 蓝湖/摹客协作平台

- UI 设计师 psd 效果图完成后，会上传到蓝湖//摹客里面，同时会拉前端工程师进入开发
- 大部分情况下，UI 会把图片按照前端设计要求给切好
- UI 设计师 上传蓝湖到或者/摹客（了解）

```css
1. /摹客官网地址： https://www.mockplus.cn/  注册一个账号
2. 下载moke  ps插件
3. PS 安装/摹客/蓝湖插件
3. 打开PS/摹客/蓝湖插件
4. 上传（需要切图，需要先标注切图）
5. 查看项目
6. 邀请成员进入（分享按钮，链接地址）
```

- 前端设计师可以直接/摹客/蓝湖测量取值

#### 2.2 适配方案

- flex 布局
- 百分比布局
- rem 布局
- vw/vh 布局
- 响应式布局
- 本次案例 flex + rem + + flexible.js + LESS

#### 2.3 初始化文件

- 引入 normalize.css

- less 中 初始化 body 样式

- 约束范围

- ```css
  @media screen and (min-width: 750px) {
    html {
      font-size: 37.5px !important;
    }
  }
  ```

#### 2.4 布局模块

1. 头部模块 .header 高度为 80px

2. nav 模块制作 多用 flex

3. 充电学习 阴影

   ```html
   box-shadow: 0 0px 10px rgba(0, 0, 0, 0.1);
   ```

#### 2.5 swiper 插件使用

官网地址：<https://www.swiper.com.cn/>

- 下载需要的 css 和 js 文件 html 页面中 引入相关文件
- 官网找到类似案例，复制 html 结构，css 样式 js 语法
- 根据需求定制修改模块

#### 2.6 图标字体上传下载

上传步骤：

1. 让 UI 美工准备好 图标字体（必须是 svg 格式）

2. 点上传按钮（保留颜色并提交）

3. 生成之后加入购物车即可

4. 点击下载 --- 下载代码

小技巧： 如何批量下载全部字体图标呢？

```javascript
var span = document.querySelectorAll(".icon-cover");
for (var i = 0, len = span.length; i < len; i++) {
  console.log(span[i].querySelector("span").click());
}
```

#### 2.7 上传码云并发布部署静态网站

准备工作： 需要下载 git 软件 需要码云注册账号

git 可以把我们的本地网站提交上传到远程仓库（码云 gitee）里面 类似以前的 ftp

码云 就是远程仓库， 类似服务器

1. 码云创建新的仓库。 heimamm

2. 利用 git 提交 把本地网站提交到 码云新建的仓库里面

   - 在网站根目录右键-- Git Bash Here

   - 如果是第一次利用 git 提交，请配置好全局选项

     ```javascript
     git config --global user.name "用户名"
     git config --global user.email "你的邮箱地址"
     ```

   - 初始化仓库

     ```javascript
     git init
     ```

   - 把本地文件放到暂存区

     ```javascript
     git add .
     ```

   - 把本地文件放到本地仓库里面

     ```javascript
     git commit -m '提交黑马面面网站'
     ```

   - 链接远程仓库

     ```javascript
     git remote add origin 你新建的仓库地址
     ```

   - 把本地仓库的文件推送到远程仓库 push

     ```javascript
     git push -u origin master
     ```

3. 码云部署发布静态网站

最后： 如果提交网站，你不愿意用 git 提交， 可以直接找到仓库，里面有文件，选择上传本地文件即可。

但是，1 个小时内，只能上传 20 个以内的文件， 前端人员，git 必备技能

## CMake 官方文档学习

### build targets

add_library(archive MODULE 7Z.cpp) //不生成.so 或者.a 文件
模块的特点就是不需要链接，只有在运行的时候才会选择性的去加载，所以不需要 target_link_libraries()
add_library(archive SHARED 7Z.cpp) //生成.so 动态文件
add_library(archive STATIC 7Z.cpp) //生成.a 静态库
除了上面的选项，我们仍然可以给我们编译的库指定框架（即哪一个系统下才能使用这个库）
add_library(MyFramework SHARED MyFramework.cpp)
set_target_properties(MyFramework PROPERTIES
FRAMEWORK TRUE
FRAMEWORK_VERSION A # Version "A" is macOS convention
MACOSX_FRAMEWORK_IDENTIFIER org.cmake.MyFramework
)
当然我们还有一个选项就是将我们所编译的库连接到另外一个库上面
前提条件就是我们所编译的库必须指定编译类型为 OBJECT 类型，然后
在另外一个库使用$<TARGET_OBJECT:objectlibname>进行连接，当然
也可以直接使用 target_link_libraries 进行连接
add_library(archive OBJECT archive.cpp zip.cpp lzma.cpp)
add_library(archiveExtras STATIC $<TARGET_OBJECTS:archive> extras.cpp)
add_executable(test_exe $<TARGET_OBJECTS:archive> test.cpp)

当然除了库可以连接 OBJECT 类型的 object 库之外，其他的 executable target
仍然可以连接这个库，即可以多次连接
add_library(archive OBJECT archive.cpp zip.cpp lzma.cpp)
add_library(archiveExtras STATIC extras.cpp)
target_link_libraries(archiveExtras PUBLIC archive)
add_executable(test_exe test.cpp)
target_link_libraries(test_exe archive)

- target_include_directories、target_compile_definitions、target_compile_options 分别为 include_directories、compile_definitions、compile_options 的补充（扩展）

- 上面的每一条命令都有访问项，分别为 PRIVATE、PUBLIC、INTERFACE，其定义就是 PRIVATE 模式仅仅只填充非接口类型的变量、INTERFACE 就单纯只填充接口变量、PUBLIC 模式两种变量

  ```cmake
  target_compile_definitions(archive
    PRIVATE BUILDING_WITH_LZMA
    INTERFACE USING_ARCHIVE_LIB
  )
  ```

- 我们在胡老师的脚本 sh 脚本中经常会看见-D、-I 之类的，实际上官方文档的解释就是-D 就是 COMILE_DEFINITIONS.-I 代表 INCLUDE_DIRECTORIES

  ```
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

  ```
  # 假设我们的lib1和lib2不兼容，其中的lib1要求独立编译的方式进行编译，lib2要求非独立编译，然后当我们的exe2连接到两个属性要求不一致的库之后就出现了如下问题
  CMake Error: The INTERFACE_POSITION_INDEPENDENT_CODE property of "lib2" does
  not agree with the value of POSITION_INDEPENDENT_CODE already determined
  for "exe2".
  ```

#### GDAL 库链接

```cmake
# 引入Boost库的头文件 和 GDAL的头文件
target_include_directories(${PROJECT_NAME}
    PUBLIC ${Boost_INCLUDE_DIR} ${GDAL_INCLUDE_DIR}
)
# 引入Boost库的lib文件 和 GDAL的lib文件
target_link_libraries(lincong ${Boost_LIBRARIES} ${GDAL_LIBRARY})
```
