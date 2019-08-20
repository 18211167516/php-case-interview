### 1. 写一个函数，获取一篇文章内容中的全部图片，并下载
```
public function searchImgDownload($url,$download_dir=''){
        //判断url是否正确
        if(!filter_var($url, FILTER_VALIDATE_URL)){
            return false;
        }
        
        if(!$download_dir){
            $download_dir =_CACHE_DIR_.'/download/';
        }

        //读取页面内容
        $html = file_get_contents($url);
        //正则匹配img标签
        preg_match_all('/<img[^>]*src="([^"]*)"[^>]*>/i',$html, $matchs);  
        $images = $matchs[1];
        //判断下载目录是否存在
        if(!is_dir($download_dir)) {
            mkdir($download_dir, 0777, true);
        }
        $allImg = [];
        foreach ($images as $img) {
            $imgName = $download_dir.md5($img).'.png';
            file_put_contents($imgName,file_get_contents($img));
            array_push($allImg, $imgName);
        }
        var_dump($allImg);exit;
    }
```

### 2. 获取当前客户端的IP地址，并判断是否在（1.1.1.1,255.255.255.254)
```
function getip()
{
    $unknown = 'unknown';
    if (isset($_SERVER['HTTP_X_FORWARDED_FOR']) && $_SERVER['HTTP_X_FORWARDED_FOR'] && strcasecmp($_SERVER['HTTP_X_FORWARDED_FOR'], $unknown)) {
        $ip = $_SERVER['HTTP_X_FORWARDED_FOR'];
    } elseif (isset($_SERVER['REMOTE_ADDR']) && $_SERVER['REMOTE_ADDR'] && strcasecmp($_SERVER['REMOTE_ADDR'], $unknown)) {
        $ip = $_SERVER['REMOTE_ADDR'];
    }
    /*
    处理多层代理的情况
    或者使用正则方式：$ip = preg_match("/[\d\.]{7,15}/", $ip, $matches) ? $matches[0] : $unknown;
    */
    if (false !== strpos($ip, ','))
        $ip = reset(explode(',', $ip));
    return $ip;
}

$client_ip = getip();
$client_ip = sprintf('%u', ip2long($client_ip));   //64位系统无压力

/**
 * plan A
 */
$range_min = sprintf('%u', ip2long('1.1.1.1'));
$range_max = sprintf('%u', ip2long('255.255.255.255'));

/**
 * plan B
 */
$range_min = bindec(decbin(ip2long('1.1.1.1')));
$range_max = bindec(decbin(ip2long('255.255.255.255')));


if ($client_ip >= $range_min and $client_ip <= $range_max) {
    echo 'true';
} else {
    echo 'false';
}
```

### 3. 从扑克牌中随机抽5张牌，判断是不是一个顺子,即这5张牌是连续的,JQK用11、12、13表示
```
function eatDuck(array $arr)
{
        $count = count($arr);
        if (count(array_unique($arr)) != $count) {
            return false;//对子
        }
        if (max($arr) - min($arr) != $count - 1) {
            return false;
        }
        return true;
}


function eatDuck(array $arr)
{
        $count = count($arr);
        if (count(array_unique($arr)) != $count) {
            return false;//对子
        }
        if (max($arr) - min($arr) != $count - 1) {
            return false;
        }
        return true;
}

var_dump(eatDuck([1, 3, 5, 2, 4]));
var_dump(eatDuck([10, 13, 11, 12, 14]));
var_dump(eatDuck([1, 3, 5, 7, 9]));

```
### 4. 什么是CSRF攻击？XSS攻击？如何防范？
```
CSRF:https://baike.baidu.com/item/CSRF/2735433
防范方式: CSRF TOKEN, 即提交表单时同时提交一段由服务端渲染表单时生成的token,通过校验token来防范csrf攻击

XSS:https://baike.baidu.com/item/xss/917356
简单来说,XSS就是正常页面执行了用户或黑客提交的前端代码,比如你用了eval('这里执行了用户提交的代码'),
或者你的页面正常解析了用户提交的html代码,如用户提交的个人信息是:<img src="广告连接"><script>window.href="恶意网站连接"</script>,
而你不加过滤转义就入库,然后页面正常解析html代码,最终用户访问这个页面就会跳转到恶意网站 ,这就是XSS
防范方式: 过滤&&转义用户输入(如htmlentities、htmlspecialchars),永久不要信任客户端
```
### 5. mysql随机取10条数据
```
select * from user where rand() limit 10;

```
### 6. http与https的主要区别
```
1、https协议需要到CA申请证书，一般免费证书较少，因而需要一定费用。

2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl/tls加密传输协议。

3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

4、http的连接很简单，是无状态的；HTTPS协议是由SSL/TLS+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

```

