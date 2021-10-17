**Report by Andrii Sobchuk. Week 2**

**Task 1.** Create Jenkins VM with internet access 

1.1 Set up VMs on Ubuntu 20.04
I set up 2 VMs with Ubuntu 20.04 at GCP  
![instances-GCP](https://user-images.githubusercontent.com/86925275/137642315-c057f7ec-7545-45b0-9415-d844f5f13c4c.png)

1.2 Add security groups
Also I add a network security group to limit the access to the servers from the Internet

![netw_sec_group](https://user-images.githubusercontent.com/86925275/137642450-9ed656f7-38ae-4387-a4f2-d5c125ac1ba5.png)

1.3 Install openjdk-8-jdk, Git 

        #installing Java 8 from PPA repository
        sudo add-apt-repository ppa:webupd8team/java
        sudo apt update

        #install Java 1.8 and Git
        sudo apt-get install openjdk-8-jre git -y
  
  1.4  Install Jenkins and enable autostart on startup 
  
        #install Jenkins
        #add the repository key to the system
        wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
        #append the Debian package repository address to the server’s sources.list
        sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
        sudo apt update
        sudo apt install jenkins
        
        #start Jenkins
        sudo systemctl start jenkins
        
        #enable autostart on startup
        sudo systemctl enable jenkins 
        #verify Jenkins status
        sudo systemctl status jenkins

![jenkins-status](https://user-images.githubusercontent.com/86925275/137643383-414e6426-e971-45cf-9e5a-f100c93559bb.png)

 1.5 Setup cumtom port 8081 for Jenkins    
 
 We need to edit configuration file with command: sudo vim /etc/default/jenkins and to change parameter  -> HTTP_PORT = 8081 instead defaul 8080
 
 ![custom-port8081](https://user-images.githubusercontent.com/86925275/137643752-e3033582-e961-455f-94a6-8531f39aa2b0.png)
 
 In order for the changes to take effect we need to restart Jenkins servise by using command:
         
         sudo service jenkins restart
 After that steps are done, we are able to access to login page with custom port 8081
 
 ![login-screen](https://user-images.githubusercontent.com/86925275/137643898-2ba2f34c-c156-4e69-b851-dea62821f9c9.png)

 
 1.6 Install additional plugins: GitHub and Role-based authorization strategy
 
 ![RBAS-pl](https://user-images.githubusercontent.com/86925275/137644502-2c65d5b0-1177-4264-b57e-cd24ed37c3ff.png)
 
 ![github-pl](https://user-images.githubusercontent.com/86925275/137644517-f671b543-8e47-4c19-9e15-6b132d6b2429.png)


Manage Jenkins -> Security (Configure Global Security) -> Authorization (Role-based authorization strategy)
![authorization](https://user-images.githubusercontent.com/86925275/137644824-7b0b9019-d6ae-454b-8a7d-34c3ded0f5b6.png)

1.7 Add new user (jenkins-NAME)
Manage Jenkins -> Security (Manage users) -> Create user
![users](https://user-images.githubusercontent.com/86925275/137644840-ee05a96c-26b7-49e6-ac3c-0d1c6bfe7fa4.png)


**Task 2.** Create Agent VM
2.1 Setup new VM (made also at GCP) and install openjdk-8-jre, Git 
                
       #installing Java 8 from PPA repository
        sudo add-apt-repository ppa:webupd8team/java
        sudo apt update

        #install Java 1.8 and Git
        sudo apt-get install openjdk-8-jre git -y
        
2.2 Prepare SSH keys

        #Create a jenkins user
        sudo adduser jenkins --shell /bin/bash
        #login as jenkins user
        su jenkins
        #Create a jenkins_slave directory under /home/jenkins
        mkdir /home/jenkins/jenkins_slave


        #Create a .ssh directory and cd into the directory.
        mkdir ~/.ssh && cd ~/.ssh

        #generate keys
        ssh-keygen

        #add public key to authorized_keys file 
        cat id_rsa.pub > ~/.ssh/authorized_keys

2.3 Connect agent to master node

Manage Jenkins -> System Configuration (Manage nodes and clouds) -> New node

I add Agent node and provide info about: node name, labels, remote root directory, host IP address and credentials (private key that we generate at previous step)

![slave1](https://user-images.githubusercontent.com/86925275/137645762-f4f03de0-af4d-4251-92f9-fd02b970ae7f.png)

![slave2](https://user-images.githubusercontent.com/86925275/137645768-65b85ffb-646a-421e-b02f-0cecb7f61dda.png)

Agent node was added successfully. 

**Task 3.** Configure  tools - NodeJS

3.1 Add NodeJS plugin and global npm packages to install (uglify-js, clean-css-cli)

Manage Jenkins -> Manage Plugins 

Find and add in available plugins NodeJS plugin and install 

![nodejs-pl](https://user-images.githubusercontent.com/86925275/137646113-9453da4d-a388-4d52-b4dc-6efdc2c8f2ac.png)

Manage Jenkins -> Global Tool Configuration -> NodeJS installations 
Name NodeJS and add required npm packages 

![nodejs-config](https://user-images.githubusercontent.com/86925275/137646203-b5f38b00-aa7c-4d3f-be63-8d65a394739f.png)



**Task 4.** Create "Multibranch Pipeline" pipeline job 


![multi-01](https://user-images.githubusercontent.com/86925275/137646502-881312a2-9135-4b64-9b62-a8e0382e5d7c.png)

![multi-02](https://user-images.githubusercontent.com/86925275/137646512-2ab90763-5f7f-4d76-b04f-a28c31fd6706.png)

![multi-03](https://user-images.githubusercontent.com/86925275/137646519-7d2d0c9b-eb84-4552-829a-db16d07cad57.png)

![multi-04](https://user-images.githubusercontent.com/86925275/137646521-85693db4-611e-4972-87db-459191453495.png)

**Jenkinsfile ( with declarative pipeline ):**

    pipeline {
    agent {
        label ('nodejs')
    }
    tools {
        nodejs ("nodejs-agent-1")
    }
    
    stages {
        stage ('Compress-js-css-files') {
            parallel {
                stage ('uglify-js') {
                    steps {
                        nodejs(nodeJSInstallationName: 'nodejs-agent-1') {
                            sh 'uglifyjs -o www/min/*.js www/js/*.js'
                            
                        }
                    }
                }
                stage ('clean-css') {
                    steps {
                        nodejs(nodeJSInstallationName: 'nodejs-agent-1') {
                            sh 'cleancss -o www/min/*.css www/css/*.css'
                            
                        }
                    }
                }
                
            }
        }
        stage('Package') {
            steps {
                sh 'mkdir artifacts'
                
                sh "tar --exclude ='./www/js/*' --exclude='./www/css'  --exclude='.git' --exclude='artifacts' -czvf  artifacts/compressed.${BUILD_ID}.tar.gz ."
                
                archiveArtifacts artifacts: "artifacts/compressed.${env.BUILD_ID}.tar.gz", fingerprint: true
            }
        }
    }
    }
 

