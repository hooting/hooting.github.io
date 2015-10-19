---
layout: post
title: "PHP之call_user_func_array用法"
subtitle: ""
category: PHP
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [PHP]
---

直接上最简单的

    header('Content-Type:text/html;charset=utf-8');
    class call_func{
        function func0() {
            echo 'func0';
        }

        function func1($a) {
            echo $a;
        }

        function func2($a, $b) {
            echo $a+$b;
        }

        function func3($a, $b, $c) {
            echo $a+$b+$c;
        }
    }

    function otest() {
        $args = func_get_args();    
        $num = func_num_args();
        call_user_func_array(array('call_func','func'.$num), $args);
    }

    otest(1,1,1);


mixed call_user_func_array ( callback function, array param_arr ) 和 mixed call_user_func ( callback $function [, mixed $parameter [, mixed $... ]] ) 相似

还可以这样实例化类的。
