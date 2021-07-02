# 初识JVM

## 基本常识

### 程序的执行方式

静态编译执行、动态编译执行、动态解释执行

注意：此处所说的编译指的是编译成可让操作系统直接执行的机器码。



### 一次编译，到处运行

![1624264951865](D:\repositories\others\research\java\jvm\images\1624264951865.png)

### 字节码和机器码的区别

​	机器码是电脑CPU直接读取运行的机器指令，运行速度最快，但是非常晦涩难懂，也比较难编写，一般从业人员接触不到。
​	字节码是一种中间状态（中间码）的二进制代码（文件）。需要直译器转译后才能成为机器码。



### JDK、JRE与JVM的关系

![1624260939687](D:\repositories\others\research\java\jvm\images\1624260939687.png)



### OracleJDK和OpenJDK

#### 查看JDK的版本

```shell
java -version

#如果是OracleJDK，显示详细为
#
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07) #Java运行时环境(即JRE)的版本信息
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode) #表明此JDK的JVM是Oracle的64位HotSpot虚拟机，运行在Server模式下(虚拟机有Server和Client两种运行模式).

#如果是OpenJDK，显示详细为
openjdk version "1.8.0_144"
OpenJDK Runtime Environment (build 1.8.0_144-b01)
OpenJDK 64-Bit Server VM (build 25.144-b01， mixed mode)
```



#### OpenJDK 的来历

​	Java由SUN公司(Sun Microsystems， 发起于美国斯坦福大学， SUN是Stanford University Network的缩写)发明， 2006年SUN公司将Java开源， 此时的JDK即为OpenJDK。

​	也就是说， OpenJDK是Java SE的开源实现， 它由SUN和Java社区提供支持， 2009年Oracle收购了Sun公司， 自此Java的维护方之一的SUN也变成了Oracle。

​	大多数JDK都是在OpenJDK的基础上编写实现的， 比如IBM J9， Azul Zulu， Azul Zing和OracleJDK. 几乎现有的所有JDK都派生自OpenJDK， 它们之间不同的是许可证：OpenJDK根据许可证GPL v2发布，Oracle JDK根据Oracle二进制代码许可协议获得许可。



#### Oracle JDK的来历

​	Oracle JDK之前被称为SUN JDK， 这是在2009年Oracle收购SUN公司之前， 收购后被命名为OracleJDK。实际上， Oracle JDK是基于OpenJDK源代码构建的， 因此Oracle JDK和OpenJDK之间没有重大的技术差异。Oracle的项目发布经理Joe Darcy在OSCON 2011 上对两者关系的介绍也证实了OpenJDK 7和Oracle JDK 7在程序上是非常接近的， 两者共用了大量相同的代码(如下图)

​	注意: 图中提示了两者共同代码的占比要远高于图形上看到的比例， 所以我们编译的OpenJDK基本上可以认为性能、功能和执行逻辑上都和官方的Oracle JDK是一致的。

![1624262655514](D:\repositories\others\research\java\jvm\images\1624262655514.png)



#### Oracle JDK与OpenJDK的区别

- OpenJDK使用的是开源免费的FreeType， 可以按照GPL v2许可证使用.GPL V2允许在商业上使用;
- Oracle JDK则采用JRL(Java Research License，Java研究授权协议) 放出.JRL只允许个人研究使用，要获得Oracle JDK的商业许可证， 需要联系Oracle的销售人员进行购买。



### JVM和Hotspot的关系

​	JVM是《JVM虚拟机规范》中提出来的规范。

​	Hotspot是使用JVM规范的商用产品，除此之外还有Sun的Classic VM， BEA的JRockit、IBM的J9、GraalVM等等

​	Oracle公司在分别收购了BEA公司和Sun公司后，将HotSpot VM和JRocket VM进行整合，在Hotspot的基础上，移植了JRocket的优秀特性。



### JVM和Java的关系

![1624263328286](D:\repositories\others\research\java\jvm\images\1624263328286.png)



### JVM的运行模式

JVM有两种运行模式：Server模式与Client模式。两种模式的区别在于：

