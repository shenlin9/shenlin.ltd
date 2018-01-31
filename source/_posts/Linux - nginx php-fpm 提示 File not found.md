

使用php-fpm解析PHP，"No input file specified"，"File not found"是令nginx新手头疼的常见错误，原因是php-fpm进程找不到SCRIPT_FILENAME配置的要执行的.php文件，php- fpm返回给nginx的默认404错误提示。

比如我的网站doucument_root下没有test.php，访问这个文件时通过抓包可以看到返回的内容。

    HTTP/1.1 404 Not Found
    Date: Fri, 21 Dec 2012 08:15:28 GMT
    Content-Type: text/html
    Proxy-Connection: close
    Server: nginx/1.2.5
    X-Powered-By: PHP/5.4.7
    Via: 1.1 c3300 (NetCache NetApp/6.0.7)
    Content-Length: 16

    File not found.


很多人不想用户直接看到这个默认的404错误信息，想自定义404错误.

给出解决办法前我们来先分析下如何避免出现这类404错误，然后再说真的遇到这种情况(比如用户输入一个错误不存在的路径)时该怎么办，才能显示自定义的404错误页。
一、错误的路径被发送到php-fpm进程

出现这类错误，十个有九个是后端fastcgi进程收到错误路径(SCRIPT_FILENAME)，而后端fastcgi收到错误路径的原因大都是配置错误。

常见的nginx.conf的配置如下：

    server {
        listen   [::]:80;
        server_name  example.com www.example.com;
        access_log  /var/www/logs/example.com.access.log;  

        location / {
            root   /var/www/example.com;
            index  index.html index.htm index.pl;
        }

        location /images {
            autoindex on;
        }

        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /var/www/example.com$fastcgi_script_name;
            include fastcgi_params;
        }
    }

这个配置中有很多不合理的地方，其中一个明显的问题就是root指令被放到了location / 块。如果root指令被定义在location块中那么该root指令只能对其所在的location生效。其它locaiont中没有root指令，像 location /images块不会匹配任何请求，需要在每个请求中重复配置root指令来解决这个问题。因此我们需要把root指令放在server块，这样各个 location就会继承父server块定义的$document_root，如果某个location需要定义一个不同 的$document_root，则可以在location单独定义一个root指令。

另一个问题就是fastCGI参数SCRIPT_FILENAME 是写死的。如果修改了root指令的值或者移动文件到别的目录，php-fpm会返回“No input file specified”错误，因为SCRIPT_FILENAME在配置中是写死的并没有随着$doucument_root变化而变化，我们可以修改 SCRIPT_FILENAME配置如下：

fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;

 

所以我们不能忘记在server块中配置root指令，不然$document_root的值为空，只会传$fastcgi_script_name到php-fpm，这样就会导致“No input file specified”错误。

 
二、请求的文件真的不存在

当nginx收到一个不在的.php文件的请求时,因为nginx只会检查$uri是否是.php结尾，不会对文件是否存在进行判断，.php结尾 的请求nginx会直接发给php-fpm处理。php-fpm处理时找不到文件就会返回“No input file specified”带着“404 Not Found”头。
解决办法

我们在nginx拦截不存在的文件，请求并返回自定义404错误

使用 try_files 捕捉不存在的urls并返回错误。

    location ~ .php$ {
     try_files $uri =404;
     fastcgi_pass 127.0.0.1:9000;
     fastcgi_index index.php;
     fastcgi_param SCRIPT_FILENAME ....
     ...................................
     ...................................
    }

上面的配置会检查.php文件是否存在，如果不存在，会返回404页面。

