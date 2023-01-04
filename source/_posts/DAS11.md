---
title:  DASCTF-month11
categories: ctf
toc: true
date: 2022/11/27
tags: 比赛
---

## 七仙女下凡

附件给了6张包含内容相同的图片

![image-20221128224912835](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128224912835.png)

对比观察可发现几张图片宽高之间存在一定的比例关系

尝试使用stegsovle中的image combiner模块进行组合

image combiner中存在多种组合方式（包括异或、或、与等），鉴于本题宽高的特殊比例，选择其中的水平交错（Vertical interlace）与垂直交错（Horizontal interlace）

交错存在以下顺序：

  先1和2拼，垂直交错增加宽度，拼出来图片的再和3拼进行水平交错加高，再和4拼，加宽，再和5，加高，和6，加宽，最后和7，加高

导出7张图片合并完的最终结果，打开，观察颜色通道：

![image-20221128215009020](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128215009020.png)

red 0处存在明显异常，使用数据提取，rgb均选择最低位

![image-20221128215425200](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128215425200.png)

根据16进制，存在jpg文件，导出改后缀

![image-20221128215515278](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128215515278.png)

## EzUSB

这道题，根据后面的flag应该考查的是对于ntfs格式的usb硬盘的dbr分区恢复（事实上也确实是，因为观察dbr扇区可发现是全空的

但选择取证大师直接嗦

![image-20221128215739910](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128215739910.png)

拉到取证大师直接深度恢复可发现题目中说的机密文件的压缩包

导出，打开提示文件格式错误

使用winhex查看

![image-20221128215902208](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128215902208.png)

说实话，第一直觉，缺文件头，但观察主体也不像个zip文件的16进制格式

后来发现，是将zip文件的16进制进行了倒序

逆过来，单独保存，打开

![image-20221128220122458](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128220122458.png)

![image-20221128220131301](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128220131301.png)

![image-20221128220150263](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221128220150263.png)

这里放上皓文学长手修usb的dbr扇区的[链接](https://www.cnblogs.com/zhwer/p/15321534.html) Orz

## Dink Kirby PC（复现）

与内存取证有关

vol分析一下内存镜像

![image-20221207184329466](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207184329466.png)

导出notepad.exe的进程

![image-20221207185235901](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207185235901.png)

命令：

```she
volatility_2.6_win64_standalone -f 66.raw --profile=Win7SP1x64 memdump -p 3860 -D ./
```

![image-20221207202327501](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207202327501.png)

发现一个带锁的压缩包，结合passkit提取的电脑开机密码可以解开

![image-20221207203429489](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207203429489.png)

这里的perl命令不是太会，但是大体上的意思应该是对$_PASSWORD进行了加密，我们可以尝试手撸爆破出来，当然也可以写脚本

TeamGipsy的exp:

```she
import string
x='878189096364096066836664'
string1='0123456789'
string2='8195376024'
string3=string.digits+string.ascii_lowercase
string4=string.ascii_lowercase+string.digits
x=x.translate(str.maketrans(string2,string1))
flag=''
for i in range(0,len(x),2):
    flag+=chr(int(x[i:i+2])^0x31)
flag=flag.translate(str.maketrans(string3,string4))
print(flag)
#ead803812f23
```

得到了aes的密钥，那么aes呢？

应该还在内存里，找吧

经过aes加密的字符串都以U2Fsd开头，我们可以直接搜关键字

![image-20221207204353939](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207204353939.png)

理论上取证大师的原始数据应该也可以搜索到

现在我们解密

![image-20221207204623577](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207204623577.png)

拿这个密码去解密附件给的encrypt.crypto文件

![image-20221207204817309](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207204817309.png)

ok了

![image-20221207204837137](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221207204837137.png)
