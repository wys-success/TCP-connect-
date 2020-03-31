# TCP端口扫描

标签（空格分隔）： 未分类

---
该端口扫描是基于TCP三次握手过程
**一.扫描器设计思路：**
获取目标主机和要扫描的常用端口列表，通过主机名得到目标网络的IP地址，用列表中的每一个端口号去连接目标地址，从而确定端口列表中哪些端口开放，哪些关闭。
**二.用到的模块及函数：**
 1.sock模块
    socket.gethostbyname(tgtHost)：将目标主机名转换成IP地址返回
    socket.socket():创建一个socket
    socket.gethostbyaddr(tgtIP)：将目标主机IP转换成域名返回
    socket.setdefaulttimeout(1):设置Socket超时,此处设置超时时间为1秒。
2.threading模块
    threading.Thread(target=function_name,args=(function_parameter1,function_parameterN))：
    function_name: 需要线程去执行的方法名。
    args: 线程执行方法接收的参数，该属性是一个元组，如果只有一个参数也需要在末尾加逗号。
**三.实现代码：**
import socket
//测试当前主机和端口是否开放

    def connScan(host,port):
        try:
            connSkt=socket.socket(socket.AF_INET, socket.SOCK_STREAM) 
            connSkt.connect((host,port))
            print("tcp open port:" + str(port))
        except:
            print('tcp closed:'+str(port))
    def portScan(tgtHost, tgtPorts):    
        try:        
            tgtIP =socket.gethostbyname(tgtHost)             print('Scan Results for: ' + tgtIP)
        except:        
            print("Cannot resolve'%s':Unknown host" % tgtHost)        
            return   
        socket.setdefaulttimeout(1)    
        for tgtPort in tgtPorts:        
            print('Scanning port ' + str(tgtPort))  
            connScan(tgtHost, int(tgtPort))

对百度端口扫描

    portScan('www.baidu.com',[80,443,3389,1433,23,445])

扫描结果

    Scan Results for: 61.135.169.121
    Scanning port 80
    tcp open port:80
    Scanning port 443
    tcp open port:443
    Scanning port 3389
    tcp closed:3389
    Scanning port 1433
    tcp closed:1433
    Scanning port 23
    tcp closed:23
    Scanning port 445
    tcp closed:445

每一个socket扫描都将会耗时几秒钟，效率较低。引入Python线程提高效率。在我们的扫描中利用线程，只需将portScan()函数的迭代改一下。同时要注意import threading

    import threading
    for tgtPort in tgtPorts:
        print('Scanning port ' + str(tgtPort))
        t = threading.Thread(target=connScan, args=(tgtHost,
        int(tgtPort)))
        t.start()

此时的结果为

    Scan Results for: 61.135.169.125
    Scanning port 80
    Scanning port 443
    Scanning port 3389
    Scanning port 1433
    Scanning port 23
    Scanning port 445
    tcp open port:443
    tcp open port:80
    tcp closed:3389
    tcp closed:1433
    tcp closed:445
    tcp closed:23