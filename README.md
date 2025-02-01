# Artifact Repository Manager with Nexus

## Description
This demo project is part of Module 6: Artifact Repository Manager with Nexus from Nana DevOps Bootcamp. This project focuses on setting up **Nexus Repository Manager** on a DigitalOcean droplet and managing Java artifacts using Gradle and Maven. The goal is to securely store, manage, and distribute build artifacts for Java applications.  

<br />

## 🚀 Technologies Used

- <b>Digital Ocean: Cloud provider for hosting Nexus Repository.</b>
- <b>Nexus: Artifact repository manager.</b>
- <b>Linux: Ubuntu for Server configuration and management.</b>
- <b>Java/Gradle/Maven: Nana's application and build tools.</b>

## 🎯 Features

- <b>Setup a droplet(VM) on Digital Ocean with security best practices.</b>
- <b>Setup **Nexus Repository Manager** on Digital ocean.</b>
- <b>Setup users and roles to access Nexus.</b>
- <b>Deploy Java artifact to Nexus using Gradle and Maven.</b>

## 🏗 Project Architecture
<img src=""/>

## ⚙️ Project Configuration:

### Creating a Droplet on Digital Ocean
1. Sign up for an account on [DigitalOcean](https://www.digitalocean.com).
2. Create a droplet:<br>
   a) Select **Create Droplet** from the Digital Ocean dashboard. <br>
   
   b) Select a region: Select the region closest to your location.<be>

   c) Select an image: Select the Ubuntu image.<br>
     
     - Choose Size: <br>
       * Droplet type: Select Basic.<br>
       * CPU options: Select Regular SSD and the 8GB/4CPUs option. The server must have enough memory to host Nexus, at least 4GB is needed.<br>

       <img src=""/>
   
   d) Select the SSH key  as the Authentication Method. <br>
    - Generate a New SSH Key (if required): If you do not have an existing SSH key, follow DigitalOceans's guide to create a new pair.
    - Use an existing SSH Key: if you already have a public SSK key pair, navigate to the .ssh folder in your local directory. Copy the public key and paste it into the appropriate field in DigitalOCean's interface.<br>
    - Use existing keys from previous projects.

   e) Select **Create Droplet**

### Configuring Firewall on Digital Ocean
Following security best practices, configure the firewall's inbound and outbound rules. In this case, you only allow inbound SSH access from your machine to the Droplet, restricting all other unnecessary connections.

1. Select the **Networking** option from the left panel, then choose **Firewall**.

   <img src=""/>

2. Click on **Create Firewall**
  
3. Set the firewall rules for incoming traffic.<br>
   Allow SSH access from your machine by adding an inbound rule that allows traffic from the public IP address of your machine on port 22. Keep in mind that this IP may change.

   <img src=""/>
   
4. SSH into the droplet to verify that everything works as expected.

   <img src=""/>

### Deploying Nexus Repository Manager on Droplet
   
