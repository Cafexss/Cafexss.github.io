---
layout: post
title:  "网站登录密码前端加密破解"
date:   2017-03-13 16:04:55
comments: true
catego


##网站登录密码前端加密破解

某网站登陆前端将密码进行了加密

我通过搜索找到了js 文件 然后下断点观察
![rock](https://striker.teambition.net/thumbnail/110q077cc7e9bde9173f3bf22af1e621bd32/w/800/h/574)


找到关键函数， 此函数调用了hex_sha1 方法对密码进行了加密处理
```javascript
fdLogin.login = function (uName, pwd, status, ckCallback) {
    pwd = hex_sha1(pwd);
    pwd = hex_sha1(uName + pwd);
	pwd = hex_sha1(pwd + this.vccode);

```




我继续跟踪 hex_sha1 ，发现此函数调用了多个函数加密函数进行加密，
想还原起来比较麻烦，不如直接调用个函数。
```javascript
function hex_sha1(s){return binb2hex(core_sha1(str2binb(s),s.length * chrsz));}
```

只要输入用户名+密码+验证码就能加密
要注意的是这里还调用了验证码
```javascript
pwd = hex_sha1(pwd + this.vccode);
```

不过我可以通过callback回调来获取他验证码的值。


#最终不懂js的我构造了个poc
https://github.com/Cafexss/tool/blob/master/jsencode.txt



#效果



![mahua](https://striker.teambition.net/thumbnail/110q056b6d9f839b6b9b36dc5d50219a0a77/w/800/h/438)
