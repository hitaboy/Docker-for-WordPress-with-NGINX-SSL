# Docker for WordPress with NGINX+SSL and MySQL Database.

Based on a [ronamosa](https://github.com/ronamosa/) [repo](https://github.com/ronamosa/docker-wordpress-nginx-ssl-mysql)

## Description
A dockerized wordpress setup with an NGINX reverse-proxy frontend doing the SSL termination, and a standard MySQL database backend.

## Pre-requisites
* docker installed locally
* docker-compose installed locally

## Setup
You'll need to do a few things to get this up & running on your local environment.

1. create your certs by going into 'certs/' and running `certs.sh`.
2. copy the `certs/my_wpress_site.cert` and `certs/my_wpress_site.key` to 'nginx/ssl/'.
3. edit nginx/conf.d/default.conf and chamge server_name to the desired domain.
4. edit docker-compose.yml and change the container_name. Swap `-prova` with your project identifier. 
5. run `docker-compose up` if you want to see all the start-up logs
6. or `docker-compose up -d` if you want to background.

## Setup Wordpress
As this was a local setup for me, the server_name of 'www.mywordpress.local' was just an entry in my local /etc/hosts.
```
127.0.0.1 www.mywordpress.local
```

* open a browser page to https://www.whateveryoursiteis.com and you should be greeted (if this is the first time running) with an page asking you to install wordpress.

## Key Points

this 'wordpress'
```
proxy_pass http://wordpress;
```
refers to the wordpress service in your docker-compose.yml file.

this part in your default.conf:
```
        proxy_set_header      Host $host;
        proxy_set_header      X-Real-IP $remote_addr;
        proxy_set_header      X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header      X-Forwarded-Host $server_name;
        proxy_set_header      X-Forwarded-Proto https;

```
helps this part in your wp-config.php to work:
```
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
        $_SERVER['HTTPS'] = 'on';
}
```
this is where we're trying to force HTTPS at all times. The 301 redirect in default.conf for requests to port 80 also helps.

## Chrome/Catalina Certificate Issue
If you are having issues trying to reach a self signed website, router or esxi host, with an error:  NET::ERR_CERT_REVOKED . You will notice you cannot ignore and proceed in the new Mac OSX Catalina release!

Solution: In the chrome browser whilst on the page, type “thisisunsafe”
