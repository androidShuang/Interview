#java基础
####1.关于equals(equals 与hashcode与==的关系)
首先看Object的equals方法
```java
public boolean equals(Object obj) {
        return (this == obj);
    }
```
很明显就是简单的比较两个对象的对应内存中存储的值是否相等。
下面看下hashcode的实现:
```java
     * @return  a hash code value for this object.
     * @see     java.lang.Object#equals(java.lang.Object)
     * @see     java.lang.System#identityHashCode
     */
    public native int hashCode();

```
这是个navtive层代码，在jvm里可以看到实现，这里就不深入了，涉及到hotspot
剩下的==很简单，就是判断对应的内存地址中的值是否相等，基本类型是否相等也可以使用==。
那么既然问，equals和hashcode与==有什么区别，为什么会有这样的问题呢？而且经常听别人说要重写equals方法同时也要重写hashcode方法，这是为什么呢？原因其实在object的hashcode的说明中有所提及（所以源码上的注释也要详细的看下），源码注释怎么说的
>  	* Returns a hash code value for the object. This method is
     * supported for the benefit of hash tables such as those provided by
     * {@link java.util.HashMap}.
  
上面的意思大概就是说，这个方法返回这个对象的hashcode值，这个方法其实是帮助hash tables的比如，hashmap类。翻译的不好，毕竟这只是给我自己看的。。。以后英语学好了在改吧，其实hashcode主要是为了像基于hash值的集合类服务的，比如hashmap，靠对象的hashcode值来将对象放到不同的桶中，然后在根据equals来决定放在桶里的什么位置上。在hashcode的说明里还有一段英文，
>      * If two objects are equal according to the {@code equals(Object)}
     * method, then calling the {@code hashCode} method on each of
     * the two objects must produce the same integer result.
  
这句话就是说，如果两个对象通过equals方法比对是相等的，那么这两个对象的hashcode值必须相同。
下面写个Demo
```java
package com.company;

import java.util.HashMap;

public class Main {

    public static void main(String[] args) {
	// write your code here
        HashMap hashMap = new HashMap<>();

        Cat cat1 = new Cat(1,1,1);
        Cat cat2 = new Cat(1,1,1);

        //在未重写equals的方法的时候会返回false
        System.out.println("equals比较的结果==="+cat1.equals(cat2));

        hashMap.put(cat1,"123");
        hashMap.put(cat2,"321");

        System.out.println(hashMap.get(new Cat(1,1,1)));

    }
}

package com.company;

public class Cat {
    private int color;
    private int weight;
    private int height;

    public Cat(int color, int weight, int height) {
        this.color = color;
        this.weight = weight;
        this.height = height;
    }

    @Override
    public boolean equals(Object obj) {
        if(this==obj){
            return true;
        }
        if(obj==null){
            return false;
        }else{
            if(obj instanceof Cat){
                Cat c = (Cat) obj;
                if(c.color==this.color&&c.weight==this.weight&&c.height==this.height){
                    return true;
                }
            }
        }
        return false;
    }

    @Override
    public int hashCode() {
        System.out.println("Integer的hashcode值==="+Integer.hashCode(this.color));
        return Integer.hashCode(this.color);
    }
}


```
以上代码中最后一行会打印什么？显然会打印321,因为我重写的hashcode与equals方法。hashmap在put的时候会先调用hashcode方法，判断放在哪个区域，然后在该区域中调用equals方法查找该区域中是否有这个对象。根据业务不同，重写hashcode与equals方法也不相同，但是有原则：
1 如果两个对象相同，那么它们的hashCode值一定要相同。也告诉我们重写equals方法，一定要重写hashCode方法，也就是说hashCode值要和类中的成员变量挂上钩，对象相同–>成员变量相同—->hashCode值一定相同。 
2 如果两个对象的hashCode相同，它们并不一定相同，这里的对象相同指的是用eqauls方法比较。
只要符合以上两个原则就好了。

####2.int、char、long各占多少字节数
我们不只说这个三种，整个java8中基本类型都说一下吧（一字节等于8个比特位）。
1.int ----4byte（说明一下1byte=8bit）
2.short----2byte
3.long----8byte
4.float----4byte
5.double----8byte
6.char----2byte
7.boolean----1byte
8.byte----1byte
注明：至于为什么 Java 中 char 无论中英文数字都占用2字节，是因为 Java 中使用 Unicode 字符，所有字符均以2个字节存储。
而如果需要识别字符是否为中文，可以使用正则匹配式。