### 7. http状态码及其含意
```
200 成功
201 请求成功并且服务器创建了新的资源。
301 永久重定向
302 临时重定向
400 错误请求
403 服务器拒绝请求
404 文件未找到
500 服务器内部错误
502 网关错误
503 服务不可用
504 网关超时
```
### 8. linux中怎么查看系统资源占用情况,同时会问到怎么查看TCP端口,TCP连接状态
```
top         //可能会问得更深,比如显示出来有哪些信息、你关心哪些信息、查看某个进程等
iostat      //磁盘cpu
free        //内存剩余
df          //磁盘使用情况
du          //文件占用信息

ps              //查看进程信息
netstat -anptol //查看端口占用情况,参数细节建议查文档,小心被问倒
```
### 9. SQL注入的原理是什么？如何防止SQL注入
```
通常都是低级程序员写的低级代码,未过滤用户输入导致的,现代框架的ORM一般都做过相应处理,如果需要自己处理,有两种解决方式:
1:转义用户输入(htmlentities/htmlspecialchars),用mysql_real_escape_string方法过滤SQL语句的参数
2:预编译sql    (最佳方式)
```
### 10. mysql 主键和唯一索引的区别
```
主键是一种约束，唯一索引是一种索引，两者在本质上是不同的
主键创建后一定包含一个唯一性索引，唯一性索引并不一定就是主键
唯一性索引列允许空值，而主键列不允许为空值
主键列在创建时，已经默认为空值 + 唯一索引了
主键可以被其他表引用为外键，而唯一索引不能
一个表最多只能创建一个主键，但可以创建多个唯一索引

https://blog.csdn.net/baoqiangwang/article/details/4832814
```
### 11. mysql 数据库中的事务是什么？
```
事务的特征：ACID
原子性Atomicity 一组DML语句要么全部成功要么全部失败
一致性Consistency 事务必须由一个状态到另一个状态
隔离性Isolation 多个事务之间能够根据事务的隔离级别表现不同
持久性Durability 提交后的事务，一旦提交，它对数据库中的数据修改是永久性的

Q:当没有事务的情况会出现什么问题?
A:当在控制台，操作mysql数据库时候，如果没有事务控制，误操作就会造成数据的永久损失。
事务的隔离级别:
```
事务的隔离级别:

|隔离级别 | 脏读| 不可重复读 | 幻读 | 加锁读|
|---|:--:|---:|--:|--:|
|读未提交(Read uncommitted)|o|o|o|不加锁|
|读已提交(Read committed)|x|o|o|不加锁|
|可重复读(Repeatable read)|x|x|o(mysql不会出现 x)|不加锁|
|可串行读(Serializable)|x|x|x|加锁 (全表锁)|


```
脏读：当某个客户端查询出了另外一个事务还没有提交的修改数据，即为脏读。
不可重复读：[同一查询]在[同一事务]中多次进行，由其它提交事务所做的修改或删除，每次返回不同的的结果集，此时发生非重复读。
幻读：[同一查询]在[同一事务]中多次进行，由于其它提交事务(事务可能没提交)所做的插入操作，每次返回不同的结果集，此时发生幻读。
```
### 12. 123123
```
234234
```
