# Managing SSL Certificates with Docker and Certbot

![website](/images/website.jpg)

I launched a WordPress site with SSL encryption using Certbot for certificate management. My docker-compose.yml configuration includes WordPress, MariaDB, Nginx and Certbot. 

This process shows how to renew your SSL certificate and setting up an automated renewal system. I used this method to renew an expired LetsEncrypt certificate for my [website](https://freecloudcredits.com/). LetsEncrypt certificates are valid for 90 days therefore without a renewal system in place, they expire and users will be met with a warning sign because the site is unencrypted.

### 1. Running the Certbot Container for Renewal

Since I use Certbot within Docker, I ran my Certbot container which is configured in my docker-compose.yml. This container handles the SSL certificate renewal. I ran the following comment which checks all certificates managed by Certbot and renews those that are near expiry:


```bash
docker-compose run certbot renew
```
### 2. Restarting the Nginx Web Server

After renewing the certificate, I needed to find out the nginx container's exact ID. I used the following command to list all running containers:

```bash
docker ps
```

### 3. Restart Nginx Container

To ensure that Nginx started using the renewed certificate, I restarted my Nginx container. I replaced [container-id-nginx] with the actual container ID of my Nginx container as listed by docker ps.

```bash
docker restart [container-id-nginx]
```

### 4. Setting Up Automatic Renewal with Cron Job

To keep my certificates up to date, I set up a cron job to automate the renewal process:

```bash
crontab -e
```

I added the following line to run the renewal check every day at 2 AM:
```cron
0 2 * * * /usr/local/bin/docker-compose -f /wordpress/docker-compose.yml run certbot renew
```

Replace /wordpress/docker-compose.yml is the path to my docker-compose.yml file. Change accordingly.

### Next steps

* Setup SSL monitoring tool.