- Client模式启动速度较快，Server模式启动较慢；
- 但是启动进入稳定期长期运行之后Server模式的程序运行速度比Client要快很多。
- 因为Server模式启动的JVM采用的是重量级的虚拟机，对程序采用了更多的优化；而Client模式启动的JVM采用的是轻量级的虚拟机。所以Server启动慢，但稳定后速度比Client远远要快。



## JVM架构图

![20190216114129109](D:\repositories\others\research\java\jvm\images\20190216114129109.png)



## JVM程序执行流程

Java编译成字节码、动态编译和解释为机器码的过程：

![1624265506213](D:\repositories\others\research\java\jvm\images\1624265506213.png)

编译器和解释器的协调工作流程：

![QQ截图20210621165630](D:\repositories\others\research\java\jvm\images\QQ截图20210621165630-1624267907794.png)



​	在部分商用虚拟机中（如HotSpot），Java程序最初是通过解释器（Interpreter）进行解释执行的，当虚拟机发现某个方法或代码块的运行特别频繁时，就会把这些代码认定为“热点代码”。为了提高热点代码的执行效率，在运行时，虚拟机将会把这些代码编译成与本地平台相关的机器码，并进行各种层次的优化，完成这个任务的编译器称为即时编译器（Just In Time Compiler，下文统称JIT编译器）。

​	由于Java虚拟机规范并没有具体的约束规则去限制即使编译器应该如何实现，所以这部分功能完全是与
虚拟机具体实现相关的内容。

​	我们的JIT是属于动态编译方式的，动态编译（dynamic compilation）指的是“在运行时进行编译”；与之相对的是事前编译（ahead-of-time compilation，简称AOT），也叫静态编译（static compilation）。

​	JIT编译（just-in-time compilation）狭义来说是当某段代码即将第一次被执行时进行编译，因而叫“即时编译”。JIT编译是动态编译的一种特例。JIT编译一词后来被泛化，时常与动态编译等价；但要注意广义与狭义的JIT编译所指的区别。



### 热点代码

​	程序中的代码只有是热点代码时，才会编译为本地代码，那么什么是热点代码呢？运行过程中会被即时编译器编译的“热点代码”有两类：

- 被多次调用的方法。

- 被多次执行的循环体。

​        两种情况，编译器都是以整个方法作为编译对象。 这种编译方法因为编译发生在方法执行过程之中，因此形象的称之为栈上替换（On Stack Replacement，OSR），即方法栈帧还在栈上，方法就被替换了。



### 热点检测方式

​	要知道方法或一段代码是不是热点代码，是不是需要触发即时编译，需要进行Hot Spot Detection（热点探测）。目前主要的热点探测方式有以下两种：

- 基于采样的热点探测

  ​	采用这种方法的虚拟机会周期性地检查各个线程的栈顶，如果发现某些方法经常出现在栈顶，那这个方法就是“热点方法”。这种探测方法的好处是实现简单高效，还可以很容易地获取方法调用关系（将调用堆栈展开即可），缺点是很难精确地确认一个方法的热度，容易因为受到线程阻塞或别的外界因素的影响而扰乱热点探测。

- 基于计数器的热点探测

  ​	采用这种方法的虚拟机会为每个方法（甚至是代码块）建立计数器，统计方法的执行次数，如果执行次数超过一定的阀值，就认为它是“热点方法”。这种统计方法实现复杂一些，需要为每个方法建立并维护计数器，而且不能直接获取到方法的调用关系，但是它的统计结果相对更加精确严谨。

​	在HotSpot虚拟机中使用的是第二种——基于计数器的热点探测方法，因此它为每个方法准备了两个计数器：方法调用计数器和回边计数器。在确定虚拟机运行参数的前提下，这两个计数器都有一个确定的阈值，当计数器超过阈值溢出了，就会触发JIT编译。



#### 方法调用计数器

​	顾名思义，这个计数器用于统计方法被调用的次数。

​	在JVM client模式下的阀值是1500次，Server是10000次。可以通过虚拟机参数： -XX：CompileThreshold设置。

