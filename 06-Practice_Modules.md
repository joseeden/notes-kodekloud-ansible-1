<!-- 2021-01-24 00:51:24 -->

# 6 - Practice: Modules #
________________________________________________________________

These are the practice questions/labs included in the course. If you want to experience the real deal of configuring in the actual KodeKloud platform, you can check out their course, [Ansible for Absolute Beginners](https://kodekloud.com/p/ansible-for-the-absolute-beginners).

Note that these are not the exact same questions. You can try to answer them and create a Playbook file in your own machines based on the questions.

When you're done, click 'Answer' to see if you got it correctly.

Good luck!
______________________________________________

**1. Create a  playbook with a play to Execute a script on a group of web server nodes called "web_nodes". The script is located at /tmp/install_script.sh**

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Execute a script on all web server nodes'
            script: /tmp/install_script.sh
```

</details>

______________________________________________

**2. Update the playbook below to add a new task to start httpd services on all web nodes. This task should be named 'Start httpd service'.**

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
```

Hint: use the [service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html) module

<br>

<details>
<summary> Answer: </summary>
```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service: 'name=httpd state=started'
```

</details>

_________________

**3. Update the playbook to add a new task in the beginning to add an entry into /etc/resolv.conf file for hosts. The line to be added is nameserver 10.1.250.10**

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present

```

Hint: Use the [Lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html) module**

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Execute a script on all web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Update entry into /etc/resolv.conf'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 10.1.250.10'
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present

```

</details>

____________________________________

**4. Update the playbook to add a new task at second position (right after adding entry to resolv.conf) to create a new web user.**


User details to be used are given below:
- Username: web_user
- uid: 1040
- group: developers

```yaml
-
    name: 'Execute a script on all web server nodes and start httpd service'
    hosts: web_nodes
    tasks:
        -
            name: 'Update entry into /etc/resolv.conf'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 10.1.250.10'
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present

```

Hint: Use the [user module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html) module.

<br>

<details>
<summary> Answer: </summary>

```yaml
-
    name: 'Execute a script on all web server nodes and start httpd service'
    hosts: web_nodes
    tasks:
        -
            name: 'Update entry into /etc/resolv.conf'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 10.1.250.10'
        -
            name: 'Create a new user'
            user:
                name: web_user
                uid: 1040
                group: developers
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present

```

</details>

____________________________________
