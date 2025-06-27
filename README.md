# 🚀 Deployment Brief: Node.js + React App with Nginx & PM2

## 1. **Install Dependencies**
```sh
sudo apt update
sudo apt install -y nodejs npm nginx
sudo npm install -g pm2
```

---

## 2. **Install Project Dependencies**
```sh
# Backend
cd /path/to/Final_Product/server
npm install

# Frontend
cd ../frontend
npm install
```

---

## 3. **Environment Variables**

### **Backend (`server/.env`):**
```
PORT=3002
HOST=0.0.0.0
# Add other backend env variables as needed
```

### **Frontend (`frontend/.env`):**
```
REACT_APP_API_URL=/api
# Add other frontend env variables as needed
```
> **After editing frontend `.env`, rebuild the frontend:**
> ```sh
> npm run build
> ```

---

## 4. **Check/Update Backend Listen Address**
In `server/index.js`:
```js
app.listen(3002, '0.0.0.0', () => {
  console.log('Server running on port 3002');
});
```

---

## 5. **Check/Update Frontend API URLs**
- Ensure all API calls use relative paths: `/api/...`
- Example:
  ```js
  fetch(`${process.env.REACT_APP_API_URL}/something`)
  ```

---

## 6. **Build the Frontend**
```sh
cd /path/to/Final_Product/frontend
npm run build
```

---

## 7. **Start Backend with PM2**
```sh
cd /path/to/Final_Product/server
pm2 start index.js --name final-product-backend
pm2 save
```

---

## 8. **Configure Nginx**
Create `/etc/nginx/sites-available/final_product`:
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
        proxy_pass http://localhost:3002/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Enable and reload:
```sh
sudo ln -s /etc/nginx/sites-available/final_product /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 9. **Test**
- Visit `http://YOUR_SERVER_IP` in your browser.
- The frontend should load.
- API requests should work.

---

## 10. **(Optional) PM2 Startup on Boot**
```sh
pm2 startup
# Follow the instructions, then:
pm2 save
```

---

**Summary:**  
- All API calls use `/api` (relative path).
- Backend listens on `0.0.0.0:3002`.
- Nginx serves the frontend and proxies `/api` to backend.
- PM2 manages backend process.
- Environment variables are set in `.env` files.

---

Let me know if you want a ready-to-copy Nginx config, `.env` templates, or help with any specific step!
