<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# 网络编程

- 网络模型：
 - **OSI（开放系统互连）参考模型**
 - **TCP/IP参考模型 **
- **IP是网络层协议，TCP/UDP是传输层协议，HTTP/FTP是应用层协议**

![](.\MyPic\OSI_and_TCPIP.bmp)

- 网络通讯的要素
 - **IP地址（InetAddress）**：网络中设备的标识（127.0.0.1是本地回环地址）
 - **端口号**：用于标识进程的逻辑地址，是不同进程的标识（有效端口0~65535，其中0~1024是系统保留端口）
 - **传输协议**：通讯的规则，常见的有TCP和UDP

## IP地址（InetAddress）

- InetAddress是IP地址类
- InetAddress未提供构造函数，**不能自己创建对象**
- InetAddress获取对象的方法：
 - `static InetAddress getLocalHost()` —— 获取当前主机的IP地址对象
 - `static InetAddress getByName(String host)` —— 获取指定主机的IP地址对象
 - `static InetAddress[] getAllByName(String host)` —— 获取包含指定主机的所有IP地址对象的数组
- InetAddress其他方法：
 - `String getHostAddress()` —— 可获取InetAddress对象的IP地址值
 - `String getHostName()` —— 可获取InetAddress对象的主机名

```
    /**
     *  IP地址
     *      InetAddress未提供构造函数，不能创建对象
     *      可通过静态方法InetAddress.getLocalHost()获取当前主机的IP地址对象
     *      也可通过静态方法InetAddress.getByName(String host)获取指定主机的IP地址对象
     *      还可通过静态方法InetAddress.getAllByName(String host)获取包含指定主机的所有IP地址对象的数组
     *      
     *      InetAddress对象的getHostAddress()方法可获取IP地址值
     *      InetAddress对象的getHostName()方法可获取主机名
     */
    private static void inetAddressDemo() throws UnknownHostException {
        InetAddress ip = InetAddress.getLocalHost();    // 获取当前主机的IP地址对象
        System.out.println("IP Address: " + ip.getHostAddress());
        System.out.println("Host Name: " + ip.getHostName());

        System.out.println("-------");

        ip = InetAddress.getByName("10.20.144.126");    // 通过名称获取当前或其他主机的IP地址对象
        System.out.println("IP Address: " + ip.getHostAddress());
        System.out.println("Host Name: " + ip.getHostName());

        System.out.println("-------");

        InetAddress[] ips = InetAddress.getAllByName("www.baidu.com");  // 获取包含该主机所有IP地址的数组
        System.out.println("www.baidu.com所拥有的IP地址数量为" + ips.length + "个");
        for (InetAddress ip1 : ips) {
            System.out.println("IP Address: " + ip1.getHostAddress());
            System.out.println("Host Name: " + ip1.getHostName());
        }
    }

输出为：
IP Address: 10.20.144.126
Host Name: CrisJiangDT
-------
IP Address: 10.20.144.126
Host Name: CrisJiangDT
-------
www.baidu.com所拥有的IP地址数量为2个
IP Address: 14.215.177.38
Host Name: www.baidu.com
IP Address: 14.215.177.39
Host Name: www.baidu.com
```

## UDP & TCP

- **UDP**：
 - 将数据及源和目的封装成数据包，**不需要建立连接**
 - 因为无连接，是**不可靠协议**，不同数据包可能选择不同的路由，也可能不按顺序到达，且不能保证到达
 - 因为不需要建立连接，**速度快**
 - 每个**数据包的大小限制**在64K
- **TCP**：
 - 采用**三次握手建立连接**，行成传输数据的通道
 - 因为建立连接，是**可靠协议**
 - 因为需要建立连接，**效率稍低**
 - 可在连接中进行**大数据量的传输**

### Socket

- Socket（套接字）是为网络服务提供的一种机制
- 网络通信的两端都有Socket，网络通信其实就是Socket之间的通信
- 数据在两个Socket之间通过IO传输

### UDP

- UDP有**发送端和接收端**
- UDP涉及的对象：
 - **DatagramSocket**：用来接收和发送数据包的Socket
 - **DatagramPacket**：数据包

UDP发送端

DatagramSocket的`send(DatagramPacket p)`用于发送数据包p

```
    /**
     * 创建UDP传输的发送端
     *      思路：
     *          1. 创建UDP的Socket服务，即创建DatagramSocket对象
     *          2. 将要发送的数据封装到数据包中，即封装成DatagramPacket对象（有InetAddress参数的构造函数是用于发送的，没有的则是用于接收的）
     *          3. 用DatagramSocket对象的send()方法，通过Socket服务将数据包对象发送出去
     *          4. 关闭Socket服务
     */
    public static void sendUDPDemo() throws IOException {
        System.out.println("接收端启动......");

        // 1. 创建UDP的Socket服务
        DatagramSocket ds = new DatagramSocket();

        // 2. 将要发送的数据封装到数据包中
        String str = "vv3岁";
        byte[] bytes = str.getBytes();
            // 用于发送的数据包，要选择有InetAddress参数的构造函数
        DatagramPacket dp = new DatagramPacket(bytes, bytes.length, InetAddress.getLocalHost(), 7777);  // 设置本地主机为目的主机，7777端口为目的端口

        // 3. 通过Socket服务将数据包发送出去
        ds.send(dp);

        // 4. 关闭Socket服务
        ds.close();
    }
```

UDP接收端

DatagramSocket的`receive(DatagramPacket p)`用于接收数据包p，是**阻塞式方法**

```
    /**
     * UDP传输的接收端
     *      思路：
     *          1. 创建UDP的Socket服务，注意要指定接收数据的端口号，否则是随机端口，无法接收到
     *          2. 创建用于存储接收到的数据的数据包（选择没有InetAddress参数的构造函数），才能用数据包对象自己的方法解析接收到的数据
     *          3. 用DatagramSocket对象的receive()方法接收数据到数据包中
     *          4. 用数据包对象的方法解析数据
     *          5，关闭Socket服务
     */
    public static void receiveUDPDemo() throws IOException {
        System.out.println("接收端启动......");

        // 1. 创建UDP的Socket服务，注意要指定接收的端口号
        DatagramSocket ds = new DatagramSocket(7777);

        // 2. 创建用于接收数据的数据包，要选择没有InetAddress参数的构造函数
        byte[] bytes = new byte[1024];
        DatagramPacket dp = new DatagramPacket(bytes, bytes.length);

        // 3. 通过Socket服务接收数据到数据包中
        ds.receive(dp); // 阻塞式方法

        // 4. 用数据包自己的方法解析所收到数据
        String ip = dp.getAddress().getHostAddress();   // getAddress()获取到InetAddress对象，再用该对象的getHostAddress()获取到IP地址
        int port = dp.getPort();    // 获取端口号，这个端口号是源主机的端口号
        byte[] data = dp.getData(); // 获取数据内容
        int length = dp.getLength(); // 获取数据长度

        // 5. 关闭Socket服务
        ds.close();

        System.out.println("IP: " + ip);
        System.out.println("Port: " + port);
        System.out.println("Data:" + new String(data, 0, length));
    }
```

UDP发送端和接收端结合**（UDP是面向无连接的，发送端和接收端哪个先启动都可以）**

```
public class UDPDemo {
    public static void main(String[] args) throws IOException {
        new Thread(new SendUDP()).start();
        new Thread(new ReceiveUDP()).start();
    }

    public static void sendUDPDemo() throws IOException {...}

    public static void receiveUDPDemo() throws IOException {...}
}z

class SendUDP implements Runnable {
    @Override
    public void run() {
        try {
            UDPDemo.sendUDPDemo();
        } catch (IOException e) { e.printStackTrace(); }
    }
}

class ReceiveUDP implements Runnable {
    @Override
    public void run() {
        try {
            UDPDemo.receiveUDPDemo();
        } catch (IOException e) { e.printStackTrace(); }
    }
}

输出为：
发送端启动......
接收端启动......
IP: 192.168.125.117
Port: 56889
Data: vv3岁
```

练习：聊天程序

```
public class MyWeChat {
    public static void main(String[] args) throws SocketException {
        DatagramSocket sendSocket = new DatagramSocket();
        DatagramSocket receiveSocket = new DatagramSocket(7777);
        new Thread(new SendWeChat(sendSocket)).start();
        new Thread(new ReceiveWeChat(receiveSocket)).start();
    }
}

// 接收线程
class ReceiveWeChat implements Runnable {
    private DatagramSocket ds;

    public ReceiveWeChat(DatagramSocket ds) { this.ds = ds; }

    @Override
    public void run() {
        byte[] buf = null;
        DatagramPacket dp = null;
        while (true) {
            buf = new byte[1024];
            dp = new DatagramPacket(buf, buf.length);
            try {
                ds.receive(dp);
                String hostName = dp.getAddress().getHostName();
                String dataStr = new String(dp.getData(), 0, dp.getLength());
                System.out.println("主机" + hostName + "说：" + dataStr);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

// 发送线程
class SendWeChat implements Runnable {
    private DatagramSocket ds;

    public SendWeChat(DatagramSocket ds) { this.ds = ds; }

    @Override
    public void run() {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String line = null;
        byte[] bytes = null;
        DatagramPacket dp = null;
        try {
            while ((line = br.readLine()) != null) {
                if ("886".equals(line)) {
                    System.out.println("主机" + InetAddress.getLocalHost().getHostName() + "退出群聊");
                    ds.close();
                    break;
                }
                bytes = line.getBytes();
                dp = new DatagramPacket(bytes, bytes.length, InetAddress.getByName("192.168.125.117"), 7777);
                ds.send(dp);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            ds.close();
        }
    }
}
```

### TCP

- TCP有**客户端和服务端**
- TCP涉及的对象：
 - **Socket**：客户端Socket
 - **ServerSocket**：服务端Socket

TCP客户端

Socket的`OutputStream getOutputStream()`方法用于获取Socket输出流对象
Socket的`InputStream getInputStream()`方法用于获取Socket输入流对象
**Socket的`shutdownOutput()`方法用于植入输出结束标记，否则服务端无法识别是否读取完毕，造成堵塞**

```
    /**
     * TCP 客户端发送数据到服务端
     *      思路：
     *          1. 创建TCP客户端的Socket服务，使用的是Socket，建议一创建Socket对象就明确目的主机及目的端口号
     *          2. 如果连接成功，则数据传输通道已经建立，该通道是Socket底层建立好的
     *             该通道既能输入，也能输出，可以通过Socket对象的getOutputStream()和getInputStream()来获取底层的字节流对象（Socket流）
     *          3. 使用输出流向服务端发送数据，或使用输入流从服务端读取数据
     *          4. 关闭Socket服务
     */
    private static void clientTCPDemo() throws IOException {
        System.out.println("TCP客户端启动......");

        // 1. 创建TCP客户端的Socket服务，并明确目的主机及端口号
        Socket socket = new Socket("10.20.145.37", 7777);

        // 2. 从Socket对象中获取底层输出流
        OutputStream os = socket.getOutputStream();

        // 3. 使用底层输出流写数据
        os.write("祖国母亲70周岁生日快乐！".getBytes());

        // 4. 关闭Socket服务
        socket.close();
    }
```

TCP服务端

ServerSocket的`Socket accept()`方法用于**获取**连接到服务端的**客户端对象**，是**阻塞式方法**

```
    /**
     * TCP 服务端接收客户端发送的数据
     *      思路：
     *          1. 创建TCP服务端的Socket服务，使用的是ServerSocket（注意，服务端创建时必须对外提供一个端口，否则客户端无法连接）
     *          2. 获取连接过来的客户端对象，使用的是ServerSocket对象的accept()方法
     *          3. 通过客户端对象，获取Socket流，来读取客户端发送的数据
     *          4. 关闭Socket服务（一般服务端也要去关闭客户端，而服务端本身不一定要关闭，还要为其他客户端服务）
     */
    private static void serverTCPDemo() throws IOException {
        System.out.println("TCP服务端启动...");

        // 1. 创建TCP服务端的Socket服务，并明确对外提供的端口号
        ServerSocket ss = new ServerSocket(7777);

        // 2. 获取连接过来的客户端对象
        Socket s = ss.accept();	// 阻塞式方法
            // 获取连接过来的客户端对象的IP地址
        String clientIP = s.getInetAddress().getHostAddress();

        // 3. 通过客户端对象获取Socket流，并读取流中传输的数据
        InputStream in = s.getInputStream();
        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            System.out.println("服务端收到客户端" +  clientIP + "发送的数据：" + new String(buf, 0, len));

        // 4. 关闭Socket服务
        s.close();  // 从服务端断开客户端连接，同时也关闭了用到Socket流的IO流（如in）
        ss.close(); // 关闭服务端
    }
```

TCP客户端和服务端结合**(注意：TCP是面向连接的，必须先启动服务端，再启动客户端，否则无法连接)**

```
    public static void main(String[] args) throws IOException {
        // 启动服务端线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    serverTCPDemo();
                } catch (IOException e) { e.printStackTrace(); }
            }
        }).start();

        // 启动客户端线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    clientTCPDemo();
                } catch (IOException e) { e.printStackTrace();}
            }
        }).start();
    }

    private static void serverTCPDemo() throws IOException {...}

    private static void clientTCPDemo() throws IOException {...}

输出为：
TCP服务端启动...
TCP客户端启动......
服务端收到客户端10.20.145.37发送的数据：祖国母亲70周岁生日快乐！
```

TCP客户端和服务端相结合，服务端收到数据后回复客户端

```
    public static void main(String[] args) throws IOException {
        // 启动服务端线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    serverTCPDemo();
                } catch (IOException e) { e.printStackTrace();}
            }
        }).start();

        // 启动客户端线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    clientTCPDemo();
                } catch (IOException e) { e.printStackTrace(); }
            }
        }).start();
    }

    private static void serverTCPDemo() throws IOException {
        System.out.println("TCP服务端启动...");

        ServerSocket ss = new ServerSocket(7777);

        Socket s = ss.accept(); // 阻塞式方法
        String clientIP = s.getInetAddress().getHostAddress();

        InputStream in = s.getInputStream();
        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            System.out.println("服务端收到客户端" +  clientIP + "发送的数据：" + new String(buf, 0, len));

            // 收到客户端发来的数据后，回复客户端“服务端已收到！”
        OutputStream out = s.getOutputStream();
        out.write("服务端已收到！".getBytes());

        s.close();  // 从服务端断开客户端连接
        ss.close(); // 关闭服务端
    }

    private static void clientTCPDemo() throws IOException {
        System.out.println("TCP客户端启动......");

        Socket socket = new Socket("127.0.0.1", 7777);

        OutputStream out = socket.getOutputStream();
        out.write("祖国母亲70周岁生日快乐！".getBytes());
		socket.shutdownOutput();	// 植入输出结束标记，否则服务端接收时无法判断是否读取完毕

            // 读取服务端的回复
        InputStream in = socket.getInputStream();
        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            System.out.println("客户端收到服务端的回复：" + new String(buf, 0, len));

        socket.close();
    }

输出为：
TCP服务端启动...
TCP客户端启动......
服务端收到客户端127.0.0.1发送的数据：祖国母亲70周岁生日快乐！
客户端收到服务端的回复：服务端已收到！
```

练习1：客户端由键盘输入字母，发送到服务端，服务端将数据转成大写字母后发送回客户端，并打印在控制台上，当客户端输入"over"时结束

 - 服务端：

```
/**
 *                     服务端思路：
 *                      1. 创建TCP服务端ServerSocket对象
 *                      2. 获取TCP客户端的Socket对象
 *                      3. 将客户端发送的数据打印，然后转换成大写并发送回客户端，源是Socket输入流，目的是Socket输出流
 */
public class TextTransServer {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(7777);
        Socket s = ss.accept();

        String ip = s.getInetAddress().getHostAddress();
        System.out.println(ip + " connected...");

        BufferedReader in = new BufferedReader(new InputStreamReader(s.getInputStream()));
        PrintWriter out = new PrintWriter(s.getOutputStream(), true);   // 用打印流可以保持数据的表现形式，且可以自动刷新，否则需要flush()

        String line = null;
        while ((line = in.readLine()) != null) {
            System.out.println(line);
            out.println(line.toUpperCase());
            /*
            如果是用print()而不是println()，则需要自己加换行标记，即out.print(line.toUpperCase() + "\r\n")，否则客户端readLine()时认为没有读完，阻塞
            如果没有自动刷新，则需要手动刷新，即out.flush()，否则客户端不能收到数据，阻塞
             */
        }

        s.close();	// in、out也同时关闭了
        ss.close();
    }
}
```

 - 客户端：

```
/**
 *                     客户端思路：
 *                      1. 创建TCP客户端Socket对象
 *                      2. 将数据发送给服务端时，源是键盘录入，目的是Socket输出流
 *                      3. 将服务端返回的数据打印在控制台时，源是Socket输入流，目的是控制台
 */
public class TextTransClient {
    public static void main(String[] args) throws IOException {
        Socket s = new Socket("10.11.177.54", 7777);

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(s.getOutputStream(), true);   // 用打印流可以保持数据的表现形式，且可以自动刷新，否则需要flush()

        BufferedReader in = new BufferedReader(new InputStreamReader(s.getInputStream()));

        String line = null;
        String upperStr = null;
        while ((line = br.readLine()) != null) {
            if ("over".equalsIgnoreCase(line))
                break;
            out.println(line);
            /*
            如果是用print()而不是println()，则需要自己加换行标记，即out.print(line + "\r\n")，否则服务端readLine()时认为没有读完，阻塞
            如果没有自动刷新，则需要手动刷新，即out.flush()，否则服务端不能收到数据，阻塞
             */

            upperStr = in.readLine();
            System.out.println(upperStr);
        }

        s.close();
    }
}
```

练习2：从客户端上传文本文件到服务端

 - 服务端：

```
/**
 *                     服务端思路：
 *                       1. 创建TCP服务端ServerSocket对象，并获取客户端Socket对象
 *                       2. 将客户端发送的文本文件读取并保存到本地文本文件中，源是Socket流，目的是本地文件
 *                       3. 读取到发送结束标记时，发送“上传成功”给客户端，目的是Socket流
 */
public class UploadTextServer {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(7777);
        Socket s = ss.accept();

        String ip = s.getInetAddress().getHostAddress();
        System.out.println(ip + " connected...");

        File file = new File("myTest\\server.txt"); // 实际上传时应该是在服务端保存一份同名的文件
        if (!file.exists())
            file.createNewFile();
        BufferedWriter bw = new BufferedWriter(new FileWriter(file));
        BufferedReader in = new BufferedReader(new InputStreamReader(s.getInputStream()));

        String line = null;
        while ((line = in.readLine()) != null) {    // 客户端的shutdownOutput()会使服务端的readLine()读到null，结束读取
            bw.write(line);
            bw.newLine();
            bw.flush();
        }

        PrintWriter out = new PrintWriter(s.getOutputStream(), true);   // 别忘了自动刷新，否则要手动flush()刷新
        out.println("上传成功！");

        bw.close();
        s.close();
        ss.close();
    }
}
```

 - 客户端：

