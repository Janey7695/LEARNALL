<!-- wp:paragraph -->
<p>本文仅为我个人的学习记录，非专业教程</p>
<!-- /wp:paragraph -->

<!-- wp:group -->
<div class="wp-block-group"><div class="wp-block-group__inner-container"><!-- wp:heading {"level":3} -->
<h3>一、初识TCP/IP</h3>
<!-- /wp:heading --></div></div>
<!-- /wp:group -->

<!-- wp:paragraph -->
<p>      TCP/IP（Transmission Control Protocol/Internet Protocol，传输控制协议/网际协议）是指能够在多个不同网络间实现信息传输的协议簇。TCP/IP协议不仅仅指的是<a rel="noreferrer noopener" target="_blank" href="https://baike.baidu.com/item/TCP/33012">TCP</a> 和<a rel="noreferrer noopener" target="_blank" href="https://baike.baidu.com/item/IP/224599">IP</a>两个协议，而是指一个由<a rel="noreferrer noopener" target="_blank" href="https://baike.baidu.com/item/FTP/13839">FTP</a>、<a rel="noreferrer noopener" target="_blank" href="https://baike.baidu.com/item/SMTP/175887">SMTP</a>、TCP、<a rel="noreferrer noopener" target="_blank" href="https://baike.baidu.com/item/UDP/571511">UDP</a>、IP等协议构成的协议簇， 只是因为在TCP/IP协议中TCP协议和IP协议最具代表性，所以被称为TCP/IP协议。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4> 1.1 IP</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>       在通信时，通信双方必须知道对方的标识，互联网上每台计算机之间的唯一标识就是IP地址，由4位数字组成，例如202.196.32.7。IP负责把数据从一台计算机发送到另一台计算机。数据被分割成一小块一小块，然后通过IP包发送出去。IP包的特点是按块发送，途径多个路由，但不能保证到达，也不能保证顺序到达。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4>1.2 TCP和UDP</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>         TCP是建立在Ip之上的。负责两台机器之间建立可靠连接，保证数据包按顺序到达。TCP通过握手建立连接，中途如果有包丢失，会自动重发。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>         UDP同样建立在IP上，但是它是面向无连接的通信协议。UDP不保证数据顺利到达，是一种不可靠传输，但是它的效率高。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4>1.3 端口</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>         一个ip包除了包含要传输的数据外，还包含源ip和目标ip、源端口和目标端口。一台计算机仅有一个ip，但是可能同时会有多个程序需要联网通信。比如同时开启QQ和浏览器。一个ip包来了之后，是要给QQ还是给浏览器？取决与端口号，每个程序会占有一个自己的端口号。常见浏览器使用80端口，FTP之间使用21端口，邮件收发使用25端口。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4>1.4 socket套接字</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>         Socket（套接字）是一个网络编程的抽象概念。是一套网络编程通用API。socket是TCP/IP网络通信最常用的API。套接字决定了连接方式，传输协议，通常我们用socket表示打开了一个网络连接。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3> 二、windows环境c/c++中TCP通信</h3>
<!-- /wp:heading -->

