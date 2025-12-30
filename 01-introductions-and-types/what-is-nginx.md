# 🌐 Nginx Types and Use Cases

![Nginx](https://img.shields.io/badge/Nginx-0F172A?style=for-the-badge&logo=nginx&logoColor=green)
![DevOps](https://img.shields.io/badge/DevOps-Learning-blue?style=for-the-badge)

---

## 📌 What is Nginx?

Nginx is a **high-performance web server, reverse proxy, and load balancer**.  

💡 Think of it as a **traffic controller** for web requests.  

**Key Features:**
- Fast and efficient
- Handles thousands of connections
- Lightweight and easy to scale

![Nginx Flow](https://raw.githubusercontent.com/alizabbasy/Nginx-HTTP-Methods-Guide/main/assets/nginx-flow.png)

---

## 🧩 Types / Uses of Nginx

Nginx can be used in several roles:

1. **Web Server** (serving static content)
2. **Reverse Proxy** (forward requests to backend servers)
3. **Forward Proxy** (clients go through Nginx to access external sites)
4. **Load Balancer** (distribute traffic across multiple servers)
5. **API Gateway** (manage APIs, routing, and security)
6. **Caching Server** (store responses to reduce backend load)

---

## 1️⃣  Web Server 🌐

**🎯 Role:**
Serve **static content** such as:
- HTML
- CSS
- JavaScript
- Images

** 📄 Example Configuration: **

```nginx
server {
    listen 80;
    server_name example.com;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 🧠 How It Works

- NGINX reads files directly from disk
- No backend processing required
- Extremely fast for static content

**Explanation:**

* Nginx reads files from disk and sends them to clients.
* No backend processing needed.

### ✅ Best Use Case
Landing pages, documentation sites, static frontends

---

## 2️⃣  Reverse Proxy 🔁

**🎯 Role:** 
Forward client requests to backend applications such as:
- Node.js
- Python (Gunicorn / Uvicorn)
- PHP-FPM
- Java Services

**Example Configuration:**

```nginx
server {
    listen 80;
    server_name example.com;

    location /api/ {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

**Explanation:**

* Client requests to `/api/` are forwarded to backend server.
* Backend handles processing and Nginx passes the response back.

** Request Flow 🔄 **
### Client → NGINX → Backend Application → NGINX → Client

**⚠️ Important:**
* NGINX does NOT process application logic.
* It only forwards requests and responses efficiently

---

## 5️⃣ Forward Proxy

**Role:** Nginx acts on behalf of clients to access external sites (not very common for public web servers).

**Example Config:**

```nginx
server {
    listen 3128;

    location / {
        proxy_pass http://$http_host$request_uri;
    }
}
```

**Explanation:**

* Client sends request to Nginx.
* Nginx fetches content from external site and returns it.

---

## 6️⃣ Load Balancer

**Role:** Distribute incoming traffic across multiple backend servers.

**Example Config:**

```nginx
upstream backend {
    server 192.168.0.101;
    server 192.168.0.102;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend;
    }
}
```

**Explanation:**

* Requests are sent to `backend` upstream, which balances load between servers.
* Helps prevent overload on a single server.

---

## 7️⃣ API Gateway

**Role:** Manage API requests, routing, authentication, and security.

**Example Config:**

```nginx
server {
    listen 80;
    server_name api.example.com;

    location /v1/ {
        proxy_pass http://backend_v1;
        # Add headers or rate limits if needed
    }
}
```

**Explanation:**

* Nginx routes API requests and can add authentication, rate limiting, or caching.

---

## 8️⃣ Caching Server

**Role:** Cache responses to reduce load on backend servers.

**Example Config:**

```nginx
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=my_cache:10m max_size=1g;

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_cache my_cache;
        proxy_pass http://backend;
    }
}
```

**Explanation:**

* Responses from backend are cached on Nginx.
* Subsequent requests are served from cache, reducing backend load.

---

This document introduces the main **roles of Nginx** with simple explanations and example configurations. Advanced configuration and optimization will be covered in future documents.