####int与integer的区别
1.从初始值来看，首先int的初始值为0而integer的初始值为null
2.使用场景...比如未参加考试的和考试分数为0的场景，就要使用integer
备注:从jdk1.5开始有了自动装箱可以Integer i = 9;
还有一道经典面试题:
```java
 public class Test {  
        public static void main(String[] args) {  
            //在-128~127 之外的数
            Integer num1 = 128;   Integer num2 = 128;           
            System.out.println(num1==num2);   //false

            // 在-128~127 之内的数 
            Integer num3 = 9;   Integer num4 = 9;   
            System.out.println(num3==num4);   //true
        }  
    }  

```
解析原因：归结于java对于Integer与int的自动装箱与拆箱的设计，是一种模式：叫享元模式（flyweight）。 
加大对简单数字的重利用，Java定义在自动装箱时对于值从–128到127之间的值，它们被装箱为Integer对象后，会存在内存中被重用，始终只存在一个对象。 
而如果超过了从–128到127之间的值，被装箱后的Integer对象并不会被重用，即相当于每次装箱时都新建一个 Integer对象。

####3.对java多态的理解
1.名词解释：事物在运行过程中存在不同的形态，或者说同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果
2.多态的前提：要有继承关系，也可以实现关系（接口和实现类），在开发中多指第二种，子类要重写父类的方法，父类引用指向子类。
3.多态的好处：代码复用，子类可以直接调用父类的方法。向后兼容，可以提高可扩充性和可维护性。
4.关于绑定：先说一下绑定的概念，将一个方法调用同一个方法主体关联起来就叫绑定。绑定分为动态绑定（后期绑定），和前期绑定（由编译器和链接程序实现），那么什么才属于动态绑定呢？除了static 、final方法之外（private方法也属于final方法），其他所有的方法(注意这里说的是方法，成员变量看下面的原则)都是后期绑定。为什么要将某个方法生命为final呢？因为为了要防止重写，可以有效的关闭动态绑定。
5.关于绑定前人总结的
>     （1）成员变量：编译看左边，运行看左边	（）

>     （2）成员方法：编译看左边，运行看右边 （动态绑定）

>     （3）静态方法：编译看左边，运行看左边	（前期绑定）
  
6.下面看个实例
```java
package com.company;

public class Dog extends Pet{

    int age = 20;
    static int number = 20;
    private int eye = 20;

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public int getAge() {
        return age;
    }

    public static void run(){
        System.out.println("dog static run");
    }

    private static void eat(){
        System.out.println("static pet private eat");
    }

    @Override
    protected void say3() {
        System.out.println("dog say3");
    }

    public void cute(){
        System.out.println("dog cute");
    }

    public void say2(){
        System.out.println("dog say2");
    }

}

package com.company;

public class Pet {
    private int age = 12;
    int number = 11;
    static int eye = 2;

    public int getAge(){
        return this.age;
    }

    public static void run(){
        System.out.println("static pet run");
    }

    private void eat(){
        System.out.println("static pet private eat");
    }

    public void say(){
        System.out.println("pet say");
    }

    private void say2(){
        System.out.println("pet say 2");
    }

    protected void say3(){
        System.out.println("pet say 3");
    }
}
package com.company;

import java.util.HashMap;

public class Main {

    public static void main(String[] args) {
	// write your code here
        Pet p = new Dog();
        p.say();
        p.say3();

        System.out.println("dog number=="+p.number);
        System.out.println("dog eye=="+p.eye);
        //这个会报错，因为这age是private的所以也是final，属于前期绑定，所以这个即使衍生类也有age
        //成员变量，那么也调用不了
//        System.out.println("dog age=="+p.age);

        int age = p.getAge();
        System.out.println(age+"");
        p.run();
//        p.eat();

    }
}
输出结果
pet say
dog say3
dog number==11
dog eye==2
20
static pet run
```
注意：成员变量是不具备多态性的。