<!-- wp:heading {"level":4} -->
<h4>2.1 进行初始化</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>         windows环境下进行使用socket，需要引入WinSock2.h的头文件，并进行对动态库的添加<code>#pragma comment(lib, "wsock32.lib")</code></p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted"><span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">WinSock2.h</span>><winsock2.h>   //这个需要写在开头
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span></winsock2.h><<span class="has-inline-color has-vivid-green-cyan-color">windows.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#<winsock2.h><windows.h>pragma</windows.h></winsock2.h></span><winsock2.h><windows.h> comment(lib,"<span class="has-inline-color has-vivid-green-cyan-color">wsock32.lib</span>")</windows.h></winsock2.h></pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>         现在拟建立一套有应答的服务器的客户端交流的体系，首先来编写服务器的程序,新建一个c文件server.c</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted"><span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">WinSock2.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">Windows.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#pragma</span> comment(lib, "<span class="has-inline-color has-vivid-green-cyan-color">wsock32.lib</span>")
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">stdio.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">string.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">asseert.h</span>> <em>//提供断言函数assert()</em>
<span class="has-inline-color has-vivid-purple-color">int</span> <strong><span class="has-inline-color has-vivid-red-color">main</span></strong>(){
    
}</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>之后需要调用<code>WSAStartup()</code>函数进行初始化。在使用套接字进行编程之前，无论是服务器还是客户端都必须加载WindowsSOCKET动态库。函数WSAStartup就实现了此功能。它是套接字应用程序<strong>必须</strong>调用的第一个函数。WSAStartup有返回值，如果初始化成功，会返回0。</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted"><span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">WinSock2.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">Windows.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#pragma</span> comment(lib, "<span class="has-inline-color has-vivid-green-cyan-color">wsock32.lib</span>")
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">stdio.h</span>>
<span class="has-inline-color has-vivid-cyan-blue-color">#include</span><<span class="has-inline-color has-vivid-green-cyan-color">string.h</span>>