1. Update the package manager on the droplet.

   ```bash
       apt update
   
3. Install Java openjdk17 on the droplet.
 
   ```bash
       apt install openjdk-17-jre-headless
   ```
   
4. Install netstat tools on the droplet.

   ```bash
       apt install net-tools
   ```
   
5. Navigate to the opt folder from your home directory.

   ```bash
       cd /opt
   ```
        
7. Navigate to the Nexus Downloads section and copy the link of the URL to download the .tar.gz file [Nexus Download](https://help.sonatype.com/en/download.html)
    
8. Download the .tar.gz file under the /opt directory.

    ```bash
       wget https://download.sonatype.com/nexus/3/nexus-3.76.1-01-unix.tar.gz
    ```

    <img src=""/>

9. Untar the file .tar.gz file.
    
    ```bash
       tar -xvf nexus-3.76.1-01-unix.tar.gz
    ```
    
    <img src=""/>
    
10. Display the files under the opt folder and we have two new directories the nexus-3.76.1-01 and sonatype-work.
    * **nexus-3.76.1-01**: Contains the nexus runtime and application.
    * **sonatype-work**: Contains your Nexus configuration and data.
    
    <img src=""/>
    
11. Best practice is to create a dedicated user for each service. In this case, create the Nexus user.
    
    ```bash
       adduser nexus
    ```
    
12. Grant only the required permission to run Nexus. Assign Nexus as the owner and group of the nexus-3.76.1-01 and sonatype-work directories.
    
    ```bash
       chown -R nexus:nexus nexus-3.76.1-01
       chown -R nexus:nexus sonatype-work
    ``` 

   <img src=""/>
    
12. Configure NExus to run as the Nexus user by modifying the nexus.rc file in the /opt/nexus-3.76.1-01/bin directory.

     ```bash
       vim nexus-3.76.1-01/bin/nexus.rc
    ``` 
    
    <img src=""/>
    
13. Switch from root user to Nexus user.

     ```bash
       su - nexus
    ```
     
14. Deploy Nexus application on the droplet using the Nexus user.

    ```bash
       /opt/nexus-3.76.1-01/bin/nexus start
    ```
    
15. Verify the proccess ID (PID) and the port used by the application.

     ```bash
       ps aux | grep nexus
       netstat -lnpt
    ```

    <img src=""/>
      
16. Modify the droplet firewall rules to allow inbound traffic on port 8081. 

    <img src=""/>
    
17. Open a browser and enter the IP address of the droplet followed by the port in the format: ip_address:port.

    [Nexus on droplet](http://157.230.56.153:8081/)

    <img src=""/>

    The Nexus repository is running on DigitalOcean.

    

### Setting up Nexus Repository Manager

1. To access the Nexus repository for the first time, use the admin credentials found in the admin.password file, which is located /opt/sonatype-work/nexus3/.

    ```bash
      cat /opt/sonatype-work/nexus3/admin.password
    ```
    <img src=""/> 

2. Create a user in the Nexus repository with permission to push to Nexus.

   <img src=""/> 
   
3. Create a role for the user in Nexus repository. Assign the least privileges to follow best practices.

  <img src=""/> 
  
4. Assign the role to the user.

   <img src=""/>

   
### Setting up Gradle App to Push to Nexus

Clone the Java-Gradle application from Nana DevOps Bootcamp, follow these steps:

1. Open app in IntelliJ and add the plugin and nexus details to the **build.gradle** file.
   
    ```bash
    apply plugin: 'maven-publish'
    publishing {    
        publications {
            maven(MavenPublication) {
                artifact("build/libs/my-app-$version" + ".jar") {
                    extension 'jar'
                }
            }
        }   
    
        repositories {
            maven {
                name: 'nexus'
                url "http://157.230.56.153:8081/repository/maven-snapshots/"
                credentials {
                    username project.repoUser
                    password project.repoPassword
                }
            }
        }  
    }

    ```
   <img src=""/>
   
2. Create the **gradle.properties** file and add the credentials to access nexus.

   <img src=""/>
   
3. Navigate to the app directory and build the application using gradle.

    ```bash
      gradle build
    ```
    
    <img src=""/>

4. Publish the artifact to the Nexus Repository.

    ```bash
      gradle publish
    ```
    
5. Verify that the artifact is available in the Nexus repository.

    <img src=""/>


### Setting up Maven App to Push to Nexus

Clone the Java-Gradle application from Nana DevOps Bootcamp, follow these steps:

1. Open the maven-app in IntelliJ and add the plugins to push from Maven to Nexus in the **pom.xml** file

   ```bash
    <groupId>com.example</groupId>
    <artifactId>java-maven-app</artifactId>
    <version>1.1.0-SNAPSHOT</version>

    <build>
        <plugins>
           <plugin>
               <groupId>org.apache.maven.plugins</groupId>
               <artifactId>maven-site-plugin</artifactId>
               <version>3.12.1</version>

           </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-deploy-plugin</artifactId>
            </plugin>
		</plugins>
	</build>
   ```

   <img src=""/>

2. Add the Nexus repository details, ensuring that the URL is pointing to the Maven Snapshot repository in Nexus.

    ```bash
    <distributionManagement>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <url>http://157.230.56.153:8081/repository/maven-snapshots</url>
        </snapshotRepository>
    </distributionManagement>	
    ```

    <img src=""/>
    
3. Navigate to the home directory and locate the .m2 directory to add the **settings xml** file.

   ```bash
   ls -a |  grep .m2
   cd .m2/
   vim settings.xml
   ```
   <img src=""/>
   
4. Add the credentials in the **setting.xml** file and save the changes.

   <img src=""/>
   
5. Navigate to the app directory and create the artifact.

   ```bash
   mvn package 
   ```
   
   <img src=""/>

6. Verify that the .jar file is available

7. Push the artifact to the Nexus repository.

   ```bash
   mvn deploy 
   ```
   
   <img src=""/>
    
8. Verify that the artifact is available in Nexus.

    <img src=""/>
   
    

