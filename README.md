Perfect! We’ll proceed with deployment using your server’s IP address (no custom domain).

Let’s assume:
- Your server’s public IP is: `YOUR_SERVER_IP` (replace this with your actual IP)
- Backend will run on port `5000`
- Frontend will be served by Nginx on port `80` (HTTP)

---

## 1. Install Dependencies

**If you haven’t already, run these commands on your server:**

```sh
# Update and install Node.js, npm, and Nginx
sudo apt update
sudo apt install -y nodejs npm nginx

# Install PM2 globally
sudo npm install -g pm2
```

---

## 2. Install Project Dependencies

```sh
# Backend
cd /path/to/Final_Product/server
npm install

# Frontend
cd ../frontend
npm install
```

---

## 3. Build the Frontend

```sh
cd /path/to/Final_Product/frontend
npm run build
```

---

## 4. Start Backend with PM2

```sh
cd /path/to/Final_Product/server
pm2 start index.js --name final-product-backend
pm2 save
```

---

## 5. Configure Nginx

**Edit the Nginx config:**

```sh
sudo nano /etc/nginx/sites-available/final_product
```

Paste this config (replace `YOUR_SERVER_IP` if needed):

```nginx
server {
    listen 80;
    server_name YOUR_SERVER_IP;

    root /path/to/Final_Product/frontend/build;
    index index.html index.htm;

    location / {
        try_files $uri /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:5000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**Enable the config:**

```sh
sudo ln -s /etc/nginx/sites-available/final_product /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 6. Test

- Visit `http://YOUR_SERVER_IP` in your browser.
- The frontend should load.
- API requests (e.g., `/api/...`) should be proxied to your backend.

---

**Let me know if you want to proceed with these steps, or if you want me to generate any config files or scripts for you!**  
If you have your actual server IP, please provide it so I can fill it in for you.
