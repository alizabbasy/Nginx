# Static vs Dynamic Files in Nginx

A beginner-friendly guide to understanding the difference between static and dynamic files, how Nginx handles them, and best practices.

---

## 1️⃣ What is a Static File?

**Definition:** Files that are already present on the server and **are sent to the client exactly as they are** without any processing.

### Examples:

* HTML (`index.html`)
* CSS and JS files
* Images (PNG, JPG, SVG)
* Fonts

### Characteristics:

1. Content **does not change** unless manually edited.
2. The server only **reads and sends** the file.
3. Fast and low resource usage.

### Nginx Handling:

* Nginx serves static files **directly from disk** very efficiently.

### Example Nginx Config for Static Files:

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

**Explanation:**

* Requests for `/` or `/index.html` will be served directly by Nginx.
* No backend processing.
* Very fast, uses minimal resources.

---

## 2️⃣ What is a Dynamic File / Content?

**Definition:** Files or content that **require processing before sending to the client**, and the content can **change based on user input or server-side logic**.

### Examples:

* PHP pages (`index.php`)
* API responses (`GET /api/user/123`)
* Server-side rendered React pages
* Forms or database-driven pages

### Characteristics:

1. Content **can change** based on user, time, or database data.
2. The server must **process** the request before sending the result.
3. Usually slower than static files because processing is required.

### Nginx Handling:

* Nginx does not process dynamic content itself.
* Usually **acts as a reverse proxy**, forwarding requests to a backend server (PHP-FPM, Node.js, Django, etc.)
* The backend generates the response and Nginx forwards it to the client.

### Example Nginx Config for Dynamic Files:

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:3000; # Node.js backend
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```

**Explanation:**

* Requests to `/api/` or PHP files are forwarded to the backend.
* Backend processes the data, queries databases if needed, and returns the result.
* Nginx just passes the request and response.

---

## 3️⃣ Key Differences

| Feature              | Static                       | Dynamic                                             |
| -------------------- | ---------------------------- | --------------------------------------------------- |
| Content              | Fixed                        | Can change based on user/data                       |
| Server Processing    | None                         | Required                                            |
| Speed                | Very fast                    | Slower than static                                  |
| Examples             | HTML, CSS, JS, Images        | PHP, APIs, Server-rendered React                    |
| Nginx Handling       | Directly serves              | Routes to backend                                   |
| Nginx Config Example | `try_files $uri $uri/ =404;` | `proxy_pass http://backend;` or `fastcgi_pass ...;` |

---

## 4️⃣ When to Use

* **Static Files:** For speed, caching, and serving fixed content.
* **Dynamic Files:** For personalized content, API endpoints, and database-driven pages.

---

💡 **Tip:** Combining Nginx serving static files with backend dynamic processing is standard practice for scalable and efficient web applications. Static files reduce server load, while dynamic routing ensures flexibility and functionality.