<span class="has-inline-color has-vivid-purple-color">int</span> <strong><span class="has-inline-color has-vivid-red-color">main</span></strong>(){
    <span class="has-inline-color has-vivid-purple-color"><strong>WORD</strong></span> <span class="has-inline-color has-vivid-purple-color">sVersionRequire</span> = <strong><span class="has-inline-color has-vivid-red-color">MAKEWORD</span></strong>(<span style="color:#533237" class="has-inline-color">2,2</span>);
    <strong><span class="has-inline-color has-vivid-purple-color">WSADATA</span></strong> <span class="has-inline-color has-vivid-purple-color">data</span>;
    <span class="has-inline-color has-vivid-cyan-blue-color">if</span>(<strong><span class="has-inline-color has-vivid-red-color">WSAStartup</span></strong>(<span class="has-inline-color has-vivid-purple-color">sVersionRequire</span>,<span class="has-inline-color has-vivid-purple-color">&data</span>)!=0)
    {
        <strong><span class="has-inline-color has-vivid-red-color">printf</span></strong>("<span class="has-inline-color has-vivid-green-cyan-color">initfailed</span>\n");
                <span class="has-inline-color has-vivid-red-color">assert</span>(0);    <em>//若初始化失败，会退出程序 并打印initfailed</em>
        <span class="has-inline-color has-vivid-cyan-blue-color">return</span> 0;
    }
}</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>第一个参数指定准备加载的WindowsSOCKET动态库的版本。一般使用宏MAKEWORD构造。如MAKEWORD(2,2）表示加载2.2版本。WSADATA会返回被加载动态链接库的基本信息。如是否加载成功，以及当前实际使用的版本。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4>2.2 创建一个socket</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>       使用<code>SOCKET sockname = socket(af,type,protocao);</code>来创建一个套接字，其中SOCKET通过查询头文件<img class="wp-image-41" style="width: 150px;" src="http://112.124.31.67/wp-content/uploads/2020/06/微信图片_20200630143341.png" alt="">定义</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":42,"sizeSlug":"large"} -->
<figure class="wp-block-image size-large"><img src="http://112.124.31.67/wp-content/uploads/2020/06/微信图片_20200630143341-1.png" alt="SOCKET的数据类型定义" class="wp-image-42"/><figcaption>数据类型SOCKET的定义</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>可知SOCKET其实是unsigned long long,它说明socket函数会有返回值，事实上，当socket生成套接字成功时，会返回0。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>af表示的是socket使用协议的地址家族，如AF_INET表示使用IPv4协议，AF_INET6表示使用IPv6协议。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>type表示使用何种数据传输方式，如以数据流（TCP）的方式：SOCK_STREAM，以数据报的形式（UDP）:SOCK_DGRAM,以原始套接字方式：SOCK_RAM。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>protocao，指示使用的协议。对于SOCK_STREAM套接字类型，该字段可以为IPPROTO_TCP或0。对于SOCK_DGRAM套接字类型，该字段为IPPROTO_UDP或0。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>使用socket（）创建一个名为Sersocket的套接字</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">#include<WinSock2.h>
#include <windows.h>
#pragma comment(lib, "wsock32.lib")
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<assert.h>

int <strong>main</strong>()
{
    WORD sVersionRequire = <strong>MAKEWORD</strong>(2,2);
    WSADATA data;
    if(<strong>WSAStartup</strong>(sVersionRequire,&data)!=0)
    {
        <strong>printf</strong>("initfailed\n");
        return 0;
    }
    SOCKET Sersocket=<strong>socket</strong>(AF_INET,SOCK_STREAM,0);
    if(Sersocket==INVALID_SOCKET)
    {
        <strong>printf</strong>("create socket fail\n");
        <strong>assert</strong>(0);
    }
    else{
        <strong>printf</strong>("create socket successfully\n");
    }
    <strong>closesocket</strong>(Sersocket); //调试结束时关闭socket
    <strong>WSACleanup</strong>();
    return 0;
}</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>这时候可以在windows命令行输入<code>gcc  server.c的目录\\server.c  -o server.c的目录\\sever.exe -lwsock32</code>对文件进行编译，生成可执行文件，然后打开可执行文件观察是否输出了create socket successfully.</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4>2.3 使用bind()将刚刚创建的套接字绑定到某一地址上</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>使用<code>int bind(SOCKET s,const struct sockaddr* name,int namelen);</code>函数来绑定套接字，<code>SOCKET s</code>表示需要绑定的套接字，<code>const struct sockaddr* name</code>则包含了欲绑定地址的信息，namelen是name的长度。先看一个用于TCP/IP地址的类似结构体sockaddr的结构socketaddr_in：</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">struct sockaddr_in{
    short sin_family;
    u_short sin_port;
    struct in_addr sin_addr;
    char sin_zero[8];
}</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>其中，sin_family表示协议的地址家族，如同socket里面的af，如果使用ipv4协议，填AF_INET。sin_port表示端口，使用时加上htons(端口号)。sin_addr是一个表示ip地址的结构体，其结构如下：</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">struct in_addr{
    union{
       struct{
           u_char s_b1,s_b2,s_b3,s_b4;
         }S_un_b;
       struct{
           u_short s_w1,s_w2;
         }S_un_w;
       u_long S_addr;
     }S_un;
}</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>结构体内部使用了联合体，起到节省空间的作用。联合体中又有3个成员，分别表示IP的3种表示方式：S_un_b:使用4个字节表示IP地址的四个数字、S_un_w:使用2个双字节来表示IP地址的四个数字、S_addr：使用一个长整型来表示IP地址。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>sockaddr的使用范围不止于TCP/IP，sockaddr_in与sockaddr都有16个字节，sockaddr_in能够强制类型转化为sockaddr。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>而为in_addr赋值可以使用一个简单的方法：<code>inet_addr()</code>函数，如</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>sin_addr.S_un.S_addr=inet_addr(IP地址)</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>同样也可以使用它的反函数inet_ntoa()将in_addr类型转化为IP字符串，如</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>char getip=inet_ntoa(sockaddr.sin_addr);</code>//参数是一个 in_addr 数据类型</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>要bind一个地址，首先得要定义一个地址(即bind()函数参数里面的name)：</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">struct sockaddr_in Seraddr;
Seraddr.sin_family=AF_INET;
Seraddr.sin_port=<strong>htons</strong>(4396);
Seraddr.sin_addr.S_un.S_addr=<strong>inet_addr</strong>("127.0.0.1");</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>在这里，决定了sersocket将要连接的地址：本机的4396端口(127.0.0.1是大多数计算机机内通信的地址)。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>bind函数同样也有返回值，因此也可以借此判断是否绑定成功</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">if(<strong>bind</strong>(Sersocket,(SOCKADDR*)&Seraddr,sizeof(Seraddr))==SOCKET_ERROR)
    {
        <strong>printf</strong>("fail to bind to 127.0.0.1 \n");
        <strong>assert</strong>(0);
    }
    else
    {
        <strong>printf</strong>("success to bind to 127.0.0.1\n");
    }</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>此时文件看起来应是这个样子</p>
