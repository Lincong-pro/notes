# 线程

## 一、基本概念：程序，进程，线程

### 1.1程序

- 程序：完成特定任务，用某种语言编写的一组指令的集合。即指**一段静态的代码**，静态对象。

### 1.2进程

- 进程：**程序的一次执行过程**，或是正在运行的一个程序，是一个动态的过程，有它自身的产出，存在，消亡的过程——生命周期
- 程序是静态的，进程是动态的
- ==进程作为资源分配的单元==，系统在运行时会为每个进程分配不同的内存区域，**==方法区和堆==**（公用）

### 1.3线程

- 线程：进程可进一步细化为线程，是一个**程序内部的一条执行路径**。
- 若一个进程同一时间并行执行多个线程，就是支持多线程的
- ==线程作为调度和执行的单位==，**每个线程拥有独立的==运行栈==和==程序计数器==(pc)**，线程切换的开销小

#### 1.3.1使用多线程的优点

- 背景：以单核CPU为例，只使用单个线程先后完成多个任务（调用多个方法），肯定比多个线程来完成用的时间更短
- ==多线程的优点==：
  1. **提高应用程序的相应，对图形化界面更有意义，可增强用户体验**
  2. **提高计算机系统CPU的利用率**
  3. **改善程序结构。将既长又复杂的进程分为多个线程，独立运行，利于理解和修改**

#### 1.3.2何时需要多线程

1. 程序需要同时执行俩个或多个任务
2. 程序需要实现一些需要等待的任务时，如用户输入，文件读写操作，网络操作，搜索时
3. 需要一些后台运行的程序时

### 1.4其他知识

#### 1.4.1单核CPU，多核CPU

- 单核CPU：其实是一种假的多线程，因为在一个时间单元内，也只能执行一个线程的任务。
- Java.exe：至少有三个线程：main()主线程，gc()垃圾回收线程，异常处理线程。

#### 1.4.2并发与并行

- 并发：多个CPU同时执行多个任务，eg：多个人做不用的事
- 并行：一个CPU（采用时间片）同时执行多个任务，eg：秒杀 

## 二、线程的创建（4种）和使用

### 2.1线程的创建和启动

```java
//java类使用java.lang.Thread来实现多线程
java.lang.Thread
//构造器
Thread():创建新的Thread对象
Thread(String threadname):创建线程并指定线程实例名
Thread(Runnable target):指定创建线程的目标对象，它实现了Runnable接口中的run方法
Thread(Runnable target,String name):创建新的Thread对象
```

- **Thread特性：**每个线程都是通过某个特定Thread对象的run()方法来完成操作的，经常把run()方法的主体成为线程体

- 通过该Thread对象的start()方法来启动这个线程，而非直接调用run()

### 2.2创建线程的方法：2种

#### 2.2.1继承Thread类的方式

1. ==定义子类继承Thread类==
2. ==子类中重写Thread类中的run方法==——将此线程执行的操作声明在run中
3. ==创建Thread子类对象，即创建了线程对象==
4. ==调用线程对象start方法：启动线程，调用run方法==——1.启动当前线程2.调用当前线程的run()
   - 注意点：如果自己手动调用run方法，那么就只是普通方法，没有启动多线程模式
   - run()方法有JVM调用，什么时候调用，执行过程控制都有操作系统的CPU调度决定
   - 想要启动多线程，必须调用start方法
   - **一个线程对象只能调用一次start()方法**，如果重复调用了，则将抛出以上的异常“IllegalThreadStateException”

