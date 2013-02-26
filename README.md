# README

## MySQL Setup

### Installation

#### Mac

Using homebrew:

It may be a good idea to clean brew and make sure it is up to date.

    brew cleanup
    brew update

Install:
    brew install mysql

NOTE: Make sure to read any prompts and instructions.

1.) Set up databases to run AS YOUR USER ACCOUNT

2.) Set up launchctl load for the mysql.plist to launch mysql on startup.

Using mysql DMG:

 TODO?

#### Linux

Use the appropriate package for your distribution.
For example, on Ubuntu:

    sudo apt-get install mysql-server

### Log In
If you had to configure the launchctl load, start the server.

    mysql.server start

Log into mysql as the root user. Unless you set a password on
installation, the mysql root password is empty.

    mysql -uroot

### Create the databases

    create database playon_development;
    create database playon_test;

### Create a user and grant permissions

    create user 'playon_dev'@'localhost';
    grant all on playon_development.* to 'playon_dev'@'localhost';
    grant all on playon_test.* to 'playon_dev'@'localhost';

### Load the seed data

From the shell prompt:

    rake app:seed