## 什么是抓包

抓包（packet capture）就是将网络传输发送与接收的数据包进行截获、重发、编辑、转存等操作，也用来检查网络安全。  -- 摘自某百科

## 抓包工具

桌面端比较著名的有 Fiddler/Charles/Wireshark 等等，移动端有 Thor/Stream/HttpCanary/Surge 等等，还有众多优秀的抓包软件就不一一列举了。所有的抓包工具本质上就是一个代理，功能就是把所有通过这个代理的网络请求显示出来，甚至进行进一步的修改操作。好的抓包工具显示的信息更直观一些，操作起来更顺手。
不管抓包工具是运行在 PC 上还是手机上，还是在路由器、VPS 上，其他的设备都可以通过它提供的端口进行数据传送，所以理论上每个抓包工具都可以对任意平台的流量进行抓包处理。上面提到的抓包工具网络上都有非常详细的教程，今天本文要介绍的是：**[elecV2P](https://github.com/elecV2/elecV2P)**，这款工具可能没有前面所提及的那么优秀，甚至可能还有比较多的缺点，但还是有些可圈可点的地方：这是一款基于 Nodejs 的网络工具，能装 Nodejs 就能跑，同时还提供了 Docker 版本，有 Docker 环境就能运行。所有前端操作都在网页上完成，无需安装任何客户端。除了基础的抓包功能外，还能通过手动或 JS 脚本来修改网络请求。如果觉得 JS 的功能还不够强大，甚至还可以直接使用 shell 指令，比如 *rm -f \** 等, 一条网络请求实现从删库到跑路。另外，还可以方便的添加定时任务，定时执行 JS/Shell/Python 等脚本。

![](https://raw.githubusercontent.com/elecV2/elecV2P-dei/master/docs/res/overview.png)

## 实操抓包

事前准备：
- 本文不会详述 网络请求/代理/分流/MITM 等基本原理，如有需求，善用搜索
- 已正常运行 elecV2P，且三个端口处于可访问状态
- 一个方便设置分流的代理软件（非必须，使用系统代理或浏览器插件也行）

本文假设 **elecV2P** 运行在本地主机 192.168.1.101 上，三个端口分别为默认的：80/8001/8002。
- 80   后台管理 webUI 端口。以下简称 webUI 或 webUI 页面
- 8001 ANYPROXY 代理端口
- 8002 ANYPROXY 代理请求查看端口

*在后面的步骤中，请根据个人的实际情况，进行替换*

然后，就可以进入主题了。本文主要分为以下几个部分：
- 证书安装
  - 默认证书
  - 自签证书
- 代理设置
  - 系统代理
  - 分流代理
- 查看网络请求
- 修改网络请求
  - 手动修改
  - 脚本修改
- COOKIE 获取
  - 手动查找复制
  - 脚本自动保存

## 证书安装

elecV2P 启动时会先在项目的 **rootCA** 文件夹下查找根证书，如果没有，则会在主机的 `$HOME/.anyproxy/certificates` 目录下生成一个新的，包含 **rootCA.key/rootCA.crt** 两个文件。如果有，则会自动把 **rootCA** 文件夹下根证书文件拷贝到 `$HOME/.anyproxy/certificates` 目录。
安装证书可手动进入该目录打开 rootCA.crt 文件进行安装并信任。或者通过默认浏览器打开 http://192.168.1.101/crt 进行下载安装，在 webUI->MITM 的界面也有证书下载的按钮。要抓包哪台设备的流量，就在相应的设备上安装信任证书。（**安装未知来源的根证书有风险，请谨慎操作**）

（可省略步骤：为了避免 `$HOME/.anyproxy/certificates` 目录下的根证书被删除（比如 Docker 升级），elecV2P 又生成一个新的证书，而新证书又需要重新安装和信任，比较麻烦。可以在 elecV2P 第一次启动生成根证书后，把 **rootCA.key/rootCA.crt** 这两个文件复制到项目的 **rootCA** 文件夹下，并进行备份。）
 
也可以把其他的自签证书复制到 **rootCA** 文件夹下进行应用。在 webUI->MITM 界面可以生成一个自签证书（或者通过其他途径生成）。**启用新的根证书，需要重启服务。**（记得先清空一下由之前根证书生成的其他域名证书）

## 代理设置

最简单的就是使用系统代理，无需使用其他软件，PC和手机上都能直接设置。

![](https://elecv2.github.io/src/winproxy.png)

设置好后，就可以在 8002 端口页面看到一些网络请求记录了。（基本的 HTTP 请求，在浏览器开发者工具中就能看到所有信息，本文不作讨论。下文提到的网络请求都默认表示 HTTPS 网络请求）

![](https://elecv2.github.io/src/a800201.png)

但是都只能看到一条 CONNECT 的请求信息，无法看到其中的内容。因为这个请求是 HTTPS 加密的，这个时候就需要使用 MITM 来解密。在 webUI->MITM 界面的 MITM host 栏，添加需要解析的域名，然后保存。

![](https://elecv2.github.io/src/mitmhost.png)

刷新一下之前的网站，就可以在 8002 端口看到相关网络请求的详细信息了。

![](https://elecv2.github.io/src/a8002req.png)
![](https://elecv2.github.io/src/a8002res.png)

到这一步，一个简单的抓包过程就算是完成了。一般需要的 cookie 信息就包含在 Request 的 Header 里面，相应的请求体（body）也可以直接拷贝出来。如果有一定的编程基础，就可以通过任意编程语言（JS/PYTHON/PHP/GO/NODEJS 等）写一个简单的脚本，来“重放”这个网络请求。至于这个网络请求中的 URL/HEADER/BODY 哪些参数是必须的，哪些是可以舍弃，需要不断的测试才能知道结果，如果无法判断全部保留即可。当然并不是所有的服务器都接受这种简单的重放请求，甚至可能会有证书检测（此时无法进行MITM，比如一些银行等安全级别比较高的网站），有些请求的 HEADER 或者 BODY 中包含一些特殊的参数，比如时间戳，或者使用特定密钥+算法形成的加密字符串等等，如果服务器验证没有通过，则重放请求失败。但是，目前来说，绝大部分的请求都可以通过脚本重放实现，尤其是一些简单的签到请求。

使用全局代理的一个缺点就是，所有的网络请求都会通过这个端口，所以短时间内可能会有大量的请求记录，要找到需要的特定网络请求会比较麻烦，于是我们可以设置分流代理，只把特定域名的流量转发到 ANYPROXY 端口进行解密。

系统有自带的分流模式，就是 **PAC 代理**，但不推荐使用，设置稍微有点复杂，网络上可以找到很详细的教程。一般还是推荐使用代理软件来实现分流，如果使用 chrome 浏览器，推荐使用插件 **SwitchyOmega** 来实现分流设置，其他客户端有 clash/小火箭/QuantumultX/Surge 等。

首先，在这些软件上添加一个 HTTP 代理：
- 代理地址: 192.168.1.101
- 代理端口: 8001
- 代理名称: elecV2P

*代理地址和端口根据实际情况进行调整*

然后把需要抓包的域名设置分流(FILTER)代理到这个服务器。

也可以使用订阅分流，在 webUI->CFILTER 界面，添加需要分流的域名，保存。（这一步可以在添加 MITM host 的时候同时完成，域名是同一个。）

![](https://elecv2.github.io/src/cfilter.png)

在底下有一个订阅地址，就是分流订阅链接。右键复制链接到相关代理软件，添加一条分流规则，代理服务器选择上面设置的 **elecV2P**。

总之，把需要抓包的域名代理到 ANYPROXY 端口即可。

## 查看/修改网络请求

查看请求，基本都可以 **8002** 端口页面完成，没有太多要讲的，修改网络请求才是重点。如前面所说，所有的抓包工具本质就是一个代理，这个代理能看到经过它的所有网络请求，那么它能不能修改这些请求呢？答案是肯定的，能。哪怕你不信任证书，虽然它看不到所有的信息，但基础的主机域名还是可以知道的，知道这个域名后，它就可以选择是否继续这个请求，还是直接放弃（阻断连接），甚至转发这个请求到另一个域名上去。比如，你本来访问 https://www.google.com/ , 代理可以直接给你转到 https://www.baidu.com/ 。如果你信任了这个代理的根证书，或者说这个代理的根证书本来就权威机构发布的，那么通过它所有的网络请求信息，它都能看得一清二楚，并且可以进行任意的增减和构造。**再次强调安装未知来源的根证书有风险，也不要随意使用一些来历不明的代理。**

回到主题，如何通过 **elecV2P** 修改网络请求。前面我们已经有了 ANYPROXY 代理，也已经信任了证书，理论上我们已经可以对所有通过这个代理的网络请求进行任意的修改。那么到底如果修改呢？我们需要规则 RULES 来实现。（不同网络工具使用的规则不一样，本文仅以 **elecV2P** 为例。）比如把 baidu.com 重定向到 google，就可以在 RULES 里添加一条 301 重定向规则。

![](https://elecv2.github.io/src/rulesbg.png)

因为要修改的是 baidu.com，所以记得先按本文前面的教程在 MITM host 添加这个域名，并把 baidu.com 代理到 ANYPROXY 端口。（之后添加规则时同样操作，不再重复说明。）

如果想修改网络请求的 header 或 body 呢？**elecV2P** 有一个创新的 **$HOLD** 功能，可以把网络请求先胁持到 webUI 页面，然后进行任意的手动修改后再发送出去。

![添加一条 $HOLD 规则](https://elecv2.github.io/src/ruleshold.png)

*$HOLD 后面的修改内容表示胁持 hold 时间，单位：秒。 0 表示持续胁持，直到手动返回*

![$HOLD 界面](https://elecv2.github.io/src/holdrequest.png)

在这个界面不仅可以查看/复制出任何你需要的信息，还可以对各项参数进行修改，然后返回。

那如果你不想手动修改，而想通过 JS 脚本来自动更改呢？也是可以的，添加一条修改方式为 JS 的规则即可。

![](https://elecv2.github.io/src/rulesjs.png)

对应的修改内容为 JS 的地址，可以是本地地址（在 JSMANAGE 中可查看的 JS 文件），也可以是一个远程的 JS 文件链接（确保这个链接是当前服务器可访问的，否则在 SETTING->网络请求相关设置 中添加访问代理）。至于这个 JS 如何编写，是另一部分内容，本文不作展开。 总之，代理会把获取到的网络请求信息传递给这个 JS，然后 JS 根据接收到的信息，选择保留存储任何需要的部分，或者进行修改后再返回给代理服务器。

## COOKIE 获取

对于一个抓包工具/代理软件来说，能获取的，或者说需要获取的远远不止 Header 里的 Cookie 这项数据，这里用 COOKIE 来统指需要获取的关键数据，可能包括且不限于网络请求 url/headers/body 中的任何参数。

手动复制就不多说了，按前面的查看网络请求，能看到的信息基本就都能复制出来。重点说一下如何使用脚本获取。

首先，脚本哪里来？当然是靠脚本作者无私的分享，然后善用 Github 的搜索功能。当然，有能力也可以自己写，关于 elecV2P 的脚本写法，参考: [说明文档 04-JS.md](https://github.com/elecV2/elecV2P-dei/tree/master/docs/04-JS.md)。

下面还是以 NobyDa 的 [JD_DailyBonus.js](https://raw.githubusercontent.com/NobyDa/Script/master/JD-DailyBonus/JD_DailyBonus.js) 这个脚本为例，讲一下如何获取京东的 cookie。

首先，打开脚本，看一下脚本作者的注释说明。一般作者都会写清楚脚本应该如何使用，找到类似如下图所示的部分。

![](https://elecv2.github.io/src/rewritemitm.png)

然后在 elecV2P webUI 中对照填写，首先是 MITM host: **api.m.jd.com**，直接在 webUI->MITM 添加保存。
（如果在代理软件中使用了 CFILTER 订阅，这里可以同时填写一下 CFILTER 列表，方便把 **api.m.jd.com** 这个域名代理到 ANYPROXY 端口。）
然后是 **rewrite**，这里只需要两部分，一个前面的匹配 URL: **https:\\/\\/api\\.m\\.jd\\.com\\/client\\.action.\*functionId=signBean**，一个是后面的 JS: **https://raw.githubusercontent.com/NobyDa/Script/master/JD-DailyBonus/JD_DailyBonus.js**, 把这两个填写到 webUI->REWRITE 的相应部分：

![](https://elecv2.github.io/src/rewrite.png)

- *rewrite 规则是 rules url 匹配，在数据返回前通过 JS 修改的简化规则，也可以通过 RULES 添加*
- *如果原 cookie 的获取方式是 script-**request** 类规则，则必须通过 RULES 添加，并选择网络请求前*

另外，如果 JS 是一个远程链接，要确保这个远程链接是 elecV2P 服务器可访问的。可以在 webUI->JSMANAGE 中远程推送或者模拟网络请求部分测试一下。像 **https://raw.githubusercontent.com** 这个网址就需要“科学上网”才能访问，所以可能需要在 webUI->SETTING->网络相关设置中添加代理。如果不想设置代理，可以选择先把 JS 文件上传到服务器（在 JSMANAGE 界面选择本地上传，或者直接复制所有 JS 代码到编辑框，设置文件名，然后点击保存至服务器。）当服务器中存在 JS 文件后，就可以使用本地 JS 的名称来代替原来的远程链接。比如已经上传了 **JD_DailyBonus.js** 这个文件，那么直接把 **https://raw.githubusercontent.com/NobyDa/Script/master/JD-DailyBonus/JD_DailyBonus.js** 改为 **JD_DailyBonus.js** 即可，表示使用本地 JS。

假如准备工作都已完成:
- **elecV2P 正常运行**
- **安装信任了根证书**
- **添加了 MITM host**
- **api.m.jd.com 正常代理到了 ANYPROXY 端口**
- **正确设置了 REWRITE 或 RULES 规则**
- **JS 文件可下载或已在本地**

接着就按照脚本里的提示，打开浏览器登录 https://bean.m.jd.com/bean/signIndex.action 。应该会马上收到 cookie 获取成功的提示，类似于：

![](https://elecv2.github.io/src/jdcookie.png)

如果出现错误，对照 elecV2P 的运行日志，以及上面的所有步骤，重新检查一遍。
如果设置了其他通知方式，也会收到通知提醒。关于通知的设置，参考：[说明文档 07-feed&notify](https://github.com/elecV2/elecV2P-dei/tree/master/docs/07-feed&notify.md) 。

## COOKIE/常量管理

获取的 COOKIE 在哪里？又如何导出呢？打开 webUI->JSMANAGE 界面，可以看到 store/cookie 管理 模块，所有存储的 COOKIE/常量 都可以在这里看到，并且可以进行任意的复制和修改。

![](https://elecv2.github.io/src/cookie.png)

如果想要添加一个新的 cookie ，直接修改 KEY 值和对应的 VALUE ，然后点击保存即可。至于这些常量该如何使用，一般不需要知道，直接运行相关脚本，脚本会自动读取。如果是想自己写脚本进行读取的话，参考：[说明文档：04-JS](https://github.com/elecV2/elecV2P-dei/tree/master/docs/04-JS.md) **$store** 部分。 

## 后话

~~感觉已经写得很啰嗦了，后话就省了。~~