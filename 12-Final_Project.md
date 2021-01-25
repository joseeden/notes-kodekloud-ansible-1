<!-- 2021-01-25 09:31:09 -->

# 12 - Final Project #
______________________________________________________________  

For the final project, we'll be using the **LAMP** stack application to deploy our own fictional KodeKloud e-commerce website that sells electroniC devices. But for this one, we'll be using *MariaDB* in place of MySQL.

    L - Linux
    A - Apache
    M - MariaDB
    P - PHP

Note that this won't be using Ansible to automate the deployment. We will just work on a single-node deployment of this application.

Before starting off, it's good to always know what you're automating - what are the different components in the application and how they all work together without automation vs. how they should work together when there's automation.

1.  Identify the system to deploy our application. This will be **CentOS Linux.** This includes installing the **firewall**.
<BR>

2.  Install and configure **Apache HTTPD server**

    - Install and configure HTTPD
    - Configure firewalld
    - Enable and start HTTPD
<br>

3.  Install and configure the database.

    - Install MariaDB
    - Configure MariaDB
    - Start MariaDB
    - Configure Firewall
    - Configure Database
    - Load data
<br>

4.  install and configure PHP on the server.

    - Install PHP
    - Configure code
    - Test
<br>

5.  Ensure we have all system requirements configured correctly.

    - Install and configure firewalls in our server
    - Configure necessary roles to enable communication between the components.

Now that we have identified the important steps to take, we can further arrange these tasks to simplify the flow:

```bash

1.  Install firewall on CentOS
2.  Install and configure MariaDB
3.  Enable and Start MariaDB
4.  Configure Firewall
5.  Configure Database
6.  Load data
7.  Install httpd
8.  Install PHP
9.  Configure Firewall
10. Configure httpd
11. Enable and start httpd
12. Download code
13. Test
```
______________________________________________________________  

