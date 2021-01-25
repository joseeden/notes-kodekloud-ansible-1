<!-- 2021-01-25 00:02:13 -->

# 9 - Practice: Variables, Conditionals, and Loops#
________________________________________________________________

These are the practice questions/labs included in the course. If you want to experience the real deal of configuring in the actual KodeKloud platform, you can check out their course, [Ansible for Absolute Beginners](https://kodekloud.com/p/ansible-for-the-absolute-beginners).

Note that these are **not the exact same questions**. You can try to answer them, simulate the problems in your own machines.

When you're done, click 'Answer' to see if you got it correctly.

Good luck!
______________________________________________

**1. Our task is to update the nameserver in the /etc/resolv.conf file of the local host with the new ip that is defined in this inventory file:** 

```bash
$ cat inventory.txt
localhost ansible_connection=localhost nameserver_ip=10.1.250.10
```

Do all the necessary additions in the update.yaml below to achieve the task.**

```yaml
-
    name: Update /etc/resolv.conf
    hosts: localhost
    tasks:
        -
            lineinfile:
                path: /etc/resolv.conf
                line: "nameserver "
```

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: Update /etc/resolv.conf
    hosts: localhost
    tasks:
        -
            lineinfile:
                path: /etc/resolv.conf
                line: "nameserver {{ nameserver_ip }}"
```
</details>

__________________________________________

**2. We are asked to update the yaml file and remove the hardcoded snmp port and instead use variable.**

```yaml
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    tasks:
        -
            name: 'Update nameserver entry into resolv.conf file'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver {{ nameserver_ip }}'
        -
            name: 'Disable SNMP Port'
            firewalld:
                port: 160-161
                permanent: true
                state: disabled
```
The snm port is defined in the **inventory file** below:
```bash
localhost ansible_connection=localhost nameserver_ip=10.1.250.10 snmp_port=160-161
```
<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    tasks:
        -
            name: 'Update nameserver entry into resolv.conf file'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver {{ nameserver_ip }}'
        -
            name: 'Disable SNMP Port'
            firewalld:
                port: "{{ snmp_port }}"
                permanent: true
                state: disabled

```
</details>

______________________________________

**3. Update the playbook below to remove hardcoded values for *car_model, country_name,* and *job_title* and instead use variable.**

```yaml
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    tasks:
        -
            name: 'Print my car model'
            command: 'echo "My car''s model is BMW M3"'
        -
            name: 'Print my country'
            command: 'echo "I live in the USA"'
        -
            name: 'Print my title'
            command: 'echo "I work as a Systems Engineer"'
```

<br>

<details>
<summary> Answer: </summary>

```yaml
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    vars:
        car_model: "BMW M3"
        country_name: "USA"
        job_title: "Systems Engineer"
    tasks:
        -
            name: 'Print my car model'
            command: 'echo "My car''s model is {{ car_model }}"'
        -
            name: 'Print my country'
            command: 'echo "I live in the {{ country_name }}"'
        -
            name: 'Print my title'
            command: 'echo "I work as a {{ job_title }}"'

```
</details>

_____________________________________________

**4. Update the playboook belo so that it will start the mysql service if the host is a *server4.corp.com*** 

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: all_servers
    tasks:
```

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: all_servers
    tasks:
        -
            service: 'name=mysql state=started'
            when: ansible_host == "server4.corp.com"
```
</details>

__________________________________________

**5. Update the YAML file below and use conditionals to print statements based on age. If age<18, then that is consider a *child*, otherwise, age>=18 is considered an adult.**

```yaml
-
    name: 'Am I an Adult or a Child?'
    hosts: localhost
    vars:
        age: 25
    tasks:
        -
            command: 'echo "I am a Child"'
        -
            command: 'echo "I am an Adult"'
```

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Am I an Adult or a Child?'
    hosts: localhost
    vars:
        age: 25
    tasks:
        -
            command: 'echo "I am a Child"'
            when: 'age < 18'
        -
            command: 'echo "I am an Adult"'
            when: 'age >= 18'

```

</details>

__________________________________________

**6. The playbook file below is already correct but we are tasked to update it to use the lineinfile instead of the *when***

```yaml
-
    name: 'Add name server entry if not already entered'
    hosts: localhost
    tasks:
        -
            shell: 'cat /etc/resolv.conf'
            register: command_output
        -
            shell: 'echo "nameserver 10.0.250.10" >> /etc/resolv.conf'
            when: 'command_output.stdout.find("10.0.250.10") == -1'
```

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Add name server entry if not already entered'
    hosts: localhost
    tasks:
        -
            shell: 'cat /etc/resolv.conf'
            register: command_output
        -
            lineinfile:
                path: '/etc/resolv.conf'
                line: 'nameserver 10.0.250.10'
```

</details>

__________________________________________

**7. Continue the playbook below by printing all the *item* in the fruits variable.**


```yaml
-
    name: 'Print list of fruits'
    hosts: localhost
    vars:
        fruits:
            - Apple
            - Banana
            - Grapes
            - Orange
    tasks:
        -
            command: 'echo '

```

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Print list of fruits'
    hosts: localhost
    vars:
        fruits:
            - Apple
            - Banana
            - Grapes
            - Orange
    tasks:
        -
            command: 'echo "{{ item }}"'
            with_items: '{{ fruits }}'

```

</details>

__________________________________________

**8. Modify the playbook below to install all the packages**


```yaml
-
    name: 'Install required packages'
    hosts: localhost
    vars:
        packages:
            - httpd
            - binutils
            - glibc
            - ksh
            - libaio
            - libXext
            - gcc
            - make
            - sysstat
            - unixODBC
            - mongodb
            - nodejs
            - grunt
    tasks:
        -
            yum: 'name=httpd state=present'
```

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Install required packages'
    hosts: localhost
    vars:
        packages:
            - httpd
            - binutils
            - glibc
            - ksh
            - libaio
            - libXext
            - gcc
            - make
            - sysstat
            - unixODBC
            - mongodb
            - nodejs
            - grunt
    tasks:
        -
            yum:
                name: '{{ item }}'
                state: present
            with_items: '{{ packages }}'
  
```

</details>

__________________________________________
