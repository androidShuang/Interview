### 二、java深入源码级的（有难度）

####1. 哪些情况下的对象会被垃圾回收机制处理掉？
1. 先确定什么情况下对象才能被称之为"垃圾"
2. 在Java中采取了 可达性分析法。该方法的基本思想是通过一系列的“GC Roots”对象作为起点进行搜索，如果在“GC Roots”和一个对象之间没有可达路径，则称该对象是不可达的，不过要注意的是被判定为不可达的对象不一定就会成为可回收对象。被判定为不可达的对象要成为可回收对象必须至少经历两次标记过程，如果在这两次标记过程中仍然没有逃脱成为可回收对象的可能性，则基本上就真的成为可回收对象了。  
[知乎上回答的不错的](https://www.zhihu.com/question/19912197)
[iteye上的](http://icyfenix.iteye.com/blog/715301)  

####2. 讲一下常见编码方式？
1. 分清格式编码和编码
2. 格式编码就是你nodepad++上那些编码方式
3. 图片的jpg，视频的AVI都是编码方式
4. 为什么要编码
    - 计算机中存储信息的最小单元是一个字节即 8 个 bit，所以能表示的字符范围是 0~255 个
    - 人类要表示的符号太多，无法用一个字节来完全表示
    - 要解决这个矛盾必须需要一个新的数据结构 char，从 char 到 byte 必须编码  
5. 明白了各种语言需要交流，经过翻译是必要的，那又如何来翻译呢？计算中提拱了多种翻译方式，常见的有 ASCII、ISO-8859-1、GB2312、GBK、UTF-8、UTF-16 等。它们都可以被看作为字典，它们规定了转化的规则，按照这个规则就可以让计算机正确的表示我们的字符。目前的编码格式很多，例如 GB2312、GBK、UTF-8、UTF-16 这几种格式都可以表示一个汉字，那我们到底选择哪种编码格式来存储汉字呢？这就要考虑到其它因素了，是存储空间重要还是编码的效率重要。根据这些因素来正确选择编码格式，下面简要介绍一下这几种编码格式。
ASCII 码
学过计算机的人都知道 ASCII 码，总共有 128 个，用一个字节的低 7 位表示，0~31 是控制字符如换行回车删除等；32~126 是打印字符，可以通过键盘输入并且能够显示出来。
ISO-8859-1
128 个字符显然是不够用的，于是 ISO 组织在 ASCII 码基础上又制定了一些列标准用来扩展 ASCII 编码，它们是 ISO-8859-1~ISO-8859-15，其中 ISO-8859-1 涵盖了大多数西欧语言字符，所有应用的最广泛。ISO-8859-1 仍然是单字节编码，它总共能表示 256 个字符。
GB2312
它的全称是《信息交换用汉字编码字符集 基本集》，它是双字节编码，总的编码范围是 A1-F7，其中从 A1-A9 是符号区，总共包含 682 个符号，从 B0-F7 是汉字区，包含 6763 个汉字。
GBK
全称叫《汉字内码扩展规范》，是国家技术监督局为 windows95 所制定的新的汉字内码规范，它的出现是为了扩展 GB2312，加入更多的汉字，它的编码范围是 8140~FEFE（去掉 XX7F）总共有 23940 个码位，它能表示 21003 个汉字，它的编码是和 GB2312 兼容的，也就是说用 GB2312 编码的汉字可以用 GBK 来解码，并且不会有乱码。
GB18030
全称是《信息交换用汉字编码字符集》，是我国的强制标准，它可能是单字节、双字节或者四字节编码，它的编码与 GB2312 编码兼容，这个虽然是国家标准，但是实际应用系统中使用的并不广泛。
UTF-16
说到 UTF 必须要提到 Unicode（Universal Code 统一码），ISO 试图想创建一个全新的超语言字典，世界上所有的语言都可以通过这本字典来相互翻译。可想而知这个字典是多么的复杂，关于 Unicode 的详细规范可以参考相应文档。Unicode 是 Java 和 XML 的基础，下面详细介绍 Unicode 在计算机中的存储形式。
UTF-16 具体定义了 Unicode 字符在计算机中存取方法。UTF-16 用两个字节来表示 Unicode 转化格式，这个是定长的表示方法，不论什么字符都可以用两个字节表示，两个字节是 16 个 bit，所以叫 UTF-16。UTF-16 表示字符非常方便，每两个字节表示一个字符，这个在字符串操作时就大大简化了操作，这也是 Java 以 UTF-16 作为内存的字符存储格式的一个很重要的原因。
UTF-8
UTF-16 统一采用两个字节表示一个字符，虽然在表示上非常简单方便，但是也有其缺点，有很大一部分字符用一个字节就可以表示的现在要两个字节表示，存储空间放大了一倍，在现在的网络带宽还非常有限的今天，这样会增大网络传输的流量，而且也没必要。而 UTF-8 采用了一种变长技术，每个编码区域有不同的字码长度。不同类型的字符可以是由 1~6 个字节组成。
UTF-8 有以下编码规则：
如果一个字节，最高位（第 8 位）为 0，表示这是一个 ASCII 字符（00 - 7F）。可见，所有 ASCII 编码已经是 UTF-8 了。
如果一个字节，以 11 开头，连续的 1 的个数暗示这个字符的字节数，例如：110xxxxx 代表它是双字节 UTF-8 字符的首字节。
如果一个字节，以 10 开始，表示它不是首字节，需要向前查找才能得到当前字符的首字节。

####3. utf-8编码中的中文占几个字节；int型几个字节？
英文字母：

字节数 : 1;编码：GB2312

字节数 : 1;编码：GBK

字节数 : 1;编码：GB18030

字节数 : 1;编码：ISO-8859-1

字节数 : 1;编码：UTF-8

字节数 : 4;编码：UTF-16

字节数 : 2;编码：UTF-16BE

字节数 : 2;编码：UTF-16LE

 

中文汉字：

字节数 : 2;编码：GB2312

字节数 : 2;编码：GBK

字节数 : 2;编码：GB18030

字节数 : 1;编码：ISO-8859-1

字节数 : 3;编码：UTF-8

字节数 : 4;编码：UTF-16

字节数 : 2;编码：UTF-16BE

字节数 : 2;编码：UTF-16LE
int占4个字节。
####4. 静态代理和动态代理的区别，什么场景使用？
1. 代理类主要负责为委托类预处理消息、过滤消息、把消息转发给委托类，以及事后处理消息等
2. 	由程序员创建或由特定工具自动生成源代码，再对其编译。在程序运行前，代理类的.class文件就已经存在了。
3. 	在程序运行时，运用反射机制动态创建而成。
4. 	静态代理事先知道要代理的是什么，而动态代理不知道要代理什么东西，只有在运行时才知道。
5. 	场景就是，在调用一个方法的时候在前面或者后面添加一些逻辑。

#### 5.Java的异常体系
1. 上一张图片
![java异常](http://img.blog.csdn.net/20160818155613382?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
2. Error与Exception

    Error是程序无法处理的错误，比如OutOfMemoryError、ThreadDeath等。

    这些异常发生时，Java虚拟机（JVM）一般会选择线程终止。

    Exception是程序本身可以处理的异常，这种异常分两大类运行时异常和非运行时异常。程序中应当尽可能去处理这些异常。

2. 运行时异常和非运行时异常      
    - 运行时异常: 都是RuntimeException类及其子类异常：     IndexOutOfBoundsException索引越界异
    ArithmeticException：数学计算异常
    NullPointerException：空指针异常
    ArrayOutOfBoundsException：数组索引越界异常
    ClassNotFoundException：类文件未找到异常
    ClassCastException：造型异常（类型转换异常）
    这些异常是不检查异常（Unchecked Exception），程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的。
    - 非运行时异常:是RuntimeException以外的异常，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如：
    IOException、文件读写异常
    FileNotFoundException：文件未找到异常
    EOFException：读写文件尾异常
    MalformedURLException：URL格式错误异常
    SocketException：Socket异常
    SQLException：SQL数据库异常

#### 6.谈谈你对解析与分派的认识。
1. 解析
      所有方法调用中的目标方法在Class文件里面都是常量池中的符号引用，在类加载的解析阶段，会将其中的一部分符号引用转化为直接引用。这种解析的前提是：方法在程序真正运行之前就有一个可确定的调用版本，并且这个方法的调用版本在运行期是不可改变的，即“编译期可知，运行期不可变”，这类目标的方法的调用称为解析（Resolution）。
2.   解析调用一定是个静态的过程，在编译期就完全确定，在类加载的解析阶段就将涉及的符号引用全部转变为可以确定的直接引用，不会延迟到运行期       再去完成。而分派（Dispatch）调用则可能是静态的也可能是动态的。于是分派方式就有静态分派和动态分派.
3.   静态分派发生在编译阶段，不是由jvm执行的，典型的应用是重载（Overload）。静态分派的最直接的解释是在重载的时候是通过参数的静态类型而不是实际类型作为判断依据的。因此在编译阶段，Javac编译器会根据参数的静态类型决定使用哪个重载版本。
4.   动态分派和多态性的体现-重写（Override）有着本质的联系。
  
#### 7.修改对象A的equals方法的签名，那么使用HashMap存放这个对象实例的时候，会调用哪个equals方法？
1. 有待认证TODO，感觉是object的equals
  
  
#### 8.Java中实现多态的机制是什么？
1. 靠的是父类或接口定义的引用变量可以指向子类或具体实现类的实例对象，而程序调用的方法在运行期才动态绑定，就是引用变量所指向的具体实例对象的方法，也就是内存里正在运行的那个对象的方法，而不是引用变量的类型中定义的方法。这里只提供一个思路。 
  

#### 9.如何将一个Java对象序列化到文件里？
1. 要保存到文件首先必须得获得文件输入流，然后将文件输入流作为参数，构造对象输入流，然后就能直接将对象输入到文件中。而要将对象恢复，则需要先获得文件输出流，然后将文件输出流作为参数，构造对象输出流，就能够得到对象，然后再强制性转换为原始对象即可，实现代码如下:
  
  
```java

package saveobject;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

import model.Person;

public class FileHelper {
	private String fileName;
	
	public FileHelper(){
		
	}
	
	public FileHelper(String fileName){
		this.fileName=fileName;
	}
	
	/*
	 * 将person对象保存到文件中
	 * params:
	 * 	p:person类对象
	 */
	public void saveObjToFile(Person p){
		try {
			//写对象流的对象
			ObjectOutputStream oos=new ObjectOutputStream(new FileOutputStream(fileName));
			
			oos.writeObject(p);                 //将Person对象p写入到oos中
			
			oos.close();                        //关闭文件流
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} 
	}
	
	/*
	 * 从文件中读出对象，并且返回Person对象
	 */
	public Person getObjFromFile(){
		try {
			ObjectInputStream ois=new ObjectInputStream(new FileInputStream(fileName));
			
			Person person=(Person)ois.readObject();              //读出对象
			
			return person;                                       //返回对象
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		return null;
	}
}


```  

2.保存对象到数据库并恢复
    对象序列化之后得到的二进制流，所以要想保存序列化之后的对象，则必须用blob字段来保存。mysql中blob字段是用来存储二进制数据的。可以直接用PreparedStatement.setObject()方法来保存对象到数据库中。
    而要将对象恢复，则首先需要读出二进制数据，读出的方法是用ResultSet.getBlob()方法，然后用Blob对象的getBinaryStream()方法来获得二进制流对象，然后将该二进制流对象作为参数构造带缓冲区的流对象BufferedStream，然后用byte[]数组从BufferedInputStream流中读取二进制数据，然后用该byte数组来构造ByteArrayInputStream，然后用ByteArrayInputStream来构造ObjectInputStream，最后直接用ObjectInputStream对象的readObject方法读出对象数据，并强制性转化为原始的对象数据。
    
```java
package saveobject;

import java.io.BufferedInputStream;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.ObjectInputStream;
import java.sql.Blob;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import model.Person;

public class DBHelper {
	private static Connection conn;                                      //连接
	private PreparedStatement pres;                                      //PreparedStatement对象
	
	static{
		try {
			Class.forName("com.mysql.jdbc.Driver");              //加载驱动
			System.out.println("数据库加载成功!!!");
			String url="jdbc:mysql://localhost:3306/testdb";
			String user="root";
			String password="20130436";
			
			conn=DriverManager.getConnection(url,user,password); //建立连接
			System.out.println("数据库连接成功!!!");
		} catch (ClassNotFoundException | SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	/*
	 * 向数据库中的表testobj中插入多个Person对象
	 * params:
	 * 	persons:Person对象list
	 */
	public void savePerson(List<Person> persons){
		String sql="insert into objtest(obj) values(?)";
		
		try {
			pres=conn.prepareStatement(sql);
			for(int i=0;i<persons.size();i++){
				pres.setObject(1, persons.get(i));
				 
				pres.addBatch();                                   //实现批量插入
			}
			
			pres.executeBatch();                                      //批量插入到数据库中
			
			if(pres!=null)
				pres.close();
			
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	/*
	 * 从数据库中读出存入的对象
	 * return:
	 * 	list:Person对象列表
	 */
	public List<Person> getPerson(){
		List<Person> list=new ArrayList<Person>();
		String sql="select obj from objtest";
		
		try {
			pres=conn.prepareStatement(sql);
			
			ResultSet res=pres.executeQuery();
			while(res.next()){
				Blob inBlob=res.getBlob(1);                             //获取blob对象
				
				InputStream is=inBlob.getBinaryStream();                //获取二进制流对象
				BufferedInputStream bis=new BufferedInputStream(is);    //带缓冲区的流对象
				
				byte[] buff=new byte[(int) inBlob.length()];
				while(-1!=(bis.read(buff, 0, buff.length))){            //一次性全部读到buff中
					ObjectInputStream in=new ObjectInputStream(new ByteArrayInputStream(buff));
					Person p=(Person)in.readObject();                   //读出对象
					
					list.add(p);
				}
				
			}
		} catch (SQLException | IOException | ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		return list;
	}
}


```

#### 10.说说你对Java反射的理解
1. java不算是动态语言，但是反射算是让java成为动态语言的第一步，
2. 什么是动态语言，是指程序在运行时可以改变其结构：新的函数可以被引进，已有的函数可以被删除等在结构上的变化。比如JavaScript便是一个典型的动态语言
3. 而静态类型语言的类型判断是在运行前判断（如编译阶段），比如java就是静态类型语言。
4. 通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。程序中一般的对象的类型都是在编译期就确定下来的，而Java反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。
5. 想要哪个类，jvm去加载，然后创建对象，然后使用。在运行时可以动态加载和创建类并使用，我认为这是反射。  

#### 11.说说你对Java注解的理解
1. 对所注解的类或者方法在不同阶段(编译前，编译，运行)进行解释。
2. 通过反射可以拿到注解类，做一些你想做的事情,dagger，ButterKnife减少重复代码，retrofit。
3. 如果注解难于理解，你就把它类同于标签，标签为了解释事物，注解为了解释代码。
4. 注解的基本语法，创建如同接口，但是多了个 @ 符号。
5. 注解的元注解。
6. 注解的属性。
7. 注解主要给编译器及工具类型的软件用的。
8. 注解的提取需要借助于 Java 的反射技术，反射比较慢，所以注解使用时也需要谨慎计较时间成本。
[CSDN上的不错的说明](http://blog.csdn.net/briblue/article/details/73824058)  

#### 12.说说你对依赖注入的理解
学习过Spring框架的人一定都会听过Spring的IoC(控制反转) 、DI(依赖注入)这两个概念，对于初学Spring的人来说，总觉得IoC 、DI这两个概念是模糊不清的，是很难理解的，今天和大家分享网上的一些技术大牛们对Spring框架的IOC的理解以及谈谈我对Spring Ioc的理解。

一、分享Iteye的开涛对Ioc的精彩讲解
　　首先要分享的是Iteye的开涛这位技术牛人对Spring框架的IOC的理解，写得非常通俗易懂，以下内容全部来自原文，原文地址：http://jinnianshilongnian.iteye.com/blog/1413846

1.1、IoC是什么
　　Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。如何理解好Ioc呢？理解好Ioc的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

　　●谁控制谁，控制什么：传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对 象的创建；谁控制谁？当然是IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）。

　　●为何是反转，哪些方面反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。

　　用图例说明一下，传统程序设计如图2-1，都是主动去创建相关对象然后再组合起来：



图1-1 传统应用程序示意图

　　当有了IoC/DI的容器后，在客户端类中不再主动去创建这些对象了，如图2-2所示:

图1-2有IoC/DI容器后程序结构示意图

1.2、IoC能做什么
　　IoC 不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试；有了IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是 松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。

　　其实IoC对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在IoC/DI思想中，应用程序就变成被动的了，被动的等待IoC容器来创建并注入它所需要的资源了。

　　IoC很好的体现了面向对象设计法则之一—— 好莱坞法则：“别找我们，我们找你”；即由IoC容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。

1.3、IoC和DI
　　DI—Dependency Injection，即“依赖注入”：组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

　　理解DI的关键是：“谁依赖谁，为什么需要依赖，谁注入谁，注入了什么”，那我们来深入分析一下：

　　●谁依赖于谁：当然是应用程序依赖于IoC容器；

　　●为什么需要依赖：应用程序需要IoC容器来提供对象需要的外部资源；

　　●谁注入谁：很明显是IoC容器注入应用程序某个对象，应用程序依赖的对象；

　　●注入了什么：就是注入某个对象所需要的外部资源（包括对象、资源、常量数据）。

　　IoC和DI由什么关系呢？其实它们是同一个概念的不同角度描述，由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以2004年大师级人物Martin Fowler又给出了一个新的名字：“依赖注入”，相对IoC 而言，“依赖注入”明确描述了“被注入对象依赖IoC容器配置依赖对象”。

　　看过很多对Spring的Ioc理解的文章，好多人对Ioc和DI的解释都晦涩难懂，反正就是一种说不清，道不明的感觉，读完之后依然是一头雾水，感觉就是开涛这位技术牛人写得特别通俗易懂，他清楚地解释了IoC(控制反转) 和DI(依赖注入)中的每一个字，读完之后给人一种豁然开朗的感觉。我相信对于初学Spring框架的人对Ioc的理解应该是有很大帮助的。

二、分享Bromon的blog上对IoC与DI浅显易懂的讲解
2.1、IoC(控制反转)
　　首先想说说IoC（Inversion of Control，控制反转）。这是spring的核心，贯穿始终。所谓IoC，对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。这是什么意思呢，举个简单的例子，我们是如何找女朋友的？常见的情况是，我们到处去看哪里有长得漂亮身材又好的mm，然后打听她们的兴趣爱好、qq号、电话号、ip号、iq号………，想办法认识她们，投其所好送其所要，然后嘿嘿……这个过程是复杂深奥的，我们必须自己设计和面对每个环节。传统的程序开发也是如此，在一个对象中，如果要使用另外的对象，就必须得到它（自己new一个，或者从JNDI中查询一个），使用完之后还要将对象销毁（比如Connection等），对象始终会和其他的接口或类藕合起来。

　　那么IoC是如何做的呢？有点像通过婚介找女朋友，在我和女朋友之间引入了一个第三者：婚姻介绍所。婚介管理了很多男男女女的资料，我可以向婚介提出一个列表，告诉它我想找个什么样的女朋友，比如长得像李嘉欣，身材像林熙雷，唱歌像周杰伦，速度像卡洛斯，技术像齐达内之类的，然后婚介就会按照我们的要求，提供一个mm，我们只需要去和她谈恋爱、结婚就行了。简单明了，如果婚介给我们的人选不符合要求，我们就会抛出异常。整个过程不再由我自己控制，而是有婚介这样一个类似容器的机构来控制。Spring所倡导的开发方式就是如此，所有的类都会在spring容器中登记，告诉spring你是个什么东西，你需要什么东西，然后spring会在系统运行到适当的时候，把你要的东西主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被spring控制，所以这叫控制反转。

2.2、DI(依赖注入)
　　IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的。比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在适当的时候制造一个Connection，然后像打针一样，注射到A当中，这样就完成了对各个对象之间关系的控制。A需要依赖 Connection才能正常运行，而这个Connection是由spring注入到A中的，依赖注入的名字就这么来的。那么DI是如何实现的呢？ Java 1.3之后一个重要特征是反射（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。

　　理解了IoC和DI的概念后，一切都将变得简单明了，剩下的工作只是在spring的框架中堆积木而已。

三、我对IoC(控制反转)和DI(依赖注入)的理解
　　在平时的java应用开发中，我们要实现某一个功能或者说是完成某个业务逻辑时至少需要两个或以上的对象来协作完成，在没有使用Spring的时候，每个对象在需要使用他的合作对象时，自己均要使用像new object() 这样的语法来将合作对象创建出来，这个合作对象是由自己主动创建出来的，创建合作对象的主动权在自己手上，自己需要哪个合作对象，就主动去创建，创建合作对象的主动权和创建时机是由自己把控的，而这样就会使得对象间的耦合度高了，A对象需要使用合作对象B来共同完成一件事，A要使用B，那么A就对B产生了依赖，也就是A和B之间存在一种耦合关系，并且是紧密耦合在一起，而使用了Spring之后就不一样了，创建合作对象B的工作是由Spring来做的，Spring创建好B对象，然后存储到一个容器里面，当A对象需要使用B对象时，Spring就从存放对象的那个容器里面取出A要使用的那个B对象，然后交给A对象使用，至于Spring是如何创建那个对象，以及什么时候创建好对象的，A对象不需要关心这些细节问题(你是什么时候生的，怎么生出来的我可不关心，能帮我干活就行)，A得到Spring给我们的对象之后，两个人一起协作完成要完成的工作即可。

　　所以控制反转IoC(Inversion of Control)是说创建对象的控制权进行转移，以前创建对象的主动权和创建时机是由自己把控的，而现在这种权力转移到第三方，比如转移交给了IoC容器，它就是一个专门用来创建对象的工厂，你要什么对象，它就给你什么对象，有了 IoC容器，依赖关系就变了，原先的依赖关系就没了，它们都依赖IoC容器了，通过IoC容器来建立它们之间的关系。

　　这是我对Spring的IoC(控制反转)的理解。DI(依赖注入)其实就是IOC的另外一种说法，DI是由Martin Fowler 在2004年初的一篇论文中首次提出的。他总结：控制的什么被反转了？就是：获得依赖对象的方式反转了。

四、小结
　　对于Spring Ioc这个核心概念，我相信每一个学习Spring的人都会有自己的理解。这种概念上的理解没有绝对的标准答案，仁者见仁智者见智。如果有理解不到位或者理解错的地方，欢迎广大园友指正！
**写的非常好**  

#### 13.Java中String的了解
1. String类初始化后是不可变的
[插个链接](https://www.cnblogs.com/xiaoxi/p/6036701.html)

#### 14. String为什么要设计成不可变的？
1. 执行效率，常量池为了执行效率，而且常量池必须是唯一的所以设计成不可变的，也是常量池的需要。字符串不变性保证了hash码的唯一性,因此可以放心地进行缓存，提高了效率。
2. 安全，String被许多的Java类(库)用来当做参数,例如 网络连接地址URL,文件路径path,还有反射机制所需要的String参数等, 假若String不是固定不变的,将会引起各种安全隐患。
  
```java
class Test{
    //不可变的String
    public static String appendStr(String s){
        s+="bbb";
        return s;
    }

    //可变的StringBuilder
    public static StringBuilder appendSb(StringBuilder sb){
        return sb.append("bbb");
    }

    public static void main(String[] args){
        //String做参数
        String s=new String("aaa");
        String ns=Test.appendStr(s);
        System.out.println("String aaa >>> "+s.toString());

        //StringBuilder做参数
        StringBuilder sb=new StringBuilder("aaa");
        StringBuilder nsb=Test.appendSb(sb);
        System.out.println("StringBuilder aaa >>> "+sb.toString());
    }
}

//Output: 
//String aaa >>> aaa
//StringBuilder aaa >>> aaabbb 

```

```java
class Test{
    public static void main(String[] args){
        HashSet<StringBuilder> hs=new HashSet<StringBuilder>();
        StringBuilder sb1=new StringBuilder("aaa");
        StringBuilder sb2=new StringBuilder("aaabbb");
        hs.add(sb1);
        hs.add(sb2);    //这时候HashSet里是{"aaa","aaabbb"}

        StringBuilder sb3=sb1;
        sb3.append("bbb");  //这时候HashSet里是{"aaabbb","aaabbb"}
        System.out.println(hs);
    }
}
//Output:
//[aaabbb, aaabbb]

```
[自己看](http://blog.csdn.net/qingmengwuhen1/article/details/52175303)
