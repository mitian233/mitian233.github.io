---
title: StickersPackDownloader使用说明
date: 2021-02-28 19:54:33
tags:
    - 作品
    - 文档
---

## 简介

这是一个用来下载某聊天工具贴纸包的工具

之前在GitHub上开源[该项目](https://github.com/mitian233/StickersPackDownloader "项目主页")的时候是拿英文写的[Readme](https://github.com/mitian233/StickersPackDownloader/blob/master/readme.md "GitHub")，毕竟我自己英语也比较蹩脚，干脆就乘此机会用中文重写一遍吧。

## 运行

用户在已安装Python环境的系统内

1. 克隆该存储库。（或者直接下载zip格式文件）
   ``` bash
   $ git clone https://github.com/mitian233/StickersPackDownloader.git
   ```

2. 程序使用第三方库`requests`，需要后期安装。
    ``` bash
    $ pip install requests
    ```

3. 使用你的Python IDE调试或者直接运行`.py`文件，按照控制台输出的提示输入相关信息即可完成下载。
   ![screenshot](https://raw.githubusercontent.com/mitian233/ImagesService/master/20210228212228.png)
   下载的文件在同一目录下，名称为`stickers.zip`

## 贴纸包ID的获取

1. 进入[LINE STORE中文主页](https://store.line.me/home/zh-Hans "LINE STORE")

2. 搜索你想要下载的贴纸包，进入详情页面
    ![screenshot](https://raw.githubusercontent.com/mitian233/ImagesService/master/20210228205218.png)
    地址栏`product`与`zh-Hans`之间的数字（不带斜杠）即为**贴纸包ID**
   
    例如该贴纸包详情页地址为
   ```url
   https://store.line.me/stickershop/product/9022/zh-Hans
   ```
    则该贴纸包ID为`9022`

## 进阶使用

该Python脚本代码如下：
```Python
import requests #导入requests库

ua = {'User-Agent': 'Mozilla/5.0'} #伪装用户代理
id = input("Input LINE stickers pack ID:")  #要求用户输入贴纸包ID
url = ("http://dl.stickershop.line.naver.jp/products/0/0/1/" + id + "/iphone/stickers@2x.zip")  #创建下载地址（已知格式）

ans = input("Download stickers pack on " + url + " Sure? (Y/N):")  #和用户确认刚才创建的下载地址
if ans[-1] in ['Y', 'y']:
    r = requests.get(url, headers=ua)
    print(r.status_code)  #确认状态码（200为正常，则保存文件成功）
    with open('stickers.zip', 'wb') as f: # 将获取到的二进制数据保存到同一目录下的stickers.zip中
        f.write(r.content)
    exit()
elif ans[-1] in ['N', 'n']:
    print("Process canceled. ")
    exit()
else:
    print("Invalid input. ")
    exit()
# End of file
```
*其贴纸包文件的下载链接有已知规律，故不做复杂的爬虫。*

默认下载贴纸放大2倍。若想下载一倍的贴纸请将下载链接最后的`/stickers@2x.zip`改为`/stickers.zip`即可下载未经放大的版本。

当然自行更改下载时创建的文件名称也是可以的，但压缩包格式必须是zip

_P.S.其实直接把代码复制下来就能跑了，很简单的脚本（_

## 后期可能会添加进去的功能

_作者水平有限，只写了个二进制数据保存到文件的功能_

- 用户自行设置下载图像的倍率（Line商店提供一倍或二倍版本，目前默认下载两倍素材）
- 按贴纸包标题命名下载完成的zip文件
- 批量下载

## 版权声明

**LINE STORE上所有的贴纸包版权均由作者所有，使用该脚本导致您受到版权纠纷，作者概不负责。**

**该工具提供与用户免费使用，禁止用于任何商业用途。**

如有任何问题都可以于项目页面向我提交issues ~~（改不改不一定，我是肯定会光明正大地咕了的）~~

## 彩蛋

今天正好ガルパ六周年，祝生日快乐🎂（2021年2月28日）