####5.String、StringBuffer、StringBuilder区别
1.简单的说区别：String是字符串常量，StringBuffer是变量线程安全，StringBuilder是变量线程不安全
2.重要区别，String是不可变的，而且StringBuffer是可以变化的，
3.做一个简单的总结
>1.如果要操作少量的数据用 = String　　

>2.单线程操作字符串缓冲区 下操作大量数据 = StringBuilder

>3.多线程操作字符串缓冲区 下操作大量数据 = StringBuffer
  
4.看个经典的面试题
```java
String str = “This is only a” + “ simple” + “ test”;
StringBuffer builder = new StringBuilder(“This is only a”).append(“simple”).append(“ test”);

//其实就是如下
//String str = “This is only a simple test”;

String str2 = “This is only a”;
String str3 = “ simple”;
String str4 = “ test”;
String str1 = str2 +str3 + str4;

```
5.这是有区别的，第一个速度快，第二个实际上内部调用的还是StringBuffer

####6.什么是内部类？内部类的作用
1.先看定义：定义在类内部,方法内部,甚至比方法体更小的代码块内部的类(if 语句里面等)
2.关于四种内部类：静态内部类，成员内部类，局部内部类，匿名内部类
3.为什么要使用内部类？在think in java中有这么一句话“使用内部类最吸引人的原因就是：每个内部类都能独立继承（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响”，这个需要多理解，其实内部类也是为了弥补多重继承的不足。需要注意的是，内部类是个编译时的概念，一旦编译成功了它就与外围类属于两个完全不同的类。
4.成员内部类：属于外部类的一个成员，所以可以访问外部类的所有属性和方法，包括Private的，外部类要访问内部类，必须通过内部类的实例访问，而且成员内部类是依附于外围类的，所以只有先创建了外部类实例，才能创建内部类实例。
```java
package com.company;

public class OuterClass {
    private String str;
    public void outetrDisplay(){
        System.out.println("outerclass....");
    }
    public class InnerClass{
        public void innerDisplay(){
            str = "chenssy";
            outetrDisplay();
        }
    }

    //推荐通过getXXX方式获取成员内部类
    public InnerClass getInnerClass(){
        return new InnerClass();
    }

    public static void main(String[] args){
        OuterClass outerClass = new OuterClass();
        OuterClass.InnerClass innerClass = outerClass.getInnerClass();
        innerClass.innerDisplay();
    }

}


```

5.局部内部类：有这样一种内部类，它是嵌套在方法和作用于内的，对于这个类的使用主要是应用与解决比较复杂的问题，想创建一个类来辅助我们的解决方案，到那时又不希望这个类是公共可用的，所以就产生了局部内部类，局部内部类和成员内部类一样被编译，只是它的作用域发生了改变，它只能在该方法和属性中被使用，出了该方法和属性就会失效。
```java
//定义在方法里：
package com.company;

public class LocalInnerTest {
    private int a = 1;
    private static int b = 2;

    public void test() {
        final int c = 3;
        class LocalInner {
            public void add1() {
                System.out.println("a= " + a);
                System.out.println("b= " + b);
                System.out.println("c= " + c);
            }
        }
        new LocalInner().add1();
    }

    static public void test2() {
        final int d = 5;
        class LocalInner2 {
            public void add1() {
                // System.out.println("a= " + a);
                System.out.println("b= " + b);
                System.out.println("c= " + d);
            }
        }
        new LocalInner2().add1();
    }

    public static void main(String args[]) {

        // LocalInnerTest() lc = new LocalInnerTest();
        new LocalInnerTest().test2();
        new LocalInnerTest().test();
    }
}


```
注意：
	如果在静态方法中：
       可以访问外部类中所有静态成员，包含私有
    如果在实例方法中：
       可以访问外部类中所有的成员，包含私有。
    局部内部类可以访问所在方法中定义的局部变量，但是要求局部变量必须使用final修饰
    局部内部类调用局部变量，要在局部变量上加final，为什么？跟生命周期有关，局部内部类与局部变量的生命周期不一致，java为了解决这个问题，采用复制的方式，什么原则呢？也就说如果局部变量的值在编译期间就可以确定，则直接在匿名内部里面创建一个拷贝。如果局部变量的值无法在编译期间确定，则通过构造器传参的方式来对拷贝进行初始化赋值。从上面可以看出，在run方法中访问的变量a根本就不是test方法中的局部变量a。这样一来就解决了前面所说的 生命周期不一致的问题。但是新的问题又来了，既然在run方法中访问的变量a和test方法中的变量a不是同一个变量，当在run方法中改变变量a的值的话，会出现什么情况？对，会造成数据不一致性，这样就达不到原本的意图和要求。为了解决这个问题，java编译器就限定必须将变量a限制为final变量，不允许对变量a进行更改（对于引用类型的变量，是不允许指向新的对象），这样数据不一致性的问题就得以解决了。
