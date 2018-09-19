---
title: hexo+github+yilia+域名搭建博客
date: 2018-06-12 20:37:44
tags:
- 博客搭建
---

使用hexo搭建一个拥有自己域名的博客是一件很炫酷的事情~~~，这个端午节花一个小时搭建自己的博客岂不美哉！
[我的博客](http://www.xiaopeng.pro)

# 步骤
1. 安装Node.js/Git/Hexo
2. 初始化
    - 创建仓库
    - 添加ssh key到github
    - 发布文章
3. 安装主题
4. 添加相册功能
5. 网银云音乐外链
6. 绑定个人域名
7. 畅言评论功能

<!--more-->

# 1、安装Node.js/Git/Hexo
1. Node用来生成静态页面，到[Node.js官网](https://nodejs.org/en/)下载相应平台的最新版本，一直下一步即可。
2. git：把本地代码提交github管理，没有安装的[可以参考](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)
3. 安装Hexo
windows可以参考[官方教程](https://hexo.io/zh-cn/docs/index.html)，mac见下：
```
sudo npm install -g hexo
```

# 2、初始化
cd到一个选定的目录，例如/Users/xiaopeng/BlogNoun，执行
```java
hexo init
```

在BlogNoun目录下，执行如下命令，安装npm
```java
npm install
```

在BlogNoun目录下执行如下命令开启hexo服务器
![博客搭建-1](/img/博客搭建/001.png)

此时，在浏览器地址栏输入http://localhost:4040， 就能看见类似的页面:
![博客搭建-2](/img/博客搭建/002.png)

## 2.1、创建代码仓库
注册[github](https://www.github.com)账号，新建仓库，命名为**用户名.github.io**。例如我的用户名是xpengV
![博客搭建-3](/img/博客搭建/003.png)
本地的BlogNoun文件夹目录结构为：
```java
-rwxr-xr-x@   1 xiaopeng  staff     2219  6 14 21:04 _config.yml
-rw-r--r--    1 xiaopeng  staff  1868411  6 14 21:04 db.json
drwxr-xr-x  309 xiaopeng  staff     9888  6 13 21:35 node_modules
-rwxr-xr-x    1 xiaopeng  staff      554  6 13 20:58 package.json
drwxr-xr-x    5 xiaopeng  staff      160  6 13 20:57 scaffolds
drwxr-xr-x    7 xiaopeng  staff      224  6 14 12:29 source
drwxr-xr-x    4 xiaopeng  staff      128  6 13 20:57 themes
```
打开_config.yml文件，修改部分文件：
```java
deploy:
  type: git
  repository: git@github.com:用户名/用户名.github.io.git
  branch: master
```
例如我的修改为repository: git@github.com:xpengV/xpengV.github.io.git

## 2.2、连接Github
1. 检查是否可以连接github
```java
ssh -T git@github.com
```
![博客搭建-4](/img/博客搭建/004.png)
若如上则跳过该步骤，否则继续

2. 生成新的ssh key
在BlogNoun目录执行以下命令，邮箱地址是注册github时的邮箱地址
```java
ssh-keygen -t rsa -C "yourEmail@163.com"
```

![博客搭建-5](/img/博客搭建/005.png)
默认会在~/.ssh生成id_rsa和id_rsa.pub两个文件

3. 添加公钥
打开~/.ssh/id_rsa.pub文件，将其中的全部内容拷贝添加到github的SSH key中
github-->setting-->SSH keys-->add SSH keys
![博客搭建-6](/img/博客搭建/006.png)
添加后点击Add Keys按钮，然后使用ssh -T git@github.com测试是否连接成功！

## 2.3、发布文章
终端cd到BlogNoun目录，执行命令 hexo new "filename" 新建文章
```java
BlogNoun xiaopeng$ pwd
/Users/xiaopeng/BlogNoun
BlogNoun xiaopeng$ hexo new "first_article"
INFO  Created: ~/BlogNoun/source/_posts/first-article.md
BlogNoun xiaopeng$ 
```
上述命令会在BlogNoun/source/_posts目录下生成first_article.md文件。

关于markdown语法，可以学习[markdown教程](https://www.jianshu.com/p/20e82ddb37cb)

之后使用
```java
hexo d -g //生成静态页面，并远程部署
```
访问http://用户名.github.io就可以看见你刚发布的文章了。
![博客搭建-008](/img/博客搭建/008.png)

# 3、安装主题
你能看到这里，说明你已经成功的发布了自己的第一篇文章。但是有没有发现你的博客主题和我的不太一样。

Hexo是一个简单优雅，风格多变的平台。这里有很多非常炫酷的主题。[主题连接](https://www.zhihu.com/question/24422335)
我使用的就是[git@github.com:litten/hexo-theme-yilia.git](http://litten.me/)

1. 选择自己喜欢的主题后，获得他的clone with ssh，在BlogNoun目录执行以下命令
获得地址方法：
![博客搭建-009](/img/博客搭建/009.png)
```java
git clone git@github.com:litten/hexo-theme-yilia.git
```

2. 将BlogNoun/_config.yml中的theme属性由landscape修改为yilia
```java
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
#theme: landscape
theme: yilia   #这里修改为你自己的主题
```

3. 重新部署
```java
hexo d -g
```
重新部署过后，刚才的first_article就变成
![博客搭建-010](/img/博客搭建/010.png)

4. 主题自定义
至于更改theme的内容。比如名称，描述，头像，网站连接，关于我等。修改BlogNoun/_config.yml和BlogNoun/theme/yilia/_config.yml
文件中对应的属性值即可。在[hexo-theme-yilia的readme.md](https://github.com/litten/hexo-theme-yilia/blob/master/README.md)中有详细的教程！

# 4、添加相册功能
yilia支持添加相册，但是由于我还没有搞明白像素的问题，所以导致照片有些失真。
![博客搭建-011](/img/博客搭建/011.png)
1. 在BlogNoun目录执行
```java
hexo new page photos  #在BlogNoun/source目录下创建photos目录
```

2. 将yilia处理图片的代码拷贝到photos目录中
*https://github.com/litten/BlogBackup/tree/master/source/photos*
- index.ejs
- ins.css
- ins.js
- ins.json
- lazyload.min.js

3. 修改相应的配置文件（自己研究吧，只说思路）
- source/photos/index.ejs  先屏蔽掉摄影模块的功能；**修改你存放相册照片的url(可以单独建立一个github代码仓库用来存放照片）。我是在source/photos_resource下存放图片的**
```java
<!--
<div class="photos-btn-wrap">
	<a class="photos-btn active" href="javascript:void(0)">Ins</a>
	<a class="photos-btn" target="_blank" href="http://litten.me/gallery/">摄影</a>
</div>
-->
<div class="instagram itemscope">
	<a href="https://raw.githubusercontent.com/xpengV/Blog_backup/master/source/photos_resource/" target="_blank" class="open-ins">图片来自blog_backup/resource，正在加载中…</a>
</div>
```

- source/photos/ins.json 配置图片的相对地址，以及描述信息
```json
"list": [
    {
      "date": "2018-04",           日期
      "arr": {
        "year": 2018,
        "month": 4,
        "src": [                   图片url，可省略
          "",
          "",
          ""
        ],
        "link": [                  图片相对于source/photos_resource的相对地址，图片格式只能是jpg，视频类型只能是mp4
          "20180425/SZLHSGY01",
          "20180425/SZLHSGY02",
          "20180425/SZLHSGY03"
        ],
        "text": [                  图片的描述
          "平安金融中心",
          "放风筝的人们",
          "深圳莲花山公园"
        ],
        "type": [                  文件格式 image对应jpg/video对应mp4
          "image",
          "image",
          "image"
        ]
      }
    },
```

- source/photos/ins.js  加载图片js
```java
...
113     var render = function render(res) {
114       var ulTmpl = "";
115       for (var j = 0, len2 = res.list.length; j < len2; j++) {
116         var data = res.list[j].arr;
117         var liTmpl = "";
118         for (var i = 0, len = data.link.length; i < len; i++) {
119           //相册从github获取时需要作如下操作 https://raw.githubusercontent.com/lawlite19/blog-back-up/master/photos/
120           var minSrc = 'https://raw.githubusercontent.com/xpengV/Blog_backup/master/source/photos_resource/' + data.link[i] + '.jpg';  //去指定目录加载所有图片
121           var src =    'https://raw.githubusercontent.com/xpengV/Blog_backup/master/source/photos_resource/' + data.link[i];
122           var type = data.type[i];
123           var target = src + (type === 'video' ? '.mp4' : '.jpg');
124           src += '.jpg';
125 
126           liTmpl += '<figure class="thumb" itemprop="associatedMedia" itemscope="" itemtype="http://schema.org/ImageObject">\
127                 <a href="' + src + '" itemprop="contentUrl" data-size="640x640" data-type="' + type + '" data-target="' + target + '">\
128                   <img class="reward-img" data-type="' + type + '" data-src="' + minSrc + '" src="/assets/img/empty.png" itemprop="thumbnail" onload="lzld(this)">\
129                 </a>\
130                 <figcaption style="display:none" itemprop="caption description">' + data.text[i] + '</figcaption>\
131             </figure>';
132         }
133         ulTmpl = ulTmpl + '<section class="archives album"><h1 class="year">' + data.year + '<em>' + data.month + '月</em></h1>\
134         <ul class="img-box-ul">' + liTmpl + '</ul>\
135         </section>';
136       }
...

```
**修改加载图片的url、minSrc和src**

4. 打开相册菜单
修改BlogNoun/theme/yilia/_config.yml
```java
menu:
   主页: /
#  随笔: /tags/随笔/
   相册: /photos
```

到这相册功能的开发就完成了^_^~

# 5、网易云音乐外链
以前的QQ空间或者其他博客网站可以加入第三方的音乐播放外链，比如[xiaopeng.pro](http://xiaopeng.pro/2018/04/19/网易云音乐热评-转载/)

1. 访问[网易云](https://music.163.com)
![博客搭建-012](/img/博客搭建/012.png)
点击生成外链播放器可以获得以下代码：
```java
<embed src="//music.163.com/style/swf/widget.swf?-------------------" width="340" height="86"  allowNetworking="all"></embed>
```

2. 将上述代码拷贝到source/_posts中md文件合适的位置即可

# 6、绑定个人域名
我是在阿里云购买的域名，xiaopeng.pro。绑定个人域名操作非常简单。
1. 在BlogNoun/source目录下创建CNAME文件，在里面添加域名信息(你的域名)
![博客搭建-013](/img/博客搭建/013.png)

2. github项目添加域名
在userName/userName.github.io/setting/GitHub Pages添加域名
![博客搭建-014](/img/博客搭建/014.png)

3. 给域名添加解析记录
- ping你的博客地址
```java
source xiaopeng$ ping xpengv.github.io
PING sni.github.map.fastly.net (123.123.123.123): 56 data bytes
Request timeout for icmp_seq 0
64 bytes from 123.123.123.123: icmp_seq=1 ttl=50 time=338.643 ms
64 bytes from 123.123.123.123: icmp_seq=2 ttl=50 time=358.213 ms
^C
--- sni.github.map.fastly.net ping statistics ---
source xiaopeng$ 
source xiaopeng$ 
```
- 打开阿里云域名控制台，配置域名解析
![博客搭建-015](/img/博客搭建/015.png)

记录类型为A的记录值就是上述ping中返回的ip地址；记录类型为CNAME的记录值就是你的博客地址。
点击启用后，在浏览器地址栏输入https//xpengv.github.io就会自动跳转到http://www.xiaopeng.pro了

**注：该步骤，需要域名实名认证后才会生效！**

# 7、畅言评论功能
给网站增加评论功能，效果图:
![博客搭建-016](/img/博客搭建/016.png)

步骤如下：
1. 进入[畅言官网](http://changyan.kuaizhan.com)，注册后在控制台的后台总览中获取APP ID和APP key
2. 将APP ID和APP KEY复制到BlogNoun/theme/yilia/_config.yml文件中
```java
#3、畅言
changyan_appid: --------
changyan_conf: ----------------------------

```

重新部署后打开每篇文章，下面都会有评论框出现。
**注：畅言需要域名备案，否则只有15天试用期**


# 8、总结
至此，你就拥有一个有自己域名的博客。虽然没什么大的用处，但是内心还是窃喜的（嘿嘿）。。。
除了上面这些功能外，比如网站计数器、打赏功能等都可以自己添加。

















