# Trusted Docker Image for Nginx

This image contains a basic install of nginx-extras.

## Setup

Create an nginx configuration directory on the host at: `/etc/nginx`
Configure nginx to meet your needs.

You can find a complete example of the [default nginx config here](https://github.com/hopsoft/docker-nginx/tree/master/nginx_conf).

## Starting the container

```sh
sudo docker run --name nginx -d -p 80:80 -p 443:443 -v /etc/nginx:/etc/nginx hopsoft/nginx
```

## Reconfigure & Restart

```
vim /etc/nginx/nginx.conf
vim /etc/nginx/sites-enabled/YOUR_SITE.conf
docker restart nginx
```

## Building the image

__Note__: This is only for those wanting to build the image themselves.

```
git clone https://github.com/hopsoft/docker-nginx.git
cd docker-nginx
vagrant up
sudo docker build -t hopsoft/nginx /vagrant
```

