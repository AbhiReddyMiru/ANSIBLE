# ANSIBLE
## TODAY WE ARE WORKING WITH ANSIBLE BY CREATING THREE EC2 SERVERS. ONE FOR CONTROLL SERVER(CENTOS) AND TWO ARE REMOTE SERVERS (UBUNTU, CENTOS)
### INSTALLATION OF PYHTON.
### 1.	Centos Control Server
-	sudo yum install -y epel-release 
-	yum update -y
- yum install -y sshd
- systemctl start sshd
-	systemctl status sshd
-	yum install -y ansible
-	ansible –version

### 2.	Ubuntu Server (Node – 1)
-	sudo apt-get update
-	sudo apt-get  install ansible

### 3.	Centos Server (Node -2)
-	yum update -y
-	yum install -y gcc
-	yum install -y python-setuptools
-	easy_install pip
-	yum install -y python-devel
-	pip install ansible
### THINGS TO KNOW
*	INVENTORY FILE: Plain text file describe remote server details
*	Modules: Has set of actions to perform some task
*	Playbooks: Each task considered as a play. Multiple plays considered as a playbook
*	JSON: Java Script Object Notation Format 
*	rpm -qc ansible => check default installed packages
### CONFIGURATION SETTING ORDER OF OPERATIONS
*	$ANSIBLE_CONFIG => Environmental variable
*	./ansible.cfg => PWD
*	~/.ansible.cfg => Home Directory 
*	/etc/ansible/ansible.cf => Global Level Inventory File
### SETUP FROM CONTROLL SYSTEM TO REMOTE SERVER.
1.	Copy key.pem file content
-	vim Ansible_Demo.pem		=> copy key.pem content.
2.	Change permission of the file.
-	chmod 400 Ansible_Demo.pem 
### 4. CREATE AN INVENTORY FILE IN CONTROL SERVER (Plain text file describe remote server details)
1. Make Directory Ansible_Ping
*       mkdir ansible_ping
        cd ansible_ping
        vim inventory (write details for BOTH the nodes as below given pattern)
        
        <Private_ipaddress1> <ansible_ssh_user=Node1_usrname> <ansible_ssh_private_Key_file=/path/to/the/key/file/Ansible_Demo.pem>
        <Private_ipaddress2> <ansible_ssh_user=Node2_usrname> <ansible_ssh_private_Key_file=/path/to/the/key/file/Ansible_Demo.pem> 
-	Exit from file
2.	Now ping both the nodes from the control server
*      Sudo ansible all -i inventory -m ping 
