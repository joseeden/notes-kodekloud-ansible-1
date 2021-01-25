
# 2 - Playbooks and Modules
______________________________________________________________    

## ANSIBLE PLAYBOOKS ##

<br>

<p align=center>
  <image src="Images/ansible-playbook-3.png">
</p>

<br>

We use Ansible playbooks to define instructions of what we want Ansible to do. It could be as simple as a series of commands or it could be as complex as a series of different tasks.

    # Simple Ansible Playbook

    - Run command1 on Server1
    - Run command2 on Server2
    - Run command3 on Server3
    - Run command4 on Server4

    # Complex Ansible Playbook

    - Deploy 25 VMs on Public Cloud
    - Deploy 40 VMs on Private Cloud
    - Install and Configure a Web Application
    - Setup Cluster Configuration

Recall that playbooks are written in **YAML**. It consist of **Plays** which defines a set of tasks to be performed on hosts. They are written in this format. Note that YAML files must start with a single dash (-) or triple dashes (---). 

But for more clarity and to define boundaries between plays when we start putting multiple plays in a single YAML file, we will use the triple dashes (---).

```yaml
-
  name: This tells what the play is about
  host: This could be a single target host or the groupname of hosts
  tasks:
      - name: Description of task 1
```

An example of an actual playbook looks like this:

```yaml
-
  name: Play 1
  hosts: localhost
  tasks:

      - name: Execute command to display the date.
        command: date

      - name: Execute script on server
        script: test_script.sh

      - name: Install httpd on CentOS
        yum: 
          name: httpd
          state: present

      - name: start web server
        service:
          name: httpd
          state: started
```

As mentioned earlier, YAML files can have not just one play. To indicate the next play, we just put the triple dashes. 

```yaml
-
  name: Play 1
  hosts: localhost
  tasks:

      - name: Execute command to display the date.
        command: date

      - name: Execute script on server
        script: test_script.sh
-
  hosts: localhost
  name: Play 2
  tasks:

      - name: Install httpd on CentOS
        yum: 
          name: httpd
          state: present

      - name: start web server
        service:
          name: httpd
          state: started
```

Note that since the **hosts** and **names** are key-value pairs which doesn't follow an order, they can be rearranged.
 
However, the key-pairs under the **tasks** are considered a list of actions to be performed and if you recall, list follow a certain order. Thus you must ensure that the list of tasks are in the correct order.

The **hosts** are retrieved from the **inventory** file. You can set it as localhost if you plan to run the play in your local host, or you could put in a different hostname if you'll perform the play on a remote server.

```bash
-
  hosts: localhost
  name: Play 2
```

You can also put in the group instead if you want to run the play on a group of servers simultaneously.

```bash
-
  name: Play 2
  hosts: mailservers
```

You just have to make sure that the values you put in **hosts** are defined in the **inventory** file.

```bash
local host

[mailserver]
server1.company.com
server2.company.com

[dbservers]
db1
db2
```
_________________________________________________________

## MODULES ##

The different actions run by tasks are called modules. In the playbook below, the modules are **command**, **script**, **yum**, and **service**.

```yaml
-
  name: Play 1
  hosts: localhost
  tasks:

      - name: Execute command to display the date.
        command: date

      - name: Execute script on server
        script: test_script.sh

      - name: Install httpd on CentOS
        yum: 
          name: httpd
          state: present

      - name: start web server
        service:
          name: httpd
          state: started
```

