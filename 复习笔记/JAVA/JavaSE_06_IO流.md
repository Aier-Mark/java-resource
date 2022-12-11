# 1 IO分类

![1657797824907](.\assets\1657797824907.png)

字节流：

1. 字节流处理的单元是一个字节，用于操作二进制文件，流可以处理任何类型（比如图片，视频，是以二进制传输的）。
2. 字节流在操作文件时，即使不关闭资源（close方法），文件也能输出。

字符流：

1. 字节流+编码表，更便于操作文字数据，是由Java虚拟机将字节转化为2个字节的Unicode字符为单位的字符而成的，所以它对多国语言支持性比较好。
2. 字符流不使用close方法的话，则不会输出任何内容，因为字符流用的是缓冲区，可以使用flush方法强制进行刷新缓冲区，这时才能在不close的情况下输出内容。

# 2 File类

## 2.1 File类创建

文件是组织一堆数据的方式。类型有很多：`.txt、.doc、.mp3、.jpg、.rmvb` 等应用程序经常需要对文件进行操作，Java提供了 java.io.File 类用于对文件的封装File类中定义了一些与平台无关的方法供我们来操作文件，例如：创建、删除、重命名文件等。在Java中，目录(文件夹)也是用File类来进行封装的。

```java
public static void main(String[] args) throws IOException {
    File file1 = new File("src\\io\\io.txt");
    File file2 = new File("e:\\MyProject\\io.txt");
    File file3 = new File("src" + File.separator + "io" + File.separator + "io1.txt");

    file1.createNewFile();
    file2.createNewFile();
    file3.createNewFile();
}
```

## 2.2 常用文件方法

| 方法                                 | 描述                           |
| :----------------------------------- | :----------------------------- |
| public File(String pathname)         | 根据传入的完整路径创建File对象 |
| public boolean exists()              | 判断文件是否存在               |
| public boolean createNewFile()       | 创建新文件，路径必须存在       |
| public long lastModified()           | 文件最后一次被修改的时间       |
| public long length()                 | 返回文件内容长度，单位字节     |
| public String getName()              | 获得文件的名字                 |
| public Strint getPath()              | 返回文件的路径信息             |
| public boolean isFile()              | 判断给定的路径是否是一个文件   |
| public boolean delete()              | 删除文件                       |
| public static final String separator | 与系统平台有关的默认路径分隔符 |

```java
public static void main(String[] args) throws IOException {
    File file = new File("src\\io\\hello.txt");

    file.createNewFile();
    System.out.println("文件是否存在："+file.exists());
    System.out.println("修改时间："+new Date(file.lastModified()).toLocaleString());
    System.out.println("文件大小："+file.length());
    System.out.println("文件名称："+file.getName());
    System.out.println("文件路径："+file.getPath());
    System.out.println("是否是一个文件："+file.isFile());
    System.out.println("绝对路径："+file.getAbsolutePath());
    System.out.println("绝对路径："+file.getAbsoluteFile());
    System.out.println("是否可读："+file.canRead());
    file.delete();
}
```

## 2.3 常用文件夹方法

| 方法                         | 描述                               |
| ---------------------------- | ---------------------------------- |
| public File(String pathname) | 根据传入的完整路径创建File对象     |
| public boolean exists()      | 判断目录是否存在                   |
| public boolean mkdir()       | 根据路径创建单级目录               |
| public boolean mkdirs()      | 根据路径创建目录且允许创建多级目录 |
| public boolean isDirectory() | 判断给定的路径是否为目录           |
| public String getName()      | 获得目录的名字                     |
| public File[] listFiles()    | 列出指定目录的全部文件             |
| public String[] list()       | 列出指定目录的全部文件的名称       |
| public boolean delete()      | 删除目录（空）                     |

```java
File file = new File("src\\io");
File file1 = new File("src\\io\\io1");
File file2 = new File("src\\io\\io2\\hello");

//创建单级目录
file1.mkdir();
//创建多级目录
file2.mkdirs();

System.out.println("是否是一个文件夹：" + file1.isDirectory());

//列出全部文件名称
String[] list = file.list();
System.out.println(Arrays.toString(list));

//列出全部文件
File[] files = file.listFiles();
for (File f : files) {
    System.out.println("名字" + f.getName());
}
```

