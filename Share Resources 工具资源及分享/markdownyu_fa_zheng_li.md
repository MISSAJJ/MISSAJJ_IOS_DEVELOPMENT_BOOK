# Markdown语法整理
```objc
Update更新：2016年5月22日 By {MISSAJJ琴瑟静听}
 
```

###MISSAJJ为什么要特意写Markdown？ 
GitBook就是基于markdown语言编辑的网站，github的README更是支持了markdown语法，除此之外还有Mou编辑器也是基于markdown的编辑工具，中国的简书博客类网站，也是支持markdown语言编辑的网站。
 
目前已经有很多开发者在使用Markdown语言写开发文档，文章博客，说明书等，此语言格式非常轻便简单，MISSAJJ编写这本IOS开发电子书也是使用的markdown语言，所以特地增加了这一章节汇总Markdown语法。

（图片加载中， 请耐心等待……）

![image](MISSAJJ-GitBook.png)


###使用 Markdown 的优点
```objc
    专注你的文字内容而不是排版样式。
    轻松的导出 HTML、PDF 和本身的 .md 文件。
    纯文本内容，兼容所有的文本编辑器与字处理软件。
    可读，直观。适合所有人的写作语言。
```

###Markdown语言简介
 
Markdown的创始人是John Gruber，此语言是一种轻量级的「标记语言」，它的优点很多，目前也被越来越多的写作爱好者，撰稿者广泛使用。看到这里请不要被「标记」、「语言」所迷惑，Markdown 的语法十分简单。常用的标记符号也不超过十个，这种相对于更为复杂的HTML 标记语言来说，Markdown 可谓是十分轻量的，学习成本也不需要太多，且一旦熟悉这种语法规则，会有一劳永逸的效果。
 

- 创始人 John Gruber 的 Markdown 语法说明
 - http://daringfireball.net/projects/markdown/syntax
- Markdown 中文版语法说明
  - http://wowubuntu.com/markdown/#list

###支持Markdown语言编辑的网站

* Gitbook 网址： https://www.gitbook.com
* Github 网址： https://www.github.com
* Mou  网址： http://25.io/mou
* 简书 网址： http://www.jianshu.com



###相关Markdown语法的文献
* 认识与入门 Markdown（MISSAJJ推荐阅读）
 * http://sspai.com/25137
* 简书:献给写作者的Markdown新手指南（MISSAJJ推荐阅读）
  * http://www.jianshu.com/p/q81RER
* Markdown 简明语法
  * http://lutaf.com/markdown-simple-usage.htm
* Markdown之表格的处理
  * http://www.ituring.com.cn/article/3452
* Cmd Markdown 编辑阅读器
  * http://www.zybuluo.com/mdeditor
* Markdown语法说明（详解版）
  * http://www.ituring.com.cn/article/504
* 怎样使用Markdown
  * http://www.ituring.com.cn/article/23
* Markdown写作浅谈
  * http://jianshu.io/p/PpDNMG
* Markdown语法示例
  * http://equation85.github.io/blog/markdown-examples
* HTML转义字符对照表
  * http://tool.oschina.net/commons?type=2
* Markdown 语法说明(简体中文版)
  * http://wowubuntu.com/markdown


###Mou编辑器的语法 

Mou是一个帮助开发者用Markdown语言编辑的编辑器，下载网址：http://25.io/mou/ 
除了这个工具，该网站还有文本转换语音Toau工具，和网页压缩的工具smaller，有需要的童鞋们可以下载体验。

（图片加载中， 请耐心等待……）

![image](mou.png)

以下是打开Mou应用默认的部分语法帮助内容，部分作了中文标注和提示，整理至此便于童鞋们查阅。

![Mou icon](Mou_128.png)

## Overview

**Mou**, the missing Markdown editor for *web developers*.

### Syntax

#### Strong and Emphasize 加粗和强调
```
**strong** or __strong__ 
```
**strong** or __strong__ ( Cmd + B )

```
*emphasize* or _emphasize_ 
```
 
*emphasize* or _emphasize_ ( Cmd + I )

**Sometimes I want a lot of text to be bold.
Like, seriously, a _LOT_ of text**