You can find more information about the available modules in the [Ansible documentation](https://docs.ansible.com/ansible/2.8/modules/modules_by_category.html). You can also run the command below to see a complete listing.

    ansible-doc -l

<br>

**Once you've build the playbook, how do you run it?**

To run the playbook, you just have to simple run *ansible-playbook <yaml file>

    ansible-playbook sample.yaml

You can also check out additional information by using the *--help* flag.

    ansible-playbook --help
____________________________________________________________

### PRACTICE 1 - RUNNING ANSIBLE ###

**ansible**
There's actually two ways to run ansible. You can use **ansible** to run one-off task such as to test connectviity between the controller and hosts machine or to simply shit down the hosts machines.

By using this command, you don't have to write a playbook or YAML file.

```bash
# The -a flag indicates 'action'
ansible <hosts> -a <command>

# Rebooting a single host and rebooting all hosts
ansible server1 -a "/sbin/reboot"
ansible all -a "/sbin/reboot" -b

# We can also define the modules to be used
ansible <hosts> -m <module-name>

# Using ping module to ping server1.
# Note that ansible will check the default /etc/ansible/host for server1
ansible server1 -m ping

# To specify a custom inventory file, we use the -i flag
ansible server1 -m ping -i inventory.txt
```

By now, you shoudl have three terminal opened simultaneously, one for each linux machines. On both **server1** and **Server2**, try to *ping 8.8.8.8* continuously. Then on the *master*, issue the command below to reboot both **server** machines. 

```bash
eden@master ansible-lab$ ansible -a "/sbin/reboot" all
server2 | FAILED | rc=1 >>
Failed to execute operation: Connection timed outMust be root.non-zero return code
server1 | FAILED | rc=1 >>
Failed to execute operation: Connection timed outMust be root.non-zero return code
```

Notice that you'll get an error when you try the command below. This is because you need to *become* root user to reboot the **server** machines. To do this, you use the -b flag to ***become root user***.

```bash
ansible -b -a "/sbin/reboot" all
```

You should see the ssh connection to both **server** machines closed. Similarly, the **master** should also lose connection to both **server** machine once they're rebooted.

```bash
eden@master ansible-lab$ ansible -b -a "/sbin/reboot" all
server1 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Shared connection to server1 closed.",
    "unreachable": true
}
server2 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Shared connection to server2 closed.",
    "unreachable": true
}
```
<br>

**ansible-playbook**
The real use of Ansible is through playbooks. As mentioned earlier, we can run playbooks by simply issuing the command *ansible-playbook YAML-file*. Note here that we don't have to specify the hosts since the hosts are already defined in the inventory file.

Recall that we have already created the [inventory.txt](inventory.txt) which will contain the two **server** machine. Modify it to look like this:

```yaml
[dbservers]
server1 ansible_host=10.0.0.246

[webservers]
server2 ansible_host=10.0.0.161

[linuxservers]
server1
server2
```

We'll also create a [ping-text.yaml.](ping-test.yaml)

```yaml
-
  name: Check connectivity to target servers
  hosts: linuxservers
  tasks:
    -
      name: Ping test
      ping: 
```

If you are unsure if your YAML formatting is correct, you can can use a free YAML checker site called [YAML Lint](http://www.yamllint.com/).

Once you've validated that your YAML formatting is correct, you can now run your playbook.

```bash
# Running the sample.yaml. 
# This will get the hosts from the default /etc/ansible/hosts
ansible-playbook ping-test.yaml

# You can define a custome inventory file by using the -i flag.
ansible-playbook ping-test.yaml -i inventory.txt
```

You should see an output like this

```bash
eden@master ansible-lab$ ansible-playbook ping-test.yaml -i inventory.txt 

PLAY [Check connectivity to target servers] ************************************************************************************************
TASK [Gathering Facts] *********************************************************************************************************************
ok: [server1]
ok: [server2]

TASK [Ping test] ***************************************************************************************************************************
ok: [server1]
ok: [server2]

PLAY RECAP *********************************************************************************************************************************
server1                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
server2                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
___________________________________________________________

### PRACTICE 2 - COPYING FILES ###

For this one, we'll be copying **/tmp/test-file.txt** from the **master** machine to both the **server** machines. We'll be using the **copy** module to accomplish this task.

```bash
eden@master ansible-lab$ ll /tmp/test-file.txt
ll: cannot access /tmp/test-file.txt: No such file or directory
eden@master ansible-lab$
eden@master ansible-lab$ touch /tmp/test-file.txt
eden@master ansible-lab$ ll /tmp/test-file.txt
-rw-rw-r--. 1 eden eden 0 Jan 24 04:10 /tmp/test-file.txt
eden@master ansible-lab$
eden@master ansible-lab$ nano /tmp/test-file.txt
eden@master ansible-lab$ cat /tmp/test-file.txt 
This is a sample file for the Practice 2 in the Ansible lab.
```

Writing the [copy-file.yaml](copy-file.yaml):

```yaml
-
  name: Copy file from controller to host machines
  hosts: linuxservers
  tasks:
    -
      name: Copy files
      copy:
        src: /tmp/test-file.txt
        dest: /etc/test-file.conf
```

Now, to run the playbook:

```bash
eden@master ansible-lab$ ansible-playbook copy-file.yaml -i inventory.txt 

PLAY [Copy file from controller to host machines] **********************************************************************
TASK [Gathering Facts] *************************************************************************************************
ok: [server1]
ok: [server2]

TASK [Copy files] ******************************************************************************************************
fatal: [server2]: FAILED! => {"changed": false, "checksum": "190209a9a6d19157cab2359cf0f40fe4dce0c0e4", "msg": "Destination /etc not writable"}
fatal: [server1]: FAILED! => {"changed": false, "checksum": "190209a9a6d19157cab2359cf0f40fe4dce0c0e4", "msg": "Destination /etc not writable"}

PLAY RECAP *************************************************************************************************************
server1                    : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0      
server2                    : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   

```

Notice here that it failed with an error of **"Destination /etc not writable"**. This is because the /etc directory can only be modified by the root. To elevate our user to root, we use the -b flag.

```bash
eden@master ansible-lab$ ansible-playbook -b copy-file.yaml -i inventory.txt

PLAY [Copy file from controller to host machines] **********************************************************************
TASK [Gathering Facts] *************************************************************************************************
ok: [server1]
ok: [server2]

TASK [Copy files] ******************************************************************************************************
changed: [server2]
changed: [server1]

PLAY RECAP *************************************************************************************************************
server1                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0      
server2                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0      

```

Checking each your **server** machines:

```bash
eden@server1 ~$ cat /etc/test-file.conf 
This is a sample file for the Practice 2 in the Ansible lab.

eden@server2 ~$ cat /etc/test-file.conf 
This is a sample file for the Practice 2 in the Ansible 
lab.
```

Note that when you run the playbook for a second time, Ansible will be able to check that the file was already copied to the **server** machine so it'll will not repeat those steps anymore and override the file. You will see a different output when you run the playbook again.

```bash
eden@master ansible-lab$ ansible-playbook -b copy-file.yaml -i inventory.txt

PLAY [Copy file from controller to host machines] ******************************

TASK [Gathering Facts] *********************************************************
ok: [server1]
ok: [server2]

TASK [Copy files] **************************************************************
ok: [server2]
ok: [server1]

PLAY RECAP *********************************************************************
server1                    : ok=2    changed=0    unreachable=0  
  failed=0    skipped=0    rescued=0    ignored=0
server2                    : ok=2    changed=0    unreachable=0  
  failed=0    skipped=0    rescued=0    ignored=0


```

This is the concept of **IDEMPOTENCY** - Ansible will only make the change if it's really needed. If the current state is already the desired state then it won't introduce any changes.
_______________________________________________________________

