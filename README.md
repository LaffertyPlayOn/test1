# README

## MySQL Setup

### Installation

#### Mac

Using homebrew:

It may be a good idea to clean brew and make sure it is up to date.

    $ brew cleanup
    $ brew update

Install:
    $ brew install mysql

NOTE: Make sure to read any prompts and instructions.

1.) Set up databases to run AS YOUR USER ACCOUNT

2.) Set up launchctl load for the mysql.plist to launch mysql on startup.

Using mysql DMG:

 TODO?

#### Linux

Use the appropriate package for your distribution.
For example, on Ubuntu:

    $ sudo apt-get install mysql-server

### Log In
If you had to configure the launchctl load, start the server.

    $ mysql.server start

Log into mysql as the root user. Unless you set a password on
installation, the mysql root password is empty.

    $ mysql -uroot

### Verify installation is configured the same way as Staging and Production instances in Amazon RDS
	**NOTE: This section may change later
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
>	- If your output appears as above, you are done and can move on...
>	- If you don't see UTC (ie, it's blank or instead you see SYSTEM or some other timezone), run the following:
	
	mysql> select * from mysql.time_zone_name where name like "%UTC%";

>		- This may return multiple rows, but you want to verify that 'UTC' is listed there somewhere.  
>		If *not*, then we need to build the timezone tables ourselves (see troubleshooting section below)
>	- If 'UTC' was found, then all we need to do is update our config file: "my.cnf"
>	- Locate your installation's my.cnf file (you can run __sudo find / -name "my.cnf"__ from another shell if you don't know where it's located) and open it in your favorite text editor.
>	- Add the following line to the file, save it, then restart your MySQL server.
	
	default-time-zone = UTC
>	- Re-run the "select @@global.time_zone..." stmt from above once you've logged back into MySQL client, verify it's as expected. 

###### *SQL_MODE settings*
	**NOTE: Currently our RDS instances are set to have no flags set for SQL_MODE.  This is 
	likely to change in the future, but for now this step may or may not be needed (depending 
	on your version and installation method).
	For the sake of mirroring our RDS instances, you may choose to make this change, or if 
	you want to leave your installation in a stricter mode, just understand that it may affect how 
	your Rspec tests run.  If anything, you should see failures that others miss (if, for instance, 
	there happens to be some validations missing), so for now I'm leaving this open ended.  Either 
	run your development environment w/ no flags, or, if you know what you're doing, you can run 
	with certain flags set.  We will revisit this documentation shortly, but here are the steps 
	to determine what your SQL_MODE settings are and where you can update them.  Toggling between
	flags and no flags is as simple as changing your my.cnf file and restarting the server instance.
	
>	- To check what SQL_MODE flags are set, run the following query in your MySQL client:

	mysql> select @@global.sql_mode, @@session.sql_mode;
	+-------------------+--------------------+
	| @@global.sql_mode | @@session.sql_mode |
	+-------------------+--------------------+
	|                   |                    |
	+-------------------+--------------------+
	1 row in set (0.00 sec)
	
>		- If your results are blank (as seen above) then you have the same set-up as the RDS instances currently (ie, no SQL_MODE flags).  If you have some set (either by default or intentionally), then of course you'll see them listed here.  The default settings can vary based on installation method and/or MySQL version.

>	- To set/change this, the best method is to, again, edit your "my.cnf" file (see steps above from *Timezone settings* section).
>
>	Comment out what (if anything) is in your my.cnf file relating to "sql_mode" and add either an empty string or a comma-separated list of the flags you want...for instance:

	sql_mode = ""

>	or

	sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 

>	For more information on what the flags are and about the SQL_MODE variable in general, feel free to read more in the MySQL reference manual (these links are for the 5.5 version, you can find the same info regarding your particular version by switching the version number in the URL or clicking on the menu items in the top left).
>
>	[SQL_MODE FAQS](http://dev.mysql.com/doc/refman/5.5/en/faqs-sql-modes.html "FAQS")
>
>	[SQL_MODE FLAGS](http://dev.mysql.com/doc/refman/5.5/en/server-sql-mode.html "FLAGS")

###### *Version*
>	This isn't vital at the moment, we will revisit whether everyone should be on same version or not later.  As it stands our Staging and Production
>	instances on RDS aren't even on the same version, so this step is optional at the moment and more or less a placeholder, should we want to come
>	back to this.  (see the info at the bottom of this page to see what the versions are as of March 2013)

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

    $ rake app:seed
    

## Addendum

### Trouble-shooting
TODO?

### Notes On Current RDS Instances and Settings (what Production and Staging look like)

### General Tips and How-To Section (for those new to MySQL)

### References and further reading (useful resources, etc)