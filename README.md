# Automated ELK Stack Deployment

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

This repository includes code defining the infrastructure below.


![Atul Afre - HW_Unit13-Final](https://user-images.githubusercontent.com/82840381/128785211-d0cb0eee-b723-4ffe-8145-b8b038abdb11.jpg)


The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the "D*mn Vulnerable Web Application"

Load balancing ensures that the application will be highly <strong>available</strong>, in addition to restricting <strong>inbound access </strong>  to the network. 

The load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers. Access controls will ensure that only authorized users — namely, ourselves — will be able to connect in the first place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network, as well as watch system metrics, such as CPU usage; attempted SSH logins; sudo escalation failures; etc.

The configuration details of each machine may be found below.



| Name     | Function   | IP Address | Operating System |
|----------|------------|------------|------------------|
| Jump Box | Gateway    | 10.0.0.4   | Linux            |
| DVWA 1   | Web Server | 10.0.0.5   | Linux            |
| DVWA 2   | Web Server | 10.0.0.6   | Linux            |
| ELK      | Monitoring | 10.1.0.4   | Linux            |


In addition to the above, Azure has provisioned a <srong>load balancer</string> in front of all machines (Web Servers only) except for the jump box. The load balancer's targets are organized into the following availability zones:

- <strong>Availability Zone 1:</strong> DVWA 1 + DVWA 2</li>


## ELK Server Configuration

The ELK VM exposes an Elastic Stack instance. Docker is used to download and manage an ELK container.

Rather than configure ELK manually, we opted to develop a reusable Ansible Playbook to accomplish the task. This playbook is duplicated below.

To use this playbook, one must log into the Jump Box, then issue: ansible-playbook install_elk.yml elk. This runs the install_elk.yml playbook on the elk host.


### Access Policies

The machines on the internal network are not exposed to the public Internet.
Only the <strong>jump box </strong> machine can accept connections from the Internet. Access to this machine is only allowed from the IP address 45.30.187.133


- <strong>Note:</strong> Your answer will be different


Machines within the network can only be accessed by <strong>each other</strong>. The DVWA 1 and DVWA 2 VMs send traffic to the ELK server.

A summary of the access policies in place can be found in the table below.


| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 45.30.187.133        |
| ELK      | Yes / No            | 10.0.0.1-254         |
| DVWA 1   | No                  | 10.0.0.1-254         |
| DVWA 2   | No                  | 10.0.0.1-254         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...

- You can run a playbook that will configure multiple machines at the same time, with the exact configurations
- Simple to configure, human-readable playbook and the commands are ran in the order they are written
- Errors encoutered will be required to only be fixed once.

The playbook implements the following tasks:

- Install docker io into system
- Installed python3-pip, package manager
- Installed python package docker
- Download elk image, and open the ports for Kibana usage


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Docker ELK-SERVER](https://user-images.githubusercontent.com/82840381/128792232-8a346f72-3208-4294-aa27-c5f202f1a871.png)


The playbook (install-elk.yml) to confifure ELK server is duplicated below.

```
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: sysadmin
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

        # Use docker_container module
      - name: download and launch a docker elk container
        docker_container:
          name: elk
          image: sebp/elk:761
          state: started
          restart_policy: always
          published_ports:
            - 5601:5601
            - 9200:9200
            - 5044:5044

        # Use systemd module
      - name: Enable service docker on boot
        systemd:
          name: docker
          enabled: yes

```


### Target Machines & Beats
This ELK server is configured to monitor the following machines:

- Web-1: 10.0.0.5
- Web-2: 10.0.0.6


We have installed the following Beats on these machines:

- FileBeat
- MetricBeat

These Beats allow us to collect the following information from each machine:

- FileBeat: Monitor file changes on the system(s)
- MetricBeat: Monitor system metrics like CPU usage, RAM, etc.






### Using the Playbook

In order to use the playbooks, you will need to have an Ansible control node already configured. We use the jump box for this purpose.

To use the playbooks, SSH into the control node and follow the steps below:

- Copy the Ansible/elk-playbook.yml file to /etc/ansible/files.
- 
![files](https://user-images.githubusercontent.com/82840381/128797639-982cf4e7-3c4d-4ddd-9d4b-4a1f9b5b1547.png)

- Update the /etc/ansible/hosts file to include the group of machines youre trying to run the playbook on.
- 
![Host](https://user-images.githubusercontent.com/82840381/128797661-9d57fc4d-4b42-40b6-916e-0be6a262fd96.png)

- Run the playbook (Filebeat-playbook.yml) to installs Filebeat on the target hosts. 

```
---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    # Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

    # Use command module
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.4.0-amd64.deb

    # Use copy module
  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

    # Use command module
  - name: Enable and Configure System Module
    command: filebeat modules enable system

    # Use command module
  - name: Setup filebeat
    command: filebeat setup

    # Use command module
  - name: Start filebeat service
    command: service filebeat start

    # Use systemd module
  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes
```

- Navigate to <public_ip_of_ELK-SERVER>:5601/app/kibana to check that the installation worked as expected.

![Dashboard](https://user-images.githubusercontent.com/82840381/128798437-1401e157-b406-48a9-ba65-f7e07018c356.png)


## Making Changes to the repo

Download the repository into your machine

- git clone <repo_url> If changes are made and you want to see the status files
- git status To add files to be commited run
- git add <file_name/folder_name> To commit with a message
- git commit -m "<msg_for_commit>" Publish the changes
- git push

