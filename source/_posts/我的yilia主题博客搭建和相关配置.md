---
title: 我的yilia主题博客搭建和相关配置
date: 2019-08-31
tags: blog
---
> 突然想写一篇关于我搭建自己博客的文章，<br>一是巩固一下自己所学的知识，<br>二是以后如果我换电脑了，方便我以后自己好重新配置自己的博客，<br>三是这篇文章或许对于想搭博客的你会有所帮助。

<br>
<!-- more -->

# 1.搭建博客
## 1.1 本地安装Hexo 
下载安装Git与Node.js略过

1.安装hexo
```
npm install hexo -g #-g表示全局安装, npm默认为当前项目安装
```


2.Hexo基本命令
```
npm update hexo -g  #升级 
hexo init <folder>  #执行init命令初始化hexo到你指定的目录
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo generate       #自动根据当前目录下文件,生成静态网页 hexo g
hexo server         #运行本地服务 hexo s
hexo clean          #清理
hexo deploy         #部署 hexo d

hexo server         #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s      #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
```

最常用的是
```
hexo clean
hexo g
hexo d
hexo s
```

3.这个时候hexo s之后就可以在浏览器
```
http://localhost:4000/
```
这里看到hexo博客的基本样子了

## 1.2 将网站推送至github
1.打开本地博客目录配置文件_comfig.yml
修改最后一行的配置：
```
deploy:
  type: git
  repository: https://github.com/UchihaPatrickStar/UchihaPatrickStar.github.io.git
  branch: master
```
2.上传github
```
hexo clean
hexo g
hexo d
```
若执行hexo g 出错则执行
```
npm install hexo --save
```
若执行hexo d 出错则执行
```
npm install hexo-deployer-git --save
```
错误修正后再次执行hexo g 和hexo d 上传到服务器。
若未关联GitHub，执行hexo d时会提示输入GitHub账号用户名和密码，即:
```
username for 'https://github.com':
password for 'https://github.com':
```
输入你的github账号密码即可。
hexo d执行成功后便可通过https://UchihaPatrickStar.github.io 访问博客，看到的内容和http://localhost:4000 相同。
3.记住github密码
在github添加ssh key后不需要每次更新博客再输入用户名和密码。首先检查本地是否包含ssh keys。如果存在则直接将ssh key添加到GitHub之中，否则新生成ssh key。

执行下述命令生成新的ssh key
```
ssh-keygen -t rsa -C "你的邮箱"
```
Mac下利用open ~/.ssh打开文件夹，打开id_rsa.pub文件，里面的信息即为ssh key，将此信息复制到GitHub的Add ssh key路径GitHub->Setting->SSH keys->add SSH key中即可。Title里填写任意标题，将复制的内容粘贴到key中，点击Add key完成添加即可记住密码。

此时本地博客内容便已关联到GitHub之中，本地博客改变之后，通过hexo g和hexo d便可更新到GitHub之中。
## 1.3 更改主题
1.安装yilia
```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
2.修改hexo根目录下的_config.yml:theme:yilia
3.更新
```
cd themes/yilia
git pull
```
# 2. 微信分享设置
这里是themes\yilia\layout\ _partial\post\share.ejs出了问题。
而且是两个问题：
## 2.1 首先是百度网盘取消了生成二维码的功能。
1.解决方法是修改themes\yilia\layout_partial\post\share.ejs
文件中的
```
//pan.baidu.com/share/qrcode?url=
```
修改为
```
//api.qrserver.com/v1/create-qr-code/?size=150x150&data=
```
2.解决方法是在文件中的
```
var sUrl = url.replace(/index\.html$/, '');
  sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'http:' + sUrl;
```
下面加一行（这里的20是根据自己网址长度计算得到的）
```
test=sUrl.substring(20);
```
然后上面就变成了：
```
<%
  var sUrl = url.replace(/index\.html$/, '');
  sUrl = /^(http:|https:)\/\//.test(sUrl) ? sUrl : 'http:' + sUrl;
  test=sUrl.substring(20);
