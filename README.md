## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Red Network Diagram/ Red Network Diagram.drawio

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

PENTEST.YML
  ---
- name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks:
  - name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    apt:
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present

  - name: download and launch a docker web container
    docker_container:
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      published_ports: 80:80

  - name: Enable docker service
    systemd:
      name: docker
      enabled: yes
INSTALL-ELK.YML
---
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
    
    # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
      
    # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present
    
    # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144
      
    # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: 262144
        state: present
        reload: yes
      
    # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044
FILEBEAT-PLAYBOOK.YML
---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:
  
  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb 
 
  - name: install filebeat deb
    command: dpkg -i filebeat-7.4.0-amd64.deb 
  
  - name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml
  
  - name: enable and configure system module
    command: filebeat modules enable system
  
  - name: setup filebeat
    command: filebeat setup
  
  - name: Start filebeat service
    command: service filebeat start

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly __open__, in addition to restricting __access___ to the network.
- _TODO: What aspect of security do load balancers protect? Application availibity. What is the advantage of a jump box?_ When there is an attack, the jumpbox uses remote connections for the cloud network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the config_____ and system files_____.
- _TODO: What does Filebeat watch for?_Monitor log files
- _TODO: What does Metricbeat record?_ It records stats from virtual machines.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.1   | Linux            |
| Web-1    | DVWA     | 10.1.0.5   | Linux            |
| Web-2    | DVWA     | 10.1.0.6   | Linux            |
| Web-3    | DVWA     | 10.0.0.8   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the __JumpBox___ machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
Add whitelisted IP addresses_ 173.75.225.7

Machines within the network can only be accessed by __JumpBox___.
- _TODO: Which machine did you allow to access your ELK VM? JumpBox What was its IP address?_ 10.0.0.5

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 173.75.225.7         |
| Web-1    | Yes                 | 173.75.225.7         |
| Web-2    | Yes                 | 173.75.225.7         |
| Web-3    | Yes                 | 173.75.225.7         |
| Elk      | Yes                 | 173.75.225.7         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible?_ Main advantage is helps considerably with the representation of Infrastructure as Code.

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ... Pentest.yml
		-Downloads and launches the DVWA Docker container
            -Enables the Docker service
		-Installs Docker
		-Installs Python
		-Installs Docker's Python Module
      Install-elk.yml
		-Increase memory to support the ELK stack
		-Download and launch the Docker ELK container
		-Installs Docker
		-Installs Python
		-Installs Docker's Python Module
	Filebeat-playbook.yml
		-Installs Filebeat
		-Configures the system module
		-Configures and launches Filebeat
		

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Docker PS] "C:\Users\chris\OneDrive\Pictures\docker ps.png"

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
Web-1 10.1.0.5
Web-2 10.0.0.6
Web-3 10.0.0.8

We have installed the following Beats on these machines:
Metricbeat and filebeat

These Beats allow us to collect the following information from each machine:
Filebeats collects log events from the virtual machine.
Microbeats collects system metrics from the virtual machine.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the __playbook___ file to _Ansible Docker container____.
- Update the __ansible hosts file to include webservers and elkservers.
- Run the playbook, and navigate to the container to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_ Pentest.yml is the playbook and you copy it from the web VM.
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_ Change and update in hosts file. Make sure the private IP is under elkservers.
- _Which URL do you navigate to in order to check that the ELK server is running?  http://[your.ELK-VM.External.IP]:5601/app/kibana. filebeats.

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
