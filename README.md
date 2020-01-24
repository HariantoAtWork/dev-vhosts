NO LONGER MAINTAINED
======================

check: https://github.com/HariantoAtWork/docker-xnmp-vhosts


DEV-VHOSTS
==========


Getting started
---------------

Download [Docker for Mac or Windows](https://www.docker.com).

Run in this directory:

    $ docker-compose up

The app will be running at [http://localhost:8080](http://localhost:8080)

> It can also run at [http://example.com.dev:8080](http://example.com.dev:8080) or [http://test.example.com.dev:8080](http://test.example.com.dev:8080) if you set:<br/>
  `/etc/hosts` with: `127.0.0.1  example.com.dev` and `127.0.0.1 test.example.com`.

> For advanced domain .dev request to 127.0.0.1 use dnsmasq



Nginx Config
------------

* Put Nginx configurations at `./nginx/enabled`
* Put Domains at `./vhosts`


