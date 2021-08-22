# 异常处理

## 一、异常概述与异常体系结构

- 异常：程序执行中发生的不正常的情况（开发过程中的语法错误，逻辑错误不是异常）

- 分类：Error：Java虚拟机无法解决的严重问题。：栈溢出（StackOverflowError）和堆溢出（OOM）。
- 分类：Exception：其他因编程错误或偶然的外在因素导致的一般性问题，可以使用针对性的代码进行处理
  1. 空指针访问
  2. 试图读取不存在的文件
  3. 网络连接中断
  4. 数组角标越界
- 解决方案：一：遇到错误就终止程序的运行，二：由程序员编写程序时，考虑到错误的检查，错误消息的提示，以及错误的处理
- 分类：编译时异常，运行时异常（除数为0，数组角标越界）

<img src="https://raw.githubusercontent.com/ph235614/pitcture_bed/master/img/image-20210807164609980.png" alt="image-20210807164609980"  />

- 运行时异常：指编译器不要求强制处理的异常，一般是指编程时的逻辑错误，Java.lang.RuntimeException类以及它的子类都是运行时异常，可以不做处理，比较普遍，全处理会对可读性和运行效率产出影响
- 编译时异常：编译器要求必须处理的异常。编译器要求Java程序员必须捕获或声明所有编译时异常。

## 二、常见异常

| 常见异常分类 | java.lang.RuntimeException     | 编译时异常                       |
| ------------ | ------------------------------ | -------------------------------- |
|              | ClassCastException             | java.io.IOExeption               |
|              | ArrayIndexOutOfBoundsException | ：FileNotFoundException          |
|              | NullPointerException           | ：EOFException                   |
|              | ArithmeticException            | java.lang.ClassNotFoundException |
|              | NumberFormatException          | java.lang.InterruptedException   |
|              | InputMismatchException         | java.lang.InterruptedException   |
|              |                                | java.sql.SQLException            |

### ArrayIndexOutOfBoundsException:数组角标越界

```java
程序：3个明确的字符串数组，for循环5次，输出arr[i]
```

### NullPointerException：空指针异常

```java
程序：指向数组arr，输出
```

### ArithmeticException：算术异常/除0

```java
System.out.println(8/0);
```

### ClassCastException：类型转换异常

```java
Object obj = new Date();
String str = (String)obj;

```

### NumberFormatException:类型转换异常

```java
String str = "abc";
int num = Integer.parseInt(str);
```

### InputMismatchException：输入不匹配

```java

```

## 三、异常处理机制一：try-catch-finally

- 抓抛模型
- 过程一：“抛”：Java执行过程中出现异常时，生成一个异常类对象，并将此对象抛出
- 过程二：“抓”：异常处理方式1.try-catch-finally2.throws

```java
try-catch-finally://右键-Surround With-Try/catch Block
try{
    //可能出现异常的代码
}catch(异常类型1 变量名1){
    //处理异常的方式1
}catch(异常类型2 变量名2){
    //处理异常的方式2
}
....
finally{
    //一定会执行的代码
}
```

1. finally是可选的
2. 使用try将可能出现异常的代码包装起来，在执行过程中，一旦出现异常，就会生产一个对应异常类的对象，根据此对象的类型，去catch中进行匹配
3. 一旦try中的异常对象匹配到某一个catch时，就进入catch中异常的处理。一旦处理完成，跳出当前的结构
4. catch中的异常类型，子类声明在父类的上面，同类都行。
5. **常用的异常对象处理的方式：1.String getMessage() 2.printStackTrace()**
6. 在try里面出现的变量在外面不能使用,可以相互嵌套
7. finally：声明的代码一定会被执行，即使catch出现异常，try，catch中有return语句。优先最高
8. finally：像数据库的连接，输入输出流，网络编程Socket等资源，JVM不会自动回收，需要手动资源释放，声明在finally中。

```java
String str = "abc";
try{
    int num = Integer.parseInt(str);
}catch(NumberFormatException e){
    System.out.println("出现数值转换异常，不要着急...");
    System.out.println(e.getMessage());
    e.printStackTrace();
}finally{ 
}
```

## 四、异常处理机制二：throws+异常类型

1. 声明在方法的声明处，指明此方法执行时，可能抛出的异常类型，一旦出现异常，仍会生成一个异常类的对象，此对象满足throws异常类型时，就会被抛出，异常后面的代码就不会被执行。
2. 捕获异常（catch）：处理抛出的异常

