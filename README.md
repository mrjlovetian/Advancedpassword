# Advancedpassword & 高级密码
![](password.pdf)

# 对称加密算法
#### 加密解密使用相同秘钥
* 常见加密算法 DES（数据加密标准）、3DES（3个秘钥加密解密）、AES（“高级加密标准”）

#### 对称加密算法EBC&CBC

######  EBC (电子代码本、每个代码都是独立加密的)
* 终端测试命令
* 加密

```
$ openssl enc -des-ecb -K 616263 -nosalt -in msg1.txt -out msg1.bin  
```

* 解密


```
$ openssl enc -des-ecb -K 616263 -nosalt -in msg1.bin -out msg1.txt -d
```

* 查看加密之后的二进制文件


```
$ xxd msg1.bin
```

######  CBC （密码块链，使⽤用⼀一个密钥和⼀一个初始化向量量 (IV)对数据执⾏行行加 密转换， 可以有效地保证密⽂文的完整性）
* 终端测试命令
* 加密


```
$ openssl enc -des-cbc -K 616263 -iv 0000000000000000 -nosalt -in a.txt -out msg1.bin
```

* 解密


```
$ openssl enc -des-cbc -K 616263 -iv 0000000000000000 -nosalt -in msg1.bin -out msg4.txt -d
```

# 非对称加密算法

##### 特点描述
* 公钥解密/私钥解密
* 私钥加密/公钥解密
* 优点：安全
* 缺点：很慢

##### 原理介绍
* 找出两个“很⼤大”的质数:P & Q
* N = P *Q
* M = (P – 1) * (Q – 1)
* 找出整数E，E与M互质，即除了了1之外，没有其他 公约数（公钥， 公钥简单，通常可以公开）
* 找出整数D，使得E*D除以M余1，即 (E * D) % M =1 （私钥， 私钥复杂，都是私家珍藏）
* 加密 (X ^ E) % N = Y  （X是明⽂文 ，Y是密⽂文）
* 解密 (Y ^ D) % N = X （Y是密⽂文， X是明⽂文）

##### 公开秘钥的好处
* 简单 （就是⼀一些乘除⽽而已）
* 可靠 （不不论给出多少份明⽂文和对应的密⽂文，也⽆无法根据已
知的明⽂文和密⽂文的对应关系，破译出下⼀一份密⽂文。N和E可以公开给任何⼈人加密使⽤用，但是只有掌握 密钥D的⼈人才可以解密，即使加密者⾃自⼰己也⽆无法解密）
* 灵活 （可以产⽣生很多的公钥E和私钥D的组合给不不同的加 密者）

### OpenSSL⽣生成密钥演练
* ⽣生成强度是 512 的 RSA 私钥

```
$ openssl genrsa -out private.pem 512
```
* 以明⽂文输出私钥内容 

```
$ openssl rsa -in private.pem -text -out private.txt
```
* 校验私钥⽂文件 

```
$ openssl rsa -in private.pem -check 
```
* 从私钥中提取公钥

```
$ openssl rsa -in private.pem -out public.pem -outform PEM -pubout 
```
* 以明⽂文输出公钥内容
```
$ openssl rsa -in public.pem -out public.txt - pubin -pubout -text
```

* 使⽤用公钥加密⼩小⽂文件
```
$ openssl rsautl -encrypt -pubin -inkey public.pem -in msg.txt -out msg.bin
```
* 使⽤用私钥解密⼩小⽂文件

```
$ openssl rsautl -decrypt -inkey private.pem - in msg.bin -out a.txt
```

* 将私钥转换成 DER 格式

```
$ openssl rsa -in private.pem -out private.der -outform der
```

* 将公钥转换成 DER 格式

```
$ openssl rsa -in public.pem -out public.der - pubin -outform der
```

