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

### Verify installation is configured the same way as Staging and Production instances in Amazon RDS
	*NOTE: This section may change later
	The things that need verification or updating are:
	- TimeZone setting
	- SQL_Mode setting
	- MySQL version (optional)

###### *Timezone settings*
Our RDS instances are configured to use 'UTC' as the default timezone, and it is not open to modification (RDS prevents this from being changed).  Therefore, it
is probably prudent for us to ensure our local installations do the same.  It is not yet 100% certain that Rails mitigates this issue for us (although it looks like it may do the magic for us)
but for the time being it's best we mirror the RDS instances as closely as possible.

>	- Log in to the MySQL client if you're not there already (you should see "mysql>" as your prompt) Note: log in as root
>	- Run the following query:
	
	mysql> select @@global.time_zone, @@session.time_zone;
>	and you should see something like:

	+--------------------+---------------------+
	| @@global.time_zone | @@session.time_zone |
	+--------------------+---------------------+
	| UTC                | UTC                 |
	+--------------------+---------------------+
	1 row in set (0.00 sec)

>	- If you don't see UTC (ie, it's blank or instead you see SYSTEM or some other timezone), run the following:
	mysql> select * from mysql.time_zone_name where name like "%UTC%";
>		- This may return multiple rows, but you want to verify that 'UTC' is listed there somewhere. If *not*, then we need to build the timezone tables ourselves (see troubleshooting section below)
>	- If 'UTC' was found, then all we need to do is update our config file: "my.cnf"
>	- Locate your installation's my.cnf file (you can run **sudo find / -name "my.cnf" ** from another shell if you don't know where it's located) and open it in your favorite text editor.
>	- Add the following line to the file, save it, then restart your MySQL server.
	default-time-zone = UTC
>	- Re-run the "select @@global.time_zone..." stmt from above once you've logged back into MySQL client, verify it's as expected. 

###### *Version* (this isn't vital at the moment, we will revisit whether everyone should be on same version...see the info at the bottom of this page to see what the versions are as of March 2013)
To see what version you have installed (if you don't already know), simply run the following command from the shell (not from within the mysql client)

	$ mysql -V
	or
	$ mysql --version



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