#### Blockquotes
```
> Right angle brackets &gt; are used for block quotes.
```
> Right angle brackets &gt; are used for block quotes.

#### Links and Email  链接和电子邮件
```
<example@example.com>
```
An email <example@example.com> link.

Simple inline link <http://chenluois.com>, another inline link [Smaller](http://25.io/smaller/), one more inline link with title [Resize](http://resizesafari.com "a Safari extension").
```   
[Resize](http://resizesafari.com "a Safari extension")

[Smaller](http://25.io/smaller/)
```
A [reference style][id] link. Input id, then anywhere in the doc, define the link with corresponding id:

[id]: http://25.io/mou/ "Markdown editor on Mac OS X"

```
A [reference style][id] link. Input id, then anywhere in the doc, define the link with corresponding id:

[id]: http://25.io/mou/ "Markdown editor on Mac OS X"

```
Titles ( or called tool tips ) in the links are optional.

#### Images

An inline image ![Smaller icon](http://25.io/smaller/favicon.ico "Title here"), title is optional.

```
![Smaller icon](http://25.io/smaller/favicon.ico "Title here")
```
A ![Resize icon][2] reference style image.

[2]: http://resizesafari.com/favicon.ico "Title"

```
A ![Resize icon][2] reference style image.

[2]: http://resizesafari.com/favicon.ico "Title"

```
#### Inline code and Block code

Inline code are surround by `backtick` key. To create a block code:
```
`backtick`

```

Indent each line by at least 1 tab, or 4 spaces.
    var Mou = exactlyTheAppIwant; 
    


####  Ordered Lists

Ordered lists are created using "1." + Space:
```
1. Ordered list item
2. Ordered list item
3. Ordered list item
```
1. Ordered list item
2. Ordered list item
3. Ordered list item

#### Unordered Lists

Unordered list are created using "*" + Space:

```
* Unordered list item
* Unordered list item
* Unordered list item 
```
* Unordered list item
* Unordered list item
* Unordered list item 

Or using "-" + Space:
```
- Unordered list item
- Unordered list item
- Unordered list item
```
- Unordered list item
- Unordered list item
- Unordered list item

#### Hard Linebreak

End a line with two or more spaces will create a hard linebreak, called `<br />` in HTML. ( Control + Return )  
Above line ended with 2 spaces.

#### Horizontal Rules 水平线

Three or more asterisks or dashes:
```
***
```
***
```
---
```
---
```
- - - -
```
- - - -

#### Headers

Setext-style:

This is H1
==========

This is H2
----------
```
This is H1
==========

This is H2
----------
```
atx-style:

# This is H1
## This is H2
### This is H3
#### This is H4
##### This is H5
###### This is H6

```
# This is H1
## This is H2
### This is H3
#### This is H4
##### This is H5
###### This is H6
```
### Extra Syntax

#### Footnotes

Footnotes work mostly like reference-style links. A footnote is made of two things: a marker in the text that will become a superscript number; a footnote definition that will be placed in a list of footnotes at the end of the document. A footnote looks like this:

That's some text with a footnote.[^1]

[^1]: And that's the footnote.


#### Strikethrough

Wrap with 2 tilde characters:

~~Strikethrough~~


#### Fenced Code Blocks

Start with a line containing 3 or more backticks, and ends with the first line with the same number of backticks:

```
Fenced code blocks are like Stardard Markdown’s regular code
blocks, except that they’re not indented and instead rely on
a start and end fence lines to delimit the code block.
```

#### Tables表格

A simple table looks like this:

#####（MISSAJJ推荐用这种:比较方便）
```
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```
演示：

First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell

If you wish, you can add a leading and tailing pipe to each line of the table:

##### 
```
| First Header | Second Header | Third Header |
| ------------ | ------------- | ------------ |
| Content Cell | Content Cell  | Content Cell |
| Content Cell | Content Cell  | Content Cell |
```
演示： 

| First Header | Second Header | Third Header |
| ------------ | ------------- | ------------ |
| Content Cell | Content Cell  | Content Cell |
| Content Cell | Content Cell  | Content Cell |

Specify alignment for each column by adding colons to separator lines:
````
First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right
````

演示：

First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right


 