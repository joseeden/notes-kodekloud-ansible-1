
# 2 - Practice: Inventory #
____________________________________________

These are the practice questions/labs included in the course. If you want to experience the real deal of configuring in the actual KodeKloud platform, you can check out their course, [Ansible for Absolute Beginners](https://kodekloud.com/p/ansible-for-the-absolute-beginners).

Note that these are not the exact qusame questions. You can try to answer them and create an inventory file in your own machines based on the questions.

When you're done, click 'Answer' to see if you got it correctly.

Good luck!
______________________________________________

**1. Create an inventory file with the following aliases and hostnames**

| alias | host |
| :-: | :-: |
| web1 | server1.company.com |
| web2 | server2.company.com |
| web3 | server3.company.com |
| db1 | server4.company.com |

<details>
<summary> Answer:</summary>

    web1 ansible_host=server1.company.com
    web2 ansible_host=server2.company.com
    web3 ansible_host=server3.company.com
    db1 ansible_host=server4.company.com

</details>

______________________________________________

**2. Using the same hosts, configure connection, user, and password for each host.**

| alias | host | connection | user | password |
| :-: | :-: | :-: | :-: | :-: | :-: |
| web1 | server1.company.com | ssh | root | P@ssw0rd |
| web2 | server2.company.com | ssh | root | P@ssw0rd |
| web3 | server3.company.com | ssh | root | P@ssw0rd |
| db1 | server4.company.com | windows | administrator | P@ssw0rd |

<details>
<summary> Answer:</summary>

    # Web Servers
    web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=P@ssw0rd
    web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=P@ssw0rd
    web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=P@ssw0rd

    # Database Servers
    db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=P@ssw0rd

</details>

______________________________________________

**3. Create groups for web_servers and db_servers. Also add "linux_servers" as parent group for both.**

| group | alias | host |
| :-: | :-: |  :-: |
| web_servers | web1 | server1.company.com |
| web_servers | web2 | server2.company.com |
| web_servers | web3 | server3.company.com |
| db_servers | db1 | server4.company.com |

<details>
<summary> Answer:</summary>

    web1 ansible_host=server1.company.com
    web2 ansible_host=server2.company.com
    web3 ansible_host=server3.company.com
    db1 ansible_host=server4.company.com

    [web_servers]
    web1
    web2
    web3

    [db_servers]
    db1

    [linux-servers:children]
    web_servers
    db_Servers

</details>

______________________________________________

**4. Create an inventory file using the data below.**


| alias | host | os | user | password |
| :-: | :-: | :-: | :-: | :-: | :-: |
| sqldb1 | sql1.xyz.com | Linux | root | L!nP@ssw0rd |
| sqldb1 | sql1.xyz.com | Linux | root | L!nP@ssw0rd |
| web_node1 | web01.xyz.com | Win | administrator | W!nP@ssw0rd |
| web_node2 | web02.xyz.com | Win | administrator | W!nP@ssw0rd |
| web_node3 | web03.xyz.com | Win | administrator | W!nP@ssw0rd |

You must also group the hosts based on these:

| Group | Members |
| :- | :- |
| db_nodes | sql_db1, sql_db2 |
| web_nodes | web_node1, web_node2, web_node3 |
| anaheim_nodes | sql_db1, web_node1 |
| burbank_nodes | sql_db2, web_node2, web_node3 |
| disney_nodes | anaheim_nodes, burbank_nodes |

<details>
<summary> Answer:</summary>

```yaml
# DB Servers
sql_db1 ansible_host=sql01.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=L!nP@ssw0rd
sql_db2 ansible_host=sql02.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=L!nP@ssw0rd

# Web Servers
web_node1 ansible_host=web01.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=W!nP@ssw0rd
web_node2 ansible_host=web02.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=W!nP@ssw0rd
web_node3 ansible_host=web03.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=W!nP@ssw0rd

# Groups
[db_nodes]
sql_db1
sql_db2

[web_nodes]
web_node1
web_node2
web_node3

[anaheim_nodes]
sql_db1
web_node1

[burbank_nodes]
sql_db2
web_node2
web_node3

[disney_nodes:children]
anaheim_nodes
burbank_nodes
```

</details>

______________________________________________________