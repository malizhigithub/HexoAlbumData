# HexoAlbumData
搭建Hexo博客相册教程

## 图片的处理
- 资源图片是保存在云端上，此教程是存放在GitHub上，可以适当修改放在七牛云也行。首先在电脑上随便一个位置创建一个新的文件夹，这里我创建文件夹名称叫Blog_Album，然后打开git，cd到此文件夹执行git init命令创建git仓库，在此文件夹内新建一个文件夹/photos 在这里存放你需要放在博客中的图片。

- 使用一个Python小脚本对图片进行压缩编辑，并且上传到自己的GitHub，这就需要我们现在GitHub上新建一个Repository,这里我依然命名为Blog_Album, 然后在本地连接远程仓库执行git remote add origin yourrepositroy@gitaddress

- 然后下载脚本tool.py文件至此文件内，此文件需要Imageprocessing.py文件的支持,可以将此文件保存在你的python3包的安装位置
C:\Users\username\AppData\Local\Programs\Python\Python35-32\Lib\site-packages

- 执行tool.py的时候如果没有PIL库的话会报错，安装即可。安装PIL只需在cmd中执行：pip install Pillow就可以了。
此外图片命名方式需要按照特定方式即：2017-02-02_discriptionofyourpic.jpg，图片的格式支持常见格式，时间和描述之间存在下划线。

- 执行tool.py的命令：cmd中python tool.py（文件所处位置完整路径），例如：python E:\malizhiBlog\HexoAlbumData\tool.py ,执行后会生成data.json文件，此文件存储了图片的链接，名称和简介，存放位置修改为你的hexo博客主题下特定位置，这里因为是后期加入的相册，所以我在/hexo/themes/next/source/lib中新建一个文件夹album来存放相册文件。所以tool.py生成data.json需要存放在这里。大约在133行中进行修改

```
- with open("E://YourBlog/themes/next/source/lib/album/data.json","w") as fp:
+ with open("YourBolgPath/themes/next/source/lib/album/data.json","w") as fp:
```

## 博客相册页面

- 在博客根目录下添加photo页面hexo n page photos,在blog的themes的Next中的_config.yml配置文件中menu选项中添加photos:
```
menu:
  home: / || home
  about: /about/ || user
  categories: /categories/ || th-list
  tags: /tags/ || tags
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  音乐: /music/ || music
+ 相册: /photos  || camera
```

在photos中的index.md添加上下面代码

```
---
title: 照片
date: 2018-09-02 21:00:00
type: photos
fancybox: false
comments: true
---
<link rel="stylesheet" href="../lib/album/ins.css">
<link rel="stylesheet" href="../lib/album/photoswipe.css"> 
<link rel="stylesheet" href="../lib/album/default-skin/default-skin.css"> 
<div class="photos-btn-wrap">
  <a class="photos-btn active" href="javascript:void(0)" target="_blank" rel="external">Photos</a>
</div>
<div class="instagram itemscope">
  <a href="http://yourbolg.com" target="_blank" class="open-ins">图片正在加载中…</a>
</div>
 
<script>
  (function() {
    var loadScript = function(path) {
      var $script = document.createElement('script')
      document.getElementsByTagName('body')[0].appendChild($script)
      $script.setAttribute('src', path)
    }
    setTimeout(function() {
        loadScript('../lib/album/ins.js')
    }, 0)
  })()
</script>
```

从index就可以看出我们还需要添加css文件和js文件，这些文件我已经上传到GitHub中的JS&CSS文件夹中，在这里需要把这个文件夹中的所有文件包括此文件夹下的所有目录都拷贝到前面创建的/hexo/themes/next/source/lib/album中。

