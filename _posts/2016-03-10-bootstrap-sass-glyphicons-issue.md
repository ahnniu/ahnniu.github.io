---
layout: post
title: Rails中使用Bootstrap Glyphicons不正常问题探究
---

## 问题描述

### Rails 环境

Ruby用的是2.1.5， Rails是4.1.14.2，sass,sass-rails都是rails new app时候默认安装的。
bootstrap-sass是自己添加到Gemfile中。`gem 'bootstrap-sass', '~> 3.3'`

- Ruby (2.1.5)
- rails (4.1.14.2)
- bootstrap-sass (3.3.5)
- sass (3.2.19)
- sass-rails (4.0.5)

### Bootstrap配置

根据 [bootstrap-sass - github - README](https://github.com/twbs/bootstrap-sass)配置

Step 1:

```console
$ mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss
```

Step 2: Import Bootstrap styles in `app/assets/stylesheets/application.scss`:

```scss
// "bootstrap-sprockets" must be imported before "bootstrap" and "bootstrap/variables"
@import "bootstrap-sprockets";
@import "bootstrap";
```

Step 3: Require Bootstrap Javascripts in `app/assets/javascripts/application.js`:

```js
//= require jquery
//= require bootstrap-sprockets
```

### 问题: Bootstrap glyphicon图标显示不正确


在 `app/views/static_pages/home.html.erb`中测试：

```html
<button type="button" class="btn btn-default" aria-label="Left Align">
  <span class="glyphicon glyphicon-align-left" aria-hidden="true"></span>
</button>

<button type="button" class="btn btn-default btn-lg">
  <span class="glyphicon glyphicon-star" aria-hidden="true"></span> Star
</button>
```

glyphicon 图标显示为方框， button的样式不正常。


## 问题分析

__glyphicon是字符字体， 显示不正确， 那么应该是字体加载不正常__， 都是根据bootstrap-sass官方文档配置，为什么加载不正常？

可能的检查点：

- 没引入字体
    + @font-face
- 引入了，但是路径没找到
    + Rails asset pipline 加载出了问题？

### 检查点 @font-face

前面看过《CSS设计指南》， 在第4章字体中，谈到网页上的字体来源有且仅有三种：

- 用户机器安装的字体，这个用户是浏览网页的用户
    + 像Arial等常见字体，一般电脑是有装的，不管是Windows还是OSX. 很显然，即使是我调试，我也没有安装glyphicon字体， 这个地方排除
- 保存在第3方网站的字体，通过link标签加入，这一般是用的CDN，或者Google上的字体。
    + Bootstrap提供了CDN加速，但是我没用
- 保存在自己服务器上的字体， 通过@font-face 引入
    + 咱就是用的这种方式，而且字体就在bootstrap-sass gem中， 我查看了 gemset下这个gem, 字体就在 `/home/ahnniu/.rvm/gems/ruby-2.1.5@twitter-clone/gems/bootstrap-sass-3.3.5/assets/fonts/bootstrap` 下， 那可能的原因就是没有正确的引入了。

接下来，查看 bootstrap-sass的源码， 看下 @font-face是否有问题


### 阅读 bootstrap-sass关于 @font-face的源码

_bootstrap.scss 文件：

```scss
@import "bootstrap/glyphicons";
```

bootstrap/_glyphicons.scss文件：

```scss
@at-root {
  // Import the fonts
  @font-face {
    font-family: 'Glyphicons Halflings';
    src: url(if($bootstrap-sass-asset-helper, twbs-font-path('#{$icon-font-path}#{$icon-font-name}.eot'), '#{$icon-font-path}#{$icon-font-name}.eot'));
    src: url(if($bootstrap-sass-asset-helper, twbs-font-path('#{$icon-font-path}#{$icon-font-name}.eot?#iefix'), '#{$icon-font-path}#{$icon-font-name}.eot?#iefix')) format('embedded-opentype'),
         url(if($bootstrap-sass-asset-helper, twbs-font-path('#{$icon-font-path}#{$icon-font-name}.woff2'), '#{$icon-font-path}#{$icon-font-name}.woff2')) format('woff2'),
         url(if($bootstrap-sass-asset-helper, twbs-font-path('#{$icon-font-path}#{$icon-font-name}.woff'), '#{$icon-font-path}#{$icon-font-name}.woff')) format('woff'),
         url(if($bootstrap-sass-asset-helper, twbs-font-path('#{$icon-font-path}#{$icon-font-name}.ttf'), '#{$icon-font-path}#{$icon-font-name}.ttf')) format('truetype'),
         url(if($bootstrap-sass-asset-helper, twbs-font-path('#{$icon-font-path}#{$icon-font-name}.svg##{$icon-font-svg-id}'), '#{$icon-font-path}#{$icon-font-name}.svg##{$icon-font-svg-id}')) format('svg');
  }
}
```

找到 @font-face了， 但是@at-root是什么东西？

查找 生成的 application.css中，也找到关于@font-face的地方：

```css
@at-root {
  @font-face {
    font-family: 'Glyphicons Halflings';
    src: url("/assets/bootstrap/glyphicons-halflings-regular.eot");
    src: url("/assets/bootstrap/glyphicons-halflings-regular.eot?#iefix") format("embedded-opentype"), url("/assets/bootstrap/glyphicons-halflings-regular.woff2") format("woff2"), url("/assets/bootstrap/glyphicons-halflings-regular.woff") format("woff"), url("/assets/bootstrap/glyphicons-halflings-regular.ttf") format("truetype"), url("/assets/bootstrap/glyphicons-halflings-regular.svg#glyphicons_halflingsregular") format("svg");
}
}
```

也被套如了@at-root中。


### 调查 @at-root

google @at-root, 发现 @at-root是 sass 3.3的一个新特性， 主要是解决一些不需要嵌套的地方。

这篇博客：[Getting Back To our @at-roots](http://sassbreak.com/getting-back-to-our-roots/) 讲的比较清楚。

我马上看了下自己sass的版本， 发现是 3.2.19, 那么问题就很明显了。

目前版本的sass不支持 @at-root。

> 另外，google bootstrap sass @at-root，直接出来了问题答案：
> 
> - [Needs sass >=3.3.0 in order to use @at-root #920](https://github.com/twbs/bootstrap-sass/issues/920)
> - [glyph-icons are showing up as rectangles #925](https://github.com/twbs/bootstrap-sass/issues/925)


## 解决问题

现在已经发现了是因为sass不支持 @at-root， 导致 @font-face正确的被编译，以下有多种解决办法：

### 方法一 升级sass

升级sass到3.3以上的版本， 通过修改Gemfile, `bundle update`.

```ruby
gem 'bootstrap-sass', '~> 3.3'
gem 'sass', '~> 3.3'
# Use SCSS for stylesheets
gem 'sass-rails', '~> 5.0.0'
```


这种方法最直接， 也推荐。

### 方法二 添加@font-face

不管怎么说，问题的根本是没有加入Glyphicons @font-face的字体包定义， 我们加入即可。

在application.scss中加入：

```css
@import "bootstrap-sprockets";
@import "bootstrap";

@font-face{
  font-family:'Glyphicons Halflings';
  src: url("bootstrap/glyphicons-halflings-regular.eot");
  src: url("bootstrap/glyphicons-halflings-regular.eot?#iefix") format("embedded-opentype"),
       url("bootstrap/glyphicons-halflings-regular.woff") format("woff"),
       url("bootstrap/glyphicons-halflings-regular.ttf") format("truetype"),
       url("bootstrap/glyphicons-halflings-regular.svg#glyphicons_halflingsregular") format("svg")
}
```

因为是对 bootstrap的hack, 所以，建议放到一个单独的文件， 比如bootstrap-override.sass,那么：

bootstrap-override.sass:

```sass
@font-face{
  font-family:'Glyphicons Halflings';
  src: url("bootstrap/glyphicons-halflings-regular.eot");
  src: url("bootstrap/glyphicons-halflings-regular.eot?#iefix") format("embedded-opentype"),
       url("bootstrap/glyphicons-halflings-regular.woff") format("woff"),
       url("bootstrap/glyphicons-halflings-regular.ttf") format("truetype"),
       url("bootstrap/glyphicons-halflings-regular.svg#glyphicons_halflingsregular") format("svg")
}
```

application.scss:

```sass
@import "bootstrap-sprockets";
@import "bootstrap";
@import "bootstrap-override"

```

### 方法三 使用CDN第三方提供的字体

可以用第三方CDN提供的字体，无需放到自己的服务器上。而且速度也比较快。可根据需要，选择国内的，还是国外的CDN. 如果是国外的CDN,也要考虑是不是被墙的因素， 比如比建议在国内网站引入google上面的字体。

application.scss:

```sass
// 如果仅考虑解决字体，可仅仅导入glyphicons.css部分，也可把bootstrap整体切换到CDN.
@import url("//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap-glyphicons.css");

```


## 插曲 - 中间走过的弯路

解决问题，就跟破案一样，根据已知条件，一点一点的推断。这里的线索是：

1. glyphicon图标显示不了 
2. glyphicon图标其实是字符字体，本质是字体 
3. 网页加载字体的三种方法 
4. 通过@font-face打包字体放到自己的服务器上
5. 看生成的css文件中有没有@font-face
6. @font-face是否有问题，比如路径对不对，或者引用的font是否存在
7. bootstrap为什么没有正确生成 @font-face
8. 解决它

当然，这也依赖是否有较为巩固的基础，比如关于网页中字体的三种方式。

在现实生活中，往往我们太依赖google, stackoverflow, 往往忽略了自己从头一点一点的去解决。就像这里的问题，自己解决下来，也花不了太长的时间。即使通过google解决了问题，有时候可能也不清楚为什么。


> 我个人喜欢做笔记，或者说做wiki, 遇到这样的问题，我都会当做tips或者经验记录下来。关于这个问题，我最早是在2016/01月份，跟着《Rails Tutorial》敲代码时候，就遇到了。如今，目前是2016/03,我打算开发一个twitter clone的项目，来学习rails, 这是第二次。正因为，第一次是知其然而不知其所依然， 今天就来彻底的解决问题，才有了今天这个博文。
> 


说说我中间走的弯路：

### 弯路一 利用google,搜索 bootstrap glyphicon图标错误相关的

网上，当然有很多资源，同样，遇到这个问题，也绝对不仅仅是我一个人，当然，搜索出来了很多。给一些参考文档，提取出来几个有用的：

- [stackoverflow - Some Glyphicons not working with boostrap-sass](http://stackoverflow.com/questions/31468298/some-glyphicons-not-working-with-boostrap-sass)
- [stackoverflow - Bootstrap 3 Glyphicons are not working](http://stackoverflow.com/questions/18369036/bootstrap-3-glyphicons-are-not-working)
- [blog - Using Bootstrap 3 with Rails 4](http://rvg.me/2013/11/using-bootstrap-3-with-rails-4/)
- [Integrating Twitter Bootstrap 3.3 with Rails 4.2](https://www.rubyplus.com/articles/1371)

当然，最后是工作了，图标出来了，但是还没有很清楚为什么按照bootstrap-sass官方网站上做，不正确，权当是它的一个bug了。

### 弯路二 潜意识中认为肯定是路径没有引用对

glyphicon font是在 bootstrap-sass gem中的，可能问题是：
- glyphicon font没有copy到 当前的rails项目中，或者copy不对。

顺着这个思路，我了解到了Rails中的asset pipline

#### 尝试一 assets:prcompile

```console
# 把bootstrap的一些图片，fonts从bootstrap-sass copy到 public/assets下
$ rake assets:precompile
```

通过这个方法，是把对应的东西copy过来了， 但是，还没有解决。

#### 尝试二 更深入的学习asset pipline

看过 Rails Guide上面关于asset pipline的介绍，比较浅显，于是，有看了下 《The Rails 4 Way》第20章， 就是详细介绍 asset pipline的。

从书上，对asset pipline有了较为深入的理解：

关于搜索路径Rails.application.assets.path, 除了 app/assets, lib/assets外，gem里如果有 assets， 会被自己添加到搜索路径中，所以，除了生产环境，copy不copy gem下的，是没关系的。

这条路，这是证明了不关 font路径的问题。


## 结论

做软件开发跟打怪，跟警察断案，其实都差不多，关键在于解决问题的方法， 根据线索，一点一点的去找到根本，然后再结合知识点去解决它。

另外，一方面，也需要不断的去修内功，其实基础性的东西更重要。

平时，google还需要用，但是不能依赖google, 多思考，多按照断案的思维来。

