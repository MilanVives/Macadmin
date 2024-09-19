https://rhuaridh.co.uk/blog/ansible-create-user.html

Scenario

We will look at a basic example of using Ansible. We will install ansible on MacOS, create the server inventory, and use a playbook to create a new SSH user on our server.

Prerequisites

Before we get started, we will need:

A local MacOS machine
A server running ubuntu
Instructions

Install Ansible on MacOS

During this example we will be using our local MacOS machine as our Control Node

First we need to install Ansible on MacOS:

pip3 install --user ansible

# Take the install location, and replace the path in this command:
echo 'export PATH="/Users/rhu/Library/Python/3.9/bin:$PATH"' >> ~/.bash_profile

source ~/.bash_profile
Create the Inventory

The Inventory is the collection of servers that Ansible will manage.

On our MacOS machine, create the inventory file:

sudo mkdir -p /etc/ansible
sudo touch /etc/ansible/hosts
Now, we need to find our server IP address and SSH user name so that we can create our hosts file.

Here is an example `/etc/ansible/hosts` file:

[demoservers]
123.456.789.101 ansible_user=ubuntu
Confirm Ansible can access our server

Now to confirm this works:

# If you are using default setup, run:
ansible all -m ping

# Example if you are using a custom key and inventory file, run:
# ansible all -m ping --private-key=~/.ssh/MyKey.pem -i /etc/ansible/hosts
Example output:

123.456.789.101 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
If you see this message then Ansible now has the access needed to start configuring the server.

Create playbook for setting up SSH users

Create create_users.yaml

---
- name: Ansible create user demo
    hosts: demoservers
    remote_user: ubuntu
    tasks:
    - name: Add the user 'demo1'
        ansible.builtin.user:
        name: demo1
    - name: Set authorized keys
        authorized_key:
        user: demo1
        state: present
        key: "ssh-ed25519 INSERT_SOME_SSH_USER_HERE demo@example.com"    
Our playbook makes use of built in Ansible features for creating and configuring SSH users on the machine.

This playbook will create a user called demo1, and will then assign that user our public SSH key so that we have access to the server.

Run Playbook

The config is in our create_users.yaml, and can be run by:

ansible-playbook create_users.yaml --become
The `--become` is important as this allows us to use sudo

Summary

And that's it! Ansible can now run playbooks against our server. This is a very brief and limited example, but will hopefully server as a good starting point for using Ansible to configure your servers.

