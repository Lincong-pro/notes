```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QRegExp>
#include <QRegExpValidator>
#include <QDebug>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    //创建一个正则表达式对象,实际上下面的正则表达式控制了lineEdit中可以输入的内容
    QRegExp regExp("[1-9][0-9]{6,10}@qq.com$");
    //使用正则表达式对象设置正则验证对象
    ui->lineEdit->setValidator(new QRegExpValidator(regExp));
    connect(ui->okButton,&QPushButton::clicked,this,[](){
        qDebug()<<"I'd like to accpet you!";
    });
    connect(ui->cancelButton,&QPushButton::clicked,this,[](){
        qDebug()<<"I'd like to reject you!";
    });
}

MainWindow::~MainWindow()
{
    delete ui;
}


void MainWindow::on_lineEdit_textChanged(const QString &arg1)
{
    //根据该文本中的内容判断是否启用OK按钮
    ui->okButton->setEnabled(ui->lineEdit->hasAcceptableInput());
    //This property holds whether the input satisfies the inputMask and the validator.
}
```

##### Qt MainWindow布局无法显示

[`参考网址`](https://www.jianshu.com/p/66b031a38c78)Note: Creating a main window without a central widget is not supported. You must have a central widget even if it is just a placeholder.

```c++
    //接下来我需要实现的东西就是实现左侧listwidget的item选择在右侧stackedwidget进行实时的显示
    QStackedWidget *stack =new QStackedWidget();
    QListWidget *list = new QListWidget();
    // 下面的三句话十分重要，决定了手动布局的效果能否显示出来
    QWidget *cen=new QWidget(this);
    setCentralWidget(cen);
    QHBoxLayout *layout = new QHBoxLayout(cen);
    layout->addWidget(stack,0,Qt::AlignCenter);
    layout->addWidget(list);
    for(int i=0;i<3;++i){
        list->insertItem(i,"Window"+QString::number(i+1));
        stack->addWidget(new QLabel("Window"+QString::number(i+1)));
    }
    //利用布局窗口控制各个部件的延展性问题
    layout->setStretchFactor(list,1);
    layout->setStretchFactor(stack,3);
    //连接信号与槽便于堆叠窗口的切换
    connect(list,&QListWidget::currentRowChanged,stack,&QStackedWidget::setCurrentIndex);
    layout->setMargin(5);
    layout->setSpacing(5);
```

##### QGraphicsView QGraphicsItem QGraphicsScene

```c++
 //用于存放绘制图元的一些操作，供QGraphicsView调用
void paint(QPainter *painter,const QStyleOptionGraphicsItem* option,QWidget* widget);
//下面是QGraphicsItem的自带函数
void Butterfly::paint(QPainter *painter, const QStyleOptionGraphicsItem *option, QWidget *widget) {
    if(up){
        painter->drawPixmap(boundingRect().topLeft(),bmp_up);
        up=!up;
    }else {
        painter->drawPixmap(boundingRect().topLeft(),bmp_down);
        up=!up;
    }
}
```

###### 上面实现自定义的组元逻辑思路之后使用   

```c++
	 //创建视图
    QGraphicsView *view=new QGraphicsView;
    //创建场景
    QGraphicsScene *scene=new QGraphicsScene;
    //创建Item
    Butterfly *butterfly=new Butterfly;
    butterfly->setPos(-100,0);
    //设置场景矩形
    scene->setSceneRect(-200,-200,400,400);
    //设置场景图元
    scene->addItem(butterfly);
    //view设置场景
    view->setScene(scene);
    view->resize(400,400);
    view->show();
```

###### timerEvent实现内部逻辑思路

```c++
void Butterfly::timerEvent(QTimerEvent *) {
    //该函数为QGraphicsItem的绘图函数
    qreal edge_right=scene()->sceneRect().right()+boundingRect().width()/2;
    qreal edge_top=scene()->sceneRect().top()+boundingRect().height()/2;
    qreal edge_bottom=scene()->sceneRect().bottom()+boundingRect().height()/2;
    if(this->pos().x()>=edge_right){
        //如果item的中心超过了边界线（pos.x()相对于scene来讲）
        setPos(scene()->sceneRect().left(),this->pos().y());//y轴不变，x轴向左移动
    }
    if(this->pos().y()>=edge_top) {
        setPos(this->pos().x(),scene()->sceneRect().bottom());//x轴不变，y轴向下移动
    }
    if(this->pos().y()<=edge_bottom) {
        setPos(this->pos().x(),scene()->sceneRect().top());//x轴不变，y轴向上移动
    }
    angle+=(rand()%10)/20;//在0-0.5之间产生随机数
    qreal dx=fabs(sin(angle*PI)*10);//0-180度之间移动过
    qreal dy=fabs(rand()%200);//控制蝴蝶在场景中的位置
    setPos(mapToParent(dx,dy));//投影到场景坐标系中
}
```

##### C++Web开发

```c++
https://www.runoob.com/cplusplus/cpp-web-programming.html
```

##### Qt的qrc

* Qt中的qrc打开的资源尽量是在子目录下或者qrc文件的同级目录

##### Qt自动管理指针的类

* QScopedPointer类似于智能指针，主要用于管理控件的释放

* QScopedArrayPointer用于RAII释放数组

  ```c++
   QScopedArrayPointer<int> i(new int[10]);
  ```

##### QtCreator便捷开发

1. 查找文件 Ctrl + K
2. 打开文件的快捷切换 Ctrl + Tab
3. 运行 Ctrl + R
4. 编译 Ctrl + B
5. 快速查找定义 F2
6. 源文件和头文件快速切换 F4

###### QtCreator命令行调试

>  colmap bundle_adjuster --input_path G:/tri --output_path C:/Users/lovely/Desktop/build1 --BundleAdjustment.max_num_iterations 5000 --BundleAdjustment.refine_focal_length 0 --BundleAdjustment.refine_principal_point 1 --BundleAdjustment.refine_extra_params 0 --BundleAdjustment.refine_extrinsics 0 --BundleAdjustment.function_tolerance 0 --BundleAdjustment.gradient_tolerance 0 --BundleAdjustment.parameter_tolerance 1e-10 --BundleAdjustment.constrain_points 1 --BundleAdjustment.constrain_points_loss_weight 0.01 \
>
> **note:**注意最后一定要加一个\作为命令行的结束

###### QtCreatorCDB调试安装

> https://developer.microsoft.com/zh-cn/windows/downloads/sdk-archive/
>
> 安装向导中只需要选择Debug Tools即可

#### QML

> 1. **QML采用下面的命令运行qml文件**
>
> ```html
> $QTDIR/bin/qmlscene rectangle.qml
> ```
>
> 2. **QML自定义属性** 
>
>    ```
>    property int(类型) times(属性名): value
>    ```
>
>    * **QML自定义属性类别**
>      + bool、unsigned int、int、float、double、qreal
>      + string(基本类型)
>
> 3. **QML给属性定义别名**
>
>    ```
>    property alias anotherTimes: thisLabel.times
>    ```
>
> 4. **QML监听事件**
>
>    ```c++
>    // focus is neeed to receive key events
>    focus: true
>    ```
>
> 5. ##### QML使用JavaScript函数
>
>    ```js
>        // (2) handler with some JS
>        Keys.onSpacePressed: {
>        increment()
>        }
>
>        // clear the text on escape
>        Keys.onEscapePressed: {
>        label.text = ''
>        }
>
>        // (3) a JS function
>        function increment() {
>        spacePresses = spacePresses + 1
>        }
>    ```
>
> 6. **QML四大基础元素**
>
>    + Item(基础元素对象)
>
>      + Geometry: x y z width height
>      + Layout handling: 
>        - anchors
>          + left right top bottom
>          + verticalCenter horizontalCenter
>          + verticalCenterOffset horizontalCenterOffset
>          + leftMargin .etc
>          + centerIn(居中)
>      + Key handling
>        - key  和 keyNavigation（按键定位）
>        - Transform 
>          - scale
>          - rotate
>          - transformOrigin
>      + Visual 
>        - opacity
>        - visible
>        - clip(限制边界元素的绘制)
>        - smooth
>
>    + Rectangle(矩形框)
>
>      - radius绘制圆角
>
>      - gradient
>
>        + ```
>          gradient: Gradient {
>              GradientStop { position: 0.0; color: 						"lightsteelblue" }
>              GradientStop { position: 1.0; color: "slategray" 			}
>          }
>          ```
>
>      - border
>
>        + color
>        + width
>
>    + Text(文本)
>
>      - style: Text.Sunken
>      - styleColor: '#FF4444'
>      - anchors.centerIn: parent
>
>    + Image(图像)
>
>    + MouseArea(鼠标区域)
>
>      - ```
>        anchors.fill: parent
>        onClicked: {
>        	status.text = "Button clicked!"
>        }
>        ```

##### 常用的QML事件

> * onTextChanged

#### Font

> 1. font.family: "Ubuntu"
> 2. font.pixelSize: 24

##### Image

###### 指定图像文件

```html
source: 'assets/rocket.png' <!--其中url的值可以是一个网络链接或者本地-->
clip: true
fillMode: Image.PreserveAspectiCrop
```



##### Text

###### 文本位置

```html
horizontalAlignment: Text.AlignHCenter
verticalAlignment: Text.AlignTop
warpMode: Text.WordWarp
```

