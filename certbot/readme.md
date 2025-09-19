# CERTBOT SSL 

## Installation using snap
[Official Certbot Documentation](https://certbot.eff.org/instructions?ws=nginx&os=snap)


```shell
sudo snap install --classic certbot
```

## Prepare certbot command
```shell
sudo ln -s /snap/bin/certbot /usr/bin/certbo
```
## Get certificate only

```shell
sudo certbot certonly --nginx
```

## get and install cert on nginx 
```shell
sudo certbot --nginx
```

## Get ssl using CLOUDFLARE
[Install Wildcard SSL Certificate with Cloudflare](https://joshuahidden.com/tutorials/installwildcard-ssl-with-cloudflare/)

### Allow plugin in certbot

```shell
sudo snap set certbot trust-plugin-with-root=ok
```

### Install cloudflare DNS Plugin 
```shell
sudo snap install certbot-dns-cloudflare
```

### Generate API Token from Cloudflare
Log in to the Cloudflare dashboard and click on My profile -> API Tokens.

You’ll need to create a token with Edit zone DNS permission.

![alt text](https://joshuahidden.com/wp-content/uploads/2024/02/cloudflare-api-token-certbot.png)


You should restrict the Zone Resources to a specific zone. You can also restrict the IP address with the Client IP Address Filtering option.

### Create cloudflare config file 
It is up to you where you want to create the file but as an example, you may want to create the file in /root/.secrets/cloudflare.ini . The contents of the configuration file should include your Cloudflare token.
```shell
dns_cloudflare_api_token = <cloudflare_api_token>
```
Protect configuration file 
```shell
sudo chmod 0700 /root/.secrets/

sudo chmod 0400 /root/.secrets/cloudflare.ini
```

### Generate SSL 

```shell
sudo certbot certonly \
  --cert-name example.com
  --dns-cloudflare \
  --dns-cloudflare-credentials /root/.secrets/cloudflare.ini \
  -d example.com,*.example.com
```
Your certificates will be saved to /etc/letsencrypt/live/example.com/

The --cert-name parameter is optional and denotes the folder name of the certificate. If omitted, the command will generate its own name (example.com-0001).
