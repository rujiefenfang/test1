# **流的概念**

- **概念**：内存与存储设备之间传输数据的通道。

  

- 水借助管道传输；数据借助流传输。

- ![img](https://pic.downk.cc/item/5f9ce4341cd1bbb86b8a1dfa.png)
- 

------

# **流的分类**

## **按方向【重点】**

- **输入流**：将<存储设备>中的内容读入到<内存>中。
- **输出流**：将<内存>中的内容写入到<存储设备>中。

![img](https://pic.downk.cc/item/5f9cea1b1cd1bbb86b8b4f67.png)

## **按单位**

- **字节流**：以字节为单位，可以读写所有数据。
- **字符流**：以字符为单位，只能读写文本数据。

## **按功能**

- **字节流**：具有实际传输数据的读写功能。
- **过滤流**：在节点流的基础之上增强功能。

------

# **字节流**

![img](https://pic.downk.cc/item/5f9d7ecb1cd1bbb86bce7018.png)

## **字节流的父类（抽象类）**

- InputStream

  字节输入流

  - `public int read(){}`
  - `public int read(byte[] b){}`
  - `public int read(byte[] b,int off,int len){}`

- OutputStream

  字节输出流

  - `public void write(int n){}`
  - `public void write(byte[] b){}`
  - `public void write(byte[] b,int off,int len){}`

## **字节流的子类**

### **文件字节流**

- **FileInputStream**
  - `public int read()`//从输入流中读取一个字节数据，返回读到的字节数据，如果达到文件末尾，返回-1。
  - `public int read(byte[] b)`//从输入流中读取字节数组长度的字节数据存入数组中，返回实际读到的字节数；如果达到文件的尾部，则返回-1。
- **FileOutputStream**
  - `public void write(int b)`//将指定字节写入输出流。
  - `public void write(bute[] b)`//一次写多个字节，将b数组中所有字节，写入输出流。

```
COPY/**
 * 演示文件字节输入流的使用
 * FileInputStream
 */
public class Demo1 {
	public static void main(String[] args) throws IOException {
		//文件内容：abcdefg
		FileInputStream fileInputStream=new FileInputStream("d:\\aaa.txt");
		
		//read();读入单个字节
		int data=fileInputStream.read();
		System.out.println((char)data);//a
		while((data=fileInputStream.read())!=-1) {
			System.out.print((char)data);
		}//bcdefg
		
		//read(byte[] b);读入多个字节
		byte[] b=new byte[1024];	
		if((data=fileInputStream.read(b))!=-1) {
			System.out.println(new String(b,0,data));
		}//bcdefg
		
		//关闭流
		fileInputStream.close();
	}
}
COPY/**
 * 演示文件字节输出流的使用
 * FileOutputStream
 */
public class Demo2 {
	public static void main(String[] args) throws IOException {
		//如果没有文件会自动创建
		FileOutputStream fileOutputStream=new FileOutputStream("d:\\bbb.txt");
		
		//write(int b);
		fileOutputStream.write(97);
		fileOutputStream.write('b');
		fileOutputStream.write('c');
		
		//write(byte[] b);
		fileOutputStream.write(new String("helloworld").getBytes());
		//此时文件bbb.txt内容为abc	helloworld
		
		//关闭流
		fileOutputStream.close();
	}
}
```

### **文件字节流小案例**

```
COPY/**
 * 使用文件字节流复制文件
 */
public class Demo3 {
	public static void main(String[] args) throws IOException {
		//将图片读取到输入流
		FileInputStream fileInputStream=new FileInputStream("d:\\MrG1.jpg");
		//从输出流写入数据
		FileOutputStream fileOutputStream=new FileOutputStream("d:\\MrG2.jpg");
		
		int count;//保存一次读取到的实际个数
		byte[] b=new byte[1024];
		
		while((count=fileInputStream.read(b))!=-1) {
			fileOutputStream.write(b, 0, count);
		}
		System.out.println("复制成功");
		
		//关闭流
		fileInputStream.close();
		fileOutputStream.close();
	}
}
```

### **字节缓冲流**

- **缓冲流**：BufferedInputStream/BufferedOutputStream

  - 提高IO效率，减少访问磁盘的次数；
  - 数据存储在缓冲区中。flush可以将缓存区的内容写入文件，也可以直接close。

  ```
  COPY/**
   * 使用字节缓冲流读取
   * BufferedInputStream
   */
  public class Demo4 {
  	public static void main(String[] args) throws IOException {
          //使用该输入流每次会从硬盘读入
          FileInputStream fileInputStream=new FileInputStream("d:\\aaa.txt");
          //缓冲流需要一个底层流
          //缓冲流每次从缓冲区读取
          BufferedInputStream bufferedInputStream=new BufferedInputStream(fileInputStream);
  
          //读取
          int data; 
          while((data=bufferedInputStream.read())!=-1) {
              System.out.print((char)data); 
          }	 
  
          //我们也可以自己创建一个缓冲区；
          //每次读取从自己创建的缓冲区中读取。
          int count;
          byte[] buf=new byte[1024];
          while((count=bufferedInputStream.read(b,0,b.length))!=-1) {			
              System.out.println(new String(buf,0,count));
          }
  
          bufferedInputStream.close();	
  	}
  }
  ```

  ```
  COPY/**
   * 使用字节缓冲流写入文件
   * BufferedOutputStream
   */
  public class Demo5 {
  	public static void main(String[] args) throws IOException {
  		FileOutputStream fileOutputStream=new FileOutputStream("d:\\buf.txt");
  		//缓冲流将数据写入到缓冲区
  		BufferedOutputStream bufferedOutputStream=new BufferedOutputStream(fileOutputStream);
  		//写入文件
  		bufferedOutputStream.write("helloworld".getBytes());
  		bufferedOutputStream.flush();
  		
  		//其实内部也会调用flush
  		bufferedOutputStream.close();
  	}
  }
  ```

------

# **对象流**

- **对象流**：ObjectOutputStream/ObjectInputStream

  - 增加了缓冲区功能。
  - 增强了读写8种基本数据类型和字符串功能。
  - 增强了读写对象的功能：
    - `readObject()`//从流中读取一个对象。
    - `writeObject(Object obj)`向流中写入一个对象。

  **使用流传输对象的过程称为序列化、反序列化。**

## **序列化**

```
COPY/**
 * 学生类
 */
public class Student {
	private String name;
	private int age;
	public Student(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	@Override
	public String toString() {
		return "Student [name=" + name + ", age=" + age + "]";
	}	
}
COPY/**
 * 使用ObjectOutputStream实现对象的序列化
 * 注：序列化的类必要要实现Serializable接口
 */
public class Demo6 {
	public static void main(String[] args) throws IOException {
        //这个文件后缀名表示二进制文件，但你可以写成其他如obj等任意后缀。
		FileOutputStream fileOutputStream=new FileOutputStream("d:\\stu.bin");
		ObjectOutputStream objectOutputStream=new ObjectOutputStream(fileOutputStream);
		//序列化（写入操作）
		Student tang=new Student("唐瑞", 21);
		objectOutputStream.writeObject(tang);
		objectOutputStream.close();
		System.out.println("序列化完毕");
	}
}
```

**注**：执行上述代码后IDE会抛出`java.io.NotSerializableException`，意思是Student类不能被序列化，需要实现Serializable接口。

```java
COPY//不需要实现任何方法
public class Student implements Serializable{ }
```

Serializable其实是一个标志接口，用来标志该类是否可以被序列化。我们进到该接口的源码可以发现里面不含任何属性和抽象方法。

```
COPYpublic interface Serializable {
}
```

## **反序列化**

```java
COPY/**
 * 使用ObjectInputStream实现反序列化（读取重构对象）
 */
public class Demo7 {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		FileInputStream fileInputStream=new FileInputStream("d:\\stu.bin");
		ObjectInputStream objectInputStream=new ObjectInputStream(fileInputStream);
		//读取文件（反序列化）
		Student student=(Student) objectInputStream.readObject();
		
		objectInputStream.close();
		
		System.out.println("执行完毕");
		System.out.println(student.toString());
	}
}
```

## **序列化和反序列化注意事项**

- 序列化类必须实现Serializable接口，前文已经说过。

- 序列化类中的对象属性也要求实现Serializable接口。也就是说如果Student类中有一个Grad类型的属性`private Grad info;`那么Grad这个类也要实现Serializable接口。

- 序列化类中可以添加序列化版本号ID，以保证序列化的类和被序列化的类是同一个类。在上面的代码中我并没有添加序列号版本，虽然IDE没有报错，但是会显示一个警告，提示我添加序列化版本号（串行版本标识）。我们可以在Student类中添加：

  ```
  COPYprivate static final long serialVersionUID = 66666L;
  ```

  此时再运行Demo7就会报一个无效类的异常：

  ```java
  COPYjava.io.InvalidClassException:
  local class incompatible: stream classdesc serialVersionUID = -3126921853274410929, local class serialVersionUID = 666660
  ```

  意思就是两个类的serialVersionUID不一样。可以看到之前虽然没有显式添加序列版本号，但它已经自动生成了一个。我们再运行一下Demo6序列化，再运行Demo7反序列化就可以正常执行了。

- 使用transient（短暂的）修饰属性，可以避免该属性被序列化。用它来修饰age：

  ```
  COPYprivate transient int age;
  ```

  把tang这个对象序列化后再反序列化，这个对象的age属性就变成了0。

- 静态属性不能被序列化。

- 可以利用集合来序列化多个对象：

  ```java
  COPYArrayList<Student> arrayList=new ArrayList<Student>();
  arrayList.add(s1);
  arrayList.add(s2);
  arrayList.add(s3);
  objectOutputStream.writeObject(arrayList);
  ```

  ```java
  COPYArrayList<Student> list=(ArrayList<Student>)objectInputStream.readObject();
  ```

------

# **编码方式**

- IOS-8859-1

  收录除ASCII外，还包括西欧、希腊语、泰语、阿拉伯语、希伯来语对应的文字符号。采用1个字节来表示，最多只能表示256个字符。

- UTF-8

  针对Unicode码表的可变长度字符编码。国际上使用的编码，也称为“万国码”，收录了几乎所有国家的常用字符。采用1至3个字节来表示一个字符。

- GB2312

  简体中文，采用1个或2个字节来表示字符，95年之前所采用的编码。

- GBK

  简体中文的扩充，GB2312的升级版本。

- BIG5

  台湾，繁体中文。

**当编码方式和解码方式不一致时，会出现乱码。**假如Demo1中的文件内容不是字母而是“**我爱中国**”这样的汉字，那么读取出来的信息就是乱码。因为字节流按字节输入输出，而这四个汉字占了12个字节，把一个汉字按一个一个字节读入自然会出现问题，这时就需要使用字符流。

------

# **字符流**

![img](https://pic.downk.cc/item/5f9ef3151cd1bbb86b28470e.png)

## **字符流的父类（抽象类）**：

- **Reader：**字符输入流

  - `public int read()`

    从流中读取单个字符，用整型来返回读取的字符；当读到流底部时返回-1。

  - `public int read(char[] c)`

    从流中读取字符保存到c数组中，返回读取的字符个数，当读到流底部时返回-1。

  - `public int read(char[] cbuf,int off,int len){}`

    抽象方法。

- **Writer：**字符输出流

  - `public void write(int n)`

    写入单个字符，只能写入包含16位低阶字节的整型数值，16位高阶字节将会被忽略。

  - `public void write(String str)`

    写入一个字符串。

  - `public void write(char[] cbuf)`

    写入一个字符数组。

## **字符流的子类**

- **FileReader：**

  - `public int read()`

    继承自InputStreamReader类。读取单个字符，返回读取的字符，当读到流底部时返回-1。

  - `public int read(char[] c)`

    继承自Reader类。

  - `public int read(char[] cbuf,int offset,int length)`

    继承自InputStreamReader类。从流中读取部分字符到cbuf中指定位置，返回读取到的字符个数，当读到流底部时返回-1。

  ```
  COPYpublic class Demo1 {
  	public static void main(String[] args) throws IOException {	
  		//文件内容：
  		//要是能活在梦里，我情愿醒不过来。
  		FileReader fileReader=new FileReader("d:\\cbuf.txt");
  		int data;
  		//read();与字节流不同，该方法读取单个字符而不是字节
  		while((data=fileReader.read())!=-1) {
  			System.out.print((char)data);
  		}
          //输出：
          //要是能活在梦里，我情愿醒不过来。
  		fileReader.close();
  	}
  }
  ```

  > **这里记录我遇到的一个问题**：
  >
  > 上述代码执行后理应是正常输出一段文字，但我这里出现了文字乱码。猜测是编码出现了问题，于是查看本地文本的保存格式为UTF-8，感觉也没错，于是打开JDK API查看FileReader类的描述：
  >
  > > Convenience class for reading character files. The constructors of this class assume that the default character encoding and the default byte-buffer size are appropriate. To specify these values yourself, construct an InputStreamReader on a FileInputStream.
  >
  > 大意就是该类的构造方法有一个默认的字符编码格式和一个默认字节缓冲区，并没有指明这个编码格式就是UTF-8。于是查看系统默认编码，打开CMD输入chcp，得到一个值为936的活动编码页，通过查询得知该代码页所对应的国家（地区）或语言为：中国 - 简体中文(GB2312)。这与本地所保存的文本编码UTF-8不一致，所以导致了文字乱码的出现。而要指定编码格式需要创建一个InputStreamReader或FileInputStream对象使用其构造方法。
  >
  > 以下是我在本地能正常运行的代码：
  >
  > ```
  > COPYpublic class Demo1 {
  > 	public static void main(String[] args) throws IOException {	
  >         //指定编码格式
  > 		InputStreamReader inputStreamReader=new InputStreamReader(new FileInputStream("d:\\cbuf.txt"),"UTF-8");
  > 		int data;
  > 		while((data=inputStreamReader.read())!=-1) {
  > 			System.out.print((char)data);
  > 		}
  > 		inputStreamReader.close();
  > 	}
  > }
  > ```

- **FileWriter:**

  - `public void write(int c)`

    继承自OutputStreamWriter类，写入一个字符。

  - `public void write(String str)`

    继承自Writer类。

  - `public void Write(char[] cbuf)`

    继承自Writer类。

  ```
  COPY/**
   * 使用FileWriter写入文件
   */
  public class Demo2 {
  	public static void main(String[] args) throws IOException {
  		FileWriter fileWriter=new FileWriter("d:\\w.txt");
  		//写入
  		fileWriter.write("给自己一个希望。");
  		
  		//关闭
  		fileWriter.close();
  		System.out.println("执行完毕");
  	}
  }
  ```

### **字符流小案例**

```
COPY/**
 * 使用FileReader和FileWrite复制文本文件
 *  注：不能复制图片或二进制文件
 */
public class Demo3 {
	public static void main(String[] args) throws IOException {
		InputStreamReader inputStreamReader=new InputStreamReader(new FileInputStream("d:\\w.txt"));
		FileWriter fileWriter=new FileWriter("d:\\w2.txt");
		//读写
		int data=0;
		while((data=inputStreamReader.read())!=-1) {
			fileWriter.write(data);			
		}
		
		inputStreamReader.close();
		fileWriter.close();
		System.out.println("执行完毕");
	}
}
```

### **字符缓冲流**

- **缓冲流**：BufferedReader/BufferedWriter

  - 高效读写
  - 支持换行输入符
  - 可一次写一行、读一行。

  ```
  COPY/**
   * 使用字符缓冲流读取文件
   * BufferedReader
   */
  public class Demo4 {
  	public static void main(String[] args) throws IOException {
  		InputStreamReader inputStreamReader=new InputStreamReader(new FileInputStream("d:\\cbuf.txt"),"UTF-8");
  		BufferedReader bufferedReader=new BufferedReader(inputStreamReader);
  		//read(char[] cbuf)
  		char[] cbuf=new char[1024];
  		int count;
  		while((count=bufferedReader.read(cbuf))!=-1) {
  			System.out.println(new String(cbuf,0,count));
  		}
  		
  		//readline();
  		//该方法一次读取一行，返回一个字符串
  		String line;
  		while((line=bufferedReader.readLine())!=null) {
  			System.out.println(line);
  		}
  	}
  }
  ```

  ```
  COPY/**
   * 使用字符缓冲流写入文件
   * BufferedWriter
   */
  public class Demo5 {
  	public static void main(String[] args) throws IOException {
  		BufferedWriter bufferedWriter=new BufferedWriter(new FileWriter("d:\\w3.txt"));
  		bufferedWriter.write("真有人看这篇博客吗？");
  		//写入一个换行符windows \r\n  linux \n
  		bufferedWriter.newLine();
  		bufferedWriter.write("不会吧不会吧！！");
  		
  		bufferedWriter.close();
  		System.out.println("执行完毕");
  	}
  }
  ```

### **打印流**

- **PrintWriter：**

  - 封装了print()/println()方法，支持写入后换行。
  - 支持数据原样打印。

  ```
  COPY/**
   * PrintWriter的使用
   */
  public class Demo6 {
  	public static void main(String[] args) throws FileNotFoundException {
  		PrintWriter printWriter=new PrintWriter("d:\\p.txt");
  		//打印到文件
  		printWriter.println(97);//97
  		printWriter.println('b');//b
  		printWriter.println(3.14);//3.14
  		printWriter.println(true);//true
  		
  		printWriter.close();
  		System.out.println("执行完毕");
  	}
  }
  ```

### **转换流**

- **桥转换流**：InputStreamReader/OutputStreamWriter

  - 可将字节流转换为字符流。
  - 可设置字符的编码方式。

  其实这个在上面的Demo中我已经用过了，这里不再演示InputStreamReader的使用。

  ```
  COPY/**
   * 使用OutputStreamWriter写入文件
   */
  public class Demo7 {
  	public static void main(String[] args) throws IOException {
  		//可以自行指定编码
  		OutputStreamWriter outputStreamWriter=new OutputStreamWriter(new FileOutputStream("d:\\info.txt"));
  		
  		outputStreamWriter.write("知道的越多，不明白的也就更多，学海无涯。");
  		
  		outputStreamWriter.close();
  		System.out.println("执行完毕");
  	}
  }
  ```

------

# **File类**

- **概念**：代表物理盘符中的一个**文件**或者**文件夹**。

- **方法**：

  - `public boolean CreateNewFile()`

    当且仅当指定的文件名不存在时创建一个指定的新的、空的文件。创建成功返回true，如果指定文件名已存在返回false。

  - `public boolean mkdir()`

    创建一个指定路径名的文件夹。当且仅当文件夹被创建时返回true，否则返回false。

  - `public boolean delete()`

    删除一个指定的文件或文件夹，文件夹必须为空才能被删除。当且仅当指定的文件或文件夹被删除时返回true，否则返回false。

  - `public boolean exists()`

    检查指定的文件或文件夹是否存在。当且仅当指定的文件或者文件夹存在时返回true，否则返回false。

  - `public File[] listFiles()`

    列出目录中的所有内容，返回一个指定路径名中的文件数组，如果指定的路径名不代表一个文件夹（目录）就返回null。

  - `public boolean renameTo(File dest)`

    重命名一个路径名所指定的文件。当且仅当修改操作成功时返回true，否则返回false。

## **文件操作**

```
COPY/**
 * 演示文件操作
 * 分隔符
 */
public class Demo1 {
	public static void main(String[] args) throws IOException, InterruptedException {
		//输出：
		//路径分隔符：;
		//名称分隔符：\
		separator();
		
		fileOp();
	}
	public static void separator() {
		System.out.println("路径分隔符："+File.pathSeparator);
		System.out.println("名称分隔符："+File.separator);
	}
	public static void fileOp() throws IOException, InterruptedException {
		//1.创建文件
		File file=new File("d:\\file.txt");
		if(!file.exists()) {
			boolean flag=file.createNewFile();			
			System.out.println("创建状态："+flag);
		}
		
		//2.删除文件
		//2.1 直接删除
		/* System.out.println("删除结果："+file.delete()); */
		//2.2 使用JVM退出时删除（不是自己删除）
		file.deleteOnExit();
		//调用休眠程序观察删除操作
		Thread.sleep(5000);
		
		//3.获取文件信息
		System.out.println("文件绝对路径："+file.getAbsolutePath());
		System.out.println("获取路径："+file.getPath());
		System.out.println("获取父目录："+file.getParent());
		System.out.println("获取文件名称："+file.getName());
		System.out.println("获取文件长度："+file.length());
		System.out.println("获取文件创建时间："+new Date(file.lastModified()).toLocaleString());
		
		//4.判断
		System.out.println("是否可写："+file.canWrite());
		System.out.println("是否可读："+file.canRead());
		System.out.println("是否隐藏："+file.isHidden());
		System.out.println("是否是文件："+file.isFile());
		System.out.println("是否是文件夹："+file.isDirectory());
	}
}
```

## **文件夹操作**

```
COPY/**
 * 文件夹的操作
 */
public class Demo2 {
	public static void main(String[] args) {
		directorOp();
	}
	public static void  directorOp() {
		//1.创建文件夹
		File dir=new File("d:\\aaa\\bbb\\ccc");
		if (!dir.exists()) {
			//mkdir();只能创建单级目录
			boolean flag=dir.mkdirs();//创建多级目录
			System.out.println("创建结果："+flag);
		}
		
		//2.删除文件夹
		//2.1 直接删除（必须为空目录）
		//System.out.println("删除结果："+dir.delete());
		//2.2 使用jvm删除
		dir.deleteOnExit();
		
		//3.获取文件夹信息
		System.out.println("获取绝对路径："+dir.getAbsolutePath());
		System.out.println("获取路径："+dir.getPath());
		System.out.println("获取父目录："+dir.getParent());
		System.out.println("获取创建时间："+new Date(dir.lastModified()).toLocaleString());
		System.out.println("文件夹名称："+dir.getName());
		
		//4.判断
		System.out.println("是否隐藏："+dir.isHidden());
		System.out.println("是否是文件："+dir.isFile());
		
		//5.遍历文件夹
		File dir2=new File("d:\\");
		String[] files=dir2.list();
		for (String string : files) {
			System.out.println(string);
		}		
	}
}
```

## **文件过滤器**

- **FileFilter接口：**

  `public interface FileFilter`

  - `boolean accepte(File pathname)`
  - 当调用File类中的listFiles()方法时，支持传入FileFilter接口实现类，对获取的文件进行过滤，只有满足条件的文件才可以出现在listFiles()的返回值中。

  在上文Demo2中添加演示代码：

  ```
  COPYpublic static void  directorOp() {
      File[] files1=dir2.listFiles(new FileFilter() {		
          @Override
          public boolean accept(File pathname) {
              if(pathname.getName().endsWith(".txt")) {
                  return true;
              }
              return false;
          }
      });
      for (File file : files1) {
          System.out.println(file.getName());
      }
  }
  ```

## **文件操作小案例**

```java
COPY/**
 * 1.递归遍历文件夹
 * 2.递归删除文件夹
 */
public class Demo3 {
	public static void main(String[] args) {
		//略
	}
	//案例1：递归遍历文件夹
	public static void listDer(File dir) {
		File[] files=dir.listFiles();
		if(files!=null&&files.length>0) {
			for (File file : files) {
				if (file.isDirectory()) {
					listDer(file);
				}else {
					System.out.println(file.getName());
				}
			}
		}
	}
	//案例2：递归删除文件夹
	public static void deleteDir(File dir) {
		File[] files=dir.listFiles();
		if (files!=null&&files.length>0) {
			for (File file : files) {
				if (file.isDirectory()) {
					deleteDir(file);
				}else {
					file.delete();
				}				
			}
		}	
		dir.delete();	
	}
}
```

------

# **补充：Properties**

- **Properties：**属性集合

- **特点：**

- 存储属性名和属性值（键值对）。

  - 属性名和属性值都是字符串类型。
  - 没有泛型。
  - 和流有关（所以没有整理在集合里面）。

- **方法：**

  - `public String getProperty(String key)`

    根据key在属性列表里查找value，如果原始属性列表找不到就去默认属性列表找。返回key所对应的value。

  - `public void list(PrintWriter out)`

    将属性列表打印在指定的输出流上，在debug时很有用。

  - `public Object setProperty(String key,String value)`

    内部调用的是Hashtable的put方法，将key和value成对地保存在属性列表中。返回这个key上一个对应的value，没有就返回null。

  Properties可以保存在一个流中或是从一个流中加载，属性列表中的每个键值对都是一个字符串。一个属性列表可以包括另一个第二属性列表来作为它的默认值，如果在原始属性列表中没有找到key时就搜索第二属性列表。

  ```java
  COPY/**
   * 演示集合properties的使用
   */
  public class Demo1 {
  	public static void main(String[] args) throws IOException {
  		Properties properties=new Properties();
  		//添加数据
  		properties.setProperty("username", "tangrui");
  		properties.setProperty("age", "21");
  		System.out.println(properties.toString());
  		
  		//遍历
  		//3.1 keySet 略
  		//3.2 entrySet 略
  		//3.3 stringPropertyNames()
  		Set<String> set=properties.stringPropertyNames();
  		for (String string : set) {
  			System.out.println(string+" "+properties.getProperty(string));
  		}
  		
  		//和流有关的方法
  		//list
  		PrintWriter printWriter=new PrintWriter("d:\\print.txt");
  		properties.list(printWriter);
  		printWriter.close();
  		//store保存
  		FileOutputStream fileOutputStream=new FileOutputStream("d:\\s.properties");
  		properties.store(fileOutputStream, "NOTES");
  		fileOutputStream.close();
  		//load加载
  		Properties properties2=new Properties();
  		FileInputStream fileInputStream=new FileInputStream("d:\\s.properties");
  		properties2.load(fileInputStream);
  		fileInputStream.close();
  		System.out.println(properties2.toString());
  	}
  }
  ```