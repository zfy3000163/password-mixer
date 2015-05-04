

# 简介 #

Antiy Password Mixer（APM）是安天实验室针对当前拖库等安全威胁发布的一套安全算法应用范例，适用于中小规模的BBS、SNS、留言板、网页游戏等常见的Web应用，以及其它可能通过网页进行用户注册、认证与密码管理的网站应用场景。APM面向Web应用的规划、开发和维护者，可以解决Web后台大多数身份认证和关键信息加密保存的需求。

APM基于GNU GPL v2协议开放源码，目前已经发布了Python、PHP和Ruby版。我们还为APM提供后续的更新和对应的支持服务。

APM的特色包括：

  * 简单，低代价的集成方案，能够容易的集成到现有的企业环境中

  * 可有效对抗 统计攻击、普通暴力破解、GPU加速破解、基于云计算的破解

  * 具备加密单向不可还原数据和支持解密明文信息两种使用模式

  * 正确的经过严格代码审查的编程实现

在当前0day横行，几乎没有网站和应用可以绝对保证自己数据库系统的安全的情况下，使用APM能使数据库遭到窃取后的损失降低到最小程度。即便数据库被公开，也能将企业的社会压力和法律责任降低到最小限度。

但这一切是如何做到的呢？APM没有什么技术含量，也没有“自有自主知识产权”的国产算法，Python版本的主程序还不足300行。我们站在安全工程师的实践经验角度，利用现有的流行开源包，完成了对RSA、HASH+SALT算法的一个外围封装，给出了网站开发者应用这些算法的相对合理的范例。当然，这些开源包中的算法实现已经经过了我们的代码级安全分析。

我们希望用这种办法，让目前还在明文保存密码的网站和使用相关算法策略并不合理（比如那些联合使用HASH或者加入单一SALT的情况）的网站，能够了解如何科学使用这些数学方法，降低安全应用的门槛。我们也希望打消那些中小网站试图自己研究一个算法的努力。放弃这些已经被理论和实践检验过的算法实现，而徒耗心力和人月，是不值得的。

当然，你也许会鄙视我们的代码，而希望自己去实现更精干和精彩的。我们只希望让更多的开发者了解安全并不神秘，与其他领域一样有大量的资源可以借鉴和应用，只要我们合理地去使用它们，就可以获得更多安全的保障。

# 背景 #

从去年的索尼泄露用户信息的风波，到今年的CSDN、天涯密码泄露事件，无不引发了用户的口令恐慌。由于大量用户一号通用，密码泄露不仅威胁到其在具体站点的安全，也关联影响到了其网银、网游、IM等关联环节的安全。这导致被“脱裤”攻击的网站的声誉受到极大影响，有些网站更因此遭到DDoS 攻击等情况。

目前相关泄露事件的影响范围包括：

| **受害厂商/站点名称** | **泄露信息涉及用户账户数** |
|:------------------------------|:--------------------------------------|
| 索尼 | 约1.016亿 |
| 世嘉 | 约130万 |
| 花旗银行 | 约20万 |
| CSDN | 约600万 |
| 多玩 | 约800万 |
| 天涯 | 约4000万 |

从事件情况来看，这些站点有的采用明文或者曾经采用明文存放密码，也有的加密算法使用不当，导致加密形同虚设。

# 分析 #

当前，攻守方面已经呈现极大地不平衡性，即使采用传统的标准HASH算法加密的站点，一旦数据库失窃，其实已经等于90%以上的密码泄露。为什么会如此？

让我们来看看，**攻击者拥有哪些资源**：

  * 已经泄露的，数以亿计用户的明文密码。
  * 体积以T计的彩虹表，已经覆盖14位以下数字字母组合的密码的MD5。
  * 成本急剧降低的GPU加速和分布式计算资源
  * 可以廉价获取的合法超算资源、云计算资源
  * 数量以十万计的僵尸网络

尽管有这些前车之鉴，**为什么多数网站都无法自己解决好账号密码相关的加密问题呢？**

  * 企业很难聘到合格的懂安全的程序员。
  * 网络流传的解决方案 , 大部分是网友自己开发,缺少专业的设计和评估。
  * 缺少专业的安全人才, 计算机科班的成才率很低。
  * 普通依靠野蛮生长成才的技术牛人,因为缺少基础的专业知识,常犯低级错误.很难正确的实现一个安全方案.不正确的实现提供了虚假的安全结果。虚假的安全危害更大。
  * 大部分程序员不可能一直跟踪最新的安全技术进展。

# APM解决了哪些问题 #

**一人一密、一站一密**：对于使用了APM的网站来说，即使两个用户密码相同，其加密的结果是不同的。对于使用了APM的不同网站来说，用户使用完全相同的信息和口令注册，加密结果也是不同的。

**高速低负载加密与验证**：一些网站不加密的原因是担心加密相关处理消耗服务服务器资源，影响用户体验，这是我们首先需要解决的。

**抗暴力生成**：由于APM\*一人一密、一站一密**的特点，采用暴力生成密文的方法，对每个站点都需要从头开始。而如果合理的存放相关参数，在只获取数据库的情况下，仅凭Antiy Password Mixer的开源代码，是无法构造有效生成器的。**

