# TCP/IP协议栈

- **应用层**：为用户提供网络应用接口，规定了应用程序数据的格式和交互规则。如HTTP协议用于网页浏览，规定了浏览器与服务器之间请求和响应的格式；SMTP协议用于邮件发送，规定了邮件的传输格式和过程。
- **传输层**：主要有TCP和UDP协议。TCP提供面向连接、可靠的字节流传输，通过序列号、确认应答、重传机制等保证数据的准确传输，如文件下载时使用TCP确保文件完整无误；UDP提供无连接、不可靠的传输，但速度快、延迟低，如视频直播中使用UDP，即使少量数据丢失也不影响整体观看体验。
- **网络层**：核心协议是IP协议，负责将数据包从源主机传输到目的主机，根据IP地址进行路由选择和转发。还包括ICMP协议用于网络诊断和错误报告，如ping命令就是利用ICMP协议来测试网络连通性。
- **数据链路层**：将网络层传来的数据包封装成帧，通过物理介质传输，并进行错误检测和纠正。如以太网协议规定了局域网中数据帧的格式和传输方式。
- **物理层**：负责将数据转换为物理信号，通过电缆、光纤等物理介质进行传输，规定了物理介质的电气、机械特性等。

## 客户/服务器模式

- **服务器端**：创建Socket并绑定到指定的IP地址和端口号，通过监听端口等待客户端连接。接收到连接请求后，创建新的进程或线程处理与客户端的通信，完成任务后关闭连接。
- **客户端**：创建Socket，指定服务器的IP地址和端口号，向服务器发起连接请求。连接建立后，与服务器进行数据交互，完成任务后关闭连接。

## Socket编程接口

- **Socket创建**：使用socket系统调用创建一个Socket，指定协议族（如AF_INET表示IPv4）、Socket类型（如SOCK_STREAM表示TCP套接字，SOCK_DGRAM表示UDP套接字）和协议（通常为0，由系统根据前两个参数自动选择）。
- **地址绑定（服务器端）**：服务器端使用bind系统调用将创建的Socket绑定到指定的IP地址和端口号，使Socket与特定的网络地址关联，以便接收客户端的连接请求。
- **连接建立（TCP）**：对于TCP套接字，服务器端使用listen系统调用使Socket处于监听状态，等待客户端连接。客户端使用connect系统调用向服务器发起连接请求，服务器通过accept系统调用接受连接，返回一个新的Socket用于与客户端进行通信。
- **数据传输**：使用send（TCP）或sendto（UDP）系统调用发送数据，数据从用户空间复制到Socket发送缓冲区，由协议栈封装成数据包发送到网络。使用recv（TCP）或recvfrom（UDP）系统调用接收数据，数据从网络进入Socket接收缓冲区，再复制到用户空间。
- **连接关闭**：使用close系统调用关闭Socket，释放相关资源。对于TCP连接，会经历四次挥手过程来确保数据的完整传输和连接的正常关闭。

## 网络地址与端口

- **IP地址**：用于标识网络中的设备，分为IPv4和IPv6。IPv4是32位二进制数，通常用点分十进制表示；IPv6是128位二进制数，采用冒号十六进制表示。如192.168.0.1是常见的IPv4地址，2001:0db8:85a3:0000:0000:8a2e:0370:7334是IPv6地址。
- **端口号**：16位整数，范围是0-65535，用于标识同一主机上的不同应用程序或服务。0-1023为系统保留端口，如80端口用于HTTP服务，21端口用于FTP服务；1024-65535为用户可使用的端口，应用程序可以选择其中未被占用的端口进行通信。

## 并发处理

- **多进程方式**：服务器为每个客户端连接创建一个子进程，子进程独立处理与客户端的通信，互不干扰。优点是稳定性高，一个子进程出现问题不会影响其他进程；缺点是创建和销毁进程开销大，占用资源多。
- **多线程方式**：服务器使用一个线程来处理一个客户端连接，多个线程共享进程的资源。优点是创建和切换线程开销小，能高效利用资源；缺点是需要注意线程安全问题，如多个线程同时访问共享数据时可能导致数据不一致。
- **异步I/O方式**：使用select、poll或epoll等系统调用实现异步I/O，服务器可以同时监控多个Socket的状态，当有数据可读或可写时才进行相应处理，避免阻塞在I/O操作上，提高了服务器的并发处理能力。
