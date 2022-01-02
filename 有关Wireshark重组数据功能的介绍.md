**有关Wireshark重组数据功能的介绍**

网络协议通常需要传输自身完整的大块数据，比如在传输文件时。底层协议可能无法处理数据块大小(例如网络数据包大小的限制)，或者是基于流的协议，比如TCP，它根本不知道数据块。在这种情况下，网络协议必须自己处理块边界，并且将数据分散到多个数据包上。显然，它还需要一种机制来确定接收端的边界。

所以，wireshark将这种机制叫做数据报重组，Wireshark的重组功能也是一个很核心的功能，它可以重组一个会话中不同数据包的信息，或者是一个重组一个完整的图片或文件。由于传输的文件往往较大，所以信息分布在多个数据包中。为了能够查看到整个图片或文件，这时候就需要使用重组数据的方法来实现。

**一、重组功能的前提**

在使用重组功能前，一定要确保 Allow subdissector to reassemble TCP streams
选项已启用。如果没有启用，在Wireshark菜单栏中依次选择
Edit\|Preferences\|Protocols，然后将 Allow subdissector to reassemble TCP
streams 复选框勾。

![](media/9dbc6fc29681841586511106dbf0bbfb.png)

FTP 通信使用命令和数据通道进行连接。数据通道仅由 TCP
握手连接数据包。在数据通道中使用重组功能，可以很容易地将传输的文件重新组成原始格式。

**二、重组 FTP 数据功能**

操作步骤如下：

1、启用 Wireshark 功能。

2、选择捕获接口及捕获文件的位置和文件名。

3、单击 Start 按钮开始捕获数据。

4、为了使 Wireshark 能捕获到 FTP 数据包，需要在一个客户端主机上操作一些 FTP
命令。

例如：

C:\\Users\\Administrator\>ftp 192.168.71.105

连接到 192.168.71.105。

220 (vsFTPd 3.0.2)

200 Always in UTF8 mode.

用户(192.168.71.105:(none)): uftp

331 Please specify the password.

密码:

230 Login successful.

ftp\> dir

200 PORT command successful. Consider using PASV.

150 Here comes the directory listing.

\-rwxr-xr-x 1 0 0 25 Mar 01 14:21 hello.txt

\-rwxr-xr-x 1 0 0 270621 Mar 01 14:21 kali linux.jpg

226 Directory send OK.

ftp: 收到 142 字节，用时 0.00秒 35.50千字节/秒。

ftp\> get "kali linux.jpg"

200 PORT command successful. Consider using PASV.

150 Opening BINARY mode data connection for kali linux.jpg (270621 bytes).

226 Transfer complete.

ftp: 收到 270621 字节，用时 0.01秒 24601.91千字节/秒。

ftp\> get hello.txt

200 PORT command successful. Consider using PASV.

150 Opening BINARY mode data connection for hello.txt (25 bytes).

226 Transfer complete.

ftp: 收到 25 字节，用时 0.00秒 25000.00千字节/秒。

ftp\> quit

221 Goodbye.

以上信息执行了登陆 FTP 服务器、下载文件及退出等命令。

1.  此时返回到 Wireshark 界面，停止捕获。捕获到了FTP 数据

![](media/b0f44661b3f6b5cfd06387dba75f0a16.png)

6、在 FTP 数据通道上重组传输的文件。在该界面右键单击62帧，并选择 Follow TCP
Stream 命令。

![](media/21bde5092810e01f1f92424584d86a38.png)

7、该界面显示的数据，就是传输的原格式重组后的结果。如果要查看该文件，单击 Save
As 按钮将该会话保存为一个新的文件。保存的文件名格式，必须要与在 RETR 或 STOR
命令中看到的格式相同。

**三、提取 FTP 传输的文件**

1、继续上面的捕获文件

2、显示过滤 FTP 数据包，利用 ftp matches "(?i)user" 过滤规则

![](media/15bba912e57696a33a6b3df09536a34a.png)

1.  从该界面可以看到一个 USER ftp 命令包，右键单击，并选择 Follow TCP Stream。

![](media/431d9ea91b2c0196d05f7fd026edc69a.png)

4、从该界面可以清楚地看到，客户端和服务器之间互相发送的数据。如客户端登录服务器命令
USER 和 PASS、定义的端口命令 PORT、监听目录命令 LIST、请求文件命令 RETR
及断开服务器的连接命令 QUIT。

5、此时我们屏蔽掉 FTP 命令包，在上图中单击 Filter Out This Stream，得到过滤规则

![](media/a3823a361d4f20c4492c66f38dc70510.png)

1.  此时，该捕获文件将不会出现有 FTP
    命令的数据包。此时右键单击任何数据帧，并选择 Follow TCP Stream
    命令，将显示如下图所示的界面。

![](media/c149c2d32fbc34b21f047d6180b696a1.png)

7、该界面显示了在 FTP
中传输的一个文件，并且该文件是一个图片（png）。为了重组该图片，单击 Save
As按钮保存该文件。这样就成功了！
