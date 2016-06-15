# WHMCS-Shadowsocks
一个WHMCS模组

本插件和文章来源于并修改于 [小蒋博客](https://www.zntec.cn/archives/whmcs-ss-module.html)

感谢 [小蒋博客](https://www.zntec.cn/archives/whmcs-ss-module.html) 和 [古尔数据](https://www.gourdata.com/) 的无私贡献!



# 为什么要修改

* 因为我不喜欢直接显示IP 而显示域名，不显示IP 岂不美哉?


* 喜欢折腾
* ~~装逼~~
* 小蒋版本提供的 Shadowsocks Manyuser 并不支持CHACHA20(在已经安装Libsodium的情况下)



# 所需安装:

分四部曲:

* 架设API(以Nginx为例)


* 建设Shadowsocks 后端 (简略带过)


* 安装并设置好WHMCS 模块（最重要的一点）
* 测试



## 在 Nginx+PHP-FPM 服务器上安装API

本文以[军哥的 LNMP 一键包](http://lnmp.org) 为例

这里的 API 要和你准备用来给 Shadowsocks Manyuser 用的数据库是同一个服务器，否则就没意义了。

条件允许,建议API和WHMCS 装在同一个 VPS最好。

1、把 API 目录下的 config 目录和 shadowsocksapi.php 放到公共目录，提供公网访问。

举个例子，如果你正在使用[军哥的 LNMP 一键包](http://lnmp.org)，那么你默认就把这个API文件放到 `/home/wwwroot/default`

2、新建一个数据库，并且导入 shadowsocks.sql 到数据库，新建数据库账户时注意设置所有地址访问。

3、修改 config 目录下的 configuration.php，把刚刚新建的数据库账户密码填进去。

**4、限制 UA 访问，这一步特别重要。**

修改Nginx的站点配置，如果是军哥LNMP包 Nginx的那么配置文件就在 /usr/local/nginx/conf/vhost

在合适的地方加入如下内容（例如 `root  /home/wwwroot/default` 的下面）：

> 	root  /home/wwwroot/default;
> 	
> 	if ($http_user_agent != "ThisIsUA"){
> 		return 444;
> 	}
>

这里把上面的“ThisIsUA”改为一段 HASH，尽可能的长，例如：

> ```
> root  /home/wwwroot/default;
>
> if ($http_user_agent != "thM95vqtyvT5d7UCUmDYkHrh"){
> 	return 444;
> }
> ```



**为了安全和便利，建议把API listen 到一个奇葩的端口。**
如 

> listen 80;

改为

> listen 9527;



然后用`crontab -e`添加计划任务

    0 0 1 * * php -q /home/wwwroot/default/cron.php



## Shadowsocks 服务器端安装

参考 [Breakwa11写的安装ShadowsocksR Manyuser教程](https://github.com/breakwa11/shadowsocks-rss/wiki/Server-Setup(manyuser))



## WHMCS 模块安装

关于 WHMCS 就不多介绍了，你连 WHMCS 都不知道，你可以忽略了

1、把模块按照目录放到 WHMCS，进入后台新建 server，IP 地址填写 API 主机的 IP

2、Access Hash 填入你刚刚那一段 HASH，账户、密码什么的留空就好了

3、新建产品，module 界面输入 API 主机的端口，如果你没有修改监听端口就输入 80，如下图



这里需要注意的就是节点列表最后一个的右边不需要” | “这个符号。

4、如果想让客户可以定制购买时候的流量，可以新增一个配置选项，名字是`traffic|流量套餐`



价格自己设置好，如果设置无误的话用户在下单的时候就可以定制流量了，如下：



## 测试

如果你一直到这一步都没出什么错的话，那么就可以自己下单试试可否自动开通啦，如果不行的话就自己检查 API 主机的 IP 对不对、端口对不对、HASH 对不对之类的问题。



比此次放出的版本多了导出 gui-config.json 自动配置和一个一周流量图表、流量数据隔日缓存等等，如果接下来真的会有人转载我的这份源码的话、我希望可以保留我的名字，要求不多、仅仅是 Tomas 五个字母。