```java
package com.ph.exer;

/**
 * @author 彭湖
 * 创建俩个分线程，一个遍历奇数，一个遍历偶数
 */
public class ThreadDemo {
    public static void main(String[] args) {
        System.out.println();
        MyThread1 m1 = new MyThread1();
        MyThread2 m2 = new MyThread2();
        m1.start();
        m2.start();
        //创建Thread类的匿名子类的方式
        new Thread(){
            @Override
            public void run() {
                for (int i =0;i<100;i++){
                    if(i%2==0){
                        System.out.println(Thread.currentThread().getName()+":"+i);
                    }
                }
            }
        }.start();
    }
}
class MyThread1 extends Thread{
    @Override
    public void run() {
        for (int i =0;i<100;i++){
            if(i%2==0){
                System.out.println(Thread.currentThread().getName()+":"+i);
            }
        }
    }
}
class MyThread2 extends Thread{
    @Override
    public void run() {
        for (int i =0;i<100;i++){
            if(i%2 != 0){
                System.out.println(Thread.currentThread().getName()+":"+i);
            }
        }
    }
}
```

#### 2.2.2实现Runnable接口的方式

1. ==定义子类，实现Runnable接口==
2. ==子类中重写Runnable接口中的run方法==
3. ==通过Thread类含参构造器创建线程对象==
4. ==将Runnable接口的子类对象作为实际参数传递给Thread类的构造器中==
5. ==调用Thread类的start方法：1.开启线程，2.调用Runnable子类接口的run方法==

```java
package com.ph.homework;
/**
 * 3、利用Runnable实现,要求多线程求解某范围素数每个线程负责
    1000范围:线程1找1-1000;线程2找1001-2000;
    线程3找 2001-3000编程
    程序将每个线程找到的素数及时打印。
 */
class MyThread implements Runnable{
    @Override
    public void run() {
        boolean isFlag = true;
        int m=0;
        int n=0;
        if(Thread.currentThread().getName() == "线程一："){
            m = 2;
            n= 1000;
        }else if(Thread.currentThread().getName() == "线程二："){
            m = 1001;
            n= 2000;
        }else{
            m = 2001;
            n= 3000;
        }
        for(int i=m;i<=n;i++){
            for(int j=2;j<i;j++){
                if(i%j==0){
                    isFlag = false;
                    break;
                }
            }
            if(isFlag){
                System.out.println(Thread.currentThread().getName()+":"+i);
            }
            isFlag = true;
        }
    }
}
public class Homework3 {
    public static void main(String[] args) {
        MyThread m1 = new MyThread();
        Thread t1 = new Thread(m1);
        Thread t2 = new Thread(m1);
        Thread t3 = new Thread(m1);

        t1.setName("线程一：");
        t2.setName("线程二：");
        t3.setName("线程三：");

        t1.start();
        t2.start();
        t3.start();


    }
}
```

### 2.3继承方法和实现方法的联系与区别

```java
//Thread类实际上是继承Rannable接口的类
public class Thread extends Object implements Runnable
```

- **开发中优先使用Rannable接口**
- **原因：1.实现的方式没有类的单继承性的局限性2.实现的方式更适合来处理多个线程有共享数据的情况**
- 区别：继承Thread：线程代码存放Thread子类的run方法中
- 区别：实现Runnable：线程代码存在接口的子类的run方法

### 2.4Thread类的有关方法

```java
void start(): 启动线程，并执行对象的run()方法
run(): 线程在被调度时执行的操作
String getName(): 返回线程的名称
void setName(String name):设置该线程名称
static Thread currentThread(): 返回当前线程。在Thread子类中就是this，通常用于主线程和Runnable实现类
static void yield()：线程让步————直接在线程类中使用
	暂停当前正在执行的线程，把执行机会让给优先级相同或更高的线程
	若队列中没有同优先级的线程，忽略此方法
join() ：当某个程序执行流中调用其他线程的 join() 方法时，调用线程将被阻塞，直到 join() 方法加入的 join 线程执行完为止
	低优先级的线程也可以获得执行
static void sleep(long millis)：(指定时间:毫秒) 
    令当前活动线程在指定时间段内放弃对CPU控制,使其他线程有机会被执行,时间到后重排队。
	抛出InterruptedException异常
stop(): 强制线程生命期结束，不推荐使用
boolean isAlive()：返回boolean，判断线程是否还活着
```

### 2.5线程的调度

- 调度策略：
  - 时间片：![image-20210810161809409](D:\桌面\数学建模笔记\数学建模笔记图片\image-20210810161809409.png)
  - 抢占式：**高优先级的线程抢占CPU**
