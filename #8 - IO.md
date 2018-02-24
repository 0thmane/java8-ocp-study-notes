# Understanding Files and Directories
WHat can java do outside the scope of managing objects anb attributes in memory? How can they save data so that information is not lost every time the program is terminated? The use files of course! You can design a program that writes the current state of an application to a file every time the application is closed and then reloads the data when the application executed the next time. IN this manner the information is preserved between program executions.

We begin this Chapter by describing what a file is and what a directory is within a file system. We then present the ```java.io.File``` class and demonstrate how to use it to read and write file information.

## Conceptualizing the File System
A *file* is record within a file system that stores user and system data. Files are organized using directories. A *directory* is a record within a file system that contains files as well as other directories. For simplicity, we often refer to a directory reference as a file record throughout this chapter , since it is stored in the file system with a unique name and with attributes similar to a file.Finally a *root directory* is the topmost directory in the file system, from which all files and directories inherit. In Windows, it is denoted with a drive name such as c:\ while on linux it is denoted with a single forward slash /. The *file system* is in charge of reading and writing data within a computer. Different operative systems use different file systems to manage their data. For example, Windows-based systems use a different file system than Unix-based ones. Java includes numerous methods, which automatically connect to the local file system for you, allowing you to perform the same operations across multiple file systems.

A *path* is a string representation of a file or directpry within a file system. Each file system defines own path separator character that is used between directory entries. In most file systems, the value of the left of the separator is the parent of the value of the right of the separator. For example, ```/user/home/zoo.txt``` means that zoo.txt is inside the home directory, with the home directory inside the user directory. You will see that paths can be absolute or relative later in the Chapter. In the figure below we show how a durectory and a file system is organized in hierarchical manner. In this example, directories are rectangles and files as ovals. Directories can be empty, as shown with tyhe c:\zoo and c:\app\employees directories.

![Directory and File Hierarchy](img/directoryHierarchy.png)

## Introducing the File Class
The first class the we will discuss is one of the most commonly used in the java.io API, the java.io.File class. The File class is used to read information about existing files and directories, list the contents of a directory, and create/delete files and directories.
An instance of a File class represents the pathname of a particular file or directory on the file system. The File class cannot read and write data within a file, altough it can be passed as reference to many stream classes to read or write data. 

:yin_yang: one common mistake new Java developers make is forgetting that File class can be used to represent directoris as well as files.

## Creating a File Object
A File object often initilizes with String containing either an absolute or relative path to the file or directory within the file system. Different operative systems vary in their format of path names. For example, Unix based systems use the forward slash / for paths, whereas Windows-based systems use the backslash \ character. That said, many programming languages and file systems support both types of slashes when writing path statements. For convenience, Java offers two options to retrieve the local separator character: a system property and a static variable defined in the File class. Both of the following examples will output the separator character:

```
System.out.println(System.getProperty("file.separator"));

System.out.println(java.io.File.separator);
```

The following code creates a File object and determines if the path it referenes exists within the file system:

```java
import java.io.File;

public class FileSample{
  public static void main(String[] args){
    File file = new File("/home/smith/data/zoo.txt");
    System.out.println(file.exists());
  }
}

```

This example uses the absolute path to a file and outputs true or false, depending on whther the file exists or not.The most common File construcot we will use takes a single String as an argument representing a relative of absolute path. The are other constructors such as the one that joins an exising File path with a relative child path, as shown in the following example:

```
File parent = new File("/home/smith");
File child = new File(aprent, "data/zoo.txt");
```

In this example we create a path that is equivalent to our previous example, using a combination of a child and a parent path. If the parent object happens to be null, then it would be skipped and the method would revert to our Sstring constructor.

## Working with a File Object
The File class contains numerous useful methods for interacting with files and directories within the file system. We represent the most commonly used one in the table below. Although this table may seem loke alot to learn, many of them are self explanatory:

