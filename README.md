# An Introduction to Ansible
## What is Ansible?
It is a simple open source IT engine which automates application deployment, intra service orchestration, cloud provisioning and many other IT tools.
- Ansible uses playbook to describe automation jobs
- Playbook uses very simple language, YAML

Ansible's strenghts are:
- It does not need any extra software installed on the remote computer
- It's fast.
- Most importantly: *It's idempotent*.

Idempotency is a property of Ansible playbooks.  "if
you run them again, they will make only the changes they must in order
to bring the system to the desired state."

## How do I install it?
```
# amazon-linux-extras install ansible2 -y
```
see below link for more details : https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

## Inventory file

In inventory file we will keep connection parameters to the servers. We can create Inventory file `hosts` in the working directory. The default inventory file is `/etc/ansible/hosts`.
### how to write Inventory file
```
[servers]
172.*.*.* ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="key.pem"
```
First line is a group. we can create many groups like [ubuntu], [amazon]. Each group can contain multiple servers. 
Next line contains parameters for connections. We need to start with IP address of our domain. After that specify ssh port to the servers, user and private key path.

## Test connections to your server
In command line execute following command:
```
ansible -i hosts servers -m ping
```
The command indicating like (ansible -i PATH_TO_INVENTORY_FILE [GROUP_NAME|IP_ADDRESS|ALL] -m ping )
You should get SUCCESS status. For debugging failed connections add `-vvvv` parameter to command above.
## Playbooks
Ansible playbooks are lists of tasks that automatically execute against hosts, Groups of hosts form our Ansible inventory. Each module within an Ansible playbook performs a specific task
see below link for more details : https://docs.ansible.com/ansible/latest/user_guide/playbooks.html
### simple playbook structure
Playbook create as a YAML file. eg. playbook.yml
```
---

- name: "Hosting Simple Website On Apache Server"
  hosts: servers
  become: true
  tasks:
```
First line contains host or group from Inventory file. The next lines uses `become` that leads to privileges escalation, run command as root user. The next line is to mention tasks, that run in servers.
### variables
We can use variables with ansible. Variables can load from playbook itself or from external file.
 - If loading from playbook itself, we can use `vars:` to mention the variables.
```
---

- name: "Hosting Simple Website On Apache Server"
  hosts: servers
  become: true
  vars:
    package: "httpd"
  tasks:
    -name : "Install apache"
     yum:
       name: "{{ package }}"
       state: present
```
 The above playbook will install apache in the servers below `servers` group. The `yum` is a module using to install packages.

- variables loading from external file be like below.
```
vi ansible.vars
  package: "httpd"
  
vi plabook.yml

---

- name: "Hosting Simple Website On Apache Server"
  hosts: servers
  become: true
  vars_files:
    - ansible.vars
  tasks:
    -name : "Install apache"
     yum:
       name: "{{ package }}"
       state: present
```
Here, we can mention the external file using `vars_files:`