%>
```
然后将上一步中的
```
'//api.qrserver.com/v1/create-qr-code/?size=150x150&data=' + sUrl
```
举例，我的网址是（UchihaPatrickStar.github.io）那么就改为：
```
<div class="page-modal wx-share js-wx-box">
    <a class="close js-modal-close" href="javascript:;"><i class="icon icon-close"></i></a>
    <p>扫一扫，分享到微信</p>
    <div class="wx-qrcode">
      <img src="<%- 'qrcode' in locals ? qrcode(sUrl) : '//api.qrserver.com/v1/create-qr-code/?size=150x150&data=http://UchihaPatrickStar.github.io/' + test  %>" alt="微信分享二维码">
    </div>
</div>
```
# 3. 置顶文章
## 3.1 安装插件
```
npm uninstall hexo-generator-index --save
npm install hexo-generator-index-pin-top --save
```
## 3.2 配置置顶标准
打开：/themes/*/layout（/_macro）/post.ejs
直接在最前面加入以下代码即可
```
<% if (page.top) { %>
  <i class="fa fa-thumb-tack"></i>
  <font color=7D26CD>置顶</font>
  <span class="post-meta-divider">|</span>
<% } %>
```
## 3.3 配置文章
然后在需要置顶的文章的Front-matter中加上top选项即可
top后面的数字越大，优先级越高
```
---
title: 2019
date: 2019-02-14 16:10:03
top: 5
---
```
## 3.4优先级配置
修改根目录配置文件/_config.yml,top值-1标示根据top值倒序（正序设置为1即可），同样date也是根据创建日期倒序。
```
index_generator:
  path: ''
  per_page: 10
  order_by:
    top: -1
    date: -1
```
# 4. 鼠标点击小红心的设置
在
```
hexo/themes/yilia/source
```
文件目录下添加
```
love.js
```
文件。
```
!function(e,t,a){function r(){for(var e=0;e<s.length;e++)s[e].alpha<=0?(t.body.removeChild(s[e].el),s.splice(e,1)):(s[e].y--,s[e].scale+=.004,s[e].alpha-=.013,s[e].el.style.cssText="left:"+s[e].x+"px;top:"+s[e].y+"px;opacity:"+s[e].alpha+";transform:scale("+s[e].scale+","+s[e].scale+") rotate(45deg);background:"+s[e].color+";z-index:99999");requestAnimationFrame(r)}function n(){var t="function"==typeof e.onclick&&e.onclick;e.onclick=function(e){t&&t(),o(e)}}function o(e){var a=t.createElement("div");a.className="heart",s.push({el:a,x:e.clientX-5,y:e.clientY-5,scale:1,alpha:1,color:c()}),t.body.appendChild(a)}function i(e){var a=t.createElement("style");a.type="text/css";try{a.appendChild(t.createTextNode(e))}catch(t){a.styleSheet.cssText=e}t.getElementsByTagName("head")[0].appendChild(a)}function c(){return"rgb("+~~(255*Math.random())+","+~~(255*Math.random())+","+~~(255*Math.random())+")"}var s=[];e.requestAnimationFrame=e.requestAnimationFrame||e.webkitRequestAnimationFrame||e.mozRequestAnimationFrame||e.oRequestAnimationFrame||e.msRequestAnimationFrame||function(e){setTimeout(e,1e3/60)},i(".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: fixed;}.heart:after{top: -5px;}.heart:before{left: -5px;}"),n(),r()}(window,document);
```
在
```
hexo/themes/yilia/layout/_partial/footer.ejs
```
文件的最后， 添加以下代码：
```
<!--页面点击小红心-->
<script type="text/javascript" src="/love.js"></script>
```
<br>

> 如此，一个简约而又简单的博客就搭建完成啦。

<br>

***注：如需更多相关配置，请移步[Shuyan的博客](http://dongshuyan.com/2016/04/07/怎么写博客/) 。***