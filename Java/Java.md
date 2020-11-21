# 1、Java基础



### 面向对象的三个基本特征

面向对象的三个基本特征是：封装、继承、多态。

#### 封装

​	封装，也就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。

#### 继承

​	继承是从已有的类中派生出新的类， 新的类能吸收已有类的数据属性和行为，并能扩展新的能力。

#### 多态

​	多态性是指在面向对象技术中，当不同的多个对象同时接收到同一个完全相同的消息之后，所表现出来的动作是各不相同的，具有多种形态；

​	实现多态，有二种方式，覆盖，重载。覆盖是指子类重新定义父类的虚函数的做法；重载是指允许存在多个同名函数，而这些函数的参数表不同（或许参数个数不同，或许参数类型不同，或许两者都不同）。



​	封装可以隐藏实现细节，使得代码模块化；继承可以扩展已存在的代码模块（类）；它们的目的都是为了——代码重用。而多态则是为了实现另一个目的——接口重用！多态的作用，就是为了类在继承和派生的时候，保证使用“家谱”中任一类的实例的某一属性时的正确调用。





### 关键字

#### final

- 修饰类 声明为final的类不能派生子类，即此类不能被继承；
  - public final class Person{ }
- 修饰变量 表示它为一个常量，变量一旦初始化，将不能改变；
  - final int COUNT = 5; 
- 修饰方法 表示向编译器表明子类不能重写此方法； 
  -  public final void eat(){ }





### 反射

Java反射机制是指在运行时去获取一个类的变量和方法信息，然后通过获取到的信息来创建对象，调用方法的一种机制。由于这种动态性，可以极大程度的增强程序的灵活性，程序不用再编译期就能完成确定，在运行期任然可以扩展。

Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：

- **Field** ：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
- **Method** ：可以使用 invoke() 方法调用与 Method 对象关联的方法；
- **Constructor** ：可以用 Constructor 的 newInstance() 创建新的对象。



#### 获得Class对象

- 使用Class类的 **forName** 静态方法
- 直接获取某一个对象的 **Class**
- 调用某个对象的 **getClass**() 方法



#### 反射获取构造方法并使用

```Java
// c为Class
Constructor<?>[] cons = c.getConstructos();
Constructor<?>[] cons = c.getDeclaredConstructors();
Constructor con = c.getConstructor(参数)
Constructor con = c.getDeclaredConstructor(参数)

// 通过反射创建对象
con.newInstance(参数)
    
// 如果newInstance调用非公共构造函数，会报错，此时需要使用 暴力反射
con.setAccessible(true);   
```



#### 反射获取成员变量并使用

```Java
Field[] fields = c.getFields();
Field[] fields = c.getDeclaredConstructors();
Field field = c.getField(FieldName);
Field field = c.getDeclaredField(FieldName);

// 通过反射给对象obj复制   obj.field = val;
field.set(obj, val);

// 暴力反射  赋值给非公有属性
field.setAccessible(true)
```



#### 反射获取方法并使用

```Java
Method[] methods = c.getMethods();
Method[] methods = c.getDeclaredMethods();
Method method = c.getMethod(MethodName)
Method method = c.getDeclaredMethod(MethodName);

// 调用obj对象的method方法，并传入相应的参数
method.invoke(obj, 参数)

// 暴力反射 使用非公有方法
method.setAccessible(true)
```



#### 反射练习

**练习1**

有一个ArrayList<Integer>集合，需要往集合插入一个String数据，如何实现

```java
public class ReflectionDemo1 {

	public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        
		ArrayList<Integer> array = new ArrayList<Integer>();
		array.add(10);
		array.add(20);

		Class<? extends ArrayList> c = array.getClass();
		Method m = c.getMethod("add", Object.class);
		m.invoke(array, "hello");
		m.invoke(array, "world");

		System.out.println(array);
	}
}
```



**练习2**

通过反射来运行类中的方法

