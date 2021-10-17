**Report by Andrii Sobchuk. Week 2**

**Task 1.** Create Jenkins VM with internet access 

1.1 Set up VMs on Ubuntu 20.04
I set up 2 VMs with Ubuntu 20.04 at GCP  
![instances-GCP](https://user-images.githubusercontent.com/86925275/137642315-c057f7ec-7545-45b0-9415-d844f5f13c4c.png)

1.2 Add security groups
Also I add a network security group to limit the access to the servers from the Internet

![netw_sec_group](https://user-images.githubusercontent.com/86925275/137642450-9ed656f7-38ae-4387-a4f2-d5c125ac1ba5.png)

1.3 Install openjdk-8-jdk, Git and Jenkins

        #installing Java 8 from PPA repository
        sudo add-apt-repository ppa:webupd8team/java
        sudo apt update

        #install Java 1.8 and Git
        sudo apt-get install openjdk-8-jre git -y
        
        #install Jenkins
        #add the repository key to the system
        wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
        #append the Debian package repository address to the server’s sources.list
        sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
        sudo apt update
        sudo apt install jenkins
        
        #start Jenkins
        sudo systemctl start jenkins
        sudo systemctl status jenkins

        
        
        
