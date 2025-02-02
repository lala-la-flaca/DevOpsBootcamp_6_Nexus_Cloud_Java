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
<img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/NexusgradleMaven.png"/>

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

       <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/1%20CreatingDroplet8GB.png"/>
       
   
   d) Select the SSH key  as the Authentication Method. <br>
    - Generate a New SSH Key (if required): If you do not have an existing SSH key, follow DigitalOceans's guide to create a new pair.
    - Use an existing SSH Key: if you already have a public SSK key pair, navigate to the .ssh folder in your local directory. Copy the public key and paste it into the appropriate field in 		 
      DigitalOCean's interface.<br>
    - Use existing keys from previous projects.

   e) Select **Create Droplet**

### Configuring Firewall on Digital Ocean
Following security best practices, configure the firewall's inbound and outbound rules. In this case, you only allow inbound SSH access from your machine to the Droplet, restricting all other unnecessary connections.

1. Select the **Networking** option from the left panel, then choose **Firewall**.

2. Click on **Create Firewall**.
  
3. Set the firewall rules for incoming traffic.<br>
   Allow SSH access from your machine by adding an inbound rule that allows traffic from the public IP address of your machine on port 22. Keep in mind that this IP may change.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/2%20Adding%20Firewall%20to%20Droplet.png"/>
   
4. SSH into the droplet to verify that everything works as expected.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/2%20AccesingDropletSSH.png"/>
   
### Deploying Nexus Repository Manager on Droplet
   
1. Update the package manager on the droplet.

   ```bash
       apt update
   ```
   
2. Install Java openjdk17 on the droplet.
 
   ```bash
       apt install openjdk-17-jre-headless
   ```
   
3. Install netstat tools on the droplet.

   ```bash
       apt install net-tools
   ```
   
4. Navigate to the opt folder from your home directory.

   ```bash
       cd /opt
   ```
        
