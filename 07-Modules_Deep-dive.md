<!-- 2021-01-24 13:26:20 -->

# 7 - Modules: Deep Dive #
______________________________________________________________    

<p align=center>
    <img src="Images/ansible-modules.png">
</p>

Ansible have categorized modules based on their functionalities. You can check out more details about the ansible modules in the [ansible documentation](https://docs.ansible.com/ansible/2.8/modules/list_of_all_modules.html). These are some of them:

1. **SYSTEM MODULES**
    These are performed on the system level, which includes the following:

    - User
    - Group
    - Hostname
    - Systemd
    - Service
    - and more...
    <br>

2.  **COMMAND MODULES**
    These are used to execute commands or scripts on hosts. This includes:

    - Command
    - Expect
    - Raw
    - Script
    - Shell
    - and more...

    <br>

3.  **FILE MODULES**
    Used for working on files. Examples are:

    - Acl
    - Archive
    - Unarchive
    - Copy
    - File
    - Find
    - LininFile
    - and more...

    <br>

4.  **DATABASE MODULES**
    These is useful in working with databases.

    - Mongodb
    - Mssql
    - Mysql
    - Postgresql
    - and more...

    <br>

5.  **CLOUD MODULES**
    This have a wide selection of modules we can use on the cloud.

    - Amazon
    - Atomic
    - Azure
    - Centurylink
    - and more...

    <br>

6.  **WINDOWS MODULES**
    Useful in a Windows environment.

    - win_copy
    - win_command
    - win_domain
    - win_file
    - win_iis_website
    - and more...

___________________________________________________

## COMMAND MODULE ##

Executes a command on a remote node. Below is a sample playbook, [command.yaml](command.yaml). Ignore the **register** and **debug** for now as they will be further discussed in other notes.

```yaml
-
  name: Demo of using command module
  hosts: server1
  tasks:
    -
      name: 'Check date'
      command: date
      register: date
    -
      name: 'Display test-file.conf contents'
      command: cat /etc/test-file.conf
      register: display
    -
      name: 'Switch first to /etc then display test-file.conf contents'
      command: chdir=/etc cat test-file.conf 
      register: change

    #  This will show the output of the tasks 1-3.
    - 
      name: 'Show variables'
      debug:
        msg: "{{date}}"
    - 
      name: 'Show variables'
      debug:
        msg: "{{display}}"
    - 
      name: 'Show variables'
      debug:
        msg: "{{change}}"

```
Now try running the [command.yaml](command.yaml). You should get a verbose output.

```bash
eden@master ansible-lab$ ansible-playbook command.yaml 
-i inventory.txt

PLAY [Demo of using command module] ********************************************

TASK [Gathering Facts] *********************************************************
ok: [server1]

TASK [Check date] **************************************************************
changed: [server1]

TASK [Display test-file.conf contents] *****************************************
changed: [server1]

TASK [Switch first to /etc then display test-file.conf 
contents] ***************
changed: [server1]

TASK [Show variables] **********************************************************
ok: [server1] => {
    "msg": {
        "changed": true, 
        "cmd": [
            "date"
        ], 
        "delta": "0:00:00.003777", 
        "end": "2021-01-24 05:00:31.992738", 
        "failed": false, 
        "rc": 0, 
        "start": "2021-01-24 05:00:31.988961", 
        "stderr": "", 
        "stderr_lines": [], 
        "stdout": "Sun Jan 24 05:00:31 UTC 2021",      
        "stdout_lines": [
            "Sun Jan 24 05:00:31 UTC 2021"
        ]
    }
}

TASK [Show variables] **********************************************************
ok: [server1] => {
    "msg": {
        "changed": true, 
        "cmd": [
            "cat", 
            "/etc/test-file.conf"
        ], 
        "delta": "0:00:00.003765", 
        "end": "2021-01-24 05:00:32.366377", 
        "failed": false, 
        "rc": 0, 
        "start": "2021-01-24 05:00:32.362612",         
        "stderr": "", 
        "stderr_lines": [], 
        "stdout": "This is a sample file for the Practice 2 in the Ansible lab.",
        "stdout_lines": [
            "This is a sample file for the Practice 2 in the Ansible lab."
        ]
    }
}

TASK [Show variables] **********************************************************
ok: [server1] => {
    "msg": {
        "changed": true, 
        "cmd": [
            "cat", 
            "test-file.conf"
        ], 
        "delta": "0:00:00.003579", 
        "end": "2021-01-24 05:00:32.725967", 
        "failed": false, 
        "rc": 0, 
        "start": "2021-01-24 05:00:32.722388", 
        "stderr": "", 
        "stderr_lines": [], 
        "stdout": "This is a sample file for the Practice 2 in the Ansible lab.",
        "stdout_lines": [
            "This is a sample file for the Practice 2 in the Ansible lab."
        ]
    }
}

PLAY RECAP *********************************************************************
server1                    : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

<br>

Here we have [create-folder.yaml](create-folder.yaml) which uses a parameter called **parameter** which checks first if the folder or file already exists before creating them.

```yaml
-
    name: 'Create ansible-test folder in target machine'
    hosts: server1
    tasks:
        -
            name: 'Create ansible-test folder on server1'
            command: mkdir ./ansible-test creates=./ansible-test 
```

Running [create-folder.yaml](create-fodler.yaml):

```bash
eden@master ansible-lab$ ansible-playbook create-folder.yaml  -i inventory.txt

PLAY [Create ansible-test folder in target machine] ****************************

TASK [Gathering Facts] *********************************************************
ok: [server1]

TASK [Create ansible-test folder on server1] ***********************************
[WARNING]: Consider using the file module with state=directory rather than
running 'mkdir'.  If you need to use command because file is insufficient you
can add 'warn: false' to this command task or set 'command_warnings=False' in
ansible.cfg to get rid of this message.
changed: [server1]

PLAY RECAP *********************************************************************
server1                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Checking server1, we see that the folder was created.

```bash
eden@server1 ~$ pwd
/home/eden
eden@server1 ~$ ll
total 0
drwxrwxr-x. 2 eden eden 6 Jan 24 05:19 ansible-test
```
________________________________________

## SCRIPT ##

Runs a local script (located on the master or controller machine) on a remote node after transferring it. The way ansible does this, it copies the script onto the remote nodes and runs them there.

A sample playbook which uses the script module:

```yaml
-
    name: Demo for usign script module
    hosts: all
    tasks:
        -
            name: Run script on remote server
            script: /some/local/script.sh -arg1 -arg2
```
____________________________

## SERVICES MODULE ##

Used to manage and maintain services on a system. This includes **start, stop,** or **restarting** services. For this, there's two ways to write the statements:

The first method is having the parameters of the **services** module in one line:

```yaml
-
    name: Start services in order
    host: all
    tasks:
        -
            name: Start the DB service
            service: name=mysql state=started
```

The second method is writing the parameters in
separate lines.

```yaml
-
    name: Start services in order
    host: all
    tasks:
        -
            name: Start the DB service
            service: 
                name: mysql 
                state: started
```
<br>

**Why is the state STARTED and not START?**
Recall that we are telling Ansible the **desired state**. Thus we use **started** to instruct Ansible to ensure thathttpd is started. This also means:

    if httpd is not already started ----> start it
    if httpd is already started,    ----> do nothing

As mentioned before, this is the concept of **IDEMPOTENCY** - an operation is idempotent is the result of performing it once is exactly the same as the result of performing it repeatedly without any intervening actions
__________________________________________

## LINEINFILE MODULE ##

Search for a line in a file and replace it or add it if it does nto exist. For example, we're given a task to add servers to the /ansible-test/sample-file.txt file in server1. (Note that you shuold have a ~/ansible-test/sample-file.txt in server1)

- server 10.0.0.125
- server 10.0.0.126

The way to do this is to use the lineinfile module.
Here we have [add.yaml](add.yaml)

```yaml
-
    name: Add servers to /ansible-test/sample-file.txt on server1
    hosts: server1
    tasks:
        -
            name: Add first server to /ansible-test/sample-file.txt
            lineinfile: 
                path: ~/ansible-test/sample-file.txt
                line: 'server 10.0.0.125'
        -
            name: Add second server to /ansible-test/sample-file.txt
            lineinfile: 
                path: ~/ansible-test/sample-file.txt
                line: 'server 10.0.0.126'
```

Running [add.yaml](add.yaml):

```bash
eden@master ansible-lab$ ansible-playbook add.yaml -i inventory.txt 

PLAY [Add servers to /ansible-test/sample-file.txt on server1] ******************************************************
TASK [Gathering Facts] **********************************************************************************************
ok: [server1]

TASK [Add first server to /ansible-test/sample-file.txt] ************************************************************
changed: [server1]

TASK [Add second server to /ansible-test/sample-file.txt] ***********************************************************
changed: [server1]

PLAY RECAP **********************************************************************************************************
server1                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```
Checking on **server1**, you should see that the two server addresses are added to the destination file.

```bash
eden@server1 ansible-test$ pwd
/home/eden/ansible-test
eden@server1 ansible-test$ cat sample-file.txt 
server 10.0.0.125
server 10.0.0.126
```
_______________________________________________________