---
layout: post
title: 我的Sublime Text 3 - 适用于Web开发
---


推荐大家使用版本3，这个版本的速度快了很多，尤其是第一次打开的时间少了很多，可以说是秒开。

这里整理了我安装的一些插件，和自定义配置(主题，配色等)，适用于码农，尤其是Web方向。

> Tips: 在Linux, Windows命令行下， 都可以使用 subl 命令打开了, ```subl .```用于打开当前文件夹

截个图看看：
![sublime_text](/images/post/2014-10-01-sublime_text_prtsc1.png)

![sublime_text](/images/post/2014-10-01-sublime_text_prtsc2.png)

## 插件

### Package Control

移步 [Package Control Installation](https://sublime.wbond.net/installation#st3)

### ConvertToUTF8

如果直接有Sublime Text创建的文本包含中文，显示应该没有问题，但是，如果是先前由其他工具，或者别人发过来的中文文本，就会乱码了，这时候就要使用 ConvertToUTF8 这个插件，我们安装即可，他会自动转换的。

### Alignment

用于对齐，比如一些变量赋值语句，等号对齐。
Usage: 
- 选择要对齐的代码
- 使用快捷键：`ctrl+alt+a`

### BracketHighLighter

用于高亮显示匹配的括号或者代码段

### Emmet

用于快捷生成一些html代码
Usage:
- 输入 div>ul>li*4
- 按下 Tab 键

### ERB Snippets

大致同Emmet, 不过是 .html.erb

> Snippets and Bindings

<table>
  <tr>
    <th>Snippet</th>
    <th>Tab Trigger</th>
    <th>Output</th>
  </tr>
  <tr>
    <td>ERB tags</td>
    <td>__er__</td>
    <td>`<%  %>`</td>
  </tr>
  <tr>
    <td>print ERB tags</td>
    <td>__pe__</td>
    <td>`<%=  %>`</td>
  </tr>
  <tr>
    <td>print ERB comment</td>
    <td>__pc__</td>
    <td>`<%#  %>`</td>
  </tr>
  <tr>
    <td>`if` block</td>
    <td>__if__</td>
    <td>`<% if  %>...<% end %>`</td>
  </tr>
  <tr>
    <td>`if` / `else` block</td>
    <td>__ife__</td>
    <td>`<% if  %>...<% else %>...<% end %>`</td>
  </tr>
  <tr>
    <td>`else` tag</td>
    <td>__else__</td>
    <td>`<% else %>`</td>
  </tr>
  <tr>
    <td>`elsif` tag</td>
    <td>__elsif__</td>
    <td>`<% elsif %>`</td>
  </tr>
  <tr>
    <td>`unless` block</td>
    <td>__unless__</td>
    <td>`<% unless  %>...<% end %>`</td>
  </tr>
  <tr>
    <td>`end` block</td>
    <td>__end__</td>
    <td>`<% end %>`</td>
  </tr>
  <tr>
    <td>`submit_tag` helper</td>
    <td>__st__</td>
    <td>`<%= submit_tag ..., ... %>`</td>
  </tr>
  <tr>
    <td>`text_field_tag` helper</td>
    <td>__tft__</td>
    <td>`<%= text_field_tag ..., ... %>`</td>
  </tr>
  <tr>
    <td>`password_field_tag` helper</td>
    <td>__pft__</td>
    <td>`<%= password_field_tag ..., ... %>`</td>
  </tr>
  <tr>
    <td>`label_tag` helper</td>
    <td>__lblt__</td>
    <td>`<%= label_tag ..., ... %>`</td>
  </tr>
  <tr>
    <td>`link_to` helper</td>
    <td>__lt__</td>
    <td>`<%= link_to ..., ... %>`</td>
  </tr>
  <tr>
    <td>`form_for` helper</td>
    <td>__form__</td>
    <td>`<%= form_for(@ ) do |f| %> ... <% end %>`</td>
  </tr>
  <tr>
    <td>`t()` helper</td>
    <td>__t__</td>
    <td>`<%= t('@') %>`</td>
  </tr>
<table>


### Markdown Preview

在浏览器浏览Markdown
Usage:
- 快捷键`ctrl+shift+p`
- 输入: Markdown Preview
- 根据提示选择命令

### MarkdownEditing

### Theme-Soda

一款不错的主题，另外，两个配色也不错

- Home: https://github.com/buymeasoda/soda-theme
- 配色：http://buymeasoda.github.com/soda-theme/extras/colour-schemes.zip

安装配色方案方法：
- 解压到 C:\Users\ahnniu@work\AppData\Roaming\Sublime Text 3\Packages\User\, Ubuntu 在用户路径下， .sublime text 3

## 快捷键

```
[
  { "keys": ["ctrl+alt+r"], "command": "reindent" },
]
```

## 自定义配置

```
{
  "color_scheme": "Packages/User/Espresso Soda.tmTheme",
  "font_size": 11,
  "ignored_packages":
  [
    "Markdown",
    "Vintage"
  ],
  "soda_classic_tabs": true,
  "tab_size": 2,
  "theme": "Soda Light 3.sublime-theme",
  "translate_tabs_to_spaces": true
}
```