```Java
public class Student {
	public void study() { System.out.println("好好学习天天向上");}
}

public class Teacher {
	public void teach() { System.out.println("用爱成就"); }
}

```

```Java
class.txt

className = reflection.Teacher
methodName = teach
```

```
public class ReflectionDemo2 {

	public static void main(String[] args) throws IOException, ClassNotFoundException, IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchMethodException {
	
		// 加载数据
		Properties properties = new Properties();
		FileReader fr = new FileReader("E:\\code\\ideaData\\interview\\src\\reflection\\class.txt");
		properties.load(fr);
		fr.close();

		/**
		 * 此时得到了如下数据
		 * className = reflection.Student2
		 * methodName = study
		 */
		 
		String className = properties.getProperty("className");
		String methodName = properties.getProperty("methodName");

		// 通过反射来使用

		Class<?> c = Class.forName(className);
		Constructor<?> constructor = c.getConstructor();
		Object obj = constructor.newInstance();

		Method m = c.getMethod(methodName);
		m.invoke(obj);
	}
}

```







# 2、JVM



### 类加载机制



#### 类加载

程序使用某个类时，如果类还未被加载到内存，系统会通过类的加载，类的连接，类的初始化来对类进行初始化。这三个步骤统称为类加载或类初始化。



- **类的加载**

  ​	将class文件读入内存，并为之创建一个java.lang.Class对象

- **类的连接**

  ​	验证阶段：检查被加载的类是否正确，与其他类协调一致

  ​	准备阶段：为类的类变量分配内存，并设置初始值

  ​	解析阶段：将二进制数据中的符号引用替换为直接引用

- **类的初始化**

  ​	对类变量进行初始化，运行静态块



类初始化步骤

- 类未被加载，程序先加载并连接该类
- 类的父类还未被初始化，先初始化其直接父类
- 类中如果有初始化语句，则系统依次执行初始化语句







#### 类初始化时机

**1、主动引用**

虚拟机规范中并没有强制约束何时进行加载，但是规范严格规定了有且只有下列五种情况必须对类进行初始化（加载、验证、准备都会随之发生）：

- 遇到 new、getstatic、putstatic、invokestatic 这四条字节码指令时，如果类没有进行过初始化，则必须先触发其初始化。最常见的生成这 4 条指令的场景是：使用 new 关键字实例化对象的时候；读取或设置一个类的静态字段（被 final 修饰、已在编译期把结果放入常量池的静态字段除外）的时候；以及调用一个类的静态方法的时候。
- 使用 java.lang.reflect 包的方法对类进行反射调用的时候，如果类没有进行初始化，则需要先触发其初始化。
- 当初始化一个类的时候，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化。
- 当虚拟机启动时，用户需要指定一个要执行的主类（包含 main() 方法的那个类），虚拟机会先初始化这个主类；
- 当使用 JDK 1.7 的动态语言支持时，如果一个 java.lang.invoke.MethodHandle 实例最后的解析结果为 REF_getStatic, REF_putStatic, REF_invokeStatic 的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化；



**2、被动引用**

以上 5 种场景中的行为称为对一个类进行主动引用。除此之外，所有引用类的方式都不会触发初始化，称为被动引用。被动引用的常见例子包括：

- 通过子类引用父类的静态字段，不会导致子类初始化。

```
System.out.println(SubClass.value);  // value 字段在 SuperClass 中定义
```

- 通过数组定义来引用类，不会触发此类的初始化。该过程会对数组类进行初始化，数组类是一个由虚拟机自动生成的、直接继承自 Object 的子类，其中包含了数组的属性和方法。

```
SuperClass[] sca = new SuperClass[10];
```

- 常量在编译阶段会存入调用类的常量池中，本质上并没有直接引用到定义常量的类，因此不会触发定义常量的类的初始化。

```
System.out.println(ConstClass.HELLOWORLD);
```





#### 类加载器

