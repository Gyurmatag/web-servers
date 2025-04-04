# Practical Web Server Tutorial: From Zero to Serving Your First Website

This hands-on tutorial will guide you through setting up your first web server. We'll start with the basics and gradually build a fully functional web server that can host multiple websites.

## Prerequisites

- A Linux computer or virtual machine (Ubuntu 22.04 LTS recommended for beginners)
- Basic knowledge of the command line
- Administrator (sudo) privileges on your system
- 1-2 hours of time to complete all sections

## Part 1: Installing Your First Web Server

Let's start by installing Nginx, one of the most popular web servers available today.

### Step 1: Update Your System

First, make sure your system is up-to-date:

```bash
sudo apt update
sudo apt upgrade -y
```

### Step 2: Install Nginx

```bash
sudo apt install nginx -y
```

### Step 3: Verify the Installation

Check if Nginx is running:

```bash
sudo systemctl status nginx
```

You should see output indicating that Nginx is "active (running)".

### Step 4: Allow Nginx Through the Firewall

```bash
sudo ufw allow 'Nginx HTTP'
sudo ufw status
```

### Step 5: Test Your Web Server

Open a web browser and navigate to your server's IP address:
- If installed locally: http://localhost or http://127.0.0.1
- If on a remote server: http://your_server_ip

You should see the default Nginx welcome page that says "Welcome to nginx!".

**Congratulations!** You now have a working web server!

## Part 2: Creating Your First Website

Now that we have Nginx running, let's create and serve your own custom website.

### Step 1: Create a Directory for Your Website

```bash
sudo mkdir -p /var/www/mywebsite/html
```

### Step 2: Set Permissions

```bash
sudo chown -R $USER:$USER /var/www/mywebsite/html
sudo chmod -R 755 /var/www/mywebsite
```

### Step 3: Create a Simple HTML Page

Create an index.html file:

```bash
nano /var/www/mywebsite/html/index.html
```

