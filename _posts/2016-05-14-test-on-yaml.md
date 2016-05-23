---
layout: post
title: 测试：点击加载图片
tags: jekyll yaml html javascript
categories: DocTool

Pic01:
    file:  WAlogo.jpg
    fn:    AA
    title: The WHITE ALBUM

ImageOnClick: |
      <html>
        <head>
          <script type="text/javascript">
            function !FN!(){
              document.getElementById('imgBanner!FN!').src = '!PTH!/!FILE!';
            }
          </script>
        </head>
        <body> 
          <div id="image" onClick="!FN!()">点击加载图片<img id="imgBanner!FN!" title="!TITLE!"/> </div> 
        </body>
      </html>

---

### 样例<sample>

<html>
<head>
<script type="text/javascript">
function aa(){
    document.getElementById('imgBanner1').src = '{{'/WAlogo.jpg' | prepend: site.imgrepo }}';
    }
  </script>
</head>
<body> 
<div id="image" onClick="aa()">点击加载图片<img id="imgBanner1" title="The WHITE ALBUM"/> </div> 
</body>
</html>

### 代码<code>

感谢网友[qq1111qq111111](http://zhidao.baidu.com/link?url=pxj3YvEDnzLTgeBX7uy6rRKLu75cy1pBiHgjL3nYCp7kYyl0BsyLMRGCQJrL2Fmby5A2OVlJAP0vCfmrObA9NiYWV-jH0yT0aGFHAZU-pQ_){:target="_blank"}

```javascript
<html>
  <head>
    <script type="text/javascript">
      function aa(){
        document.getElementById('imgBanner1').src = '{{'/WAlogo.jpg' | prepend: site.imgrepo }}';
      }
    </script>
  </head>
  <body> 
    <div id="image" onClick="aa()">点击加载图片<img id="imgBanner1" title="The WHITE ALBUM"/> </div> 
  </body>
</html>
```
注：在这个jekyll生成的静态博客中，一些变量已经被替换。

### 改进<improvement>

如果我们需要在一个页面中插入多个“点击加载图片”，就必须为每个图片指定一个`aa`、一个`imgBanner1`和一个`title`。

首先考虑这样的方案：在文档头部定义变量，并在代码中引用，即在头部添加

```yaml
Pic01:
    file:  WAlogo.jpg
    fn:    AA
    title: The WHITE ALBUM
```
则可以通过下面的代码来实现功能

```html
<html>
  <head>
    <script type="text/javascript">
      function \{\{page.Pic01.fn\}\}(){
        document.getElementById('imgBanner\{\{page.Pic01.fn\}\}').src = '\{\{'/' | prepend: site.imgrepo \}\}\{\{page.Pic01.file\}\}';
      }
    </script>
  </head>
  <body> 
    <div id="image" onClick="\{\{page.Pic01.fn\}\}()">点击加载图片<img id="imgBanner\{\{page.Pic01.fn\}\}" title="\{\{page.Pic01.title\}\}"/> </div> 
  </body>
</html>
```
示例：
<html>
  <head>
    <script type="text/javascript">
      function {{page.Pic01.fn}}(){
        document.getElementById('imgBanner{{page.Pic01.fn}}').src = '{{'/' | prepend: site.imgrepo }}{{page.Pic01.file}}';
      }
    </script>
  </head>
  <body> 
    <div id="image" onClick="{{page.Pic01.fn}}()">点击加载图片<img id="imgBanner{{page.Pic01.fn}}" title="{{page.Pic01.title}}"/> </div> 
  </body>
</html>
但是接下来就需要依次替换代码中的`Pic01`。为了让这个过程更加方便，需要定义模板。yaml的`anchor`很难实现模板定义，因为它在解析时是静态的、被一次赋值之后固定下来的。但我们可以定义一个模板，再借用`replace`，即定义出

```yaml
ImageOnClick: |
      <html>
        <head>
          <script type="text/javascript">
            function !FN!(){
              document.getElementById('imgBanner!FN!').src = '!PTH!/!FILE!';
            }
          </script>
        </head>
        <body> 
          <div id="image" onClick="!FN!()">点击加载图片<img id="imgBanner!FN!" title="!TITLE!"/> </div> 
        </body>
      </html>
```
而插入图片时使用下面的命令

```yaml
\{\{ page.ImageOnClick
| replace: '!FN!', 'ABAB'
| replace: '!FILE!', 'WAlogo.jpg'
| replace: '!TITLE!', 'The WHITE ALBUM'
| replace: '!CONTENT!', '点击加载图片'
| replace: '!PTH!': site.imgrepo \}\}
```
虽然代码不够轻量，但是已经足够实用了。

最后将`page`变量`ImageOnClick`搬到`_config.yml`下，使之成为`site`变量，收工。
{{ site.ImageOnClick
| replace: '!FN!', 'ABAB'
| replace: '!FILE!', 'WAlogo.jpg'
| replace: '!TITLE!', 'The WHITE ALBUM'
| replace: '!CONTENT!', '点击加载图片'
| replace: '!PTH!': site.imgrepo }}