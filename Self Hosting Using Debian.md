# Self Hosting Using Debian

## Step 1: Updating the OS

- Run the following code:

```bash
sudo apt update && sudo apt upgrade 
```

## Step 2: Add your user in the sudoers group and enable password-less sudo

1. Switch to root user and enter the root password

```bash
su root    
```

2. Access the sudoers file

```bash
sudo visudo
```

4. locate the section for User Privilleges and your user

```bash
# User privilege specification
root ALL=(ALL:ALL) ALL
<your username> ALL=(ALL) NOPASSWD: ALL
```

Step 3: Install ssh server

1. Run the following code:

```bash
sudo apt install openssh-server        
```

2. Determine the Ip address of the machine (here the ip address is 192.168.1.36) and connect to it via ssh

```bash
ip a

example output:
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cf:33:ac brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.36/24 brd 192.168.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 85944sec preferred_lft 85944sec
    inet6 fe80::a00:27ff:fecf:33ac/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

```text
example output:
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cf:33:ac brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.36/24 brd 192.168.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 85944sec preferred_lft 85944sec
    inet6 fe80::a00:27ff:fecf:33ac/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

3. Open a terminal window on the other machine and write

```bash
ssh <username>@<the device ip>
##example:
ssh moahmed@192.168.1.243
## you will be prompted to enter a password, enter the password of the user you are connceting to
```

## Step 4: Disable root ssh Access

1. Open the SSH daemon configuration file

```bash
sudo nano /etc/ssh/sshd_config
```

2. Locate the premit root login and turn it to "no"

```bash
# How it might look
PermitRootLogin yes
# After changing value
PermitRootLogin no
```

## Step 5 install docker and docker compose

1. Run the following code to install docker:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```

2. Test your docker installation

```bash
sudo docker run hello-world
```

3. Install the latest version of docker compose and verify that it is installed

```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

4. Add your user to the docker group

```bash
sudo usermod -aG docker <username> # add the user tp the docker group
su - <username> # log out and log back in
groups # verify by making sure that docker shows up when you run this command
```

## Step 5: Install nextcloud

1. Create a nextcloud directory (The directory structre is optional) and create a docker-compose yaml file

```bash
mkdir docker && cd docker && mkdir nextcloud && cd nextcloud && nano docker-compose.yml
```

2. Write this into the yml file ( change the passwords )

```yml
version: '3'

services:
  nextcloud:
    image: nextcloud
    container_name: nextcloud
    restart: unless-stopped
    ports:
      - 8080:80     # Change the port if port 8080 is already used up
    volumes:
      - ./nextcloud:/var/www/html
    environment:
      - MYSQL_HOST=mysql
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=test123 # Change this password
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: unless-stopped
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=test123 # Change this password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=test123 # Change this password

volumes:
  db:
```

3. Run The container
   
   ```bash
   docker-compose up -d
   ```

4. Open it in the browser and create the username and password for the main user

```textile
http://<the server IP address>:8080 #change the port if you changed it in the compose filr 
```

5. Choose If you want to install thier extra apps or not

# Congrats! You now have nextcloud up and running and accessible from your LOCAL network. The next guide is on how to access it via the internet