```
/**
 *                     客户端思路：
 *                       1. 创建TCP客户端Socket对象
 *                       2. 将本地文本文件发送给服务端，源是本地文件，目的是Socket输出流
 *                       3. 植入发送结束标记（用Socket对象的shutdownOutput()方法），否则服务端无法识别传输是否结束，造成阻塞
 *                       4. 读取服务端发送的“上传成功”，源是Socket流，目的是控制台
 */
public class UploadTextClient {
    public static void main(String[] args) throws IOException {
        Socket s = new Socket("10.11.177.54", 7777);

        File file = new File("myTest\\client.txt");
        if (!file.exists())
            throw new RuntimeException("该文件不存在");
        BufferedReader br = new BufferedReader(new FileReader(file));
        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(s.getOutputStream()));

        String line = null;
        while ((line = br.readLine()) != null) {
            out.write(line);
            out.newLine();
            out.flush();
        }

        // 植入发送结束标记，否则服务端无法识别传输是否结束，造成阻塞
        s.shutdownOutput(); // 会使服务端的read()读到-1，readLine()读到null

        BufferedReader in = new BufferedReader(new InputStreamReader(s.getInputStream()));
        System.out.println(in.readLine());

        br.close();
        s.close();
    }
}
```

练习3：客户端上传图片到服务端

 - 服务端：

```
/**
 *                      服务端思路：
 *                          1. 创建TCP服务端ServerSocket对象，并获取客户端Socket对象
 *                          2. 将客户端发送的图片保存到本地，源是Socket输入流，目的是本地文件
 *                          3. 传输结束后，发送“上传成功”到客户端，目的是Socket输出流
 */
public class UploadImgServer {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(7777);
        Socket s = ss.accept();

        String ip = s.getInetAddress().getHostAddress();
        System.out.println(ip + " connected...");

        InputStream in = s.getInputStream();
        File file = new File("myTest\\Portugal.jpg");
        FileOutputStream fos = new FileOutputStream(file);

        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            fos.write(buf, 0, len);

        PrintWriter out = new PrintWriter(s.getOutputStream(), true);
        out.println("上传成功！");

        fos.close();
        s.close();
        ss.close();
    }
}
```

 - 客户端：

```
/**
 *                      客户端思路：
 *                          1. 创建TCP客户端Socket对象
 *                          2. 将图片传输到服务端，源是本地文件，目的是Socket输出流
 *                          3. 传输完数据后植入结束标记，否则服务端无法识别传输是否结束，造成堵塞
 *                          4. 接收服务端发送的“上传成功”，源是Socket输入流，目的是控制台
 */
public class UploadImgClient {
    public static void main(String[] args) throws IOException {
        Socket s = new Socket("10.11.177.54", 7777);

        File file = new File("C:\\Users\\Cris_JiangDT\\Desktop\\仲意搅屎的梅城人\\Portugal.jpg");
        if (!file.exists())
            throw new RuntimeException("该文件不存在");
        FileInputStream fis = new FileInputStream(file);
        OutputStream out = s.getOutputStream();

        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = fis.read(buf)) != -1)
            out.write(buf, 0, len);

        s.shutdownOutput(); // 植入传输结束标记

        BufferedReader in = new BufferedReader(new InputStreamReader(s.getInputStream()));
        System.out.println(in.readLine());

        fis.close();
        s.close();
    }
}
```

为了满足多个客户端同时上传图片，服务端需要多线程
 - 多线程服务端：

```
/**
 *                      多线程服务端思路：
 *                          1. 创建TCP服务端ServerSocket对象，并不断获取客户端Socket对象
 *                          2. 为每个客户端Socket对象开辟一条线程，进行图片的上传（源是Socket输入流，目的是本地文件）
 */
public class UploadImgServer2 {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(7777);

        // 不断获取客户端Socket对象，并给获取到的Socket对象开辟一条线程用于上传图片
        while (true) {
            Socket s = ss.accept(); // 由于是阻塞式方法，不需要循环跳出条件，当没有客户端连接进来时会自动阻塞
            new Thread(new UploadImgTask(s)).start();
        }
    }
}

class UploadImgTask implements Runnable {
    private Socket s;

    public UploadImgTask(Socket s) { this.s = s; }

    @Override
    public void run() {
        String ip = s.getInetAddress().getHostAddress();
        System.out.println(ip + " connected...");

        int count = 0;
        InputStream in = null;
        File file = null;
        FileOutputStream fos = null;
        try {
            in = s.getInputStream();

            file = new File("myTest\\" + ip + ".jpg");
            if (file.exists())
                file = new File("myTest\\" + ip + "(" + (++count) + ").jpg");
            fos = new FileOutputStream(file);

            byte[] buf = new byte[1024];
            int len = 0;
            while ((len = in.read(buf)) != -1)
                fos.write(buf, 0, len);

            PrintWriter out = new PrintWriter(s.getOutputStream(), true);   // 如果不加true进行自动刷新，则要手动out.flush()进行刷新
            out.println("上传成功！");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    throw new RuntimeException("FileOutputStream资源关闭失败");
                }
            }
            if (s != null) {
                try {
                    s.close();
                } catch (IOException e) {
                    throw new RuntimeException("Socket资源关闭失败");
                }
            }
        }

    }
}
```

#### 服务端 & 客户端

- 最常见的客户端：浏览器（如IE、谷歌等浏览器）
- 最常见的服务端：服务器（如**Tomcat**，它提供了**Servlet接口**，在服务端写的用于处理请求的类都要实现该接口）
- **服务端的作用：处理请求并给予应答**

练习1：自定义服务端，浏览器做客户端进行访问，将浏览器发给服务端的信息打印在控制台上，并发送“欢迎光临”到浏览器

```
public class MyTomcat {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(7777);
        while (true) {
            Socket s = ss.accept();
            System.out.println(s.getInetAddress().getHostAddress() + " connected...");

            InputStream in = s.getInputStream();
            byte[] buf = new byte[1024];
            int len = in.read(buf);
            System.out.println(new String(buf, 0, len));
            // 如果用循环读取，浏览器无法收到“欢迎光临”，因为无法判断内容是否读取结束

            PrintWriter out = new PrintWriter(s.getOutputStream(), true);   // 注意要自动/手动刷新
            out.println("欢迎光临");    // 注意要将浏览器编码设置为UTF-8，否则乱码

            s.close();
        }
    }
}

在浏览器输入"http://10.11.182.205:7777"（其中10.11.182.205为本地IP地址，也可输入本地回环地址127.0.0.1），并将浏览器编码设置为UTF-8，在浏览器上显示出“欢迎光临”

控制台输出为：
10.11.182.205 connected...	// 自定义的输出
GET /favicon.ico HTTP/1.1   // 请求行：包括请求方式、请求的资源路径和HTTP协议版本
Accept: */*	// 此行开始为请求消息头，即请求属性信息（键值对形式）
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; WOW64; Trident/7.0; .NET4.0C; .NET4.0E; .NET CLR 2.0.50727; .NET CLR 3.0.30729; .NET CLR 3.5.30729)
Host: 10.11.182.205:7777
Connection: Keep-Alive
		  // 空行
......   // 请求体

```

练习2：模拟浏览器获取练习1中服务端的信息

 - 模拟服务端同上
 - 模拟浏览器：

```
public class MyBrowser {
    public static void main(String[] args) throws IOException {
        Socket s = new Socket("127.0.0.1", 8080);   // 10.11.182.205为本地IP地址，8080是开启了Tomcat的端口
        PrintWriter out = new PrintWriter(s.getOutputStream(), true);   // 注意要自动/手动刷新

        out.println("GET /myWebs/MyHTML.html HTTP/1.1");  // 请求行
        out.println("Accept: */*");
        out.println("Host: 107.0.0.1:8080");
        out.println("Connection: close");
        out.println();  // 空行
        out.println();  // 请求体

        InputStream in = s.getInputStream();
        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            System.out.println(new String(buf, 0, len));

        s.close();
    }
}

输出为：
HTTP/1.1 200	// 应答行：包括HTTP协议版本、应答状态码（200是找到）和应答状态描述（OK是成功）
Accept-Ranges: bytes	// 此行开始为应答消息头，即应答属性信息（键值对形式）
ETag: W/"1194-1570709641067"
Last-Modified: Thu, 10 Oct 2019 12:14:01 GMT
Content-Type: text/html
Content-Length: 1194
Date: Thu, 10 Oct 2019 12:14:10 GMT
Connection: close
				// 空行
<!DOCTYPE html>	// 此行开始为应答体（网页源码）
<title>	Welcome to World of Cris_JiangDT-HTML	</title>
<html>
<body style = "background-color:PowderBlue">

<h1>	It's my first heading!! It's heading tag!!	</h1>
<h2>	Oh!! It's my second heading now!! And it's much smaller!!	</h2>
<h3>	Smaller!!!!	</h3>
<h4>	Can we stop???	</h4>
<h5>	Don't be smaller, please!!	</h5>
<h6>	OK, now we can't be smaller. ^_^ 	</h6>

<p>	It's my first paragraph!! It's paragraph tag!!	</p>

<p> Now, let's code a link... </p>
<a href="http://www.baidu.com">	Click here to link Baidu. 	</a>
<br />
<a href="https://www.google.com.hk/?gws_rd=ssl">	Click here to link Google.	</a>


<p	Here, let's see my cookie...	</p>	
<img src="D:\IDEA\IdeaProject\myTest\Cookie.jpg" />

<h1 style = "text-align:center; font-family:arial; color:blue">	Th
is is a heading that has been centralized	</h1>

<p> To line feed <br /> without <br /> creating a new paragraph</p>

<p style="font-family:verdana; color:red">	This text is in Verdana and Red	</p>
<p style="background-color:red; font-family:times; color:green">	This text is in Times and Green	</p>
<p style="font-size:30px">	This text is in 30 pixels high	</p>

</body>
</html>

```

- **GET请求没有请求体，POST请求有请求体**
- 一些常见的响应状态码：
 - 200：请求成功，浏览器会把响应体内容（通常是HTML）显示在页面上
 - 404：请求的资源没有找到，说明客户端错误地请求了不存在的资源
 - 500：请求的资源找到了，但服务器内部出现了错误
 - 302：重定向，表示服务器要求客户端再发一个请求到新的URL地址，该地址在响应头Location中
 - 304：（只对静态页面即HTML有效）说明浏览器之前所缓存的页面的最后修改时间和服务器的页面的最后修改时间相同，服务器不发响应体，而是让浏览器直接到其缓存中拿到该页面![](.\MyPic\304.png)

#### URL（统一资源定位符）

- 无论是**DatagramSocket**还是**Socket，都是传输层对象，封装了IP地址和端口号，而没有封装传输协议**（如HTTP），而**URL是应用层对象，可以封装传输协议、IP地址和端口号**
- URL（统一资源定位符）和URI（统一资源标识符）的区别：
 - URI和URL都定义了资源是什么，但URL还定义了该如何访问资源
 - **URL是一种具体的URI**，是URI的一个子集，它不仅唯一标识资源，而且还提供了定位该资源的信息
 - URI相当于身份证号，而URL相当于身份证住址+姓名
- URL类的方法：
 - `String getProtocol()` —— 获取URL的传输协议
 - `String getHost()` —— 获取URL的主机名/IP地址
 - `int getPort()` —— 获取URL的端口号
 - `String getFile()` —— 获取URL的文件名（如果有参数，则带参数）
 - `String getPath()` —— 获取URL的文件路径（不带参数）
 - `String getQuery()` —— 获取URL的参数信息

```
    /**
     * URL获取其各部分信息的方法，如getProtocol()、getHost()、getPort()、getFile()、getPath()、getQuery()
     */
    private static void urlDemo1() throws MalformedURLException {
        String str_url = "http://127.0.0.1:8080/myWebs/MyHTML.html?name=JiangDT";   // “?”后面的是自定义的参数
        URL url = new URL(str_url);

        String protocol = url.getProtocol();
        String host = url.getHost();
        int port = url.getPort();
        String file = url.getFile();    // getFile()带参数信息（如果有的话）
        String path = url.getPath();    // getPath()只负责到文件，不带参数信息
        String query = url.getQuery();

        System.out.println("Protocol: " + protocol);    // Protocol: http
        System.out.println("Host: " + host);    // Host: 127.0.0.1
        System.out.println("Port: " + port);    // Port: 8080
        System.out.println("File: " + file);    // File: /myWebs/MyHTML.html?name=JiangDT
        System.out.println("Path: " + path);    // Path: /myWebs/MyHTML.html
        System.out.println("Query: " + query);  // Query: name=JiangDT
    }
```

- URL类的`InputStream openStream()`方法 —— 用于获取连接到该URL对象所指向资源的字节输入流，解析了数据头（应答消息头），只留下数据体（应答体）

```
    /**
     * URL的openStream()方法 —— 用于获取连接到该URL对象内容的字节输入流，解析了数据头（应答消息头），只留下数据体（应答体）
     */
    private static void urlDemo2() throws IOException {
        String str_url = "http://127.0.0.1:8080/myWebs/MyHTML.html?name=JiangDT";
        URL url = new URL(str_url);

        InputStream in = url.openStream();
        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            System.out.println(new String(buf, 0, len));	// 输出为浏览器的应答体，即网页源代码（不带应答消息头，已经被解析了）

        in.close();
    }
```

- URL类的`URLConnection openConnection()`方法 —— 获取URLConnection对象

#### URLConnection

- URLConnection是URL连接器，将URL连接封装成了对象，其实 URLConnection = Java内置的可以解析具体协议的对象 + Socket
- URLConnection的构造函数：
 - `URLConnetion(URL url)` —— 获取url所指向内容的URL连接器
 - 可通过URL对象的`URLConnection openConnection()`方法获取URLConnection对象
- URLConnection类的方法：
 - `InputStream getInputStream()` —— 获取URLConnection中URL所指向内容的输入流
 - `OutputStream getOutputStream()` —— 获取URLConnection中URL所指向内容的输出流
 - `String getHeaderField(String name)` —— 通过属性名获取属性值，其实是解析具体协议的过程

```
    /**
     * URLConnection：URL连接器，将连接封装成了对象，其实 URLConnection = Java内置的可以解析具体协议的对象 + Socket
     * URL类的openStream()方法其实相当于openConnection().getInputStream()
     *
     * URLConnection类
     *  可以获取URL所指向内容的输入流（getInputStream()方法）、输出流（getOutputStream()方法）等，
     *  还可以通过getHeaderField(String name)等方法通过属性名获取属性值
     */
    private static void urlConnectionDemo() throws IOException {
        String str_url = "http://127.0.0.1:8080/myWebs/MyHTML.html?name=JiangDT";
        URL url = new URL(str_url);
        URLConnection conn = url.openConnection();  // 等效于 URLConnection conn = new URLConnection(url);

        System.out.println(conn);   // sun.net.www.protocol.http.HttpURLConnection:http://127.0.0.1:8080/myWebs/MyHTML.html?name=JiangDT
        String type = conn.getHeaderField("Content-Type");
        System.out.println("Content-Type: " + type);   // Content-Type: text/html

        System.out.println("-------");

        InputStream in = conn.getInputStream();
        byte[] buf = new byte[1024];
        int len = 0;
        while ((len = in.read(buf)) != -1)
            System.out.println(new String(buf, 0, len));	// 输出为浏览器的应答体，即网页源代码（不带应答消息头，已经被解析了）

        in.close();
    }
```

## 网络架构

- **网络架构分为C/S（Client/Server）和B/S（Browser/Server）**
- C/S的特点：
 - 该架构的软件，客户端和服务端都需要编写
 - 开发成本较高，维护较麻烦
 - 好处：客户端可以在本地分担一些运算
- B/S的特点：
 - 该架构的软件，只需要编写服务端，不需要编写客户端，客户端由浏览器取代
 - 开发成本较低，维护较简单
 - 弊端：所有运算都要在服务端完成（如扫雷就不适合）

# 反射机制

- Java反射机制是在运行状态中，对于任意一个实体类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性
- **反射机制大大提高了程序的可扩展性**
- 运用反射机制，开发者在扩展程序时，只需要**实现**程序提供的**接口**，并将**扩展信息写进**程序的**配置文件**中，**程序运行时**就能自动读取配置文件中的信息，并**动态获取**、创建新扩展的类，**并调用**该类对象的**方法**
- **反射机制其实就是对类的解剖，依靠的是Class类，该类可以获取字节码文件（.class文件）中的所有内容**，如名称、字段、构造函数、一般函数等（类比Person类可以拿到所有Person对象的内容如姓名、年龄等），因此想要对一个类文件进行解剖，只要拿到该类的字节码文件对象即可
- **类加载器（ClassLoader）的作用**：将class文件字节码内容加载到内存中，并将这些**静态数据转换成方法区的运行时数据结构**，然后在堆中生成一个代表这个类的java.lang.Class对象，作为方法区中类数据的访问入口
 - ClassLoader体系：
 1. 引导类加载器（负责加载Java核心类库，无法获取到）
 2. 扩展类加载器（调用系统类加载器的getParent()可获取到）
 3. 系统类加载器（负责记载自定义类，通过xxx.class.getClassLoader()可获取到）

## Class类（字节码文件类）的理解

- 程序经过javac.exe命令后，会生成一个或多个字节码文件（.class文件）
- 使用java.exe命令对某个字节码文件进行解释运行，相当于将该字节码文件加载到内存中，称为类的加载。加载到内存中的类，就称为运行时类，就是Class类的一个实例
- 各种结构都可以有Class对象，如普通类、Class类、接口、数组、基本数据类型、注解、枚举、void等
 - 只要数组的类型和维度相同，就是同一个Class，如`int[] a = new int[10]; int[] b = new int[100];`，则`a.getClass()`和`b.getClass()`相同
- **即Class类的一个实例对应着加载到内存中的一个运行时类，Class是反射的源头**


下面用到的Person类

```
class Person {
    private String name;
    private int age;

    public Person() {
        super();
        System.out.println("Person() run...");
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("Person(String name, int age) run... " + this.name + " : " + this.age);
    }

    public void introduce() {
        System.out.println("My name is " + this.name + ", and I'm " + this.age + " years old.");
    }

    private void secret(int money) {
        System.out.println("My name is "+ this.name + ", I'm " + this.age + " years old, I have " + money + " dollars.");
    }
}
```

- 要运用反射机制，就要获取类的字节码文件对象，**获取字节码文件对象的方法有三种**：
 - **Object类的`Class getClass()`方法**，这种方式必须明确类并获取到对象，扩展性差
 - **任何类都具有的静态属性`.class`属性**，这种方式较为简单，但还是要明确类并用类的静态属性，扩展性也不强
 - **Class类的`Class forName(String name)`方法**，这种方式只需要知道类的字符串名称就能获取到字节码文件对象，**扩展性强，在反射机制中主要用这种方式**（**注意：类的字符串名称必须是完整的、带包名的**，因为是字符串，因此和是否import无关）

```
    /**
     * 方式三： 用Class类的forName(String name)方法，注意name参数必须是完整的带包名的，是字符串，因此和是否import无关
     *  这种方式只要知道类的字符串名称就可以获取该类的字节码文件对象，扩展性强
     */
    private static void classDemo3() throws ClassNotFoundException {
        Class<Person> clazz = Class.forName("pers.reflection.Person");
        System.out.println(clazz);  // class pers.reflection.Person
        Person p = clazz.newInstance();	// 调用空参构造函数创建Person对象，如果clzz没加泛型，则拿到的是Object，要强转
    }

    /**
     * 方式二： 任何数据类型都具备的一个静态属性 .class
     *  这种方式相对简单，但还是要明确到该类的静态成员，扩展性也不强
     */
    private static void classDemo2() {
        Class clazz = Person.class;
        System.out.println(clazz);  // class pers.reflection.Person
    }

    /**
     * 方式一： Object类的getClass()方法
     *  这种方式必须要明确具体的类，并获取到该类对象，扩展性差
     */
    private static void classDemo1() {
         Person p = new Person();
         Class clazz = p.getClass();
         System.out.println(clazz);  // class pers.reflection.Person
    }
```