- Java的调度方法
  - 同优先级线程组成先进先出队列（先到先服务），使用时间片策略
  - 对高优先级，使用优先调度的抢占式策略

### 2.6线程的优先级

- **线程的优先级等级**
  - **MAX_PRIORITY：10**
  - **MIN _PRIORITY：1**
  - **NORM_PRIORITY：5**

-  **涉及的方法**
  - **getPriority()** **：**返回线程优先值
  - **setPriority(int newPriority)** **：**改变线程的优先级

-  **说明**
  - 线程创建时继承父线程的优先级
  - 低优先级只是获得调度的概率低，并非一定是在高优先级线程之后才被调用

### 2.7线程的分类

- Java中的线程分为两类：一种是**守护线程**，一种是**用户线程**。 
- 它们在几乎每个方面都是相同的，唯一的区别是判断JVM何时离开。 
- 守护线程是用来服务用户线程的，通过在start()方法前调用
- **thread.setDaemon(true**)可以把一个用户线程变成一个守护线程。
- Java垃圾回收就是一个典型的守护线程。
- 若JVM中都是守护线程，当前JVM将退出。 
- 形象理解：兔死狗烹，鸟尽弓藏

## 三、线程的生命周期

- jdk中用Thread.State类定义了线程的几种状态

![image-20210810201906512](D:\桌面\数学建模笔记\数学建模笔记图片\image-20210810201906512.png)

### 3.1新建

- 新建：当一个Thread类或其子类的对象被声明并创建时，新生的线程对象处于新建状态

### 3.2就绪

- 就绪：处于新建状态的线程被start()后，将进入线程队列等待CPU时间片，此时它已具备了运行的条件，只是没分配到CPU资源

### 3.3运行

- 运行：当就绪的线程被调度并获得CPU资源时,便进入运行状态， run()方法定义了线程的操作和功能

### 3.4阻塞

- 阻塞：在某种特殊情况下，被人为挂起或执行输入输出操作时，让出 CPU 并临时中止自己的执行，进入阻塞状态

### 3.5死亡

- 死亡：线程完成了它的全部工作或线程被提前强制性地中止或出现异常导致结束

## 四、线程的同步

### 4.1同步机制

- 解决多线程的安全问题：同步机制

```java
//方式一：同步代码块
//操作共享数据的代码，即为需要被同步的代码
//同步监视器：锁：
synchronized(同步监视器){
    //需要被同步的代码；
}
//方式二：同步方法
//将synchronize放在方法的声明中，表示整个方法为同步方法，和继承一起使用：默认锁：this
//和接口一起使用：public static synchronized void show(String name){，默认锁：类名.class
public synchronized void show(String name){
    //需要被同步的代码；
}
```

### 4.2同步机制中的锁

- 同步锁机制：在《Thinking in Java》中，是这么说的：对于并发工作，你需要某种方式来防止两个任务访问相同的资源（其实就是共享资源竞争）。 防止这种冲突的方法就是当资源被一个任务使用时，在其上加锁。第一个访问某项资源的任务必须锁定这项资源，使其他任务在其被解锁之前，就无法访问它了，而在其被解锁之时，另一个任务就可以锁定并使用它了。
- synchronized的锁是什么？
  1. **任意对象都可以作为同步锁**。所有对象都自动含有单一的锁（监视器）。
  2. 同步方法的锁：静态方法（类名.class）、非静态方法（this）
  3. 同步代码块：自己指定，很多时候也是指定为this或类名.class
- 注意：
  1. 必须确保使用同一个资源的==**多个线程共用一把锁**==，这个非常重要，否则就无法保证共享资源的安全
  2. **==一个线程类中的所有静态方法共用同一把锁（类名.class）,所有非静态方法共用同一把锁（this）,同步代码块（指定需谨慎）==**
  3. **继承使用（类名.class）类也是对象，类只会加载一次，可以当锁使用，接口使用this**