​	默认情况下，如果一段时间内方法调用计数器的值没有超过虚拟机设置的阈值，则在垃圾回收时计数器会热度衰减，数值减少 1/2，此时间范围又被称为半衰期。所以方法调用计数器中存储的实际上并不是方法调用的绝对次数。可通过-XX:UseCounterDecay 设置 true/false 来开启/关闭热度衰减（默认开启），通过-XX:CounterHalfLifeTime 设置半衰期的周期，单位是秒。

​	当计数器超过阈值，会另起一个线程进行编译，而当前任以解释方式执行。

![1624327214966](D:\repositories\others\research\java\jvm\images\1624327214966.png)

#### 回边计数器

​	它的作用就是统计一个方法中循环体代码执行的次数，在字节码中遇到控制流向后跳转的指令称为“回边”。



## JIT使用

### 为什么要使用解释器与编译器并存的架构

- 时间开销：我们一般说的编译比解释快指的是“执行编译后的代码”比“解释器解释执行”要快。而JIT编译的时间是要比解释执行一次略慢一些的。所以对于频繁执行的代码进行编译才能保证有正面的收益，对只执行一次的代码做编译是得不偿失的。
- 空间开销：JIT编译后的代码相对于字节码而言要大得多。与时间一样，对于频繁执行的代码才值得编译。对所有代码编译会导致空间消耗大大增加。
- 当程序需要迅速启动和执行的时候，解释器可以首先发挥作用，省去编译的时间，立即执行。在程序运行后，随着时间的推移，编译器逐渐发挥作用，把越来越多的代码编译成本地代码之后，可以获取更高的执行效率
- 当程序运行环境中内存资源限制较大（如部分嵌入式系统中），可以使用解释器执行节约内存，反之可以使用编译执行来提升效率。

- 两种方式并存可以让我们在时间和空间中寻求一种最优解。



### 为何要实现两个不同的即时编译器

​	HotSpot虚拟机中内置了两个即时编译器：Client Complier和Server Complier，简称为C1、C2编译器，分别用在客户端和服务端。

​	目前主流的HotSpot虚拟机中默认是采用解释器与其中一个编译器直接配合的方式工作。程序使用哪个编译器，取决于虚拟机运行的模式。HotSpot虚拟机会根据自身版本与宿主机器的硬件性能自动选择运行模式，用户也可以使用“-client”或“-server”参数去强制指定虚拟机运行在Client模式或Server模式。

​	用Client Complier获取更高的编译速度，用Server Complier 来获取更好的编译质量。为什么提供多个即时编译器与为什么提供多个垃圾收集器类似，都是为了适应不同的应用场景。

### 如何编译为本地代码

​	Server Compiler和Client Compiler两个编译器的编译过程是不一样的。
​	对Client Compiler来说，它是一个简单快速的编译器，主要关注点在于局部优化，而放弃许多耗时较长的全局优化手段。
​	而Server Compiler则是专门面向服务器端的，并为服务端的性能配置特别调整过的编译器，是一个充分优化过的高级编译器，它执行所有经典的优化动作，如无用代码消除、循环展开、常量传播等。



## JIT优化

### 公共子表达式的消除

​	公共子表达式消除是一个普遍应用于各种编译器的经典优化技术，他的含义是：如果一个表达式E已经计算过了，并且从先前的计算到现在E中所有变量的值都没有发生变化，那么E的这次出现就成为了公共子表达式。对于这种表达式，没有必要花时间再对他进行计算，只需要直接用前面计算过的表达式结果代替就可以了。

- 如果这种优化仅限于程序的基本块内，便称为局部公共子表达式消除（Local Common Subexpression Elimination）

- 如果这种优化范围涵盖了多个基本块，那就称为全局公共子表达式消除（Global Common Subexpression Elimination）。

  

  举个简单的例子来说明他的优化过程，假设存在如下代码：

```java
int d = (c*b)*12+a+(a+b*c); 
```

​	如果这段代码交给Javac编译器则不会进行任何优化，那生成的代码如下所示，是完全遵照Java源码的写法直译而成的。

```
iload_2 // b
imul // 计算b*c
bipush 12 // 推入12
imul // 计算(c*b)*12
iload_1 // a
iadd // 计算(c*b)*12+a
iload_1 // a
iload_2 // b
iload_3 // c
imul // 计算b*c
iadd // 计算a+b*c
iadd // 计算(c*b)*12+a+(a+b*c)
istore 4
```

