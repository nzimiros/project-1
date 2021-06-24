# project-1
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![RedTeam_Network_Diagram (https://github.com/nzimiros/project-1/blob/main/Diagrams/RedTeam%20Network%20Diagram%20.png)] 
(Images/RedTeam_Network_Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the install_elk.yaml file may be used to install only certain pieces of it, such as Filebeat.

```sh
  GNU nano 4.8                                                                                  install_elk.yaml                                                                                            
---

- hosts: Elk
  become: yes
  tasks:

    - name: install docker
      apt:
        name: docker.io
        update_cache: yes
        state: present
        force_apt_get: yes
    - name: install pip
      apt:
        name: python3-pip
        state: present
        force_apt_get: yes
    - name: install python docker module
      pip:
        name: docker
        state: present
    - name: more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
    - name: download and install elk docker container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
    - name: enable docker service
      systemd:
        name: docker
        enabled: yes
```

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the filesystems and system  metrics.

The configuration details of each machine may be found below.

| Name     | Function   | IP Address | Operating System |
|----------|----------  |------------|------------------|
| Jump Box | Gateway    | 10.0.0.1   | Linux            |
| Elk      | Monitoring | 10.1.0.4   | Linux            |
| DVWA 1   | Web Server | 10.0.0.5   | Linux            |
| DVWA 2   | Web Server | 10.0.0.6   | Linux            |
| DVWA 3   | Web Server | 10.0.0.7   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 184.58.21.143


Machines within the network can only be accessed by each other. The DVWA 1, DVWA 2 and DVWA 3 VMs send traffic to the Elk Server

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses |
|----------  |---------------------|----------------------|
| Jump Box   | Yes                 | 184.58.21.143        |
| ELk Server | No                  | 10.0.0.1-254         | 
| DVWA 1     | No                  | 10.0.0.1-254         |
| DVWA 2     | No                  | 10.0.0.1-254         |
| DVWA 3     | NO                  | 10.0.0.1-254         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it make deployment more efficient

The playbook implements the following tasks:
- Install Docker
- Install Python
- Downloand and install elk docker container
- Enable docker on boot



The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker compose ps (https://github.com/nzimiros/project-1/blob/main/Ansible/Images/Screenshot_docker_ps.png)]  
 


### Target Machines & Beats

This ELK server is configured to monitor the following machines:
- DVWA 1 at 10.0.0.5
- DVWA 2 at 10.0.0.6
- DVWA 3 at 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat 
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat: filebeat detects changes to the filesystem. We can use it to collect Apache logs and log events
- Metricbeat: metricbeat detects changes in system metrics such as CPU usage. We use it to detect SSH login attempts, failed sudo escalation and CPU/RAM stats

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

Copy the configuration file to ansible 
- cd /etc/ansible
- mkdir files
- #clone repository with files
- git clone https://github.com/nzimiros/project-1.git
- ls
- #move playbooks stored in Ansible folder to etc/ansible/files
- cp project-1/Ansible ./files

Update the hosts file to include the private address 
- #add hosts  of VMs to use be used by containters to /etc/ansible/hosts
- nano /etc/ansible/hosts
```sh
	- [webservers]
	- 10.0.0.5
	- 10.0.0.6
	- 10.0.0.7
	- [Elk]
	- 10.1.0.4
```
Run the playbook, and navigate to http://10.1.0.4:5601 (this is the address of Kibana) to check that the installation worked as expected.
- cd /etc/ansible
- ansible-playbook install_elk.yml Elk
- ansible-playbook install_filebeat.yml webservers https://github.com/nzimiros/project-1/blob/main/Ansible/install_filebeat.yml
- ansible-playbook install_metricbeat.yml webservers https://github.com/nzimiros/project-1/blob/main/Ansible/install_metricbeat.yml
- wait a few mininutes and type curl http://10.1.0.4:5601 in terminal and it should give an html output


_
