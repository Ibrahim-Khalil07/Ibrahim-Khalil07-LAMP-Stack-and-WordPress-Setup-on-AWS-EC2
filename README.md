# AWS EC2 LAMP Stack and WordPress Setup

This README document provides a step-by-step guide for setting up a LAMP (Linux, Apache, MySQL, PHP) stack and installing WordPress on an AWS EC2 instance. The goal is to create a fully functioning web server for hosting a WordPress-based website.

## Table of Contents

1. [Task 1: Launch an AWS EC2 Instance](#task-1-launch-an-aws-ec2-instance)
2. [Task 2: Update and Upgrade](#task-2-update-and-upgrade)
3. [Task 3: Install the LAMP Stack](#task-3-install-the-lamp-stack)
4. [Task 4: Install WordPress](#task-4-install-wordpress)
5. [Task 5: Complete WordPress Setup](#task-5-complete-wordpress-setup)
6. [Challenges Faced](#challenges-faced)
7. [Outcomes](#outcomes)
8. [Overall Objectives](#overall-objectives)


## Task 1: Launch an AWS EC2 Instance

### Overview
In this task, we will launch an AWS EC2 instance to serve as our web server.

### Steps
1. Sign in to your AWS account and access the AWS Management Console.

2. Click on "EC2" to open the EC2 dashboard.

3. Click the "Launch Instance" button to create a new EC2 instance.

4. Choose an Amazon Machine Image (AMI). Select "Amazon Linux 2 AMI" as it's a suitable choice for this purpose.

5. Select an instance type. The default option (e.g., t2.micro) is suitable for a small WordPress site.

6. Configure instance details, add storage, configure security groups, and review your settings. Make sure to open ports 80 (HTTP) and 22 (SSH) in the security group settings.

7. Launch the instance and select or create an SSH key pair to access the instance securely.

8. Connect to your EC2 instance using SSH. Replace `your-instance-ip` with your instance's public IP address:

   ```bash
   ssh -i your-key.pem ec2-user@your-instance-ip


##  Task 2: Update 

### Overview
In this task, we will update the system packages on our EC2 instance.

### Step

 Update the package manager cache:
   
    sudo yum update -y

 ##  Task 3: Install the LAMP Stack

 ### Overview
 In this task, we will install and configure the components of the LAMP stack: Apache, MariaDB (MySQL), and PHP.

 ### Steps

 1. Install Apache
  
    
        sudo yum install httpd -y

Start the Apache web server and enable it to start on boot:

    sudo systemctl start httpd
    sudo systemctl enable httpd
 
You can check this by copying your IP in new browzer and you will see the apache web page   

2. Install MariaDB (MySQL)
  
       sudo yum install mariadb-server -y

Start the MariaDB service and enable it to start on boot:
 
    sudo systemctl start mariadb
    sudo systemctl enable mariadb

Secure the MySQL installation by running:

    sudo mysql_secure_installation

Follow the prompts to set a root password, remove anonymous users, disallow root login remotely, remove test databases, and reload privileges.

3. Install PHP and Necessary Extensions

   Before upgrading PHP, it's a good idea to check which PHP versions are available in the Amazon Linux 2 package repositories. You can use the following command to list available PHP versions:

       sudo amazon-linux-extras list | grep php

This command will list various PHP versions available as "php7.x" or similar. Make a note of the version you want to install.

### Notice
I am facing this error when I try to access my wordpress page 

    Your server is running PHP version 5.4.16 but      
    WordPress 6.3.1 requires at least 7.0.0.
So that why I am not using default php version and specifically using php 7 or above version

Let's assume you want to install PHP 7.4. To do that, use the following commands:

    sudo amazon-linux-extras enable php7.4
    sudo yum install php-cli php-fpm php-json php-common php-mysqlnd php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath php-json

This will enable the PHP 7.4 repository and install the necessary PHP packages. Adjust the version number as needed.

Restart Apache

After installing the new PHP version, you'll need to restart the Apache web server to apply the changes:

    sudo systemctl restart httpd

You can verify that the new PHP version is active by running:
  
    php -v

This should display the updated PHP version, such as PHP 7.4.x.

You should also check the PHP configuration to ensure it's correctly set up. You can create a PHP info page to view the PHP configuration details:

Create a new file called phpinfo.php in your web root directory:


    sudo nano /var/www/html/phpinfo.php

Add the following PHP code to the file:

    php

    <?php
    phpinfo();
    ?>
Save the file and exit the text editor.

Access the PHP info page in your web browser by navigating to:

    bash

    http://your-ec2-instance-ip/phpinfo.php

Replace your-ec2-instance-ip with the actual IP address or domain name of your EC2 instance.

This page should display detailed information about your PHP installation and configuration.

Now that you've upgraded PHP, try accessing your WordPress site again. The error should no longer appear, and your WordPress installation should work correctly with the updated PHP version.

Remember that updating PHP may require you to check and potentially update your WordPress plugins and themes to ensure compatibility with the new PHP version. Always back up your WordPress site before making significant changes like this.

## Task 4: Install WordPress

### Overview
In this task, we will download and configure WordPress on our EC2 instance.

### Steps

1. Create a MySQL Database and User for WordPress

Access the MySQL server:

    sudo mysql -u root -p

Create a MySQL database for WordPress. Replace yourdbname with your preferred database name:

    CREATE DATABASE yourdbname;

Create a MySQL user and set a password. Replace yourdbuser and yourdbpassword with your preferred values:

    CREATE USER 'yourdbuser'@'localhost' IDENTIFIED BY 'yourdbpassword';

Grant the user all privileges on the database:

    GRANT ALL PRIVILEGES ON yourdbname.* TO 'yourdbuser'@'localhost';

Flush privileges and exit MySQL:

    FLUSH PRIVILEGES;
    EXIT;

2. Download and configure WordPress:

       cd /tmp
       wget https://wordpress.org/latest.tar.gz
       tar -zxvf latest.tar.gz
       sudo cp -r /tmp/wordpress/* /var/www/html/

3. Create the WordPress configuration file:

       sudo mv /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

4. Edit the wp-config.php file and provide your database information:

       sudo nano /var/www/html/wp-config.php

Update the following lines:

      define('DB_NAME', 'yourdbname');
      define('DB_USER', 'yourdbuser');
      define('DB_PASSWORD', 'yourdbpassword');

Save and exit the text editor.

5. Set proper permissions on the WordPress directory:

       sudo chown -R apache:apache /var/www/html/

## Task 5: Complete WordPress Setup

1. Open a web browser and navigate to your EC2 instance's public IP address or domain name.

2. Follow the on-screen instructions to complete the WordPress setup. You'll need to provide a site title, username, password, and email address.

3. Once WordPress is installed, you can log in to the WordPress admin dashboard using the credentials you just created.

## Challenges Faced

### Challenge 1: PHP Version Compatibility
The server initially had PHP 5.4 installed, but WordPress required PHP 7.0 or higher.
To resolve this, we updated PHP to a compatible version (e.g., PHP 7.4).
### Challenge 2: Security Considerations
Ensuring proper security settings during MySQL setup was crucial.
We secured MySQL by setting a root password, removing anonymous users, and disabling root login remotely.

## Outcomes

1. Successfully set up a LAMP stack on an AWS EC2 instance.
2. Installed WordPress and configured it to use a MySQL database.
3. Resolved PHP version compatibility issues.
4. Created a fully functional web server for hosting WordPress-based websites.

## Overall Objectives

The main objectives of this project were:

1. To create a functional web server using AWS EC2.
2. To install and configure a LAMP stack to support WordPress.
3. To deploy and set up WordPress for hosting a website.
4. To ensure compatibility with the latest PHP version.
5. To address security considerations during MySQL setup.

