# The NGINX Reverse Proxy Manager

NGINX proxy manager is a reverse proxy management system, that is based on NGINX
with a nice and clean web UI. You can also obtain trusted SSL certificates,
manage several proxies with individual configs, customizations, and intrusion
protection. It is open-source and maintained GitHub. It’s perfect for small
environments like home labs or small server environments. You can easily expose
any web service or application with it. It includes a free SSL certificate
management by letsencrypt that can also be used to obtain wildcard certs. To
deploy the NGINX proxy manager you need a small MySQL database container and the
NGINX container. Let’s have a look at how to deploy this easily with
docker-compose.

## Create a DNS A record for your sub domain that points to your server

Create a new A record on my server that is called “proxy.example.com” that
points to the public IP of the virtual server.

## Deploy NGINX Proxy Manager in docker

Create the docker-compose file. Create the new project folder in the `/opt
directory` called `nginxproxymanager`. Then I create a docker-compose.yaml file
with the following template.

```yml
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    restart: always
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: ${USERNAME}
      DB_MYSQL_PASSWORD: ${PASSWORD}
      DB_MYSQL_NAME: ${MYSQLNAME}
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
  db:
    image: 'jc21/mariadb-aria:10.4'
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${ROOTPASSWD}
      MYSQL_DATABASE: ${DATABASE}
      MYSQL_USER: ${USERNAME}
      MYSQL_PASSWORD: ${PASSWORD}
    volumes:
      - ./data/mysql:/var/lib/mysql
```

### The Password file

Store passwords in plain text is not a good idea. Create `.env` file with the
following content:

```yml
USERNAME=proxy
PASSWORD=supergeheim
MYSQLNAME=npm
ROOTPASSWD=rootsupergeheim
DATABASE=npm
```

**Notice:** Please change the names and take strong passwords, not npm.

### Start the NGINX Proxy Manager stack with the following command.

```sh
docker-compose up -d
```

## Login to the web UI of NGINX Proxy Manager

Now we can log in to the web UI. Simply use your browser to connect to your
server by using the IP address or an FQDN and connect on port “81”. Log in with
the username `admin@example.com` and the password `changeme`. Next, you should
change your username and password, and that’s it!

## Secure the NGINX Proxy Manager

If the NGINX Proxy Manager, Docker and Docker Compose are installed on the same
server, the internal IP address is required.

```sh
ip addr show docker0
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
link/ether 02:42:5d:50:ff:be brd ff:ff:ff:ff:ff:ff
inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
valid_lft forever preferred_lft forever
```
**The IP Address is:** `172.17.0.1`

## Set up an new Proxy Host

Configure the details.

<img src="images/new-proxy.png" alt="New Proxy Host">

## Set a Let's Encrypt certificate

Change to SSL.

<img src="images/proxy-ssl.png" alt="Proxy SSL">

## The result of the configuration

The configuration details.

<img src="images/proxy-hosts.png" alt="Proxy Hosts">

