# 一些基本概念

## 阻塞与非阻塞

指的是等待调用结果返回之前，调用方的状态

阻塞：发出请求等待请求结果时不能进行其他操作

非阻塞：发出请求等待请求结果时可以进行其他操作

## 同步与异步

指的是通信机制的区别，等待调用的结果的

同步：调用后必须等待结果才可以返回

异步：调用后可以不先知道结果直接返回，直到后面收到结果

## 四种排列组合

基于阻塞与非阻塞、同步与异步，IO操作可以分为四类：

- 同步阻塞（告白时一直等着回复，同时不能做其他事）
- 同步非阻塞（告白时一直等着回复，但是等的时候可以做别的事）
- 异步阻塞（告白后回家等消息，但是一直想着结果）
- 异步非阻塞（告白时回家等消息，同时可以做很多事）

## 网络编程演进史

> 始于BIO，陷于NIO，终于AIO

同步阻塞式IO(BIO Block IO)->同步阻塞与非阻塞模式(NIO No/New  Block IO jdk1.4)->异步非阻塞I/O模型(AIO Asynchronous IO jdk1.7)

![在这里插入图片描述](https://img-blog.csdnimg.cn/a476f2e0d559432ba1068aa0543f451f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

# 网络层的解析与协议

## URL的解析

![在这里插入图片描述](https://img-blog.csdnimg.cn/bbfde7b548e14e619065612570c9ab62.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 域名的解析

域名的解析是从右向左解析，下面以`www.google.com.root`为例看一下域名的层级，根域名都是`.root`默认会被省略

![在这里插入图片描述](https://img-blog.csdnimg.cn/41dd039eb9204e5daacc609112d483b8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 域名DNS查询的两种方式

DNS有一个分布式映射数据库，查询DNS分为递归查询和迭代查询，查询时经过的域名服务器，只要IP地址被查询出来了就会进行缓存，方便下次查询。

递归查询：DNS客户端查询根域名，如果根域名知道IP地址就直接返回，如果不知道就查询顶级域名，如果....

![在这里插入图片描述](https://img-blog.csdnimg.cn/918a0ea98be340aa9d07c52ff9ef83f4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

迭代查询：如果根域名知道I地址就直接返回，如果不知道就把可以查询的顶级域名返回，DNS客户端去顶级域名查询，如果知道了就直接返回，如果不知道就将二级域名返回...

![在这里插入图片描述](https://img-blog.csdnimg.cn/5586563a47f04ee1b559178385ccdbf9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 网络协议快速扫盲

我们其实是把网络页面信息转化为电信号中的0和1，把它通过光缆传给另一方，另一方接收到之后将电信号层层解析并加载到浏览器中。

将网络传输分层之后，每一层只需要依赖（适配于）下一层就可以，需要担心的事情就减少了很多，当做了改动后不用担心其他层的适配，只需要担心一层即可

| 分层   | 介绍                         | 常见协议      | 向上传输的依赖 |
| ------ | ---------------------------- | ------------- | -------------- |
| 应用层 | 用户能接触的应用             | HTTP FTP SMTP | 应用程序       |
| 传输层 | 端口与端口之间的连接         | TCP UDP       | 端口           |
| 网络层 | 主机与主机之间的联系         | IP            | IP地址         |
| 链路层 | 网卡和网卡的传输             | Ethernet      | mac地址        |
| 实体层 | 将电信号通过物理连接进行传输 | 电信号        | 电信号         |

## 网络各层数据包格式

| 分层   | 数据包格式                                                 |
| ------ | ---------------------------------------------------------- |
| 应用层 | 将TCP/UDP数据进行划分：应用层数据等内容                    |
| 传输层 | 将IP数据进行划分：TCP/UDP标头+TCP/UDP数据                  |
| 网络层 | 将Ethernet数据进行划分：IP标头+IP数据                      |
| 链路层 | 帧：1500(Ethernet数据)+18(Ethernet标头保存mac地址等)个字节 |

![在这里插入图片描述](https://img-blog.csdnimg.cn/e42eb5fb3b894d3faee29a84085001a7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)



# 网络编程基础知识

## 网络编程的本质

网络编程的本质是进程间的通信，其实就是数据的输入输出。因此我们要了解输入输出模型，数据从数据源输入到应用进程就是输入流，反之是输出流。数据源可以是文件、字符串、对象、Socket等等

![在这里插入图片描述](https://img-blog.csdnimg.cn/8d78b0225df64d61b65862846ed94e4d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## java.io的流

字节流操作单位是字节，即8bit，人类常常一个个字符读，为了减少额外转化的努力，Java帮我们做了这类工作

![在这里插入图片描述](https://img-blog.csdnimg.cn/99be093d07be4366a1d24b4ef09e34bf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

### 字符流介绍

先看一下常见的字符流吧：

![在这里插入图片描述](https://img-blog.csdnimg.cn/5fd58be3661e4d759632c9578a93f6f4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

提供了额外功能的Reader和Writer，需要建立在基本的Reader和Writer的基础上，InputStreamReader是一个连接的桥梁，将字节流转化为字符流

![在这里插入图片描述](https://img-blog.csdnimg.cn/c537595002e34dbfb7c65e5611d95a60.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

### 字节流介绍

康康最基本的字节流对象：

![在这里插入图片描述](https://img-blog.csdnimg.cn/7896bba730d843afa295c5dba82e6f3d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

在基础的字节流的基础上添加一些功能的字节流对象，DataInputStream或帮助我们直接转化为Java数据类型：

![在这里插入图片描述](https://img-blog.csdnimg.cn/7faec5ffce434828a4df259864e30d25.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 流中存在的装饰器模式

不管是在字符流还是字节流，我们都有一些高级的对象，但是这些对象是基于一些基本的对象实现的，我们有基本对象的功能，同时还提供了更好的功能。例如BufferedInputStream提供了缓存区，这就是装饰

![在这里插入图片描述](https://img-blog.csdnimg.cn/ef9e59a7e3f04415b359d858a3bbbcf7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## Socket概述

socket也是一种数据源，绑定了Socket就可以传输数据流，那socket到底是怎么发送数据呢？其实是需要网卡此类硬件传输数据，首先应用会创建一个socket，其次将这个ip地址、端口、socket绑定到网卡的驱动程序，然后发信息就先发给socket即可，socket发给网卡驱动程序，网卡从硬件层面将信息发送到

![在这里插入图片描述](https://img-blog.csdnimg.cn/8f2c647e4b4e486a81668f6b7ae38cf1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

同理，接受数据的时候也是类似，网卡收集到远程网卡传来数据，就会发给socket，socket将信息发给应用进程

![在这里插入图片描述](https://img-blog.csdnimg.cn/9dd43117541e436c8a2a792fbb8cf052.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 网络通信的线程池

如果为每个请求创建一个线程再回收的话，会有浪费，同时由于操作系统的限制，我们也不能无止境的创建线程。Java提供了很多实现线程池的工具，通过实现ExecutorService接口，提供了两种不同类型的任务，没有返回值就是Runnable，需要返回值就用Callable

最终，我们从线程池中得到的是Future对象，代表任务最终完成的状态，例如isDone()表示现在任务是否已完成，完成了可以使用get()方法得到结果

![image-20210911222746341](C:\Users\forev\AppData\Roaming\Typora\typora-user-images\image-20210911222746341.png)

Executors提供了许多静态方法帮我们创建线程池

![在这里插入图片描述](https://img-blog.csdnimg.cn/2b91edbb3d8144bebf922718d8392835.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

# BIO阻塞模型

 ## socket与serversocket

socket是客户端的，serversocket是服务端的，其连接过程通常如下

![在这里插入图片描述](https://img-blog.csdnimg.cn/856da863b25c4ea58fae96ffe1459404.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## BIO阻塞模型

有一个线程Acceptor接受其他线程的请求，创建一个新线程用来连接client，再来一个client同理 

![在这里插入图片描述](https://img-blog.csdnimg.cn/7404d88efd2f41b9b8618e25c8f1aa16.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 伪异步IO编程模型（线程池）

使用线程池进行优化，否则线程太多太浪费了，如果有可用的线程就使用，没有就等待，这样优化后，系统的可靠性和可伸缩性就有了很大的提升

![在这里插入图片描述](https://img-blog.csdnimg.cn/8b22185961944b69a8b34378ab5c689a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

# NIO非阻塞模型

## NIO概述

ServerSocket的appcet是阻塞的，InputStream的read和OutStream的write都是阻塞的，同时无法在同一个线程处理多个Stream IO，即一个用户阻塞整个线程，在BIO中我们可以使用多线程，然后使用了线程池进行优化。我们可以使用一种非阻塞的形式来处理数据的输入和输出，即NIO。

NIO可以理解为new或NoblockingIO，我们不再使用流这个东西，而是使用Channel(通道)替代Stream，流是有方向的单向的，但Channel是双向的；流的读写是阻塞的，Channel可以阻塞也可以非阻塞。

NIO还提供了Selector，一个Selector可以监控多条Channel，比如我们想从非阻塞的Channel读取数据，但是要用读的数据的时候我们不知道Channel是否读完了，可以使用Selector。

同时，NIO可以在一个线程中处理多个Channel I/O。多线程不一定会提高效率，当线程数太多会造成上下文交换压力，此外创建、销毁一个线程还会浪费系统资源。

![在这里插入图片描述](https://img-blog.csdnimg.cn/33fea8ceaa83429daf6efec3bf057244.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## Buffer简析

我们使用Channel读写数据，其实是用的Buffer，针对一个Channel操作，离不开他的Buffer操作。Channel支持双向操作，可读可写，所以Buffer也可以双向操作，同时buffer是有大小的

- 使用flip()方法将buffer从写模式改为读模式
- clear()方法将buffer从读模式改为写模式（实际上是将position、limit指针回归原位）
- compact()方法会把buffer读后修改成写模式剩下的数据放到最上面，position紧接着在它下面，同时写是从position开始写，不会覆盖原来没读完的数据，下次flip()后一读就是最开始的

> 关于动态过程，大家可以下载这个原视频[null](null)

## Channel简析

Channel通过Buffer进行操作，不同Channel也可以直接传输数据，简单介绍几个Channel类

![在这里插入图片描述](https://img-blog.csdnimg.cn/e4d52e53b306485082f9f1eb8c633fba.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 多方法拷贝文件

- 不使用任何缓冲区，一个个字节来写
- 使用缓冲区，一次读一个缓冲区
- Channel用它的buffer操作
- 两个Channel直接传输数据

> 测试小文件(179 KB)耗时：1783、2、13、1
>
> 测试大文件(36.2 MB)耗时：

```java
interface FileCopyRunner {
    void copyFile(File source,File target);
}
public class FileCopyDemo {

    private static final int ROUNDS=5;

    private static void bencgmark(FileCopyRunner fileCopyRunner,File source,File target) {
        long elapsed=0L;
        for (int i=0;i<ROUNDS;i++) {
            long startTime = System.currentTimeMillis();
            fileCopyRunner.copyFile(source, target);
            elapsed += System.currentTimeMillis()-startTime;
            target.delete();
        }
        System.out.println(fileCopyRunner+":"+elapsed/ROUNDS);
    }

    private static void close(Closeable closeable) {
        if (closeable != null) {
            try {
                closeable.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        //不使用任何缓冲区，一个个字节来写
        FileCopyRunner noBufferStreamCopy = new FileCopyRunner() {
            @Override
            public void copyFile(File source, File target) {
                InputStream fin = null;
                OutputStream fout = null;
                try {
                    fin = new FileInputStream(source);
                    fout = new FileOutputStream(target);
                    int read;
                    while ((read = fin.read())!=-1) {
                        fout.write(read);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    close(fin);
                    close(fout);
                }
            }
        };
        //使用缓冲区，一次读一个缓冲区
        FileCopyRunner bufferedStreamCopy = new FileCopyRunner() {
            @Override
            public void copyFile(File source, File target) {
                BufferedInputStream fin = null;
                BufferedOutputStream fout = null;
                try {
                    fin = new BufferedInputStream(
                            new FileInputStream(source)
                    );
                    fout = new BufferedOutputStream(
                            new FileOutputStream(target)
                    );
                    byte[] buffer = new byte[1024];
                    int result;
                    while ((result = fin.read(buffer))!=-1) {
                        fout.write(buffer,0,result);
                        fout.flush();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    close(fin);
                    close(fout);
                }
            }
        };
        //Channel用它的buffer操作
        FileCopyRunner nioBufferCopy = new FileCopyRunner() {
            @Override
            public void copyFile(File source, File target) {
                FileChannel fin = null;
                FileChannel fout = null;
                try {
                    fin = new FileInputStream(source).getChannel();
                    fout = new FileOutputStream(target).getChannel();
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    while ((fin.read(buffer)!=-1)) {
                        buffer.flip();
                        //全部读完
                        while (buffer.hasRemaining()) {
                            fout.write(buffer);
                        }
                        buffer.clear();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    close(fin);
                    close(fout);
                }
            }
        };
        //两个Channel直接传输数据
        FileCopyRunner nioTransferCopy = new FileCopyRunner() {
            @Override
            public void copyFile(File source, File target) {
                FileChannel fin = null;
                FileChannel fout = null;
                try {
                    fin = new FileInputStream(source).getChannel();
                    fout = new FileOutputStream(target).getChannel();
                    long transferred=0L;//记录一共拷贝了多少字节
                    long size = fin.size();
                    while (transferred != size) {
                        //从哪开始、传输多少、传到哪里
                        transferred += fin.transferTo(0, size, fout);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    close(fin);
                    close(fout);
                }
            }
        };
        File smallFile = new File("E:\\IdeaProjects\\EasyIO\\resources\\smallFile.jpg");
        File smallFileCopy = new File("E:\\IdeaProjects\\EasyIO\\resources\\smallFileCopy.jpg");
        bencgmark(noBufferStreamCopy,smallFile,smallFileCopy);
        bencgmark(bufferedStreamCopy,smallFile,smallFileCopy);
        bencgmark(nioBufferCopy,smallFile,smallFileCopy);
        bencgmark(nioTransferCopy,smallFile,smallFileCopy);
    }
}
```

## Selector简析

监控多个通道的状态，需要我们将Channel注册到Selector中，对于不同的类型，我么可以有一套逻辑

![在这里插入图片描述](https://img-blog.csdnimg.cn/5b53c4be14b348638b11b32b060ef8ec.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

在Selector的常用方法如下：

- innterestOps()：注册的状态们
- readyOps()：有哪些状态是Channel准备好的，可操作的状态
- channel()：返回Channel对象
- selector()：返回Seletor对象
- attachment：每一个Channel对象添加一个Object对象，可以是任意辅助对象

![在这里插入图片描述](https://img-blog.csdnimg.cn/414a3565321d45bc90c7167367b22379.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## NIO优化聊天室

![在这里插入图片描述](https://img-blog.csdnimg.cn/50abfba54608482a985e5eb557061ad9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

# AIO异步通信模型

## AIO异步通信模型

阻塞式

![在这里插入图片描述](https://img-blog.csdnimg.cn/162de28b62fc4eae9a7712cc4e8411f1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

非阻塞式，轮询看是否完成

![在这里插入图片描述](https://img-blog.csdnimg.cn/67b593a87fe84b2bbd6dd9fcfec12f30.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

NIO的形式是IO多路复用

![在这里插入图片描述](https://img-blog.csdnimg.cn/2da34c0deb3749ea952714daa480eb13.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)



前面的都是同步的，这里是异步的，等执行完了就来传递一个信号

![在这里插入图片描述](https://img-blog.csdnimg.cn/6dc67a05ff524591a5dd8eb45da94dcf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 异步调用机制

主要使用`AsynchronousServerSocketChannel`和``这两个类

方式一：通过Future对象（对未来某个时间会结束的操作）

![在这里插入图片描述](https://img-blog.csdnimg.cn/134202ad516642089818b386b35dddad.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

方式二（常用）：通过CompletionHandler，执行成功和失败后的逻辑都可以自定义

![在这里插入图片描述](https://img-blog.csdnimg.cn/9e1ff150baf8440e8595f03272b8bc03.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## AIO优化聊天室

![在这里插入图片描述](https://img-blog.csdnimg.cn/fa65b69c20cc4121b8ef95d79c2ef304.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

# 基于NIO完成一个web服务器

## 动态资源请求流程

请求会进入容器，容器将请求发给servlet，servlet将请求处理后把结果返回给容器，然后容器返回给客户端

![在这里插入图片描述](https://img-blog.csdnimg.cn/7a8b5792fe00401b85714221e7567feb.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## Tomcat结构

- server：服务器最顶层的组件，负责运行Tomcat服务器，加载服务器资源和环境变量
- service：集合connector和engine的抽象组件，一个server可以包含多个service，一个service可以包含多个connector和一个engine
- connector：负责接受外来请求，提供基于不同协议的实现，解析请求，返回响应，将请求传递给processor
- processor：进一步对请求进行拆解
- engine：engine到最里面都是容器。容器用来处理请求，engine是容器的顶层组件
- host：虚拟主机，一个engine可以支持多个虚拟主机的请求，engine通过解析发给对应的host
- context：每个context代表一个网络应用，是最复杂的组件之一，进行应用资源管理、应用类就艾滋啊、servlet管理、安全管理等
- wrapper：包裹内部的servlet实例

![在这里插入图片描述](https://img-blog.csdnimg.cn/c324485bf99349d5b31757767fd42237.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA56y85Lit5bCP5aSc6I66,size_20,color_FFFFFF,t_70,g_se,x_16)

## 现存问题

- 当html中包含图片的时候就会请求两次资源（第二次是浏览器渲染的时候请求的）
- 自己写socket，直接运行打印出来没有信息，debug就有信息，因为有端口号？