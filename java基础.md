#java基础
###1.关于equals(equals 与hashcode与==的关系)
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

###2int、char、long各占多少字节数
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

####对java多态的理解