- Class对象**获取构造函数**的方法：
 - `Constructor<T> getConstructor(Class<?>... parameterTypes)` —— 按参数列表获取该类的构造函数（公有的）
 - `Constructor<?>[] getConstructors()` —— 获取包含该类所有公有构造函数的数组
 - `Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)` —— 按参数列表获取该类的构造函数（包括私有的）
 - `Constructor<?>[] getDeclaredConstructors()` —— 获取包含该类所有构造函数（包括私有构造函数）的数组
- Class对象**获取字段**的方法：
 - `Field getField(String name)` —— 通过字段名获取该类的公有字段（包括父类的）
 - `Field[] getFields()` —— 获取包含该类所有公有字段的数组（包括父类的）
 - `Field getDeclaredField(String name)` —— 通过字段名获取该类的字段（包括私有的，但不包括父类的）
 - `Field[] getDeclaredFields()` —— 获取包含该类所有字段的数组（包括私有字段，但不包括父类的字段）
- Class对象**获取函数**的方法：
 - `Method getMethod(String name, Class<?>... parameterTypes)` —— 通过函数名和参数列表获取该类公有的一般函数（包括继承自父类的）
 - `Method[] getMethods()` —— 获取包含该类所有公有的一般函数的数组（包括继承自父类的）
 - `Method getDeclaredMethod(String name, Class<?>... parameter Types)` —— 通过函数名和参数列表获取该类的一般函数（包括私有函数，但不包括继承自父类的函数）
 - `Method[] getDeclaredMethods()` —— 获取包含该类所有一般函数的数组（包括私有函数，但不包括继承自父类的函数）

### Constructor（构造器）

- Constructor的方法：
 - `newInstance(Object... initagrs)` —— 创建该类对象，如果该Constructor有参数，可传入参数创建
 - `boolean setAccessible(boolean flag)` —— 设置是否暴力访问（不检查权限）

```
    /**
     * 创建空参的对象
     */
    private static void constructorDemo1() throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        // 原来创建对象的方法，过程是先根据名称，找.class文件，加载进内存，创建Class对象，再用new创建Person对象
        pers.reflection.Person p = new pers.reflection.Person();

        // 现在创建对象的方法，过程是先根据名称，找.class文件，加载进内存，创建Class对象，获取Constructor，再通过Constructor的newInstance()方法创建Person对象
        String className = "pers.reflection.Person";
        Class clazz = Class.forName(className);
        Constructor constructor = clazz.getConstructor();
        Object obj = constructor.newInstance();	

		// 如果Class类加泛型 Class<Person> clazz = Person.class，则newInstance()时拿到的直接就是Person类对象，不用强转
    }
}
```

```
    /**
     * 创建有参数的对象
     */
    private static void constructorDemo2() throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
		// 之前
        pers.reflection.Person p = new pers.reflection.Person("vv", 3);

		// 现在
        String className = "pers.reflection.Person";
        Class clazz = Class.forName(className);
        Constructor constructor = clazz.getConstructor(String.class, int.class);    // 指定了参数列表中的参数类型
        /*
        若构造函数是私有的，通过getDeclaredConstructor()只能获取到该构造器
        并不能直接newInstance()创建对象，要先setAccessible(true)，不检查权限，破坏了安全性，不建议这种做法
		这称为暴力访问（Constructor、Field、Method都可用）

        Constructor constructor = clazz.getDeclaredConstructor(String.class, int.class);
        constructor.setAccessible(true);
         */
        Object obj = constructor.newInstance("vv", 3);
    }
```

### Field（字段）

- Field的方法：
 - `void set(Object obj, Object value)` —— 设置obj对象在该字段的值为value
 - `Object get(Object obj)` —— 获取obj对象在该字段的值
 - `boolean setAccessible(boolean flag)` —— 设置是否暴力访问（不检查权限）

```
    /**
     * 通过反射 获取/设置 对象的字段值
     * 字段是对于对象来说的，因此首先要有对象
     */
    private static void fieldDemo() throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        String className = "pers.reflection.Person";
        // 获取Person类的字节码文件对象
        Class clazz = Class.forName(className);
        // 通过获取Person类的构造函数来创建Person对象
        Object p = clazz.getConstructor(String.class, int.class).newInstance("vv", 3);

        // 获取Person类的age字段对象，因为该字段是私有的，因此要用getDeclaredField()方法
        Field field = clazz.getDeclaredField("age");

        // 暴力访问，否则获取了Field对象还是不能进行操作
        field.setAccessible(true);

        // 通过age字段对象获取某个Person对象(p)在该字段的值
        Object age = field.get(p);
        System.out.println("age: " + age);    // age: 3

        // 设置某个Person对象(p)在该字段的值
        field.set(p, 4);
        System.out.println("age: " + field.get(p)); // age: 4
    }
}
```

### Method（函数）

- Method的方法：
 - `Object invoke(Object obj, Object... args)` —— 运行该类对象obj的该函数，**返回值是Object类型的该方法的返回值**

```
    /**
     * 获取Class所对应的类中的所有一般函数
     */
    private static void methodDemo1() throws ClassNotFoundException {
        Class clazz = Class.forName("pers.reflection.Person");

        // 获取该类所有公有的一般函数（包括继承自父类的函数）
        Method[] methods = clazz.getMethods();
        for (Method method : methods)
            System.out.println(method);

        System.out.println("-------");

        // 获取该类所有一般函数（包括私有函数，但不包括继承自父类的函数）
        methods = clazz.getDeclaredMethods();
        for (Method method : methods)
            System.out.println(method);
    }

输出为：
public void pers.reflection.Person.introduce()
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public java.lang.String java.lang.Object.toString()
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
-------
public void pers.reflection.Person.introduce()
private void pers.reflection.Person.secret(int)
```

```
    /**
     * 获取空参的函数并运行
     */
    private static void methodDemo2() throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        Class clazz = Class.forName("pers.reflection.Person");
        // 创建该类对象
        Object p = clazz.getConstructor(String.class, int.class).newInstance("vv", 3);  // 用的是参数列表为(String, int)的构造函数，初始值是("vv", 3)
        // 获取函数名为introduce()的一般函数（空参）
        Method method = clazz.getMethod("introduce");
        // 运行该类对象对象p的该函数，该函数无参数
        method.invoke(p); // My name is vv, and I'm 3 years old.
    }
```

```
    /**
     * 获取有参数的一般函数并执行
     */
    private static void methodDemo3() throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        Class clazz = Class.forName("pers.reflection.Person");
        // 创建该类对象
        Object p = clazz.getConstructor(String.class, int.class).newInstance("vv", 3);
        // 获取名为secret(int money)的私有的一般函数
        Method method = clazz.getDeclaredMethod("secret", int.class);
        // 暴力访问，否则获取了也不能进行操作，因为是私有函数
        method.setAccessible(true);
        // 运行该类对象的该函数，要传入参数
        method.invoke(p, 777);  // My name is vv, I'm 3 years old, I have 777 dollars.
    }
```

- **如果是调用静态成员（静态属性/静态函数），则对象参数可以写任意对象，如null或clazz等（不能不写）**

### 父类、接口

下面用到的类
```
class Creature<String> implements Serializable { }

@JavaBean
class Dog extends Creature<String> implements Comparable {
    @Override
    public int compareTo(Object o) { return 0;  }
}
```

```
    /**
     * 获取运行时类的父类、带泛型的父类、带泛型父类的泛型
     */
    private static void demo1() {
        // 获取运行时类的父类
        Class clazz = Dog.class;
        Class superclass = clazz.getSuperclass();
        System.out.println(superclass); // class pers.reflection.Creature

        // 获取运行时类带泛型的父类
        Type genericSuperclass = clazz.getGenericSuperclass();
        System.out.println(genericSuperclass);  // pers.reflection.Creature<java.lang.String>

        // 获取运行时类带泛型的父类的泛型
        ParameterizedType parameterizedType = (ParameterizedType) genericSuperclass;
        Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
        for (Type type : actualTypeArguments)
            System.out.println(type.getTypeName()); // java.lang.String
    }
```

```
    /**
     * 获取运行时类的接口、父类的接口
     */
    private static void demo2() {
        // 获取运行时类的接口
        Class clazz = Dog.class;
        Class[] interfaces = clazz.getInterfaces();
        for (Class c : interfaces)
            System.out.println(c);  // interface java.lang.Comparable

        // 获取运行时类父类的接口
        Class superclass = clazz.getSuperclass();
        Class[] interfaces1 = superclass.getInterfaces();
        for (Class c : interfaces1)
            System.out.println(c);  // interface java.io.Serializable
    }
```

### 所在包

```
    /**
     * 获取运行时类所在的包
     */
    private static void demo3() {
        Class clazz = Dog.class;
        Package pack = clazz.getPackage();
        System.out.println(pack);   // package pers.reflection
    }
```

### 注解

```
    /**
     * 获取运行时类所声明的注解
     */
    private static void demo4() {
        Class clazz = Dog.class;
        Annotation[] annotations = clazz.getAnnotations();
        for (Annotation a : annotations)
            System.out.println(a);  // @java.beans.JavaBean(defaultProperty="", description="", defaultEventSet="")
    }
```

### 反射与封装性的理解

- 什么时候用反射，什么时候直接new对象？
 - 一般情况下直接new对象；而反射体现动态性，当编译时不确定要new哪个类对象时，可以用反射
- 反射可以调用私有成员，那么反射与封装性是不是相互矛盾？
 - 不矛盾。封装性是提示调用公有的成员比调用私有的成员更好，但如果反射非要调用私有的成员，也可以调

---

练习：利用反射机制实现电脑USB接口的扩展

配置文件configUSB.properties中的内容如下：
```
USB1 = pers.reflection.USBMouse
USB2 = pers.reflection.USBFlashDisk
```

想要添加USB设备，只需要在该配置文件中添加该USB类名称信息即可

```
public class ReflectExercise {
    public static void main(String[] args) {
        PC pc = new PC("MyPC");
        run(pc);
    }

    private static void run(PC pc) {
        if (pc == null)
            throw new RuntimeException("PC不存在！");

        pc.open();

        try {
            pc.runAllUSB();
        } catch (IOException e) {
            throw new RuntimeException("USB配置文件读取错误！");
        }
        pc.close();
    }
}

class PC {
    private String pcName;

    public PC() { this.pcName = "NoName"; }

    public PC(String pcName) {
        this.pcName = pcName;
    }

    public void open() {
        System.out.println(this.pcName + " open...");
    }

    public void close() {
        System.out.println(this.pcName + " close...");
    }

    public void runAllUSB() throws IOException {
        File config_file = new File("MyTest\\configUSB.properties");
        if (!config_file.exists())
            throw new RuntimeException("USB配置文件不存在！");

        FileInputStream fis = new FileInputStream(config_file);
        Properties prop = new Properties();
        prop.load(fis);

        Class clazz = null;
        for (int i = 1; i <= prop.size(); i++) {
                try {
                    clazz = Class.forName(prop.getProperty("USB" + i));
                    USBInterface usb = (USBInterface) clazz.getConstructor().newInstance();
                    usb.open();
                    usb.close();
                } catch (Exception e) {
                    throw new RuntimeException("配置文件内容或类字节码文件错误！");
                }
        }
    }
}

interface USBInterface {
    void open();
    void close();
}

class USBMouse implements USBInterface {
    public USBMouse() { }

    @Override
    public void open() {
        System.out.println("USB Mouse open...");
    }

    @Override
    public void close() {
        System.out.println("USB Mouse close...");
    }
}

class USBFlashDisk implements USBInterface {
    public USBFlashDisk() { }

    @Override
    public void open() {
        System.out.println("USB Flash Disk open...");
    }

    @Override
    public void close() {
        System.out.println("USB Flash Disk close...");
    }
}

输出为：
MyPC open...
USB Mouse open...
USB Mouse close...
USB Flash Disk open...
USB Flash Disk close...
MyPC close...
```

## 代理

- 代理：
 - 静态代理：客户通过代理类来调用其他对象的方法，代理对象和被代理对象在编译时已确定
 - **动态代理**：客户通过代理类来调用其他对象的方法，并且是**在程序运行时根据需要动态创建目标类的代理对象**
- **动态代理可以看作反射机制的一种应用**

**静态代理**

- 静态代理的缺点：
 1. 代理类和被代理类在编译的时候就写死了
 2. 不同的被代理类需要不同的代理类来代理，造成代理类过多

- 其实实现Runnable接口创建多线程就可以看作是一种静态代理：
 - `class MyThread implements Runnable { }` 相当于被代理类
 - `class Thread implements Runnable { }` 相当于代理类
 - `new Thread(new MyThread()).start();` 其实start()方法内部就调用了MyThread的run()方法

```
public class StaticProxyDemo {
    public static void main(String[] args) {
        Factory factory = new ProxyFactory(new NikeFactory());
        factory.produce();
    }
}

// 接口
interface Factory {
    void produce();
}

// 被代理类
class NikeFactory implements Factory {
    @Override
    public void produce() {
        System.out.println("Nike工厂生产中...");
    }
}

// 代理类
class ProxyFactory implements Factory {
    private Factory factory;

    public ProxyFactory(Factory factory) {
        this.factory = factory;
    }

    @Override
    public void produce() {
        System.out.println("代理工厂做准备工作...");
        factory.produce();
        System.out.println("代理工厂做收尾工作...");
    }
}

-------
代理工厂做准备工作...
Nike工厂生产中...
代理工厂做收尾工作...
```

**动态代理**
 - 问题一：如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象
 - 问题二：当通过代理类的对象调用方法a时，如何动态的调用被代理类中的同名方法a

```
public class ProxyDemo {
    public static void main(String[] args) {
        // 创建被代理对象
        SuperMan superMan = new SuperMan("Cirs", 21);
        // 获取代理对象proxyInstance
        Human proxyInstance = (Human) MyProxy.getProxyInstance(superMan);
        // 通过代理对象，调用被代理对象的方法
        proxyInstance.introduce();  // I'm Cirs, and I'm 21 years old.
        proxyInstance.eat("酱香饼");   // I'm eating 酱香饼...

        System.out.println("-------");

        NikeFactory nikeFactory = new NikeFactory();
        Factory proxyInstance2 = (Factory) MyProxy.getProxyInstance(nikeFactory);
        proxyInstance2.produce();   // Nike工厂生产中...
    }
}

// 接口
interface Human {
    void introduce();
    void eat(String food);
}

// 被代理类
class SuperMan implements Human {
    private String name;
    private int age;

    public SuperMan(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public void introduce() {
        System.out.println("I'm " + this.name + ", and I'm " + this.age + " years old.");
    }

    @Override
    public void eat(String food) {
        System.out.println("I'm eating " + food + "...");
    }
}

class MyProxy {
    /**
     * @param obj 被代理对象
     * @return 代理对象
     * 传入被代理对象，动态的创建并获取代理对象
     */
    public static Object getProxyInstance(Object obj) {
        // InvocationHandler用于解决问题二
        MyInvocationHandler handler = new MyInvocationHandler();
        // InvocationHandler关联被代理对象
        handler.bind(obj);
        // Proxy.newProxyInstance(ClassLoader, interfaces[], InvocationHandler)用于解决问题一
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), handler);
    }
}

class MyInvocationHandler implements InvocationHandler {
    // 被代理对象
    private Object obj;

    public void bind(Object obj) {
        this.obj = obj;
    }

    /**
     * @param proxy 代理对象
     * @param method 代理对象调用的方法，也即作为被代理对象要调用的方法
     * @param args 代理对象调用的方法的参数列表
     * @return 被代理对象调用同名方法的返回值
     * @throws Throwable
     */
    // 当代理对象调用方法a时，会自动调用如下invoke()方法
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 调用被代理对象的同名方法，并返回该返回值
        return method.invoke(this.obj, args);
    }
}
```

# 正则表达式

- 正则表达式用于**操作字符串数据**
- 正则表达式是通过一些特定的符号来体现的
- 正则表达式**简化了书写，但阅读性差**
- String类的`boolean matches(String regex)`方法，用于判断字符串是否匹配正则表达式regex定义的规则
- String类的`String[] split(String regex)`方法，可以根据正则表达式regex的规则切割字符串
- String类的`String replaceAll(String regex, String replacement)`方法，可将字符串符合正则表达式regex定义的规则的部分用replacement替换

```
    /**
     * 需求：校验QQ号是否符合规则，规则如下：长度5~15位，只能是数字，不能以0开头
     *  可用String的 boolean matches(String regex) 方法
     */
    private static void regexDemo1() {
        // 定义正则表达式
        String regex = "[1-9][0-9]{4,14}";  // 第1位取1到9，第2位取0到9，之后最少4位，最多14位，都和第2位一样取0到9

        // 将QQ号与正则表达式用String的matches(String regex)进行匹配，得到结果
        String qq = "12345";
        System.out.println(qq + " : " +  qq.matches(regex));    // 12345 : true

        qq = "123456789012345";
        System.out.println(qq + " : " + qq.matches(regex)); // 123456789012345 : true

        qq = "012345";
        System.out.println(qq + " : " + qq.matches(regex)); // 012345 : false

        qq = "1234";
        System.out.println(qq + " : " + qq.matches(regex)); // 1234 : false

        qq = "12345678901234567";
        System.out.println(qq + " : " + qq.matches(regex));// 12345678901234567 : false

        qq = "123v456";
        System.out.println(qq + " : " + qq.matches(regex)); // 123v456 : false
    }
```

- 正则表达式一些常见的符号：
 - `[abc]` —— a 或 b 或 c
 - `[^abc]` —— 除了a、b、c之外的其他字符
 - `[a-zA-Z]` —— 所有大小写字母
 - `[ad-z]` —— a 或 d到z
 - `.` —— 任意字符
 - `\d` —— 任意0到9的数字
 - `\D` —— 除了0到9之外的其他字符
 - `\w` —— 0到9的数字 或 任意大小写字母 或 下划线_
 - `\W` —— 和\w相反
 - `^` —— 行开头
 - `$` —— 行结尾
 - `\B` —— 词边界
 - `X?` —— X字符**出现0次或1次（至多出现1次）**
 - `X*` —— X字符**出现0次或1次或多次（出不出现都行，出现几次都行）**
 - `X+` —— X字符**出现1次或多次（至少出现1次）**
 - `X{n}` —— X字符**出现n次**
 - `X{n,}` —— X字符**至少出现n次**
 - `X{n, m}` —— X字符**至少出现n次，至多出现m次**

