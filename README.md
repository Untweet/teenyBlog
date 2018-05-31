# teenyBlog

teenyBlog可能是全球第二小的博客系统，是的，__它只有一个页面__。
<br>
本项目依赖 [语雀](https://yuque.com) 提供后台管理和API。页面渲染采用 vue，获取数据使用 axios。

__演示：__[https://luan.ma](https://luan.ma)


---

## 安装方法
1. 在 [语雀](http://yuque.com) 创建知识库，并设置权限为公开，这里举例知识库的url最后为 /user/repo 
2. 搜索 index.html中的  `v-else :href=`  修改为自己的知识库地址
3. 搜索 index.html 中  `axios.get(` 地址改为 `https://yuque.com/api/v2/repos/user/repo/toc`  的自行代理地址。
很遗憾，语雀API并不提供跨域支持，所以您必须对该API进行代理后才能使用。也许日后会放开这个限制。
我这里给出一个PHP的代理源码：
```php
<?php
//memcache缓存，加快访问速度，带token每小时可以被访问5000次，也可以不带缓存
$mmc = memcache_init();
$res = $mmc->get("yuque_cache");
if(empty($res)){
  $res = getData();
    $mmc->set('yuque_cache', $res, time() + 60); //60s
}
header("Access-Control-Allow-Origin: *"); // 开放跨域，您也可以修改为您的指定域名
header("Content-type: application/json; charset=utf-8");
echo $res;


function getData(){
    $ch = curl_init(); 
    curl_setopt($ch,CURLOPT_URL,'https://yuque.com/api/v2/repos/page/blog/toc');
    curl_setopt($ch, CURLOPT_HEADER, 0);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    $header=array(  
    "Content-Type: application/x-www-form-urlencoded",  
    "User-Agent: myProxy", //官方文档要求必须带一个
    "X-Auth-Token: YXDQoZMhHxNw1" //在个人设置页面获取，没有这个的话，一个小时只能请求200次
    );
    curl_setopt($ch,CURLOPT_HTTPHEADER,$header);
    $res = curl_exec($ch);
    return $res;
}
?>
```
4. 修改博客标题、github地址等个性化内容，直接上传到一个静态空间即可。


