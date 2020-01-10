>java.io下的流种类繁多，但是只要我们捋顺了之后，就会豁然开朗,接下来我们一起来捋捋这错综复杂的io流  

![](https://github.com/zhangwanghua/dafeng/blob/master/src/java%E9%AB%98%E7%BA%A7/img/t_io.png?raw=true)  

其中，以Stream结尾的为字节流，以Writer或者Reader结尾的为字符流。所有的输入流都是抽象类IuputStream（字节输入流）或者抽象类Reader（字符输入流）的子类，所有的输出流都是抽象类OutputStream(字节输出流)或者抽象类Writer(字符输出流)的子类。字符流能实现的功能字节流都能实现，反之不一定。如：图片，视频等二进制文件，只能使用字节流读写。

## 1.字符流FileReader和FileWriter
使用FileReader和FileWriter类完成文本文件复制：
```java
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class CopyFile {
    public static void main(String[] args) throws IOException {
        //创建输入流对象
        FileReader fr=new FileReader("C:\\Test\\copyfrom.txt");//文件不存在会抛出java.io.FileNotFoundException
        //创建输出流对象
        FileWriter fw=new FileWriter("C:\\Test\\copyto.txt");
        /*创建输出流做的工作：
         *         1、调用系统资源创建了一个文件
         *         2、创建输出流对象
         *         3、把输出流对象指向文件        
         * */
        //文本文件复制，一次读一个字符
        method1(fr, fw);
        //文本文件复制，一次读一个字符数组
        method2(fr, fw);
        
        fr.close();
        fw.close();
    }

    public static void method1(FileReader fr, FileWriter fw) throws IOException {
        int ch;
        while((ch=fr.read())!=-1) {//读数据
            fw.write(ch);//写数据
        }
        fw.flush();
    }

    public static void method2(FileReader fr, FileWriter fw) throws IOException {
        char chs[]=new char[1024];
        int len=0;
        while((len=fr.read(chs))!=-1) {//读数据
            fw.write(chs,0,len);//写数据
        }
        fw.flush();
    }
}
```
## 2.字符缓冲流BufferedReader和BufferedWriter
>字符缓冲流具备文本特有的表现形式，行操作  

### public class BufferedReader extends Reader
1. 从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。
2. 可以指定缓冲区的大小，或者可使用默认的大小。大多数情况下，默认值就足够大了。
3. 通常，Reader 所作的每个读取请求都会导致对底层字符或字节流进行相应的读取请求。因此，建议用 BufferedReader 包装所有其 read() 操作可能开销很高的 Reader（如 FileReader 和 InputStreamReader）。例如，
BufferedReader in = new BufferedReader(new FileReader("foo.in"));
4. 将缓冲指定文件的输入。如果没有缓冲，则每次调用 read() 或 readLine() 都会导致从文件中读取字节，并将其转换为字符后返回，而这是极其低效的。 

### public class BufferedWriter extends Writer

1. 将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。
2. 可以指定缓冲区的大小，或者接受默认的大小。在大多数情况下，默认值就足够大了。
3. 该类提供了 newLine() 方法，它使用平台自己的行分隔符概念，此概念由系统属性 line.separator 定义。并非所有平台都使用新行符 ('\n') 来终止各行。因此调用此方法来终止每个输出行要优于直接写入新行符。
4. 通常 Writer 将其输出立即发送到底层字符或字节流。除非要求提示输出，否则建议用 BufferedWriter 包装所有其 write() 操作可能开销很高的 Writer（如 FileWriters 和 OutputStreamWriters）。例如，
PrintWriter out = new PrintWriter(new BufferedWriter(new FileWriter("foo.out")));
5. 缓冲 PrintWriter 对文件的输出。如果没有缓冲，则每次调用 print() 方法会导致将字符转换为字节，然后立即写入到文件，而这是极其低效的。

使用BufferedReader和BufferedWriter完成文件复制  
```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class CopyFile2 {
    public static void main(String[] args) throws IOException {
        //创建输入流对象
        BufferedReader br=new BufferedReader(new FileReader("C:\\Test\\copyfrom.txt"));//文件不存在会抛出java.io.FileNotFoundException
        //创建输出流对象
        BufferedWriter bw=new BufferedWriter(new FileWriter("C:\\Test\\copyto.txt"));
        //文本文件复制
        char [] chs=new char[1024];
        int len=0;
        while((len=br.read(chs))!=-1) {
            bw.write(chs, 0, len);
        }
        //释放资源
        br.close();
        bw.close();
    }
}
```
缓冲区的工作原理：  
1. 使用了底层流对象从具体设备上获取数据，并将数据存储到缓冲区的数组内。
2. 通过缓冲区的read()方法从缓冲区获取具体的字符数据，这样就提高了效率。
3. 如果用read方法读取字符数据，并存储到另一个容器中，直到读取到了换行符时，将另一个容器临时存储的数据转成字符串返回，就形成了readLine()功能。

## 3.字节流FileInputStream和FileOutputStream 
### public class FileInputStream extends InputStream

1. FileInputStream 从文件系统中的某个文件中获得输入字节。哪些文件可用取决于主机环境。
2. FileInputStream 用于读取诸如图像数据之类的原始字节流。

### public class FileOutputStream extends OutputStream

1. 文件输出流是用于将数据写入 File 或 FileDescriptor 的输出流。文件是否可用或能否可以被创建取决于基础平台。特别是某些平台一次只允许一个 FileOutputStream（或其他文件写入对象）打开文件进行写入。在这种情况下，如果所涉及的文件已经打开，则此类中的构造方法将失败。
2. FileOutputStream 用于写入诸如图像数据之类的原始字节的流。

使用字节流复制图片
```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class CopImg {
    public static void main(String[] args) throws IOException {
        FileInputStream fin=new FileInputStream("C:\\Users\\Administrator\\Desktop\\Img.jpg");
        FileOutputStream fout=new FileOutputStream("C:\\Users\\Administrator\\Desktop\\ImgCopy.jpg");
        int len=0;
        byte[] buff=new byte[1024];
        while((len=fin.read(buff))!=-1) {
            fout.write(buff, 0, len);
        }
        fin.close();
        fout.close();
    }
}
```
## 4.字节缓冲流BufferedInputStream和BufferedOutputStream
### public class BufferedInputStream extends FilterInputStream

BufferedInputStream 为另一个输入流添加一些功能，即缓冲输入以及支持 mark 和 reset 方法的能力。在创建 BufferedInputStream 时，会创建一个内部缓冲区数组。在读取或跳过流中的字节时，可根据需要从包含的输入流再次填充该内部缓冲区，一次填充多个字节。mark 操作记录输入流中的某个点，reset 操作使得在从包含的输入流中获取新字节之前，再次读取自最后一次 mark 操作后读取的所有字节。

### public class BufferedOutputStream extends FilterOutputStream

该类实现缓冲的输出流。通过设置这种输出流，应用程序就可以将各个字节写入底层输出流中，而不必针对每次字节写入调用底层系统。

使用字节缓冲流实现图片的复制
```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class CopyImg {
    public static void main(String[] args) throws IOException {
        BufferedInputStream bfin=new BufferedInputStream(new FileInputStream("C:\\Users\\Administrator\\Desktop\\Img.jpg"));
        BufferedOutputStream bfout=new BufferedOutputStream(new FileOutputStream("C:\\Users\\Administrator\\Desktop\\ImgCopybuff.jpg"));
        int len=0;
        byte[] buff=new byte[1024];
        while((len=bfin.read(buff))!=-1) {
            bfout.write(buff, 0, len);
        }
        bfin.close();
        bfout.close();
    }
}
```

## 5.转换流：InputStreamReader和OutputStreamWriter
InputStreamReader和OutputStreamWriter是字符和字节的桥梁，也可称之为字符转换流。原理：字节流+编码。

FileReader和FileWriter作为子类，仅作为操作字符文件的便捷类存在。当操作的字符文件，使用的是默认编码表时可以不用父类，而直接使用子类完成操作，简化代码。
一旦要指定其他编码时，不能使用子类，必须使用字符转换流。

### public class InputStreamReader extends Reader

1. InputStreamReader 是字节流通向字符流的桥梁：它使用指定的 charset 读取字节并将其解码为字符。它使用的字符集可以由名称指定或显式给定，或者可以接受平台默认的字符集。
2. 每次调用 InputStreamReader 中的一个 read() 方法都会导致从底层输入流读取一个或多个字节。要启用从字节到字符的有效转换，可以提前从底层流读取更多的字节，使其超过满足当前读取操作所需的字节。
3. 为了达到最高效率，可以考虑在 BufferedReader 内包装 InputStreamReader。例如：  
<font color=red>BufferedReader in = new BufferedReader(new InputStreamReader(System.in))；//重要</font>
```java
import java.io.BufferedReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;

/**
 * 使用标准输入流，读取键盘录入的数据，存储到项目根目录下的a.txt中
 * 将字节输入流转换成字符输入流，InputStreamReader
 */
public class InputStreamReaderDemo {
    public static void main(String[] args) throws IOException {
        //创建输入流对象
        BufferedReader r=new BufferedReader(new InputStreamReader(System.in));
        //创建输出流对象
        FileWriter fw=new FileWriter("a.txt");
        //读写数据
        char[] chs=new char[1024];
        int len;
        while((len=r.read(chs))!=-1) {
            fw.write(chs,0,len);
            fw.flush();
        }
        //释放资源
        r.close();
        fw.close();

    }

    public static void method2() throws IOException {
        //创建输入流对象
        InputStream is=System.in;
        Reader r=new InputStreamReader(is);
        //创建输出流对象
        FileWriter fw=new FileWriter("a.txt");

        //读写数据
        char[] chs=new char[1024];
        int len;
        while((len=r.read(chs))!=-1) {
            fw.write(chs,0,len);
            fw.flush();
        }
        //释放资源
        is.close();
        fw.close();
    }

    public static void method1() throws IOException {
        //创建输入流对象
        InputStream is=System.in;
        //创建输出流对象
        FileWriter fw=new FileWriter("a.txt");
        
        //读写数据
        byte[] bys=new byte[1024];
        int len;
        while((len=is.read(bys))!=-1) {
            fw.write(new String(bys,0,len));
            fw.flush();
        }
        //释放资源
        is.close();
        fw.close();
    }    
}
```
### public class OutputStreamWriter extends Writer

1. OutputStreamWriter 是字符流通向字节流的桥梁：可使用指定的 charset 将要写入流中的字符编码成字节。它使用的字符集可以由名称指定或显式给定，否则将接受平台默认的字符集。
2. 每次调用 write() 方法都会导致在给定字符（或字符集）上调用编码转换器。在写入底层输出流之前，得到的这些字节将在缓冲区中累积。可以指定此缓冲区的大小，不过，默认的缓冲区对多数用途来说已足够大。注意，传递给 write() 方法的字符没有缓冲。
3. 为了获得最高效率，可考虑将 OutputStreamWriter 包装到 BufferedWriter 中，以避免频繁调用转换器。例如：  
<font color=red>Writer out = new BufferedWriter(new OutputStreamWriter(System.out));//重要</font>
利用标准输出流将文本输出到命令行  
```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.Writer;

/**
 * 读取项目目录下的文件copy.java,并输出到命令行
 * 由于标准输出流是字节输出流，所以只能输出字节或者字节数组，但是我们读取到的数据是字符串，如果想进行输出，
 * 还需要转换成字节数组(method1)。
 * 要想通过标准输出流输出字符串，把标准输出流转换成一种字符输出流即可(method2)。
 */
public class OutputStreamWriterDemo {
    public static void main(String[] args) throws IOException {
        //创建输入流
        BufferedReader br=new BufferedReader(new FileReader("copy.java"));
        //创建输出流
        BufferedWriter bw=new BufferedWriter(new OutputStreamWriter(System.out));
        String line;//用于接收读到的数据
        while((line=br.readLine())!=null) {
            bw.write(line);
            bw.write("\r\n");
        }
        br.close();
        bw.close();
    }

    public static void method2() throws FileNotFoundException, IOException {
        //创建输入流
        BufferedReader br=new BufferedReader(new FileReader("copy.java"));
        //创建输出流
        //OutputStream os=System.out;
        Writer w=new OutputStreamWriter(System.out);//多态，父类引用指向子类对象
        String line;//用于接收读到的数据
        while((line=br.readLine())!=null) {
            w.write(line);
            w.write("\r\n");
        }
        br.close();
        w.close();
    }

    public static void method1() throws FileNotFoundException, IOException {
        //创建输入流
        BufferedReader br=new BufferedReader(new FileReader("copy.java"));
        //创建输出流
        OutputStream os=System.out;
        String line;//用于接收读到的数据
        while((line=br.readLine())!=null) {
            os.write(line.getBytes());
            os.write("\r\n".getBytes());
        }
        br.close();
        os.close();
    }
}
```
```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.UnsupportedEncodingException;

public class TransStreamDemo {
    public static void main(String[] args) throws IOException {
        writeCN();
        readCN();
    }

    public static void readCN() throws UnsupportedEncodingException, FileNotFoundException, IOException {
        //InputStreamReader将字节数组使用指定的编码表解码成文字
        InputStreamReader isr=new InputStreamReader(new FileInputStream("temp.txt"),"utf-8");
        char[] buff=new char[1024];
        int len=isr.read(buff);
        System.out.println(new String(buff,0,len));
        isr.close();
    }

    public static void writeCN() throws UnsupportedEncodingException, FileNotFoundException, IOException {
        //OutputStreamWriter将字符串按照指定的编码表转成字节，再使用字符流将这些字节写出去
        OutputStreamWriter osw=new OutputStreamWriter(new FileOutputStream("temp.txt"),"utf-8");//本身是字符流，传入字节流
        osw.write("你好");
        osw.close();
    }
}
```
## 6.IO流使用规律总结

### 6.1明确要操作的数据是数据源还是数据目的(要读还是要写)
- 源:   InputStream　　Reader  
- 目的:OutputStream　　Writer

### 6.2明确要操作的设备上的数据是字节还是文本
- 源：  
>字节：InputStream  
文本：Reader
- 目的：  
>字节：OutputStream  
文本：Writer

### 6.3明确数据所在的具体设备
- 源设备：
>硬盘：文件 File开头  
内存：数组，字符串  
键盘：System.in  
网络：Socket  
- 目的设备：  
>硬盘：文件 File开头  
内存：数组，字符串  
屏幕：System.out  
网络：Socket  

### 6.4明确是否需要额外功能
- 需要转换——转换流 InputStreamReader OutputStreamWriter
- 需要高效——缓冲流Bufferedxxx
- 多个源——序列流 SequenceInputStream
- 对象序列化——ObjectInputStream,ObjectOutputStream
- 保证数据的输出形式——打印流PrintStream Printwriter
- 操作基本数据，保证字节原样性——DataOutputStream,DataInputStream