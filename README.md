map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}

server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html/0-Base;
    }
    location /preview {
        root   /usr/share/nginx/html/;
    }
    location /dist {
        root   /usr/share/nginx/html/;
    }
    location ~ "/back/(.*)" {
        proxy_set_header X-Forwarded-Host $host:$server_port;
        proxy_set_header X-Forwarded-Server $host:$server_port;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        resolver 127.0.0.11 valid=30s;
        set $upstream_backend http://scpas_backend:8080/;
        proxy_pass $upstream_backend$1$is_args$args;
        proxy_redirect $upstream_backend$1 /back/$1;
        proxy_http_version 1.1;
    }
    location ~ "/itesoft-messaging/(.*)" {
        resolver 127.0.0.11 valid=30s;
        set $upstream_backend http://scpas_backend:8080/itesoft-messaging/;
        proxy_pass $upstream_backend$1$is_args$args;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}
