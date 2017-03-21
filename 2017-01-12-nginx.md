# nginx

## install new nginx on linux when exists other nginx

```
# nginx -V  // show exists nginx install path --prefix
# tar zxvf nginx.tar.gz
# cd nginx-1.11.6
# ./configure --prefix=/usr/local/nginx-1116 --with-http_stub_status_module --with-http_ssl_module  --with-http_realip_module
# make & make install

// if addition new module on exists nginx, do not make install

```

### maybe error like this:
> ./configure: error: the HTTP rewrite module requires the PCRE library.
```
# yum install -y pcre-devel  
```

> ./configure: error: the HTTP gzip module requires the zlib library.
```
# yum install -y zlib-devel  
```

> ./configure: error: the HTTP cache module requires md5 functions from OpenSSL library.
>  ./configure: error: SSL modules require the OpenSSL library.
```
# yum install -y openssl openssl-devel  
```

## configure SSL
```
server {
    listen 443;
    server_name wx.sge9999.com;
    ssl on;
    #root html;
    #index index.html index.htm;
    ssl_certificate   /root/cert/213996143500431.pem;
    ssl_certificate_key  /root/cert/213996143500431.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        proxy_pass http://127.0.0.1:8888;
        proxy_redirect  http://127.0.0.1:8888 https://wx.sge9999.com;
        proxy_redirect  https://127.0.0.1:8443 https://wx.sge9999.com;
        proxy_set_header Host $host:$server_port;
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        #proxy_set_header Upgrade $http_upgrade;
        #proxy_set_header Connection "upgrade";
    }
}
```

## configure CDN
```
server {
    listen 8888;
    server_name localhost;
    location / {
		# the static directory
        root /root;
    }
}

```

```
url的/问题
在nginx中配置proxy_pass时，当在后面的url加上了/，相当于是绝对根路径，则nginx不会把location中匹配的路径部分代理走;如果没有/，则会把匹配的路径部分也给代理走。
 
下面四种情况分别用http://192.168.1.4/proxy/test.html 进行访问。
第一种：
location /proxy/ {
     proxy_pass http://127.0.0.1:81/;
}
会被代理到http://127.0.0.1:81/test.html 这个url
 
第二咱(相对于第一种，最后少一个 /)
location /proxy/ {
     proxy_pass http://127.0.0.1:81;
}
会被代理到http://127.0.0.1:81/proxy/test.html 这个url
 
第三种：
location /proxy/ {
     proxy_pass http://127.0.0.1:81/ftlynx/;
}
会被代理到http://127.0.0.1:81/ftlynx/test.html 这个url。
 
第四种情况(相对于第三种，最后少一个 / )：
location /proxy/ {
     proxy_pass http://127.0.0.1:81/ftlynx;
}
会被代理到http://127.0.0.1:81/ftlynxtest.html 这个url
```

```
location的匹配种类有哪些?

格式  location [ 空格 | = | ~ | ~* | !~ | !~* ] /uri/ {}

# 精确匹配: 相等(=)
# 字符串匹配: 字符串匹配(空格) 匹配开头(^~)
# 正则匹配: 区分大小写匹配(~) 不区分大小写匹配(~*) 区分大小写不匹配(!~) 不区分大小写不匹配(!~*)
location搜索优先级优先级如何?

精确匹配 > 字符串匹配( 长 > 短 [ 注: ^~ 匹配则停止匹配 ]) > 正则匹配( 上 > 下 )

# 精确匹配只能命中一个
# 字符串匹配使用匹配最长的最为匹配结果
# 正则匹配按照location定义的顺序进行匹配，先定义具有高优先级
```