```java
synchronized(MyThread.class){
    //需要被同步的代码；
}
synchronized(this){
    //需要被同步的代码；
}
```

### 4.3同步的范围

1. **==如何找问题，即代码是否存在线程安全？（非常重要）==**
   1. 明确哪些代码是多线程运行的代码
   2. 明确多个线程是否有共享数据
   3. 明确多线程运行代码中是否有多条语句操作共享数据
2. ==**如何解决呢？（非常重要）**==
   - 对多条操作共享数据的语句，只能让一个线程都执行完，在执行过程中，其他线程不可以参与执行。即所有操作共享数据的这些语句都要放在同步范围中
3. **切记**
   - 范围太小：没锁住所有有安全问题的代码
   - 范围太大：没发挥多线程的功能。

### 4.4释放锁的操作

1. 当前线程的同步方法、同步代码块执行结束。
2. 当前线程在同步代码块、同步方法中遇到break、return终止了该代码块、该方法的继续执行。
3. 当前线程在同步代码块、同步方法中出现了未处理的Error或Exception，导致异常结束。
4. 当前线程在同步代码块、同步方法中执行了线程对象的**wait()**方法，当前线程暂停，并释放锁。

### 4.5不会释放锁的操作

1. 线程执行同步代码块或同步方法时，程序调用Thread.sleep()、Thread.yield()方法暂停当前线程的执行
2. 线程执行同步代码块时，其他线程调用了该线程的suspend()方法将该线程挂起，该线程不会释放锁（同步监视器）。
   - 应尽量避免使用suspend()和resume()来控制线程

### 4.6单例设计模式之懒汉式(线程安全)

```Java
class Singleton {
	private static Singleton instance = null;
	private Singleton(){}
	public static Singleton getInstance(){
		if(instance==null){
			synchronized(Singleton.class){
				if(instance == null){
					instance=new Singleton();
				} 
            } 
        }
		return instance;
	}
}
public class SingletonTest{
	public static void main(String[] args){
		Singleton s1=Singleton.getInstance();
		Singleton s2=Singleton.getInstance();
		System.out.println(s1==s2);
	}
}
```

### 4.7线程的死锁问题

1. 死锁
   - 不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁
   - 出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于阻塞状态，无法继续
2. 解决方法
   1. 专门的算法、原则
   2. 尽量减少同步资源的定义
   3. 尽量避免嵌套同步

```java
public class DeadLockTest {
	public static void main(String[] args) {
		final StringBuffer s1 = new StringBuffer();
		final StringBuffer s2 = new StringBuffer();
		new Thread() {
			public void run() {
				synchronized (s1) {
					s2.append("A");
					synchronized (s2) {
						s2.append("B");
						System.out.print(s1);
						System.out.print(s2);
					} 
                } 
            }
		}.start();
		new Thread() {
			public void run() {
				synchronized (s2) {
					s2.append("C");
					synchronized (s1) {
						s1.append("D");
						System.out.print(s2);
						System.out.print(s1);
					} 
            	} 
        	}
		}.start();
	} 
}
```

### 4.8Lock(锁)

- import java.util.concurrent.locks.ReentrantLock;接口是控制多个线程对共享资源进行访问的工具。锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应先获得Lock对象

- 同步锁使用Lock对象充当。

- ReentrantLock 类实现了 Lock ，它拥有与 synchronized 相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock，可以显式加锁、释放锁。

- **优先使用顺序**：Lock ——同步代码块（已经进入了方法体，分配了相应资源）—— 同步方法（在方法体之外）

- ```java
  class A{
  	private final ReentrantLock lock = new ReenTrantLock();
  	public void m(){
  		lock.lock();
  		try{
  			//保证线程安全的代码; 
          }finally{
  		lock.unlock(); 
  		} 
      } 
  }
  注意：如果同步代码有异常，要将unlock()写入finally语句块
  ```

- ==**synchronized** **与** **Lock** **的对比**==

  1. Lock是显式锁（手动开启和关闭锁，别忘记关闭锁），synchronized是隐式锁，出了作用域自动释放
  2. Lock只有代码块锁，synchronized有代码块锁和方法锁
  3. 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）
  4. 相同：相同的并发性和内存语义，都可以解决线程安全问题

