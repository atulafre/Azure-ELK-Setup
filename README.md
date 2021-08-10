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


In addition to the above, Azure has provisioned a <srong>load balancer</string> in front of all machines except for the jump box. The load balancer's targets are organized into the following availability zones:

<ol>
  <li><strong>Availability Zone 1:</strong> DVWA 1 + DVWA 2</li>
  <li><strong>Availability Zone 2:</strong> ELK item</li>
</ol>


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
To use the playbooks, we must perform the following steps:



SSH into the control node and follow the steps below:

- Copy the Ansible/elk-playbook.yml file to /etc/ansible/files.
- Update the /etc/ansible/hosts file to include the group of machines youre trying to run the playbook on.
- Run the playbook, and navigate to <public_ip_of_elk_machine>:5601/app/kibana to check that the installation worked as expected.







## Making Changes to the repo

Download the repository into your machine

- git clone <repo_url> If changes are made and you want to see the status files
- git status To add files to be commited run
- git add <file_name/folder_name> To commit with a message
- git commit -m "<msg_for_commit>" Publish the changes
- git push


_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
