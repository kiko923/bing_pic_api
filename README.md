
![输入图片说明](https://images.gitee.com/uploads/images/2020/0215/172524_c4b55d80_4785166.jpeg "2020020907522347.jpg")

必应（Bing）集成了多个独特功能，包括每日首页美图，通过将来自世界各地的高质量图片设置为首页背景，美轮美奂的必应美图备很多人当做壁纸使用，今天我们自己搭建api服务自动获取每天的必应壁纸美图，搭建好的api服务可以用来作为网页背景或者其他服务调用，非常方便！

### 作者信息：

1. 作者：墨涩
2. QQ：651921384
3. 微信：mosetuifei
4. 网站：https://www.sunweihu.com/


### 本站必应壁纸api服务

https://api.sunweihu.com/api/bing1/api.php

### 原理分析

经过对必应首页的抓包，我们可以获得首页图的获取API。它的格式是这样的：

http://cn.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1

这里有几个GET参数，它们的作用分别是：

n，必要参数。这是输出信息的数量。比如n=1，即为1条，以此类推，至多输出8条。
format，非必要。返回结果的格式，不存在或者等于xml时，输出为xml格式，等于js时，输出json格式
idx，非必要。不存在或者等于0时，输出当天的图片，-1为已经预备用于明天显示的信息，1则为昨天的图片，以此类推，idx最多获取到前16天的图片信息
这里将n设定为1、format设定为js、idx设定为1，去发出GET请求，返回的数据是这样的：

```
{
    "images": [
        {
            "startdate": "20161222",
            "fullstartdate": "201612221600",
            "enddate": "20161223",
            "url": "/az/hprichbg/rb/TheDomeEdinburgh_ZH-CN11993142817_1920x1080.jpg",
            "urlbase": "/az/hprichbg/rb/TheDomeEdinburgh_ZH-CN11993142817",
            "copyright": "爱丁堡一家叫做The Dome的夜店，苏格兰 (© Marty McKillop/500px)",
            "copyrightlink": "http://www.bing.com/search?q=The+Dome,+Edinburgh&form=hpcapt&mkt=zh-cn",
            "quiz": "/search?q=Bing+homepage+quiz&filters=WQOskey:%22HPQuiz_20161222_TheDomeEdinburgh%22&FORM=HPQUIZ",
            "wp": false,
            "hsh": "376393c9b49c6d8d1a6e7c2d38343105",
            "drk": 1,
            "top": 1,
            "bot": 1,
            "hs": []
        }
    ],
    "tooltips": {
        "loading": "正在加载...",
        "previous": "上一个图像",
        "next": "下一个图像",
        "walle": "此图片不能下载用作壁纸。",
        "walls": "下载今日美图。仅限用作桌面壁纸。"
    }
}
```

其中的“images”节点下的“url”值便是我们要获取的图像地址。我们把它取出来，再加上Bing的网址前缀(http://cn.bing.com)即组合成了完整的图像地址。比如说上面返回数据的完整图像地址是这样的：

http://cn.bing.com/az/hprichbg/rb/TheDomeEdinburgh_ZH-CN11993142817_1920x1080.jpg

知道了背景图的获取方式，接下来就是用PHP去动态抓取了。

### 搭建api服务


你只需新建一个php文件，贴入如下代码：

```
<?php
$str = file_get_contents('http://cn.bing.com/HPImageArchive.aspx?idx=0&n=1');   // 从bing获取数据
 
if(preg_match('/<url>([^<]+)<\/url>/isU', $str, $matches)) { // 正则匹配抓取图片url
    $imgurl = 'http://cn.bing.com'.$matches[1];
} else {  // 如果由于某些原因，没抓取到图片地址
    $imgurl = 'http://img.infinitynewtab.com/InfinityWallpaper/2_14.jpg'; // 使用默认的图像(默认图像链接可修改为自己的)
}
 
header("Location: {$imgurl}");    // 跳转至目标图像
```


然后把这个php文件上传到你的服务器或者是网站空间，访问这个php应该就能看到被跳转到了Bing的图片。

注意：服务中使用的preg_match指令最高支持php7.2版本，高于php7.2的版本会无法使用。

### 使用方法：

直接将那个php文件的绝对地址当做图片放进网页中即可。你也可以直接调用本站的必应壁纸api服务使用。

### 地址：

https://api.sunweihu.com/api/bing1/api.php

### php文件下载

https://www.lanzous.com/i96nide






# 【原创】通过API获取每日必应壁纸 Bing Wallpaper

## [](#p-1001496-api-1)API

> [https://bing.com/HPImageArchive.aspx 14](https://bing.com/HPImageArchive.aspx)
> 
> 或
> 
> [https://cn.bing.com/HPImageArchive.aspx 10](https://cn.bing.com/HPImageArchive.aspx)
> 
> 完整示例
> 
> [https://cn.bing.com/HPImageArchive.aspx?format=js&mkt=zh-CN&idx=0&n=8&nc=1612409408851&pid=hp&FORM=BEHPTB&uhd=1&uhdwidth=3840&uhdheight=2160 24](https://cn.bing.com/HPImageArchive.aspx?format=js&mkt=zh-CN&idx=0&n=8&nc=1612409408851&pid=hp&FORM=BEHPTB&uhd=1&uhdwidth=3840&uhdheight=2160)

## [](#p-1001496-h-2)参数

参数名

参数值

描述

format

js/rss/xml

决定返回结果格式，==必填==

idx

\-1 到 8 的整数

日期偏移量，若空或0则返回本日数据，-1返回明日，1返回昨日，以此类推，==必填==

n

1 到 8 的整数

返回结果数量，==必填==

mkt

en-US / zh-CN

决定国家地区，==若访问IP为大陆，此参数失效，强制返回zh-CN结果==

uhd

1

若为空或0，返回`1080P`结果，若为 1 则返回`4K`结果，==从2019.05.10开始支持==

uhdwidth=3840

3840

与 `uhd` 字段搭配使用，可为空，决定返回的 `url` 字段的分辨率参数，意义不大

uhdheight=2160

2160

与 `uhd` 字段搭配使用，可为空，决定返回的 `url` 字段的分辨率参数，意义不大

pid

hp

若为空，返回结果的 `copyrightlink` 字段带域名，若非空则不带域名

nc

13位时间戳

根据时间筛选，由于 `idx` 存在限制，意义不大，可为空

FROM

BEHPTB / REDIRERR

目前作用存疑，疑似区分国内版/国际版？可为空

## [](#p-1001496-h-3)调用方式

*   `Http` 请求

## [](#p-1001496-h-4)注意

*   使用大陆IP访问会导致 `mkt` 字段失效
    
*   \==过久的图片会变成同一张！！切记及时下载保存==，例如：
    
    > [bing.com/az/hprichbg/rb/JulianAlps_ZH-CN11764181030_1920x1080.jpg 10](http://bing.com/az/hprichbg/rb/JulianAlps_ZH-CN11764181030_1920x1080.jpg)
    > 
    > [bing.com/az/hprichbg/rb/Policewomen_ZH-CN9260416327_1920x1080.jpg 8](http://bing.com/az/hprichbg/rb/Policewomen_ZH-CN9260416327_1920x1080.jpg)
    
*   从`2019-05-10`开始，支持 `UHD` 格式
    
*   从 `url` 的结构有两种
    
    *   `2019-03-08`的`url`内容
        
        > “url”: “/az/hprichbg/rb/Policewomen\_ZH-CN9260416327\_1920x1080.jpg”,
        
    *   `2019-03-09`的`url`内容
        
        > “url”: “/th?id=OHR.GrapeHarvest\_ZH-CN9372743517\_1920x1080.jpg&rf=NorthMale\_1920x1080.jpg&pid=hp”,
        
*   可以使用如下两种方式对图片进行缩放，以这两张图片为例
    
    > [https://cn.bing.com/th?id=OHR.RPIR_EN-US1987126650_UHD.jpg 2](https://cn.bing.com/th?id=OHR.RPIR_EN-US1987126650_UHD.jpg) ==注意这里的UHD，下面会去掉==
    > 
    > [https://cn.bing.com/th?id=OHR.SnowBuntings_ZH-CN6554424742.jpg 2](https://cn.bing.com/th?id=OHR.SnowBuntings_ZH-CN6554424742.jpg)
    
    *   在`.jpg`前加上分辨率，此方式下载出的图片即为调整后分辨率，==注意此种方式需要去掉UHD 等标识==
        
        > [https://cn.bing.com/th?id=OHR.RPIR_EN-US1987126650_1366x768.jpg 2](https://cn.bing.com/th?id=OHR.RPIR_EN-US1987126650_1366x768.jpg) ==去掉UHD标识==
        > 
        > [https://cn.bing.com/th?id=OHR.SnowBuntings_ZH-CN6554424742_1366x768.jpg 3](https://cn.bing.com/th?id=OHR.SnowBuntings_ZH-CN6554424742_1366x768.jpg)
        
    *   在地址最后加上参数，此方式适合无需下载的页面展示
        
        > [https://cn.bing.com/th?id=OHR.RPIR_EN-US1987126650_UHD.jpg&w=1366&h=768 4](https://cn.bing.com/th?id=OHR.RPIR_EN-US1987126650_UHD.jpg&w=1366&h=768) ==这里则可以继续使用UHD标识==
        > 
        > [https://cn.bing.com/th?id=OHR.SnowBuntings_ZH-CN6554424742.jpg&w=1366&h=768 4](https://cn.bing.com/th?id=OHR.SnowBuntings_ZH-CN6554424742.jpg&w=1366&h=768)