​	当这段代码进入到虚拟机即时编译器后，他将进行如下优化：编译器检测到”cb“与”bc“是一样的表达式，而且在计算期间b与c的值是不变的。因此，这条表达式就可能被视为：

```java
int d = E*12+a+(a+E);
```

​	这时，编译器还可能（取决于哪种虚拟机的编译器以及具体的上下文而定）进行另外一种优化：代数化简（Algebraic Simplification），把表达式变为：

```java
int d = E*13+a*2;
```

​	表达式进行变换之后，再计算起来就可以节省一些时间了。



### 方法内联

​	在使用JIT进行即时编译时，将方法调用直接使用方法体中的代码进行替换，这就是方法内联，减少了方法调用过程中压栈与入栈的开销。

​	例如，下面的代码：

```java
private int add1(int x1， int x2， int x3， int x4) {
	return add2(x1， x2) + add2(x3， x4);
}
private int add2(int x1， int x2) {
	return x1 + x2;
}
```

​	在运行一段时候后，JVM会把代码优化为：

```java
private int add1(int x1， int x2， int x3， int x4) {
     return x1 + x2 + x3 + x4;
}
```



### 逃逸分析

​	逃逸分析(Escape Analysis)是目前Java虚拟机中比较前沿的优化技术。这是一种可以有效减少Java程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。**通过逃逸分析，Java Hotspot编译器能够分析出一个新的对象的引用的使用范围从而决定是否要将这个对象分配到堆上。**
​	逃逸分析的基本行为就是分析对象动态作用域：当一个对象在方法中被定义后，它可能被外部方法所引用，例如作为调用参数传递到其他地方中，称为方法逃逸。

​	逃逸分析包括：

- 全局变量赋值逃逸

- 方法返回值逃逸

- 实例引用发生逃逸

- 线程逃逸:赋值给类变量或可以在其他线程中访问的实例变量

  例如：

```java
public class EscapeAnalysis {
  //全局变量
  public static Object object;
 
  public void globalVariableEscape(){//全局变量赋值逃逸 
    object = new Object(); 
  } 
 
  public Object methodEscape(){  //方法返回值逃逸
    return new Object();
  }
 
  public void instancePassEscape(){ //实例引用发生逃逸
    this.speak(this);
  }
 
  public void speak(EscapeAnalysis escapeAnalysis){
    System.out.println("Escape Hello");
  }
}
```

​	再例如如下的方法：

```java
public static StringBuffer craeteStringBuffer(String s1， String s2) {
	StringBuffer sb = new StringBuffer();
	sb.append(s1);
	sb.append(s2);
	return sb;
}
```

​	其中，StringBuffer sb是一个方法内部变量，上述代码中直接将sb返回，这样这个StringBuffer有可能被其他方法所改变，这样它的作用域就不只是在方法内部，虽然它是一个局部变量，称其逃逸到了方法外部。甚至还有可能被外部线程访问到，譬如赋值给类变量或可以在其他线程中访问的实例变量，称为线程逃逸。

​	上述代码如果想要StringBuffer sb不逃出方法，可以这样写：

```java
public static String createStringBuffer(String s1， String s2) {
	StringBuffer sb = new StringBuffer();
	sb.append(s1);
	sb.append(s2);
	return sb.toString();
}
```



​	使用逃逸分析，编译器可以对代码做如下优化：

- 同步省略。如果一个对象被发现只能从一个线程被访问到，那么对于这个对象的操作可以不考虑同步。
- 将堆分配转化为栈分配。如果一个对象在子程序中被分配，要使指向该对象的指针永远不会逃逸，对象可能是栈分配的候选，而不是堆分配。
- 分离对象或标量替换。有的对象可能不需要作为一个连续的内存结构存在也可以被访问到，那么对象的部分（或全部）可以不存储在内存，而是存储在CPU寄存器中。



​	在Java代码运行时，通过JVM参数可指定是否开启逃逸分析

