---
title: WEB安全
date: 2017-08-02 09:57:34
updated: 2017-08-02 09:57:34
tags:
  - WEB安全
categories:
  - 基础
toc: true
---
本文介绍web安全。

工作中有接触过一部分，系统整理一下。后续需要补上攻防代码。

<!-- more -->
# 知识点

## Crypto

Node.js 的 `crypto` 模块封装了诸多的加密功能, 包括 OpenSSL 的哈希、HMAC、加密、解密、签名和验证函数等.

在客户端加密, 是增加传输的过程中被第三方嗅探到密码后破解的成本. 对于游戏, 在客户端加密是防止外挂/破解等. 在服务端加密 (如 md5) 是避免管理数据库的 DBA 或者攻击者攻击数据库之后直接拿到明文密码, 从而提高安全性.

## TLS/SSL

 SSL (Secure Socket Layer)的主要用途是:

1. 认证用户和服务器, 确保数据发送到正确的客户机和服务器;
2. 加密数据以防止数据中途被窃取;
3. 维护数据的完整性, 确保数据在传输过程中不被改变.

存在三个特性:

- 机密性：SSL协议使用密钥加密通信数据
- 可靠性：服务器和客户都会被认证, 客户的认证是可选的
- 完整性：SSL协议会对传送的数据进行完整性检查

 IETF 把 SSL 标准化/强化. 标准化之后的名称改为传输层安全协议 (Transport Layer Security, TLS). 很多相关的文章都把这两者并列称呼 (TLS/SSL), 因为这两者可以视作同一个东西的不同阶段.

## HTTPS

公钥基础设施 (Public Key Infrastructure, PKI) 是一种遵循标准的, 利用公钥加密技术为电子商务的开展提供一套安全基础平台的技术和规范. 其基础建置包含认证中心 (Certification Authority, CA) 、注册中心 (Register Authority, RA) 、目录服务 (Directory Service, DS) 服务器.

由 RA 统筹、审核用户的证书申请, 将证书申请送至 CA 处理后发出证书, 并将证书公告至 DS 中. 在使用证书的过程中, 除了对证书的信任关系与证书本身的正确性做检查外, 并透过产生和发布证书废止列表 (Certificate Revocation List, CRL) 对证书的状态做确认检查, 了解证书是否因某种原因而遭废弃. 证书就像是个人的身分证, 其内容包括证书序号、用户名称、公开金钥 (Public Key) 、证书有效期限等.

