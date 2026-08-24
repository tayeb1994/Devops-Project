# Multi Trier Application Setup Locally 
Windows Tools Install chocolatey from the instructions given in the link below. 
- https://chocolatey.org/docs/installation 
## Run all the below commands on Powershell (Open Powershell as Admin) 
- choco install virtualbox--version=7.0.8-y 
- choco install vagrant--version=2.3.7-y , Always try to download updated version 
- choco install git-y 
- choco install corretto17jdk-y 
- choco install maven-y 
- choco install awscli-y 
- choco install intellijidea-community-y 
- choco install vscode-y 
- choco install sublimetext3-y 
<img width="672" height="474" alt="image" src="https://github.com/user-attachments/assets/c8d37688-f118-4539-a38e-d0f5f07d70d6" />

## Prerequisite
1. Oracle VM Virtualbox
2. Vagrant
3. Vagrant plugins
   
**Execute below command in your computer to install host manager plugin.**
- $ vagrant plugin install vagrant-hostmanager  **(vagrant plugin install vagrant-hostmanager tells Vagrant to download and install the vagrant-hostmanager plugin, which automatically manages your system’s /etc/hosts file for multi‑machine Vagrant setups.)**

4. Git bash or equivalent editor

## VM SETUP
1. Clone source code.
https://github.com/hkhcoder/vprofile-project
2. Cd into the repository.
3. Switch to the local branch.(/d/project_AWS_Imran/vprofile-project)
4. cd into vagrant/Manual_provisioning

## Bring up vm’s
$ vagrant up

<img width="795" height="331" alt="image" src="https://github.com/user-attachments/assets/195c061a-5ed5-474b-bd57-952586962a2a" />


*NOTE: Bringing up all the vm’s may take a long time based on various factors.If vm setup stops in the middle run “vagrant up” command again.*
INFO: All the vm’s hostname and /etc/hosts file entries will be automatically updated

## PROVISIONING

**Services**
1. Nginx=> Web Service
2. Tomcat=> Application Server
3. RabbitMQ=> Broker/Queuing Agent
4. Memcache=> DB Caching
5. ElasticSearch => Indexing/Search service
6. MySQL=> SQL Database

- **Setup should be done in below mentioned order**
    1. MySQL
    2. (Database SVC)
    3. Memcache (DB Caching SVC)
    4. RabbitMQ (Broker/Queue SVC)
    5. Tomcat(Application SVC)
    6. Nginx(Web SVC)

## MYSQL Setup
- Login to the db vm **$ vagrant ssh db01**
- Use  **$ Sudo -i** command to go root user
- Verify Hosts entry, if entries missing update the it with IP and hostnames using this command **cat /etc/hosts**

<img width="777" height="317" alt="image" src="https://github.com/user-attachments/assets/22bced89-d7ee-4faf-ae09-13930076db1c" />

- Update OS with latest patches **# dnf update-y**

<img width="624" height="468" alt="image" src="https://github.com/user-attachments/assets/5d9afa0e-257e-49bb-aeba-dd289a3e375e" />

- Set Repository **# dnf install epel-release-y**

<img width="618" height="296" alt="image" src="https://github.com/user-attachments/assets/77342141-4895-4371-8817-a50aa58ebb07" />

- Install Maria DB Package **# dnf install git mariadb-server -y**

<img width="659" height="436" alt="image" src="https://github.com/user-attachments/assets/aa1ed3fc-1ba4-46c1-911a-6f94c8f390ff" />

- Starting & enabling mariadb-server
1. **systemctl start mariadb**
2. **systemctl enable mariadb**
3. **systemctl status mariadb**

<img width="1168" height="538" alt="image" src="https://github.com/user-attachments/assets/de1c8a43-db3e-4210-b4bc-2c7431d13fc5" />

- RUN mysql secure installationscript. **# mysql_secure_installation**
NOTE:Set db root password, I will be using admin123 as password

<img width="632" height="791" alt="image" src="https://github.com/user-attachments/assets/6ae8aa38-c678-4066-a93e-5fdc73134c95" />

- Set DB name and users. **# mysql -u root – p ( pass: 123)**

<img width="720" height="277" alt="image" src="https://github.com/user-attachments/assets/3fdae333-24e5-4e4e-b855-fe387b9cd624" />

- mysql> create database accounts;
- mysql> grant all privileges on accounts.* TO 'admin'@'localhost' identified by 'admin123'; **for local login**
- mysql> grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123'; **for remote access**
- mysql> FLUSH PRIVILEGES;
- mysql> exit;
  
- **Download Source code & Initialize Database.**
1. cd /tmp/
2. git clone-b local https://github.com/hkhcoder/vprofile-project.git
3. cd vprofile-project
4. mysql-u root-p accounts < src/main/resources/db_backup.sql (pass: 123 )
5. mysql-u root-p accounts
6. mysql> show tables;

<img width="900" height="717" alt="image" src="https://github.com/user-attachments/assets/c7104324-eece-44fa-8c47-6562be80debf" />

- mysql> exit;

