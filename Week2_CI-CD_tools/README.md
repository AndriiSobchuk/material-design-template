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



