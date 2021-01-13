---

layout: default
title:  "tryhackme-Overpass"

---

# Overpass

参考：
https://blog.szymex.pw/thm/overpass.html
https://tryhackme.com/

# 信息收集

nmap -sC -sV 10.10.151.89


打开网页，查看源代码

下载源码，下载编译脚本

下载了overpassLinux，赋予执行权限


![OV__AVN__JX02_FC_XUA~_K.png](https://i.loli.net/2021/01/12/fvt9BWruOhi31X2.png)


查看~/目录下.overpass文件加密为ROT-47

使用目录扫描扫出后台

![FGNNJSN_70@AJW_ADE3T__5.png](https://i.loli.net/2021/01/12/yA1JhmZFLSgur7v.png)


在登陆后台页面查看源码，查看登陆相关源码

查看`login.js`源码，发现此处使用判断真假进行登陆验证，else处设置cookie为Incorrect credentials时可以跳转到/admin处


```
async function login() {
    const usernameBox = document.querySelector("#username");
    const passwordBox = document.querySelector("#password");
    const loginStatus = document.querySelector("#loginStatus");
    loginStatus.textContent = ""
    const creds = { username: usernameBox.value, password: passwordBox.value }
    const response = await postData("/api/login", creds)
    const statusOrCookie = await response.text()
    if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value=""
    } else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"
    }
}
```


尝试修改cookie


![DX6V__YXSF_V_N_A2_7_3GQ.png](https://i.loli.net/2021/01/12/5JQtTSrcnxf2ZgM.png)

此处发现名为James的用户创建了ssh的私钥

# 入口点

将rsa私钥保存为id_rsa

使用`ssh2john` 转为john能识别的文件,然后使用john破解出密码

https://github.com/DaMinGshidashi/john/blob/bionic/ssh2john.py

```
ssh2john id_rsa > id_rsa_hash
john  --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash

```


![9_9MTYZ4LCB5_35FQFUR9_R.png](https://i.loli.net/2021/01/13/rUQavcJOL8kwidD.png)


```
chmod 600 id_rsa

ssh -i id_rsa james@ip
```

![8Y_Z__1_TYR~X8T_YS_3753.png](https://i.loli.net/2021/01/13/YidxmO4PlRXLI58.png)

# 提权
查看todo.txt文件发现有自动更新

查看计划任务

`cat /etc/crontab`

![C7Y9FO`_B709CD2U_PULY4I.png](https://i.loli.net/2021/01/13/9b8NwohXqaSGUEY.png)

发现有使用root上传文件并使用bash

`/etc/hosts`任何人可读可写,overpass.thm处修改上我们的ip

![AB5WBZ3R61W_R__42W_@_AN.png](https://i.loli.net/2021/01/13/2EYx3FWCrInHoAi.png)

在本机参照目标靶机crontab文件修改对应目录

创建文件目录并加入反弹shell
![76TM8_G_@@J3TQQ5_6JU8LY.png](https://i.loli.net/2021/01/13/3tuj6kYBoiCgyeP.png)

开启80服务让靶机下载反弹shell文件

![GQAZ39HCH@~XT`__2QD3I1B.png](https://i.loli.net/2021/01/13/DvIsLfnqAyBxGS1.png)


开启监听

![B14_LK0QR7S_RYAHD@3_I78.png](https://i.loli.net/2021/01/13/RvwnNXWxe5i4gF2.png)

成功

