# WE LEARN HOW TO WRITE PLAYBOOKS IN THIS CHAPTER

## NOTE:
* PLAYBOOKS ARE ALWAYS STORED IN .YML FORM. 
* Execution: anisble-playbook PlaybookName.yml

### FirstPlaybook.yml (Installing apache package in remote servers)

    ---
    - hosts: dbserver01
      become: True
      gather_facts: no
    tasks:
    - name: Install apache httpd on dbserver01
      yum: name=httpd state=present
    - name: Start Apache httpd
      service: name=httpd state=started
    - name: check Apache httpd status service
      command: systemctl status httpd

    - hosts: webserver01
      become: True
      gather_facts: no
    tasks:
    - name: Install Apache on webserver01
      apt: name=apache2 state=present
    - name: Start Apache
      service: name=apache2 state=started
    - name: Check status
      command: systemctl status apache2


### SecondPlaybook.yml (Create User on REMOTE SERVER)

    ---
    - hosts: all
      become: true
      gather_facts: no
      
    vars:
      username: Rambo
      shell: /bin/bash
      password: ramb
    tasks:
    - name: create user
      username: "{{ username }}"
      shell: "{{ shell }}"
      password: "{{ password | password_hash('sha512') }}"

### After execution how to check weather user is created or not
    
    Cd ansible_modules
    Ssh -i Ansible_Demo.pem ubuntu@ubuntu_IPPrivate_Address
    Id  Rambo
    Exit

    Ssh -i Ansible_Demo.pem centos@centos_IPPrivate_Address
    Id  Rambo
    Exit

### DYNAMICALLY CREATING SAME THING:
        ---
        - hosts: all
          become: true
          gather_facts: no
      
        vars_prompt:
        - name: "username"
          prompt: "Please enter Username"
          private: no
        - name: "shell"
          prompt: "Please specify shell"
          private: no
        - name: "password"
          prompt: "Please enter Password"
          private: yes

        tasks:
        - name: create user
          user:
          name: "{{ username }}"
          shell: "{{ shell }}"
          password: "{{ password | password_hash('sha512') }}"

####  AFTER RUNNING SUCCESSFULL
-	cd ansible_module
-	ssh -i Ansible_Demo.pem centos@centos_IP_Address
-	id abhi 		| id reddy	| id abhishek
-	su abhi -> to change from one user to abhi
-	su reddy -> change abhi to reddy
-	su abhishek -> change from reddy to abhishek 

### DISPLAY ALL THE OPTION AND ITS OUTPUT ON DEBUG SCREEN

#### Create new commands.yml file

    ---
    - hosts: all
      become: true
      gather_facts: no

    tasks:
    - name: check the disk space
      command: df -hT
      register: disk
    - debug: var=disk.stdout_lines

    - name: checking RAM Space
      command: free -m
      register: disk1
    - debug: var=disk1.stdout_lines

    - name: checking virtual memory space
      command: vmstat
      register: disk2
    - debug: var=disk2.stdout_lines

    - name: checking process list
      command: ps aux
      register: disk3
    - debug: var=disk3.stdout_lines

    - name: Check versions of ansible
      command: ansible --version
      register: disk4
    - debug: var=disk4.stdout_lines

### IF YOU WANT TO COPY OUTPUT TO SOMEFILE
    ansible-playbook commands.yml > output
    ls -l
    vim output

### COPY FILE FROM REMOTE TO REMOTE
    ---
    - hosts: webserver01
      become: true
      gather_facts: no

    tasks:
    - name: copy file from ubuntu server to centos
      synchronize:
         src: /home/ubuntu/ubuntu_file
         dest: /home/centos/
         mode: pull
      delegate_to: dbserver01

### CONDITIONALLY INSTALL APACHE AND HTTPD ON CENTOS AND UBUNTU
    ---
    - hosts: all
      become: True
      gather_facts: yes

    tasks:
    - name: Install apache httpd on dbserver01
      yum: name=httpd state=present
      when: ansible_os_family=="RedHat"

    - name: Start Apache httpd httpd
      service: name=httpd state=started
      when: ansible_os_family=="RedHat"

    - name: Install Apache2 on webserver01
      apt: name=apache2 state=present
      when: ansible_os_family=="Debian"

    - name: Start Apache
      service: name=apache2 state=started
      when: ansible_os_family=="Debian"

    - name: Check Apache status
      command: systemctl status httpd
      when: ansible_os_family=="RedHat"

    - name: check Apache status
      command: systemctl status apache2
      when: ansible_os_family=="Debian"

### USING TAG WE CAN DISPLAY ONLY TAGGED ONE
    ---
    - hosts: all
      become: true
      gather_facts: no

    tasks:
    - name: check the disk space
      command: df -hT
      register: disk
      tags: disktag
    - debug: var= disktag.stdout_lines

    - name: checking RAM Space
      command: free -m
      register: disk1
      tags: ramtag
    - debug: var= ramtag.stdout_lines

    - name: checking virtual memory space
      command: vmstat
      register: disk2
      tags: virtualtag
    - debug: var= virtualtag.stdout_lines

    - name: checking process list
      command: ps aux
      register: disk3
      tags: processtag
    - debug: var= processtag.stdout_lines

    - name: Check versions of ansible
      command: ansible --version
      register: disk4
      tags: versiontag
    - debug: var=versiontag.stdout_lines

#### HOW TO RUN = ansible-playbook tags.yml --tags versiontag

## CREATE EC2 INSTANCE USING ANSIBLE
1.	Install following pip and boto
-	Easy-install pip
-	Pip install boto
2.	Create IAM user
-	Create user
-	Full Adminaccess
-	Download csv file

ec2user.yml

    ---
    - name: Create EC2 instances
      hosts: localhost
      gather_facts: no
    
    vars:
      region: us-east-1
      instance_type: t2.micro
      image: ami-97785bed
      group: Ansible_demo
      key_name: Abhi
    
    # check ec3 docs
    
    tasks:
    - name: Launch Instance
      ec2:
        region: "{{ region }}"
        instance_type: "{{instance_type }}"
        image: "{{ image }}"
        group: "{{ group }}"
        key_name: "{{ key_name }}"
        aws_access_key: 
        aws_secret_key: 
        wait: true
        register: ec2_output
    - debug: var=ec2_output

## ENCLOSE SECRET KEY

### Ansible vault – to store our secret keys in encrypted manner
-	ansible-vault --help
Usage: ansible-vault [create|decrypt|edit|encrypt|encrypt_string|rekey|view] [options] [vaultfile.yml]
-	ansible-vault create aws_key.yml
•	sekey: aws secret key
•	ackey: aws access key
-	ansible-vault view aws_key.yml
-	ansible-vault rekey aws_key.yml
-	ansible-vault edit aws_key.yml
-	ansible-vault decrypt aws_key.yml

### 1st method
To execute ansible vault ec2.yml.

    sudo ansible-playbook ec2.yml - -ask-vault-pass

### 2nd method

    vim .keys.txt
    password
    wq!

    sudo ansible-playbook ec2.yml - -vault-password-file .keys.txt

### 3rd method
Mention keys.yml file in ansible.cfg file

    vim ansible.cgf 
    Add at the end  Vault_password_file=./.keys.txt
    wq!

    sudo ansible-playbook ec2.yml