```shell
-XX:+DoEscapeAnalysis ： 表示开启逃逸分析
-XX:-DoEscapeAnalysis ： 表示关闭逃逸分析
```



### 对象的栈上内存分配

​	我们知道，在一般情况下，对象和数组元素的内存分配是在堆内存上进行的。但是随着JIT编译器的日渐成熟，很多优化使这种分配策略并不绝对。JIT编译器就可以在编译期间根据逃逸分析的结果，来决定是否可以将对象的内存分配从堆转化为栈。

​	以下面代码为例：

```java
public class EscapeAnalysisTest {
    public static void main(String[] args) {
        long a1 = System.currentTimeMillis();
        for (int i = 0; i < 1000000; i++) {
        	alloc();
        }
        // 查看执行时间
        long a2 = System.currentTimeMillis();
        System.out.println("cost " + (a2 - a1) + " ms");
        // 为了方便查看堆内存中对象个数，线程sleep
        try {
        	Thread.sleep(100000);
        } catch (InterruptedException e1) {
        	e1.printStackTrace();
        }
    }
    private static void alloc() {
    	User user = new User();
    }
    static class User {
    }
}
```

​	代码内容很简单，就是使用for循环，在代码中创建100万个User对象。

​	我们在alloc方法中定义了User对象，但是并没有在方法外部引用他。也就是说，这个对象并不会逃逸到alloc外部。经过JIT的逃逸分析之后，就可以对其内存分配进行优化。

​	首先，我们指定以下JVM参数并运行：

```shell
-Xmx4G -Xms4G 
-XX:-DoEscapeAnalysis #关闭逃逸分析
-XX:+PrintGCDetails  #打印GC日志
-XX:+HeapDumpOnOutOfMemoryError
```

​	在程序打印出  cost XX ms 后，代码运行结束之前，我们使用jmap命令，来查看下当前堆内存中有多少个User对象：

```shell
~ jps
2809 StackAllocTest
2810 Jps
~ jmap -histo 2809
num   #instances     #bytes class name
----------------------------------------------
 1:      524    87282184 [I
 2:    1000000    16000000 StackAllocTest$User
 3:      6806     2093136 [B
 4:      8006     1320872 [C
 5:      4188     100512 java.lang.String
 6:      581      66304 java.lang.Class
```

​	从上面的jmap执行结果中我们可以看到，堆中共创建了100万个 StackAllocTest$User 实例。在关闭逃避分析的情况下（-XX:-DoEscapeAnalysis），虽然在alloc方法中创建的User对象并没有逃逸到方法外部，但是还是被分配在堆内存中。也就说，如果没有JIT编译器优化，没有逃逸分析技术，正常情况下就应该是这样的。即所有对象都分配到堆内存中。

​	接下来，我们开启逃逸分析，再来执行下以上代码。

```shell
-Xmx4G -Xms4G -XX:+DoEscapeAnalysis -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError
```

​	在程序打印出  cost XX ms 后，代码运行结束之前，我们使用 jmap 命令，来查看下当前堆内存中有多少个User对象：

```shell
~ jps
709
2858 Launcher
2859 StackAllocTest
2860 Jps
~ jmap -histo 2859
num   #instances     #bytes class name
---------------------------------------------
 1:      524    101944280 [I
 2:      6806     2093136 [B
 3:     83619     1337904 StackAllocTest$User
 4:      8006     1320872 [C
 5:      4188     100512 java.lang.String
 6:      581      66304 java.lang.Class
```

​	从以上打印结果中可以发现，开启了逃逸分析之后（-XX:+DoEscapeAnalysis），在堆内存中只有8万多个 StackAllocTest$User 对象。也就是说在经过JIT优化之后，堆内存中分配的对象数量，从100万降到了8万。剩余的8万多个是因为JIT优化不是一开始就进行的，且该优化并不是绝对的将对象放到栈中，还会考虑一些其它因素。

​	除了以上通过jmap验证对象个数的方法以外，还可以尝试将堆内存调小，然后执行以上代码，根据GC的次数来分析，也能发现，开启了逃逸分析之后，在运行期间，GC次数会明显减少。正是因为很多堆上分配被优化成了栈上分配，所以GC次数有了明显的减少。

