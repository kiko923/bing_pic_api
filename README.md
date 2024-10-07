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
