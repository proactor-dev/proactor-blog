# Creating Hello World Web Application with Rust

In this article we will discuss creating a Hello World web application using Rust.

Rust has a selection of web frameworks, which makes creating simple web applications easy. Each framework has its strenghts and weaknesses. In this article we will be using Rust Rocket, which is one of the main Rust web frameworks, however not the most mature one, since being at version 0.5 at the time of writing this article.

## Prerequisites

It is expected that the Rust is installed on the system in order to complete examples shown in this article. For platform specific infomation about installing Rust please visit Rust [installation page](https://www.rust-lang.org/tools/install).

## Rust Rocket Web Application

Let's create Hello World! web application with Rust Rocket. Detailed information about Rust Rocket framework you can find from [here](https://rocket.rs/).

### Step 1. Create new Rust Rocket application
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
```rust
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
Congratulations! Now you have your Rust Rocket Hello World! app setup. You can try running it to check everything went as expected:
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

### Step 3. Create local git repository
Go to working folder and create local git repository
```bash
git init -b main
```
Add and commit existing files
```bash
git add .
git commit -m "Initial commit"
```

### Step 4. Create empty GitHub repository
* Go to github.com and sign in to your github account
* Create new empty repository
* Do not add README, licence or .gitignore files

### Step 5. Attach to remote repo and push the source code to GitHub repository
```sh
git remote add origin REMOTE-URL
git push -u origin main
```

That's it - now we have a functional Rust Rocket web application and it's stored in Github repository for future improvements.

In the [next arcticle](2-deploy-rust-webapp-to-vps.md) we will discuss how to deploy our Hello World! web application into Virtual Private Server (VPS).