Add the following HTML code:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My First Web Server</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 20px;
            text-align: center;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 5px;
        }
        h1 {
            color: #333;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to My First Website!</h1>
        <p>This page is being served by Nginx on my very own web server.</p>
        <p>Today's date: <script>document.write(new Date().toLocaleDateString());</script></p>
    </div>
</body>
</html>
```

Save and exit (Ctrl+X, then Y, then Enter).

### Step 4: Create a Server Block Configuration

Create a new server block configuration file:

```bash
sudo nano /etc/nginx/sites-available/mywebsite
```

Add the following configuration:

```nginx
server {
    listen 80;
    listen [::]:80;

    root /var/www/mywebsite/html;
    index index.html index.htm;

    server_name mywebsite.local www.mywebsite.local;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Save and exit.

### Step 5: Enable the Server Block

```bash
sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/
```

### Step 6: Test and Reload Nginx

Check if the configuration is valid:

```bash
sudo nginx -t
```

If there are no errors, reload Nginx:

```bash
sudo systemctl reload nginx
```

### Step 7: Update Local Hosts File (for Local Testing)

To test your site using the domain name you specified:

```bash
sudo nano /etc/hosts
```

Add the following line:

```
127.0.0.1   mywebsite.local www.mywebsite.local
```

Save and exit.

### Step 8: Visit Your New Website

Open a browser and go to http://mywebsite.local

You should see your custom webpage instead of the default Nginx page.

## Part 3: Hosting Multiple Websites (Virtual Hosts)

Let's set up a second website to demonstrate virtual hosting.

### Step 1: Create a Directory for Your Second Website

```bash
sudo mkdir -p /var/www/secondsite/html
sudo chown -R $USER:$USER /var/www/secondsite/html
sudo chmod -R 755 /var/www/secondsite
```

### Step 2: Create Content for the Second Site

```bash
nano /var/www/secondsite/html/index.html
```

Add different HTML content:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Second Website</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 20px;
            text-align: center;
            background-color: #f0f8ff;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            border: 1px solid #b0c4de;
            padding: 20px;
            border-radius: 5px;
            background-color: white;
        }
        h1 {
            color: #4682b4;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to My Second Website!</h1>
        <p>This demonstrates virtual hosting with Nginx.</p>
        <p>Server time: <script>document.write(new Date().toLocaleTimeString());</script></p>
    </div>
</body>
</html>
```

Save and exit.

### Step 3: Create a Server Block for the Second Site

```bash
sudo nano /etc/nginx/sites-available/secondsite
```

Add the following configuration:

```nginx
server {
    listen 80;
    listen [::]:80;

    root /var/www/secondsite/html;
    index index.html index.htm;

    server_name secondsite.local www.secondsite.local;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Save and exit.

### Step 4: Enable the New Server Block

```bash
sudo ln -s /etc/nginx/sites-available/secondsite /etc/nginx/sites-enabled/
```

### Step 5: Update Hosts File

```bash
sudo nano /etc/hosts
```

Add the second site:

```
127.0.0.1   secondsite.local www.secondsite.local
```

Save and exit.

### Step 6: Test and Reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 7: Test Both Websites

Open your browser and navigate to:
- http://mywebsite.local
- http://secondsite.local

You should see different content for each site, even though they're hosted on the same server with the same IP address!

## Part 4: Installing a Complete LEMP Stack

Let's expand our setup to a full LEMP stack (Linux, Nginx, MySQL, PHP).

### Step 1: Install MySQL

```bash
sudo apt install mysql-server -y
```

### Step 2: Secure MySQL Installation

```bash
sudo mysql_secure_installation
```

Follow the prompts to set a root password and secure your MySQL installation.

### Step 3: Install PHP and Required Extensions

```bash
sudo apt install php-fpm php-mysql -y
```

### Step 4: Configure Nginx to Use PHP

Edit the configuration file for your first website:

```bash
sudo nano /etc/nginx/sites-available/mywebsite
```

Update it to handle PHP files:

```nginx
server {
    listen 80;
    listen [::]:80;

    root /var/www/mywebsite/html;
    index index.php index.html index.htm;

    server_name mywebsite.local www.mywebsite.local;

    location / {
        try_files $uri $uri/ =404;
    }

    # Add this block to process PHP files
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
    }

    # Deny access to .htaccess files
    location ~ /\.ht {
        deny all;
    }
}
```

Note: The PHP version in the socket path (php8.1-fpm.sock) may need to be adjusted based on your installed PHP version.

### Step 5: Create a PHP Test File

```bash
nano /var/www/mywebsite/html/info.php
```

Add the following PHP code:

```php
<?php
phpinfo();
?>
```

Save and exit.

### Step 6: Test and Reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 7: Test PHP Processing

Visit http://mywebsite.local/info.php in your browser. You should see the PHP info page with detailed information about your PHP installation.

## Part 5: Setting Up a Simple Reverse Proxy

Let's configure Nginx as a reverse proxy for a simple application.

### Step 1: Install a Sample Application (Node.js)

First, install Node.js:

```bash
sudo apt install nodejs npm -y
```

### Step 2: Create a Simple Node.js Application

Create a directory for your application:

```bash
mkdir ~/nodeapp
cd ~/nodeapp
```

Initialize a new Node.js project:

```bash
npm init -y
```

Install Express (a Node.js web framework):

```bash
npm install express
```

Create a simple server file:

```bash
nano index.js
```

Add the following code:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send(`
    <html>
    <head>
      <title>Node.js Application</title>
      <style>
        body { font-family: Arial, sans-serif; margin: 40px; line-height: 1.6; }
        h1 { color: #333; }
        .container { border: 1px solid #ddd; padding: 20px; border-radius: 5px; }
      </style>
    </head>
    <body>
      <div class="container">
        <h1>Hello from Node.js!</h1>
        <p>This content is being served by a Node.js application through Nginx reverse proxy.</p>
        <p>Server time: ${new Date()}</p>
      </div>
    </body>
    </html>
  `);
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});
```

Save and exit.

### Step 3: Run the Node.js Application

```bash
node index.js
```

Your application should start and listen on port 3000. Leave this terminal window open.

### Step 4: Create a Reverse Proxy Configuration

Open a new terminal window and create a new Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/nodeproxy
```

Add the following configuration:

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name nodeapp.local www.nodeapp.local;

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

Save and exit.

### Step 5: Enable the Configuration

```bash
sudo ln -s /etc/nginx/sites-available/nodeproxy /etc/nginx/sites-enabled/
```

### Step 6: Update Hosts File

```bash
sudo nano /etc/hosts
```

Add the Node.js application domain:

```
127.0.0.1   nodeapp.local www.nodeapp.local
```

Save and exit.

### Step 7: Test and Reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 8: Test the Reverse Proxy

Visit http://nodeapp.local in your browser. You should see the content from your Node.js application, but it's being accessed through Nginx, which is acting as a reverse proxy.

## Part 6: Troubleshooting Common Issues

Let's practice troubleshooting by intentionally creating and then fixing some common issues.

### Scenario 1: 404 Not Found Error

1. Rename your index.html file:

```bash
cd /var/www/mywebsite/html
mv index.html home.html
```

2. Access http://mywebsite.local and observe the 404 error.

3. Check Nginx's error log:

```bash
sudo tail /var/log/nginx/error.log
```

4. Fix the issue by either renaming the file back or updating the server block to look for home.html:

```bash
sudo nano /etc/nginx/sites-available/mywebsite
```

Change `index index.php index.html index.htm;` to `index index.php home.html index.htm;`

5. Reload Nginx and test again:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Scenario 2: Permission Issues

1. Change the permissions of your website directory to be too restrictive:

```bash
sudo chmod 700 /var/www/secondsite/html
```

2. Access http://secondsite.local and observe the 403 Forbidden error.

3. Check Nginx's error log:

```bash
sudo tail /var/log/nginx/error.log
```

4. Fix the permissions:

```bash
sudo chmod 755 /var/www/secondsite/html
```

5. Reload and test again.

### Scenario 3: Configuration Syntax Error

1. Introduce a syntax error in one of your Nginx configuration files:

```bash
sudo nano /etc/nginx/sites-available/mywebsite
```

Remove a closing curly brace `}` somewhere in the file.

2. Test the configuration:

```bash
sudo nginx -t
```

You should see an error message.

3. Fix the error by adding back the curly brace.

4. Test again to confirm it's fixed:

```bash
sudo nginx -t
```

## Part 7: Creating a More Robust Reverse Proxy

Let's enhance our reverse proxy configuration to make it more robust and production-ready.

### Step 1: Update the Node.js Proxy Configuration

```bash
sudo nano /etc/nginx/sites-available/nodeproxy
```

Replace the content with a more comprehensive configuration:

```nginx
upstream nodejs_app {
    server 127.0.0.1:3000;
    # If you had multiple Node.js instances, you could add them here:
    # server 127.0.0.1:3001;
    # server 127.0.0.1:3002;
}

server {
    listen 80;
    listen [::]:80;
    server_name nodeapp.local www.nodeapp.local;

    access_log /var/log/nginx/nodeapp.access.log;
    error_log /var/log/nginx/nodeapp.error.log;

    # Redirect all HTTP traffic to HTTPS (for production use)
    # return 301 https://$host$request_uri;

    location / {
        proxy_pass http://nodejs_app;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Timeout settings
        proxy_read_timeout 90s;
        proxy_connect_timeout 90s;
        proxy_send_timeout 90s;
        
        # Buffer settings
        proxy_buffer_size 4k;
        proxy_buffers 4 32k;
        proxy_busy_buffers_size 64k;
        
        # Cache settings (disabled for dynamic content)
        proxy_no_cache 1;
        proxy_cache_bypass 1;
    }

    # For static content, we can serve it directly without proxying
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        root /var/www/nodeapp/public;
        expires 30d;
    }
}
```

Save and exit.

### Step 2: Create a Directory for Static Content

```bash
sudo mkdir -p /var/www/nodeapp/public
sudo chown -R $USER:$USER /var/www/nodeapp
```

### Step 3: Add Some Static Content

```bash
echo "console.log('Static file loaded!');" > /var/www/nodeapp/public/static.js
```

### Step 4: Test and Reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 5: Update Your Node.js App to Reference the Static File

In a new terminal:

```bash
cd ~/nodeapp
nano index.js
```

Update the HTML to include the static JS file:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send(`
    <html>
    <head>
      <title>Node.js Application</title>
      <style>
        body { font-family: Arial, sans-serif; margin: 40px; line-height: 1.6; }
        h1 { color: #333; }
        .container { border: 1px solid #ddd; padding: 20px; border-radius: 5px; }
      </style>
      <script src="/static.js"></script>
    </head>
    <body>
      <div class="container">
        <h1>Hello from Node.js!</h1>
        <p>This content is being served by a Node.js application through Nginx reverse proxy.</p>
        <p>Server time: ${new Date()}</p>
        <p>Check the console to see a message from the static JS file.</p>
      </div>
    </body>
    </html>
  `);
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});
```

Restart your Node.js application:

```bash
# Press Ctrl+C to stop the current instance
node index.js
```

### Step 6: Test the Enhanced Reverse Proxy

1. Visit http://nodeapp.local in your browser
2. Open the browser developer tools (F12) and check the console
3. You should see "Static file loaded!" which confirms that Nginx is directly serving the static file

## Part 8: Performance Monitoring and Optimization

Let's conclude with some basic performance monitoring and optimization techniques.

### Step 1: Install Tools for Monitoring

```bash
sudo apt install htop apache2-utils -y
```

### Step 2: Enable Nginx Status Page

```bash
sudo nano /etc/nginx/sites-available/status
```

Add the following configuration:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name localhost;

    # Only allow access from localhost
    location /nginx_status {
        stub_status on;
        access_log off;
        allow 127.0.0.1;
        deny all;
    }
}
```

