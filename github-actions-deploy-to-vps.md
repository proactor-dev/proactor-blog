# Continuous Deployment on GitHub Actions and Virtual Private Server

This article discusses CI/CD pipeline based on GitHub Actions and Virtual Private Server. As an example CD pipeline is built for web applications with two separate technologies;

1. Python (FastApi)
2. Rust ("Some" Web Framework such as Rocket or Salvo)

#### Rerefences
* Comparing FastAPI and Rust Web Frameworks inspired by [this](https://dev.to/dbanty/replacing-fastapi-with-rust-part-1-intro-251h) series of articles.
* CD environment setup inspired by [this article](https://dev.to/knowbee/how-to-setup-continuous-deployment-of-a-website-on-a-vps-using-github-actions-54im).

## Rust Rocket.rs "Hello world!"" app

Lets create hello world app with Rust rocket.rs web framework

### Step 1. Create new rust app
See [rocket.rs](https://rocket.rs/v0.5/guide/getting-started/) getting started for details.
```bash
% cargo new rocket-poc
% cd rocket-poc
```
Add following to Cargo.toml
```rs
[dependencies]
rocket = "0.5.0"
```
Modify main.rs accordingly:
```rs
#[macro_use] extern crate rocket;

#[get("/")]
fn index() -> &'static str {
    "Hello, world!"
}

#[launch]
fn rocket() -> _ {
    rocket::build().mount("/", routes![index])
}
```
Congratulations! Now you have your rocket.rs hello world app setup. You can try running it to check everything went as expected:
```bash
cargo run
```
You should see "Hello, world!" with your browser on address http://localhost:8000/

### Step 2. Configuring the port
Let's configure the rocket.rs app to run on separate port.

Create new file Rocket.toml with following content to use port 8081 in all environments

More about rocket.rs configurations from [here](https://rocket.rs/v0.5/guide/configuration/#rockettoml).
```
[global]
port = 8081
```
Now lets run the app again. Now the output should be found from new port:
http://localhost:8081

If everything went well let's stop the server with ```crtl-c``` and store the source code in github.

### Step 3. Push the source code to GitHub repository
Go to working folder and create local git repository
```bash
git init -b main
```
Add and commit existing files
```bash
git add .

```


## Virtual Private Server (VPS)

#### Step 1. Login to your VPS server and create SSH key for GitHub account email address

```bash
$ ssh <username>@<hostname>
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "<your github email address>"

Generating public/private rsa key pair.
Enter file in which to save the key (/home/<username>/.ssh/id_rsa): <press enter>
Enter passphrase (empty for no passphrase): <press enter>
Enter same passphrase again: <press enter>
Your identification has been saved in /home/<username>/.ssh/id_rsa
Your public key has been saved in /home/<username>/.ssh/id_rsa.pub
```

As a result id_rsa and id_rsa.pub files are created.

### Step 2. Add newly created public key to "authorized_keys"
```bash
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

([markdown-cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))