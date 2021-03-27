---
title: "Https协议与加密算法的运用"
date: 2021-02-27T18:41:40+08:00
draft: true
---

## 一、什么是Https

HTTPS （全称：Hyper Text Transfer Protocol over SecureSocket Layer），是以安全为目标的 HTTP 通道，在HTTP的基础上通过传输加密和身份认证保证了传输过程的安全性。

### 传统http传输的缺点

1. 通信使用明文，传输内容可能被窃听。
2. 不验证后通信方的身份，容易伪装进行请求。
3. 无法保证报文的完整性，可能遭遇篡改。

### 中间人攻击

当A、B通信时，C不主动去为其“转发”，只是把他们的传输的数据备份，以获取用户网络的活动，包括账户、密码等敏感信息，这种被动攻击也是非常难被发现的。


## 二、Https用到了哪些加密算法？

### 使用对称加密:

如果通信双方都各自持有同一个密钥，且没有别人知道，这两方的通信安全当然是可以被保证的（除非密钥被破解）。然而最大的问题就是这个密钥怎么让传输的双方知晓，同时不被别人知道。

如果浏览器内部预存了每个网站的密钥，理论上用对称加密是可行的，但是并不现实。

### 使用非对称加密:

某网站拥有用于非对称加密的公钥A、私钥A’；浏览器拥有用于非对称加密的公钥B、私钥B’。

浏览器像网站服务器请求，服务器把公钥A明文给传输浏览器。

浏览器把公钥B明文传输给服务器。

之后浏览器向服务器传输的所有东西都用公钥A加密，服务器收到后用私钥A’解密。由于只有服务器拥有这个私钥A’可以解密，所以能保证这条数据的安全。

服务器向浏览器传输的所有东西都用公钥B加密，浏览器收到后用私钥B’解密。同上也可以保证这条数据的安全。

## 三、非对称加密与对称加密的性能分析

### 主要问题

1. RSA算法加密流程。

2. AES算法加密流程。

3. 四则运算与逻辑运算的性能差异。


### RSA：

1. 随机选择两个**不相等**的质数p和11和13。（实际应用中，这两个质数越大，就越难破解。）

2. 计算p和q的乘积n。n = 11×13 = 143

      n的长度就是密钥长度。143写成二进制是10001111，一共有8位，所以这个密钥就是8位。

     实际应用中，RSA密钥一般是1024位，重要场合则为2048位。

3. 计算n的欧拉函数φ(n)。称作L，根据公式φ(n) = (p-1)(q-1) 算出φ(143)等于10*12，即120.

4. 随机选择一个整数e，也就是公钥当中用来加密的那个数字条件是1< e < φ(n)，且e与φ(n) 互质。

     在1到3120之间，随机选择了17。（实际应用中，常常选择65537。）

5. 计算e对于φ(n)的模反元素d。也就是密钥当中用来解密的那个数字

     e*d ≡ 1 (mod φ(n))，这个公式简单的说就是 e*d除以φ(N)得到的余数为1，

     即e*d - 1 = kφ(n)，已知 e = 7 φ(n) = 120，7d + 120*k = 1。

    ```cpp
     辗转相除法计算d：

     120 = 7 * 17 + 1；
     17 = 17 * 1；
     1 = 120 * 1 + 7 * (-17)；
    最终得出，d = -17 k = 1；
    ```

    虽然我们得到了 d=-17但 在rsa中 d必须是一个正整数,所以得到 e对φ(n)的模逆元 为 120 + （-17）也就是103。

    上面的例子 公钥 (143,7) 私钥(143, 103)。

6. 将n和e封装成公钥，n和d封装成私钥。

     在例子中，n=3233，e=17，d=2753，所以公钥就是 (3233,17)，私钥就是（3233, 2753）。

7. 同余方程

```cpp
求关于x的同余方程ax≡1(mod b) 的最小正整数解
ax mod b = 1，它的实质是 ax + by = 1。
anxn + bnyn = gcd(an,bn)。
gcd:最大公因数，辗转相除法。
int gcd(int a,int b) 
{ 
if(b==0) return a; 
else return gcd(b,a%b); 
}
```

假设对m加密得密文c，则c  = me  (mod n)， m = cd(mod n)。

### 关于如何生成大素数

目前的公开密钥 算法大部分基于大整数分解、有限域上的离散对数问题和椭 圆曲线上的离散对数问题，这些数学难题的构建大部分都需 要生成一种超大的素数，尤其在经典的RSA算法中，生成的素数的质量对系统的安全性有很大的影响。