```
	/**
     *   数量词
     *      X?          X字符出现0次或1次（至多出现1次）
     *      X*          X字符出现0次或1次或多次（出不出现都行，出现几次都行）
     *      X+          X字符出现1次或多次（至少出现1次）
     *      X{n}        X字符出现n次
     *      X{n,}       X字符出现至少n次
     *      X{n, m}     X字符至少出现n次，至多出现m次
     */
    private static void regexDemo2() {
        String regex = "ab?c";  // a和c之间b出现0次或1次

        String str = "ac";
        System.out.println(str.matches(regex)); // true
        str = "abc";
        System.out.println(str.matches(regex)); // true
        str = "abbc";
        System.out.println(str.matches(regex)); // false

        System.out.println("-------");

        regex = "ab*c"; // a和c之间b出现0次或1次或多次

        str = "ac";
        System.out.println(str.matches(regex)); // true
        str = "abc";
        System.out.println(str.matches(regex)); // true
        str = "abbc";
        System.out.println(str.matches(regex)); // true

        System.out.println("-------");

        regex = "ab+c"; // a和c之间b出现1次或多次

        str = "ac";
        System.out.println(str.matches(regex)); // false
        str = "abc";
        System.out.println(str.matches(regex)); // true
        str = "abbc";
        System.out.println(str.matches(regex)); // true

        System.out.println("-------");

        regex = "ab{2}c";   // a和c之间b出现2次

        str = "abc";
        System.out.println(str.matches(regex)); // false
        str = "abbc";
        System.out.println(str.matches(regex)); // true
        str = "abbbc";
        System.out.println(str.matches(regex)); // false

        System.out.println("-------");

        regex = "ab{2,}c";  // a和c之间b至少出现2次

        str = "abc";
        System.out.println(str.matches(regex)); // false
        str = "abbc";
        System.out.println(str.matches(regex)); // true
        str = "abbbc";
        System.out.println(str.matches(regex)); // true

        System.out.println("-------");

        regex = "ab{2,3}c"; // a和c之间b至少出现2次，至多出现3次

        str = "abc";
        System.out.println(str.matches(regex)); // false
        str = "abbc";
        System.out.println(str.matches(regex)); // true
        str = "abbbc";
        System.out.println(str.matches(regex)); // true
        str = "abbbbc";
        System.out.println(str.matches(regex)); // false
    }
```

## 正则表达式的功能

- 正则表达式的功能（对字符串）主要有：
 - **匹配**
 - **切割**
 - **替换**
 - **获取**

**正则表达式功能之 切割**
- 用的是String类的`String[] split(String regex)`方法
- 引入组的概念，组用()括起来，并会自动编号，编号按左括号从1开始编，如在正则表达式`((A)(B(C)))`中，`(A)(B(C))`是第1组，`A`是第2组，`B(C)`是第3组，`C`是第4组，而第0组始终代表整个表达式；用`\i`表示第i组，在字符串中则表示为`\\i`

```
    /**
     * 正则表达式 切割 字符串
     */
    private static void regexFunction_split() {
        // 用多个空格切割
        String str = "vv    cris jdt     lw";
        String regex = " +";    // 1个或多个空格
        String[] strings = str.split(regex);
        for (String string : strings)
            System.out.println(string);

        System.out.println("-------");

        // 用三个及以上的叠词切割
        str = "vvtttcrisxxxxjdtqqqqqlw";
        regex = "(.)\\1{2,}";    // 任意字符"."出现且作为组1，"\\1{2,}"表示组1再次出现2次或2次以上，即该字符总共连续出现3次，而直接"(.){3,}"其实是三个可能不同的任意字符
        strings = str.split(regex);
        for (String string : strings)
            System.out.println(string);
    }
```

**正则表达式功能之 匹配**
- 用的是String类的`boolean matches(String regex)`方法

```
    /**
     * 正则表达式 匹配 字符串
     *      需求：匹配（校验）手机号码
     */
    private static void regexFunction_match1() {
        // 定义手机号匹配规则
        String regex = "1[3578]\\d{9}"; // 第1位是1，第2位是3或5或7或8，后面9位是任意数字，相当于 String regex = "1[3578][0-9]{9}";

        String tel = "13823853327";
        boolean b = tel.matches(regex);
        System.out.println(tel + " : " + b);    // 13823853327 : true

        tel = "18300128588";
        b = tel.matches(regex);
        System.out.println(tel + " : " + b);    // 18300128588 : true

        tel = "750115079";
        b = tel.matches(regex);
        System.out.println(tel + " : " + b);    // 750115079 : false
    }
```

```
    /**
     * 运用正则表达式中的组匹配AABB类的成语
     */
    private static void regexFunction_match2() {
        String str1 = "快快乐乐";
        String str2 = "摇摇欲坠";

        String regex1 = "(.)\\1(.)\\2"; // 第一个字符连续出现2次，第三个字符也连续出现2次
        System.out.println(str1 + " : " + str1.matches(regex1));   // 快快乐乐 : true
        System.out.println(str2 + " : " + str2.matches(regex1));   // 摇摇欲坠 : false
    }
```

**正则表达式功能之 替换**
- 用的是String类的`String replaceAll(String regex, String replacement)`方法
- 在用`replaceAll(String regex, String replacement)`时，若参数replacement需要用到参数regex中的信息时，可用"$1"代表regex中的组1，"$2"代表regex中的组2，以此类推

```
    /**
     * 正则表达式 替换 字符串
     *      "$"可以获取前一个正则表达式参数中的信息
     */
    private static void regexFunction_replace1() {
        String str = "vvtttcrisxxxxjdtqqqqqlw";

        // 将三个及以上的叠词替换成"#"
        String regex = "(.)\\1{2,}";
        String result_str = str.replaceAll(regex, "#");
        System.out.println(result_str); // vv#cris#jdt#lw

        // 将三个及以上的叠词缩减为一个该叠词
        result_str = str.replaceAll(regex, "$1");   // "&1"代表获取前一个正则表达式参数中的组1
        System.out.println(result_str); // vvtcrisxjdtqlw
    }
```

```
    /**
     * 需求：隐藏手机号码的第4-8位，如：18300128588显示为183****8588
     *      思路：将手机号分组，只拿头尾两组，中间用"*"替换
     */
    private static void regexFunction_replace2() {
        // 定义规则
        String regex = "(\\d{3})\\d{4}(\\d{4})"; // 将前3位分为组1，后4位分为组2
        String tel = "13823853327";
        String result_tel = tel.replaceAll(regex, "$1****$2");  // 参数replacement取了参数regex中的组1和组2，中间用"*"填充
                                                               // 注意此处replacement不能用"$1*{4}$2"，因为该参数不是正则表达式
                                                              // 只是从前一个正则表达式参数中获取一些组
        System.out.println(result_tel); // 138****3327
    }
```

**正则表达式功能之 获取**
- 用的是**正则对象Pattern和匹配器Matcher**
- Pattern不能创建对象，要通过`static Pattern compile(String regex)`将正则表达式regex封装成Pattern对象
- Patter类的`Matcher matcher(CharSequence input)`可和字符串相关联，并获取对字符串进行操作的匹配器Matcher对象
- Matcher类的`boolean find()`可匹配该匹配器关联的字符串中与匹配器相匹配的下一个子序列，找到则返回true
- Matcher类的`String group()`可获取到find()方法匹配到的子序列
- Mathcer类的`int start()`和`int end()`可获取到find()方法匹配到的子序列在原序列中的起始位置和结束位置（相当于String类的indexOf()方法，包含开头，不包含结尾）
- 其实正则表达式的前三种功能，虽然用的是String类，但调用的还是匹配器Matcher类

```
    /**
     * 正则表达式 获取 字符串
     *      1. 将正则表达式封装成对象Pattern，该类不能创建对象，通过 static Pattern compile(String regex) 将regex封装成Pattern对象
     *        即：Pattern p = Pattern.compile("a*b");
     *      2. 通过Pattern对象的 Matcher matcher(CharSequence input) 关联字符串，获取到对字符串进行操作的匹配器(Matcher)对象
     *        即：Matcher m = p.matches("aaaab");
     *      3. 通过Matcher匹配器对象对字符串进行操作
     *        如：m.find()可匹配下一个和匹配器匹配的子序列（匹配到则返回true），再通过m.group()可获取该匹配成功的子序列
     *           m.start()和m.end()可获取该匹配子序列在字符串中的位置，相当于String的indexOf()方法
     *
     *  需求：获取字符串中由3个字符组成的单词
     */
    private static void regexFunction_get() {
        String str = "The steps you take do not need to be big, they just need to take you in the right direction.";
        String regex = "\\b[a-zA-Z]{3}\\b"; // 任意大小写字母连续出现三次，"\b"是单词边界，如果不加，如单词take会匹配到"tak"和"ake"
        // 将正则规则封装成Pattern对象
        Pattern p = Pattern.compile(regex);
        // 关联字符串并获取Matcher对象
        Matcher m = p.matcher(str);
        // 通过Matcher对象对字符串进行操作
        while (m.find()) {
            System.out.println(m.group() + " " + m.start() + "-" + m.end());
        }
    }

输出为：
The 0-3
you 10-13
not 22-25
big 37-40
you 65-68
the 72-75
```

练习1：治疗口吃，如输入"我..我我我...爱爱...爱中中...中中中...国国...国"，输出为"我爱中国"

```
    private static void exercise1() {
        String str = "我..我我我...爱爱...爱中中...中中中...国国...国";
        System.out.println(str);    // 我..我我我...爱爱...爱中中...中中中...国国...国

        str = str.replaceAll("\\.", "");    // 将字符串中的"."去掉
        System.out.println(str);    // 我我我我爱爱爱中中中中中国国国

        str = str.replaceAll("(.)\\1+", "$1");  // 将字符串中的叠词缩减
        System.out.println(str);    // 我爱中国
    }
```

练习2：对IP地址进行排序，如"127.0.0.1  10.11.154.7   192.168.10.34"，排序后为"10.11.154.7"、"127.0.0.1"、"192.168.10.34"

解法2：

```
    /**
     * 对IP地址进行排序，如"127.0.0.1  10.11.154.7   192.168.10.34"，排序后为"10.11.154.7"、"127.0.0.1"、"192.168.10.34"
     *  思路：TreeSet可以进行排序，但因为IP地址每一段的位数可能不同，不能直接用字符串的自然排序方式
     *       可以先按最大所需补0数，将所有位都补2个0，再取低3位，这样IP地址每一段都变成3位
     *       每一段位数相同后，可用字符串的自然排序方式对IP地址进行排序，排完序后再把多余的0去掉
     */
    private static void exercise2_2() {
        String ip_str = "127.0.0.1  10.11.154.7   192.168.10.34 138.238.53.1";
        System.out.println(ip_str); // 127.0.0.1  10.11.154.7   192.168.10.34 138.238.53.1

        // 按最大所需位数补0，每一段都补2个0
        ip_str = ip_str.replaceAll("(\\d+)", "00$1");
        System.out.println(ip_str); // 00127.000.000.001  0010.0011.00154.007   00192.00168.0010.0034 00138.00238.0053.001

        // 每一段都取低3位，使每一段都是3位，以便用字符串的自然排序方式进行排序
        ip_str = ip_str.replaceAll("0*(\\d{3})", "$1");
        System.out.println(ip_str); // 127.000.000.001  010.011.154.007   192.168.010.034 138.238.053.001

        // 用空格切割字符串，分割出每一个IP地址
        String[] ips = ip_str.split(" +");
        System.out.println("排序前：");
        for (String ip : ips)
            System.out.println(ip);

        // 利用TreeSet排序，排序后把多余的0去掉
        TreeSet<String> ts = new TreeSet<>();
        for (String ip : ips)
            ts.add(ip);
        System.out.println("排序后：");
        for (String ip : ts)
            System.out.println(ip.replaceAll("0*(\\d+)", "$1"));    // 把多余的0去掉，取有效位
    }

输出为：
127.0.0.1  10.11.154.7   192.168.10.34 138.238.53.1
00127.000.000.001  0010.0011.00154.007   00192.00168.0010.0034 00138.00238.0053.001
127.000.000.001  010.011.154.007   192.168.010.034 138.238.053.001
排序前：
127.000.000.001
010.011.154.007
192.168.010.034
138.238.053.001
排序后：
10.11.154.7
127.0.0.1
138.238.53.1
192.168.10.34
```

解法1：（麻烦）

```
    /**
     * 对IP地址进行排序，如"127.0.0.1  10.11.154.7   192.168.10.34"，排序后为"10.11.154.7"、"127.0.0.1"、"192.168.10.34"
     */
    private static void exercise2_1() {
        String ip_str = "127.0.0.1  10.11.154.7   192.168.10.34 138.238.53.1";

        String[] ips = ip_str.split(" +"); // 用空格（1个或多个）切割字符串
        System.out.println("排序前：");
        for (String ip : ips)
            System.out.println(ip);

        int max = 0;
        for (int i = 0; i < ips.length - 1; i++) {
            for (int j = i + 1; j < ips.length; j++) {
                if (ipCompare(ips[i], ips[j]) > 0)
                    swap(ips, i, j);
            }
        }

        System.out.println("排序后：");
        for (String ip : ips)
            System.out.println(ip);
    }

    private static int ipCompare(String ip1, String ip2) {
        String regex = "\\d{1,3}";
        Pattern p = Pattern.compile(regex);
        Matcher m1 = p.matcher(ip1);
        Matcher m2 = p.matcher(ip2);
        for (int i = 0; i < 4; i++) {
            m1.find();
            m2.find();
            int ip1Part = Integer.parseInt(m1.group());
            int ip2Part = Integer.parseInt(m2.group());
            if (ip1Part < ip2Part)
                return -1;
            else if (ip1Part > ip2Part)
                return 1;
        }
        return 0;
    }

    private static void swap(String[] arr, int i, int j) {
        String temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
```

练习3：校验邮箱

```
    /**
     * 需求：校验邮箱是否正确，规则：用户名由数字、字母、下划线组成，邮箱名由数字、字母组成，域名可有多个，每个由2到3个字母组成
     *       如："Cris_JiangDT@foxmail.com"中，"Cris_JiangDT"为用户名，"foxmail"为邮箱名，".com"为域名
     */
    private static void exercise3() {
        String mail_str = "Cris_JiangDT@foxmail.com";
        String regex = "[a-zA-Z0-9_]+@[a-zA-Z0-9]+(\\.[a-z]{2,3})+";
        boolean b = mail_str.matches(regex);
        System.out.println(mail_str + " : " + b);   // Cris_JiangDT@foxmail.com : true
    }
```

练习4：爬虫

```
    /**
     * 爬虫
     */
    private static void exercise4() throws IOException {
        // 爬的数据源，选取的是直播吧上的一个比赛战报网页（也可以是本地文件等其他源）
        URL url = new URL("https://news.zhibo8.cc/zuqiu/2019-10-13/226217.htm");
        
        // 用于存储爬到的数据
        List<String> list = new ArrayList<>();
        
        // 爬取战报中提到的所有分钟数，并存储到list中
        String regex = "\\d+分钟";
        BufferedReader in = new BufferedReader(new InputStreamReader(url.openStream()));
        String line = null;
        while ((line = in.readLine()) != null) {
            Pattern p = Pattern.compile(regex);
            Matcher m = p.matcher(line);
            while (m.find())
                list.add(m.group());
        }

        for (String min : list)
            System.out.println(min);    // 输出为该战报中所提到的所有分钟数
    }
```
---

# ...

## JavaBean

- JavaBean是一种Java写成的可重用组件，是符合以下标准的Java类：
 - 类是公共的
 - 有一个空参的公共构造器
 - 有属性，且属性有对应的get、set方法

## MVC设计模式

- MVC设计模式是最常用的设计模式之一，将整个程序分为三层：数据模型层(Model)、视图模型层(View)、控制器层(Controller)

## 工厂设计模式

- 工厂设计模式分为**简单工厂模式**（静态工厂模式）和**工厂方法模式**
- **简单工厂模式将创建者和调用者分离**，提供xxxFactory类，专门用于创建对象，缺点是若新增产品，需要修改代码，**不利于扩展**
- **工厂方法模式**提高了扩展性，新增产品时已有的代码不用改动，但也没有真正避免代码修改
- Spring中利用Java的反射机制与配置文件的巧妙结合解决了这一问题

```
    /**
     * 简单工厂模式
     */
    private static void factoryDemo1() {
        Audi audi = new CarFactory().getAudi();
        BYD byd = new CarFactory().getBYD();
        audi.run();
        byd.run();
    }

interface Car {
    void run();
}

class Audi implements Car {
    @Override
    public void run() { System.out.println("奥迪run..."); }
}

class BYD implements Car {
    @Override
    public void run() { System.out.println("比亚迪run..."); }
}

class CarFactory {
    public Audi getAudi() { return new Audi(); }

    public BYD getBYD() { return new BYD(); }
}
```

```
    /**
     * 工厂方法模式
     */
    private static void factoryDemo2() {
        Audi audi = new AudiFactory().getCar();
        BYD byd = new BYDFactory().getCar();
        audi.run();
        byd.run();
    }


interface Car { 
	void run();
}

class Audi implements Car {
    @Override
    public void run() { System.out.println("奥迪run..."); }
}

class BYD implements Car {
    @Override
    public void run() { System.out.println("比亚迪run..."); }
}

interface CarFactory2 {
    Car getCar();
}

class AudiFactory implements CarFactory2 {
    @Override
    public Audi getCar() { return new Audi(); }
}

class BYDFactory implements CarFactory2 {
    @Override
    public BYD getCar() { return new BYD(); }
}
```
## 枚举类

- 枚举类：**类的对象是有限个**，且是**确定的**
- 当需要**定义一组常量**时，强烈建议**使用枚举类**
- 如果枚举类中只有一个对象，则可以作为单例模式的实现方式

自定义枚举类（JDK5.0之前）
```
    /**
     * JDK5.0之前，需要自定义枚举类
     */
    private static void demo1() {
        Season spring = Season.Spring;
        System.out.println(spring);
    }

// 自定义枚举类Season
class Season {
    // 声明属性，用private final
    private final String seasonName;
    private final String seasonDesc;

    // 私有化构造器，并给属性赋值
    private Season(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    // 创建有限个确定的对象，用public static final
    public static final Season Spring = new Season("春天", "春暖花开");
    public static final Season Summer = new Season("夏天", "夏日炎炎");
    public static final Season Autumn = new Season("秋天", "秋高气爽");
    public static final Season Winter = new Season("冬天", "冰天雪地");

    // 其他需求：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }

    // 提供toString()方法

    @Override
    public String toString() {
        return this.seasonName + " " + this.seasonDesc;
    }
}
```