**Method Name**    | **Description**
-------------------|---------------
exists()           | Returns true if the file or directory exists
getName()          | Returns the name of the file or directory denoted by this path
getAbsolutePath()  | Returns the absolute pathname string of this path
isDirectory()      | Returns true if the file denoted by this path is directory.
isFile()           | Returns true if the file denoted by this path is a file
length()           | Returns the number of bytes in the file. For performance reasons, the file system may allocate more bytes on disk than the file actually uses
lastModified()     | Returns the number of milliseconds since the epoch when the filke was last modified
delete()           | Deletes the file or directoryu. If this pathname denotes a directory, then the directory must be empty in order to be deleted
reanmeTo(File)     | Renames the file denoted by this path
mkdir()            | Creates the directory named by this path
mkdirs()           | Creates the directory named by this path
getParent()        | Returns the abstract pathname of this path including any nonexisitent parent directories.
lastFiles()        | Returns a File[] array denoting the files in the directory

The following is a sample program that given a file path outputs information about the file or directory, such as whther it exists, what files are contained within it and so forth:

```java
import hava.io.File;

public class ReadFileInformation{
  public static void main(String[] args){
    File file = new File("C:\\data\\zoo.txt");
    System.out.println("File exists: " + file.exists());
    
    if(file.exists()){
      System.out.println("Absilute Path: " + file.getAbsolutePath());
      System.out.println("Is Directory: " + file.isDirectory());
      System.out.println("Parent Path: " + file.getParent());
      
      if(file.isFile()){
        System.out.println("File Size: " + file.length());
        System.out.println("File Last Modified: " + file.lastModified());
      }else{
        for(File subfile : file.listFiles()){
          System.out.println("\t" + subfile.getName());
        }
      }
    }
  }
}

```
If the path provided did not point to a file, it would output the following

```
File Exists: false
```

If the path provided pointed to a valid faile, it would putput something similar ro the following:

```java
File Exists: true
Absolute Path: C:\data\zoo.txt
Parent Path: C:\data
Is Directory: false
File Size: 12382
File lastModified: 104215000000
```

Finally, if the path provided pointed to a valid directory, such as C:\data, it would output something similar to the following:

```java
File Exists: true
Absolute Path: C:\data
Parent Path: C:\

Is Directory
  employee.txt
  zoo.txt
  zoo-backup.txt

```


# Introducing Streams
In this section, we present the concept of streams in Java and show how they are used for input/output processing. I/O refers to the natura of how data is accessed, either by reading the data fro the resource (input), or writing the data to a resource (output). This section will focus on the common ways that data using files accessed by streams. Notice that the I/O streams that we discuss in this chapter are data streams and completely unrelated to the new Stream API that you saw in Chapter 4 "Functional Programming".

## Stream Fundamentals
The contents of a file may be accessed or written via a stream, which is a list of data elements presented sequentially. Streams should be conceptually thought of as long, nerly never ending stream of water with data presented one "wave" at a time.For example, it may be helful to visualize a stream as being so large that all of the data contained in it could not possibly fit into memory. A 1 terabyte file could not be stored entirely in memory by most computer systems. The file can still be read and written by a program with very little memory, since the streams allows the application to focus on only a small portion of the overall stream at any given time. A real world scenario example is that writing a file one byte at a time is time consuming and slow in practice because the round-trip between the Java application and the file system is relatively expensive. By using a ```BufferedOutputStream``` the Java application can write a large chunk of bytes at a time, reducing the round-trips and drastically improving performance.

In fact you have been using streams since your first "Hello World" program! Java provides three built-in streams, System.in, System.err and System.out the last of which we have been using to output data to the screen throughtout this book. 

## Stream Nomenclature
The ```java.io``` API provides numerous classes for creating, accessing, and manipulating streams - so many that it tends to overwhelm most new Java developers/programmer. Stay calm! we will review the major differences between each stream class and show you how to distinguish between them. Even if you do come across a particular stream that you don't recognize, often the name of the stream gives you enough info to understand exactly what it does.

