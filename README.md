# 微信公共帐号图片自动回复程序
该程序实现通过向微信公共帐号发送特定内容（本例中是发送1024）自动收到图片（本例中是回复美女图片）的效果。

程序文件说明及机制如下：
* 图片爬虫程序：beauty_clawer.py
* media_id存储接口程序in_media_db.php
* 微信接口程序index.php
* media_id数据库结构文件1024_beauty.sql

图片爬虫程序在爬取图片之后将图片保存至本地images目录中，然后调用media_id存储接口程序将images目录中的图片作为临时素材逐个上传至微信服务器，并将上传素材后所得到的media_id以及文件名保存在MySQL数据库中。当用户向公共微信发送文本内容时，由微信接口程序判断其发送内容是否是“1024”，如是，则连接数据库随机获取media_id值，并通过微信接口返回图像文件进行展现。

此外还包括定时进行图片爬取更新的脚本clawer.sh，可通过修改该脚本中的Python参数来设置每次爬取的图片数量，并通过crontab设置计划任务定期爬取图片。

受限于公共微信接口权限，程序部署需满足两个条件：
* 公共微信帐号需是认证帐号（订阅号/服务号）；
* 微信素材管理需获得接口调用凭据access_token，获得access_token的接口调用频率限制是**2000次/日**，即每天最多上传2000张图片；
* 因临时素材media_id有效期是**三天**，故至多需要每三天执行一次图片爬虫程序以更新media_id数据库；

## 开发服务器环境配置
* PHP版本：需5.5以上版本；
* MySQL版本：为与PHP的MySQL扩展程序版本兼容，建议与PHP版本一致；
* Python版本：v2.7

## 程序部署说明
**微信公众号开发者模式开通及配置请自行查看微信公众平台管理后台及[开发者文档](http://mp.weixin.qq.com/wiki/home/index.html)**

微信开发者模式下开发服务器接口仅接受80端口，将wx_config.php、index.php、in_media_db.php置于Web目录下，scripts目录置于非Web目录下（否则可通过开发服务器下载爬虫程序）。

此外，需注意以下几点：
* 微信公共平台服务器配置的URL可配置为其他接口文件；
* 程序中的Web目录相对于scripts目录的相对路径是../www/weixin/，需自行调整；
* 程序架构支持分布式，即可将爬虫程序、数据库和微信接口分别部署于不同服务器；
* 微信接口程序使用明文消息加解密方式开发，若选择其他方式可自行调整；
