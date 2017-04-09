---
layout: post
title:  "Discuz x3 越权操作"
date:   2017-03-16 11:04:55
comments: true
categories: php bug
---

##Discuz x3 越权操作

这个漏洞是朋友通过黑盒测试发现， 然后我写了个补丁 。
此时尚未修复就不透露细节， 看的懂的人自己会用。

我通过搜索找到了js 文件 然后下断点观察
![rock](https://striker.teambition.net/thumbnail/110q077cc7e9bde9173f3bf22af1e621bd32/w/800/h/574)


此处直接传入用户的值， 没有校验该贴是否属于当前用户导致 越权产生
```javascript

foreach($topiclist as $pid) {
	$post = C::t('forum_post')->fetch('tid:'.$_G['tid'], $pid, false);
	$sticktopiclist[$pid] = $post['position'];
}


后面进行操作
.........



```



我自己写的修复方法 ，官方此漏洞暂未修复。

// 通过全局函数获取当前用户的帖子， 当post的值（帖子）属于当前用户才能进行操作。

```javascript
foreach($topiclist as $pid) {
	$post = C::t('forum_post')->fetch('tid:'.$_G['tid'], $pid, false);  
	if($post['tid']==$_G['tid']){
		$sticktopiclist[$pid] = $post['position'];
		}else{
			die('null');
		}
	

	

}

```

