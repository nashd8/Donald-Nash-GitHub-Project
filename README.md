## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Cloud Infrastructure](Diagrams/Cloud_Infrastructure.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select playbook files may be used to install only certain pieces of it, such as Filebeat.

### _Resources_

### Ansible Playbook Files

- [Elk Playbook File](Ansible/elk.yml)
- [DVWA Playbook File](Ansible/dvwa.yml)
- [Filebeat Playbook File](Ansible/filebeat.yml)
- [Metricbeat Playbook File](Ansible/metricbeat.yml)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available and reliable, in addition to restricting hackers and unwanted traffic to the network.
- Load Balancers offer additional levels of security to your web applications via a Web Application Firewall, as well as protecting against DDoS attacks by distributing requests equally to each server. Further, health probes ensure that if a response is not received by a server in the Load Balancers back end pool that no further traffic will be sent to that server, as it will assume the server is dead.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
- Filebeat watches for log events and is used to monitor log files and forward to logstash for indexing
- Metricbeat watches for metrics from the operating system, containers and services running on the servers

The configuration details of each machine may be found below.

| Name          | Function    | IP Address                               |   Operating System   |
|---------------|-------------|------------------------------------------|----------------------|
| Jump Box      | Gateway     | Private: 10.0.0.4 Public: 40.78.99.229   | Linux (Ubuntu 18.04) |
| Web Server 1  | Application | 10.0.0.5                                 | Linux (Ubuntu 18.04) |
| Web Server 2  | Application | 10.0.0.6                                 | Linux (Ubuntu 18.04) |
| ELK Server    | Logs        | Private: 10.1.0.4 Public: 52.255.141.157 | Linux (Ubuntu 18.04) |

### Access Policies

The machines on the internal network are not exposed to the public Internet except for the Jumpbox and web application interfaces. 

Only the Jumpbox machine accept connections from the Internet. Access to this machines is only allowed from the following IP address:
- 135.23.84.40

The Load Balancer can accept HTTP connection via port 80 in order to access the DVWA web Interface.
The ELK Stack can accept HTTP connection via port 5601 in order to access the Kibana web Interface.

HTTP access to these machines are only allowed from the following IP address:
- 135.23.84.40

Machines within the network can only be accessed by the ansible container on the Jumpbox.
- _Note: Internal access to my ELK VM can only be obtained by my Jumpbox; IP:10.0.0.4, External access to my ELK VM's Kibana interface can only be obtained on port 5601 by my local machine; IP: 135.23.84.40_

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP Addresses    |
|---------------|---------------------|-------------------------|
| Jump Box      | Yes                 | 135.23.84.40            |
| Load Balancer | Yes                 | 135.23.84.40            |
| ELK Server    | Yes                 | 10.0.0.4 / 135.23.84.40 |  
| Web Server 1  | No                  | 10.0.0.4                |
| Web Server 2  | No                  | 10.0.0.4                |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows for Continuous Integration/Deployment. Infrastructure as Code allows IT administrators to push configuration files to new machines and networks, and conintuously update machines, containers, and networks. This allows for more time to focus on more important tasks that may deliver more value to the organization. 

The playbook implements the following tasks:
- Install Docker.io
- Install Python pip3
- Install Docker module
- Increase virtual memory
- Install Elk container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Docker ps results](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web Server 1; IP: 10.0.0.5
- Web Server 2; IP: 10.0.0.6

We have installed the following Beats on these machines:
- Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeats monitors log files or specified locations in order to collect log events. Logs can include system logs, SSH login logs, Sudo command logs etc. 
- Metricbeats collects metrics from the Operating System and services running on the servers. This includes CPU usage, memory usage, total numbers of containers/dockers etc.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml file to /etc/ansible in your ansible container. _Note: you may also run the curl command to download the config file: curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml_
- Update the filebeat-config.yml file to include the following
    - username and password
    - Go to line #1106 and replace the IP address with the IP address of your ELK machine/elasticsearch port.
      
      output.elasticsearch:
      
      hosts: ["10.1.0.4:9200"]
      
      username: "elastic"
      
      password: "changeme"

    - Go to line #1806 and replace the IP address with the IP address of your ELK machine/kibana port.
     
      setup.kibana:
      
      host: "10.1.0.4:5601"
      
    - save this file to /etc/ansible/filebeat-config.yml

- Run the playbook, and navigate to kibana to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- The 'filebeat playbook file' listed under 'Resources' at the top of this document is the playbook file, this file should be copied to /etc/ansible in your ansible container.
- To make Ansible run a playbook on a specific machine, we must first update the 'hosts' file in the ansible container under /etc/ansible. In this config file we can add specific servers with IP addresses to allow playbooks to be run on specific machines. We then specify the host in the playbook in order for it to run on those specified machines. 

The following screenshot displays the 'hosts' configuration file with webservers and elkservers

![Hosts config file](Images/hosts_config_file.png)

We then specify either 'webservers' or 'elkservers' in the playbook yaml file to specify which machine to install the ELK over which machines to install our beats. Please review playbooks under resources at the beginning of this document for reference.

- To ensure the ELK server is running, navigate to the following URL http://52.255.141.157:5601/app/kibana#/home?_g=() _Note: This may vary depending on your ELK servers public IP address_

### Bonus: Commands to download and run Elk playbook to install elk container
_Specific commands the user will need to run to download the playbook, update the files, etc._

- SSH into your Ansible control node
- cd /etc/ansible  _Note: command to change directory within ansible container_ 
- curl -O https://raw.githubusercontent.com/nashd8/Donald-Nash-GitHub-Project/main/Ansible/elk.yml _Note: command to download raw .yml file from this page_
- nano hosts _Note: command to edit hosts file with specified servers_
- nano elk.yml _Note: command to edit downloaded .yml file to change hosts_
- ansible-playbook ./elk.yml _Note: command to run the playbook_