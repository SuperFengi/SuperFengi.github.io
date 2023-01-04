---
title:  ⎛⎝龙猫⎠⎞
categories: ctf
date: 2022/12/12
tags: 刷题记录
---
lazzzaro师傅出的一道题

下载附件改后缀为.zip

解压出一张打不开的图片和带密码的压缩包

打不开的图片的16进制头与正常jpg比较后发现规律

0101修改为正常图片得到：

![image-20221212223822607](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221212223822607.png)

发现图片16进制尾部隐藏的信息，转两次hex得到：

![image-20221212224021597](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221212224021597.png)

得到压缩包密码并提示进行异或

起初以为是对key文件进行异或，并且key文件与2B异或完确实还能得到一张图片

![image-20221212224134883](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221212224134883.png)

后来发现相偏了，这里考查的并不是双图隐写

因为key文件导入到010保存后的大小是和totoro.jpg一样的，所以是用key直接和totoro.jpg逐字节异或

exp：

```she
f1=open(r'C:\Users\19682\Desktop\Totoro.jpg','rb')
f11=f1.read()
f2=open(r'C:\Users\19682\Desktop\key','rb')
f22=f2.read()
print(len(f11))
print(len(f22))
res=''
for i in range(len(f11)):
    res+=chr(f11[i]^f22[i])

b=open('1234','w')
b.write(res)
f1.close()
f2.close()
```

得到的文件导入010得：

![image-20221212224727962](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221212224727962.png)

在众多+号中存在一段特殊序列，包含+[><-.]，即两个问号中间得部分

复制到brainfuck解密得到flag

![image-20221212225225310](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221212225225310.png)