```
package com.ph.locktest;

import java.util.concurrent.locks.ReentrantLock;

/**
 * 测试Lock类的使用
 * @author 彭湖
 * @create 2021-08-10-21:53
 *
 */
class Window implements Runnable{
    private int ticket = 100;
    //1.实例化ReentrantLock
    private ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while(true){
            try{
                //2.调用锁定方法lock()
                lock.lock();
                if(ticket>0){
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()+"买票：票号为："+ticket);
                    ticket--;
                }else{
                    break;
                }
            }finally {
                //3.调用解锁方法unlock()
                lock.unlock();
            }
        }
    }
}
public class LockTest {
    public static void main(String[] args) {
        Window w = new Window();
        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);
        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");
        t1.start();
        t2.start();
        t3.start();
    }
}
```

## 五、线程的通信

### 5.1wait() 

- 令当前线程挂起并放弃CPU、同步资源并等待，使别的线程可访问并修改共享资源，而当前线程排队等候其他线程调用notify()或notifyAll()方法唤醒，唤醒后等待重新获得对监视器的所有权后才能继续执行
  1. 在当前线程中调用方法： 对象名.wait()
  2. 使当前线程进入等待（某对象）状态 ，直到另一线程对该对象发出 notify (或notifyAll) 为止
  3. 调用方法的必要条件：当前线程必须具有对该对象的监控权（加锁）
  4. **调用此方法后，当前线程将释放对象监控权 ，然后进入等待**
  5. 在当前线程被notify后，要重新获得监控权，然后从断点处继续代码的执行
- **==sleep和wait异同：==**
  1. 相同：一旦执行方法，都可以使得当前的线程进入阻塞状态
  2. 不同：
     1. 声明位置不同：Thread类中声明sleep(),Object类中声明wait()
     2. 调用要求不同：sleep()可以在任何场景下使用，wait()必须使用在同步代码中（synchronized）
     3. sleep不会释放锁，wait会释放锁

### 5.2 notify()

- 唤醒正在排队等待同步资源的线程中优先级最高者结束等待
  1. 在当前线程中调用方法： 对象名.notify()
  2. 功能：唤醒等待该对象监控权的一个/所有线程
  3. 调用方法的必要条件：当前线程必须具有对该对象的监控权（加锁）

### 5.3notifyAll()

- 唤醒正在排队等待资源的所有线程结束等待

### 5.4注意点

- 这三个方法只有在synchronized方法或synchronized代码块中才能使用，否则会报java.lang.IllegalMonitorStateException异常。
- 因为这三个方法必须有锁对象调用，而任意对象都可以作为synchronized的同步锁，因此这三个方法只能在Object类中声明。

```
class Communication implements Runnable {
	int i = 1;
	public void run() {
		while (true) {
			synchronized (this) {
				notify();
				if (i <= 100) {
					System.out.println(Thread.currentThread().getName() + ":" + i++);
				} else
					break;
				try {
					wait();
				} catch (InterruptedException e) { 
					e.printStackTrace();
				} 
			} 
		} 
	} 
}
```

### 5.5经典例题：生产者/消费者问题