The goal in this section is to familiarize you with common terminology and naming convention used with streams. Don't worry if you don't recognize the particular stream class names used in this section or their function; we will covering in detail in the next part of the chapter.

## Byte Streams vs. Character Streams
The java.io API defines two sets of classes for reading and writing streams; those with Stream in their names and those with Reader/Writer in their name. For example, the java.io API defines both ```FileInputStream``` class as well as a ```FileReader``` class, both of which defines a Stream that reads a file. The difference between the two classes is based on how the stream is read or written. 

Differences between Stream adn Reader/Writers:

1- The stream classes are used for inputting and outputting **all types of binary or byte data**.
2- The reader and writer classes are used for inputting and outputting **only character and String data**.

It is important to remember that even though readers/writers do not contain the word Stream in their class name, they are still in fact streams! The use of Reader/Writer in the name is just to distinguish them from byte streams. Throughout the chapter, we will often refer to Reader/Writer classes as streams, since conceptually they are streams.

**Why use Character Streams?** Since they byte stream classes can be used to input and output all types of binary data, including strings, it naturally follows that you can write all of your code to use the byte stream classes, never really needing the character stream classes. There are advantages, though, to using reader/writer classes, as they are specifically focused on managing character and string data. For example, you can use a Writer class to output a String value to a file without having to worry about the underlying byte encoding of the file.  For this reason the character stream classes are sometimes referred to as convenience classes for working with thext data.

The java.io API is structured shuch that all of the stream classes have the word ```InputStream``` or ```OutputStream``` in their name, while all Reader/Writer classes have either Reader or Writer in their name. Pay close attention to the name of the java.io class on the exam, as decoding it often gives you context clues as to what the class does. For example, without needing to look it up, it should be clear that ```FileReader``` is a class that reads data from a file as characters or strings. Furthermore, ```ObjectOutputStream``` sounds like a class that writes object data to a byte stream.  

## Input and Output
Most input stream classes have corresponding Output class and vice versa. For example, the ```FileOutputStream``` class writes data that can be read by a ```FileInputStream```. If you understand the features of a particular Inout or Output stream class, you should naturally know what its complementary class does. It follows, then, that most Reader classes have corresponding Writer class. For example, the FileWriter class writes data that can be read by a FileReader.
There are exceptions to this rule. For the exam, you should know that PrinterWriter has no accompanying PrinterReader class. Likewise, the PrintStream class has no corresponding InputStream class. We will discuss these classes later in the chapter.

## Low-Level vs. High-Level Streams
Another way you can familiarize yourself with java.io API is by segmenting streams into low-level and high-level streams. A *low-level stream* connects directly with the source of the data, such as a file, an array, or a String. Low-level streams process the raw data or resources and are accessed in a direct and unfiltered manner. For example, a ```FileInputStream``` is a class that reads file data one byte at a time. 

Alternatively, a *high-level stream* is built on top of another stream using wrapping. *Wrapping* is the process by which an instance is passed to the constructor of another class and operations on the resulting instance are filtered and applied to the original instance. For example, take a look at the FileWriter and BufferedWriter objects in the following sample code. 

```java
try(Buffered bufferedReader = new BufferedReader(new FileReader("zoo-data.txt")){
  System.out.println(bufferedReader.readLine());
}
```
In this example, FileReader is the low-level stream reader, whereas BufferReader is the high-level stream that takes a FileReader as input. Many operations on the high-level stream pass through as operations to the underlying low-level stream, such as read() or close(). Other operations override or add new functionality to the low-level stream methods. The high level stream adds new methods, such as readLine(), as well as performance enhancements for reading and filtering the low-level data.

High-level streams can take other high-level streams as input. For example, although the following code might seem a litte odd at first, the style of wrapping a stream is quite common in practice:

```java
try (ObjectInoutStream objectStream = new ObjectInputStream(
                                              new BufferedInputStream(
                                                new FileInputStream("zoo-data.txt")))){
    System.out.println(objectStream.readObject());                                                
}
```

