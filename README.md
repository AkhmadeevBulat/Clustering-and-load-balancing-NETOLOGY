# Домашнее задание к занятию «Кластеризация и балансировка нагрузки»

---
## Ахмадеев Булат Наилевич

---

## Задание 1

1. Установил python3 командой ```sudo apt install python3``` на двух серверах с ip-адресами ```10.20.40.5``` и ```10.20.40.8```

2. Создал файлы на двух серверах ```server1.py``` и ```server2.py```

Код в ```server1.py``` и ```server2.py```:

```
import http.server
import socketserver

PORT = 8888

class MyHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b'Server 1 response')

with socketserver.TCPServer(("", PORT), MyHandler) as httpd:
    print("serving at port", PORT)
    httpd.serve_forever()

```

```
import http.server
import socketserver

PORT = 8888

class MyHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b'Server 2 response')

with socketserver.TCPServer(("", PORT), MyHandler) as httpd:
    print("serving at port", PORT)
    httpd.serve_forever()

```

3. Запустил эти два сервера на python3

![ФОТО 4 штуки](images/server-python-1.png) 
![ФОТО 4 штуки](images/server-python-2.png) 
![ФОТО 4 штуки](images/server-python-web-1.png) 
![ФОТО 4 штуки](images/server-python-web-2.png)

4. Запустил 3-ий сервер с ip-адресом ```10.20.40.6```

5. Установил HAProxy командой ```sudo apt install haproxy``` на 3-ем сервере

6. Открыл конфигурационный файл HAProxy командой ```sudo nano /etc/haproxy/haproxy.cfg```

```
global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon

        # Default SSL material locations
        ca-base /etc/ssl/certs
        crt-base /etc/ssl/private

        # See: https://ssl-config.mozilla.org/#server=haproxy&server-version=2.0.3&config=intermediate
        ssl-default-bind-ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
        ssl-default-bind-ciphersuites TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
        ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http

frontend http_front
    bind *:80
    default_backend http_back

backend http_back
    balance roundrobin
    server server1 10.20.40.5:8888 check
    server server2 10.20.40.8:8888 check
```

7. Перезапустил сервис HAProxy командой ```sudo systemctl restart haproxy```

8. Перенаправление работает:

![ФОТО](images/HAProxy-2.png) 
![ФОТО](images/HAProxy-1.png)

---

## Задание 2



---