- **Restartmariadb-server**
1. #systemctl restart mariadb

-**Starting the firewall and allowing the maria db to access from port no.3306**
1. #systemctl start firewalld
2. #systemctl enable firewalld
3. #firewall-cmd--get-active-zones
4. #firewall-cmd--zone=public--add-port=3306/tcp--permanent
5. #firewall-cmd--reload
6. #systemctl restart mariadb

## 2.MEM CACHE SETUP 
1. Log into the Memcache vm,  $ vagrant ssh mc01 
2. Verify Hosts entry, if entries missing update the it with IP and host names   # cat /etc/hosts 
3. Update OS with latest patches,   # dnf update-y 

**Install start & enable memcache on port 11211** 
- #sudo dnf install epel-release-y 
- #sudo dnf install memcached-y
- #sudo systemctl start memcached 
- #sudo systemctl enable memcached
- #sudo systemctl status memcached

<img width="1893" height="1120" alt="image" src="https://github.com/user-attachments/assets/9f0d10cb-3f12-4496-b0b6-4b2395b5250c" />


- #sed-i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached   (You only have to change 127.0.0.1 → 0.0.0.0 when you want **memcached to accept connections from other machines, not just from the local server**.)
- The bind address determines who can reach your memcached instance:
    • **127.0.0.1** = listen only on localhost → no external access (This is the default on many systems because memcached has no authentication and should not be exposed publicly.) 
    • **0.0.0.0** = listen on all network interfaces → any machine that can reach your server can connect (This is used when memcached is part of a distributed setup and other servers need access.) 

- #sudo systemctl restart memcached 

**Starting the firewall and allowing the port 11211 to access memcache**  
1. #systemctl start firewalld 
2. #systemctl enable firewalld 
3. #firewall-cmd--add-port=11211/tcp 
4. #firewall-cmd--runtime-to-permanent 
5. #firewall-cmd--add-port=11111/udp 
6. #firewall-cmd--runtime-to-permanent 
 
- #sudo memcached-p 11211-U 11111-u memcached-d

## 3.RABBITMQ SETUP 
- Log into the RabbitMQ vm $ vagrant ssh rmq01 
- Verify Hostsentry, if entries missing update the it with IP and hostnames, #cat /etc/hosts 
- Update OS with latest patches, #dnf update-y    

<img width="804" height="615" alt="image" src="https://github.com/user-attachments/assets/0c3b0078-dfee-4602-9f81-98a27cec0a27" />

- Set EPEL Repository, # dnf install epel-release-y
- Install Dependencies, # sudo dnf install wget-y (**means: Install the wget package using DNF, run as root, and automatically confirm the installation.**)

<img width="856" height="245" alt="image" src="https://github.com/user-attachments/assets/70212deb-cf98-427a-a9b1-0553bb8bdeca" />

- #dnf-y install centos-release-rabbitmq-38  (**means:Install the RabbitMQ 3.8 repository package and automatically confirm the installation.**)

<img width="874" height="649" alt="image" src="https://github.com/user-attachments/assets/68a8d754-1cda-454b-94dc-822fba5f3132" />

- #dnf--enablerepo=centos-rabbitmq-38-y install rabbitmq-server (**means:Enable the RabbitMQ 3.8 repository, automatically confirm prompts, and install the RabbitMQ server package.**)

<img width="874" height="639" alt="image" src="https://github.com/user-attachments/assets/5210224b-ad93-4deb-b702-43fc078011a2" />


**Set up access to user test and make it admin**
- #sudo sh-c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config' (**means: As root, write a RabbitMQ config file that allows the guest user to connect from any host.**)
- #sudo rabbitmqctl add_user test test (**means: As root, create a RabbitMQ user named test with the password test.**)
- #sudo rabbitmqctl set_user_tags test administrator (**means:As root, give the user test full administrator privileges in RabbitMQ.**)
- #rabbitmqctl set_permissions-p / test ".*" ".*" ".*" (**means:Give user test full configure, write, and read permissions on the default vhost /**)
- #sudo systemctl restart rabbitmq-server
- #sudo systemctl status rabbitmq-server

<img width="866" height="641" alt="image" src="https://github.com/user-attachments/assets/5caa419b-daf5-466d-a483-4853873359a0" />

## 4.TOMCAT SETUP 
**Tomcat is a Java-based web server and servlet container. To run it, you need Java installed, Tomcat downloaded, and the service started.** 
- Login to the tomcat vm, $ vagrant ssh app01
- Verify Hosts entry, if entries missing update the it with IP and hostnames, #cat /etc/hosts
- Update OS with latest patches, # dnf update-y
- Set Repository, # dnf install epel-release-y
- Install Dependencies, # dnf-y install java-17-openjdk java-17-openjdk-devel

<img width="808" height="527" alt="image" src="https://github.com/user-attachments/assets/0edb3058-7a03-480e-9cef-68c449681caa" />

