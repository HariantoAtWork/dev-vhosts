Build Docker (Alpine) Nginx PHP7 Vhosts for development
=======================================================

Create a development vhosts (virtual host) system with Docker (Nginx and PHP7) on Mac.

A containerized services without polluting your own system. Without installing LAMP or Brew.

> After watching DockerCon2016 couple days ago. I feel like experimenting and trying to create a workflow for my future deployments.



Getting started
---------------


Download [Docker](https://www.docker.com/products/docker#mac) for Mac

file: ./docker-compose.yml
```yml
version: '2'

services:
  nginx:
    image: nginx:alpine
    links:
      - fpm
    ports:
      - "8080:80"
    volumes:
      - ./nginx/enabled:/etc/nginx/conf.d
      - ./nginx/snippets:/nginx/snippets
    volumes_from:
      - app

  fpm:
    image: php:7.0.8-fpm-alpine
    volumes_from:
      - app

  app:
    image: busybox
    volumes:
      - ./vhosts:/vhosts
```

To run

```bash
docker-compose up
```

> directories will automatically created on your host



Default Nginx Config
--------------------


file: ./nginx/snippets/php7.0.8-fpm.conf
```nginx
    location ~ \.php(/|$) {
        try_files      $uri = 404;
        
        fastcgi_pass fpm:9000;
        fastcgi_index  index.php;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;

        include fastcgi_params;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTPS off;

        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
    }
```

file: ./nginx/enabled/default.conf
```nginx
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /vhosts/_default_/httpdocs;

	index index.html index.htm index.php;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ /index.php$is_args$args =404;
	}

	include /nginx/snippets/php7.0.8-fpm.conf;
}
```

file: ./vhosts/_default_/httpdocs/index.html

```nginx
My Default Page.html
```

> to check the site: http://localhost:8080



Create a example.com.dev in your configuration
----------------------------------------------


file: ./nginx/enabled/example.com.conf

```nginx
server {
    disable_symlinks off;

    listen  80;
    server_name ~^example\.com(\.dev)?$;

    root /vhosts/example.com/httpdocs;

    autoindex on;
    index index.html index.php;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    include /nginx/snippets/php7.0.8-fpm.conf;
}

server {
    disable_symlinks off;
    
    server_name ~^((?<subdomain>.*)\.)example\.com(\.dev)?$;

    root /vhosts/example.com/subdomains/${subdomain}/httpdocs;

    autoindex on;
    index index.html index.php;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    include /nginx/snippets/php7.0.8-fpm.conf;
}
```

file structure
```default

./vhosts
  ./example.com
    ./httpdocs
      - index.html
    ./subdomains
      ./test
        /httpdocs
         - index.html
```


> to check the site: http://example.com.dev:8080
> to check subdomain: http://test.example.com.dev:8080


Setup your .DEV domains with Dnsmasq
------------------------------------

Tutorial [here](/setup-local-wildcard-domains/)