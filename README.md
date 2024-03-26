# ansible-kubernetes-cluster
This repository sets up a kubernetes cluster on SWISS TXT CloudStack using ansible playbooks. It creates three new nodes on CloudStack including a new network and asociates a new public nat ip to it. Also it creates a new cluster on these three nodes with one being the master node and the other two being two worker nodes. It installs the basic usage tools for kubernetes including flannel as CNI, containerd as CRI and Nginx as ingress controller. The playbook also includes an optional example deployment to ensure, that pods services and ingress are all working correctly. To run this playbook, there are several things to be ready before using it:

# Prerequisites
If you want to use this Repository, there are several prerequisites to meet before you can start using it:

## Clone the Repository
First and most important step is to clone the repository to your host, where you want to run the ansible plabook:
1. Create a project folder for this playbook.
2. Clone the repository using git clone into the newly created directory:
```ruby
git clone https://github.com/swisstxt/ansible-kubernetes-cluster.git
```
3. Create a secrets.yml file in the hosts/group_vars/all directory and paste the following variables into it and change the values to your API-Key and -Secret.
```ruby
---
api_key: <YOUR_API_KEY>
api_secret: <YOUR_API_SECRET>
api_host: https://ma-cloud.swisstxt.ch/client/api
```
4. Change the version variables to your needs in the hosts/group_vars/all/versions.yml file

## Python, .venv and Ansible Installation
1. Install Python3 including pip and .venv
```ruby
sudo yum install python3-pip
```
2. Create a new virtual environment one directory above the repository folder
```ruby
python3 -m venv .venv
```
3. Start the virtual environment every time you use the ansible playbook
```ruby
source .venv/bin/activate
```
4. Before installing ansible and the other requirements update pip
```ruby
python -m pip install --upgrade pip
```
5. Install the requirement.txt into the virtual environment
```ruby
pip install -r requirements.txt
```

## SSH-Keys
If you don't have a ssh-key for the currently operating user, create one using the following command and leave all configuration to default:
```ruby
ssh-keygen
```

## Local Kubernetes Installation (Source: galaxy.ansible.com, kubernetes.io)

To remotly connect to the Kubernetes Cluster you need to install kubectl locally on your machine:

```ruby
sudo cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
EOF
```
```ruby
sudo yum install -y kubectl
```

# Use this Repository
To run the whole playbook execute the following command:
```ruby
ansible-playbook playbooks/site.yml
```

To only run one role the following tags are being provided:  
To run cloud_setup, use: cloud  
To run create_nodes, use: nodes  
To run common_nodes_config, use: common  
To run master_node, use: master  
To run worker_node, use: worker  
To run ingress_controller, use: ingress

You can use the defined tags with the follwowing command:
```ruby
ansible-playbook playbook/site.yml --tags="tag1,tag2"
```

## Custom Configurations
To suit the playbook to your needs, you can set some variables found in the hosts/group_vars/all/vars.yml to custom values. The variables, which you want to change are highlighted below:

vars.yml File:
```ruby
# Network name on CloudStack
network: "vnet-kubcluster"
# Zone name in which the network and nodes are created    
zone: "ZUERICH_IX"
# Template for the Nodes            
template: "AlmaLinux 9"
# Service offering on CloudStack       
offering: "2cpu_4gb"
# SRG and SWISS TXT public ip ranges          
cidrs: "['146.159.0.0/16','185.27.109.19/32','147.161.128.0/17']"
# Variable for path of this File                     
vars_file_path: "{{ playbook_dir }}/../hosts/group_vars/all/vars.yml"
# Outbound SSH & kubeapi ports
master_port: 2222             
worker1_port: 2223
worker2_port: 2224
kubeapi_port: 6443
# Loadbalancer Rules for kubernetes
loadbalancer_http: "http-loadbalancer-workers"
loadbalancer_https: "https-loadbalancer-workers"
# Algorithm for loadbalancer
loadbalancer_algorithm: "roundrobin"
# Name for kubernetes user
kubernetes_user: "kube"
```

version.yml File:
```ruby
# These are the default values. They can be changed to your needs. Please make sure that all Versions you put in are compatible with each other.
# Version Variable for yum-utils package
v_yum_utils: 4.3.0
# Version Variable for python3-dnf-plugin-versionlock
v_versionlock: 4.3.0
# Version Variable for containerd
v_containerd: 1.6.28
# Version Variable for pip package manager
v_pip: 21.2.3
# Version Variable for pyyaml pip package
v_pyyaml: 6.0.1
# Version Variable for kubernetes pip package
v_kubernetes: 29.0.0
# Version Variable for jsonpatch pip package
v_jsonpatch: 1.33
# Version Variable for the Container Network Interface (CNI) Plugins
v_cni_plugins: 1.2.0
# Version Variable for the yum kubernetes Repository
v_kubernetes_repo: 1.29
# Version Variable for the kubelet, kubeadm and kubectl packages
v_kube_packages: 1.29.1
# Version Variable for flannel version
v_flannel: 0.24.3
```

## Deploy Example Deployment

To Deploy the Example Application, execute the following command:
```ruby
ansible-playbook playbooks/roles/deployments/tasks/example_deployment.yml
```
