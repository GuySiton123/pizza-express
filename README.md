<h1 align="center">Guide: Building testing and deploying "Pizza Express" on KIND Cluster with Ansible</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Node.js-v8.4-green">
  <img src="https://img.shields.io/badge/kind-v0.17.0-red">
  <img src="https://img.shields.io/badge/Ansible-v2.9.27-blue">
  <img src="https://img.shields.io/badge/CentOS-7-yellow">
  <img src="https://img.shields.io/badge/Redis-v6.2.7-red">
  <img src="https://img.shields.io/badge/Docker-v1.13.1-blue">
</p>

<p align="center">First of all, it was really fun to perform this task and I learned a couple of new things. Here are a couple of notes I want you to know before you examine my task:</p>

<ul>
  <li>As a result of working on a local VM, with no ability to create LoadBalancer automatically, I used service from the type NodePort (with your approval of course :) ), without using ingress-controller and ingresses in general. This action forced me to change the "external port" of the application from 8081 to 32081.</li>
  <li>Because KIND is running inside a container locally on the VM, we’ll test the node.js application webpage with the curl command. First, check what is the internal IP of the remote node in your KIND cluster (`kubectl get node -o wide`), and then run `curl http://node ip:32081` on the Linux vm which you check the task with.</li>
  <li>To enable the running of the playbook from any VM, I used only realtive paths in the playbook, please make sure you run the playbook from the folder specified in the instructions.</li>
</ul>

## Prerequisites

- VM with Linux CentOS 7 (or any other Linux distribution) installed
- VM have internet access and a user with privileged permissions to run the playbook with
- OS firewall turned off on the VM

## Set up the environment and run the playbook
### Step 1 - Install Ansible on the VM:
1. Install Ansible on the master node.
	- Follow this guide to install Ansible on CentOS 7: https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-centos-7
	- If using a different Linux distribution, follow other guides to install Ansible.
2. Verify that Ansible is installed correctly by running the command on the master node after the installation process:
```
ansible --version
```
### Step 2 - Run the playbook:
1. Clone this GitHub repo to your VM, and change the working directory to pizza-express-playbook (it is mandatory to run the playbook from this directory!).
2. Run the playbook with the following command:
```
ansible-playbook pizza-express.yaml
```

## Workflow Overview
<p align="center">This is an overview of the playbook structure.</p>

### Ansible
- This ansible playbook has four roles:
	1. install_kind_and_dependencies: Runs on the localhost and installs KIND, Docker, kubectl, and helm only if they are not already installed.
	2. create_kind_cluster: Runs on the localhost and creates the KIND cluster on the node if it is not exists using the kind_cluster_name variable, build the pizza-express image with a test tag and load the image to the kind cluster.
	3. install_app_and_test: Runs on the localhost, install the helm chart on the kind cluster, check if the newly created pod is created successfully, run unit tests and http test and if the tests are passing, tag the image as latest
