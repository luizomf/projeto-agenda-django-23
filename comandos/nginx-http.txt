# https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/
#
# REPLACES
# __YOUR_DOMAIN_OR_IP__ = Replace with your domain
# __APP_ABSOLUTE_PATH__ = Replace with the path to the folder for the project
# __STATIC_ABSOLUTE_PATH__ = Replace with the path to the folder for static files
# __MEDIA_ABSOLUTE_PATH__ = Replace with the path to the folder for media files
# __GUNICORN_FILE_NAME__ = Replace with your unix socket name (don't add .socket)
# 
# Set timezone
# List - timedatectl list-timezones
# sudo timedatectl set-timezone America/Sao_Paulo
#
# HTTP
server {
  listen 80;
  listen [::]:80;
  server_name __YOUR_DOMAIN_OR_IP__;

  # Add index.php to the list if you are using PHP
  index index.html index.htm index.nginx-debian.html index.php;
  
  # ATTENTION: __STATIC_ABSOLUTE_PATH__
  location /static {
    autoindex on;
    alias __STATIC_ABSOLUTE_PATH__;
  }

  # ATTENTION: __MEDIA_ABSOLUTE_PATH__ 
  location /media {
    autoindex on;
    alias __MEDIA_ABSOLUTE_PATH__;
  }

  # ATTENTION: __GUNICORN_FILE_NAME__
  location / {
    proxy_pass http://unix:/run/__GUNICORN_FILE_NAME__.socket;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }

  # deny access to .htaccess files, if Apache's document root
  # concurs with nginx's one
  #
  location ~ /\.ht {
    deny all;
  }

  location ~ /\. {
    access_log off;
    log_not_found off;
    deny all;
  }

  gzip on;
  gzip_disable "msie6";

  gzip_comp_level 6;
  gzip_min_length 1100;
  gzip_buffers 4 32k;
  gzip_proxied any;
  gzip_types
    text/plain
    text/css
    text/js
    text/xml
    text/javascript
    application/javascript
    application/x-javascript
    application/json
    application/xml
    application/rss+xml
    image/svg+xml;

  access_log off;
  #access_log  /var/log/nginx/__YOUR_DOMAIN_OR_IP__-access.log;
  error_log   /var/log/nginx/__YOUR_DOMAIN_OR_IP__-error.log;
}