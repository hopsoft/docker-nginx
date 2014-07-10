# Trusted Docker Image for Nginx

This image contains a basic install of nginx-extras.

## Setup

Create an nginx configuration directory on the host at `/etc/nginx`,
then configure nginx to meet your needs.

You can easily create the default configuration like so.

```
mkdir /etc/nginx
git clone https://github.com/hopsoft/docker-nginx.git
cp -R ./docker-nginx/nginx_conf/* /etc/nginx
rm -rf ./docker-nginx
```

## Starting the container

```sh
sudo docker run --name nginx -d -p 80:80 -p 443:443 -v /var/log -v /etc/nginx:/etc/nginx:r hopsoft/nginx
```

__Note__: If you are reverse proxying to a containerized app,
be sure to look at the following Docker features.

- [Linking Containers](https://docs.docker.com/userguide/dockerlinks/) - so you can dynamically reference your app container's assigned ip address etc...
- [Data Volumes](https://docs.docker.com/userguide/dockervolumes/) - specifically `--volumes-from` so you can share volumes from your app container

For example, here's how I'm starting the nginx container for one of my apps.

```
# start the app
sudo docker run -d --name app -v /path/to/app -p 5000:5000 namespace/app

# start nginx
sudo docker run --name nginx -d --link app:app -p 80:80 --volumes-from app -v /etc/nginx:/etc/nginx:r hopsoft/nginx
```

This allows a simple nginx app config.

```
# /etc/nginx/sites-enabled/app.conf
upstream app {
  server app:5000;
}

server {
  listen 80;
  server_name www.example.com;
  root /path/to/app;
  index index.html index.htm;

  location / {
    proxy_pass  http://app;
    proxy_set_header Host $http_host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_next_upstream error timeout invalid_header;
  }

  location ~* ^/assets/ {
    expires 1y;
    add_header Cache-Control public;
    add_header Last-Modified "";
    add_header ETag "";
    break;
  }
}
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