<!-- /wp:paragraph -->

<!-- wp:preformatted -->
<pre class="wp-block-preformatted">#include<WinSock2.h>
#include <windows.h>
#pragma comment(lib, "wsock32.lib")
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<assert.h>

int <strong>main</strong>()
{
    //初始化
    WORD sVersionRequire = <strong>MAKEWORD</strong>(2,2);
    WSADATA data;
    if(<strong>WSAStartup</strong>(sVersionRequire,&data)!=0)
    {
        <strong>printf</strong>("initfailed\n");
        return 0;
    }
    //创建套接字
    SOCKET Sersocket=<strong>socket</strong>(AF_INET,SOCK_STREAM,0);
    if(Sersocket==INVALID_SOCKET)
    {
        <strong>printf</strong>("create socket fail\n");
        <strong>assert</strong>(0);
    }
    else{
        <strong>printf</strong>("create socket successfully\n");
    }
    //绑定套接字到ip
    struct sockaddr_in Seraddr;
    Seraddr.sin_family=AF_INET;
    Seraddr.sin_port=<strong>htons</strong>(4396);
    Seraddr.sin_addr.S_un.S_addr=<strong>inet_addr</strong>("127.0.0.1");
    if(<strong>bind</strong>(Sersocket,(SOCKADDR*)&Seraddr,sizeof(Seraddr))==SOCKET_ERROR)
    {
        <strong>printf</strong>("failed to bind to 127.0.0.1 \n");
        <strong>assert</strong>(0);
    }
    else
    {
        <strong>printf</strong>("succeed to bind to 127.0.0.1\n");
    }
    //结束时关闭套接字
    <strong>closesocket</strong>(Sersocket);
    <strong>WSACleanup</strong>();
    return 0;
}</pre>
<!-- /wp:preformatted -->