In this example, FileInputStream is the low level stream that interacts directly with the file, which is wrapped by a high level BufferedInputStream to improve performance. Finally, the entire object is wrapped by a high level ObjectInputStream, which allows us to filter the data as Java objects. 

For the exam the only low-level stream classes you need to be familiar with are the one that operates on files. The rest of the non-abstract stream classes are all high level streams.

***Real World Scenario*** **Use Buffered Streams When Working with Files**. As briefly mentioned, Buffered classes read or write data in groups, rather than a single byte or character at a time. The performance gain from using a Buffered class to access a low-level file stream cannot be overstated. Unless you are doing something very specilized in your application, you should always wrap a file stram with a Buffered class in practice.

The reason that Buffered streams tend to perform so well in practice is that file systems are geared for sequencial disk access. The more sequencial bytes you read at a time, the fewer round-trips between the Java process and the file-system, improving the access of your application. For example, accessing 16 sequential bytes is a lot faster than accessing 16 bytes spread accross the hard drive.


## Stream Base Classes
The java.io library defines abstract classes that are the parents of all stream classes defined within the API: InputStream, OutputStream, Reader and Writer. For convenience, the authors of the Java API include the name of the abstract parent class as the suffix of the child class. For example, ```ObjectInputStream``` ends with ```InputStream``` meaning it has InputStream as an inherited parent class. Although most stream classes in java.io follow this pattern, PrintStream, which is an OutputStream, does not. The constructor of high-level streams often take a reference to the abstract class. For example. BufferedWriter takes Writer object as input, which allows it to take any subclass or Writer. The advantage of using a reference to the abstract parent class in the class constructor should be apparant in the previous high-level stream example. With high level-streams, a class may be wrapped multiple times. Furthermore, developers may define their own stream subclass that performs custom filtering. By using the abstract parent class as input, the high level stream classes can be used much more often without concern for the particular underlying stream subclass.

Once common area where the exam likes to play tricks on you is mixing and macthing stream classes that are not compatible with each other. For example, take a look at each of the following examples and see if you can determine why they do not compile. 

```java
new BufferedInputStream(new FileReader("zoo-data.txt"));      //DOES NOT COMPILE

new BufferedWriter(new FileOutputStream("zoo-data.txt"));     //DOES NOT COMPILE

new ObjectInputStream(new FileOutputReader("zoo-data.txt"));  //DOES NOT COMPILE

new BufferedInputStream(new InputStream());                   //DOES NOT COMPILE

```

The first two examples do not compile because they mix Reader/Writer classes with InputStream/OutputStream classes, respectively. The third example does not compile because we are mixing an OutputStream with an InputStream. Although it is possible to read data from and InputStream abd write it to an OutputStream, wrapping the stream is not the way to do so. As you shall see later in this chapter the data must be copied over, often interatively. Finally the last example does not compile because InoutStream is an abstract class, and therefore you cannot instantiate an instance of it.

## Decoding Java I/O Class Names
Given that there are so many different java.io stream classes, it is reasonable to think that you might encounter one on the exam whose name you may have forgotten. Luckily, the function of most stream classes can be understood by decoding the name of the class. We summarize these properties in the following list.

### Review of java.io Class Properties
- A class with the word InputStream or OutputStream in its name is used for reading or writing binary data, respectively.
- A class with the word Reader or Writer in its name is used for reading or writing character or string data, respectively.
- Most, but not all, input classes have corresponding output class.
- A low level stream connects directly with the source of the data.
- A high-level stream is built on top of another stream using wrapping.
- A class with Buffered in its name reads or writes data in groups of bytes or characters and often improves performance in sequential file systems.

When wrapping a stream you can mix and match only types that inherits from the same abstract parent stream. The table below describes those java.io streams you should be familiar with for the exam. Note that most of the information about each stream, such as whether it is an input or output stream or whether it accesses data using bytes characters, can be decoded by the name alone.

