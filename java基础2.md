#java基础
####7.抽象类和接口区别
1.从设计目的来看，接口是对动作的抽象，是对行为的约束，抽象类是对类本质的抽象，捕捉子类的通用特性，是为了代码复用。
2.从使用上看，接口是like a，抽象类是is a的关系。
3.语法上的：
- 接口中所有的方法隐含的都是抽象的。而抽象类则可以同时包含抽象和非抽象的方法。换句话说，接口只能定义方法描述，而抽象类可以定义方法描述和方法实现。
- 类可以实现很多个接口，但是只能继承一个抽象类。
- 类如果要实现一个接口，它必须要实现接口声明的所有方法。但是，类可以不实现抽象类声明的所有方法，当然，在这种情况下，类也必须得声明成是抽象的。
- Java接口中声明的变量默认都是static final的。抽象类可以包含非final的变量。（至于为什么，后面会说）
- Java接口中的成员函数默认是public的。抽象类的成员函数可以是private，protected或者是public。
- 接口没有构造方法，抽象类可以有构造方法
- main方法，抽象类可以有main方法，并可以运行。接口没有main方法。
- 抽象类中的方法method不能同时是静态的，接口也不行，我觉得主要是因为不能实例化，所以不能分配内存。
  
**注意**：
为什么接口只能定义常量即static final的？下面的这个回答个人认为好理解：
　  我们都知道，接口中不能定义方法的实现，但是抽象类中是可以定义变量、常量以及方法的实现的，所以我们可以将接口看为比抽象类更高层次的抽象，是特殊的抽象类。加入接口可以定义变量，因为接口中的方法都是抽象的，我们无法通过行为，例如set()方法来修改这一属性值。

　　或许有些人会说我们可以让实现接口的类的对象去修改接口的属性值，如果真的这样的话，比如接口中我定义了变量a，而且我们通过实现本接口的类B的对象改变了a的值，那么其他实现本接口的类B、类C中的变量a得知都要随着改变（因为它们实现的都是一个接口，处理的也是同一个变量，而变量的处理方式为传址），那么就会造成想要实现本接口的类却不知道接口里现在变量a到底是什么值，就会造成混乱。

　　而且，抽象的概念是将不可变的东西提取出来封装到一起，将可变的东西放到实现中去，接口的设计理念既然是高层的抽象，那么就应该定义为不可变的东西，如果接口中定义了变量，就说明带了可变的成分，就不是高层抽象了。

####8.抽象类是否可以没有方法和属性？
可以，Serializable，Cloneable接口就是。

####9.接口的意义
自己总结的，接口就是为了定义规则或者说规范，目的是为了更好的扩展和解耦。可能还有就是多继承的补充吧。

####10.抽象类的意义
抽象接口是为了规定子类的行为，但是在子类中行为的具体展现可以不一样，其实还是为了代码复用。

####11.抽象类与接口的应用场景
- 如果你拥有一些方法并且想让它们中的一些有默认实现，那么使用抽象类吧。
如果你想实现多重继承，那么你必须使用接口。
由于Java不支持多继承，子类不能够继承多个类，但可以实现多个接口。因此你就可以使用接口来解决它。
如果基本功能在不断改变，那么就需要使用抽象类。如果不断改变基本功能并且使用接口，那么就需要改变所有实现了该接口的类。
- 抽象类表示共有许要实现的方法 接口用来特定类有需要才实现 人都会吃饭 但有的人抽烟
- 抽象类是用来捕捉子类的通用特性的

####12.泛型中extends和super的区别
- <? extends T>：是指 “上界通配符（Upper Bounds Wildcards）”
- <? super T>：是指 “下界通配符（Lower Bounds Wildcards）”
1. 为什么要用通配符和边界？  
使用泛型的过程中，经常出现一种很别扭的情况。比如按照题主的例子，我们有Fruit类，和它的派生类Apple类。
  
