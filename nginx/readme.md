# NGINX CONF
## Installation on ubuntu server
```
sudo apt update
sudo apt install nginx
```

## Installation specific nginx version on ubuntu
[Official Nginx Documentation](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)

```
### 1. Install the prerequisite

sudo apt update && \
sudo apt install curl \
                 gnupg2 \
                 ca-certificates \
                 lsb-release \
                 debian-archive-keyring

### 2. Import official signing key 

curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
     | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

### 3. Setup for source of repository branch

## For stable

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list


## For mainline

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/mainline/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list  

## 4. Set to use official repository instead of the distro 

echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
   | sudo tee /etc/apt/preferences.d/99nginx

sudo apt update && \
sudo apt install nginx

```

## Basic Auth for nginx Vhost
[Official Nginx Documentation](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/)

### Prerequisites

```shell
sudo apt install apache2-utils 
```

### Generate the password files
```shell
sudo htpasswd -c /location/of/the/.files username
```

### Nginx configuration for basic Authentication
### For The Whole Vhost
```nginx
server {
    ...
    auth_basic           "Administrator’s Area";
    auth_basic_user_file conf/htpasswd;

    location / {
    }
}
```
### For Location 
```nginx
location /endpoint {
    auth_basic           "Administrator’s Area";
    auth_basic_user_file /location/of/the/.files;
}
```


## Vhost Configuration Template

```
server {
    server_name your.domain.cloud;

    #Log
    access_log /var/log/nginx/your.domain.cloud.access.log;
    error_log /var/log/nginx/your.domain.cloud.error.log warn;

    location / {
        proxy_pass http://app_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Connection "";
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
    }

    location /location/ {
        proxy_pass http://app_backend2/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Connection "";
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
    }

    listen 443 ssl;
    ssl_certificate /etc/nginx/ssl/your-custome.crt;
    ssl_certificate_key /etc/nginx/ssl/your-custome.key;

}
server {
    if ($host = your.domain.cloud) {
        return 301 https://$host$request_uri;
    } 


    listen 80;
    server_name your.domain.cloud;
    return 404; 


}
```

