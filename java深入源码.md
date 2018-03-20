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
* 说说你对依赖注入的理解
* 说一下泛型原理，并举例说明
* Java中String的了解
* String为什么要设计成不可变的？