Save and exit.

Enable the configuration:

```bash
sudo ln -s /etc/nginx/sites-available/status /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### Step 3: Monitor Nginx Status

```bash
curl http://localhost/nginx_status
```

You should see basic statistics about Nginx's performance.

### Step 4: Enable Gzip Compression

Edit the main Nginx configuration:

```bash
sudo nano /etc/nginx/nginx.conf
```

Find the gzip settings section and make sure it looks like this:

```nginx
gzip on;
gzip_disable "msie6";
gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_min_length 256;
gzip_types
  application/atom+xml
  application/javascript
  application/json
  application/ld+json
  application/manifest+json
  application/rss+xml
  application/vnd.geo+json
  application/vnd.ms-fontobject
  application/x-font-ttf
  application/x-web-app-manifest+json
  application/xhtml+xml
  application/xml
  font/opentype
  image/bmp
  image/svg+xml
  image/x-icon
  text/cache-manifest
  text/css
  text/plain
  text/vcard
  text/vnd.rim.location.xloc
  text/vtt
  text/x-component
  text/x-cross-domain-policy;
```

Save and exit.

Test and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 5: Performance Testing

Let's do a simple performance test using Apache Bench:

```bash
ab -n 1000 -c 10 http://mywebsite.local/
```

This command will make 1000 requests with 10 concurrent connections.

## Conclusion

Congratulations! You've successfully:

1. Installed and configured Nginx as a web server
2. Created multiple websites using virtual hosting
3. Set up a complete LEMP stack (Linux, Nginx, MySQL, PHP)
4. Configured Nginx as a reverse proxy for a Node.js application
5. Learned how to troubleshoot common issues
6. Implemented performance optimizations

This tutorial has given you hands-on experience with many of the key concepts in web server administration. You now have a solid foundation to build upon for more advanced configurations and deployments.

## Next Steps

Here are some suggestions for further exploration:

1. Set up SSL/TLS with Let's Encrypt for secure HTTPS connections
2. Configure server caching for better performance
3. Implement fail2ban for basic security
4. Learn how to set up server monitoring with tools like Prometheus and Grafana
5. Explore containerization using Docker with Nginx as a reverse proxy
