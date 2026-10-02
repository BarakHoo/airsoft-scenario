# Airsoft Scenario Page — Static Site

Single self-contained `index.html` (all images embedded as Base64, robust offline font fallbacks). No build step, no external asset files required.

## Deploy on Lightsail (Nginx)

### One-time server setup
```bash
# SSH into your Lightsail instance, then:
sudo mkdir -p /var/www/airsoft
sudo chown -R $USER:$USER /var/www/airsoft
cd /var/www/airsoft
git clone <YOUR_GITHUB_REPO_URL> .
```

### Nginx server block
Create `/etc/nginx/sites-available/airsoft`:
```nginx
server {
	listen 80;
	server_name YOUR_PUBLIC_HOST;   # e.g. coke-run.site  (separate domain = hides munkys.dev)
	root /var/www/airsoft;
	index index.html;
	location / { try_files $uri $uri/ =404; }
}
```
Enable + reload:
```bash
sudo ln -s /etc/nginx/sites-available/airsoft /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

### HTTPS (free, recommended)
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d YOUR_PUBLIC_HOST
```

### Updating the site later
On your PC: edit, then
```bash
git add -A && git commit -m "update" && git push
```
On the server:
```bash
cd /var/www/airsoft && git pull
```
(No nginx reload needed — static files are served fresh.)

## Privacy note (hiding munkys.dev)
- A subdomain (`game.munkys.dev`) or path (`munkys.dev/xxxx`) **still shows munkys.dev**.
- To fully hide the association, point a **separate domain** at the same Lightsail IP and use it as `server_name`. The server can host both domains simultaneously.