- 启动类加载器 BootStrap Classloader

  ​	执行 JAVA_HOME/jre/lib 文件下

- 扩展类加载器 Extension ClassLoader

  ​	执行JAVA_HOME/jre/lib/ext

- Application ClassLoader

  ​	classpath

- 自定义类加载器

  ​	加载自定义的类







# 3、Java并发



## 基本概念



### sleep、wait、yield、join的区别

**sleep：**不会释放锁，只会阻塞线程，将 cpu 让给其他线程，指定时间到了则恢复运行过程，可中断， sleep 给其他线程运行时不考虑线程的优先级，因此会给低优先级的线程运行的机会。

**wait：** Object 的方法， wait 必须在有锁条件下 (SYNC) 下使用，wait 释放锁。

**yield：**Thread 的方法，暂停当前正在执行的线程对象，不会释放锁，和 sleep 不同的是 yield方法并不会让线程进入阻塞状态，而是让线程重回**就绪状态**，它只需要等待重新获取CPU执行时间，所以**执行yield()的线程有可能在进入到可执行状态后马上又被执行**。还有一点和 sleep 不同的是 **yield 方法只能使同优先级或更高优先级的线程有执行的机会**。

**join：**让一个线程等待另外一个线程完成才继续执行。如果线程A执行体中调用B线程的join()方法，则A线程将会被阻塞，直到B线程执行完为止，A才能得以**继续执行**。

### 重入锁

某个线程试图获得一个已经由他自己持有的锁。



### 进程和线程的区别

- 线程是程序执行的最小单位，而进程是操作系统分配资源的最小单位；
- 一个进程由一个或多个线程组成，线程是一个进程中代码的不同执行路。
- 进程之间相互独立，但同一进程下的各个线程之间共享程序的内存空间(包括代码段，数据集，堆等)及一些进程级的资源(如打开文件和信号等)，某进程内的线程在其他进程不可见；
- 调度和切换：线程上下文切换比进程上下文切换要快得多



### 锁池和等待池

- **锁池**：假设线程A已经拥有了某个对象(注意:不是类)的锁，而其它的线程想要调用这个对象的某个synchronized方法(或者synchronized块)，由于这些线程在进入对象的synchronized方法之前必须先获得该对象的锁的拥有权，但是该对象的锁目前正被线程A拥有，所以这些线程就进入了该对象的锁池中。
- **等待池**：假设一个线程A调用了某个对象的wait()方法，线程A就会释放该对象的锁后，进入到了该对象的等待池中。



等待池不竞争锁，锁池竞争锁。

notify() 唤醒一个 wait 线程，notifyAll() 唤醒等待池中所有线程（等待池 ---> 锁池） 

所以 notify 可能会导致死锁，但是 notifyAll 不会。



### FutureTask



**类继承结构：** FutureTask 实现了 RunnableFuture 接口，而 RunnableFuture 继承了 Runnable 和 Future ，也就是说 FutureTask 既是Runnable ，也是 Future 。



<img src="https://upload-images.jianshu.io/upload_images/11183270-33a6924a765124e1.png?imageMogr2/auto-orient/strip|imageView2/2/w/1018/format/webp" alt="img" style="zoom:50%;" />



**FutureTask 构造方法**

```Java
    // 构造方法1
    public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // ensure visibility of callable
    }
    // 构造方法2
    public FutureTask(Runnable runnable, V result) {
        //将runnable包装成callable
        this.callable = Executors.callable(runnable, result);
        this.state = NEW;       // ensure visibility of callable
    }
```



```Java
private static final int NEW          = 0; // 任务新建和执行中

private static final int COMPLETING   = 1; // 任务将要执行完毕

private static final int NORMAL       = 2; // 任务正常执行结束

private static final int EXCEPTIONAL  = 3; // 任务异常

private static final int CANCELLED    = 4; // 任务取消
 
private static final int INTERRUPTING = 5; // 任务线程即将被中断

private static final int INTERRUPTED  = 6; // 任务线程已中断
```