**Method Name**    | **Low/High Level**   |    **Description**
-------------------|----------------------|--------------------------------------------
InputStream        |  N/A                 | The abstract class all InputStream classes inherit from
OutputStream       |  N/A                 | The abstract class all OutputStream classes inherit from
Reader             |  N/A                 | The abstract class all Reader classes inherit from
Writer             |  N/A                 | The abstract class all Writer classes inherit from
FileInputStream    |  Low                 | Reads file data as bytes
FileOutputStream   |  Low                 | Writes file data as bytes
FileReader         |  Low                 | Reads file data as bytes
FileWriter         |  Low                 | Writes file data as characters
BufferedReader     |  High                | Reads charater data from an exisiting Reader in a buffered manner, which improves efficiency and perfrmance
BufferWriter       |  High                | Writes charater data from an exisiting Writer in a buffered manner, which improves efficiency and perfrmance
ObjectInputStream  |  High                | Deserializes primitive Java data types and graphs of Java objetcs to an existing InputStream
ObjectOutputStream |  High                | Serializes primitive Java data types and graphs of Java objetcs to an existing OutputStream
InputStreamReader  |  High                | Reads character data from existing InputStream
OutputStreamWriter |  High                | Writes character data to an exisiting OutputStream
PrintStream        |  High                | Writes formatted representations of Java objects to a binary stream
PrintWriter        |  High                | Writes formatted representation of Java objects to a text-based output stream

We will discuss these java.io classes in more details including examples in upcoming sections.

## Common Stream Operations
Before we delve into specific stream classes, let's review some common processes when working with streams.

### Closing the Stream
Since streams are considered resources, it is imperative that they be closed after they are used lest they lead to resource leaks. As you saw in chapter 6 "Exceptions and Assertions" you can accomplish this by calling the close() methoid in the finally block or using try-with-resource syntax.

In a file system, failing to close a file properly could leave oit locked by the operative system such that no other process could read/write to it until the program is terminated. Throughout this chapter, we will close stream resources wusing try-with-resources syntax, since this is preferred way of closing resources in Java. 

### Flushing the Stream
When data is written to an OutputStream, the underlying operative system does not necessarily guarantee that the data will make it to the file immediately. In many operating systems, the data may be cached in memory, with a write occuring only after a temporary cache is filled or after some amount of time has passed. If the data is cached in memory and the application terminates unexpectedly, the data would be lost, bacause it was never written to te file system. To address this, Java provides a flush() method, which requests that all accuulated data be written immediately to disk. The flush() method helps reduce the amount of data lost if the application terminates unexpectedly. It is not without cost though. Each time it is used it may cause a noticeable delay in the application, especially for large file. The flush() method should be use intermittently. For small files it should only be called only once.

You do not need to call the flush() method explicitly when you have finished writing to a file, since the close() method will atomatoically do this. in some cases, calling the flush() method intermittently while writing a large file, rather than performing a single large flush when the file is closed, may appear to improve performance by stretcthing the disk access over course of the write process.

### Making the Stream
The InputStream and Reader classes include mark(int) and reset() methods to move the stream back to an earlier position. Before calling either of these methods, you should call the markSupported() method, which returns true only if mark() is supported. Not all java.io input stream classes support this operation, and trying to call mark(int) or reset() on a class that doesn not support these operations will throw an exception at runtime.

Once you've verified that the stream can support these operations, you can call mark(int) with a read-ahead limit value. You can then read as many bytes as you want up to the limit value. If at any point you want to go back to the earlier position where you last called mark(), then you just call reset() and the stream will "revert" to an earlier state. In practice, it's not actually putting the data back into the stream but storing the data that was already read into memory for you to read again. Therefore, you should not call the mark() operation with too large a value as this could take up a lot of memory. Assume that we have an InputStream instance whose next values are ABCD. Consider the following code snippet:

```java
InputStream is = ...
System.out.println((char) in.read());
if(is.markSupported()){
  is.mark(100);
  System.out.print((char) is.read());
  System.out.print((char) is.read());
  is.reset();
}
System.out.print((char) is.read());
System.out.print((char) is.read());
System.out.print((char) is.read());
```

