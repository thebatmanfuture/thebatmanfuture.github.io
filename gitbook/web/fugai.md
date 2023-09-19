#### 变量覆盖

[![bKPjs0.png](https://s4.ax1x.com/2022/02/28/bKPjs0.png)](https://imgtu.com/i/bKPjs0)

变量覆盖漏洞在ctf考题中有关命令执行/代码执行的绕过里考，或者在对方存在文件上传点，但是waf对指定函数进行黑名单处理

~~~php
<?php		//ctf考题对flag过滤
$a = fl;
$b = ag;
$c = $a.$b;
?>
----------
<?php		//对assert断言函数过滤
$bb="assert";
$aa='bb';
$$aa($_POST['a']);
?>
-----------
~~~



经常引发变量覆盖漏洞的函数有：extract()，parse_str()，import_request_variables()

其实以上3、4种函数本质原理都是在利用变量<-->数组时，或者是$符号的解析特性导致变量绕过执行

##### extract

extract() 函数从数组中将变量导入到当前的符号表

~~~php
<?php
$a = "Original";
$my_array = array("a" => "Cat","b" => "Dog", "c" => "Horse");
extract($my_array);
echo "\$a = $a; \$b = $b; \$c = $c";
?>
---------------------------
输出：$a = Cat; $b = Dog; $c = Horse
~~~

单引号不会解析，所以写的`是$a那么就是$a`，双引号会解析，所以输出 b

[![bKFktg.png](https://s4.ax1x.com/2022/02/28/bKFktg.png)](https://imgtu.com/i/bKFktg)

这些斜杠的的意思就是，让后面的变量失去意义。那么写的是什么就是什么。
extract[函数](https://so.csdn.net/so/search?q=%E5%87%BD%E6%95%B0&spm=1001.2101.3001.7020)就是把数组中的变量，覆盖掉原来的变量，
这里我们可以理解为本来变量a应该输出Original，但是因为extract函数，所以把原来的a给覆盖了

变量test我们不知道是什么，extract函数就是把数组中的变量，覆盖掉原来的变量，
简单的说就是，$_GET获取到的值，通过extract函数的作用，把原来变量test的值给覆盖了，
然后isset函数是检查是否存在
最后如果gift和content的值相等的话，就输出flag
[![bKFJ39.png](https://s4.ax1x.com/2022/02/28/bKFJ39.png)](https://imgtu.com/i/bKFJ39)

通常都是在白盒审计时，查找关键函数才能找到变量覆盖漏洞，有利于拿webshell

##### $$

[![bKF2ut.png](https://s4.ax1x.com/2022/02/28/bKF2ut.png)](https://imgtu.com/i/bKF2ut)

##### parse_str

见我[BUUCTF-P15视频讲解](https://www.bilibili.com/video/BV1PF411n7kM?p=15)，或者php代码安全文章

~~~php
<?php
$a = 1;
parse_str("foo_bar=42",$o);
print_r($o);

echo "<br>";

parse_str("%20news[id%00=42",$o);  //Array ( [news_id] => 42 )
print_r($o);       
//  %20news[id%00=42
?>
~~~

这里get方式传入的%20news[id%00=42，最后被parse_str函数转换为[news_id] => 42

这里%20是空格url编码，被去掉了；%00是截断也被去掉了；[ 符号被转换为下划线。

作用：绕过某些针对关键字变量waf的拦截

比如sql注入语句：`/news.php?%20news[id%00=42"+AND+1=0--`

这里对方未匹配到news_id变量所以waf放行，同理，我们可以在变量$a，前后中间增加%00，%20等会被parse_str替换掉的位置

##### import_request_variables

import_request_variables 函数可以在 register_global = off 时，把 GET/POST/Cookie 变量导入全局作用域中

~~~php
<?php
$a = '0';
import_request_variables('G');
 
if($a == 1){
  echo "hello!";
}else{
  echo "word!";
}
?>
~~~

直接把输入的当变量并赋值了，比如：[www.xxx.com/1.php?a=0](https://blog.csdn.net/weixin_57567655/article/details/www.xxx.com/1.php?a=0),这里就直接把赋值给了$a,所以会输出word!,还有就是import_request_variables 函数里面的’G‘就是代表get传参，’P‘就是代表post传参

像这种变量覆盖漏洞实战中只能通过代码审计挖掘，这里就是利用我们通过get，post，cookie方式传入变量覆盖原代码存在的变量



先知社区文章https://xz.aliyun.com/t/11276







#### 异或生成

待更新