### ThreadLocal

​	变量共享可以使用 **public static** ，所有线程都可以共享这个变量。如果想实现每一个线程都有自己的共享变量，可以使用 **ThreadLocal**。

​	解决了变量在不同线程间的隔离性，不同的线程拥有自己的值，不同线程中的值是可以放入 **ThreadLocal** 中进行保存的。



**ThreadLocal 和 Synchronized 区别**

ThreadLocal和Synchronized都是为了解决多线程中相同变量的访问冲突问题，不同的点是

- Synchronized是通过线程等待，牺牲时间来解决访问冲突
- ThreadLocal是通过每个线程单独一份存储空间，牺牲空间来解决冲突，并且相比于Synchronized，ThreadLocal具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问到想要的值。



### Volatile

1、保证可见性 

2、防止指令重排



**可见性：**

<img src="https://images0.cnblogs.com/i/475287/201403/091134177063947.jpg" alt="img" style="zoom:67%;" />



​		线程将**共享变量P**从**主内存**拷贝到自己的线程独有的**工作内存**中，在工作内存中进行读取和修改，然后再将**变量P**从**工作内存**传递到**主内存**进行更新。

​		但是若A、B线程同时从主内存读取共享变量，A线程修改共享变量后，则直接load到主内存中，不会告知B线程已经修改了，B线程依旧用自己的工作区域的共享值，并不知道变化。

​		**volatile 强制线程必须从主内存中修改共享变量。**

​		volatile 保证了可见性，但是不能解决多个线程同时修改**共享变量**带来的不一致问题。

```Java
/*
两个线程A、B，同时进入 if 语句，虽然是volatile，A、B线程均已经进入到了if语句中，之后修改也来不及了。

public Class Test {

    volatile int flag = 1;
    
    public void test() {
        if(flag == 1) {  // flag还没来得及修改，多个线程同时进入
            System.out.println("第一个到达！你是唯一的第一");
            flag = 0;
        }
    }
    
}


```







## 线程间的状态转换

| 线程状态                | 导致状态发生改变的条件                                       |
| ----------------------- | ------------------------------------------------------------ |
| NEW(新建)               | 线程刚被创建,但是并未启动,还没有调用start方法                |
| Runnable(可运行)        | 线程可以在java虚拟机中运行的状态,可能正在运行自己代码,也可能没有,这取决于操作系统处理器 |
| Blocked(锁阻塞)         | 当一个线程试图获取一个对象锁,而该对象锁被其他的线程持有,则该线程进入Blocked状态；当该线程持有锁时,该线程将变成Runnable状态 |
| Timed Waiting(计时等待) | 同waiting状态,有几个方法有超时参数,调用他们将进入Timed Waiting状态,这一状态将一直保持到超时期满或者接收到唤醒通知,带有超时参数的常用方法有Thread.sleep、锁对象.wait() |
| Waiting(无限等待)       | 一个线程在等待另一个线程执行一个(唤醒)动作时，该线程进入Waiting状态。进入这个状态后是不能自动唤醒的，必须等待另一个线程调用notify或者notifyAll方法才能够唤醒 |
| Teminated(被终止)       | 因为run方法正常退出而死亡，或者因为没有捕获的异常终止了run方法而死亡 |





## 死锁

​	两个或两个以上的进程在执行过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去。

### 死锁的条件

**1、互斥条件：**指进程对所分配到的资源进行排它性使用，即在一段时间内某资源只由一个进程占用。如果此时还有其它进程请求资源，则请求者只能等待，直至占有资源的进程用毕释放。

**2、请求和保持条件：**指进程已经保持至少一个资源，但又提出了新的资源请求，而该资源已被其它进程占有，此时请求进程阻塞，但又对自己已获得的其它资源保持不放。

**3、不剥夺条件：**指进程已获得的资源，在未使用完之前，不能被剥夺，只能在使用完时由自己释放。

