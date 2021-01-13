---

layout: default
title:  "Try Hack Me:Overpass 2 - Hacked"

---

# Try Hack Me:Overpass 2 - Hacked

参考:

https://beginninghacking.net/2020/08/16/try-hack-me-overpass-2-hacked-this-room-is-only-a-day-old-will-unlock-it-when-it-reaches-7-days-unless-you-have-already-cracked-it-the-password-to-unlock-this-is-the-root-flag-thmd/

https://linkavych.gitbook.io/info-sec-prep/try-hack-me-overpass-2

## task1 Forensics - Analyse the PCAP

根据提示，通过查看流量包即可获取相应数据

使用`John`破解流量数据包内`/etc/shadow`密码

将`/etc/shadow`内容保存为hash文件

使用`john`破解,得到数据

```
john --wordlists=/usr/share/wordlists/fasttrack.txt hash

```

## Task 2  Research - Analyse the code

下载持久性后门工具

查看`main.go`文件，发现使用的hash，与硬编码

使用`hashcat`破解流量数据包内hash值

命令格式

`hashcat -m <HASH_TYPE> -a 0 -o <outfile> <HASH:SALT> <wordlist>`


## Task 3  Attack - Get back in!

使用前面获取的密码进行ssh连接

进入后是James权限

