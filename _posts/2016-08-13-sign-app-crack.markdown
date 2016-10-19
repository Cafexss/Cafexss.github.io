#  签名破解入门实例

------

1.
    **硬编码带来的安全问题**
下载了某app ， 抓包找漏洞， 发现重置密码处修改信息就会报错非法请求。 我想应该存在签名。
 Markdown：





![cmd-markdown-logo](https://striker.teambition.net/thumbnail/110l4b531ca37dbde90853d02d0ae733adb1/w/800/h/282)

> * 校验参数 summary


反编译app  搜索字符串 summary

这里代码没有经过混淆 ，一目了然。

     String v4 = "glsapp2014" + phoneNumber + "phone" + validationCode + v0;
一个硬编码加当前手机号加phone+验证码+当前系统时间

    glsapp201417091604326phone8888820160602105949
然后把他经过md5加密



     642958c7e1f7af9b222754865a95fd04


![jeb][1]


  2.
  **知道了上面的一切我们只需要使用Python生成，相对于的签名md5就可以了。**

  然后跑起来就无压力了 ， 5位数md5破解起来 也就十万个组合， 无压力



 ![burp][2]



  [1]: https://striker.teambition.net/thumbnail/110k4d7ecf6631c9004757bf9cca3de79275/w/800/h/427
  [2]: https://striker.teambition.net/thumbnail/110kb8dca3c47ce19d5043bd27c36e172788/w/800/h/400