## 2.4 File过滤器

使用 FileFilter 或者 FilenameFilter 可以实现文件过滤功能。

```java
public class Test08_文件过滤 {
    public static void main(String[] args) {
        File file = new File("src\\io\\son1\\son2");
        File[] fs = file.listFiles(new Filter());
        for (File f : fs) {
            System.out.println(f.getName());
        }

        File[] fs2 = file.listFiles(new FileFilter() {
            @Override
            public boolean accept(File pathname) {
                return pathname.getName().endsWith(".png");
            }
        });

        for (File f : fs2) {
            System.out.println(f.getName());
        }
    }
}

class Filter implements FileFilter {
    @Override
    public boolean accept(File pathname) {
        return pathname.getName().endsWith(".txt") || pathname.getName().endsWith(".doc");
    }
}
```

## 2.5 递归

递归做为一种算法在程序设计语言中广泛应用.是指函数/过程/子程序在运行过程中直接或间接调用自身而产生的重 入现象。(自己调用自己，有结束条件)注意：递归时一定要明确结束条件。（递归次数过多也会内存溢出）

```java
static String trim = "\t";
static int num = 0;

public static void main(String[] args) {
    File file = new File("src\\io");
    find(file);
}

public static void find(File file) {
    File[] files = file.listFiles();
    for (File f : files) {
        if (f.isFile()) {
            for (int i = 0; i < num; i++) {
                System.out.print(trim);
            }
            System.out.println(f.getName() + "\t" + f.length() + " 字符");
        }
    }
    for (File f : files) {
        if (f.isDirectory()) {
            for (int i = 0; i < num; i++) {
                System.out.print(trim);
            }
            System.out.println(f.getName());
            num++;
            find(f);
            num--;
        }
    }
}
```

# 3 字节流

IO流简介：（Input/Output）

I/O类库中使用“流”这个抽象概念。Java对设备中数据的操作是通过流的方式。表示任何有能力产出数据的数据源对象，或者是有能力接受数据的接收端对象。“流”屏蔽了实际的I/O设备中处理数据的细节。IO流用来处理设备之间的数据传输。设备是指硬盘、内存、键盘录入、网络等。Java用于操作流的对象都在IO包中。IO流技术主要用来处理设备之间的数据传输。由于Java用于操作流的对象都在IO包中。所以使用IO流需要 导包如：import java.io.*；

IO流的分类：

- 按操作数据类型的不同分为两种：字节流与字符流。 
- 按流向分为：输入流，输出流。以程序（内存）为参照物，输入到程序（内存），或是从程序（内存）输出。

字节流：

计算机中都是二进制数据，一个字节是8个2进制位。字节可以表示所有的数据，比如文本、音频、视频、图片，都是作为字节存在的。也就是说字节流处理的数据非常多。

字节流的抽象基类： 

- 输入流： java.io.InputStream 
- 输出流： java.io.OutputStream 
- 特点： 字节流的抽象基类派生出来的子类名称都是以其父类名作为子类名的后缀。 如 ： FileInputStream , ByteArrayInputStream 等。 
- 说明： 字节流处理的单元是一个字节，用于操作二进制文件（计算机中所有文件都是二进制文件）    

## 3.1 InputStream

步骤：

1. 打开流（即创建流）。
2. 通过流读取内容。
3. 用完后，关闭流资源。

### 3.1.1 创建InputStream对象

```java
public static void main(String[] args) throws FileNotFoundException {
    String url = "src\\upload\\a.txt";
    
    InputStream is1 = new FileInputStream(url);
    
    File file = new File(url);
    InputStream is2 = new FileInputStream(file);
}
```

### 3.1.2 读取一个字节

```java
public static void main(String[] args) throws IOException {
    String url = "src\\io\\hello.txt";

    InputStream is = new FileInputStream(url);

    //一次读取一个字节
    int num = is.read();

    //类型转换
    System.out.println((char) num);

    //循环读取，但还是一次一个字节
    File f = new File(url);
    for (int i = 0; i < f.length() - 1; i++) {
        int n = is.read();
        System.out.print((char) n);
    }

    is.close();
}
```

### 3.1.3 读取多个字节