​	该优化方式经常会和标量替换一起使用。



### 标量替换

​	标量（Scalar）是指一个无法再分解成更小的数据的数据 。

​	在JIT阶段，如果经过逃逸分析，发现一个对象不会被外界访问的话，那么经过JIT优化，就会把这个对象拆解成若干个其中包含的若干个成员变量来代替。这样可以使得占用的内存大大减少。

```java
//有一个类A
public class A{
  public int a=1;
  public int b=2
}
//方法getAB使用类A里面的a，b
private void getAB(){
  A x = new A();
  x.a;
  x.b;
}
//JVM在编译的时候会直接编译成
private void getAB(){
  a = 1;
  b = 2;
}
//这就是标量替换
```



### 同步锁消除

​	同样基于逃逸分析，当加锁的变量不会发生逃逸，是线程私有的完全没有必要加锁。 在JIT编译时期就可以将同步锁去掉，以减少加锁与解锁造成的资源开销。

```java
public class TestLockEliminate {
	public static String getString(String s1， String s2) {
		StringBuffer sb = new StringBuffer();
		sb.append(s1);
    	sb.append(s2);
    	return sb.toString();
 	}
	public static void main(String[] args) {
		long tsStart = System.currentTimeMillis();
      	for (int i = 0; i < 10000000; i++) {
      		getString("TestLockEliminate "， "Suffix");
  	 	}
    	System.out.println("一共耗费：" + (System.currentTimeMillis() - tsStart) + " ms");
 	}
}
```

​	以上代码中，getString()方法中的StringBuffer数以函数内部的局部变量，进作用于方法内部，不可能逃逸出该方法，因此他就不可能被多个线程同时访问，也就没有资源的竞争，但是StringBuffer的append操作却需要执行同步操作。

​	分别使用以下参数执行上述代码可以明显看出同步锁消除后对于性能的提升。

```shell
-XX:+DoEscapeAnalysis -XX:-EliminateLocks
-XX:+DoEscapeAnalysis -XX:+EliminateLocks
```



# Class文件



## 文件结构说明

![20200810164425831](D:\repositories\others\research\java\jvm\images\20200810164425831.png)

根据上图，我们可以将class文件的组织结构概括为以下的表格（其中u4表示4个无符号字节，u2表示2个无符号字节）：

|    **类型**    |      **名称**       |        **说明**         |         数量          |
| :------------: | :-----------------: | :---------------------: | :-------------------: |
|       u4       |        magic        | 魔数，识别Class文件格式 |           1           |
|       u2       |    minor_version    |        副版本号         |           1           |
|       u2       |    major_version    |        主版本号         |           1           |
|       u2       | constant_pool_count |       常量池数量        |           1           |
|    cp_info     |    constant_pool    |         常量池          | constant_pool_count-1 |
|       u2       |    access_flags     |        访问标志         |           1           |
|       u2       |     this_class      |         类引用          |           1           |
|       u2       |     super_class     |        父类引用         |           1           |
|       u2       |  interfaces_count   |        接口数量         |           1           |
|       u2       |     interfaces      |        接口集合         |   interfaces_count    |
|       u2       |    fields_count     |        字段数量         |           1           |
|   field_info   |       fields        |        字段集合         |     fields_count      |
|       u2       |    methods_count    |        方法数量         |           1           |
|  method_info   |       methods       |        方法集合         |     methods_count     |
|       u2       |  attributes_count   |        属性数量         |           1           |
| attribute_info |     attributes      |        属性集合         |   attributes_count    |



### 魔数

​	所有的由Java编译器编译而成的class文件的前4个字节都是“0xCAFEBABE”。

​	它的作用在于：当JVM在尝试加载某个文件到内存中来的时候，会首先判断此class文件有没有JVM认为可以接受的“签名”，即JVM会首先读取文件的前4个字节，判断该4个字节是否是“0xCAFEBABE”，果是，则JVM会认为可以将此文件当作class文件来加载并使用。

​	对于魔数的由来，Java编程语言之父,詹姆斯•高斯林(James Gosling),曾这样说过：