使用enum关键字定义枚举类（JDK5.0开始）：默认继承于java.lang.Enum类
```
    /**
     * JDK5.0开始，可以使用enum关键字定义枚举类
     * 说明：声明枚举类对象（必须写在最前面）时形式： 对象名(属性1,...) （对象之间用","隔开，且不能再写多余的public static final）
     *       使用enum关键字定义的枚举类默认继承于java.lang.Enum类，toString()方法被重写过，返回的是该枚举类对象的对象名
     *       
     * Enum类的常用方法：
     *      toString()：返回对象名
     *      values()：返回包含该枚举类所有对象的数组
     *      valueOf(String objName)：通过字符串形式的对象名，找到枚举类中该同名对象（若找不到该对象，则抛出异常）
     */
    private static void demo2() {
        Season1 summer = Season1.SUMMER;
        System.out.println(summer); // SUMMER (其父类Enum类重写了toString()方法，返回的是对象名)
        System.out.println(summer.getClass().getSuperclass());  // class java.lang.Enum

        System.out.println("-------");

        // Enum类的values()方法 返回包含该枚举类中所有对象的数组
        Season1[] seasons = Season1.values();
        for (Season1 season : seasons)
            System.out.println(season); // SPRING SUMMER AUTUMN WINTER

        Thread.State[] states = Thread.State.values();
        for (Thread.State state : states)
            System.out.println(state);  // NEW RUNNABLE BLOCKED WAITING TIMED_WAITING TERMINATED

        System.out.println("-------");

        // valueOf(String objName)方法 通过字符串形式的对象名，返回枚举类中该同名对象
        Season1 autumn = Season1.valueOf("AUTUMN");
        System.out.println(autumn); // AUTUMN
    }

// 使用enum关键字定义枚举类Season1
enum Season1 {
    // 提供枚举类对象，必须写在最前面，形式是 对象名1(属性1,...),对象名2(属性1,...);
    SPRING("春天", "春天来了"),
    SUMMER("夏天", "夏天来了"),
    AUTUMN("秋天", "秋天来了"),
    WINTER("冬天", "冬天来了");

    // 声明枚举类的属性，用private final
    private final String seasonName;
    private final String seasonDesc;

    // 私有构造器，可以省略private
    Season1(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    // 提供获取属性的方法
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }
}
```

- 使用**Enum类实现接口**时，**可以实现枚举的对象分别覆写**接口的**抽象方法**
- 若枚举类中有抽象方法，枚举类的每个实例也要分别实现该抽象方法，和实现接口时一样

```
interface InterfaceA {
    void show();
}

// 使用Enum类实现接口，可以实现不同枚举对象分别覆写接口的抽象方法
enum Sex implements InterfaceA {
    MALE {
        @Override
        public void show() { System.out.println("男人"); }
    }, FEMALE {
        @Override
        public void show() { System.out.println("女人"); }
    };

    Sex() {}
}
```

- Enum类的常用方法
 - `String name()` 获取枚举对象的名称
 - `int ordinal()` 获取枚举对象的下标
 - （静态）`Xxx valueOf(String name)` 通过枚举对象的名称获取枚举对象 （API中找不到的方法，编译时才会有）
 - （静态）`Xxx[] values()` 获取所有枚举对象（API中找不到的方法，编译时才会有）


```
    /**
     * Enum类的常用方法
     *      String name() 获取枚举对象的名称
     *      int ordinal() 获取枚举对象的下标
     *      （静态）Xxx valueOf(String name) 通过枚举对象的名称获取枚举对象 （API中找不到的方法，编译时才会有）
     *      （静态）Xxx[] values() 获取所有枚举对象（API中找不到的方法，编译时才会有）
     */
    private static void demo3() {
        // 通过枚举对象获取其名称和下标
        Season1 winter = Season1.WINTER;
        String name1 = winter.name();
        int index1 = winter.ordinal();
        System.out.println("name1 = " + name1);
        System.out.println("index1 = " + index1);

        // 通过枚举对象的名称获取其对象和下标
        String name2 = "SPRING";
        Season1 spring = Season1.valueOf(name2);
        int index2 = spring.ordinal();
        System.out.println("index2 = " + index2);

        // 通过枚举对象的下标获取枚举对象和其名称
        int index3 = 2; // 第3个
        Season1[] seasons = Season1.values();
        Season1 autumn = seasons[index3];
        String name3 = autumn.name();
        System.out.println("name3 = " + name3);
    }
```

## 注解(Annotation)

- 注解是JDK5.0新增的功能
- 注解就是代码里的一些**特殊标记**（如`@Override`），这些标记可以在编译、类加载、运行时被读取，可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充信息
- 在JavaEE中，注解占据了重要角色
- 可以说：**框架 = 注解 + 反射 + 设计模式**
- 如何**自定义注解**？参照@SuppressWarnings来自定义
 1. 注解声明为`@interface`
 2. 内部成员定义，通常用value表示
 3. 可以指定内部成员的默认值，用default
 4. 如果自定义注解没有内部成员，表明是标识作用
 - 如果注解有内部成员，在使用注解时，需要指明内部成员的值（除非内部成员定义有默认值）
 - 自定义注解必须配上注解的信息处理流程（使用反射）才有意义
 - 自定义注解一般都会声明2个元注解：`@Rentation`和`@Target`
- **元注解：对现有的注解进行修饰的注解**
- JDK中4种元注解：
 - `@Rentation`：指定所修饰的Annotation的生命周期：SOURCE/CLASS(默认)/RUNTIME**（只有声明为RUNTIME生命周期的注解，才能通过反射获取）**
 - `@Target`：指定所修饰的Annotation能修饰哪些程序元素
 - `@Documented`：表示所修饰的Annotation在被javadoc解析时保留下来
 - `@Inherited`：表示所修饰的Annotation具有继承性，声明了该Annotation的类的子类将自动声明该Annotation
- 注解 JDK8的新特性：
 - 可重复注解
 - 类型注解 

# JDK8新特性

## 接口

- **JDK8开始，接口中**除了定义抽象方法和全局常量外，还**可以定义静态方法、默认方法**
- **接口中的静态方法只能接口自己调用，不能被其实现类调用**
- **接口中的默认方法可以被其实现类调用或重写**（使接口往工具类的方向靠拢）
 - **实现类中想调用接口中的默认方法（已被重写）要用 `接口.super.方法名()`** （相当于子类调用父类被重写过的方法时要用 `super.方法名()`）
- JDK内置的3个基本注解：`@Override`、`@Deprecated`、`@SuppressWarnings`

```
    /**
     * Java8开始，接口可以定义静态方法和默认方法
     * 接口的静态方法只能接口自己调用，其实现类无法调用
     * 接口的默认方法可以被其实现类调用或重写，接口自己无法调用
     */
    private static void demo1() {
        InterfaceA.method1();   // Interface A method1 run...

        AImpl impl = new AImpl();
        impl.method2(); // AImpl method2 run...
    }

interface InterfaceA {
    // 静态方法(省略了public)，接口的实现类无法调用，只能接口自己调用
    static void method1() { System.out.println("Interface A method1 run..."); }

    // 默认方法(省略了public)，接口的实现类可以调用，也可以重写
    default void method2() { System.out.println("Interface A method2 run..."); }
}

class AImpl implements InterfaceA {
    @Override
    public void method2() { System.out.println("AImpl method2 run..."); }
}
```

- 如果子类（或继承类）**继承的父类和实现的接口中声明了同名同参数的方法**（在接口中是默认方法），且该**子类没有重写**该方法，则运行时**调用的是父类中的方法**，而不是接口中的方法**（注意：如果是属性而不是方法，则编译会报错）**

```
    /**
     * 子类（继承类）的父类和实现的接口有同名同参数的方法时（在接口中是默认方法），默认调用父类的方法（子类未重写的情况下）
     * 注意：如果父类和实现的接口有同名的参数，编译会报错
     */
    private static void demo2() {
        SubB sub = new SubB();
        sub.function(); // Class B function run...
    }

interface InterfaceB {
    default void function() { System.out.println("Interface B function run..."); }
}

class ClassB {
    public void function() { System.out.println("Class B function run..."); }
}

class SubB extends ClassB implements InterfaceB {}
```

- 如果实现类**继承的多个接口**中有**同名同参数的默认方法**，则实现类**必须重写该方法，否则编译报错**，因为不知道该执行哪个接口中的该方法（**如果是抽象方法，则不会报错**，相当于实现类同时实现了多个接口中的该方法）（**如果有父类，父类中也有该方法，也不会冲突**，因为会调用父类的该方法）

```
    /**
     * 如果实现类继承的多个接口中有同名同参数的默认方法，则实现类必须重写该方法，否则编译报错
     */
    private static void demo3() {
        BCImpl impl = new BCImpl();
        impl.function();    // BCImpl function run...
        impl.function2();
    }

interface InterfaceB {
    default void function() { System.out.println("Interface B function run..."); }
}

interface InterfaceC {
    default void function() { System.out.println("Interface C function run..."); }
}

class BCImpl implements InterfaceB, InterfaceC {
    // 必须重写，否则接口冲突，编译报错
    @Override
    public void function() { System.out.println("BCImpl function run..."); }

    public void function2() {
        // 在实现类中调用接口的默认方法（已被重写）的方式：接口.super.方法名()
        InterfaceB.super.function();    // Interface B function run...
        InterfaceC.super.function();    // Interface C function run...
    }
}
```

## 日期API

- JDK8之前的日期API（Date、Calendar）的问题：
 - 可变性：像日期和时间这样的类应该是不可变的
 - 偏移性：Date中的年份是对1900的偏移，月份是从0开始的
 - 格式化：Date才有格式化，而Calendar没有

### LocalDate、LocalTime、LocalDateTime

- 类似于Calendar

```
    /**
     * LocalDate、LocalTime、LocalDateTime的使用：
     *
     * 获取日期对象：
     *      LocalDate、LocalTime、LocalDateTime 的 静态 now()方法：获取当前时间的日期对象
     *      LocalDate、LocalTime、LocalDateTime 的 静态 of()方法：获取指定时间的日期对象
     *
     * 获取日期对象中的各个字段：getXxx()
     *
     * 设置日期对象的值（返回的是新的日期对象，类似String）：withXxx()
     *
     * 在日期对象基础上偏移日期（返回的是新的日期对象）：plusXxxs()、minusXxxs()
     */
    private static void demo1() {
        // now() 获取当前时间的日期对象
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();
//        System.out.println("localDate = " + localDate); // localDate = 2019-10-30
//        System.out.println("localTime = " + localTime); // localTime = 19:16:16.236788300
        System.out.println("localDateTime = " + localDateTime); // localDateTime = 2019-10-30T19:16:16.236788300

        // of() 获取指定时间的日期对象
        LocalDateTime localDateTime2 = LocalDateTime.of(1998, 10, 19, 13, 59, 59);
        System.out.println("localDateTime2 = " + localDateTime2);   // localDateTime2 = 1998-10-19T13:59:59

        // getXxx() 获取日期对象中的各个对象
//        System.out.println(localDateTime2.getYear());   // 1998
        System.out.println(localDateTime2.getDayOfYear());  // 292
        System.out.println(localDateTime2.getMonth());  // OCTOBER
        System.out.println(localDateTime2.getMonthValue());  // 10
//        System.out.println(localDateTime2.getDayOfMonth()); // 19
        System.out.println(localDateTime2.getDayOfWeek());  // MONDAY
//        System.out.println(localDateTime2.getHour());   // 13
//        System.out.println(localDateTime2.getMinute()); // 59
//        System.out.println(localDateTime2.getSecond()); //59

        // withXxx() 设置日期对象的值（其实是返回了新的日期对象，体现了不可变性，类似String）
        LocalDateTime localDateTime3 = localDateTime2.withDayOfMonth(27);
        System.out.println("localDateTime2 = " + localDateTime2);   // localDateTime2 = 1998-10-19T13:59:59
        System.out.println("localDateTime3 = " + localDateTime3);   // localDateTime3 = 1998-10-27T13:59:59

        // plusXxxs()、minusXxxs() 在日期对象的基础上偏移时间（返回新的日期对象，体现了不可变性）
        LocalDateTime localDateTime4 = localDateTime2.plusMonths(3);
        LocalDateTime localDateTime5 = localDateTime2.minusMonths(3);
        System.out.println("localDateTime2 = " + localDateTime2);   // localDateTime2 = 1998-10-19T13:59:59
        System.out.println("localDateTime4 = " + localDateTime4);   // localDateTime4 = 1999-01-19T13:59:59
        System.out.println("localDateTime5 = " + localDateTime5);   // localDateTime5 = 1998-07-19T13:59:59
    }
```

### Instant

- 类似于java.util.Date

```
    /**
     * Instant的使用：
     *
     * 获取当前本初子午线的标准时间：静态的now()方法
     * 添加时间的偏移量：atOffset()方法
     * 获取从1970-1-1开始的毫秒数：toEpochMilli()方法
     * 按照毫秒数创建Instant对象：静态的ofEpochMilli()
     */
    private static void demo2() {
        // now() 获取的是UTC标准时间
        Instant instant = Instant.now();
        // atOffset() 偏移时间
        OffsetDateTime offsetInstant = instant.atOffset(ZoneOffset.ofHours(8));
        System.out.println("instant = " + instant); // instant = 2019-10-30T11:46:54.625336400Z
        System.out.println("offsetInstant = " + offsetInstant); // offsetInstant = 2019-10-30T19:46:54.625336400+08:00

        // toEpochMilli() 获取从1970-1-1开始的毫秒数
        long milli = instant.toEpochMilli();
        System.out.println("milli = " + milli); // milli = 1572436014625

        // ofEpochMilli() 通过毫秒数创建Instant对象
        Instant instant1 = Instant.ofEpochMilli(milli);
        System.out.println("instant1 = " + instant1);   // instant1 = 2019-10-30T11:46:54.625Z
    }
```

### DateTimeFormatter

- 类似于SimpleDateFormat

```
    /**
     * DateTimeFormatter的使用：
     *
     * 获取对象：
     * 静态的ofLocalizedDateTime()、ofLocalizedDate()
     * （重点） 自定义样式：静态的ofPattern()方法
     *
     * 将日期对象转换成字符串：format()方法
     * 将字符串转换成日期对象：parse()方法
     */
    private static void demo3() {
        LocalDateTime localDateTime = LocalDateTime.now();

        DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM);   // FULL/LONG/MEDIUM/SHORT
        String str1 = formatter1.format(localDateTime);
        System.out.println("str1 = " + str1);   // str1 = 2019年10月30日 下午8:08:44

        /* 自定义样式（常用） */
        // 自定义格式 ofPattern()方法
        DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        // format() 将日期对象转换成字符串
        String str2 = formatter2.format(localDateTime);
        System.out.println("str2 = " + str2);   // str2 = 2019-10-30 08:08:44
        // parse() 将字符串转换成日期对象
        TemporalAccessor parse = formatter2.parse(str2);
        System.out.println("parse = " + parse); // parse = {HourOfAmPm=8, NanoOfSecond=0, MilliOfSecond=0, MicroOfSecond=0, MinuteOfHour=8, SecondOfMinute=44},ISO resolved to 2019-10-30
    }
```

## Lambda表达式

- **Lambda表达式的本质：函数式接口(Functional Interface)的具体实现类**
- 语法，例如`Comparator<Integer> comparator2 = (o1, o2) -> { return Integer.compare(o1, o2); };`
 - `->`：Lambda操作符
 - `->`左边：Lambda形参列表，其实就是接口中抽象方法的形参列表**（参数类型可省略，称为类型推断）（有且只有一个参数时，小括号`()`可以省略）**
 - `->`右边：Lambda体，其实就是重写接口中抽象方法的方法体**(当方法体中语句只有一条时，`return`和大括号`{}`都可以省略)**

```
    private static void lambdaDemo1() {
        // 以前的写法
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return Integer.compare(o1, o2);
            }
        };
        int result1 = comparator1.compare(7, 8);
        System.out.println("result1 = " + result1); // result1 = -1

        // Lambda表达式的写法
        Comparator<Integer> comparator2 = (o1, o2) -> Integer.compare(o1, o2);
        int result2 = comparator2.compare(8, 7);
        System.out.println("result2 = " + result2); // result2 = 1

        // 方法引用的写法
        Comparator<Integer> comparator3 = Integer :: compare;
        int result3 = comparator3.compare(7, 7);
        System.out.println("result3 = " + result3); // result3 = 0
    }
```

## 函数式接口（Functional Interface）

- **只有一个抽象方法的接口，称为函数式接口(Functional Interface)**
- 可以在接口上使用注解`@FunctionalInterface`来检查其是否是函数式接口

```
// 自定义函数式接口
@FunctionalInterface
interface MyFunctionalInterface {
    void show();
//    void method();    // 函数式接口中有且只能有一个抽象方法
}
```

```
    /**
     * Java内置的四大核心函数式接口及其抽象方法：
     *      消费型接口 Consumer<T>       void accept(T t)
     *      供给型接口 Supplier<T>       T get()
     *      函数型接口 Function<T, R>    R apply(T t)
     *      判断型接口 Predicate<T>      boolean test(T t)
     */
    private static void functionalDemo1() {
        // 以前的写法
        showMoney(700, new Consumer<Double>() {
            @Override
            public void accept(Double aDouble) {
                System.out.println("消费了" + aDouble + "元。");
            }
        }); // 消费了700.0元。

        // Lambda表达式的写法
        showMoney(500, money -> System.out.println("消费了" + money + "英镑！")); // 消费了500.0英镑！

    }

    private static void showMoney(double money, Consumer<Double> consumer) {
        consumer.accept(money);
    }
```

```
    private static void functionalDemo2() {
        List<String> list = Arrays.asList("中国", "美国", "越南", "葡萄牙", "阿根廷", "英国", "德国");

        // 以前的写法
        List<String> resultList1 = filterList(list, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.contains("国");
            }
        });
        System.out.println("resultList1 = " + resultList1); // resultList1 = [中国, 美国, 英国, 德国]

        // Lambda表达式的写法
        List<String> resultList2 = filterList(list, str -> str.contains("国"));
        System.out.println("resultList2 = " + resultList2); // resultList2 = [中国, 美国, 英国, 德国]
    }

    // 根据Predicate接口给定的规则，过滤List中的字符串，返回过滤后的List
    private static List<String> filterList(List<String> list, Predicate<String> pred) {
        List<String> resultList = new ArrayList<>();
        for (String str : list) {
            if (pred.test(str))
                resultList.add(str);
        }
        return resultList;
    }
```

## 方法引用

- **方法引用本质：Lambda表达式**，而Lambda表达式是函数式接口的实现类，所以方法引用也是**函数式接口的实现类**
- 当Lambda体的操作已经有方法实现时，就可以使用方法引用
- 方法引用的格式：`调用者(类/对象) :: 调用的函数名(不用写参数列表)`
- 方法引用的三种情况：
 1. 对象调用非静态方法
 2. 类(或对象)调用静态方法
 3. 类调用非静态方法
- **方法引用的要求：函数式接口的抽象函数 和 方法引用的方法 要求参数列表和返回值类型一致（情况3例外）**

情况一：对象调用非静态方法
```
    private static void methodReferDemo1() {
        // Lambda表达式的写法
        Consumer<String> consumer1 = str -> System.out.println(str);
        consumer1.accept("北京欢迎你");  // 北京欢迎你

        // 方法引用的写法
        // Lambda体是一个输出语句，可以用PrintStream的println()方法实现
        PrintStream out = System.out;
        Consumer<String> consumer2 = out :: println;
        consumer2.accept("我和你");    // 我和你
    }
```

```
    private static void methodReferDemo2() {
        Person p = new Person("jdt", 21);

        // Lambda表达式的写法
        Supplier<String> supplier1 = () -> p.getName();
        String name1 = supplier1.get();
        System.out.println("name1 = " + name1); // name1 = jdt

        // 方法引用的写法
        // Lambda体由p的getName()方法实现
        Supplier<String> supplier2 = p :: getName;
        String name2 = supplier2.get();
        System.out.println("name2 = " + name2); // name2 = jdt
    }
```

情况二：类(或对象)调用静态方法
```
    private static void methodReferDemo3() {
        // Lambda表达式的写法
        Comparator<Integer> comparator1 = (x, y) -> Integer.compare(x, y);
        int result1 = comparator1.compare(7, 8);
        System.out.println("result1 = " + result1); // result1 = -1

        // 方法引用的写法
        // Lambda体由Integer的静态方法compare()方法实现
        Comparator<Integer> comparator2 = Integer :: compare;
        int result2 = comparator2.compare(8, 7);
        System.out.println("result2 = " + result2); // result2 = 1
    }
```

