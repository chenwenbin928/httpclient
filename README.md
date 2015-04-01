HttpClient 通过HTTP/HTTPS协议获取数据
===========
安装
===========
"require" : {
    "php" : ">=5.4.0"
    "leaps/httpclient": "1.2.5",
}

$HttpClient = new \Leaps\HttpClient\HttpClient();
-----------
获取一个实例化对像

    echo (new \Leaps\HttpClient\HttpClient())->get('http://www.baidu.com/');


setAgent($agent = null)
---------
设置客户端信息，留空则使用系统默认，返回HttpClient自身

	echo (new \Leaps\HttpClient\HttpClient())->setAgent('test')->get('http://www.baidu.com/');

setCookies($cookie)
---------
设置cookie内容，$cookie为字符串，多个cookie请用;隔开

	(new \Leaps\HttpClient\HttpClient())->setCookies('a=1;b=a;c[0]=1;c[1]=2');
    
setProxy($host,$port)
---------
设置代理服务器地址
(new \Leaps\HttpClient\HttpClient())->setProxy('username','password');

setAuthorization($username,$password)
---------
设置基本认证的用户名和密码
(new \Leaps\HttpClient\HttpClient())->setAuthorization('username','password');
    
setReferer($referer)
---------
设置引用页

	(new \Leaps\HttpClient\HttpClient())->setReferer('http://www.test.com/');

setIp($ip)
---------
设置请求的服务器的IP，这样可避免请求域名时DNS解析

	// 这样设置请求页面并不会通过DNS解析获取百度服务器的数据，而是直接请求127.0.0.1（即本机）的服务器的数据
	(new \Leaps\HttpClient\HttpClient())->setIp('127.0.0.1')->get('http://www.baidu.com/test.html');

setMultiMaxNum($num)
---------
设置并发请求时最大列队数量，系统默认为100。
HttpClient是支持并发请求的，详细可查看下面的get()方法。如果同时请求一个服务器，在一瞬间会对被请求服务器造成巨大压力，也会对本服务器增加网络IO压力，所以这个参数可以控制同时并发的数量上限，当达到上限后，列队将等待执行完毕一个追加一个插入列队。


setOption($key, $value)
---------
设置其它参数用以弥补HttpClient类中不存在的方法，具体请看具体驱动的方法，比如采用CURL的话，其实就相当于CURL的setOption()方法

	(new \Leaps\HttpClient\HttpClient())->setOption(CURLOPT_TIMEOUT,30);

get($url, $timeout = 10)
---------
用GET方法请求一个(或多个)页面，这样可以大大缩短API请求时间，并可以设置超时时间，单位：秒
支持并发进程请求，并发请求的特点：比如需要同时请求100个页面，传统的是一个一个载入，假设每个页面需要0.1秒，那么100个页面就需要耗时10秒，而通过并发的方式，100个页面理论上也就是0.1秒就可以同时载入完成了，效率非常高。

单个URL直接返回请求的内容的对象，多个URL则返回以URL为key的数组

	// 请求单个页面
	echo (new \Leaps\HttpClient\HttpClient())->get('http://www.baidu.com/',3)->data();
	
	// 请求多个页面
	$urls = array
	(
		'http://www.baidu.com/',
		'http://www.google.com/',
		'http://www.sina.com.cn/test.html',
	);
	// 返回已url为key的数组，注意，前后顺序有可能跟$urls中的不一样
	print_r(
		(new \Leaps\HttpClient\HttpClient())->get($urls)
	);

post($url, $data, $timeout = 30)
---------
用POST方法提交数据，支持多个页面同时请求
// 请求单个页面

	(new \Leaps\HttpClient\HttpClient())->post('http://www.baidu.com/',array('a'=>1,'b'=>1));
	
	// 请求多个页面
	$urls = array
	(
		'http://www.baidu.com/',
		'http://www.google.com/',
	);
	$vars = array
	(
		array('a'=>1,'b'=>1),	//对应 http://www.baidu.com/
		array('c'=>1,'d'=>1),	//对应 http://www.google.com/
	);
	(new HttpClient())->get($urls,$vars);


getDriver()
----------
返回当前驱动的对象，有时候需要获取driver后再进行操作，此时可通过此方法获取当前对象

	(new \Leaps\HttpClient\HttpClient())->getDriver();