## JS&CSS文件夹中的内容如下：
![](https://user-gold-cdn.xitu.io/2018/9/2/1659a975f3a8289a?w=212&h=169&f=png&s=4883)

然后再将photoswipe-ui-default.min.js, photoswipe.min.js两个文件添加到themes/next/source/js/src中。

对ins.js（ins.js在album文件夹中）文件进行一个简单的修正，将里面大约在121和122行左右的连接修改为你的GitHub中图片连接地址

```
-    var minSrc = 'https://raw.githubusercontent.com/lizhi/Blog_Album/master/min_photos/' + data.link[i];
-    var src = 'https://raw.githubusercontent.com/lizhi/Blog_Album/master/photos/' + data.link[i];
+    var minSrc = 'https://raw.githubusercontent.com/yougithubname/pathtoyourphotos/min_photos/' + data.link[i];
+    var src = 'https://raw.githubusercontent.com/yougithubname/pathtoyourphotos/photos/' + data.link[i];
```

确保你的图片地址是正确的，否则加载不出来，检测方法可以将选择一个图片地址看能不能再浏览器中打开。

> 注意点：图片的链接都是https://raw.githubusercontent.com 开头的，不是图片的存储地址，否则加载不出

## 其他配置
- 上面属于资源的配置，下面是引用配置，在next/layout/_layout.swig的头部前添加对js文件的引用如下：
```
<script src="{{ url_for(theme.js) }}/src/photoswipe.min.js?v={{ theme.version }}"></script>
<script src="{{ url_for(theme.js) }}/src/photoswipe-ui-default.min.js?v={{ theme.version }}"></script>
```
在body中添加如下的内容：

```
{% if page.type === "photos" %}
  <!-- Root element of PhotoSwipe. Must have class pswp. -->
  <div class="pswp" tabindex="-1" role="dialog" aria-hidden="true">
    <div class="pswp__bg"></div>
    <div class="pswp__scroll-wrap">
        <div class="pswp__container">
            <div class="pswp__item"></div>
            <div class="pswp__item"></div>
            <div class="pswp__item"></div>
        </div>
        <div class="pswp__ui pswp__ui--hidden">
            <div class="pswp__top-bar">
                <div class="pswp__counter"></div>
                <button class="pswp__button pswp__button--close" title="Close (Esc)"></button>
                <button class="pswp__button pswp__button--share" title="Share"></button>
                <button class="pswp__button pswp__button--fs" title="Toggle fullscreen"></button>
                <button class="pswp__button pswp__button--zoom" title="Zoom in/out"></button>
                <!-- Preloader demo http://codepen.io/dimsemenov/pen/yyBWoR -->
                <!-- element will get class pswp__preloader--active when preloader is running -->
                <div class="pswp__preloader">
                    <div class="pswp__preloader__icn">
                      <div class="pswp__preloader__cut">
                        <div class="pswp__preloader__donut"></div>
                      </div>
                    </div>
                </div>
            </div>
            <div class="pswp__share-modal pswp__share-modal--hidden pswp__single-tap">
                <div class="pswp__share-tooltip"></div> 
            </div>
            <button class="pswp__button pswp__button--arrow--left" title="Previous (arrow left)">
            </button>
            <button class="pswp__button pswp__button--arrow--right" title="Next (arrow right)">
            </button>
            <div class="pswp__caption">
                <div class="pswp__caption__center"></div>
            </div>
        </div>
    </div>
  </div>
{% endif %}
```

- 在/themes/next/layout/_scripts/pages/post-details.swig中添加：

```
<script type="text/javascript" src="{{ url_for(theme.js) }}/src/photoswipe.min.js?v={{ theme.version }}"></script>
<script type="text/javascript" src="{{ url_for(theme.js) }}/src/photoswipe-ui-default.min.js?v={{ theme.version }}"></script>
```
- 当主题设置fancyboxy为True时，在相册页面存在冲突去掉主题的fancybox所以对/themes/next/layout/_partials/head.swig文件进行修改：

```
+{% if page.type === "photos"  %}
+  {% set theme.fancybox = false %}
+{% endif %}
+{% if page.type !== "photos"  %}
+  {% set theme.fancybox = true %}
+{% endif %}

{% if theme.fancybox  %}
  {% set fancybox_css_uri = url_for(theme.vendors._internal + '/fancybox/source/jquery.fancybox.css?v=2.1.5') %}
  {% if theme.vendors.fancybox_css %}
    {% set fancybox_css_uri = theme.vendors.fancybox_css %}
  {% endif %}
  <link href="{{ fancybox_css_uri }}" rel="stylesheet" type="text/css" />
{% endif %}
```

## 效果
![](https://user-gold-cdn.xitu.io/2018/9/2/1659a9a5569553ad?w=487&h=508&f=png&s=171549)