- 生产者(Productor)将产品交给店员(Clerk)，而消费者(Customer)从店员处取走产品，店员一次只能持有固定数量的产品(比如:20），如果生产者试图生产更多的产品，店员会叫生产者停一下，如果店中有空位放产品了再通知生产者继续生产；如果店中没有产品了，店员会告诉消费者等一下，如果店中有产品了再通知消费者来取走产品。
- 这里可能出现两个问题：
  1. 生产者比消费者快时，消费者会漏掉一些数据没有取到。
  2. 消费者比生产者快时，消费者会取相同的数据。

```Java
package com.ph.locktest;

/**
 * 生产者(Productor)将产品交给店员(Clerk)，而消费者(Customer)从店员处取走产品，店员一次只能持有固定数量的产品(比如:20），
 * 如果生产者试图生产更多的产品，店员会叫生产者停一下，如果店中有空位放产品了再通知生产者继续生产；如果店中没有产品了，
 * 店员会告诉消费者等一下，如果店中有产品了再通知消费者来取走产品。
 *
 * @author 彭湖
 * @create 2021-08-11-10:36
 * <p>
 * 分析：
 * 1.是否是多线程问题？是，生产者线程，消费者线程
 * 2.是否有共享数据？有，店员（或产品）
 * 3.如何解决线程的安全问题？同步机制（3种）
 * 4.是否涉及线程通信？是
 */

class Clerk {
    private int productCount = 0;

    //生产
    public synchronized void produceProduct() {
        if (productCount < 20) {
            productCount++;
            System.out.println(Thread.currentThread().getName() + ":开始生成第" + productCount + "个产品");
            notifyAll();
        } else {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    //消费
    public synchronized void consumeProduct() {
        if (productCount > 0) {
            System.out.println(Thread.currentThread().getName() + ":开始消费第" + productCount + "个产品");
            productCount--;
            notifyAll();
        } else {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Productor extends Thread {//生产者
    private Clerk clerk;

    public Productor() {
    }

    public Productor(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "：开始生产");
        while (true) {
            try {
                //sleep(10);
                Thread.sleep((int) Math.random() * 1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.produceProduct();
        }
    }
}

class Customer extends Thread {//消费者
    private Clerk clerk;

    public Customer() {
    }

    public Customer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "：开始消费");
        while (true) {
            try {
                //sleep(20);
                Thread.sleep((int) Math.random() * 1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            clerk.consumeProduct();
        }
    }
}

public class ProductorTest {
    public static void main(String[] args) {
        System.out.println("");
        Clerk cleck = new Clerk();
        Productor pro1 = new Productor(cleck);
        pro1.setName("生产者1");
        Customer cus1 = new Customer(cleck);
        cus1.setName("消费者1");
        Customer cus2 = new Customer(cleck);
        cus2.setName("消费者2");
        pro1.start();
        cus1.start();
        cus2.start();
    }
}
```

## 六、JDK5.0新增线程创建方式

### 6.1实现Callable接口

1. 与使用Runnable相比， Callable功能更强大些
   1. 相比run()方法，可以有返回值
   2. 方法可以抛出异常
   3. 支持泛型的返回值
   4. 需要借助FutureTask类，比如获取返回结果
2. Future接口
   1. 可以对具体Runnable、Callable任务的执行结果进行取消、查询是否完成、获取结果等
   2. **FutrueTask是Futrue接口的唯一的实现类**
   3. FutureTask 同时实现了Runnable, Future接口。它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值

```java
package com.ph.locktest;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * @author 彭湖
 * @create 2021-08-11-11:27
 */
//1.创建一个实现Callable的实现类
class numThread implements Callable {
    //2.实现call方法，将此线程需要执行的操作声明在call（）中
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}

public class CallableTest {
    public static void main(String[] args) {
        //3.创建Callable接口实现类的对象
        numThread n = new numThread();
        //4.将此Callable接口实现类的对象作为阐述传递到FutureTask构造器中，创建FutureTask的对象
        FutureTask f = new FutureTask(n);
        //5.将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()
        new Thread(f).start();
        try {
            //6.获取Callable中call方法的返回值
            //get()返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值
            Object sum = f.get();
            System.out.println(sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

### 6.2使用线程池

- 背景
  - 经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大
- 思路
  - 提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中的公共交通工具
- 好处
  1. 提高响应速度（减少了创建新线程的时间）
  2. 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
  3. 便于线程管理
     - corePoolSize：核心池的大小
     - maximumPoolSize：最大线程数
     - keepAliveTime：线程没有任务时最多保持多长时间后会终止

- 线程池相关API：**ExecutorService** 和 **Executors**
  1. **ExecutorService** 真正的线程池接口。常见子类ThreadPoolExecutor
     1. void execute(Runnable command) ：执行任务/命令，没有返回值，一般用来执行Runnable
     2. <T> Future<T> submit(Callable<T> task)：执行任务，有返回值，一般又来执行Callable
     3. void shutdown() ：关闭连接池
  2. **Executors工具类、线程池的工厂类，用于创建并返回不同类型的线程池**
     1. Executors.newCachedThreadPool()：创建一个可根据需要创建新线程的线程池
     2. Executors.newFixedThreadPool(n); 创建一个可重用固定线程数的线程池
     3. Executors.newSingleThreadExecutor() ：创建一个只有一个线程的线程池
     4. Executors.newScheduledThreadPool(n)：创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。

```
package com.ph.threadtest;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadPoolExecutor;

/**
 * 创建线程的方式4：创建线程池
 *
 * @author 彭湖
 * @create 2021-08-11-11:49
 */
class NumberThread implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {

            System.out.println(Thread.currentThread().getName() + ":"+i);
        }
    }
}
class NumberThread1 implements Runnable {

