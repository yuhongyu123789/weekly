---
title: Hexo博客搭建
date: 2023-10-14 18:49:02
tags: 其他
mathjax: true
---

# Hexo 博客搭建
## 一、安装Node .js和Git
> 验证：
>
> ```bash
> node -v
> npm -v
> git --version
> ```

## 二、安装Hexo

```bash
npm install hexo-cli -g
```

> 验证：
>
> ```bash
> hexo -v
> ```
>
> 

## 三、Github配置

### 建立Github仓库

左上角选择`New repositories`，仓库名起名为`Monoceros406.github.io`，并建立README文件，然后`Creating repository` 

### 本地博客文件

新建文件夹存放网站，并`Git Bash Here`。

```bash
#连接国内服务器（淘宝）
npm install -g cnpm --registry=https://registry.npm.taobao.org
#安装Hexo
cnpm install -g hexo-cli
#初始化Hexo博客：
hexo init
#启动服务，在本地预览
hexo s
#生成（Hexo自带Hello World博客）
hexo g
```

## 四、设置SSH

在Git下：

```bash
cd ~/.ssh
ssh-keygen -t rsa -C ‘注册时的邮箱地址’
#一路回车即可
```

去看回显中的路径找到`id_rsa.pub`，记事本打开并全部复制。

在Github上头像找到`Settings->SSH and GPG keys->New SSH key`，名字随意。

```bash
git config --global user.name “注册时用户名”
git config --global user.email “注册时邮箱”
```

## 五、上传博客

找到文件夹下`_config.yml`文件，最后改为：

```yaml
deploy:
        type: git
        repo: https://github.com/用户名/用户名.github.io.git
        branch: main
```

并下载上传工具：

```bash
cnpm install hexo-deployer-git
```

## 六、文章部署

```bash
#在source文件夹下_posts内新建文章.md
hexo new "文章名称"
#在本地localhost中预览
hexo s
#确认无误生成文件
hexo g
#部署到Github
hexo d
```

## 七、主题设置

使用主题：`Butterfly 4.10.0`

https://github.com/jerryc127/hexo-theme-butterfly

```bash
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
	#或Gitee更快：
	git clone -b master https://gitee.com/immyw/hexo-theme-butterfly.git themes/butterfly
#安装插件
npm install hexo-renderer-pug hexo-renderer-stylus
```

在`_config.yml`中更改：

```yaml
theme: butterfly
```

## 八、MathJax数学公式

更换渲染引擎：

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

找到博客根目录下`node_modules\kramed\lib\rules\inline.js`

```javascript
//第11行更改：
escape: /^\\([`*\[\]()#$+\-.!_>])/，
//第20行更改：
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/，
```

进入主题文件夹，找到`_config.yml`，找到并更改：

```yaml
# MathJax Support
mathjax:
  enable: true
  per_page: true
```

在每篇文章开头都加上：

```markdown
---
title: index.html
date: 2016-12-28 21:01:30
tags:
mathjax: true
--
```

### Nunjucks Error: _posts/*.md [Line *, Column *] expected variable end解决方法

在敏感内容前后加上：

```latex
{% raw %}
	敏感内容
{% endraw%}
```

## 九、图片上传

安装插件：

```bahs
npm install hexo-asset-image --save
```

再找到Hexo的配置文件`_config.yml`，中改为：

```yaml
post_asset_folder: true
```

这样一来，在每次新建文章时，`source\_posts`目录下会出现一个`*.md`和一个同名文件夹。将图片放入文件夹内。例如将`1.png`放入文件夹内，Markdown中这样写：

```markdown
![](1.png)
```

一般这样来说就行了。有可能出现版本问题导致路径不对。打开`\node_modules\hexo-asset-image\index.js`，替换为以下代码：

```javascript
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
        var link = data.permalink;
    if(version.length > 0 && Number(version[0]) == 3)
       var beginPos = getPosition(link, '/', 1) + 1;
    else
       var beginPos = getPosition(link, '/', 3) + 1;
    // In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
    var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
        if ($(this).attr('src')){
            // For windows style path, we replace '\' to '/'.
            var src = $(this).attr('src').replace('\\', '/');
            if(!/http[s]*.*|\/\/.*/.test(src) &&
               !/^\s*\//.test(src)) {
              // For "about" page, the first part of "src" can't be removed.
              // In addition, to support multi-level local directory.
              var linkArray = link.split('/').filter(function(elem){
                return elem != '';
              });
              var srcArray = src.split('/').filter(function(elem){
                return elem != '' && elem != '.';
              });
              if(srcArray.length > 1)
                srcArray.shift();
              src = srcArray.join('/');
              $(this).attr('src', config.root + link + src);
              console.info&&console.info("update link as:-->"+config.root + link + src);
            }
        }else{
            console.info&&console.info("no src attr, skipped...");
            console.info&&console.info($(this));
        }
      });
      data[key] = $.html();
    }
  }
});
```