​	We used to go to lunch at a place called St Michael's Alley. According to local legend, in the deep dark past, the Grateful Dead used to perform there before they made it big. It was a pretty funky place that was definitely a Grateful Dead Kinda Place. When Jerry died, they even put up a little Buddhist-esque shrine. When we used to go there, we referred to the place as Cafe Dead. Somewhere along the line it was noticed that this was a HEX number. I was re-vamping some file format code and needed a couple of magic numbers: one for the persistent object file, and one for classes. I used CAFEDEAD for the object file format, and in grepping for 4 character hex words that fit after "CAFE" (it seemed to be a good theme) I hit on BABE and decided to use it. At that time, it didn't seem terribly important or destined to go anywhere but the trash-can of history. So CAFEBABE became the class file format, and CAFEDEAD was the persistent object format. But the persistent object facility went away, and along with it went the use of CAFEDEAD - it was eventually replaced by RMI



### 版本号

​	主版本号和次版本号在class文件中各占两个字节，副版本号占用第5、6两个字节，而主版本号则占用第7，8两个字节。JDK1.0的主版本号为45，以后的每个新主版本都会在原先版本的基础上加1。若现在使用的是JDK1.8编译出来的class文件，则相应的主版本号应该是52，对应的7，8个字节的十六进制的值应该是 0x0034

​	一个 JVM实例只能支持特定范围内的主版本号 （Mi 至Mj） 和 0 至特定范围内 （0 至 m） 的副版本号。假设一个 Class 文件的格式版本号为 V， 仅当Mi.0 ≤ v ≤ Mj.m成立时，这个 Class 文件才可以被此 Java 虚拟机支持。不同版本的 Java 虚拟机实现支持的版本号也不同，高版本号的 Java虚拟机实现可以支持低版本号的 Class 文件，反之则不成立。JVM在加载class文件的时候，会读取出主版本号，然后比较这个class文件的主版本号和JVM本身的版本号，如果JVM本身的版本号 < class文件的版本号，JVM会认为加载不了这个class文件，会抛出我们经常见到的" java.lang.UnsupportedClassVersionError: Bad version number in .class file " Error 错误；反之，JVM会认为可以加载此class文件，继续加载此class文件。

### 常量池计数器

​	常量池是class文件中非常重要的结构，它描述着整个class文件的字面量信息。常量池是由一组constant_pool结构体数组组成的，而数组的大小则由常量池计数器指定。常量池计数器constant_pool_count 的值 = constant_pool表中的成员数+ 1。constant_pool表的索引值只有在大于 0 且小于constant_pool_count时才会被认为是有效的

​	注：常量池计数器默认从1开始而不是从0开始，当constant_pool_count = 1时，常量池中的cp_info个数为0。在指定class文件规范的时候，将索引#0项常量空出来是有特殊考虑的，这样当：某些数据在特定的情况下想表达“不引用任何一个常量池项”的意思时，就可以将其引用的常量的索引值设置为#0来表示。

### 常量池数据区



![1625207115917](D:\repositories\others\research\java\jvm\images\1625207115917.png)



![1624701698568](D:\repositories\others\research\java\jvm\images\1624701698568.png)

​	每个cp_info都会记录着class文件中某种类型的字面量。JVM根据tag值确定cp_info表示的具体类型，截至jdk13，JVM定义了以下17种类型的常量

| **tag值** |             **常量**             |                         描述                         |
| :-------: | :------------------------------: | :----------------------------------------------------------: |
|     1      |       CONSTANT_Utf8_info        |                             utf8编码的字符串                             |
|     3     |      CONSTANT_Integer_info       |                      表示int的数值常量                       |
|     4     |       CONSTANT_Float_info        |                     表示float的数值常量                      |
|     5     |        CONSTANT_Long_info        |                      表示long的数值常量                      |
|     6     |       CONSTANT_Double_info       |                     表示double的数值常量                     |
|     7     |       CONSTANT_Class_info        |                   表示类或接口的完全限定名                   |
|     8     |       CONSTANT_String_info       |                   表示String类型的常量对象                   |
|     9     |      CONSTANT_Fieldref_info      |                        表示类中的字段                        |
|    10     |     CONSTANT_MethodRef_info      |                        表示类中的方法                        |
|    11     | CONSTANT_InterfaceMethodref_info |                   表示类所实现的接口的方法                   |
|    12     |    CONSTANT_NameAndType_info     |                  表示字段或方法的名称和类型                  |
|    15     |    CONSTANT_MethodHandle_info    |                         表示方法句柄                         |
|    16     |     CONSTANT_MethodType_info     |                         表示方法类型                         |
| 17 | CONSTANT_Dynamic_info | 表示一个动态计算常量 |
|    18      |  CONSTANT_InvokeDynamic_info    | 表示一个动态方法调用点 |
| 19 | CONSTANT_Module_info | 表示一个模块 |
| 20 | CONSTANT_Package_info | 表示一个模块中开放或者导出的包 |

