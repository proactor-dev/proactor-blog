# Deploy Rust Web App on Virtual Private Server

This article discusses deploying Rust based web app on Virtual Private Server (VPS). In this example Debian 11 linux based VPS is used as a target machine for deployment and Apache server as a web server.

It is expected that Rust based web application exists on the git server that will be pulled and installed on the VPS. You can use your own, or follow the instructions on our [previous article](1-rust-hello-world-web-app.md) to create simple Hello World! application.

This article is based on this [post](https://steadylearner.medium.com/how-to-deploy-rust-web-application-8c0e81394bd5) about deploying Rust web application on DigitalOcean server with Nginx. In this article we will using propriotary Virtual Private Server running Apache, which is going to be used as a web server instead of Nginx.

## Prerequisite: SSH to your Virtual Private Server
In order to setup you application on remote host, you need an SSH connection in order to login to your Virtual Private Server (VPS). Check you VPS provider for details if you don't know how.
```console
$ ssh <username>@<hostname>
```
> Note: All the command line actions below are done on your remote VPS host, which runs Debian 11 on our case. You may check for platform dependent differences if working on some other operating system.

## Step 1. Install Git and Rust

Once logged in to your VPS. First thing is to update system packages.

### Update system packages
```console
$ sudo apt update
$ sudo apt upgrade
```

### Install Git
Git will be needed to download web application from the repository. This is needed, if you have a ready source code available in your repo. For this guide it's not mandatory, since there is another option to create web app locally without using existing repo.
```console
$ sudo apt install git
$ git --version # Verify that install succeeded
git version 2.30.2
```
### Generate and associate SSH key for accessing Github
If you are using Github as a repository, you need to enable SSH based authentication.

First lets generate an SSH key:
```console
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "<your github email address>"

Generating public/private rsa key pair.
Enter file in which to save the key (/home/<username>/.ssh/id_rsa): <press enter>
Enter passphrase (empty for no passphrase): <press enter>
Enter same passphrase again: <press enter>
Your identification has been saved in /home/<username>/.ssh/id_rsa
Your public key has been saved in /home/<username>/.ssh/id_rsa.pub
```
Add the generated key to your Github account. This is done from Github account settings by clicking "SSH and GPG keys", and then clicking "New SSH key".

Copy the contents of your id_rds.pub file. To see contents of the file type:
```console
$ cat ~/.ssh/id_rsa.pub
```
Paste the contents to Github form and submit. You should have now your public SSH key stored to Github account.

For more details please check [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

### Install Rust using Rustup

Rustup is a toolchain manager for Rust and installs Rust compiler _rustc_ and package manager _cargo_.

For detailed platform specific instructions, please see: [Rust installation page](https://www.rust-lang.org/tools/install).
```console
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Add Rust to path. Rustup tries to add this (or similar) line to your starrup scipt _.bashrc_ in our case, so that Rust is automatically visible on PATH next time you login to system.
```sh
source "$HOME/.cargo/env"
```

Verify that Rust compiler is functional
```console
$ rustc --version
rustc 1.74.1 (a28077b28 2023-12-04)
```

If everything is ok, next step is to install and run Rust web application. In this example we will be using Rust Rocket framework for this.

## Step 2. Install Rust Rocket Web App

Next let's create Rust Rocket web app for experimental purposes.

### Option A. Download Rust Rocket app from Github
We will be using Git to download Rocket web app from Github that we created in our [previous article](1-rust-hello-world-web-add.md).

Alternatively you can follow **Option B.** and create new web app locally, if you don't have ready app to experiment with.

Let's download app from Github.
>Note we are using private repo in this example, so you should use your own repo or follow Step 2, Option B. below.
```console
$ git clone [YOUR-REPOSITORY.git]
```
If everything goes ok, you should now have your Rust Rocket web application cloned on your server. Congratulations! Next step is to run the app in order to verity it runs locally.

### Option B. Create new Rust Rocket Web Application
In case you do not have ready web application to deploy, you can create simple one locally.

We are not going to reproduce the steps needed here, but you can refer to our [previous article](1-rust-hello-world-web-add.md) and more specificly Steps 1 and 2.

In case you run in to problems please check Rust Rocket official documentation for [getting started guide](https://rocket.rs/v0.5/guide/getting-started/).

## Step 3. Test running the Web App locally
Next lets try running our newly installed Rust Rocket web application locally.

Todo this lets cd to correct folder and first check everything is ok with our with `cargo`:
```console
$ cd rocket-poc
$ cargo check # Checks the current application and packages
```
If problems arise, please try to solve those first before running the application. If you are working in the fresh bare minimum environment you might for example be messing C compiler, which is needed by rust.
```console
error: linker `cc` not found
```
In such case install build tools by typing:
```console
$ sudo apt install build-essential
```
...and try to run `cargo check` again.

When everything is ok, and `cargo` was able to install packages you are ready to run the app. Let's run the app with the --release flag in order to create proper application binaries for later use.
```console
$ cargo run --release
```
If everything went as expected, you should now have your application operational and Rocket promts the successfull launch on terminal with:
```sh
🚀 Rocket has launched from http://127.0.0.1:8081
```
The IP and port may be different on your case depending on your Rocket application configurations.

Congratulations! This ends the first part of the article. Next we will make Rocket application run as a service and configure Apache to serve Rocket application to make it available from the Internet.

## Step 3. Make Rust Application run as a service

Let's make our Rust Rocket web application run as a systemd service, so that it runs even if the terminal is closed and restarts when systems is restarted.

First go to systemd service directory
```console
$ cd /etc/systemd/system
```

Create service file with your favourite editor (nano in our case) and name it with suitable name eg.: `sudo nano rocket-poc.service`.

Add following content to the services file:
```console
[Unit]
Description=Rust Rocket Web Application PoC
[Service]
User=www-data
Group=www-data
WorkingDirectory=/home/yourname/yourwebsite/yourproject/
Environment="ROCKET_ENV=prod"
Environment="ROCKET_ADDRESS=0.0.0.0"
Environment="ROCKET_PORT=8081"
Environment="ROCKET_LOG=critical"
ExecStart=/home/yourname/yourwebsite/yourproject/target/release/yourproject
[Install]
WantedBy=multi-user.target
```

Once done, you should be able to start your newly created service by typing: `sudo systemctl start rocket-poc.service`.

Let's check if our application is functional by using curl:
```console
$ curl http://0.0.0.0:8081/
Hello, world!
```
It works! Now only thing remaining is to make this url and port visible to the Internet by making Apache act as a reverse proxy server. We'll do that in the next chapter.

However before configuring Apache, couple of additional words about systemd services.

To check that the systemd service is running we can type:
```console
$ ps -aux | grep 'rocket'
www-data   30505  0.0  0.2  75580  4152 ?        Ssl  22:27   0:00 /home/yourname/yourproject/target/release/yourproject
```
Even better way to check service status is to use `systemctl status rocket-poc` command.

Let's enable service so that it's operational even after system restart by typing:
```console
$ sudo systemctl enable rocket-poc.service
```

And finally in case we would like to stop running the systemd service we can type: `sudo systemctl stop rocket-poc.service` or if we want to disable service from automatically restarting `sudo systemctl disable rocket-poc.service`.

## Step 4. Configuring Apache server to work with our Rust web Application

We will use a reverse proxy setup to make Apache serve our Rust web application content.

If not yet installed you can install Apache by typing:
```console
$ sudo apt install apache2
```

Next we need to make sure that Apache `proxy` and `proxy_http` modules are enabled:
```console
$ sudo a2enmod proxy
$ sudo a2enmod proxy_http
```

Following step is to introduce new site configuration for Apache server.
```console
$ sudo nano /etc/apache2/sites-available/your-app.conf
```
Replace `your-app` with the proper name of your application.

Add following content to `your-app.conf`config file:
```apache
<VirtualHost *:80>
    ServerName your-app.domain.com

    ProxyPass / http://localhost:8081/
    ProxyPassReverse / http://localhost:8081/
</VirtualHost>
```
Again, replace `your-app` with the proper name of your app and specify the right port for your Rust application (8081 in our case)

The above configuration works for the case where Rust application will run on subdomain. In case main domain is used replace ServerName with something like `your-app.com`, where your-app refers to your domain name.

After the subdomain config file is saved, the subdomain needs to be enabled by typing:
```console
$ sudo /usr/sbin/a2ensite your-app.conf
```
If everything is ok with your configuration file, the script will echo:
```console
Enabling site your-app.
To activate the new configuration, you need to run:
  systemctl reload apache2
```

Finally let's restart Apache service as instructed above in order for our changes (and our newly configured site) to become active:
```console
$ sudo systemctl reload apache2
```

Let's check, that our newly configured site is functional with `curl`:
```console
$ curl your-app.domain.com
Hello, world!
````

Seems to work! If you got this far, you have now functional Rust Rocket web application on your subdomain. Congratulations!

If however, like in our case, server requires secure https access, there is still one more final step to open the application for public use. That is setting up the certificate and virtual host for https access.

We will be using certbot to create a subdomain certificate for us. This can be easily done by typing:
```console
$ sudo certbot --apache -d your-app.domain.com
```
or simply:
```console
$ sudo certbot
```
and selecting site from the list provided.

This creates following verbose output in case certificate creation is successful:
```console
Requesting a certificate for your-app.domain.com
Performing the following challenges:
http-01 challenge for your-app.domain.com
Waiting for verification...
Cleaning up challenges
Created an SSL vhost at /etc/apache2/sites-available/your-app-le-ssl.conf
Deploying Certificate to VirtualHost /etc/apache2/sites-available/your-app-le-ssl.conf
Enabling available site: /etc/apache2/sites-available/your-app-le-ssl.conf
Redirecting vhost in /etc/apache2/sites-enabled/your-app.conf to ssl vhost in /etc/apache2/sites-available/your-app-le-ssl.conf

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled https://your-app.domain.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/your-app.domain.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/your-app.domain.com/privkey.pem
   Your certificate will expire on 2024-03-19. To obtain a new or
   tweaked version of this certificate in the future, simply run
   certbot again with the "certonly" option. To non-interactively
   renew *all* of your certificates, run "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

So the certbot created us necessary certificates and also updated Apache virtual host config files for https access.

Now we are ready and finally site is visible for external users at address: https://your-app.domain.com!

Still couple of final hints and then we are done.

1. To list existing certificates managed by certbot type: `sudo certbot certificates`
2. To renew all Let's encrypt certificates type: `sudo certbot renew`. To ensure Apache is using latest certificates type: `sudo systemctl restart apache2` after renewing certificates.

This ends our quite lengthy article how to deploy Rust Rocket web application on Virtual Private Server. Congratulations if you got this far!

In the next article we will discuss how to add continuous development pipeline in order to run live updates to our demo application. 