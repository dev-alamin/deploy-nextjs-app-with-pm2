# **Deploying Next.js App with PM2 + Nginx + SSL (Let's Encrypt)**

* * * * *

### 1\. Clone Your Project & Install Dependencies

-   SSH into your server.

-   Clone your project repo:\
    `git clone <your-repo-url>`

-   Go into project directory:\
    `cd <your-project-folder>`

-   Install dependencies:\
    `npm install`

* * * * *

### 2\. Build Your Next.js App for Production

Run the build command to generate the optimized production build:\
`npm run build`

* * * * *

### 3\. Start Your Next.js App with PM2

Start the app using PM2 to keep it running in the background and auto-restart on failure:

```
pm2 start npm --name <app-name> -- start --cwd /full/path/to/your/project

```

Example:

```
pm2 start npm --name devspark-next -- start --cwd /var/www/devspark/portfolio-frontend

```

* * * * *

### 4\. Basic PM2 Commands

-   `pm2 list` --- List all PM2 processes and their status

-   `pm2 status` --- Show detailed status of running apps

-   `pm2 logs <app-name>` --- View real-time logs

-   `pm2 restart <app-name>` --- Restart your app after code or config changes

-   `pm2 stop <app-name>` --- Stop the app

-   `pm2 delete <app-name>` --- Remove the app from PM2

-   `pm2 save` --- Save the current process list for auto respawn on reboot

-   `pm2 startup` --- Generate and enable system startup script for PM2

After starting your app, check it's running:\
`curl http://localhost:3000`

* * * * *

### 5\. Set Up Nginx as a Reverse Proxy

Nginx will forward HTTP/HTTPS traffic to your Next.js app running on localhost:3000.

1.  Install nginx (if not installed):

```
sudo apt update
sudo apt install nginx

```

1.  Create a new nginx config file:

```
sudo nano /etc/nginx/sites-available/your-domain.conf

```

1.  Paste this config (replace `your-domain.com` and adjust paths):

```
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

```

1.  Enable the config:

```
sudo ln -s /etc/nginx/sites-available/your-domain.conf /etc/nginx/sites-enabled/

```

1.  Test nginx config and reload:

```
sudo nginx -t
sudo systemctl reload nginx

```

At this point, visiting `http://your-domain.com` will proxy requests to your Next.js app on port 3000.

* * * * *

### 6\. Enable SSL with Let's Encrypt (Certbot)

1.  Install Certbot and nginx plugin:

```
sudo apt install certbot python3-certbot-nginx

```

1.  Obtain and install the SSL certificate:

```
sudo certbot --nginx -d your-domain.com -d www.your-domain.com

```

1.  Follow prompts to enter your email and agree to terms.

Certbot will automatically configure nginx to redirect HTTP to HTTPS and use SSL certificates.

1.  Verify auto-renewal is scheduled:

```
sudo systemctl status certbot.timer

```

Renew certificates manually (if needed) with:

```
sudo certbot renew --dry-run

```

* * * * *

### 7\. Final Check

-   Visit `https://your-domain.com` in a browser and confirm your Next.js app loads securely.

-   PM2 ensures your app keeps running even after server restarts.

-   Nginx manages SSL and proxying for better performance and security.

* * * * *

### 8\. Optional: Set PM2 to Start on Server Boot

Run:

```
pm2 startup
pm2 save

```

This sets up your app to auto-start when the server reboots.

* * * * *

**Summary:**

-   Build your Next.js app with `npm run build`.

-   Run it with PM2:\
    `pm2 start npm --name <app-name> -- start --cwd /your/project/path`

-   Configure nginx as a reverse proxy.

-   Secure your site with SSL using Certbot.

-   Use PM2 commands to manage the app anytime.