The code snippet will output the following if the mark() operation is supported:

```
ABCBCD
```

It first outputs A before the if statement. Since we are given that the stream supports the mark() operation, it will enter the if statement and read two characters, BC. It then calls the reset() operation, moving our stream back at the state that it was in after the A was read, comes out of the if statement and BC are read again, followed by D.
If the mark operation is not supported it will output this instead, skipping the if statement entirely:

```
ABCD
```

Notice that regardless of whether the mark() operation was supported, we took care to have the stream end at the same position. Finally, if you call reset() after you have passed your mark() read limit, an exception may be thrown at runtime since the marked position may become invalidated. We say "an exception may be thrown" as some implementations may use a buffer to allow extra data to be read before the mark is invalidated  

### Skipping over Data
The InputStream and Reader classes also include ```skip(long)``` method, which as you might expect skips pver a certain number of bytes. It returns a long value, which indicates the number of bytes that were actually skipped. If the return value is zero or negative, such as if the end of the stream was reached, no bytes were skipped. Assume we have an InputStream instance whose next values are TIGERS. Consider the following code snippet:

```java
InputStream is = ...
System.out.println((char) is.read());
is.skip(2);
is.read();
System.out.println((char) is.read());
System.out.println((char) is.read());
```
The code will read one character, T, skip two characters, IG and then read three more characters, ERS, only the last two of which are printed to the user, which results in the following output.

```
TRS
```
You may notice in this example that calling the skip() operation is equivalent to calling read() and discarding the output. For skipping a handful of bytes, there is virtually no difference. On the other hand, for skipping a large number of bytes, skip() will often be faster, because it will use arrays to read the data.

# Working with Streams
Now that we have reviewed the types of streams and their properties it's time to jump in and work with some stream code! Some of the techniques for accessing streams may seem a bit new to you, but as you will see they are very similar among different stream classes.

## The FileInputStream and FileOutputStream Classes
These two are used to read bytes from a file or write bytes to a file, respectively. These classes include constructors that take a File object or a String, representing a path to the file.

The data in a FileInoutStream object is commonly accessed by successive calls to the read() method until a value of -1 is returned, indicating that the end of the stream - in this case the end of the file - has been reached. Although less common, you can choose to stop reading the stream early just by exiting the loop, such as if some search String is found. Note that when reading a single value of a FileInputStream instance, the read() method returns primitive int value rather than a byte value. It does this so that it has an additional value avaialable to be returned, specifically -1, when the end of the file is reached. For compatibility, the FileOutputStream also uses int instead of byte for writing a single byte to a file.

The FileInputStream class also contains overloaded versions of the read() method, which takes a pointer to a byte array where the data is written. The method returns an integer value indicating how many bytes can be read into the byte array. It is also used by Buffered classes to improve performance, as you shall see in the next section.

A FileOutputStream object is accessed by writing successive bytes using the write(int) method. Like the FileInputStream class, the FileOutputStream also cotains overloaded versions of the write() method that allow a byte array to be passed and can be used by Buffered classes. The following code uses FileInputStream and FileOutputStream to copy a file:

```java
import java.io.*;

public class CopyFileSample{
  public static void copy(File source, File destination) throws IOException{
      try(InputStream in = new FileInputStream(source); 
          OutputStream out = new FileOutputStream(destination)){
            int b;
            while((b = in.read()) != -1){
              out.write(b);
            }
      }
  }
  
  public static void main(String[] args) throws IOException{
    File source = new File("Zoo.class");
    File destination = new File("ZooCopy.class");
    copy(source, destination);
  }
}
```

The main method creates two File objects, one for the source file to copy from and one for the destination file to copy to. If the destination file already exists, it will be overridden by this code. Both File objects are created using relative paths, so application would search for the Zoo.class in the current directory to read from, throwing a FileNotFoundException if the file is not found, which is a subclass of an IOException.

