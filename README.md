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

# Use this Repository
To run the whole playbook execute the following command:
```ruby
ansible-playbook playbooks/site.yml
```
Tags are coming soon...
You can use the defined tags with the follwowing command:
```ruby
ansible-playbook playbook/site.yml --tags="tag1,tag2"
```

## Custom Configurations
To suit the playbook to your needs, you can set some variables found in the hosts/group_vars/all/vars.yml to custom values. The variables, which you want to change are highlighted below:

Coming soon...

## Deploy Example Deployment
Coming soon...