### RSA算法有两个作⽤用⼀一个是加密⼀一个是数字签名
###### 数字签名（发送⽅）
* 报⽂文" HASH 得到 "报⽂文摘要
* 报⽂文摘要" ⽤用公钥加密(数字签名) + 报⽂文发送给接收⽅方
###### 数字签名（接收⽅）
* "报⽂文" HASH 得到 "报⽂文摘要"
* "数字签名" ⽤用私钥解密 判断是否与 "报⽂文摘要" 相 同

### PKCS 介绍
###### Public-Key Cryptography Standards (PKCS)是 由美国 RSA 数据安全公司及其合作伙伴制定的⼀一 组公钥密码学标准，其中包括证书申请、证书更更 新、证书作废表发布、扩展证书内容以及数字签 名、数字信封的格式等⽅方⾯面的⼀一系列列相关协议
######  PKCS协议
* **PKCS#1** 定义 RSA 公开密钥算法加密和签名机制，主要⽤用 于组织 PKCS#7 中所描述的数字签名和数字信封 （专⻔门⽤用来加密/解密）
* **PKCS#3** 定义Diffie-Hellman密钥交换协议
* **PKCS#5** 描述⼀一种利利⽤用从⼝口令派⽣生出来的安全密钥加密字符 串串的⽅方法。使⽤用 MD2 或 MD5 从⼝口令中派⽣生密 钥，并采⽤用 DES-CBC 模式加密。主要⽤用于加密 从⼀一个计算机传送到另⼀一个计算机的私⼈人密钥，不不 能⽤用于加密消息
* **PKCS#6** 描述了了公钥证书的标准语法，主要描述X.509证书 的扩展格式
* **PKCS#7** 定义⼀一种通⽤用的消息语法，包括数字签名和加密等 ⽤用于增强的加密机制，PKCS#7 与 PEM 兼容，所 以不不需其他密码操作，就可以将加密的消息转换成 PEM消息 （根数字签名有关）
* **PKCS#8** 描述私有密钥信息格式，该信息包括公开密钥算法的私有密钥以及可选的属性集等（JAVA加密只⽀支持 PK 8）
* **PKCS#9** 定义⼀一些⽤用于 PKCS#6 证书扩展、PKCS#7 数字 签名和 PKCS#8 私钥加密信息的属性类型
* **PKCS#10** 描述证书请求语法
* **PKCS#11** 称为 Cyptoki，定义了了⼀一套独⽴立于技术的程序设计 接⼝口，⽤用于智能卡和 PCMCIA 卡之类的加密设备
* **PKCS#12** 描述个⼈人信息交换语法标准。描述了了将⽤用户公钥、 私钥、证书和其他相关信息打包的语法（苹果的开发者证书也俗称 P12）
* **PKCS#13** 椭圆曲线密码体制标准
* **PKCS#14** 伪随机数⽣生成标准
* **PKCS#15** 密码令牌信息格式标准

### 程序开发证书⽣生成
* ⽣生成私钥

```
$ openssl genrsa -out ca.key 1024
```
* 创建证书请求

```
openssl req -new -key ca.key -out rsacert.csr
```
* ⽣生成证书并签名，有效期10年年

```
$ openssl x509 -req -days 3650 -in rsacert.csr -signkey ca.key -out rsacert.crt
```
* 转换格式 （将 PEM 格式⽂文件转换成 DER 格式）

```
$ openssl x509 -outform der -in rsacert.crt -out rsacert.der
```
*  导入P12

```
$ openssl pkcs12 -export -out p.p12 -inkey ca.key -in rsacert.crt
```
### iOS 中的相关函数
* SecKeyEncrypt  //使⽤用公钥对数据加密
* SecKeyDecrypt  //使⽤用私钥对数据解密
* SecKeyRawVerify //使⽤用公钥对数字签名进⾏行行验证
* SecKeyRawVerify // 使⽤用私钥⽣生成数字签名
### 注意
* iOS中不不能使⽤用PEM格式的证书
* OpenSSL默认⽣生成的都是PEM格式的证书