The copy() method creates instances of FileInputStream and FileOutputStream, and it proceeds to read the FileInputStream one byte at a time, copying the value to the FileOutputStream as it's read. As soon as the in.read() returns a -1 value, the loop ends. Finally, both streams are closed using the try-with-resources syntax. Note that performance for this code, epecifically for large files, would not be particularly good because the sample does not use any byte arrays.As you shall see in the next section, we can improve the implementation using byte arrays and buffered streams. 

## The BufferedInputStream and BufferedOutputStream Classes
We can enhance our implementation with only a few minor a few minor code changes by wrapping the FileInputStream and FileOutputStream classes that you saw in the previous example, with the BufferedInputStream and BufferedOutputStream classes respectively. Instead of reading the data one byte at a time, we use the underlying ```read(byte[])``` method of BufferedInputStream, which returns the number of bytes read into the provided byte array. The number of bytes read is important for two reasons. First, if the value returned is 0, then we know that we have reached the end of the file and can stop reading from the BufferedInutStream. Second, the last read of the file will likely only partially fill the byte array, since it is unlikely for the file size to be an exact multiple of our buffer array size. For example, if the buffer size is 1024 bytes and the file size 1054 bytes, then the last read will only be 30 bytes. The length value tells us how many of the bytes in the array were actually read from the file. The remaining bytes of the array will be filled with leftover data fro the previous read that should be discarded. The data is written into the BufferedOutputStream using the ```write(byte[],int,int)``` method, which takes as input a byte array, an offset, and a length value, respectively. The offset value is the number of values to skip before writing characters, and it is often set to 0. The length value is the number of characters from the byte array to write.

**Why use the Buffered Classes?** It's quite common to use Buffered classes anytime you are reading or writing data with byte arrays. The Buffered classes contain numerous performance enhancements for managing stream data in memory.

For example, the BufferedInputStream class is capable of retrieving and storing in memory more data than you might request with a single read() call. For successive calls to the read() method with small byte arrays, this would e faster in a wide variety of situations, since the data can be returned directly from memory without going to the file system. 

Here is a modified form of our copy() methid, which uses byte arrays and BufferedStream classes:

```java
import java.io.*;

public class CopyBufferFileSample{
  public static void copy(File source, File destination) throws IOException{
    try (
      InputStream in = new BufferedInputStream(new FileInputStream(source));
      OutputStream out = new BufferedOutputStream( new FileOutputStream(destination))){
      byte[] buffer = new byte[1024];
      int lengthRead;
      while((lengthRead = in.read(buffer)) > 0){
        out.write(buffer, 0, lengthRead);
        out.flush();
      }
    }
  }
}

```

You can see that this sample code that uses byte arrays is very similar to the nonbuffered sample code, although the performance improvement for using both the Buffered classes and byte arrays is an order of magnitude faster in practice. We also added a flush() command in the loop, as previously discussed, to ensure that the written data actually makes it to disk before the next buffer of data is read.

**Buffer Size Tuning** we chose a buffer size of 1024 in this example, as this is appropriate for a wide variety of circumstances, although in practice you might see a better performance with a higher or lower buffer size. This depends of several number of factors like the system block size and the CPU hardware. It is also common to choose a power of 2 for the buffer size, since most underlying hardware is structured with file block and cache sizes that are power of 2. The Buffered classes allow you to specify the number in the constructor and if none is provided they use a default value which is a power of 2. Regardless the buffer size you choose the biggest performance you will see is moving from nonbuffer to buffred file access. Adjusting the buffer size may improve slightly but it is unlikely to have a significant impact.

## The FileReader and BufferedWriter Classes

### Comparing the Two Copy Applications
## The ObjectInputStream and ObjectOutputStream Classes
## The Serializable Interface
## Serializing and Deserializing Objects
## Understanding Object Creation
## The PrintStream and PrintWriter Classes
### print()
### println()
### format() and printf()
## Sample PrintWriter Application
## Review of Stream Classes
## Other Stream Classes

# Interacting with Users
## The Old Way
## The New Way
### reader() and writer()
### format() and printf()
### flush()
### readLine()
### readPassword()