```
    private static void methodReferDemo4() {
        // Lambda表达式的写法
        Function<Double, Long> func1 = d -> Math.round(d);
        long result1 = func1.apply(1.3);
        System.out.println("result1 = " + result1); // result1 = 1

        // 方法引用的写法
        // Lambda体由Math.round()实现
        Function<Double, Long> func2 = Math :: round;
        long result2 = func2.apply(1.7);
        System.out.println("result2 = " + result2); // result2 = 2
    }
```

情况三：类调用非静态方法（Lambda形参列表的第一个形参 作为 非静态方法的调用者）
```
    private static void methodReferDemo5() {
        // Lambda表达式的写法
        Comparator<String> comp1 = (s1, s2) -> s1.compareTo(s2);
        int result1 = comp1.compare("abc", "abd");
        System.out.println("result1 = " + result1); // result1 = -1

        // 方法引用的写法
        // Lambda体由String中的 int t1.compareTo(t2)实现
        Comparator<String> comp2 = String :: compareTo;
        int result2 = comp2.compare("abd", "abc");
        System.out.println("result2 = " + result2); // result2 = 1
    }
```

```
    private static void methodReferDemo6() {
        // Lambda表达式的写法
        BiPredicate<String, String> pred1 = (s1, s2) -> s1.equals(s2);
        System.out.println(pred1.test("abc", "abc"));   // true

        // 方法引用的写法
        // Lambda体由String中的 boolean s1.equals(s2)实现
        BiPredicate<String, String> pred2 = String :: equals;
        System.out.println(pred2.test("abc", "abc"));   // true
    }
```

```
    private static void methodReferDemo7() {
        Person person = new Person("JDT", 21);

        // Lambda表达式的写法
        Function<Person, String> func1 = p -> p.getName();
        System.out.println(func1.apply(person));    // JDT

        // 方法引用的写法
        // Lambda体由Person中的 String p.getName()实现
        Function<Person, String> func2 = Person :: getName;
        System.out.println(func2.apply(person));    // JDT
    }
```

## 构造器引用

- 构造器引用和方法引用类似，形式：`类名 :: new`

下面用到的Person类
```
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        System.out.println("Person(name, age) run...");
        this.name = name;
        this.age = age;
    }

    public Person(String name) {
        System.out.println("Person(name) run...");
        this.name = name;
    }

    public Person() {
        System.out.println("Person() run...");
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```
    private static void constructorReferDemo1() {
        // 调用Person的空参构造器创建Person对象
        // Lambda表达式的写法
        Supplier<Person> supp1 = () -> new Person();
        Person p1 = supp1.get();    // Person() run...
        System.out.println("p1 = " + p1);   // p1 = Person{name='null', age=0}

        // 构造器引用的写法
        Supplier<Person> supp2 = Person :: new;
        Person p2 = supp2.get();    // Person() run...
        System.out.println("p2 = " + p2);   // p2 = Person{name='null', age=0}
    }
```

```
    private static void constructorReferDemo2() {
        // 调用Person(String name)的构造器创建Person对象
        // Lambda表达式的写法
        Function<String, Person> func1 = name -> new Person(name);
        Person p1 = func1.apply("JDT"); // Person(name) run...
        System.out.println("p1 = " + p1);   // p1 = Person{name='JDT', age=0}

        // 构造器引用的写法
        Function<String, Person> func2 = Person :: new;
        Person p2 = func2.apply("LW");  // Person(name) run...
        System.out.println("p2 = " + p2);   // p2 = Person{name='LW', age=0}
    }
```

```
    private static void constructorReferDemo3() {
        // 调用Person(String name, int age)的构造器创建Person对象
        // Lambda表达式的写法
        BiFunction<String, Integer, Person> func1 = (name, age) -> new Person(name, age);
        Person p1 = func1.apply("JDT", 21); // Person(name, age) run...
        System.out.println("p1 = " + p1);   // p1 = Person{name='JDT', age=21}

        // 构造器引用的写法
        BiFunction<String, Integer, Person> func2 = Person :: new;
        Person p2 = func2.apply("LW", 3);   // Person(name, age) run...
        System.out.println("p2 = " + p2);   // p2 = Person{name='LW', age=3}
    }
```

## 数组引用

- 数组引用和构造器引用类似，即数组是要创建的对象，形式：`数组类型[] :: new`

```
    private static void arrayReferDemo1() {
        // 创建指定长度的String数组
        // Lambda表达式的写法
        Function<Integer, String[]> func1 = length -> new String[length];
        String[] arr1 = func1.apply(5);
        System.out.println("arr1 = " + Arrays.toString(arr1));  // arr1 = [null, null, null, null, null]

        // 数组引用的写法
        Function<Integer, String[]> func2 = String[] :: new;
        String[] arr2 = func2.apply(3);
        System.out.println("arr2 = " + Arrays.toString(arr2));  // arr2 = [null, null, null]
    }
```

## Stream API

- 使用Stream API对集合数据进行操作，就类似于使用SQL执行的数据库查询
- Stream和Collection集合的区别：**Collection**是静态的**内存**数据结构，而**Stream**是有关**计算**的，与**CPU**有关
- **Stream操作的3个步骤：**
 1. **创建Stream**（数据源）
 2. **中间操作**（对数据源的数据进行操作，如过滤、映射等）
 3. **终止操作（要执行终止操作时，才执行中间操作并得到结果，且一旦执行终止操作，该Stream不能再被使用）**
- **Stream的特点：**
 - Stream自己**不存储数据**
 - Stream是**不可变的**，而会返回持有结果的新Stream
 - Stream的**操作是延迟执行的，即需要结果的时候才执行**

### 创建Stream

- Stream：IntStream、LongStream、DoubleStream、Stream&lt;T&gt;
- 创建Stream的4种方式：(主要看数据的承载形式)
 1. 集合Collection接口的默认方法 `stream()`方法 和 `parallelStream()`方法
 2. 数组工具类Arrays的静态方法 `stream(T[])`方法
 3. Stream类的静态方法 `of(T...)`方法
 4. 创建无限流：迭代/生成
 - 迭代：Stream类的静态方法 `iterate(T seed, UnaryOperator<T> f)`（第一个参数是初始值，第二个参数是迭代函数，即Function接口的实现类）
 - 生成：Stream类的静态方法 `generate(Supplier<T> s)`（参数是用于生成数据的Supplier接口的实现类）


```
    /**
     * Stream：IntStream、LongStream、DoubleStream、Stream<T>
     * 创建Stream的4种方式：(主要看数据的承载形式)
     *      1. 集合Collection接口的默认方法    stream()方法 和 parallelStream()方法
     *      2. 数组工具类Arrays的静态方法     stream(T[])方法
     *      3. Stream类的静态方法             of(T...)方法
     *      4. 创建无限流
     *                  迭代：Stream类的静态方法 iterate(T seed, UnaryOperator<T> f)
     *                  生成：Stream类的静态方法 generate(Supplier<T> s)
     */
    private static void streamDemo1() {
        // 创建Stream的方式1：集合Collection接口的默认方法 stream()方法 和 parallelStream()方法
        List<String> list = Arrays.asList("abc1", "abc2", "abc3");
        Stream<String> stream1 = list.stream();
        Stream<String> stream2 = list.parallelStream();

        // 创建Stream的方式2：数组工具类Arrays的静态方法 stream(T[])方法
        int[] arr = {1, 2, 3};
        IntStream stream3 = Arrays.stream(arr);

        // 创建Stream的方式3：Stream类的静态方法 of(T...)方法
        Stream<Character> stream4 = Stream.of('a', 'b', 'c');

        // 创建Stream的方式4：迭代/生成
        // 迭代：Stream类的静态方法 iterate(T seed, UnaryOperator<T> f)  （第一个参数是初始值，第二个参数是迭代函数，即Function接口的实现类）
        // 遍历前10个奇数
        Stream.iterate(1, n -> n + 2).limit(10).forEach(System.out :: println);

        // 生成：Stream类的静态方法 generate(Supplier<T> s)  （参数是用于生成数据的Supplier接口的实现类）
        // 生成10个[0,1)之间的随机数
        Stream.generate(Math :: random).limit(10).forEach(System.out :: println);
    }
```

### Stream的中间操作

- Stream的中间操作包括：**筛选与切片、映射、排序**

- **筛选与切片：**
 - `filter(Predicate p)` —— 可接收Lambda，从Stream中排除某些元素
 - `limit(n)` —— 截断流，使Stream中元素个数不超过n
 - `skip(n)` —— 跳过Stream中前n个元素，如果流中元素不足n个，则返回空流
 - `distinct()` —— 根据Stream中元素的hashCode()方法和equals()方法，去除流中重复的元素

```
    /**
     * Stream的中间操作
     *          1. 筛选与切片
     *                  filter(Predicate p) —— 可接收Lambda，从Stream中排除某些元素
     *                  limit(n) —— 截断流，使Stream中元素个数不超过n
     *                  skip(n) —— 跳过Stream中前n个元素，如果流中元素不足n个，则返回空流
     *                  distinct() —— 根据Stream中元素的hashCode()方法和equals()方法，去除流中重复的元素
     */
    private static void streamDemo2() {
        List<String> list = Arrays.asList("中国", "美国", "葡萄牙", "阿根廷", "德国", "英国");

        // 筛选Stream中包含"国"的元素
        Stream<String> stream1 = list.stream();
//        stream1.filter(s -> s.contains("国")).forEach(System.out :: println);

        // 获取Stream中前3个元素
        Stream<String> stream2 = list.stream(); // 不能再用stream1，因为stream1已经执行了终止操作
//        stream2.limit(3).forEach(System.out :: println);

        // 跳过Stream中前3个元素
        Stream<String> stream3 = list.stream();
//        stream3.skip(3).forEach(System.out :: println);

        // 去除Stream中重复的元素
//        list.add("中国1");  // 报错，Arrays.asList()方法生成的List是固定大小的，无法进行add、remove等操作
        list = Arrays.asList("中国", "美国", "葡萄牙", "阿根廷", "德国", "英国", "中国", "葡萄牙");
        Stream<String> stream4 = list.stream();
        stream4.distinct().forEach(System.out :: println);
    }

输出为：
--------------
中国
美国
德国
英国
-------
中国
美国
葡萄牙
-------
阿根廷
德国
英国
-------
中国
美国
葡萄牙
阿根廷
德国
英国
```

- **映射：**
 - `map(Function f)` —— 将Stream中的每个元素通过Function的实现类的方法进行映射
 - `flatMap(Function f)` —— 若Stream中嵌套有Stream，flatMap()会将内Stream中的每个元素都看成一个元素，类似List的addAll()方法；而map()会将内Stream整体看成一个元素，类似List的add()方法

```
    /**
     * Stream的中间操作
     *          2. 映射
     *                  map(Function f) —— 将Stream中的每个元素通过Function的实现类的方法进行映射
     *                  flatMap(Function f) —— 若Stream中嵌套有Stream，flatMap()会将内Stream中的每个元素都看成一个元素，类似List的addAll()方法
     *                                         而map()会将内Stream整体看成一个元素，类似List的add()方法
     */
    private static void streamDemo3() {
        List<String> list1 = Arrays.asList("jdt", "lw", "cris");
        list1.stream().map(String :: toUpperCase).forEach(System.out :: println);
        System.out.println("-------");

        // 练习：输出list2中所有name长度大于3的Person的姓名
        // 思路：先将每个Person映射为该Person的name，再筛选出长度大于3的name
        List<Person> list2 = new ArrayList<>();
        list2.add(new Person("江岱庭", 21));
        list2.add(new Person("李薇", 3));
        list2.add(new Person("克里斯蒂亚诺", 34));
        Stream<String> nameStream = list2.stream().map(Person::getName);
        nameStream.filter(name -> name.length() > 3).forEach(System.out :: println);
        System.out.println("-------");

        // map()和flatMap()的区别：Stream中嵌套Stream时，map()将内层Stream作为一个元素，而flatMap()将内层Stream中的每个元素都作为一个元素
        Stream<Stream<Character>> streamStream = list1.stream().map(StreamAPIDemo::fromStringToStream);
        streamStream.forEach(stream -> stream.forEach(System.out :: print));
        System.out.println();
        Stream<Character> characterStream = list1.stream().flatMap(StreamAPIDemo::fromStringToStream);
        characterStream.forEach(System.out :: print);
    }

输出为:
--------------
JDT
LW
CRIS
-------
克里斯蒂亚诺
-------
jdtlwcris
jdtlwcris
输出为：
JDT
LW
CRIS
-------
克里斯蒂亚诺
-------
jdtlwcris
jdtlwcris
```

- **排序：**
 - `sorted()` —— 按元素自然顺序排序
 - `sorted(Comparator com)` —— 定制排序

```
    /**
     * Stream的中间操作
     *          3. 排序
     *                  sorted() —— 按元素自然顺序排序
     *                  sorted(Comparator com) —— 定制排序
     */
    private static void streamDemo4() {
        List<Integer> list1 = Arrays.asList(1, 7, 0, -9, 30, 13);
        list1.stream().sorted().forEach(System.out :: println);
        System.out.println("-------");

        List<Person> list2 = new ArrayList<>();
        list2.add(new Person("jdt", 21));
        list2.add(new Person("lw", 3));
        list2.add(new Person("cristiano", 34));
//        list2.stream().sorted().forEach(System.out :: println);   // 报错，原因是Person类没有实现Comparable接口
        list2.stream().sorted((p1, p2) -> (p1.getAge() - p2.getAge()) == 0 ? p1.getName().compareTo(p2.getName()) : (p1.getAge() - p2.getAge()))
                .forEach(System.out :: println);
    }

输出为:
--------------
-9
0
1
7
13
30
-------
Person{name='lw', age=3}
Person{name='jdt', age=21}
Person{name='cristiano', age=34}
```

### Stream的终止操作

- Stream的终止操作包括：**匹配与查找、归约、收集**

- **匹配与查找:**
 - `allMatch(Predicate p)` —— 检查是否所有元素都满足Predicate实现类所定义的条件
 - `anyMatch(Predicate p)` —— 检查是否有元素满足Predicate实现类所定义的条件
 - `noneMatch(Predicate p)` —— 检查是否没有元素满足Predicate实现类所定义的条件
 - `findFirst()` —— 返回Stream中第一个元素
 - `findAny()` —— 返回Stream中任意一个元素
 - `count()` —— 返回Stream中元素的个数
 - `max(Comparator com)` —— 返回Stream中的最大值元素
 - `min(Comparator com)` —— 返回Stream中的最小值元素
 - `forEach(Consumer c)` —— 内部迭代

```
    /**
     * Stream的终止操作
     *          1. 匹配与查找
     *                  allMatch(Predicate p) —— 检查是否所有元素都满足Predicate实现类所定义的条件
     *                  anyMatch(Predicate p) —— 检查是否有元素满足Predicate实现类所定义的条件
     *                  noneMatch(Predicate p) —— 检查是否没有元素满足Predicate实现类所定义的条件
     *
     *                  findFirst() —— 返回Stream中第一个元素
     *                  findAny() —— 返回Stream中任意一个元素
     *                      （在串行的流中，findAny()和findFirst()返回的都是第一个对象
     *                          而在并行的流中，findAny()返回的是最快处理完的那个线程的数据
     *                          因此，在并行操作中，若对数据没有顺序上的要求，那么findAny()的效率会比findFirst()高）
     *                  count() —— 返回Stream中元素的个数
     *                  max(Comparator com) —— 返回Stream中的最大值元素
     *                  min(Comparator com) —— 返回Stream中的最小值元素
     *                  
     *                  forEach(Consumer c) —— 内部迭代
     */
    private static void streamDemo5() {
        List<Person> list = new ArrayList<>();
        list.add(new Person("jdt", 21));
        list.add(new Person("lw", 3));
        list.add(new Person("cristiano", 34));

        // 检查Stream中是否所有Person的年龄都大于等于18岁
        boolean b1 = list.stream().allMatch(p -> p.getAge() >= 18);
        System.out.println("b1 = " + b1);   // b1 = false

        // 检查Stream中是否有Person的年龄为3岁
        boolean b2 = list.stream().anyMatch(p -> p.getAge() == 3);
        System.out.println("b2 = " + b2);   // b2 = true

        // 检查Stream中是否没有Person的姓名为cristiano
        boolean b3 = list.stream().noneMatch(p -> p.getName().equals("cristiano"));
        System.out.println("b3 = " + b3);   // b3 = false（noneMatch()判断的是“是否没有”，因此有的话返回的是false，没有的话返回的是true）

        // 查找Stream中第一个Person
        Optional<Person> person1 = list.stream().findFirst();
        System.out.println("person1 = " + person1);   // person1 = Optional[Person{name='jdt', age=21}]

        // findAny()
        Optional<Person> person2 = list.parallelStream().findAny();
        System.out.println("person2 = " + person2);   // person2 = Optional[Person{name='lw', age=3}]

        // 查找Stream中年龄大于等于18的Person的个数
        long count = list.stream().filter(p -> p.getAge() >= 18).count();
        System.out.println("count = " + count); // count = 2

        // 查找Stream中年龄最大的Person的年龄
        Optional<Integer> maxAge = list.stream().map(Person::getAge).max(Integer::compareTo);
        System.out.println("maxAge = " + maxAge);   // maxAge = Optional[34]

        // 查找Stream中年龄最小的Person
        Optional<Person> minPerson = list.stream().min((p1, p2) -> p1.getAge() - p2.getAge());
        System.out.println("minPerson = " + minPerson); // minPerson = Optional[Person{name='lw', age=3}]
        
        // 流的内部迭代
//        list.stream().forEach(System.out :: println);
        // 集合也可以内部迭代
//        list.forEach(System.out :: println);
    }
```

- **归约：**
 - `reduce(T identity, BinaryOperator)` —— 可将Stream中的元素反复结合起来得到一个值，第一个参数是初始值，第二个参数是对元素进行的操作
 - `reduce(BinaryOperator)`

```
    /**
     * Stream的终止操作
     *          2. 归约
     *                  reduce(T identity, BinaryOperator) —— 可将Stream中的元素反复结合起来得到一个值，第一个参数是初始值，第二个参数是对元素进行的操作
     *                  reduce(BinaryOperator)
     */
    private static void streamDemo6() {
        // 求整数1到10的和
        Stream<Integer> stream = Stream.iterate(1, n -> n + 1).limit(10);
        Integer sum = stream.reduce(0, Integer :: sum);
        System.out.println("sum = " + sum);

        // 计算Stream中所有Person的年龄之和
        List<Person> list = new ArrayList<>();
        list.add(new Person("jdt", 21));
        list.add(new Person("lw", 3));
        list.add(new Person("cristiano", 34));
        Optional<Integer> ageSum = list.stream().map(Person::getAge).reduce(Integer::sum);
        System.out.println("ageSum = " + ageSum);   // ageSum = Optional[58]
    }