费马小定理：假如p是质数，且gcd(a,p)=1，那么 a^(p-1)≡1（mod p）。即：假如a是整数，p是质数，且a,p互质(即两者只有一个公约数1)，那么a的(p-1)次方除以p的余数恒等于1。基于此可以快速判断一个大整数是否为素数。


### AES

字节代换、行移位、列混合、轮密钥加。

AES的字节代换其实就是一个简单的查表操作。AES定义了一个S盒和一个逆S盒，类似于凯撒密码的加解密。


行移位是一个简单的左循环移位操作。当密钥长度为128比特时，状态矩阵的第0行左移0字节，第1行左移1字节，第2行左移2字节，第3行左移3字节。

列混合变换是通过矩阵相乘来实现的，经行移位后的状态矩阵与固定的矩阵相乘，得到混淆后的状态矩阵。

轮密钥加是将128位轮密钥Ki同状态矩阵中的数据进行逐位异或操作。

# SHA

SHA-2，名称来自于安全散列算法2（英语：Secure Hash Algorithm 2）的缩写，一种密码散列函数算法标准，由美国国家安全局研发，由美国国家标准与技术研究院（NIST）在2001年发布。属于SHA算法之一，是SHA-1的后继者。

其下又可再分为六个不同的算法标准，包括了：SHA-224、SHA-256、SHA-384、SHA-512、SHA-512/224、SHA-512/256。

## 加密算法结合

Https通过用RSA加密AES的密钥传输，解密后再通过对称加密进行消息传输。

## 数字证书

网站在使用HTTPS前，需要向“CA机构”申请颁发一份数字证书，数字证书里有证书持有者、证书持有者的公钥等信息，服务器把证书传输给浏览器，浏览器从证书里取公钥就行了，证书就如身份证一样，可以证明“该公钥对应该网站”。

但是证书需要防篡改和防伪。

## 数字签名

1.CA拥有非对称加密的私钥和公钥。
2.CA对证书明文信息进行hash。
3.对hash后的值用私钥加密，得到数字签名。
明文和数字签名共同组成了数字证书，这样一份数字证书就可以颁发给网站了。

浏览器验证过程：

拿到证书，得到明文T，数字签名S。

用CA机构的公钥对S解密（由于是浏览器信任的机构，所以浏览器保有它的公钥。详情见下文），得到S’。

用证书里说明的hash算法对明文T进行hash得到T’。

比较S’是否等于T’，等于则表明证书可信。

## 安全性分析

### 中间人有可能篡改该证书吗？
假设中间人篡改了证书的原文，由于他没有CA机构的私钥，所以无法得到此时加密后签名，

无法相应地篡改签名。浏览器收到该证书后会发现原文和签名解密后的值不一致，则说明证书已被篡改，证书不可信，从而终止向服务器传输信息，防止信息泄露给中间人。

### 中间人有可能把证书掉包吗？

假设有另一个网站B也拿到了CA机构认证的证书，它想搞垮网站A，想劫持网站A的信息。

于是它成为中间人拦截到了A传给浏览器的证书，然后替换成自己的证书，传给浏览器，之后浏览器就会错误地拿到B的证书里的公钥了，会导致上文提到的漏洞。

其实这并不会发生，因为证书里包含了网站A的信息，包括域名，浏览器把证书里的域名与自己请求的域名比对一下就知道有没有被掉包了。
 
### 怎么证明CA机构的公钥是可信的？

操作系统、浏览器本身会预装一些它们信任的根证书，如果其中有该CA机构的根证书，那就可以拿到它对应的可信公钥了。

实际上证书之间的认证也可以不止一层，可以A信任B，B信任C，以此类推，我们把它叫做信任链或数字证书链，也就是一连串的数字证书，由根证书为起点，透过层层信任，使终端实体证书的持有者可以获得转授的信任，以证明身份。

微软的操作系统会预装一些可信的根证书，为这一举措背书的实际上是国家和政府。

## 哈希算法实现

```c

// DJB Hash 
unsigned int DJBHash(char *str)
{
    unsigned int hash = 5381;
 
    while (*str)
    {
        hash += (hash << 5) + (*str++);
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// AP Hash 
unsigned int APHash(char *str)
{
    unsigned int hash = 0;
    int i;
 
    for (i=0; *str; i++)
    {
        if ((i & 1) == 0)
        {
            hash ^= ((hash << 7) ^ (*str++) ^ (hash >> 3));
        }
        else
        {
            hash ^= (~((hash << 11) ^ (*str++) ^ (hash >> 5)));
        }
    }
 
    return (hash & 0x7FFFFFFF);
}

```