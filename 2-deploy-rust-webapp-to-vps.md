# Deploy Rust Web App on Virtual Private Server

This article discusses deploying Rust based web app on Virtual Private Server (VPS). In this example Debian 11 linux based VPS is used as a target machine for deployment and Apache server as a web server.

It is expected that Rust based web application exists on the git server that will be pulled and installed on the VPS.

This article is based on this [post](https://steadylearner.medium.com/how-to-deploy-rust-web-application-8c0e81394bd5) about deploying Rust web application on DigitalOcean server with Nginx. In this article we will using propriotary Virtual Private Server running Apache, which is going to be used as a web server instead of Nginx.

## Step 0. (Prerequisite) SSH to your Virtual Private Server
Create SSH connection and login to your Virtual Private Server (VPS). Check you VPS provider for details if you don't know how.
```sh
$ ssh <username>@<hostname>
```

## Step 1. Install Git and Rust

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
We will be using git to download existing Rocket web app from Github. Alternatively you can follow **Option B.** and create new web app locally, if you don't have ready app to experiment with.

Let's download app from Github.
>Note we are using private repo in this example, so you should use your own repo or follow Step 2, Option B. below.
```sh
$ git clone https://github.com/proactor-dev/rocket-poc.git
```
If everything goes ok, you should now have your Rust Rocket web application cloned on your server. Conratulations! Next run the app in order to verity it runs locally

### Option B. Create new Rust Rocket Web Application
In case you do not have ready web application to deploy, let's create simple one as an example.

[TODO]

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