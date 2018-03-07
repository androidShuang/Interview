#java基础
1.关于equals(equals 与hashcode与==的关系)
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

2