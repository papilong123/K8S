# server-name优先匹配顺序例子

```
# 左匹配
server {
 listen 80;
 server_name *.nginx-test.com;
 root /usr/share/nginx/html/nginx-test/left-match/;
 location / {
 index index.html;
 }
}

# 正则匹配
server {
 listen 80;
 server_name ~^.*\.nginx-test\..*$;
 root /usr/share/nginx/html/nginx-test/reg-match/;
 location / {
 index index.html;
 }
}

# 右匹配
server {
 listen 80;
 server_name www.nginx-test.*;
 root /usr/share/nginx/html/nginx-test/right-match/;
 location / {
 index index.html;
 }
}

# 完全匹配
server {
 listen 80;
 server_name www.nginx-test.com;
 root /usr/share/nginx/html/nginx-test/all-match/;
 location / {
 index index.html;
 }
}
```

访问分析

当访问 www.nginx-test.com 时，都可以被匹配上，因此选择优先级最高的“完全匹配”；
当访问 mail.nginx-test.com 时，会进行“左匹配”；
当访问 www.nginx-test.org 时，会进行“右匹配”；
当访问 doc.nginx-test.com 时，会进行“左匹配”；
当访问 www.nginx-test.cn 时，会进行“右匹配”；
当访问 fe.nginx-test.club 时，会进行“正则匹配”；



## location正则匹配实例

```
server {
 listen 80;
 server_name www.nginx-test.com;

# 只有当访问 www.nginx-test.com/match_all/ 时才会匹配上#/usr/share/nginx/html/match_all/index.html
 location = /match_all/ {
   root /usr/share/nginx/html
   index index.html
 }

# 当访问 www.nginx-test.com/1.jpg 等路径时会去 /usr/share/nginx/images/1.jpg 找对应的资源
 location ~ \.(jpeg|jpg|png|svg)$ {
  root /usr/share/nginx/images;
 }

# 当访问 www.nginx-test.com/bbs/ 时会匹配上 /usr/share/nginx/html/bbs/index.html
 location ^~ /bbs/ {
  root /usr/share/nginx/html;
  index index.html index.htm;
 }
}
location 中的反斜线
location /test {
 ...
}

location /test/ {
 ...
}
```



- 不带 / 当访问 www.nginx-test.com/test 时， [Nginx ](http://mp.weixin.qq.com/s?__biz=MzI0MDQ4MTM5NQ==&mid=2247489421&idx=1&sn=2878d9dd5257d96f7d307a47484558d0&chksm=e91b7291de6cfb87c7291694f6bbed5c52d043ad312f800b6905cf0002ee7bb7660852b287a2&scene=21#wechat_redirect)先找是否有 test 目录，如果有则找 test 目录下的 index.html ；如果没有 test 目录， nginx 则会找是否有 test 文件。
- 带 / 当访问 www.nginx-test.com/test 时， [Nginx ](http://mp.weixin.qq.com/s?__biz=MzI0MDQ4MTM5NQ==&mid=2247489066&idx=1&sn=e90f0bc81a487822428ea82b00fb09fa&chksm=e91b7336de6cfa203259a5c7d001031521d055eb7cf53f46c0c89b11512ac0c2677692a3e6c3&scene=21#wechat_redirect)先找是否有 test 目录，如果有则找 test 目录下的 index.html ，如果没有它也不会去找是否存在 test 文件。