    @Override
    public void run() {
        for (int i = 10; i < 20; i++) {

            System.out.println(Thread.currentThread().getName() + ":"+i);
        }
    }
}
public class Exe1 {
    public static void main(String[] args) {
        //1.提供指定线程数量的线程池
        ExecutorService service = Executors.newFixedThreadPool(10);
        ThreadPoolExecutor service1 = (ThreadPoolExecutor) service;
        service1.setCorePoolSize(15);
//        service1.setKeepAliveTime(100);
        //设置线程池的属性
        System.out.println(service.getClass());
        //2.执行指定的线程的操作，需要提供实现Runnable接口或者Callable接口实现类的对象
        service.execute(new NumberThread());//适合使用于Runnable
        service.execute(new NumberThread1());
//        service.submit();//适合使用于Callable
        //3.关闭连接池
        service.shutdown();
    }
}
```

## 七、问题

```
package com.ph.threadtest;

/**
 * 写两个线程，一个线程打印1-52,另一个线程打印A-Z,打印顺序为12A34B…5152Z
 *
 * @author 彭湖
 * @create 2021-08-11-11:49
 */

//打印1-52
class MyThread1 extends Thread {

    @Override
    public synchronized void run() {
        notify();
        for (int i = 1; i <= 52; i++) {
            System.out.println(i);
        }
        try {
            wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

//打印A-Z
class MyThread2 extends Thread {

    @Override
    public synchronized void run() {
        notify();
        for (int i = 0; i < 26; i++) {
            System.out.println((char) ('A' + i));
        }
        try {
            wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
class MyThread implements Runnable{

    @Override
    public void run() {

    }
    public synchronized void myThread(){

        for (int i = 1; i <= 52; i++) {
            System.out.println(i);
        }

        for (int i = 0; i < 26; i++) {
            System.out.println((char) ('A' + i));
        }

    }
}
public class Exe2 {
    public static void main(String[] args) {
        MyThread1 myThread1 = new MyThread1();
        MyThread2 myThread2 = new MyThread2();
        myThread1.start();
        myThread2.start();
    }
}

```





```
package com.ph.threadtest;

/**
 * 编写一个程序，启动三个线程，三个线程的名称分别是A,B,C；
     每个线程将自己的名字在屏幕上打印5遍，打印顺序是
     ABCABCABCABCABC
 * @author 彭湖
 * @create 2021-08-11-11:50
 */

class MyThread3 implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            synchronized (this){
                notifyAll();
                System.out.println(Thread.currentThread().getName());
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
public class Exe3 {
    public static void main(String[] args) {
        MyThread3 myThread3 = new MyThread3();
        Thread t1 = new Thread(myThread3);
        Thread t2 = new Thread(myThread3);
        Thread t3 = new Thread(myThread3);
        t1.setName("A");
        t2.setName("B");
        t3.setName("C");
        t1.setPriority(Thread.MAX_PRIORITY);
        t3.setPriority(Thread.MIN_PRIORITY);
        t1.start();
        t2.start();
        t3.start();
    }
}

```













