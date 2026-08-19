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
- Verify Hosts entry, if entries missing update the it with IP and hostnames **cat /etc/hosts**

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
- mysql> grant all privileges on accounts.* TO 'admin'@'localhost' identified by 'admin123'; for local login
- mysql> grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123'; for remote access
- mysql> FLUSH PRIVILEGES;
- mysql> exit;
**Download Source code & Initialize Database.**
1. # cd /tmp/
2. # git clone-b local https://github.com/hkhcoder/vprofile-project.git
3. # cd vprofile-project
4. # mysql-u root-p accounts < src/main/resources/db_backup.sql (pass: 123 )
5. # mysql-u root-p accounts
6. mysql> show tables;