**抗查表攻击**：同上，由于APM使暴力生成的变得没有意义，因此也没有查表资源可以使用。

**抗统计分析**：对于那些使用自制HASH算法，使用其他HASH算法的站点来说，攻击者亦无须破解，只要根据HASH值进行排序，然后与高频密码表进行比对，就可以使大部分用户的密码处于不安全的状况。但经过APM处理过的口令是空间均匀分布的，完全没有统计价值。

**变成快羊**：我们相信您听过一句格言——**狼只咬最慢的羊**。APM并不能解决拖库的问题，但APM让拖库的价值变得很小。无论如何，全球采用APM的站点都是少数，因此，当你的库对攻击者失去意义，他自然就会去寻找更慢的羊。

# APM不能解决哪些问题及我们的建议 #
| **威胁** | **说明** | **建议** |
|:-----------|:-----------|:-----------|
| 用户端失密 | 用户自身因中了木马流失了密码，或把密码告诉其他人，录入时被偷窥等等。 | 加强用户安全知识普及。 |
| 钓鱼网站 | 用户登录了假冒的网站而被套走了密码。 | 网站加强搜索，以发现对自身钓鱼的网站。 |
| 一号通 | 通过其他网站流出的用户名和密码来尝试使用了APM的网站。 | 增加一个采用不同ID登陆失败的频度控制机制。提升探测难度。 |
| 通讯窃取 | 通讯过程的被工作者监听、ICP被ARP欺骗监听等等。 |使用HTTPS代替HTTP做登陆验证，但这样会增加服务器的负载。 |

# 我们计划解决的问题 #

## 联合构造碰撞 ##
在完整的数据库和加密参数被获取的情况下，目前的方案依然存在一个联合构造碰撞的问题，会影响到那些存在一号通情况的用户。我们已经设计了另一组方案解决这个问题，但其方案比较复杂，对于现有网站的移植有一定难度。

## 客户端有限强度加密 ##

我们希望能够提供一份客户端进行加密的JS脚本，以增强口令和凭证的传输安全性。但受JS能力所限，其加密强度是没有充分保证的。

# 如何使用APM #

## 有用的链接 ##
下面的链接可能会对您有帮助：

[APM安装方法](http://code.google.com/p/password-mixer/wiki/Installation)

[APM接口说明](http://code.google.com/p/password-mixer/wiki/API)

[APM使用常见问题](http://code.google.com/p/password-mixer/wiki/FAQ)

[APM用户数据迁移指南](http://code.google.com/p/password-mixer/wiki/DataMigration)

[源码和文档下载](http://code.google.com/p/password-mixer/downloads/list)

## 语言和平台 ##

如果您的应用基于Python、PHP或Ruby开发，您可以马上开始使用。如果您基于C/C++、Perl或者其他的，请与我们联系开发定制。

## 从头开始 ##

如果您尚在搭建你的WEB应用，那么一切很简单。您需要阅读我们的文档范例，进行下列操作。

先通过APM初始化程序，生成专属于您应用的加密参数，在您妥善保管了有关参数后，就可以继续开发了。

在您的应用中，实现通过把需要加密的存放信息，通过APM进行加密，再进行存储。当然，由于加密后的数据注定要比加密前更长，您可能会微调你数据库的字段设计。

您只需要在注册和登陆两项功能中，都嵌入APM就可以完成相应的工作了。

## 现有移植 ##
如果您已经具有了业已发布的应用，那么在下列情况下，您仍可以按照我们手册的指导，完成APM的嵌入过程：
  * 口令以明文存储
  * 口令以可还原的加密存储
  * 口令以某个单向HASH存储
  * 口令以某个单向HASH+简单SALT存储。

除了需要完成从头开始其中所说的工作外，您还需要根据我们文档的指导，实现现有数据的移植。

# 权益说明和建议 #

APM的源码基于GNU GPL v2协议开放，我们欢迎任何在该协议下对代码的改进和贡献。

您可能没有精力去审计大量的代码，因此建议你从我们的原厂发布站点获取APM，以避免其被恶意篡改。

注册用户在10万以下的互联网应用，都可以免费使用APM。但我们希望使用APM的站点能够放置我们的产品标志和链接。APM的标志为：

[![](http://www.antiy.net/opensource/apm/logo.png)](http://www.antiy.net/)

# 配套服务 #

我们可能会考虑为下列服务收取费用：

  * 开发定制其他语言版本：我们会免费发布Python、PHP和Ruby语言的版本，但对于其他的语言版本我们都会收费定制开发。

  * 提供使用APM的技术支持：远程和现场支持。

  * 提供登录安全的建议：登录安全机制的顾问服务。

  * 密码安全性云查询：一个基于口令MD5检测的应用，可以嵌入在你的登录过程中用以检测用户所设密码是否是已经泄露的。大家可以试用我们已经发布的免费客户端，下载地址是：  http://www.antiyfx.com/download/apc.zip

# 联系我们 #

**安天网站**：http://www.antiy.com

**联系我们**：apm@antiy.com