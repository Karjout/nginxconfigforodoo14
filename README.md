# إعدادات  nginx و odoo 14
# Nginx Config for Odoo 14
     
##  الكود التالي يوضع في ملف الاعدادات الخاص بالنطاق في مخدم nginx
```
upstream odoo {
 server 127.0.0.1:8069;
}
upstream odoochat {
 server 127.0.0.1:8072;
}

# http -> https
server {
    if ($host = odoo.elbasri.net) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


   listen 80;
   server_name odoo.elbasri.net;
   rewrite ^(.*) https://$host$1 permanent;


}

server {
 listen 80;
 server_name odoo.elbasri.net;
 proxy_read_timeout 720s;
 proxy_connect_timeout 720s;
 proxy_send_timeout 720s;

 # Add Headers for odoo proxy mode
 proxy_set_header X-Forwarded-Host $host;
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header X-Forwarded-Proto $scheme;
 proxy_set_header X-Real-IP $remote_addr;


 # log
 access_log /var/log/nginx/odoo.access.log;
 error_log /var/log/nginx/odoo.error.log;

 # Redirect longpoll requests to odoo longpolling port
 location /longpolling {
 proxy_pass http://odoochat;
 }

 # Redirect requests to odoo backend server
 location / {
   proxy_redirect off;
   proxy_pass http://odoo;
 }

 # common gzip
 gzip_types text/css text/scss text/plain text/xml application/xml application/json application/javascript;
 gzip on;

}

```

#ملاحظة :
لاستخدام نطاقات فرعية او نطاقات جذر مختلفة لقواعد بيانات متعددة، يكفي اضافة هذا السطر في ملف اعدادات اودو:
```
dbfilter = ^%h$
```
###  لأي مساعدة يمكنك الاتصال بي عبر بيانات الاتصال الموجودة في موقعي التالي
https://www.nacer.ma/
                    
Video/فيديو:
https://www.youtube.com/watch?v=Pi9Yffgmlk4