6.静态内部类，静态内部类与非静态内部类之间存在一个最大的区别，我们知道非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围内，但是静态内部类却没有。没有这个引用就意味着：它的创建是不需要依赖于外围类的，它不能使用任何外围类的非static成员变量和方法。官网有一段翻译：
从字面上看，一个被称为静态嵌套类，一个被称为内部类。从字面的角度解释是这样的：什么是嵌套？嵌套就是我跟你没关系，自己可以完全独立存在，但是我就想借你的壳用一下，来隐藏一下我自己（真TM猥琐）。什么是内部？内部就是我是你的一部分，我了解你，我知道你的全部，没有你就没有我。（所以内部类对象是以外部类对象存在为前提的）
```java
public class OuterClass {
    private String sex;
    public static String name = "chenssy";
 
    /**
     *静态内部类
     */
    static class InnerClass1{
        /* 在静态内部类中可以存在静态成员 */
        public static String _name1 = "chenssy_static";
 
        public void display(){
            /* 
             * 静态内部类只能访问外围类的静态成员变量和方法
             * 不能访问外围类的非静态成员变量和方法
             */
            System.out.println("OutClass name :" + name);
        }
    }
 
    /**
     * 非静态内部类
     */
    class InnerClass2{
        /* 非静态内部类中不能存在静态成员 */
        public String _name2 = "chenssy_inner";
        /* 非静态内部类中可以调用外围类的任何成员,不管是静态的还是非静态的 */
        public void display(){
            System.out.println("OuterClass name：" + name);
        }
    }
 
    /**
     * @desc 外围类方法
     * @author chenssy
     * @data 2013-10-25
     * @return void
     */
    public void display(){
        /* 外围类访问静态内部类：内部类. */
        System.out.println(InnerClass1._name1);
        /* 静态内部类 可以直接创建实例不需要依赖于外围类 */
        new InnerClass1().display();
 
        /* 非静态内部的创建需要依赖于外围类 */
        OuterClass.InnerClass2 inner2 = new OuterClass().new InnerClass2();
        /* 方位非静态内部类的成员需要使用非静态内部类的实例 */
        System.out.println(inner2._name2);
        inner2.display();
    }
 
    public static void main(String[] args) {
        OuterClass outer = new OuterClass();
        outer.display();
    }
}
//----------------
//Output:
//chenssy_static
//OutClass name :chenssy
//chenssy_inner
//OuterClass name：chenssy

```
7.匿名内部类：没有名字的局部内部类，只使用一次。看代码
```java
   Runnable runnable = new Runnable() {
                @Override
                public void run() {

                }
            };
    new View.OnClickListener(){
    @Override
    public void onClick(View v) {

    }
};
//上面都是常用的匿名内部类
abstract class Person {
    public abstract void eat();
}

public class Demo {
    public static void main(String[] args) {
        Person p = new Person() {
            public void eat() {
                System.out.println("eat something");
            }
        };
        p.eat();
    }
}
```
在看一个代码：
```java
//1
        new Object(){
            void show(){
                System.out.println("show run");                
            }
        }.show();//正确
        //2
        Object obj = new Object(){
            void show(){
                System.out.println("show run");
            }
        };
        obj.show();//编译错误

```
1和2的写法正确吗？有区别吗？说出原因。
写法是正确，1和2都是在通过匿名内部类建立一个Object类的子类对象。
区别：
第一个可是编译通过，并运行。
第二个编译失败，因为匿名内部类是一个子类对象，当用Object的obj引用指向时，就被提升为了
Object类型，而编译时检查Object类中是否有show方法，所以编译失败。
