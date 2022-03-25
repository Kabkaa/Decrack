# Decrack
本文将探讨目前按键小精灵脚本的防破解，仅限于按键小精灵（以下简称按键脚本），因各脚本软件构架和编程语言的不同，其它脚本不在本文讨论范畴。文章后边会有相应的防护方式及插件代码，但强烈推荐细读本文。
目前市面上按键脚本破解甚多，经过一定的被破解脚本采样分析可知，按键脚本被破解可分为如下两种形式：
1.修改APK程序
2.直接修改脚本文件
其中第二种方式很难防御，但是因为难度较高，会的人很少，本文主要讨论第一种也是最常见的破解方式的防护。要讨论如何防护破解首先要讨论的是脚本是如何验证和保护的，一个收费的按键脚本最常见的验证方式便是卡密，卡密的验证方式会有两种：
1.本地验证
2.网络服务器验证
第一种是很罕见的，因为本地验证意味着卡密是不变的，所有用户使用同一卡密，意义不大，不在本文讨论之列。
重点看第二种网络验证，网络验证又分为两种：
1.按键官方服务器
官方使用了RSA签名的方式来保证和服务器的通讯不被篡改，同时对脚本文件多加了一层加密(后文会提及加密层数问题)，客户端向服务器提交卡密，服务器返回脚本解密的密匙和卡密剩余时间，客户端验证后将密匙提交给so，so层对脚本文件进行解密和运行，运行后so层于服务器进行心跳通讯。官方卡密验证乍看起来是不错的，只可惜因为官方对小精灵的人数有限制，所以催生出了离线版，离线自然是要剔除官方心跳的，结果顺带发现了官方卡密的漏洞……
2.各种三方验证服务
各种三方验证体系就各有千秋了,常见的有权朗,泡椒云,百宝云,易游等,还有自建服务器的作者,纵观各个验证系统均有一个漏洞,那就是服务器会对客户端发来的数据做校验,然而客户端却对服务器的回复数据不做校验,因此便有了可乘之机,拦截下脚本对服务器的通讯,模仿服务器的回复,脚本自然认为卡密通过了.因此防破解是两个方向的,验证服务端真伪和防止APK被篡改.对于辨别服务器真伪还是比较麻烦的,服务器没有对自身数据进行签名校验,因此从回复内容无从得知,最好的办法是找服务端有签名返回的加密服务.下面就防篡改进行讨论.
防破解防护:
防护卡密不被破解需要对文件进行校验，如校验失败将不予运行。需要校验的文件是位于APK的assets下的DaemonClient.zip和位于\lib\armeabi-v7a下的libmqm.so，另在\lib\x86下还有一个libmqm.so，这是x86构架下使用的，可防可不防，只有少数模拟器和x86安卓会用到，具体含义可自行了解，不再赘述。对于DaemonClient.zip的校验方式可使用字节大小校验，因为其为压缩包，修改它必须解压修改，修改后重新压缩其大小将无法保证和未修改版本相同。对于libmqm.so则需要使用MD5或是CRC校验算法，因为对它的修改是不会改变其大小的。同时注意这两个文件在APK版本不同时是不同的。
 
在对被保护文件进行更改后,校验失败脚本不能运行.
以上方法对官方和三方均有保护作用.除此之外官方还有另一点可增加破解难度.
进入官方后台,找到应用管理
 
可以找到一个高级版的验证代码,使用该代码代替普通版验证代码,可防简单APK修改破解.
最后,附送工具DeCrack工具,使用方法是打包你写好的小精灵两遍, 第一遍直接打包,然后拖进软件,把得到的代码放在脚本前面,再打包一遍,此时改APK破解的手段就失效了.
