# phpfpm-workerman
phpfpm-workerman。到底有啥不同。phpfpm 也可以起多个进程。workerman也可以起多个进程。。。。


面试的时候。聊聊聊。被问到了这个问题。

那么到底不同 在哪呢？


后来仔细想。明白了。


phpfpm 。起的进程。进程是在内存的概念。

phpfpm  不需要重启。代码就能生效。 workerman 需要重启。


workerman  叫常驻内存框架。 phpfpm 不叫。

但是为什么phpfpm 还要 启动n个进程呢。


这涉及到 PHP 执行过程。7个阶段，phpfpm 进程常驻内存 省去了其中的 几个步骤

master进程
fcgi_init()  --->sapi_startup()  --->cgi_sapi_module.startup ---->php_cgi_startup --->php_module_startup--->fpm_init()
--->fpm_run()---->1、fork子进程 2、 循环事件（fpm_event_loop 处理）
fork子进程---》fpm_children_create_initial

worker进程
fcgi_accept_request---》accept---》处理请求阶段---》fpm_request_info---》php_request_startup---》php_fopen_primary_script（获取访问脚本）  ---》脚本.php ----》php_execute_script(执行)----》zend_execute_scripts----》交给zend 引擎 ---》处理结束---》php_request_shutdown----》调用 register_shutdown_function  __destruct()  发送响应内容   释放内存  ----》等待下一个事件fcgi_accept_request


phpfpm 进程启动  ===》 这个进程里面 加载 了 php 解释器 ！！！


php源代码、opcache是在磁盘上的。  但是 php 解释器 在 内存中。


在worker进程处理请求时，无需再次初始化PHP运行环境，这也是php-fpm性能优异的原因之一。

https://www.workerman.net/performance
workerman  tcp 协议
php-fpm    http ??