​	这些类型各自有着完全独立的数据结构：

![img](file:///C:\Users\Administrator\AppData\Roaming\Tencent\Users\823325891\QQ\WinTemp\RichOle\[2`]A8%JNY7H`{3~L}$4C_P.png)



### 访问标志

​	access_flags，在常量池结束后，紧接着的2个字节表示访问标志，这个标志用于识别一些类或者接口层次的访问信息。具体的标志位以及含义见下表：

|    标志名称    | 标志值 |                             含义                             |
| :------------: | :----: | :----------------------------------------------------------: |
|   ACC_PUBLIC   | 0x0001 |                       是否为public类型                       |
|   ACC_FINAL    | 0x0010 |                      是否被声明为final                       |
|   ACC_SUPER    | 0x0020 | 是否允许使用invokespecial字节码指令的新语义，invokespecial指令的语义在jdk1.0.2发生过改变，为了区别这条指令使用哪种语义，jdk1.0.2之后编译出来的类这个标志都必须为1 |
| ACC_INTERFACE  | 0x0200 |                       标识这是一个接口                       |
|  ACC_ABSTRACT  | 0x0400 | 是否为abstract类型，对于接口或抽象类来说，此标志为1，其它为0 |
| ACC_SYNTHETIC  | 0x1000 |                标识这个类并非由用户代码产生的                |
| ACC_ANNOTATION | 0x2000 |                       标识这是一个注解                       |
|    ACC_ENUM    | 0x4000 |                       标识这是一个枚举                       |
|   ACC_MODULE   | 0x8000 |                       标识这是一个模块                       |

​	access_flags中一共有16个标志位可用，当前只定义了9个，没有使用的要求一律为0。以下面的Test为例，它的ACC_PUBLIC，ACC_SUPER，ACC_ABSTRACT为1，其它均为0，所以它的访问标志的值为0x0001|0x0020|0x0400 = 0x0421

```java
public abstract class Test{
}
```



### 类索引

​	this_class，一个u2类型的数据，指向常量池中CONSTANT_Class_info类型的索引值。表示这个class文件定义的类或接口



### 父类索引

​	super_class，一个u2类型的数据，指向常量池中CONSTANT_Class_info类型的索引值。

​	java中除了java.lang.Object之外，所有类有有且仅有一个父类。所以只有java.lang.Object的父类索引是0。



### 接口计数器

​	interfaces_count，一个u2类型的数据，表示当前类或接口的直接父接口数量。



### 接口集合

​	interfaces，数组中的每个成员的值必须是一个对constant_pool表中项目的一个有效索引值， 它的长度为 interfaces_count。每个成员interfaces[i] 必须为
CONSTANT_Class_info类型常量，其中 【0 ≤ i <interfaces_count】。在interfaces[]数组中，成员所表示的接口顺序和对应的源代码中给定的接口顺序（从左至右）一样，即interfaces[0]对应的是源代码中最左边的接口。



### 字段计数器

​	fields_count，u2类型。表示当前 Class 文件 fields[]数组的成员个数。 fields[]数组中每一项都是一个field_info结构的数据项，它用于表示该类或接口声明的【类字段】或者【实例字段】。















## 文件查看方式

### Hex Editor

​	可以安装Hex Editor等工具打开

![1624691607303](D:\repositories\others\research\java\jvm\images\1624691607303.png)



### javap





































