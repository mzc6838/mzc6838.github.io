---
title: markdown折叠代码块的方法
date: 2020-02-19 21:22:47
tags:
- markdown
categories:
- 博客建设
---
# 缘起
&emsp;刚才在迁移远古博客的时候有一段特别特别长的代码段，如果单独放在前端里面显示估计也没几个人看得下去，所以就去查了一下怎么能吧那段代码 既保留 又折叠
&emsp;然后就有了下面的方法。
<!--more-->
# 方法
一般在插入代码段时使用三个`号（esc键下面的符号）
在这里要折叠代码段使用如下方法(我是格式警犬不要怪我占篇幅)：
```html
<details>
    <summary>
        <mark>
            <font color=darkred>点击查看详细内容</font>
        </mark>
    </summary>
  <p> - 测试 测试测试</p>
    <pre>
        <code>  
for i in a:
    print(i)
        </code>
    </pre>
</details>
```
summary：折叠语法展示的摘要

details：折叠语法标签

pre：以原有格式显示元素内的文字是已经格式化的文本。

blockcode：表示程序的代码块。

code：指定代码范例。

经过测试，可以直接在各标签中指定元素的style（即变色，字体大小什么的）
# 效果
<details>
    <summary>
        <mark>
            <font color=darkred>点击查看详细内容</font>
        </mark>
    </summary>
  <p> - 测试 测试测试</p>
    <pre>
        <code>  
for i in a:
    print(i)
        </code>
    </pre>
</details>
<br><br>

>资料来源：[MarkDown折叠语法](https://www.cnblogs.com/byho/p/10570145.html)