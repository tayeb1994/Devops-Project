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

NOTE: Bringing up all the vm’s may take a long time based on various factors.If vm setup stops in the middle run “vagrant up” command again.
INFO: All the vm’s hostname and /etc/hosts file entries will be automatically updated
