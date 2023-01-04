---
title:  2022安洵杯
categories: ctf
toc: true
date: 2022/11/27
tags: 比赛
---

回家路上打的比赛，高铁站坐牢捏

## Crypto

### Cry1

签到题，对四位数进行爆破即可

exp：

```she
from hashlib import *
b='abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'
a='qD0FP6G2kS8QcpG7'
for i in b:
    for j in b:
        for m in b:
            for n in b:
                c=str(i)+str(j)+str(m)+str(n)+a
                d=sha256(c.encode('utf-8')).hexdigest()
                if d=='86455b01fd7022f72ff93db1e9b2760cea520588db534cdeb3febb2d82a56e1e':
                    print(c)
```

不过拿flag还是比较看脸的qaq,试了无数次

![image-20221128220814803](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128220814803.png)

## Misc

### GumpKing

![image-20221128220939908](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128220939908.png)

基于虚幻引擎的小游戏

给的文件没什么太大帮助，一开始还以为是修改配置文件去拿flag

这里使用作弊工具（cheat engine）去修改分数值

游戏全屏不太好使用ce进行扫描,alt+enter切换为窗口模式

![image-20221128221326696](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128221326696.png)

思路：

游戏开始前即分数为零时对0进行扫描，分数发生变化时对当前分数进行再次扫描，直到确定记录分数的地址，另开一局对该地址的值进行更改即可

经过多次尝试终于确定了记录分数的地址：

![image-20221128221901636](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128221901636.png)

修改其初始值

![image-20221128221940124](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128221940124.png)

得到答案

### little_thief

流量包分析

常规操作，导出http流查看是否存在有用信息：

![image-20221128222100705](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222100705.png)

好，导出

有密码，找

追踪tcp流逐个分析

![image-20221128222245109](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222245109.png)

拉到最上面可以发现是登录信息

![image-20221128222337227](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222337227.png)

以游客身份登陆的信息，没啥用，再找

![image-20221128222409363](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222409363.png)

一眼jwt

![image-20221128222443977](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222443977.png)

好，解一下

![image-20221128222522970](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222522970.png)

找到压缩包密码了

打开flag.html，爆搜没有搜到flag,应该是隐写

使用wbStego4.3open进行解密，没有密钥

得

![image-20221128222843144](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128222843144.png)

### Redcoast

一堆二进制，转为16进制保存，得到压缩包

将压缩包里的图片按25x25的大小进行排列，得到一个二维码

![image-20221207221705935](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207221705935.png)

exp1:

```
from PIL import Image
import binascii
import base64

a=Image.new('RGB',(2500,2500),'white')
n=0
for i in range(25):
    for j in range(25):
        b=Image.open(f'./pic/{n}.png')
        a.paste(b,(100*j,100*i))
        n+=1
a.save('6.png')
```

扫描，得到压缩包密码：

![image-20221207221758034](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207221758034.png)

打开里面还是一堆数字+字母

cyberchef:

![image-20221207214408846](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207214408846.png)

是一张图片，但是存在许多混乱的像素带：

![image-20221207221939223](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207221939223.png)

用stegsolve或者写脚本将每一个像素点的rgb提取并转为ascii

exp2:

```she
from PIL import Image
img = Image.open(r'C:\Users\19682\Desktop\3.png')
pixel =[]
imgSize = img.size
x, y = imgSize[0], imgSize[1]  # (x1200, y750)
for i in range(y):
    for j in range(x):
        pixel.append(img.getpixel((j, i)))
message = ''
for i in pixel:
    for j in i:
        message += chr(j)
with open('flag.txt', 'a', encoding='utf-8') as f:
        f.write(message)
        f.close()
```



![image-20221207221404264](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207221404264.png)