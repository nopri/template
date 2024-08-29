Application Template 
for application written in Singkong programming language

Singkong.jar:
    Download from https://nopri.github.io/Singkong.jar

Templates:
(Apache-2.0 license)
------------------------------------------------------------
gui:
    GUI
    (menubar, tab, statusbar, thread)

    based on gui-login template

    How to run:
    java -jar Singkong.jar main.singkong

gui-login:
    GUI with login
    (login, popup progress, menubar, tab, statusbar, thread)

    based on Bakwan: https://github.com/nopri/bakwan

    How to run:
    java -jar Singkong.jar main.singkong

web:
    Web: public, HTML only
    (header/footer, link, query string)
    
    Please read how to configure web server
    Apache HTTP Server: please enable .htaccess for data dir

web-bootstrap4:
    Web: public, using Bootstrap 4
    (header/footer, navbar, query string)
    Navbar based on Navbar fixed example
    Bundled with Bootstrap and jQuery in lib dir

    Please read how to configure web server
    Apache HTTP Server: please enable .htaccess for data dir
------------------------------------------------------------

How to configure web server (Windows, macOS, Linux):

Windows 
* Tested on Windows 10 and 11
* System administration privileges required

Install Internet Information Services:
- Run: Turn Windows features on or off
- At Windows Features dialog:
  - Expand: Internet Information Services
    - Expand: World Wide Web Services
      - Expand: Application Development Features
        - Select: CGI and ISAPI Extensions 
  - Click OK

Configure Internet Information Services:
- Run: Internet Information Services (IIS) Manager
- Features View
  - Run: Handler Mappings
  - At Handler Mappings dialog:
    - Add Script Map...
      - At Add Script Map dialog:
        - Request path: *.web
        - Executable: 
          <path to java.exe> -DSINGKONG=0 -jar <path to Singkong.jar> %s
        - Name: SingkongWeb
        - Click OK
    - At Script Map confirmation dialog for ISAPI extension: Yes


macOS
* Using Apache HTTP Server
* Tested on macOS 11
* System administration privileges (using sudo) required
* Commands are run in: Terminal

- Edit: /etc/apache2/httpd.conf
  - Search for multi-processing module and cgi_module, 
    make sure the modules are loaded:
    
	LoadModule mpm_prefork_module libexec/apache2/mod_mpm_prefork.so
	<IfModule mpm_prefork_module>
		LoadModule cgi_module libexec/apache2/mod_cgi.so
	</IfModule>

  - Make sure actions_module is also loaded:

	LoadModule actions_module libexec/apache2/mod_actions.so

  - Configure /Library/WebServer/CGI-Executables to allow exec CGI:
	
	<Directory "/Library/WebServer/CGIExecutables">
		AllowOverride None
		Options ExecCGI
		Require all granted
	</Directory>

  - Add the following lines:

	AddHandler SingkongWeb .web
	Action SingkongWeb "/cgi-bin/singkongweb.cgi"

- Restart Apache HTTP Server:
  apachectl restart

- Create singkongweb.cgi file: 
  /Library/WebServer/CGI-Executables/singkongweb.cgi 

	#!/bin/bash

	if [ -z "$PATH_TRANSLATED" ];
	then
		printf "Status: 404 Not Found\n"
		printf "Content-type: text/plain\n\n"
		printf "not found\n"
	else
		java -DSINGKONG=0 -jar /opt/Singkong.jar "$PATH_TRANSLATED"
	fi

- chmod: chmod +x /Library/WebServer/CGIExecutables/singkongweb.cgi

- Download Singkong.jar and save to /opt
  curl https://nopri.github.io/Singkong.jar -o /opt/Singkong.jar

- Where to save *.web files: /Library/WebServer/Documents/


Ubuntu Linux
* Using Apache HTTP Server
* Tested on Ubuntu 22.04
* System administration privileges (using sudo) required
* Commands are run in terminal emulator

- Install packages:
  apt-get update
  apt-get install apache2 default-jre

- Enable Apache HTTP Server modules: actions cgi
  a2enmod actions cgi
  
- Create configuration file: 
  /etc/apache2/conf-available/singkongweb.conf

	AddHandler SingkongWeb .web
	Action SingkongWeb "/cgi-bin/singkongweb.cgi"

- Enable Apache HTTP Server configuration:
  a2enconf singkongweb serve-cgi-bin

- Restart Apache HTTP Server:
  systemctl restart httpd

- Create singkongweb.cgi file: 
  /usr/lib/cgi-bin/singkongweb.cgi

	#!/bin/bash

	if [ -z "$PATH_TRANSLATED" ];
	then
		printf "Status: 404 Not Found\n"
		printf "Content-type: text/plain\n\n"
		printf "not found\n"
	else
		java -DSINGKONG=0 -jar /opt/Singkong.jar "$PATH_TRANSLATED"
	fi

- chmod: chmod +x /usr/lib/cgi-bin/singkongweb.cgi

- Download Singkong.jar and save to /opt
  wget https://nopri.github.io/Singkong.jar -O /opt/Singkong.jar

- Where to save *.web files: /var/www/html/


Alma Linux and Rocky Linux
* Using Apache HTTP Server
* Tested using version 8
* System administration privileges (using sudo) required
* SELinux is disabled (SELINUX=disabled in file /etc/selinux/config)
* Commands are run in terminal emulator

- Install packages:
  dnf install httpd java-1.8.0-openjdk

- Create configuration file: 
  /etc/httpd/conf.d/singkongweb.conf

	AddHandler SingkongWeb .web
	Action SingkongWeb "/cgi-bin/singkongweb.cgi"

- Restart Apache HTTP Server:
  systemctl restart httpd

- Create singkongweb.cgi file: 
  /var/www/cgi-bin/singkongweb.cgi

	#!/bin/bash

	if [ -z "$PATH_TRANSLATED" ];
	then
		printf "Status: 404 Not Found\n"
		printf "Content-type: text/plain\n\n"
		printf "not found\n"
	else
		java -DSINGKONG=0 -jar /opt/Singkong.jar "$PATH_TRANSLATED"
	fi

- chmod: chmod +x /var/www/cgi-bin/singkongweb.cgi

- Download Singkong.jar and save to /opt
  curl https://nopri.github.io/Singkong.jar -o /opt/Singkong.jar

- Where to save *.web files: /var/www/html/

