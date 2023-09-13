# How to Expose the Nextloud Instance to the Internet

## Step 1: Install nginx proxy manager

1. Create a directory in the docker directory (change the directory names if you find them confusing)

```bash
cd docker && mkdir npm && cd npm
```

2. Create a new yml file and add the following contents to it (read the comments)

```bash
nano docker-compose.yml
```

```yml
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:     # Change the ports if they are already used on your network
      - '80:80' 
      - '81:81'  # Remember this as it will be used for opening the dashboard
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt

networks:
  default:
    name: my-main-net
    external: false # Change based on your network (trail and error if not sure)
```

3. Create the container

```bash
docker-compose up -d
```

4. Open the dashboard in your browser and change your mail and password

```textile
http://<your server ip address>:81/login #change the port if you changed it in the compose file
# default mail: admin@example.com
# default password: changeme
```

## Congrats! You now have nginx proxy manager up and running

## To continue this tutorial you need to have a domain name tied to a static ip or dynamic dns. If you do not have this refer to "setting up DDNS""  and "managing and buying domin name" tutorials before you continue.

## Step 2: Accessing nextcloud via your domain name

1. Forward the following ports on your router (How to do so is different for every router)

```textile
80
443
3478 # for nextcloud talk only
3012 # for nextcloud talk only
```

2. Create a new proxy host using nginx proxy managerny adding your chosen domain name along with the server ip and the port used for managing nextcloud.

3. Add an SSL certificate by editing the proxy host. Go to the SSL tab, request a new let's encrypt certificate if ypu do not have one and enable all the security options. (optional)

## Step 3: Add your domain name to the trusted domains list in the next cloud config

1. Access the container files

```bash
docker ps # to find the container name or id
docker exec -it <container_name_or_id> /bin/bash
```

2. install a text editor if ther is not one

```bash
apt update
apt install nano
```

3. Navigate to the directory that includes the config file amd open it

```bash
cd /var/www/html/config
nano config.php
```

4. Add your domain to the trusted domains array

example before editing:

```php
'trusted_domains' =>
array (
  0 => 'localhost',
),
```

example after editing:

```php
'trusted_domains' =>
array (
  0 => 'localhost',
  1 => 'yourdomain.com',
),
```

5. exit the container and restart it (you might not need to restart)

```bash
exit
docker restart <container_name_or_id>
```

# Congrats! Write your domain name in your browser from any network and you will be able to access your nextcloud istance.
