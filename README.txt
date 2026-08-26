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

web-login:
    Web: public, HTML only, session
    (header/footer, link, query string)
    
    Please read how to configure web server

    session directory: 
    - set writable by web server user

web-bootstrap4:
    Web: public, using Bootstrap 4
    (header/footer, navbar, query string)
    Navbar based on Navbar fixed example
    Bundled with Bootstrap and jQuery in lib dir

    Please read how to configure web server

web-bootstrap4-login:
    Web: public, using Bootstrap 4, session
    (header/footer, navbar, query string)
    Navbar based on Navbar fixed example
    Bundled with Bootstrap and jQuery in lib dir
    
    Please read how to configure web server

    session directory: 
    - set writable by web server user

web-mdbootstrap5-admin:
    Web: admin, using MDB 5 UI Kit, session
    (header/footer, navbar, sidebar, query string)
    Based on free admin dashboard template 
    Bundled (in lib dir): 
    - jQuery
    - DataTables
    - Apache ECharts
    - MDB 5 UI Kit
    - bootstrap-datepicker
    - Font Awesome 6 (Free)
    
    Please read how to configure web server

    session directory: 
    - set writable by web server user

web-adminlte4-admin:
    [The currently recommended Singkong web admin template]
    Web: admin, using AdminLTE4, session
    (header/footer, navbar, sidebar, QR Code 
    toolbar, breadcrumb, query string)
    Bundled (in lib dir): 
    - jQuery
    - DataTables
    - Apache ECharts
    - AdminLTE4
    - bootstrap-datepicker
    - Bootstrap Icons

    Please read how to configure web server

    session directory: 
    - set writable by web server user

------------------------------------------------------------

How to configure web server 
(Windows, macOS, Linux, cPanel/Apache, DirectAdmin/Apache):

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

Session directory:
- set writable by IIS_IUSRS

web.config file:
- contains requestFiltering and hiddenSegments settings

------------------------------------------------------------
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

- Restart Apache HTTP Server:
  apachectl restart

- Where to save *.web files: /Library/WebServer/Documents/


------------------------------------------------------------
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
  
- Enable Apache HTTP Server configuration:
  a2enconf serve-cgi-bin

- Virtual host configuration:
  (example: /etc/apache2/sites-enabled/000-default.conf) 

    <VirtualHost *:80>
            DocumentRoot /var/www/html
            <Directory /var/www/html>
                    AllowOverride All
            </Directory>
    </VirtualHost>

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

- Restart Apache HTTP Server:
  systemctl restart apache2

- Where to save *.web files: /var/www/html/

- session directory permission example:
  chown -R www-data session
  or
  chgrp -R www-data session
  chmod -R g+w session


------------------------------------------------------------
Alma Linux and Rocky Linux
* Using Apache HTTP Server
* Tested using version 8
* System administration privileges (using sudo) required
* SELinux is disabled (SELINUX=disabled in file /etc/selinux/config)
* Commands are run in terminal emulator

- Install packages:
  dnf install httpd java-1.8.0-openjdk

- Virtual host configuration:
  (example: /etc/httpd/conf.d/default.conf)

    <VirtualHost *:80>
            DocumentRoot /var/www/html
            <Directory /var/www/html>
                    AllowOverride All
            </Directory>
    </VirtualHost>

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

- Restart Apache HTTP Server:
  systemctl restart httpd

- Where to save *.web files: /var/www/html/

- session directory permission example:
  chown -R apache session
  or
  chgrp -R apache session
  chmod -R g+w session


------------------------------------------------------------
cPanel/Apache
* As regular user without system administration privileges
* Install Java runtime in home directory,
  if Java runtime is not installed
* Download https://nopri.github.io/Singkong.jar,
  save to home directory

- Create singkongweb.cgi file,
  (in public_html/cgi-bin/ in home directory)
  (please replace <java> and <singkong> with
  respective path to Java and Singkong)

	#!/bin/bash

	if [ -z "$PATH_TRANSLATED" ];
	then
		printf "Status: 404 Not Found\n"
		printf "Content-type: text/plain\n\n"
		printf "not found\n"
	else
		<java> -DSINGKONG=0 -jar <singkong> "$PATH_TRANSLATED"
	fi

- chmod: chmod +x singkongweb.cgi
  (or right click singkongweb.cgi in File Manager,
  Change Permissions, Execute)

- Where to save *.web files: public_html in home directory


------------------------------------------------------------
DirectAdmin/Apache
* As regular user without system administration privileges
* Install Java runtime in home directory,
  if Java runtime is not installed
* Download https://nopri.github.io/Singkong.jar,
  save to home directory

- Create singkongweb.cgi file,
  (in public_html/cgi-bin/ in home directory)
  (please replace <java> and <singkong> with
  respective path to Java and Singkong)

	#!/bin/bash

	if [ -z "$PATH_TRANSLATED" ];
	then
		printf "Status: 404 Not Found\n"
		printf "Content-type: text/plain\n\n"
		printf "not found\n"
	else
		<java> -DSINGKONG=0 -jar <singkong> "$PATH_TRANSLATED"
	fi

- chmod: chmod +x singkongweb.cgi
  (or right click singkongweb.cgi in File Manager,
  Set Permissions, Execute)

- Where to save *.web files: public_html in home directory


