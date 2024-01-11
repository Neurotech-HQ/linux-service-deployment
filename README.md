# Linux service deployment

To start you have to know how to run your service, regardless of the programming lamhuage used to produce it.

Note: Create a production build for the service.

## Prerequisite

- The box[virtual machine] to run the service in.
- DNS settings pointing the domain(subdomain) Alias(A) to the ip address of the box.
    Example test.yourdomain.com    A   188.34.98.87

- install nginx in the server, We will be using it as a reverse proxxy.

### Getting Started

Do what you do to move the files to the production server.

TIP: You could use the scp to move file to remote server or just use git.

Showing an example on how to move files to remote server using scp asssuming you have the keys to access the machine.

After compressing the file to the build.zip file. Continue to move it to the remote server.
Change the file and key loactions as per your environment.

``` bash

scp -P 22 /Users/karage/Documents/build.zip -i "your-key-pair.pem" ubuntu@ec2-3-93-879-61.compute-1.amazonaws.com:/home/ubuntu

```

### Creating the service

Depending on the cpu resource you want the service to have, all those can be tweaked in the service file.
I have provided a sample file for python projects and running binary files.

create a service in the folder /etc/systemd/system named according to your view and imaginations.

``` bash

sudo touch /etc/systemd/system/nameoftheservice.service

```

Edit the service, using your editor of choice

``` bash

sudo vi /etc/systemd/system/nameoftheservice.service

```

Sample of the servicves can be seen in the folder `services`

## Running the service

```bash

sudo systemctl start  nameoftheservice

```

## Enable the service

This starts the service when the server restarts after a crash or manually rebooting the server.

```bash

sudo systemctl enable  nameoftheservice

```

### Configuring the Nginx

After the service is running, hopely you know what port the services is running in.

Key things to note is the port the service is running in and the domain you want to associate the service with.

## Create an nginx conf for the service

I will use a different conf for every service, to decouple the nginx conf with all service configuration files. This helps in uinderstanding the flow and debugging.

Create a file in the /etc/nginx/sites-enabled/ folder

Name it uniquely as you can and associative to the service.

``` bash

sudo touch /etc/nginx/sites-enabled/yourservice

```

The templates for the service will be found over the folder nginx.

Edit the file as per the service requirements. Note to change the `server_name` and the port on the proxy_pass line.

## Restart NGiNX

After every change you do, to make sure that the nginx sntax is okay, you can run the command below. If it fails make sure you observe the cause of the error and solve it first.

``` bash

sudo nginx -t

```

After all the changes are done and are okay, You can now restart the Nginx server

```bash

sudo service nginx -s reload

```

You can now test the unsecure endpoint that is running your service.
Go to the browser and go to the link of the domain you set in the your DNS settings of your domain.

Example go to: <http://test.yourdomain.com>

### Making it secure

We will use eff certs to secure communications between our server and client accessing it.

## Install snapd

This tool will be used to install the certificates.

``` bash

sudo apt update
sudo apt install snapd

```

## Install certbot

``` bash

sudo snap install --classic certbot

```

Ensure that cerbot command can be run

``` bash

sudo ln -s /snap/bin/certbot /usr/bin/certbot

```

Create certs for the serive and domain you created.

``` bash

sudo certbot --nginx

```

Follow the procedures and answer the few questions follewd and we're done.
