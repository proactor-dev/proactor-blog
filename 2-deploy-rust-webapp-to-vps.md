# Deploy Rust Web App on Virtual Private Server

This article discusses deploying Rust based web app on Virtual Private Server (VPS). In this example Debian 11 linux based VPS is used as a target machine for deployment and Apache server as a web server.

It is expected that Rust based web application exists on the git server that will be pulled and installed on the VPS. You can use your own, or follow the instructions on our [previous article](1-rust-hello-world-web-app.md) to create simple Hello World! application.

This article is based on this [post](https://steadylearner.medium.com/how-to-deploy-rust-web-application-8c0e81394bd5) about deploying Rust web application on DigitalOcean server with Nginx. In this article we will using propriotary Virtual Private Server running Apache, which is going to be used as a web server instead of Nginx.

## Prerequisite: SSH to your Virtual Private Server
In order to setup you application on remote host, you need an SSH connection in order to login to your Virtual Private Server (VPS). Check you VPS provider for details if you don't know how.
```sh
$ ssh <username>@<hostname>
```
> Note: All the command line actions below are done on your remote VPS host, which runs Debian 11 on our case. You may check for platform dependent differences if working on some other operating system.

## Step 1. Install Git and Rust

Once logged in to your VPS. First thing is to update system packages.

### Update system packages
```sh
$ sudo apt update
$ sudo apt upgrade
```

### Install Git
Git will be needed to download web application from the repository. This is needed, if you have a ready source code available in your repo. For this guide it's not mandatory, since there is another option to create web app locally without using existing repo.
```sh
$ sudo apt install git
$ git --version # Verify that install succeeded
git version 2.30.2
```
### Generate and associate SSH key for accessing Github
If you are using Github as a repository, you need to enable SSH based authentication.

First lets generate an SSH key:
```sh
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
```sh
cat ~/.ssh/id_rsa.pub
```
Paste the contents to Github form and submit. You should have now your public SSH key stored to Github account.

For more details please check [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

### Install Rust using Rustup

Rustup is a toolchain manager for Rust and installs Rust compiler _rustc_ and package manager _cargo_.

For detailed platform specific instructions, please see: [Rust installation page](https://www.rust-lang.org/tools/install).
```sh
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Add Rust to path. Rustup tries to add this (or similar) line to your starrup scipt _.bashrc_ in our case, so that Rust is automatically visible on PATH next time you login to system.
```sh
source "$HOME/.cargo/env"
```

Verify that Rust compiler is functional
```sh
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
```sh
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
```sh
$ cd rocket-poc
$ cargo check # Checks the current application and packages
```
If problems arise, please try to solve those first before running the application. If you are working in the fresh bare minimum environment you might for example be messing C compiler, which is needed by rust.
```sh
error: linker `cc` not found
```
In such case install build tools by typing:
```sh
sudo apt install build-essential
```
...and try to run `cargo check` again.

When everything is ok, and `cargo` was able to install packages you are ready to run the app. Let's run the app with the --release flag in order to create proper application binaries for later use.
```sh
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
```sh
cd /etc/systemd/system
```

Create service file with your favourite editor (nano in our case) and name it with suitable name eg.: `sudo nano rocket-poc.service`.

Add following content to the services file:
```sh
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
```sh
$ curl http://0.0.0.0:8081/
Hello, world!
```
It works! Now only thing remaining is to make this url and port visible to the Internet by making Apache act as a reverse proxy server. We'll do that in the next chapter.

However before configuring Apache, couple of additional words about systemd services.

To check that the systemd service is running we can type:
```sh
$ ps -aux | grep 'rocket'
www-data   30505  0.0  0.2  75580  4152 ?        Ssl  22:27   0:00 /home/yourname/yourproject/target/release/yourproject
```
Even better way to check service status is to use `systemctl status rocket-poc` command.

Let's enable service so that it's operational even after system restart by typing:
```sh
sudo systemctl enable rocket-poc.service
```

And finally in case we would like to stop running the systemd service we can type: `sudo systemctl start rocket-poc.service` or if we want to disable service from automatically restarting `sudo systemctl enable rocket-poc.service`.

## Step 4. Configuring Apache server to work with our Rust web Application

We will use a reverse proxy setup to make Apache serve our Rust web application content.

If not yet installed you can install Apache by typing:
```sh
sudo apt install apache2
```

Next we need to enable Apache `proxy` and `proxy_http` modules:
```sh
sudo a2enmod proxy
sudo a2enmod proxy_http
```


