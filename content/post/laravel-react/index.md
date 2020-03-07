---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Setting up Laravel and React in WSL"
subtitle: ""
summary: "Step by step process for setting up Laravel and React and all it's dependencies in WSL"
authors: []
tags: ["wsl", "laravel", "react"]
categories: ["Tutorial"]
date: 2020-03-07T15:04:55+09:00
lastmod: 2020-03-07T15:04:55+09:00
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false
  # placement:
# Custom featured image size
custom_size:
  use: false
  size: 

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

Recently I was given an assignment to build a simple web app using Laravel. Having never used PHP before, this was an interesting challenge! Because setting up environments is never fun, I decided to write this post to summarize my notes and hopefully help out anyone else looking to get Laravel up and running. I setup my environment using WSL, but this guide should work for any Unix system.

## Prerequisite
If you're a Windows user, first you're going to want to get WSL setup and running. For more information please see: [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

## Getting Started
From a fresh WSL install, we'll want to install the following packages:
- Apache 2.4.29
- PHP 7.2.24
- Node 8.10.0

## Step 1 - Node, Apache, PHP
The following commands will get the above packages, and other dependencies installed.
```
sudo apt update && sudo apt upgrade
sudo apt install apache2 curl openssl zip unzip git libonig-dev nodejs npm

```
Check nodejs and npm were installed correctly with:
```
$ node --version
v8.10.0
$ npm --version
3.5.2
```

After confirming nodejs and npm are ok, confirm apache is ok, and if not, get it running
```
$ apache2 -v
Server version: Apache/2.4.29 (Ubuntu)
Server built:   2019-09-16T12:58:48
$ sudo service apache2 status
```

You can confirm the server is running and restart/start/stop it with the following commands when needed
```
sudo service apache2 status
sudo service apache2 restart
sudo service apache2 start
sudo service apache2 stop
```

Now that apache2 is up and running, it's time to get all the required php packages installed! In this tutorial, we're going to go with PHP 7.2.
```
sudo apt install php7.2 libapache2-mod-php php-cli php-cgi php-mbstring php-zip php7.2-gd php-xml php7.2-sqlite
```

You can visit [PHP Function Reference](https://www.php.net/manual/en/funcref.php) for more info on some of the above packages.

## Step 2 - Composer and Laravel
The composer installation guide comes with an SHA-384 installer check, so I recommend visiting the installation guide for more information. If the second step of the following commands fail, please see the guide.
[Download Composer](https://getcomposer.org/download/)

### Summarized Composer Installation (Feb 2020)
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'e0012edf3e80b6978849f5eff0d4b4e4c79ff1609dd1e613307e16318854d24ae64f26d17af3ef0bf7cfb710ca74755a') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --install-dir=~/.composer/vendor/bin/ --filename=composer
php -r "unlink('composer-setup.php');"
```

If the composer-setup.php command fails, please create the directory yourself and re-run.
```
mkdir -p ~/.composer/vendor/bin
```

Make sure composer is added to your `.bashrc`
```
echo 'PATH=$PATH:$HOME/.composer/vendor/bin/' >> ~/.bashrc
source ~/.bashrc
```

Finally, confirm composer installed correctly
```
$ composer --version
Composer version 1.9.3 2020-02-04 12:58:49
```

Laravel install is much simpler! Just run the following commands and you're good to go
```
composer global require laravel/installer
```
```
$ laravel --version
Laravel Installer 3.0.1
```

## Step 3 - React Framework
Laravel comes with VueJS scaffolding as default, but I personally prefer working with React. Luckily, switching over to React is very simple! Just run the following commands in the directory of your choice and you'll be good to go.
```
laravel new project_name
cd project_name
php artisan preset react
npm install
```

The above commands will create a new Laravel project named `project_name` in a directory of the same name. It will set the preset scaffolding to React and install all the required node modules. 

## Step 4 - Build and Go!
We're almost there! To get your new Laravel app running, run the following commands
```
composer install
npm run dev
```

Once those are finished, you will want to duplicate the provided `env.example` and rename it to `.env` and setup you're preferred database.

If you want something lightweight and simple, you can just set
```
DB_CONNECTION=sqlite
```
and delete the other `DB_` lines. This will use the database file in the default directory `database/database.sqlite` (make sure to create this file yourself!)

Lastly, get our API_KEY filled in with:
```
php artisan key:generate
```

And that should be it!! If everything went smoothly, you can run 
```
php serve artisan --port=8181
```
and you're good to go! Open up the basic site at `localhost:8181` and have fun working with Laravel and React!

### Final Comments
If you have any questions, advice, or found something wrong in this explanation, please let me know down below!



*Leave a comment over at [dev.to](https://dev.to/tylerwel/setting-up-laravel-and-react-in-wsl-1l42)!*