- **#dnf install git wget-y** is a package‑installation command for Fedora, RHEL, and CentOS 8+ systems. Here’s exactly what each part means and why it’s used. 
**Git → for cloning repos, managing code**
**Wget → for downloading files via HTTP/HTTPS/FTP**

<img width="822" height="715" alt="image" src="https://github.com/user-attachments/assets/34b4355d-c64e-4ae6-87c5-90df783bc3a7" />

- Change dir to /tmp, # cd /tmp/
- Download & Tomcat Package, # wget https://archive.apache.org/dist/tomcat/tomcat-10/v10.1.26/bin/apache-tomcat-10 .1.26.tar.gz

<img width="820" height="687" alt="image" src="https://github.com/user-attachments/assets/a7ea9324-094b-4c5b-b357-bbb78a6ac2a8" />

- #tar -xzvf apache-tomcat-10.1.26.tar.gz (**This command extracts the compressed Tomcat archive (apache-tomcat-10.1.26.tar.gz) into the current directory.**) 

<img width="812" height="637" alt="image" src="https://github.com/user-attachments/assets/d51da8e1-5ba8-4d0d-a7b6-391446a8e839" />

**Add tomcat user**
- #useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat (**This command creates a new system user named tomcat, gives it a custom home directory at /usr/local/tomcat, and prevents it from logging in by assigning the nologin shell.**) 
- Copy data to tomcat home dir, # cp -r /tmp/apache-tomcat-10.1.26/* /usr/local/tomcat/ (**It copies all files and folders from the extracted Tomcat directory in /tmp into the Tomcat installation directory at /usr/local/tomcat/.**) 
- Make tomcat user owner of tomcat home dir, # chown -R tomcat.tomcat /usr/local/tomcat (**It changes the ownership of all files and folders inside /usr/local/tomcat so that the tomcat user and tomcat group own them.**)

**Set up system ctl command for tomcat**
- Open tomcat service file, # vi /etc/systemd/system/tomcat.service
- Update the file with below content

**[Unit]
Description=Tomcat
After=network.target
[Service]
User=tomcat
Group=tomcat
WorkingDirectory=/usr/local/tomcat
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINE_BASE=/usr/local/tomcat
ExecStart=/usr/local/tomcat/bin/catalina.sh run
ExecStop=/usr/local/tomcat/bin/shutdown.sh
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target**

**Reload systemd files**
- #systemctl daemon-reload

**Start & Enable service**
- #systemctl start tomcat
- #systemctl enable tomcat
- #systemctl status tomcat
  
<img width="905" height="620" alt="image" src="https://github.com/user-attachments/assets/6dd9bfd0-90d7-4b24-9121-22dd3a2aed56" />

## CODE BUILD & DEPLOY(app01)
**Maven Setup**
- #cd /tmp/ (**Moves you into the /tmp directory — a temporary workspace commonly used for downloads and installations.** )
- #wget https://archive.apache.org/dist/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip ( **Downloads the Maven 3.9.9 binary ZIP file from the Apache archive.**)

  <img width="895" height="416" alt="image" src="https://github.com/user-attachments/assets/7621b207-a8d9-4abe-a700-89a98553a0ea" />

- #unzip apache-maven-3.9.9-bin.zip (Extracts the ZIP file.) 
- #cp-r apache-maven-3.9.9 /usr/local/maven3.9 ( Copies the extracted Maven directory into /usr/local/ )

<img width="902" height="225" alt="image" src="https://github.com/user-attachments/assets/f36065dd-289b-45fc-9a92-7db2e18c9884" />

- #export MAVEN_OPTS="-Xmx512m" 
**Sets a JVM option for Maven:**
    • -Xmx512m = maximum heap size 512 MB
    • Helps Maven run more smoothly on large builds
- Download Source code, # git clone-b local https://github.com/hkhcoder/vprofile-project.git

<img width="893" height="623" alt="image" src="https://github.com/user-attachments/assets/e2359787-b8af-4249-8a0c-0a3c444c46c2" />

**Update configuration**
- #cd vprofile-project
- #vim src/main/resources/application.properties

<img width="902" height="625" alt="image" src="https://github.com/user-attachments/assets/072481c3-105f-415f-a3b9-787b0844f1b6" />

- #Update file with backend server details ( don’t have to change now)
**Build code**
- Run below command inside the repository (vprofile-project), # /usr/local/maven3.9/bin/mvn install

<img width="898" height="622" alt="image" src="https://github.com/user-attachments/assets/5965fd10-ebc7-445e-b253-8c1b3f6dc9f6" />

<img width="892" height="312" alt="image" src="https://github.com/user-attachments/assets/0005d621-bda4-48d9-b398-e0a560316508" />

**Deploy artifact**
- #systemctl stop tomcat
- #rm-rf /usr/local/tomcat/webapps/ROOT*
- #cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
- #systemctl start tomcat
- #chown tomcat.tomcat /usr/local/tomcat/webapps-R
- #systemctl restart tomcat

<img width="900" height="620" alt="image" src="https://github.com/user-attachments/assets/6581ae2b-bb49-4bf8-8611-ff684cdb64ca" />



