---
layout: post
title: "PHP按比例缩放图"
subtitle: ""
category: PHP
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [PHP]
---

upload.php文件内容

    $uid = 3;
    $time = time();
    // 图片目录
    $img_dir = "upload/";
    // 把图片传到服务器
    // 初始化变量
    $uploaded = 0;
    $unuploaded = 0;
    $imgname = $uid.$time;
    //只允许三张图片上传
    for ($i=0; $i<=2; $i++) {
        //获取当前图片的信息
        $is_file = $_FILES['imgfile']['name'][$i];
        //如果当前图片不为空
        if (!empty($is_file)) {
            // 判断上传的图片的类型是不是jpg,gif,png,bmp中的一种，同时判断是否上传成功
            $imgtypearr = array('image/jpeg','image/gif','image/png','image/bmp');
            $imgtype = $_FILES['imgfile']['type'][$i];
            if (in_array($imgtype,$imgtypearr)) {
                move_uploaded_file($_FILES['imgfile']['tmp_name'][$i], $img_dir.$imgname.$is_file);
                //按比例缩放图片
                if(resizeImage($img_dir.$imgname.$is_file,330,380,$img_dir.$imgname.$is_file)){
                    echo $is_file."缩小图片成功！";
                }else{
                    echo $is_file."上传图片失败！";
                }
            }else{
                echo $is_file.'图片格式不对！';
            }
        }
    }
    
    /**
     * 按比例缩小图片
     *
     * @param string $im 源文件
     * @param int $maxwidth 规定图片宽 324px
     * @param int $maxhidth 规定图片高 343px
   	 * @param int $maxwidth 输出文件
     *
     * 返回文件
     *
     */
    function resizeImage($im,$maxwidth,$maxheight,$to)
    {
        // 载入图像到php到变量中
        $suffix = substr(strrchr($im,'.'),1); //strrchr返回最后的.jpg 或者.jpeg
        $style = strtolower($suffix);
        if($style=='jpg' || $style=='jpeg'){
            $originail = imagecreatefromjpeg($im); //jpg
        }elseif($style=='png'){
            $originail = imagecreatefrompng($im); //png
        }elseif($style=='gif'){
            $originail = imagecreatefromgif($im); //gif
        }
        $dims = getimagesize($im); //获取图片大小；
        //Array ( [0] => 800 [1] => 600 
        [2] => 2 [3] => width="800" height="600" 
        [bits] => 8 [channels] => 3 [mime] => image/jpeg )
        $pic_width = $dims['0'];
        $pic_height = $dims['1'];
        if(($maxwidth && $pic_width > $maxwidth) || ($maxheight && $pic_height > $maxheight))
        {
            $resizewidth_tag = false;
            $resizeheight_tag = false;
    
            if($maxwidth && $pic_width>$maxwidth){
                $widthratio = $maxwidth/$pic_width;
                $resizewidth_tag = true;
            }
    
            if($maxheight && $pic_height>$maxheight){
                $heightratio = $maxheight/$pic_height;
                $resizeheight_tag = true;
            }
    
            if($resizewidth_tag && $resizeheight_tag){
                if($widthratio<$heightratio)
                    $ratio = $widthratio;
                else
                    $ratio = $heightratio;
            }
    
            if($resizewidth_tag && !$resizeheight_tag)
                $ratio = $widthratio;
            if($resizeheight_tag && !$resizewidth_tag)
                $ratio = $heightratio;
    
            $newwidth = $pic_width * $ratio;
            $newheight = $pic_height * $ratio;
    
            //创建空白图像
            $newim = imagecreatetruecolor($newwidth,$newheight);
    
            //重采样拷贝部分图像并调整大小
            imagecopyresampled($newim,$originail ,0,0,0,0,$newwidth,$newheight,$dims['0'],$dims['1']);
            
        }else{
            //创建空白图像
            $newim = imagecreatetruecolor($dims['0'],$dims['1']);
            //重采样拷贝部分图像并调整大小
            imagecopyresampled($newim,$originail,0,0,0,0,$dims['0'],$dims['1'],$dims['0'],$dims['1']);
        }
    
        return imagejpeg($newim,$to);
    }

PHP 按比例缩放图片