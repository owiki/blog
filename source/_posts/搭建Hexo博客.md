---
title: 搭建Hexo博客
categories: 其他
tag: blog
---

忽然觉得Hexo搭建博客很快，对，那就折腾一下吧！

### 新建GitHub Page仓库

- 建仓库的过程和新建其他仓库一样，只是注意仓库名使用`<username>.github.io`的形式就行，这里不再赘述

### 自定义域名

- 个人建议去GoDaddy买域名，一般都是24块钱左右，土豪另算
- 买完就能在GoDaddy控制台解析域名，一般解析两条就行，一条指向`github.io`这个域名的IP地址（不知道就ping一下），另一条指向`<username>.github.io`，对，就是你刚新建的GitHub Page仓库

#### 以GoDaddy为例，域名解析入口如下图

![域名解析](/images/20191125225206.png)

#### 找到“添加”按钮，新建记录如下


类型 | 主机 | 指向 | TTL 
---|---|---|---
A | @ | github.io这个域名的IP | 600
CNAME | www | <username>.github.io | 600

### 开始搭建hexo博客

```
npm install -g hexo-cli
```

```
hexo init
```
#### 本地运行

- 安装依赖

```
npm install
```
- 生成静态文件

```
hexo generate / hexo g
```
- 本地运行调试

```
hexo server / hexo s
```
#### [关联并发布到GitHub](https://hexo.io/zh-cn/docs/one-command-deployment#Git)

- 修改`_config.yml`文件的`deploy`

```
deploy:
  type: 'git'
  repo: <your GitHub repository url>
  branch: master
  message: <自定义提交信息>
```
- 安装 hexo-deployer-git

```
npm install hexo-deployer-git --save
```
- 发布

```
hexo deploy / hexo d
```
#### [使用next主题](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/README.md)

- 安装

```
git clone https://github.com/theme-next/hexo-theme-next themes/next
```
- 配置，找到`_config.yml`里的`theme`字段，修改为`next`

```
theme: next
```
- 发布，如果不生效先执行`hexo clean`

```
hexo d -g
```

#### 配置next

##### 设置菜单栏

- 打开`themes/next`主题下的`_config.yml`文件，找到`menu`字段

```
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archivemenu
```
- 新建categories和tags标签页

```
hexo new page tags

hexo new page categories
```
- 设置tags和categories页面显示内容

```
title: 博客分类
date: 2019-11-24 14:46:37
comments: false
type: "categories"

title: 博客标签
date: 2019-11-24 14:45:33
comments: false
type: "tags"
```
- 在编辑markdown的时候，在顶部添加表示就能正常显示了

```
---
title: Hello World
categories: 测试分类
tags: [bullshit,what]
---
```
##### 设置代码高亮风格

- 找到`themes/next/_config.yml`文件的`highlight_theme`字段，可以设置注释给的几种样式
- 找到根目录`_config.yml`文件的`highlight`，将`auto_detect`设为`true`
- 重新编译并运行就可以生效了

##### 设置文章评论

- 找到`themes/next/_config.yml`的`comments`字段，随便选一种使用就行

##### 折叠首页文章显示内容

- 找到`themes/next/_config.yml`的`auto_excerpt`字段，设置为`true`
- 重新编译并运行

##### 自定义404页面

- 首先创建一个404页面文件

```
hexo new page 404
```
- 在404页面的html代码前，头部要添加如下一段代码，这样404页面会内嵌在markdown渲染后的框架里

```
---
title: some error
toc: false
comments: false
permalink: /404
---

<doctype html>
xxx
```
##### 自定义Favicon

- 可以去[favicon.io](https://favicon.io/)这个网站找一个Favicon，然后下载下来
- 将Favicon图片放在`themes/next/source/images`文件夹中
- 设置`themes/next/_config.yml`的`favicon`字段，将文件路径改成自己的Favicon文件名
- 重新编译并运行即可生效

##### 添加GA

- 找到`themes/next/_config.yml`的`google_analytics`字段，将UA-id填进去

##### 设置搜索

- 安装第三方插件`hexo-generator-searchdb`

```
npm install hexo-generator-searchdb --save
```
- 编辑根目录`_config.yml`文件，在文件末尾添加下面这段代码

```
#搜索
search:
  path: search.xml
  field: post
  content: true
  format: html
```
- 编辑`themes/next/_config.yml`文件，找到并设置`local_search`为`true`

```
# Local search
local_search:
  enable: true
```
##### 压缩静态页面

- 全局加本地安装gulp，先看看`package.json`里面是不是有gulp，有的话先卸载掉

```
npm uninstall -g gulp

npm uninstall --save gulp 

npm install -g gulp
```
- 然后安装gulp和gulp插件

```
npm install --save gulp-minify-css gulp-htmlmin gulp-htmlclean gulp
```
- 然后在根目录新建`gulpfile.js`文件，并添加如下代码，即选择压缩html和css资源

```
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
gulp.task('minify-css', function (done) {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss())
        .pipe(gulp.dest('./public'));
    done();
});
gulp.task('minify-html', function (done) {
    return gulp.src('./public/**/*.html')
        .pipe(htmlclean())
        .pipe(htmlmin({
            removeComments: true,
            minifyJS: true,
            minifyCSS: true,
            minifyURLs: true,
        }))
        .pipe(gulp.dest('./public'));
    done();
});
gulp.task('default', gulp.series(gulp.parallel('minify-html', 'minify-css')), function () {
    console.log("----------gulp Finished----------");
    // Do something after a, b, and c are finished.
});

```
- 最后运行，这个压缩本地看不到效果，要发布才能看到

```
hexo g

gulp

hexo d
```
##### 在hexo里插图片

- 基于vscode的markdown插件 - `Paste Image`
- 在hexo的`source`里新建`images`文件夹，这个就是用来保存所有文件的图片的
- 配置`Paste Image`，设置粘贴图片的保存路径为相对路径`../images`

![保存路径](/images/20191216222122.png)

- 设置插入markdown的路径格式，至于这些字符的含义，看插件介绍页

1. `${imageSyntaxPrefix}`：markdown的格式`![](`
2. `${imageSyntaxSuffix}`：也是markdown的格式`)`
3. `/image/${imageFileName}`：就是绝对路径了，因为这个images文件夹在编译完以后是在根目录的文件夹，所有要用绝对路径

![插入路径](/images/20191216222240.png)

![路径字符含义](/images/20191216222457.png)

- 还可以设置图片的名称

![图片名称](/images/20191216222341.png)