**4、环路等待条件：**指在发生死锁时，必然存在一个进程——资源的环形链，即进程集合{P0，P1，P2，···，Pn}中的P0正在等待一个P1占用的资源；P1正在等待P2占用的资源，……，Pn正在等待已被P0占用的资源。



### 死锁解决



**死锁预防**

- 资源一次性分配：一次性分配所有资源，这样就不会再有请求了：（破坏请求条件）
- 只要有一个资源得不到分配，也不给这个进程分配其他的资源：（破坏请保持条件）
- 可剥夺资源：即当某进程获得了部分资源，但得不到其它资源，则释放已占有的资源（破坏不可剥夺条件）
- 资源有序分配法：系统给每类资源赋予一个编号，每一个进程按编号递增的顺序请求资源，释放则相反（破坏环路等待条件）



这是一种较简单和直观的事先预防的方法。方法是通过设置某些限制条件，去破坏产生死锁的四个必要条件中的一个或者几个，来预防发生死锁。预防死锁是一种较易实现的方法，已被广泛使用。但是由于所施加的限制条件往往太严格，可能会导致系统资源利用率和系统吞吐量降低。



**死锁避免**

系统对进程发出的每一个系统能够满足的资源申请进行动态检查，并根据检查结果决定是否分配资源；如果分配后系统可能发生死锁，则不予分配，否则予以分配。这是一种保证系统不进入死锁状态的动态策略。



**死锁解决**

- 剥夺资源：从其它进程剥夺足够数量的资源给死锁进程，以解除死锁状态；
- 撤消进程：可以直接撤消死锁进程或撤消代价最小的进程，直至有足够的资源可用，死锁状态消除为止；所谓代价是指优先级、运行代价、进程的重要性和价值等。





### 银行家算法

避免死锁的著名算法

​	在银行中，客户申请贷款的数量是有限的，每个客户在第一次申请贷款时要声明完成该项目所需的最大资金量，在满足所有贷款要求时，客户应及时归还。银行家在客户申请的贷款数量不超过自己拥有的最大值时，都应尽量满足客户的需要。在这样的描述中，银行家就好比操作系统，资金就是资源，客户就相当于要申请资源的进程。







## 锁优化

锁一共有4种状态，级别从低到高依次是：无锁状态、偏向锁状态、轻量级锁状态和重量级锁状态。

偏向锁：大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁。无竞争时不需要进行CAS操作来加锁和解锁。

轻量级锁：无竞争时通过CAS操作来加锁和解锁。（自旋锁——是一种锁的机制，不是状态）

重量级锁：真正的加锁操作



**锁粒度变细**

方法里只有一句话需要Sync，整个方法Sync 到 只有那句话 Sync，即加锁的范围变小，即锁的粒度变细。







## Java内存模型

Java 内存模型试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。



### Happens-Before规则

无需任何同步手段就可以保证的

 1）程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。
 2）监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。
 3）volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。
 4）传递性：如果A happens-before B，且B happens-before C，那么A happens-before C。
 5）start()规则：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作。
 6）join()规则：如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。
 7 ）线程中断规则：对线程interrupt方法的调用happens-before于被中断线程的代码检测到中断事件的发生。





# 4、Java容器



### 哈希表

处理冲突的方法：

- 开放定址法
  - 线性探测
  - 再平方探测
  - 伪随机探测

- 链式地址法

  HashMap冲突的解决方法

  对于相同的值，采用链表进行连接。使用数组存储每一个链表

- 建立公共溢出区

- 再哈希法







# 5、数据结构



## 二叉排序树

二叉排序树 又称 二叉查找树 和 二叉搜索树。



**概念**：一棵空树，或者是具有下列性质的二叉树

- 若左子树不空，则左子树上所有结点的值均小于它的根结点的值；
- 若右子树不空，则右子树上所有结点的值均大于或等于它的根结点的值；
- 左、右子树也分别为二叉排序树；