在 TLS/SLL 中你可以使用 OpenSSL 来生成 TLS/SSL 传输时用来认证的 public/private key. 不过这个 public/private key 是自己生成的, 而通过 PKI 基础设施可以获得权威的第三方证书 (key) 从而加密 HTTP 传输安全. 目前博客圈子里比较流行的是 [Let's Encrypt 签发免费的 HTTPS 证书](https://imququ.com/post/letsencrypt-certificate.html).

需要注意的是, 如果 PKI 受到攻击, 那么 HTTPS 也一样不安全.

此外有的 CA 机构以邮件方式认证, 那么当某个网站的邮件服务收到攻击/渗透, 那么攻击者也可能以此从 CA 机构获取权威的正确的证书.

### CA工作原理

现在假设客户A向银行B传送数字信息，为了保证信息传送的真实性、完整性和不可否认性，需要对要传送的信息进行数字加密和数字签名，其传送过程如下： 

- 客户A准备好要传送的数字信息（明文）。（**准备明文**） 


- 客户A对数字信息进行哈希（hash）运算，得到一个信息摘要。（**准备摘要**） 


- 客户A用自己的私钥（SK）对信息摘要进行加密得到客户A的数字签名，并将其附在数字信息上。（**用私钥对数字信息进行数字签名**）　 
- 客户A随机产生一个加密密钥（DES密钥），并用此密钥对要发送的信息进行加密，形成密文。（**生成密文**） 
- 客户A用双方共有的公钥（PK）对刚才随机产生的加密密钥进行加密，将加密后的DES密钥连同密文一起传送给乙。（**用公钥对DES密钥进行加密**）　　 
- 银行B收到客户A传送过来的密文和加过密的DES密钥，先用自己的私钥（SK）对加密的DES密钥进行解密，得到DES密钥。（**用私钥对DES密钥解密**）　　 
- 银行B然后用DES密钥对收到的密文进行解密，得到明文的数字信息，然后将DES密钥抛弃（即DES密钥作废）。（**解密文**）　　 
- 银行B用双方共有的公钥（PK）对客户A的数字签名进行解密，得到信息摘要。银行B用相同的hash算法对收到的明文再进行一次hash运算，得到一个新的信息摘要。　　 （**用公钥解密数字签名**） 
- 银行B将收到的信息摘要和新产生的信息摘要进行比较，如果一致，说明收到的信息没有被修改过。（**对比信息摘要和信息**） 

## XSS

### 是什么？

跨站脚本 (Cross-Site Scripting, XSS) 是一种代码注入方式， 其注入方式很简单包括但不限于 JavaScript / VBScript / CSS / Flash 等.

当其他用户浏览到这些网页时, 就会执行这些恶意脚本, 对用户进行 Cookie 窃取/会话劫持/钓鱼欺骗等各种攻击. 其原理, 如使用 js 脚本收集当前用户环境的信息 (Cookie 等), 然后通过 img.src, Ajax, onclick/onload/onerror 事件等方式将用户数据传递到攻击者的服务器上. 

### 类型

- **反射型XSS**:非持久化，欺骗用户去点击链接,攻击代码包含在url中,被用户点击之后执行攻击代码.
- **存储型XSS**:持久型,攻击提交恶意代码到服务器，服务器存储该段代码,这样当其他用户请求后，服务器返回并发给用户，用户浏览此类页面时就可能受到攻击。例如:恶意用户的HTML或JS输入服务器->进入数据库->服务器响应时查询数据库->用户浏览器。

### 防范与过滤

- 输入编码过滤:对于每一个输入，在客户端和服务器端验证是否合法字符，长度是否合法，格式是否正确,对字符进行转义.非法字符过滤.
- 输出编码过滤:对所有要动态输出到页面的内容，**进行相关的编码和转义**.主要有HTML字符过滤和转义, JS脚本转义过滤. url转义过滤.
- 设置http-only,避免攻击脚本读取cookie.


### 攻防流程

#### 升级攻击

##### append的利用

直接给innerHTML赋值一段js，是无法被执行的。但是，jquery的append可以做到，究其原因，就是因为jquery会在将append元素变为fragment的时候，找到其中的script标签，再使用eval执行一遍。jquery的append使用的方式也是innerHTML。而innerHTML是会将unicode码转换为字符实体的。

**Case**

```php+HTML
<?php
    $username="\u003cscript\u003ealert('okok');";
?>
$('#username_info').append("<?php echo htmlentities($username);?>");
```

虽然进行了转义，注入的代码还是会再次被执行。

##### img标签的再次利用

img标签，在加载图片失败的时候，会调用该元素上的onerror事件。我们正可以利用这种方式来进行攻击。

**Case**

```php
<?php
    $imgsrc="\" onerror=\"javascript:alert('侯医生');\"";
?>
```

解决方法：接着进行转义

```javascript
<img src="<?php echo htmlentities($imgsrc);?>" />
```

##### 组合使用

innerHTML赋值的script标签，不会被执行，但是innerHTML赋值一个img标签是可以被识别的。我们把img标签的左右尖括号，使用unicode进行伪装，让转义方法认不出来，即使innerHTML也可以利用上了

**Case**

```php+HTML
<?php
    $username="\u003cimg src=\'\' onerror=javascript:alert(\'okok\');\u003e";
?>
<script>
  	document.getElementById('username_info').innerHTML = "<?php echo htmlentities($username);?>";
</script>
```

#### 升级防御

将输出的字符串中的`\`反斜杠进行转义(json转义)。这样，`\`就不会被当做unicode码的开头来被处理了。

**Case**

```javascript
document.getElementById('username_info').innerHTML = <?php echo json_encode(htmlentities($username));?>;
```

#### XSS再升级

有的时候，会有一些习惯，拿URL上的get参数去构建网页。好比说，直接拿url上的用户名去展示啦，拿url上的一些回跳地址之类的。但是url上的参数，我们是无法提前对其进行转义的。

**Case**

```html
<script>
  var param = /=(.+)$/.exec(location.search);
  var value = decodeURIComponent(param[1]);
  $('#username_info').append(value);
</script>
```

#### 防御再次升级

这种从url中获取的信息，最好由后端获取，在前端转义后再行输出

使用url中的参数的时候要小心，更不要拿URL中的参数去eval。

**Case**

```javascript
<script>
    var value = decodeURIComponent("<?php echo htmlentities($_GET['username']);?>");
    $('#username_info').append(value);
</script>
```

#### 保护好你的cookie

使用cookie的HttpOnly属性，加上了这个属性的cookie字段，js是无法进行读写的。

### CPS 策略

计算脚本的 hashes 值，设置 CSP 头

策略指令可以参见 [CSP Policy Directives](https://developer.mozilla.org/en-US/docs/Web/Security/CSP/CSP_policy_directives)以及[阮一峰的博文](http://www.ruanyifeng.com/blog/2016/09/csp.html), [屈大神的博文](https://imququ.com/post/content-security-policy-reference.html)

## CSRF

### 什么是CSRF攻击？

跨站请求伪造 (Cross-Site Request Forgery) 是一种伪造跨站请求的攻击方式.

例如利用你在 A 站 (攻击目标) 的 cookie / 权限等, 在 B 站 (恶意/钓鱼网站) 拼装 A 站的请求.

### 如何攻击？

#### 要合理使用post与get

通常我们会为了省事儿，把一些应当提交的数据，做成get请求。殊不知，这不仅仅是违反了http的标准而已，也同样会被黑客所利用。

**Case**

你开发的网站中，有一个购买商品的操作，而商品ID图个省事儿，就使用了url中的get参数。

```php+HTML
<?php
// 从cookie中获取用户名，看似稳妥
$username = $_COOKIE['username'];
$productId = $_GET['pid'];
?>

<?php
echo $username . '买入商品：' . $productId;
?>

//黑客的网站
<img src="http://localhost:8082/lab/xsrflab/submit.php?pid=1" />
```

所以，我们日常的开发，还是要遵循提交业务，严格按照post请求去做的。更不要使用jsonp去做提交型的接口，这样非常的危险。

#### csrf攻击升级

如果你使用了post请求来处理关键业务的，还是有办法可以破解的。

**Case**

```php+HTML
<?php
$username = $_COOKIE['username'];
// 换为post了，可以规避黑客直接的提交
$productId = $_POST['pid'];
?>

<?php
echo $username . '买入商品：' . $productId;
?>

//黑客的网站
<button id="clickme">点我看相册</button>
<script>
  $('#clickme').on('click', function () {
    // 用户再不知情的情况下，提交了表单，服务器这边也会以为是用户提交过来的。
    $('#myform').submit();
  });
</script>
<form id="myform" style="display:none;" target="myformer" method="post" action="http://myhost:8082/lab/xsrflab/submit.php">
  <input type="hidden" name="pid" value="1">
</form>
<iframe name="myformer" style="display:none;"></iframe>
```

**这种情况如何防御？**

最简单的办法就是加**验证码**，这样除了用户，黑客的网站是获取不到用户本次session的验证码的。但是这样也会降低用户的提交体验，特别是有些经常性的操作

另一种方式，就是在用访问的页面中，都种下验证用的**token**，用户所有的提交都必须带上本次页面中生成的token，这种方式的本质和使用验证码没什么两样，但是这种方式，整个页面每一次的session，使用同一个token就行，很多post操作，开发者就可以自动带上当前页面的token。如果token校验不通过，则证明此次提交并非从本站发送来，则终止提交过程。如果token确实为本网站生成的话，则可以通过。

### 预防

1. 在 HTTP 头中自定义属性并验证
2. 检查 CSRF token.
3. cookie中加入hash随机数.
4. 通过检查来过滤简单的 CSRF 攻击, 主要检查一下两个 header:

- Origin Header

- Referer Header

## 中间人攻击

中间人 (Man-in-the-middle attack, MITM) 是指攻击者与通讯的两端分别创建独立的联系, 并交换其所收到的数据, 使通讯的两端认为他们正在通过一个私密的连接与对方直接对话, 但事实上整个会话都被攻击者完全控制. 在中间人攻击中, 攻击者可以拦截通讯双方的通话并插入新的内容.

目前比较常见的是在公共场所放置精心准备的免费 wifi, 劫持/监控通过该 wifi 的流量. 或者攻击路由器, 连上你家 wifi 攻破你家 wifi 之后在上面劫持流量等.

对于通信过程中的 MITM, 常见的方案是通过 PKI / TLS 预防, 及时是通过存在第三方中间人的 wifi 你通过 HTTPS 访问的页面依旧是安全的. 而 HTTP 协议是明文传输, 则没有任何防护可言.

如果你的网站还没有进行https加密的化，则在表单提交部分，最好进行非对称加密--即客户端加密，只有服务端能解开。这样中间的劫持者便无法获取加密内容的真实信息了。

不常见的还有强力的互相认证, 你确认他之后, 他也确认你一下; 延迟测试, 统计传输时间, 如果通讯延迟过高则认为可能存在第三方中间人; 等等.

## SQL/NoSQL 注入

注入攻击是指当所执行的一些操作中有部分由用户传入时, 用户可以将其恶意逻辑注入到操作中. 当你使用 eval, new Function 等方式执行的字符串中有用户输入的部分时, 就可能被注入攻击. 上文中的 XSS 就属于一种注入攻击. 

### SQL

Sql 注入是网站常见的一种注入攻击方式. 其原因主要是由于登录时需要验证用户名/密码

**Case**

例如上传密码为 `'; DROP TABLE users; --` 使得最终执行的内容为:

```sql
SELECT * FROM users WHERE usernae = 'myName' AND password = ''; DROP TABLE users; --';
```

其能实现的功能, 包括但不限于删除数据 (经济损失), 篡改数据 (密码等), 窃取数据 (网站管理权限, 用户数据) 等. 

防治手段常见于:

- 给表名/字段名加前缀 (避免被猜到)
- 报错隐藏表信息 (避免被看到, 12306 早起就出现过的问题)
- 过滤可以拼接 SQL 的关键字符
- 对用户输入进行转义
- 验证用户输入的类型 (避免 limit, order by 等注入)
- 等...

### NoSQL

**Case**

```javascript
let {user, pass, age} = ctx.query;

db.collection.find({
  user, pass,
  $where: `this.age >= ${age}`
})
```

## DDOS

> DDoS即Distributed Denial of Service，分布式拒绝服务。也就是攻击者借助或者利用服务器技术，将多个计算机（肉鸡或僵尸机）联合起来作为攻击平台，对一个或者多个目标服务器，同一时间发送大量垃圾信息，或利用某种干扰信息的方式，导致目标服务器无法及时响应正常用户正常请求，或者直接导致目标服务器宕机，从而无法为正常用户提供服务的一种攻击行为。

攻击方式:

- 端口扫描攻击
- ping洪水（flooding）攻击
- SYN洪水（flooding）攻击
- FTP跳转攻击

防范手段:

- 保证服务器系统的安全,确保服务器软件没有任何漏洞，防止攻击者入侵。
- 确保服务器采用最新系统，并打上安全补丁。
- 在服务器上删除未使用的服务，关闭未使用的端口。
- 对于服务器上运行的网站，确保其打了最新的补丁，没有安全漏洞。
- 隐藏服务器的真实IP地址

## 通用防范措施

1. 开发时要提防用户产生的内容，要对用户输入的信息进行层层检测
2. 要注意对用户的输出内容进行过滤(进行转义等)
3. 重要的内容记得要加密传输(无论是利用https也好，自己加密也好)
4. get请求与post请求，要严格遵守规范，不要混用，不要将一些危险的提交使用jsonp完成。
5. 对于URL上携带的信息，要谨慎使用。



# 问题



# 参考文章

- [前端需要知道的web安全知识](https://segmentfault.com/a/1190000010913697)
- [CA认证简单介绍和工作流程](http://wlh269.iteye.com/blog/733398)
- [[聊一聊系列\]聊一聊WEB前端安全那些事儿](https://segmentfault.com/a/1190000006672214)