5. Open a browser, navigate to the Nexus Downloads section, and copy the URL for the .tar.gz file [Nexus Download](https://help.sonatype.com/en/download.html).
    
6. Download the .tar.gz file under the /opt directory.

    ```bash
       wget https://download.sonatype.com/nexus/3/nexus-3.76.1-01-unix.tar.gz
    ```

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/5%20Installing%20Nexus%20Opt%20folder.png"/>

7. Untar the file .tar.gz file.
    
    ```bash
       tar -xvf nexus-3.76.1-01-unix.tar.gz
    ```
    
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/6%20Untar%20package.png"/>
    
8. Display the files under the /opt directory. You should see two new directories: **nexus-3.76.1-01** and **sonatype-work**.
   
    * **nexus-3.76.1-01**: Contains the nexus runtime and application.
    * **sonatype-work**: Contains your Nexus configuration and data.

      
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/7%20Nexus%20folders.png"/>
    
9. The best practice is to create a dedicated user for each service. In this case, create the Nexus user.
    
    ```bash
       adduser nexus
    ```
    
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/8%20Creating%20NExus%20user.png"/>
    
10. Grant only the required permission to run Nexus. Assign Nexus as the owner and group of the **nexus-3.76.1-01** and **sonatype-work** directories.
    
    ```bash
       chown -R nexus:nexus nexus-3.76.1-01
       chown -R nexus:nexus sonatype-work
    ```
    
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/9.%20Changing%20Folders%20owernship%20from%20root%20to%20nexus.png"/>
    
11. Configure Nexus to run as the Nexus user by modifying the nexus.rc file in the /opt/nexus-3.76.1-01/bin directory.

     ```bash
       vim nexus-3.76.1-01/bin/nexus.rc
    ``` 
    
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/10%20SeetingUp%20The%20NExusRC%20file%20to%20run%20as%20NExus%20user.png"/>
    
12. Switch from root user to Nexus user.

     ```bash
       su - nexus
    ```
     
13. Deploy Nexus application on the droplet using the Nexus user.

    ```bash
       /opt/nexus-3.76.1-01/bin/nexus start
    ```

     <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/11%20Starting%20app%20from%20nexus%20user.png"/>
    
14. Verify the process ID (PID) and the port used by the application.

     ```bash
       ps aux | grep nexus
       netstat -lnpt
    ```
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/12%20Checking%20Nexus%20is%20running%201.png"/>
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/12%20Checking%20Nexus%20is%20running.png"/>
      
15. Modify the droplet firewall rules to allow inbound traffic on port 8081. 

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/Modifying%20inboud%20rules%20droplets%20firewall.png"/>
    
16. Open a browser and enter the IP address of the droplet followed by the port in the following format: ip_address:port.

    The Nexus repository is running on DigitalOcean.

    [Nexus on droplet](http://157.230.56.153:8081/)

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/13%20NExus%20webUI%20repository.PNG"/>
   

### Setting up Nexus Repository Manager

1. To access the Nexus repository for the first time, use the admin credentials found in the admin.password file, which is located /opt/sonatype-work/nexus3/.

    ```bash
      cat /opt/sonatype-work/nexus3/admin.password
    ```
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/14%20Accesing%20defult%20credetentials.png"/> 

2. Create a user in the Nexus repository with permission to push to Nexus.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/18%20Creating%20user.png"/> 
   
3. Create a role for the user in Nexus repository.
 
  <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/19%20Creating%20roles.png"/> 

4. Setup the role by adding type, Role ID, Role Name
  
  <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/22%20Creating%20roles%20%203.png"/>

5. Click on modify privileges and assign the least privileges to follow best practices.
 
  <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/21%20Adding%20permission%20to%20role.png"/>
  
6. Assign the role to the user.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/23%20Assign%20Role%20to%20user.png"/>

### Setting up Gradle App to Push to Nexus

Clone the Java-Gradle application from Nana DevOps Bootcamp, follow these steps:

1. Open the app in IntelliJ, add the plugin, and nexus details to the **build.gradle** file.
   
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
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/24%20a%20BuildGradle%20File%20modifications.png"/>
   
2. Create the **gradle.properties** file.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/24%20Gradle%20properties%20file.png"/>

3. Add the credentials in the **gradle.properties** file.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/GradleRepoCredentials.png"/>
   
4. Navigate to the app directory and build the application using gradle.

    ```bash
      gradle build
    ```
    
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/25%20Build%20de%20App%20GRadlebuild.png"/>

5. Publish the artifact to the Nexus Repository.

    ```bash
      gradle publish
    ```
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/Publishing%20JavaApp%20with%20gradle%20NExus.png"/>
    
6. Verify that the artifact is available in the Nexus repository.

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/AppAvailableNexus.png"/>


### Setting up Maven App to Push to Nexus

Clone the Java-Gradle application from Nana DevOps Bootcamp, follow these steps:

1. Open the maven-app in IntelliJ and add the plugins in the **pom.xml** file to push from Maven to Nexus.

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

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/DefiningandCallingPluginMaven.png"/>

2. Add the Nexus repository details, ensuring that the URL is pointing to the Maven Snapshot repository in Nexus.

    ```bash
    <distributionManagement>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <url>http://157.230.56.153:8081/repository/maven-snapshots</url>
        </snapshotRepository>
    </distributionManagement>	
    ```

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/Configuring%20Snapshot%20repository%20maven.png"/>
    
3. Navigate to the home directory and locate the .m2 directory to add the **settings xml** file.

   ```bash
   ls -a |  grep .m2
   cd .m2/
   vim settings.xml
   ```
   
4. Add the credentials in the **setting.xml** file and save the changes.

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/Configurationof%20M2%20file%20with%20credentials%20maven.png"/>
   
5. Navigate to the app directory and create the artifact.

   ```bash
   mvn package 
   ```
   
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/JarFileCreatedMvn.png"/>

6. Verify that the .jar file is available.

7. Push the artifact to the Nexus repository.

   ```bash
   mvn deploy 
   ```
   
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/DeployingTheAppToNexus.png"/>
    
8. Verify that the artifact is available in Nexus.

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_6_Nexus_Cloud_Java/blob/main/Img/BothAppsNExus.png"/>
   
    