```

- **收集：**
 - `collect(Collector c)` —— 将Stream中的元素收集到容器(如集合)中（Collectors提供了toList()、toSet()等方法可以提供Collector实例）

```
    /**
     * Stream的终止操作
     *          3. 收集
     *                  collect(Collector c) —— 将Stream中的元素收集到容器（如集合）中
     *                          Collectors提供了toList()、toSet()等方法可以提供Collector实例
     */
    private static void streamDemo7() {
        // 将list中年龄大于18的Person装到一个新的list/set中
        List<Person> list = new ArrayList<>();
        list.add(new Person("jdt", 21));
        list.add(new Person("lw", 3));
        list.add(new Person("cristiano", 34));

        List<Person> list1 = list.stream().filter(p -> p.getAge() >= 18).collect(Collectors.toList());
        list1.forEach(System.out :: println);
        System.out.println("-------");
        Set<Person> set1 = list.stream().filter(p -> p.getAge() >= 18).collect(Collectors.toSet());
        set1.forEach(System.out :: println);
    }
```

## Optional类

- Optional类用于避免空指针异常
- Optional类可以看作是容器类，里面封装其他数据


- 创建Optional类对象
 - `Optional.of(T t)` —— t必须是非null的
 - `Optional.empty()` —— 创建空的Optional实例
 - `Optional.ofNullable(T t)` —— t可以是null

```
    /**
     * 创建Optional类的方法：
     *          Optional.of(T t) —— t必须是非null的
     *          Optional.empty() —— 创建空的Optional实例
     *          Optional.ofNullable(T t) —— t可以是null
     */
    private static void optionalDemo1() {
        String str1 = null;
        String str2 = "abc";

//        Optional<String> op1 = Optional.of(str1);   // 通过of()创建Optional实例，不能为空，会报错

        Optional<String> op2 = Optional.of(str2);
        System.out.println("op2 = " + op2); // op2 = Optional[abc]

        Optional<Object> op3 = Optional.empty();
        System.out.println("op3 = " + op3); // op3 = Optional.empty

        Optional<String> op4 = Optional.ofNullable(str1);   // 通过ofNullable()创建Optional实例可以为空
        System.out.println("op4 = " + op4); // op4 = Optional.empty
    }
```

- Optional类获取内部封装的数据
 - `T orElse(T t)` —— 如果当前Optional内部封装的t是非null的，就返回内部封装的t；否则返回参数中的t（避免了null的出现）
 - `T get()` —— 返回当前Optional内部封装的t，如果是null，抛出异常
 - `boolean isPresent()` —— 判断当前Optional内部封装有对象

```
    /**
     * Optional类获取内部封装的数据的方法：
     *          T orElse(T t) —— 如果当前Optional内部封装的t是非null的，就返回内部封装的t；否则返回参数中的t（避免了null的出现）
     *          T get() —— 返回当前Optional内部封装的t，如果是null，抛出异常
     */
    private static void optionalDemo2() {
        String str1 = null;
        Optional<String> op1 = Optional.ofNullable(str1);
        String str2 = op1.orElse("abc");    // 由于op1内部封装的是null，因此返回参数中的"abc"，确保了str2是非null的
        System.out.println("str2 = " + str2);   // str2 = abc

        Person person = null;
        Optional<Person> personOp = Optional.ofNullable(person);
        Person person1 = personOp.orElse(new Person("江岱庭", 21));    // 保证person1是非null的
        System.out.println(person1.getName());
    }
```

# JDK9新特性

## 模块化系统

- 可以在使用其他Module中的内容，但是要在两个Module的src中分别创建module-info，具体[视频教程](https://www.bilibili.com/video/av48370019/?p=694)

## Java的REPL工具：jShell命令

- 提供了命令行形式的交互式编程环境，实现即写即得、快速运行，具体[视频教程](https://www.bilibili.com/video/av48370019/?p=695)

## 语法/API层面

### 接口的私有方法

- JDK9开始，接口中可以定义私有方法，只能在接口内部调用

```
public class InterfacePrivateMethodDemo {
    public static void main(String[] args) {
        MyInterface.methodStatic(); // static method from interface...
//        MyInterface.methodPrivate();  // 报错，因为私有方法不能在外部调用

        MyInterImpl impl = new MyInterImpl();
        impl.methodAbstract();  // implement abstract method...
        impl.methodDefault();   // override default method...
    }
}

interface MyInterface {
    // 抽象方法
    void methodAbstract();
    // 静态方法（只能接口自己调用，实现类无法调用）
    static void methodStatic() {
        System.out.println("static method from interface...");
    }
    // 默认方法（权限也是public）
    default void methodDefault() {
        System.out.println("default method from interface...");
    }

    // 私有方法（只能在接口内部调用）
    private void methodPrivate() {
        System.out.println("private method from interface...");
    }
}

class MyInterImpl implements MyInterface {
    @Override
    public void methodAbstract() {
        System.out.println("implement abstract method...");
    }

