## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![TODO: Update the path with the name of your diagram](Images/diagram_filename.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.
```
  ---
# install_elk.yml
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

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

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
          
```
This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build'


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
Load Balancers allow admins to configure what machines recieves traffic and when. Allowing traffic to be shared on multiple servers to distribute load. Protecting webservers from certain security vulnerabilites as well.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems and system metrics.
- Filebeat monitors the log files or file locations that you specify for changes.
- Metricbeat monitors the metrics and statistics from the machines it is on and then collects and ships them to the elasticsearch or log stack.

The configuration details of each machine may be found below.

| Name       | Function   | IP Address | Operating Sytem |
|------------|------------|------------|-----------------|
| Jump Box   | Gateway    | 10.0.0.4   | Linux           |
| Web1       | Web Server | 10.0.0.5   | Linux           |
| Web2       | Web Server | 10.0.0.7   | Linux           |
| ELK Server | ELK Stack  | 10.1.0.4   | Linux           |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 216.255.213.17

Machines within the network can only be accessed by each other.
-Jump Box Provisioner IP: 10.0.0.4

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accesible | Allowed IP Addresses     |
|------------|--------------------|--------------------------|
| Jump Box   | Yes                | 216.255.213.17           |
| Web1       | No                 | 10.0.0.1/254             |
| Web2       | No                 | 10.0.0.1/254             |
| ELK Server | No                 | 10.0.0.1/254             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- The configuration can be replicated many times without any differences or variations.

The playbook implements the following tasks:
```
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
          
```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[Docker Output](Images/docker.PNG)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.5
- 10.0.0.7
We have installed the following Beats on these machines:
- FileBeat
- Metric beat

These Beats allow us to collect the following information from each machine:
- Filebeat monitors the log files or file locations that you specify for changes.
- Metricbeat monitors the metrics and statistics from the machines it is on and then collects and ships them to the elasticsearch or log stack.


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook file to ansible control node.
- Update the hosts file to include your web servers and elk server
- Run the playbook, and navigate to http://40.118.170.243:5601 to check that the installation worked as expected.