```java
class Fruit {}
class Apple extends Fruit {}

```
然后有一个最简单的容器：Plate类。盘子里可以放一个泛型的“东西”。我们可以对这个东西做最简单的“放”和“取”的动作：set( )和get( )方法。
```java
class Plate<T>{
    private T item;
    public Plate(T t){item=t;}
    public void set(T t){item=t;}
    public T get(){return item;}
}

```
现在我定义一个“水果盘子”，逻辑上水果盘子当然可以装苹果。
```java
Plate<Fruit> p=new Plate<Apple>(new Apple());
```
但实际上Java编译器不允许这个操作。会报错，“装苹果的盘子”无法转换成“装水果的盘子”。
```java

error: incompatible types: Plate<Apple> cannot be converted to Plate<Fruit>
```
实际上，编译器脑袋里认定的逻辑是这样的：
苹果 IS-A 水果
装苹果的盘子 NOT-IS-A 装水果的盘子
所以，就算容器里装的东西之间有继承关系，但容器之间是没有继承关系的。所以我们不可以把Plate<Apple>的引用传递给Plate<Fruit>。

为了让泛型用起来更舒服，Sun的大脑袋们就想出了<? extends T>和<? super T>的办法，来让”水果盘子“和”苹果盘子“之间发生关系。
2.什么是上界？
下面代码就是“上界通配符（Upper Bounds Wildcards）”：
```java
Plate<？ extends Fruit>

```
翻译成人话就是：一个能放水果以及一切是水果派生类的盘子。再直白点就是：啥水果都能放的盘子。这和我们人类的逻辑就比较接近了。Plate<？ extends Fruit>和Plate<Apple>最大的区别就是：Plate<？ extends Fruit>是Plate<Fruit>以及Plate<Apple>的基类。直接的好处就是，我们可以用“苹果盘子”给“水果盘子”赋值了。
```java
Plate<? extends Fruit> p=new Plate<Apple>(new Apple());

```
如果把Fruit和Apple的例子再扩展一下，食物分成水果和肉类，水果有苹果和香蕉，肉类有猪肉和牛肉，苹果还有两种青苹果和红苹果。
```java
//Lev 1
class Food{}

//Lev 2
class Fruit extends Food{}
class Meat extends Food{}

//Lev 3
class Apple extends Fruit{}
class Banana extends Fruit{}
class Pork extends Meat{}
class Beef extends Meat{}

//Lev 4
class RedApple extends Apple{}
class GreenApple extends Apple{}

```
在这个体系中，上界通配符 “Plate<？ extends Fruit>” 覆盖下图中蓝色的区域。
![1](https://pic2.zhimg.com/80/cdec0a066693684036d4bcaab4fdc1e3_hd.jpg)
3.什么是下界
相对应的，“下界通配符（Lower Bounds Wildcards）”：
```java
Plate<？ super Fruit>
```
表达的就是相反的概念：一个能放水果以及一切是水果基类的盘子。Plate<？ super Fruit>是Plate<Fruit>的基类，但不是Plate<Apple>的基类。对应刚才那个例子，Plate<？ super Fruit>覆盖下图中红色的区域。
![2](https://pic2.zhimg.com/80/0800ab14b2177e31ee3b9f6d477918fa_hd.jpg)
4.上下界通配符的副作用
边界让Java不同泛型之间的转换更容易了。但不要忘记，这样的转换也有一定的副作用。那就是容器的部分功能可能失效。

还是以刚才的Plate为例。我们可以对盘子做两件事，往盘子里set( )新东西，以及从盘子里get( )东西。
```java
class Plate<T>{
    private T item;
    public Plate(T t){item=t;}
    public void set(T t){item=t;}
    public T get(){return item;}
}

```
4.1.上界<? extends T>不能往里存，只能往外取
<? extends Fruit>会使往盘子里放东西的set( )方法失效。但取东西get( )方法还有效。比如下面例子里两个set()方法，插入Apple和Fruit都报错。
```java

Plate<? extends Fruit> p=new Plate<Apple>(new Apple());

//不能存入任何元素
p.set(new Fruit());    //Error
p.set(new Apple());    //Error

//读取出来的东西只能存放在Fruit或它的基类里。
Fruit newFruit1=p.get();
Object newFruit2=p.get();
Apple newFruit3=p.get();    //Error
```
原因是编译器只知道容器内是Fruit或者它的派生类，但具体是什么类型不知道。可能是Fruit？可能是Apple？也可能是Banana，RedApple，GreenApple？编译器在看到后面用Plate<Apple>赋值以后，盘子里没有被标上有“苹果”。而是标上一个占位符：CAP#1，来表示捕获一个Fruit或Fruit的子类，具体是什么类不知道，代号CAP#1。然后无论是想往里插入Apple或者Meat或者Fruit编译器都不知道能不能和这个CAP#1匹配，所以就都不允许。

所以通配符<?>和类型参数<T>的区别就在于，对编译器来说所有的T都代表同一种类型。比如下面这个泛型方法里，三个T都指代同一个类型，要么都是String，要么都是Integer。
```java
public <T> List<T> fill(T... t);
```
但通配符<?>没有这种约束，Plate<?>单纯的就表示：盘子里放了一个东西，是什么我不知道。

所以题主问题里的错误就在这里，Plate<？ extends Fruit>里什么都放不进去。
4.2. 下界<? super T>不影响往里存，但往外取只能放在Object对象里
使用下界<? super Fruit>会使从盘子里取东西的get( )方法部分失效，只能存放到Object对象里。set( )方法正常。
```java

Plate<? super Fruit> p=new Plate<Fruit>(new Fruit());

//存入元素正常
p.set(new Fruit());
p.set(new Apple());

//读取出来的东西只能存放在Object类里。
Apple newFruit3=p.get();    //Error
Fruit newFruit1=p.get();    //Error
Object newFruit2=p.get();
```
因为下界规定了元素的最小粒度的下限，实际上是放松了容器元素的类型控制。既然元素是Fruit的基类，那往里存粒度比Fruit小的都可以。但往外读取元素就费劲了，只有所有类的基类Object对象才能装下。但这样的话，元素的类型信息就全部丢失。
5. PECS原则
最后看一下什么是PECS（Producer Extends Consumer Super）原则，已经很好理解了：
> 频繁往外读取内容的，适合用上界Extends。
> 经常往里插入的，适合用下界Super。
以上为知乎上的回答，感觉浅显易懂，如涉及侵权，请联系我，我会删除。
  
关于泛型就说这些。
在加个链接[importNew 泛型中? super T和? extends T的区别](http://www.importnew.com/17006.html)
####13.父类的静态方法能否被子类重写
"重写"只能适用于实例方法.不能用于静态方法.对于静态方法,只能隐藏（形式上被重写了，但是不符合的多态的特性），“重写”是用来实现多态性的，只有实例方法是可以实现多态，而静态方法无法实现多态。
所以无法重写，这只是概念上的分析，或者说是从结果上分析，但是真正的原因应该跟jvm有关系。
[从jvm角度查看](http://blog.csdn.net/TyroneRenekton/article/details/68923475)

####14.进程和线程的区别
首先来一句概括的总论：进程和线程都是一个时间段的描述，是CPU工作时间段的描述。
**下面细说背景：**
CPU+RAM+各种资源（比如显卡，光驱，键盘，GPS, 等等外设）构成我们的电脑，但是电脑的运行，实际就是CPU和相关寄存器以及RAM之间的事情。
**一个最最基础的事实**：CPU太快，太快，太快了，寄存器仅仅能够追的上他的脚步，RAM和别的挂在各总线上的设备完全是望其项背。那当多个任务要执行的时候怎么办呢？轮流着来?或者谁优先级高谁来？不管怎么样的策略，一句话就是在CPU看来就是轮流着来。
**一个必须知道的事实**：执行一段程序代码，实现一个功能的过程介绍 ，当得到CPU的时候，相关的资源必须也已经就位，就是显卡啊，GPS啊什么的必须就位，然后CPU开始执行。这里除了CPU以外所有的就构成了这个程序的执行环境，也就是我们所定义的程序上下文。当这个程序执行完了，或者分配给他的CPU执行时间用完了，那它就要被切换出去，等待下一次CPU的临幸。在被切换出去的最后一步工作就是保存程序上下文，因为这个是下次他被CPU临幸的运行环境，必须保存。

**串联起来的事实**：前面讲过在CPU看来所有的任务都是一个一个的轮流执行的，具体的轮流方法就是：先加载程序A的上下文，然后开始执行A，保存程序A的上下文，调入下一个要执行的程序B的程序上下文，然后开始执行B,保存程序B的上下文。。。。

**========= 重要的东西出现了========**
进程和线程就是这样的背景出来的，两个名词不过是对应的CPU时间段的描述，名词就是这样的功能。

**进程就是包换上下文切换的程序执行时间总和 = CPU加载上下文+CPU执行+CPU保存上下文**
**线程是什么呢？**
进程的颗粒度太大，每次都要有上下的调入，保存，调出。如果我们把进程比喻为一个运行在电脑上的软件，那么一个软件的执行不可能是一条逻辑执行的，必定有多个分支和多个程序段，就好比要实现程序A，实际分成 a，b，c等多个块组合而成。那么这里具体的执行就可能变成：程序A得到CPU =》CPU加载上下文，开始执行程序A的a小段，然后执行A的b小段，然后再执行A的c小段，最后CPU保存A的上下文。

这里a，b，c的执行是共享了A的上下文，CPU在执行的时候没有进行上下文切换的。这里**的a，b，c就是线程，也就是说线程是共享了进程的上下文环境，的更为细小的CPU时间段。**

**到此全文结束，再一个总结：

进程和线程都是一个时间段的描述，是CPU工作时间段的描述，不过是颗粒大小不同。**

作者：zhonyong
链接：https://www.zhihu.com/question/25532384/answer/81152571
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

下面是一个简单的解释，也不错
#####1. 定义
	进程:具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.
    线程:进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.
#####2.关系
	一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行.

	相对进程而言，线程是一个更加接近于执行体的概念，它可以与同进程中的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。
#####3.区别
	进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。
- 简而言之,一个程序至少有一个进程,一个进程至少有一个线程.
- 线程的划分尺度小于进程，使得多线程程序的并发性高。
- 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率
- 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
- 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。
  
  
#####4.优缺点
线程和进程在使用上各有优缺点：
线程执行开销小，但不利于资源的管理和保护；
而进程正相反。
同时，线程适合于在SMP机器上运行，而进程则可以跨机器迁移。

####15.final，finally，finalize的区别
1.final,用于修饰类，成员变量，成员方法，final修饰的类，不能被继承，其中所有的方法不能被重写，所以不能同时写abstract final修饰类，final修饰的方法不能被重写，但是子类可以用父类final修饰的方法，final修饰的成员编程是不可改变的，如果成员变量是基本数据类型，初始化之后成员变量的值不能被改变，如果成员变量是引用类型，那么它只能指向初始化时指向的那个对象，不能再指向别的对象，但是对象当中的内容是允许改变的。
- Final修饰的形参，不能在方法中对形参赋值

```java
public void test(fnal int a){
	a = 1;
}

public void test2(final String str){
	str = "aaa";
}
```
  
  以上都是报错的。
- Final修饰的局部变量和成员变量情况一样，一旦赋值，不可改变。

```java
public void test(int a){
	final String str = "123";
    str = "456";
}

```
2.finally是在异常处理时提供finally块来执行任何清除操作。不管有没有异常被抛出、捕获，finally块都会被执行。try块中的内容是在无异常时执行到结束。catch块中的内容，是在try块内容发生catch所声明的异常时，跳转到catch块中执行。finally块则是无论异常是否发生，都会执行finally块的内容，所以在代码逻辑中有需要无论发生什么都必须执行的代码，就可以放在finally块中
3.finalize是方法名。java技术允许使用finalize（）方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。这个方法是由垃圾收集器在确定这个对象没有被引用时对这个对象调用的。它是在object类中定义的，因此所有的类都继承了它。子类覆盖finalize（）方法以整理系统资源或者被执行其他清理工作。finalize（）方法是在垃圾收集器删除对象之前对这个对象调用的。


####18.静态属性和静态方法是否可以被继承？是否可以被重写？以及原因？
结论：java中静态属性和静态方法可以被继承，但是没有被重写(overwrite)而是被隐藏. 
原因： 
- 1). 静态方法和属性是属于类的，调用的时候直接通过类名.方法名完成对，不需要继承机制及可以调用。如果子类里面定义了静态方法和属性，那么这时候父类的静态方法或属性称之为"隐藏"。如果你想要调用父类的静态方法和属性，直接通过父类名.方法或变量名完成，至于是否继承一说，子类是有继承静态方法和属性，但是跟实例方法和属性不太一样，存在"隐藏"的这种情况。 
- 2). 多态之所以能够实现依赖于继承、接口和重写、重载（继承和重写最为关键）。有了继承和重写就可以实现父类的引用指向不同子类的对象。重写的功能是："重写"后子类的优先级要高于父类的优先级，但是“隐藏”是没有这个优先级之分的。 
- 3). 静态属性、静态方法和非静态的属性都可以被继承和隐藏而不能被重写，因此不能实现多态，不能实现父类的引用可以指向不同子类的对象。非静态方法可以被继承和重写，因此可以实现多态。 

####19.静态内部类的设计意图
它唯一的作用就是随着类的加载（而不是随着对象的产生）而产生，以致可以用类名+静态成员名直接获得。
这样静态内部类就可以理解了，因为这个类没有必要单独存放一个文件，它一般来说只被所在外部类使用。并且它可以直接被用 外部类名+内部类名 获得。

####21.谈谈对kotlin的理解
1. 代码简单
2. 函数式编程
3. 语法糖
4. java兼容  

####22.闭包和局部内部类的区别
1. 引java中的,每个内部类都能独立地继承一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响.
2. 闭包就是把函数以及变量包起来，使得变量的生存周期延长。闭包跟面向对象是一棵树上的两条枝，实现的功能是等价的
3.  如果在内部类中使用到了外部方法的变量,需要使用 final 修饰,否则无法编译通过,但如果使用的是 JDK8,那么即便你不加final 修饰,也是可以编译过的,因为编译器替你加上了。
4.  内部类使用外部的局部变量,实际上形成了闭包,也就是捕获了所在方法内的变量


####23.string 转换成 integer的方式及原理
```java
//判空不然容易空指针
String str = "...";
Integer i = null;
if(str!=null){
     i = Integer.valueOf(str);
}

```
2.实现方式valueof内部调用的parseInt方法
```java
 public static int parseInt(String s, int radix)
                throws NumberFormatException
    {
        /*
         * WARNING: This method may be invoked early during VM initialization
         * before IntegerCache is initialized. Care must be taken to not use
         * the valueOf method.
         */

        if (s == null) {
            throw new NumberFormatException("null");
        }

        if (radix < Character.MIN_RADIX) {
            throw new NumberFormatException("radix " + radix +
                                            " less than Character.MIN_RADIX");
        }

        if (radix > Character.MAX_RADIX) {
            throw new NumberFormatException("radix " + radix +
                                            " greater than Character.MAX_RADIX");
        }

        int result = 0;
        boolean negative = false;
        int i = 0, len = s.length();
        int limit = -Integer.MAX_VALUE;
        int multmin;
        int digit;

        if (len > 0) {
            char firstChar = s.charAt(0);
            if (firstChar < '0') { // Possible leading "+" or "-"
                if (firstChar == '-') {
                    negative = true;
                    limit = Integer.MIN_VALUE;
                } else if (firstChar != '+')
                    throw NumberFormatException.forInputString(s);

                if (len == 1) // Cannot have lone "+" or "-"
                    throw NumberFormatException.forInputString(s);
                i++;
            }
            multmin = limit / radix;
            while (i < len) {
                // Accumulating negatively avoids surprises near MAX_VALUE
                digit = Character.digit(s.charAt(i++),radix);
                if (digit < 0) {
                    throw NumberFormatException.forInputString(s);
                }
                if (result < multmin) {
                    throw NumberFormatException.forInputString(s);
                }
                result *= radix;
                if (result < limit + digit) {
                    throw NumberFormatException.forInputString(s);
                }
                result -= digit;
            }
        } else {
            throw NumberFormatException.forInputString(s);
        }
        return negative ? result : -result;
    }

```
