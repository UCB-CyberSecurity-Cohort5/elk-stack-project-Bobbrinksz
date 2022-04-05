The files in this repository were used to configure the network depicted below.
![image](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/diagram/Briana%20Norman%20NetWork.jpg)
"Azure Network with Elk Server Diagram"

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the [AllPlayBooks aka JumpBox Files](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/tree/main/Jumpbox%20Files) file may be used to install only certain pieces of it, such as Filebeat.

- [ElkPlaybook.yml](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/elkplaybook.yml) - this Playbook installs Elk Ansible container. 
- [filebeat-playbook.yml](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/filebeat-playbook.yml) - This is a Playbook that installs and launches the filebeat service
- [filebeatconfig.yml](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/filebeatconfig.yml) - This is the full FileBeat Configuration documented in a file listing all non-deprecated options and its comments that have its corresponding updated IP Addressess
- [metricbeat-playbook.yml](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/metricbeat-playbook.yml) - This Metricbeat Playbook shows what is being installed to the Docker Metrics/metricbeat
- [metricbeat-config.yml](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/metricbeat-config.yml) - This metricbeat configuration file illustrates its  most commonly used options and highlights them
- [vmplaybook.yml](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/vmplaybook.yml) - This is a configured Web Docker VM that has Ansible along with Launch Web DVWA Docker Container
- [host file](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/host%20file) - This is the default ansible 'hosts' file
- [Config](https://github.com/UCB-CyberSecurity-Cohort5/elk-stack-project-Bobbrinksz/blob/main/Jumpbox%20Files/Config) - This is the configuration file for ansible



This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology
The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.

_TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_
- Load Balancers play a significant role in both aspects of security and protection. The function of the "off-loading" in a load balancer is critical for defending company's or organizations from DDoS (denail of service) attacks by balancing or shifting the incoming traffic  and additional attack traffic from a company or corporate server to multiple servers which can include a public cloud provider, which then prevent the DDoS from executing because the server has not been overloaded with request traffic on one or smaller servers.
- The advantages of a Jump box are:

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the machine and its metrics aka traffic and system logs.

_TODO: What does Filebeat watch for?_
- Filebeat forwards things like; monitoring log files (full or seleced and specified) along with a list log events to Logstash for indexing (or to Elasticsearch for indexing).

_TODO: What does Metricbeat record?_
- Metricbeat is a simplified (or lightweight) agent/shipper that is utilized in collecting the systems metrics along with more things like application metrics and then forwards them to Elasticsearch aka Elastic Stack Server. 

The configuration details of each machine may be found below.

_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.
| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web1     |DVWA Container|10.0.0.5| Linux            |
| Web2     |DVWA Container|10.0.0.6| Linux            |
| Web3     |DVWA Container|10.0.0.7| Linux            |

### Access Policies
The machines on the internal network are not exposed to the public Internet.
Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed connection through the "LoadBalancer FrontEnd IP" which has the following IP addresses:

TODO: Add whitelisted IP addresses_
Machines within the network can only be accessed by _____.
- 13.67.200.90

TODO: Which machine did you allow to access your ELK VM? What was its IP address?
- 10.0.0.4

**A summary of the access policies in place can be found in the table below**.

| Name     | Publicly Accessible |Allowed IP Addresses  |
|----------|---------------------|----------------------|
| Jump Box | No                  |13.67.200.90/22, 13.67.200.90/80|
| Web1     | No                  |10.0.0.4              |
| Web2     | No                  |10.0.0.4              |
| Web3     | No                  |10.0.0.4              |


### Elk Configuration
Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because... 
_it benefits overall costs reductions due to the downtime and costs not occured prior to deployment. Additionally, due to the limited need to monitor automated scripted tasks the consistency and reliability increases tenfold because of these checked, tested, made and deloyed scripts. Better operations management and efficiency by enabling and putting IaC aka Infrastructure as Code. 

_TODO: What is the main advantage of automating configuration with Ansible?_

---

## The playbook implements the following tasks
    ---
    - name: Configure Elk VM with Docker
      hosts: elk
      remote_user: RedAdmin
      become: true
      tasks:

### Using apt install module - installing docker.io using apt
    - name: installing docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

### Using apt install module - installing python3-pip using apt
    - name: installing pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

### Using python module - installing the docker python module with pip
    - name: installing python module
      pip:
        name: docker
        state: present

### Using a sysctl module - increasing virtual memory to "262144"
    - name: more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

### Using a DockerContainer module - download and launch the docker elk container with the image "sebp/elk:761"
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

### Using a control system daemon module aka a systemd module - enabling docker service on Boot systemd
    - name: enabling service doc
      systemd:
        name: docker
        enabled: yes

_TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ...
- ...

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_
We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_
These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:
SSH into the control node and follow the steps below:
- Copy the _____ file to _____.
- Update the _____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.
_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?
_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
