# Level-IP [![Build Status](https://travis-ci.org/saminiir/level-ip.svg?branch=master)](https://travis-ci.org/saminiir/level-ip)

Level-IP是一款运行在Linux用户空间的TCP协议栈，它有以下特点

- 独立运行，与应用程序完全隔离

- 使用Linux虚拟网卡实现通信

- 兼容Linux Socket API

- 为其他应用程序，提供网路链接服务

大家要注意的是，该tcp协议栈并不完美，目前它只实现了以下功能:

- ARP请求、回复、缓存

- ICMP ping和回复

- IPv4数据包处理，校验和

- TCPv4握手

- TCP数据传输

- TCP分段重组

- 超时重传和RTT估计

以下功能还有待完善:

- ip分片重组

- 滑动窗口

- 糊涂窗口与避免

- 零窗口探针

- 拥塞控制

- select套接字

- ...

# 使用Level-IP

1. 获取Level-IP源码

    ```
    git clone https://github.com/EmbedHacker/level-ip
    ```
2. 安装libcap-dev工具，以修改可执行程序的权限

    ```
    sudo apt install libcab-dev
    ```
3. 编译所有的目标文件

    ```
    make all
    ```
4. 开启路由功能

    ```
    sudo sysctl -w net.ipv4.ip_forward=1
    ```
5. 接受虚拟网卡的输入信息

    ```
    sudo iptables -I INPUT --source 10.0.0.0/24 -j ACCEPT
    ```
6. 伪装真实网卡的ip，注意enp0s3为真实网卡，
    **用户应根据ifconfig命令的查询结果修改**
    ```
    sudo iptables -t nat -I POSTROUTING --out-interface enp0s3 -j MASQUERADE
    ```
7. 设置真实网卡数据转发给虚拟网卡，注意修改真实网卡!!
    ```
    sudo iptables -I FORWARD --in-interface enp0s3 --out-interface tap0 -j ACCEPT
    ```
8. 设置虚拟网卡数据转发给真实网卡,注意修改真实网卡!!
    ```
    sudo iptables -I FORWARD --in-interface tap0 --out-interface enp0s3 -j ACCEPT
    ```

9. 运行tcp协议栈
    ```
    ./lvl-ip
    ```

10. 重新打开另一个端口，运行curl应用程序，抓取百度网址
    ```
    cd tools
    ./level-ip ../apps/curl/curl www.baidu.com 80
    ```

如果curl程序和tcp协议栈正常运行，则抓取结果如下:

```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: no-cache
Content-Length: 14615
Content-Type: text/html
Date: Mon, 22 Jun 2020 14:03:20 GMT
P3p: CP=" OTI DSP COR IVA OUR IND COM "
P3p: CP=" OTI DSP COR IVA OUR IND COM "
Pragma: no-cache
Server: BWS/1.1
Set-Cookie: BAIDUID=EE1559CC045E03490CEE4C7D88B5DC37:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: BIDUPSID=EE1559CC045E03490CEE4C7D88B5DC37; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: PSTM=1592834600; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: BAIDUID=EE1559CC045E03491F87D3CE34948683:FG=1; max-age=31536000; expires=Tue, 22-Jun-21 14:03:20 GMT; domain=.baidu.com; path=/; version=1; comment=bd
Traceid: 1592834600066917530611219588553201043838
Vary: Accept-Encoding
X-Ua-Compatible: IE=Edge,chrome=1
Connection: close

<!DOCTYPE html><!--STATUS OK-->
<html>
<head>
	<meta http-equiv="content-type" content="text/html;charset=utf-8">
	<meta http-equiv="X-UA-Compatible" content="IE=Edge">
	<link rel="dns-prefetch" href="//s1.bdstatic.com"/>
	<link rel="dns-prefetch" href="//t1.baidu.com"/>
	<link rel="dns-prefetch" href="//t2.baidu.com"/>
	<link rel="dns-prefetch" href="//t3.baidu.com"/>
	<link rel="dns-prefetch" href="//t10.baidu.com"/>
	<link rel="dns-prefetch" href="//t11.baidu.com"/>
	<link rel="dns-prefetch" href="//t12.baidu.com"/>
	<link rel="dns-prefetch" href="//b1.bdstatic.com"/>
	<title>百度一下，你就知道</title>
	<link href="http://s1.bdstatic.com/r/www/cache/static/home/css/index.css" rel="stylesheet" type="text/css" />
...

```


# 参考资料

* Linux内核TCP/IP协议栈, [source code](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/net/ipv4)
* picoTCP, [source code](https://github.com/tass-belgium/picotcp)

# 版权

[LICENSE.md](LICENSE.md) (MIT)