<!-- wp:paragraph -->
<p>使用同样的方法编译文件，执行后可以看到succeed to bind to 127.0.0.1</p>
<!-- /wp:paragraph -->
<!-- wp:heading {"level":4} -->
 <h4>2.4 通过listen使套接字监听某个地址</h4>
 <!-- /wp:heading -->
 <!-- wp:paragraph -->
 <p>使用函数<code>listen()</code>使刚刚绑定了地址的套接字开始监听绑定的地址。<code>int listen(SOCKET s,int num)</code>,其中s表示要进行监听的套接字，num为运行的最大连接数，即最多能有多少个客户端连接IP时被监听到。同样，<code>listen()</code>也有返回值，成功返回0，失败返回SOCKET_ERROR。</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>....
 if(listen(Sersocket,5)==SOCKET_ERROR)
  {
  printf("failed to listen 127.0.0.1\n");
  assert(0);
  }
  else
  {
  printf("succeed to listen 127.0.0.1,waiting for creating a link with client...\n");
  }
  closesocket(Sersocket);
  WSACleanup();
  return 0;</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>server.c中加入这段代码后，编译执行，可以看到命令行出现<br>create socket successfully<br>succeed to bind to 127.0.0.1<br>succeed to listen 127.0.0.1,waiting for creating a link with client...<br>PS D:\ProgPra\csocket2></p>
 <!-- /wp:paragraph -->
 <!-- wp:heading {"level":4} -->
 <h4>2.5 accept一个客户端请求</h4>
 <!-- /wp:heading -->
 <!-- wp:paragraph -->
 <p>使用<code>SOCKET accept(SOCKET s,struct sockaddr* addr,int* addrlen)</code>函数接受一个被监听到的客户端的请求。s表示负责监听的套接字，这里即指服务器套接字，addr是一个将要接收客户端地址的结构体，如同上面bind时候使用的Seraddr，addrlen表示返回客户端地址的长度。可以看到accept的返回值是SOCKET，因此它会返回一个套接字，这个套接字表示的就是客户端的套接字，服务器可以通过这个返回的客户端套接字与客户端进行通信。</p>
 <!-- /wp:paragraph -->
 <!-- wp:paragraph -->
 <p>在accept之前，我们需要先创建一个struct sockaddr变量。</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>struct sockaddr_in cliaddr;
 int cliaddrlen=sizeof(cliaddr);</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>然后将accept接收到的客户端生成一个可供服务器与客户端通信的套接字</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>SOCKET Clisocket=accept(Sersocket,(SOCKADDR*)&cliaddr,&cliaddrlen);</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>之后同样需要对Clisocket的合法性进行检验，代码添加，最后是这样</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>....
 struct sockaddr_in cliaddr;
  int cliaddrlen=sizeof(cliaddr);
  SOCKET Clisocket=accept(Sersocket,(SOCKADDR*)&cliaddr,&cliaddrlen);
  if(Clisocket==INVALID_SOCKET)
  {
  printf("failed to create a link with client");
  assert(0);
  }
  else
  {
  printf("successed to create a link with a client from: %s\n",inet_ntoa(cliaddr.sin_addr));
  }
  closesocket(Sersocket);
  WSACleanup();
  return 0;</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>使用inet_ntoa会将存储在addr里面的地址以字符串的形式转译出来，可以用是个函数来显示连接的客户端的IP地址。</p>
 <!-- /wp:paragraph -->
 <!-- wp:paragraph -->
 <p>此时编译并执行后，与之前的结果是一样的，因为我们没有接入客户端，因此accept不会有响应。自此，如果我们要检验accept的正确性，就需要有一个可接入服务器的客户端，因此，开始编写客户端的程序，在同目录下创建一个叫client.c的文件，并引入相同的头文件，以及相同的初始化</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>#include <winsock2.h>
 #include <windows.h>
 #pragma comment(lib,"ws2_32.lib")
 #include<string.h>
 #include<stdio.h>
 #include<assert.h>
 int main(){
  WORD sVersionRequire = MAKEWORD(2,2);
  WSADATA data;
  if(WSAStartup(sVersionRequire,&data)!=0)
  {
  printf("initfailed\n");
  return 0;
  }
 }</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>同样的，客户端也需要一个代表本机的套接字，创建方法与服务器相同</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>....
 SOCKET Clisocket = socket(AF_INET,SOCK_STREAM,0);
  if(Clisocket==INVALID_SOCKET)
  {
  printf("create socket fail\n");
  closesocket(Clisocket);
  WSACleanup();
  assert(0);
  }
  else
  {
  printf("create socket successfully");
  }</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>之后，与服务器不同的是，客户端不需要bind到一个ip地址，也不需要监听某个端口，客户端需要做的是connect到一个服务器上，因此使用<code>int connect(SOCKET s,const struct sockaddr* addr,int addrlen)</code>，其中s是需要进行连接的客户端，即这里的Clisocket，addr是服务器的地址信息，addrlen是addr的长度，首先要定义addr</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>struct sockaddr_in Seraddr;
 Seraddr.sin_family=AF_INET;
 Seraddr.sin_port=htons(4396);
 Seraddr.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>服务器采用AF_INET的通信协议族，欲连接的端口号是4396（与之前在服务器监听的端口号一致），要连接的IP地址是127.0.0.1,即与本机的服务器通信。</p>
 <!-- /wp:paragraph -->
 <!-- wp:paragraph -->
 <p>connect同样是一个有返回值的函数，因此我们也可以用这个来进行验证，看是否连接成功，添加代码后看起来是这个样子的</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>#include <winsock2.h>
 #include <windows.h>
 #pragma comment(lib,"ws2_32.lib")
 #include<string.h>
 #include<stdio.h>
 #include<assert.h>
 int main(){
  WORD sVersionRequire = MAKEWORD(2,2);
  WSADATA data;
  if(WSAStartup(sVersionRequire,&data)!=0)
  {
  printf("initfailed\n");
  return 0;
  }
  SOCKET Clisocket = socket(AF_INET,SOCK_STREAM,0);
  if(Clisocket==INVALID_SOCKET)
  {
  printf("create socket fail\n");
  closesocket(Clisocket);
  WSACleanup();
  assert(0);
  }
  else
  {
  printf("create socket successfully\nwaiting for creating a link with server....\n");
  }
  struct sockaddr_in Seraddr;
  Seraddr.sin_family=AF_INET;
  Seraddr.sin_port=htons(4396);
  Seraddr.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
  if(connect(Clisocket,(SOCKADDR*)&Seraddr,sizeof(Seraddr))==SOCKET_ERROR)
  {
  printf("failed to create a link with 127.0.0.1\n");
  closesocket(Clisocket);
  WSACleanup();
  assert(0);
  }
  else
  {
  printf("successed to link with 127.0.0.1\n");
  }
  closesocket(Clisocket);
  WSACleanup();
  return 0;
 }</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>现在可以编译client.c文件，生成可执行文件。我们只运行client.exe文件，会在命令行看到如下字样</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>create socket successfully
 waiting for creating a link with server....
 failed to create a link with 127.0.0.1
 Assertion failed!
 Program: d:\ProgPra\csocket2\CLIENT.exe
 File: d:\ProgPra\csocket2\CLIENT.c, Line 37
 Expression: 0
 PS D:\ProgPra\csocket2></code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>程序通过在connect里面的断言退出了，此时说明之前的步骤都正确，这里退出是因为没有连接到服务器，因此我们现在不用命令行，直接双击打开服务器的程序server.exe，会有如下画面</p>
 <!-- /wp:paragraph -->
 <!-- wp:paragraph -->
 <p>然后再用命令行打开client.exe，这样，两者就开始通信，命令行会有如下画面</p>
 <!-- /wp:paragraph -->
 <!-- wp:paragraph -->
 <p>然后server.exe忽然消失，因为程序主线程执行完毕后，程序就自动关闭了，如果我们想观察server.exe最后到底发生了什么，可以在server.c里面引入头文件<stdlib.h> 然后在程序末尾加上<code>system("pause");</code>函数</p>
 <!-- /wp:paragraph -->
 <!-- wp:code -->
 <pre class="wp-block-code"><code>......
  SOCKET Clisocket=accept(Sersocket,(SOCKADDR*)&cliaddr,&cliaddrlen);
  if(Clisocket==INVALID_SOCKET)
  {
  printf("failed to create a link with client");
  assert(0);
  }
  else
  {
  printf("successed to create a link with a client from: %s\n",inet_ntoa(cliaddr.sin_addr));
  }
  closesocket(Sersocket);
  WSACleanup();
  system("pause");
  return 0;
 }</code></pre>
 <!-- /wp:code -->
 <!-- wp:paragraph -->
 <p>重新编译，再次先双击打开server.exe,然后再在命令行里面运行client.exe，可以在server里面观察到如下画面</p>
 <!-- /wp:paragraph -->
 <!-- wp:paragraph -->
 <p>可以看到，服务器与客户端已经成功连接，进而证明了服务器代码里面accept的正确。</p>
 <!-- /wp:paragraph -->
 <!-- wp:heading {"level":4} -->
 <h4>2.6 服务器与客户端之间的信息交流</h4>
 <!-- /wp:heading -->