```java
方法重写中：子类重写的方法抛出的异常不大于父类被重写的方法抛出的异常
    父类中的方法没有throws方式处理异常，子类重写的方法也不能使用throws，只能使用try-catch-finally
    经验：调用几个递进关系的方法时，用throws，在调用方法的地方用try-catch-finally
```

## 五、手动抛出异常：throw

- throw:表示抛出一个异常类的对象，生成异常对象的过程，声明在方法体内
- throws:属于异常处理的一种方式，声明在方法的声明处

````java
Exception exception = new ClassCastException();//创建好的异常对象不抛出对程序没有任何影响
//手动抛出
IOException e = new IOException();
throw e;
//类里面
public void message throws Exception{
    //代码
    throw new RuntimeException("您输入的数据非法");
}
main(){
    try{
        //有可能异常代码
    }catch Exception e{
         System.out.println(e.getMessage());
    }
}
````

## 六、用户自定义异常类

- 用户自定义异常类一般都是RuntimeException的子类
- **1.需要编写几个重载的构造器，2.需要提供serialVersionUID，3.通过throw抛出**，最重要的是异常类的名字，当异常出现时，根据名字判断异常

```java
public class MyException extends RuntimeException{
    static final long serialVersionUID = -7034897190745766939L;//序列号
    public MyException(){}
    public MyException(String message){
        super(message);
    }
}
```

## 例题：

```
编写应用程序EcmDef.java，接收命令行的两个参数，要求不能输入负数，计算两数相除。
对数据类型不一致 (NumberFormatException)、缺少命令行参数(ArrayIndexOutOfBoundsException、除0(ArithmeticException)及输入负数(EcDef 自定义的异常)进行异常处理。
提示：(1)在主类(EcmDef)中定义异常方法(ecm)完成两数相除功能。 (2)在main()方法中使用异常处理语句进行异常处理。 (3)在程序中，自定义对应输入负数的异常类(EcDef)。 (4)运行时接受参数 java EcmDef 20 10 //args[0]=“20” args[1]=“10” (5)Interger类的static方法parseInt(String s)将s转换成对应的int值。 如：int a=Interger.parseInt(“314”); //a=314;
```

- 主函数

```java
package com.ph.exception;

/*
*   编写应用程序EcmDef.java，接收命令行的两个参数，要求不能输入负数，计算两数相除。
	1.对数据类型不一致 (NumberFormatException)、
	2.缺少命令行参数(ArrayIndexOutOfBoundsException、
	3.除0(ArithmeticException)
	4.及输入负数(EcDef 自定义的异常)进行异常处理。
	提示：
	(1)在主类(EcmDef)中定义异常方法(ecm)完成两数相除功能。 
	(2)在main()方法中使用异常处理语句进行异常处理。 
	(3)在程序中，自定义对应输入负数的异常类(EcDef)。 
	(4)运行时接受参数 java EcmDef 20 10 //args[0]=“20” args[1]=“10”
	(5)Interger类的static方法parseInt(String s)将s转换成对应的int值。 
	如：int a=Interger.parseInt(“314”); //a=314;
 */
public class EcmDef {
	public static void main(String[] args) {

		try {
			// Run As- Run Configurations - 第二个(x)=Arguments - 输入
			int i = Integer.parseInt(args[0]);
			int j = Integer.parseInt(args[1]);

			int result = ecm(i, j);

			System.out.println(result);
		} catch (NumberFormatException e) {
			System.out.println("对数据类型不一致");
			// e.printStackTrace();
		} catch (ArrayIndexOutOfBoundsException e) {
			System.out.println("缺少命令行参数");
			// e.printStackTrace();
		} catch (ArithmeticException e) {
			System.out.println("除0");
		} catch (EcDef e) {
			System.out.println();
		}

	}

	public static int ecm(int i, int j) throws EcDef {
		if (i < 0 || j < 0) {
			throw new EcDef("分子分母为负数！");
		}
		return i / j;
	}
}

```

- 类函数

```java
package com.ph.exception;

public class EcDef extends Exception {
	static final long serialVersionUID = -3387516993124229948L;// 改动一下，独立序列号

	public EcDef() {
	}

	public EcDef(String msg) {
		super(msg);
	}
}
```











