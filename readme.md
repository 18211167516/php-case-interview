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
