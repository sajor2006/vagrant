Тестовое задание 

nginx proxy_pass <br>
80 -> 443<br>
443 -> 443<br>

<br>
В vagrant файле нужно заменять ip на свои.
<br>
<br>

web
<pre>
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    include /etc/nginx/conf.d/*.conf;

    server {
real_ip_header X-Real-IP;
set_real_ip_from 192.168.33.143;
ssl on;
ssl_certificate /etc/ssl/certificate.crt;
ssl_certificate_key /etc/ssl/privateKey.key;
        listen 443 default_server;
        listen       [::]:80 default_server;
        server_name  test-admin.local.net;
        root         /usr/share/nginx/html;

        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
</pre>

proxy
<pre>
      user nginx;
      worker_processes auto;
      error_log /var/log/nginx/error.log;
      pid /run/nginx.pid;
      
      include /usr/share/nginx/modules/*.conf;
      
      events {
          worker_connections 1024;
      }
      
      http {
          log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                            '$status $body_bytes_sent "$http_referer" '
                            '"$http_user_agent" "$http_x_forwarded_for"';
      
          access_log  /var/log/nginx/access.log  main;
      
          sendfile            on;
          tcp_nopush          on;
          tcp_nodelay         on;
          keepalive_timeout   65;
          types_hash_max_size 2048;
      
          include             /etc/nginx/mime.types;
          default_type        application/octet-stream;
      
          include /etc/nginx/conf.d/*.conf;
      
      
          server {
              listen       80 default_server;
              server_name  _;
              root         /usr/share/nginx/html;
      
              include /etc/nginx/default.d/*.conf;
      
              location / {
              }
      
              error_page 404 /404.html;
                  location = /40x.html {
              }
      
              error_page 500 502 503 504 /50x.html;
                  location = /50x.html {
              }
          }
       
      server {
              listen       80;
              server_name  test-admin.local.net;
              root         /usr/share/nginx/html;
      
              include /etc/nginx/default.d/*.conf;
                location / {
                        proxy_pass "https://192.168.33.66";
                        proxy_set_header Host $host;
                        proxy_set_header X-Real-IP $remote_addr;
                }
           error_page 404 /404.html;
                  location = /40x.html {
              }
      
              error_page 500 502 503 504 /50x.html;
                  location = /50x.html {
              }
        }
      
          server {
              listen       443;
              server_name  test-admin.local.net;
              root         /usr/share/nginx/html;
        
              ssl on;
              ssl_certificate /etc/ssl/certificate.crt;
              ssl_certificate_key /etc/ssl/privateKey.key;
      
              include /etc/nginx/default.d/*.conf;
              location / {
          proxy_pass "https://192.168.33.66";
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
              }
              error_page 404 /404.html;
                  location = /40x.html {
              }
      
              error_page 500 502 503 504 /50x.html;
                  location = /50x.html {
              }
          }
      }
</pre>
