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