    @Override
    public void methodDefault() {
        System.out.println("override default method...");
    }
}
```

### 钻石操作符的升级

- JDK8中钻石操作符`<>`不能和匿名内部类共存，JDK9开始可以，即可以`Comparator<Person> com = new Comparator<>() {...}`

### try语法结构的升级

```
    /**
     * JDK8之前，try中关闭资源的做法：用finally语句块
     */
    private static void beforeJDK8() {
        BufferedReader in = null;
        String line = null;
        try {
            in = new BufferedReader(new InputStreamReader(System.in));
            while ((line = in.readLine()) != null) {
                if ("over".equalsIgnoreCase(line))
                    break;
                System.out.println(line.toUpperCase());
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (in != null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

```
    /**
     * JDK8中，try中关闭资源的做法：可以在try()小括号中声明并初始化要关闭的资源，无论是否捕捉到异常，该资源都会被关闭
     * 缺点是所有要关闭的资源必须在try()小括号中进行声明并初始化
     */
    private static void whileJDK8() {
        String line = null;
        try(BufferedReader in = new BufferedReader(new InputStreamReader(System.in))) { // 无论是否捕捉到异常，资源in都会被关闭
            while ((line = in.readLine()) != null) {
                if ("over".equalsIgnoreCase(line))
                    break;
                System.out.println(line.toUpperCase());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

```
    /**
     * JDK9开始，try中关闭资源的做法：要关闭的资源仍放在try()小括号中（如果有多个资源，用分号隔开），但是可以在try语句外面进行声明和初始化
     * 但是在try{}里面不要修改该资源！
     */
    private static void whileJDK9() {
        String line = null;
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        try(in) { // 无论是否捕捉到异常，资源in都会被关闭
            while ((line = in.readLine()) != null) {
                if ("over".equalsIgnoreCase(line))
                    break;
                System.out.println(line.toUpperCase());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

### String底层存储

- JDK9之前，String底层存储用的是char[]数组
- JDK9开始，String底层存储改成用byte[]数组（跟String相关的结构如StringBuffer、StringBuilder底层存储也跟着变成了byte[]数组）
- 这是因为大多数用到的String中存储的都是字母、数字等，只用1个字节就能够存储下，而如果用char[]数组，则每个字符要用2个字节存储，浪费空间
- 而在String存储汉字等1个字节存不下的数据时，会有flag，可连续读取多个字节，不会出问题

### 集合工厂方法创建只读集合

- JDK8及之前，创建只读集合的方法：
 - Collections工具类的unmodifiableCollection(Collection)、unmodifiableList(List)、unmodifiableSet(Set)、unmodifiableMap(Map)等方法
 - Arrays工具类的asList(T...)方法

```
    /**
     * JDK8及之前，创建只读集合的方法：
     *              Collections工具类的unmodifiableCollection(Collection)、unmodifiableList(List)、unmodifiableSet(Set)、unmodifiableMap(Map)等方法
     *              Arrays工具类的asList(T...)方法
     */
    private static void beforeJDK9() {
        List<Integer> list1 = new ArrayList<>();
        list1.add(7);
        list1.add(9);
        list1.add(0);
        list1.add(3);
        // 创建只读List
        List<Integer> list2 = Collections.unmodifiableList(list1);
//        list2.add(-1);    // UnsupportedOperationException

        // 创建只读List
        List<Integer> list3 = Arrays.asList(1, 2, 3, 4);
//        list3.add(5);    // UnsupportedOperationException
    }
```

- JDK9开始，创建只读集合的方法：
 - Collection、List、Set、Map等的of(T...)方法
 - Map的ofEntries(Entry...)方法

```
    /**
     * JDK9开始，创建只读集合的方法：
     *              Collection、List、Set、Map等的of(T...)方法
     *              Map的ofEntries(Entry...)方法
     */
    private static void afterJDK9() {
        // 创建只读Set
        Set<Integer> set = Set.of(1, 2, 3);
//        set.add(4); // UnsupportedOperationException

        // 创建只读Map
        Map<String, Integer> map1 = Map.of("江岱庭", 21, "李薇", 3, "C罗", 34);   // 都是逗号隔开
//        map1.put("哈登", 30); // UnsupportedOperationException

        Map<String, Integer> map2 = Map.ofEntries(Map.entry("江岱庭", 21), Map.entry("李薇", 3), Map.entry("C罗", 34));
//        map2.put("哈登", 30); // UnsupportedOperationException
    }
```

### InputStream的transferTo()方法

- JDK9开始，InputStream出现了transferTo(OutputStream out)方法，可以将输入流中的所有数据直接复制到输出流中

```
    public static void main(String[] args) {
        ClassLoader classLoader = InputStreamTransferToDemo.class.getClassLoader();
        try(InputStream in = classLoader.getResourceAsStream("test.txt");   // 类加载器的getResourceAsStream(Path)方法相对路径是小module的src目录下
            OutputStream out = new FileOutputStream("MyProject2\\myTest\\copy.txt")) {  // FileOutputStream(Path)相对路径是在大module下
            in.transferTo(out); // 将输入流中的数据复制到输出流中
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

### 增强的Stream API

- JDK9中Stream新增的方法：
 - `takeWhile(Predicate p)` —— 从头开始，返回尽可能多的符合Predicate实现类定义的规则的元素，一旦出现不符合的元素，就截断，后面的元素也不再获取
 - `dropWhile(Predicate p)` —— 和 takeWhile() 的操作正好相反，丢弃元素直到第一个不符合Predicate实现类定义的规则的元素，且返回剩余的元素
 - `ofNullable(T)` —— of(T...)方法的参数不能有且仅有1个null，而ofNullable(T)方法的参数是可以为null的单个元素（没有参数是多个元素的重载形式）
 - `iterate(T seed, Predicate p, UnaryOperator<T> f)` —— iterate()方法的重载方法，可自定义终止条件，seed按f定义的规则迭代，不满足p时停止迭代

```
    /**
     * takeWhile(Predicate p) —— 从头开始，返回尽可能多的符合Predicate实现类定义的规则的元素，一旦出现不符合的元素，就截断，后面的元素也不再获取
     * dropWhile(Predicate p) —— 和 takeWhile() 的操作正好相反，丢弃元素直到第一个不符合Predicate实现类定义的规则的元素，且返回剩余的元素
     */
    private static void demo1() {
        List<Integer> list = List.of(1, 3, 5, 7, 11, 6, 2, 0);
        list.stream().takeWhile(n -> n < 10).forEach(System.out :: println);    // 输出1 3 5 7（元素11不满足，后面的元素也不再获取）
        list.stream().dropWhile(n -> n < 10).forEach(System.out :: println);    // 输出11 6 2 0（元素11是第一个不满足的元素，从该元素开始一直获取到结尾）
    }
```

```
    /**
     * Stream的of()方法中的参数可以包含null，但不能有且只有1个null（实践证明多个null也可以）
     * JDK9开始，Stream新增 ofNullable(T)方法，参数是可以为null的单个元素（没有参数是多个元素的重载形式）
     */
    private static void demo2() {
        Stream<Integer> stream1 = Stream.of(1, 2, 3, null);
        stream1.forEach(System.out :: println);  // 1 2 3 null

        // Stream的of()方法中的参数不能有且只有1个null
//        Stream<Object> stream2 = Stream.of(null);
//        stream2.forEach(System.out :: println); // NullPointerException

        // ofNullable(T)方法，参数只有一个，且可以为null
        Stream<Object> stream3 = Stream.ofNullable(null);
        System.out.println(stream3.count());    // 0（不认为该null是元素）
    }
```

```
    /**
     * JDK9的Stream API新增 iterate()方法的重载方法
     *      iterate(T seed, Predicate p, UnaryOperator<T> f)  可自定义终止条件，seed按f定义的规则迭代，不满足p时停止迭代
     */
    private static void demo3() {
        // 遍历1-10的整数
        // JDK8
//        Stream.iterate(1, x -> x = x + 1).limit(10).forEach(System.out :: println);

        // JDK9新增iterate()的重载方法：iterate(T seed, Predicate p, UnaryOperator<T> f)
        Stream.iterate(1, x -> x <= 10, x -> x = x + 1).forEach(System.out :: println);
    }
```

### 新增Optional类的stream()方法

```
    public static void main(String[] args) {
        List<String> list = List.of("JDT", "LW", "Cris");
        Optional<List<String>> op = Optional.of(list);
        System.out.println(op.stream().count());    // 1
        // 遍历op中封装的list的每个元素
        Stream<List<String>> stream = op.stream();
        stream.flatMap(x -> x.stream()).forEach(System.out :: println);    // JDT LW Cris
    }
```

# JDK10新特性

## 局部变量类型推断

- 在局部变量进行声明并初始化时，**由右边的类型可以推断左边的变量类型**，因此可以用`var`代替左边的变量类型
- **注意：右边必须要赋值，且不能赋值为null，否则无法进行类型推断**
- **在编译时，编译器会自动将var替换成推断出的类型，写入字节码文件中**（Java仍是静态类型语言，而不是像JavaScript那种动态语言）
- **`var`不是一个关键字**，因此可以作为变量名、方法名等普通标识符，但不要把它作为类名

```
    /**
     * 在进行局部变量声明并初始化时，由右边的类型可以推断左边的变量类型，因此可以用var代替左边的变量类型
     * 注意：右边必须要赋值，且不能赋值为null，否则无法进行类型推断
     * 在编译时，编译器会自动将var替换成推断出的类型，写入字节码文件中
     */
    private static void varDemo1() {
        var num = 10;
        System.out.println(num);    // 10

        var str = "xxx";
        System.out.println(str + " : " + str.getClass()); // xxx : class java.lang.String

//        var str1 = null;  // 会报错，右边必须赋值，且不能为null，否则无法进行类型推断

        var list = List.of("abc", "nba", "fifa");
        System.out.println(list);   // [abc, nba, fifa]
    }
```

```
    /**
     * 不能类型推断的情况
     *      1. 没有初始化的局部变量声明
     *      2. 方法的返回值类型
     *      3. 方法的参数类型
     *      4. 构造器的参数类型
     *      5. 属性
     *      6. catch语句块
     */
//    private var id;   // 报错，属性有默认值，而无法推断出是什么类型的默认值
//    private static var Demo2(var x) {  // 报错，方法返回值类型和参数类型不能进行类型推断

    private static void varDemo2() {
//        var num;  // 报错，必须进行初始化，且不能为null
        
//        try {
//            
//        } catch (var e) { // 报错，无法推断是什么类型的异常
//            
//        }
    }
```

## 新增集合类创建只读集合的copyOf()方法

- 集合的copyOf(Collection coll)方法创建只读集合
 - **如果coll本身就是只读集合，则返回coll，而不创建新的集合**
 - **如果coll本身不是只读集合，则创建新的只读集合，元素和coll中一致，并返回**

```
    public static void main(String[] args) {
        List<String> list1 = List.of("abc", "nba", "portugal");
        List<String> list2 = List.copyOf(list1);	// list1是只读集合，直接返回list1
        System.out.println(list1 == list2); // true

        List<String> list3 = new ArrayList<>();
        list3.add("abc");
        list3.add("nba");
        list3.add("portugal");
        List<String> list4 = List.copyOf(list3);	//  list3不是只读集合，故返回的不是list3，而是新创建的只读集合
        System.out.println(list3 == list4); // false
    }
```

# JDK11新特性

- JDK11是LTS（长期支持版本）

## ZGC

- Java最强大的部分：JVM和GC

## 新增String的方法

- JDK11新增String的方法：
- String新增的方法：
 - `boolean isBlank()` —— 判断字符串是否是空白
 - `String strip()` —— 去除两端的空白（替换原来的trim()方法）
 - `String stripTrailing()` —— 去除尾端的空白
 - `String stripLeading()` —— 去除开头的空白
 - `String repeat(int count)` —— 重复
 - `int lines.count()` —— 统级行数

```
public class StringMethodDemo {
    public static void main(String[] args) {
        String str1 = "  \t \n   ";
        System.out.println(str1.isBlank()); // true

        String str2 = "  \n abc  \t  ";
        System.out.println("---" + str2.strip() + "---");   // ---abc---
        System.out.println("---" + str2.stripLeading() + "---");    // ---abc  	  ---
        System.out.println("---" + str2.stripTrailing() + "---");   /* ---
                                                                     abc--- */

        String str3 = "abc";
        String str4 = str3.repeat(4);
        System.out.println(str4);   // abcabcabcabc

        String str5 = "abc \n abc \n abc";
        System.out.println(str5.lines().count());   // 3
    }
```

## 新增Optional类的isEmpty()方法

- JDK11新增Optional类的isEmpty()方法，判断封装的数据是否为空，和isPresent()方法功能相反
- JDK9还新增了Optional类的or(Supplier)方法，和orElse(T)方法类似，但or()方法操作的是Optional对象，orElse()方法操作的是Optional对象封装的数据value

## 全新的HttpClient API

# JDK12新特性

## Shenandoah GC

- JDK12出现Shenandoah GC
- Shenandoah GC是低停顿时间的GC，且停顿时间和堆内存大小无关
- Shenandoah GC可以将回收后的堆内存归还给操作系统
- 垃圾回收器的吞吐量和低延迟这两个目标是相互矛盾的

## 可中断的的G1 Mixed GC

- JDK12对G1进行了增强，使其在垃圾回收过程中可以中断，并可以将回收后的堆内存归还给操作系统

## 语法/API层面

### 增强switch语句

- JDK12之前，switch语法的缺点：
 1. 匹配是自上而下的，如果忘写break，下面的case无论是否满足，都会执行
 2. 在case语句中定义的变量作用于是整个switch语句块，但不一定会执行到变量定义的那个case语句，而在不同case语句定义的变量名不能重复
 3. 不能在一个case语句中匹配多个结果，即即使a和b的结果一致也不能写成`case a, b:`
 4. 整个switch不能作为表达式返回值

```
    /**
     * JDK12之前，switch语法的缺点：
     *              1. 匹配是自上而下的，如果忘写break，下面的case无论是否满足，都会执行
     *              2. 在case语句中定义的变量作用于是整个switch语句块，但不一定会执行到变量定义的那个case语句，而在不同case语句定义的变量名不能重复
     *              3. 不能在一个case语句中匹配多个结果，即即使a和b的结果一致也不能写成 case a, b:
     *              4. 整个switch不能作为表达式返回值
     */
    private static void beforeJDK12() {
        Fruit fruit = Fruit.BANANA;
        // 变量numOfLetters必须定义在switch语句块外面，否则如果定义在某个case语句里面，该语句不一定会被执行，又不能在多个case里定义同名变量
        int numOfLetters = 0;
        switch (fruit) {
            // case穿透，不能写成 case APPLE, PEACH:
            case APPLE:
            case PEACH:
                numOfLetters = 5;
                break;
            case BANANA:
            case ORANGE:
                numOfLetters = 6;
                break;
            case WATERMELON:
                numOfLetters = 10;
                break;
            default:
                throw new RuntimeException("水果错误");
        }
        System.out.println("numOfLetters = " + numOfLetters);   // numOfLetters = 6
    }

    enum Fruit {
        APPLE, PEACH, BANANA, ORANGE, WATERMELON;
    }
```

- JDK12中switch的新特性：
 1. 不用写break，匹配后不会往下执行
 2. 一个case中可以匹配多个结果
 3. switch语句可以作为表达式返回值

```
    /**
     * JDK12中switch的新特性：
     *              1. 不用写break，匹配后不会往下执行
     *              2. 一个case中可以匹配多个结果
     *              3. switch语句可以作为表达式返回值
     */
    private static void whileJDK12() {
        Fruit fruit = Fruit.BANANA;
        // switch语句可以作为表达式返回值
        int numOfLetters = switch (fruit) {
            case APPLE, PEACH -> 5;   // 一个case可以匹配多个结果，且不用再写break
            case BANANA, ORANGE -> 6;
            case WATERMELON -> 10;
            default -> throw new RuntimeException("水果错误");
        };
        System.out.println("numOfLetters = " + numOfLetters);   // numOfLetters = 6

        fruit = Fruit.WATERMELON;
        switch (fruit) {
            case APPLE, PEACH -> System.out.println("苹果/桃子");
            case BANANA, ORANGE -> System.out.println("香蕉/橙子");
            case WATERMELON -> System.out.println("西瓜");    // 西瓜
            default -> new RuntimeException("水果错误");
        }
    }
```

### 新增String的方法

- JDK12中String类新增的方法：
 - `String transform(Function f)` —— 将字符串按照f定义的规则映射成新的字符串，且可以连续调用，类似于Stream中的map()方法
 - `String indent(int num)` —— 缩进num个空格（若字符串有多行，则每行都缩进num个空格）


```
    /**
     * String类新增的方法：
     *          String transform(Function f) —— 将字符串按照f定义的规则映射成新的字符串，且可以连续调用，类似于Stream中的map()方法
     */
    private static void demo1() {
        List<String> list1 = List.of(" Java", " python   ", "C++ ");

        // 通过字符串的transform()方法进行映射
        ArrayList<String> list2 = new ArrayList<>();
        list1.forEach(str -> list2.add(str.transform(String :: strip)
                .transform(String :: toUpperCase)
                .transform("Hello, " :: concat)));
        list2.forEach(System.out :: println);

        // 通过Stream的map()方法进行映射
        Stream<String> stream = list1.stream().map(str -> str.strip().toUpperCase()).map("Hello, "::concat);
        List<String> list3 = stream.collect(Collectors.toList());
        list3.forEach(System.out :: println);
    }

-------
Hello, JAVA
Hello, PYTHON
Hello, C++
Hello, JAVA
Hello, PYTHON
Hello, C++
```

```
    /**
     * String类新增的方法：
     *          String indent(int num) —— 缩进num个空格（若字符串有多行，则每行都缩进num个空格）
     */
    private static void demo2() {
        String str = "Java\n  Python\nC++";
        str = str.indent(2);
        System.out.println(str);
    }

-------
  Java
    Python
  C++
```

### 新增Paths的方法

- JDK12中Paths类新增的方法：
 - `static long mismatch(Path path1, Path path2)` —— 返回两个文件中首个不相同的字节的位置，若两个文件完全相同，返回-1

```
    public static void main(String[] args) throws IOException {
        FileWriter fw1 = new FileWriter("D:\\IDEA\\IdeaProject\\MyProject2\\myTest\\a.txt");
        fw1.write("a");
        fw1.write("b");
        fw1.write("c");
        fw1.close();

        FileWriter fw2 = new FileWriter("D:\\IDEA\\IdeaProject\\MyProject2\\myTest\\b.txt");
        fw2.write("a");
        fw2.write("B");
        fw2.write("c");
        fw2.close();

        long mismatch = Files.mismatch(Path.of("D:\\IDEA\\IdeaProject\\MyProject2\\myTest\\a.txt"), Path.of("D:\\IDEA\\IdeaProject\\MyProject2\\myTest\\b.txt"));
        System.out.println("mismatch = " + mismatch);   // mismatch = 1 （即两个文件索引为1的字节不同）
    }
```

# JDK13新特性

## ZGC的变化

- JDK13中，ZGC也可以将回收的堆内存归还给操作系统

## 语法/API层面

### 增强Switch语句

- JDK13中，switch结构新增`yield`关键字
- `yield`可用于结束switch语句块，并返回值（类似于return）（JDK12的switch语句块中用`->`来返回值，但不能执行其他语句后再返回值）

```
    public static void main(String[] args) {
        Sex sex = Sex.Unknown;
        String str = switch (sex) {
            case Male: yield "男";
            case Female: yield "女";
            default: {
                System.out.println("人妖哈哈哈");
                yield "未知"; // 可以先执行其他语句，再返回值，并会结束switch语句块
            }
        };
        System.out.println("性别：" + str);
    }

    enum Sex {
        Male, Female, Unknown;
    }

-------
人妖哈哈哈
性别：未知
```

### 文本块Text Bolck

- Text Block可以简化有多行的字符串，避免转义字符，增强字符串的可读性
- Text Block分别以3个双引号`"""`开始分隔符和结束分隔符

```
    /**
     * Text Block可以简化有多行的字符串，避免转义字符，增强字符串的可读性
     * Text Block分别以3个双引号"""开始分隔符和结束分隔符
     */
    private static void demo1() {
        // 之前的写法
        String str1 = "<html>\n" +
                "    <head>\n" +
                "        <meta http-equiv=\"Content-Type\" content=\"text/html; charset=utf-8\">\n" +
                "        <title>Title</title>\n" +
                "    </head>\n" +
                "    <body>\n" +
                "        Body\n" +
                "    </body>\n" +
                "</html>";
        System.out.println(str1);

        System.out.println("-------");

        // 利用Text Block后的写法
        String str2 = """
        <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Title</title>
    </head>
    <body>
        Body
    </body>
</html>
""";
        System.out.println(str2);
    }
```

- Text Block以开始分隔符的行终止符后的第一个字符开始**（开始分隔符后面必须换行）**
- Text Block以结束分隔符前的最后一个字符结束**（结束分隔符前面不一定要换行，如果换行，默认有换行符存在）**

```
    /**
     * Text Block以开始分隔符的行终止符后的第一个字符开始（开始分隔符后面必须换行）
     * Text Block以结束分隔符前的最后一个字符结束（结束分隔符前面不一定要换行，如果换行，默认有换行符存在）
     */
    private static void demo2() {
        /* 错误的写法，会报错
        String str1 = """a
""";
         */

        String str2 = """
a""";   // 相当于 str2 = "a";

        String str3 = """
a
""";    // 相当于 str3 = "a\n";

        System.out.println(str2.length());  // 1
        System.out.println(str3.length());  // 2
    }
```

```
    /**
     * Text Block空字符串的写法
     */
    private static void demo3() {
        // 空字符串的写法
        String str1 = """
""";
        System.out.println(str1.length());  // 0

        String str2 = """

""";
        System.out.println(str2.length());  // 1（其实有个换行符\n）
    }
```

- 在运行时，Text Block将被实例化为字符串，和字符串没有区别，具有相同内容的Text Block指向字符串常量池中的同一常量

```
    /**
     * 在运行时，Text Block将被实例化为字符串，和字符串没有区别，具有相同内容的Text Block指向字符串常量池中的同一常量
     */
    private static void demo4() {
        String str1 = "abc";
        String str2 = """
abc""";
        System.out.println(str1 == str2);   // true（str1和str2都指向字符串常量池中的同一常量）
    }
```

- Text Block会自动删除多余的空格
 - 每行末尾的空格会自动删除
 - **每行开头的空格则根据结束标识符前的空格数进行删除**

```
    /**
     * Text Block会自动删除多余的空格：每行末尾的空格会自动删除，而每行开头的空格则根据结束标识符前的空格数进行删除
     */
    private static void demo5() {
        String str1 = """
  abc
""";    // abc后面的空格会被自动删除，但是有一个换行符\n，而abc前面的2个空格没有删除
        System.out.println(str1.length());  // 6

        String str2 = """
  abc
  """;  // abc前面的2个空格被删除，因为结束标识符前面也有2个空格
        System.out.println(str2.length());  // 4

        String str3 = """
    abc
  """;  // abc前面的4个空格被删除其中的2个，因为结束标识符前面有2个空格
        System.out.println(str3.length());  // 6
    }
```

- Text Block内可以正常使用双引号而不用转义，但是三引号需要转义，否则会被识别为结束标识符
- Text Block中每行末尾默认存在换行符\n，如果再加\n则会换两行

```
    /**
     * Text Block内可以正常使用双引号而不用转义，但是三引号需要转义，否则会被识别为结束标识符
     * Text Block中每行末尾默认存在换行符\n，如果再加\n则会换两行
     */
    private static void demo6() {
        String str1 = """
        "双引号不用转义";
        \"""三引号需要转义\""";
""";
        System.out.println(str1);

        System.out.println("-------");

        String str2 = """
        abc\n
        efg
        hij
""";
        System.out.println(str2);

        System.out.println("-------");
    }

----------------------
输出为：
        "双引号不用转义";
        """三引号需要转义""";

-------
        abc

        efg
        hij

-------
```

- Text Block的拼接和字符串一样，但有时会显得比较笨重，可以用String的replace()方法或静态format()方法来进行改进

```
    /**
     * Text Block的拼接和字符串一样，但有时会显得比较笨重，可以用String的replace()方法或format()方法来进行改进
     */
    private static void demo7() {
        String type = "String";

        // 普通拼接的写法，比较笨重
        String str1 = """
    private static void method(""" + type + """
 o) {
        System.out.println(o);
}""";
        System.out.println(str1);

        // 使用String的replace方法改进的写法
        String str2 = """
private static void method($type o) {
        System.out.println(o);
}""".replace("$type", type);
        System.out.println(str2);

        // 使用String的format()方法改进的写法
        String str3 = String.format("""
private static void method(%s o) {
        System.out.println(o);
}""", type);
        System.out.println(str3);
    }

-------
输出为：
private static void method(String o) {
        System.out.println(o);
}
private static void method(String o) {
        System.out.println(o);
}
private static void method(String o) {
        System.out.println(o);
}
```
---


### MessageFormat类

- MessageFormat能在字符串有些值未确定时先用占位符（如`{0}`）表示，等待值确定后再填充进去，可在配置文件中某些参数不确定时使用


```
The first example uses the static method MessageFormat.format, which internally creates a MessageFormat for one-time use:

 int planet = 7;
 String event = "a disturbance in the Force";

 String result = MessageFormat.format(
     "At {1,time} on {1,date}, there was {2} on planet {0,number,integer}.",
     planet, new Date(), event);
 
The output is:
 At 12:30 PM on Jul 3, 2053, there was a disturbance in the Force on planet 7.
```
---

### BigInteger类和BigDecimal类

- BigInteger类用于大数据整数的运算，例如可以计算1000的阶乘：
```
    @Test
    public void test1() {
        BigInteger res = new BigInteger("1");
        int n = 1000;
        for (int i = 1; i <= n; i++) {
            BigInteger big_i = new BigInteger(String.valueOf(i));
            res = res.multiply(big_i);
        }
        System.out.println(res);
    }
```
- BigDecimal类用于浮点数的精确运算，避免二进制运算的误差，例如
```
    @Test
    public void test2() {
        double a = 2.0;
        double b = 1.1;
        System.out.println(a - b);  // 0.8999999999999999，是由于二进制运算的误差导致的
    }

    @Test
    public void test3() {
        BigDecimal a = new BigDecimal("2.0");
        BigDecimal b = new BigDecimal("1.1");
        double res = a.subtract(b).doubleValue();
        System.out.println(res);    // 0.9
    }
```

- 所有有关金额的运算最好都用BigDecimal类
- BigInteger和BigDecimal都是Number的子类，都有intValue()、doubleValue()等方法可以方便地转换成int、double
---

## 注解

- **注解的作用：替代配置文件**
- 所有注解都是Annotation的子类
- 定义注解类的格式：`@interface MyAnno1 {}`
- 注解的作用目标：类、方法、构造器、参数、局部变量、包

##### 注解的属性

- 注解可以有属性，属性可以有默认值
- 注解的属性类型：8种基本数据类型、String、Enum、注解，以及以上数据类型的一维数组类型
- 使用注解时，必须给没有默认值的属性赋值，可以给有默认值的属性赋值
- 使用注解时，若只给名为value的属性赋值，可以省略`value=`，直接写属性值
- 使用注解时，若数组类型的属性只有一个元素，可以省略`{}`

```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/4 19:51
 * @Description: 注解
 */

@MyAnnotation1(b = "xxx", c = MyEnum.A, annotation2 = @MyAnnotation2(value = 1)) // 有默认值的属性可以省略
@MyAnnotation2(100) // 只给名为value的属性赋值时，可以省略"value="
@MyAnnotation3(a = {1, 2}, b = 0) // 数组属性只有一个元素时，可以省略"{}"
public class AnnotationDemo1 {
}

@interface MyAnnotation1 {
    int a() default 0;  // 注解属性可以有默认值
    String b();
    MyEnum c();
    MyAnnotation2 annotation2();
    int[] d() default {0};
    // 注解属性只能是8种基本数据类型、String、Enum、注解，以及它们的一维数组类型
//    Integer e();  // 错
//    int[][] e();  // 错
}

@interface MyAnnotation2 {
    int value() default 0;
    String str() default "xxx";
}

@interface MyAnnotation3 {
    int[] a();
    int[] b();
}

enum MyEnum {
    A, B, C
}
```

##### 注解的作用目标限定和保留策略

- 注解的作用目标限定
 - 在定义注解时，可以限定其作用目标（如只可以作用在方法上，而不可以作用在类上）
 - 在定义注解时，给注解添加注解`@Target`来限定其作用目标
- 注解的保留策略
 - 注解的保留策略分为：
 1. SOURCE：指注解只在源文件中存在，编译后不会出现在字节码文件中
 2. CLASS：指注解在源文件中存在，且编译后会出现在字节码文件中，但在JVM加载类时，会被忽略
 3. **RUNTIME**：指注解在源文件和字节码文件中都存在，且在JVM加载类时，会被加载到内存中（它是**可反射注解**）
 - 在定义注解时，给注解添加注解`@Retention`来指定其保留策略


```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/4 20:13
 * @Description: 注解的作用目标限定和保留策略
 */

//@MyAnnotation4    // 错
public class AnnotationDemo2 {
    @MyAnnotation4
    private void function() {}
}

@Target(value = {ElementType.METHOD, ElementType.LOCAL_VARIABLE})   // 将MyAnnotation4注解的作用目标限定为方法和局部变量
    @Retention(RetentionPolicy.RUNTIME) // 将MyAnnotation4注解的保留策略定为RUNTIME，是可反射注解
@interface MyAnnotation4 {
}

```

##### 反射注解

- **只有保留策略是RUNTIME的注解才能够通过反射获取**
- 反射注解需要在注解的作用目标上反射
 - 类上的注解需要使用Class来获取
 - 方法上的注解需要使用Method来获取
 - 构造器上的注解需要使用Construcator来获取
 - 参数上的注解需要使用Field来获取
- 它们都有获取指定类型注解的方法：`Annotation getAnnotation(Class)`

```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/4 20:53
 * @Description: 反射注解
 */
public class ReflectAnnotationDemo {
    /**
     * 通过反射获取类上的注解，需要通过Class类获取
     */
    @Test
    public void demo1() {
        Class<D> clazz = D.class;
        MyAnnotation1 annotation = clazz.getAnnotation(MyAnnotation1.class);
        System.out.println("name = " + annotation.name());  // name = D类
    }

    /**
     * 通过反射获取方法上的注解，需要通过Method来获取
     */
    @Test
    public void demo2() throws NoSuchMethodException {
        Class<D> clazz = D.class;
        Method method = clazz.getMethod("function");
        MyAnnotation1 annotation = method.getAnnotation(MyAnnotation1.class);
        System.out.println("name = " + annotation.name());  // name = function方法
    }
}

@MyAnnotation1(name = "D类")
class D {
    @MyAnnotation1(name = "function方法")
    public void function() {

    }
}

@Retention(RetentionPolicy.RUNTIME) // 只有保留策略为RUNTIME的注解才能够通过反射获取
@interface MyAnnotation1 {
    String name();
}
```

---

##### 反射泛型信息

```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/4 20:35
 * @Description: 反射泛型信息
 *                  A是泛型类，B和C是A的子类，都给A传递了泛型信息，想要在A中获取到子类传递的泛型信息
 */

public class ReflectGenericDemo {
    public static void main(String[] args) {
        new B();
    }
}

abstract class A<T> {
    public A() {
        // 获取子类传递的泛型信息
//        Class<? extends A> clazz = this.getClass(); // 得到子类的类型（A是抽象类，没有this，是子类调用父类的构造器传递的this）
//        Type type = clazz.getGenericSuperclass();   // 获取子类传递给父类的参数化类型
//        ParameterizedType pType = (ParameterizedType) type; // 它就是A<String>、A<Integer>等
//        Type[] types = pType.getActualTypeArguments();  // 它是一个Class数组
//        Class clazz1 = (Class) types[0];    // 它就是String.class、Integer.class等

        // 写成一句
        Class clazz1 = (Class) ((ParameterizedType) this.getClass().getGenericSuperclass()).getActualTypeArguments()[0];

        System.out.println(clazz1.getName());   // java.lang.String
    }
}

class B extends A<String> { }

class C extends A<Integer> { }
```
---

## 类加载器

- 类加载器分为：
 - **引导ClassLoader**：负责加载核心类库(rt.jar)下的类
 - **扩展ClassLoader**：负责加载扩展包（ext包）下的类
 - **系统ClassLoader**：负责加载classpath下的类，即第三方jar包下的类和自定义类
- 加载类的过程：
 - 哪种ClassLoader加载的类所引发的类加载，就由哪种ClassLoader负责加载
 - 但类加载存在**委托机制：ClassLoader加载类时会先委托给上一级的ClassLoader进行加载，若返回null才自己去加载**。即系统ClassLoader加载类时会先委托给扩展ClassLoader，若扩展ClassLoader返回null，系统ClassLoader才自己去加载；而扩展ClassLoader又会先委托给引导ClassLoader，若引导ClassLoader返回null，扩展ClassLoader才自己去加载
- 委托机制的好处：安全，核心类库下的类永远都是由引导ClassLoader来加载，不会出现核心类被调包的情况
- ClassLoader可以通过**`ClassLoader getParent()`方法来获取上一级ClassLoader**，实现委托机制，**但不允许我们获取引导ClassLoader**，我们调用扩展ClassLoader的getParent()方法会返回null

##### Tomcat的类加载器

- Tomcat自身有两种ClassLoader，它们的特点是先自己动手，再委托上一级ClassLoader
 - 服务器ClassLoader，先自己动手，找不到再委托系统ClassLoader
 - 应用ClassLoader，先自己动手，找不到再委托服务器ClassLoader
![](.\MyPic\TomcatClassLoader.jpg)