To automate the process of generating an SSH key pair on a new instance/server, installing Ansible, and then copying the public key to target servers, you can follow these steps. This process involves creating an Ansible playbook that performs these tasks.

### Step 1: Create an Ansible Playbook

Create a file named `setup_ansible.yml` with the following content:

```yaml
---
- hosts: all
 become: yes
 tasks:
    - name: Update package lists
      apt:
        update_cache: yes

    - name: Install Ansible
      apt:
        name: ansible
        state: present

    - name: Generate SSH key pair
      openssh_keypair:
        path: /home/ubuntu/.ssh/id_rsa
        owner: ubuntu
        group: ubuntu
        mode: '0600'
      register: ssh_key

    - name: Show generated public key
      debug:
        var: ssh_key.public_key

    - name: Copy public key to target servers
      authorized_key:
        user: ubuntu
        state: present
        key: "{{ ssh_key.public_key }}"
```

This playbook does the following:

- Updates the package lists on the target servers.
- Installs Ansible.
- Generates an SSH key pair for the `ubuntu` user.
- Displays the generated public key.
- Copies the public key to the `authorized_keys` file of the `ubuntu` user on the target servers.

### Step 2: Create an Inventory File

Create an inventory file named `inventory.ini` with the IP addresses or hostnames of your target servers:

```
[target_servers]
172.31.37.245
```

### Step 3: Run the Ansible Playbook

Run the playbook using the `ansible-playbook` command:

```bash
ansible-playbook -i inventory.ini setup_ansible.yml
```

This command tells Ansible to run the `setup_ansible.yml` playbook against the hosts listed in the `inventory.ini` file.

### Additional Notes

- Ensure that the target servers are accessible via SSH and that you have the necessary permissions to install packages and manage SSH keys.
- The `openssh_keypair` module is used to generate the SSH key pair, and the `authorized_key` module is used to add the public key to the `authorized_keys` file on the target servers.
- This playbook assumes that the target servers are running Ubuntu and that the `ubuntu` user has sudo privileges. Adjust the user and paths as necessary for your environment.

By following these steps, you can automate the setup of Ansible and SSH key management on new instances or servers using Ansible [6][10].

Citations:
[1] https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html
[2] https://stackoverflow.com/questions/25629933/ansible-copy-ssh-key-from-one-host-to-another
[3] https://www.cherryservers.com/blog/how-to-set-up-ansible-inventory-file
[4] https://www.cyberciti.biz/faq/how-to-upload-ssh-public-key-to-as-authorized_key-using-ansible/
[5] https://www.codesandnotes.be/2020/01/13/generate-ssh-keys-using-ansible/
[6] https://mahira-technology.medium.com/how-to-copy-ssh-key-to-remote-host-using-ansible-9b0fd00f3786
[7] https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-automate-initial-server-setup-on-ubuntu-22-04
[8] https://www.middlewareinventory.com/blog/ansible-ssh-key-exchange/
[9] https://www.reddit.com/r/ansible/comments/17kn1c5/ansible_inventory_file_question/
[10] https://serverfault.com/questions/910071/how-to-generate-host-ssh-keys-via-ansible

To generate an Ansible playbook that installs Nginx, you can follow the example provided in the sources. This playbook will target hosts defined in an inventory file under a group named `web`. The playbook will install Nginx and ensure the service is started. Here's a step-by-step guide based on the information from the sources:

### Step 1: Create an Inventory File

First, you need to create an inventory file named `inventory.cfg` that lists the hosts where you want to install Nginx. For example:

```
[web]
server1 ansible_host=192.168.1.10
server2 ansible_host=192.168.1.11
```

This file defines a group named `web` with two servers.

### Step 2: Create the Ansible Playbook

Next, create a playbook named `install_nginx.yaml` with the following content:

```yaml
---
- hosts: web
 become: yes
 tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: latest
        update_cache: yes

    - name: Ensure Nginx is running
      service:
        name: nginx
        state: started
```

This playbook does the following:

- Targets the `web` group defined in your inventory file.
- Uses the `apt` module to install Nginx, ensuring the package is up to date.
- Ensures the Nginx service is running.

### Step 3: Run the Ansible Playbook

Finally, run the playbook using the `ansible-playbook` command. Make sure you have SSH access to the hosts defined in your inventory file and that Ansible is installed on your control node.

```bash
ansible-playbook -i inventory.cfg install_nginx.yaml
```

This command tells Ansible to run the `install_nginx.yaml` playbook against the hosts listed in the `inventory.cfg` file. The `-i` option specifies the inventory file, and the playbook file name follows it.

### Additional Notes

- The `become: yes` directive in the playbook ensures that Ansible runs the tasks with elevated privileges, which is necessary for installing packages and managing services.
- Ensure that the hosts in your inventory file are accessible via SSH and that you have the necessary permissions to install packages and manage services.

By following these steps, you can automate the installation and configuration of Nginx on your servers using Ansible [1][3].

Citations:
[1] https://blog.devgenius.io/how-to-install-nginx-using-ansible-playbook-48e2aa74f944
[2] https://code-maven.com/install-and-configure-nginx-using-ansible
[3] https://dev.to/dpuig/creating-an-ansible-playbook-to-install-and-configure-nginx-for-hosting-static-websites-3n6j
[4] https://blog.prasadsuman.me/configuring-nginx-using-ansible-playbook
[5] https://tech.sadaalomma.com/ubuntu/install-nginx-using-ansible-playbook-ubuntu/
[6] https://charlesreid1.com/wiki/Ansible/Nginx_Playbook
[7] https://www.nginx.com/blog/installing-nginx-nginx-plus-ansible/
[8] https://www.linkedin.com/pulse/ansible-playbook-install-configure-nginx-daniel-puig-gerarde
[9] https://github.com/nickjj/ansible-nginx
[10] https://blog.devops.dev/ansible-playbooks-for-nginx-and-mongodb-configuration-ca88799020c2
[11] https://blog.devops.dev/hands-on-ansible-project-deploying-a-web-app-on-nginx-servers-f231cedba8d0
[12] https://medium.com/@linuxlearninghub/ansible-playbook-to-install-docker-and-run-nginx-image-as-a-container-ad47c37cd6e5
[13] https://www.exoscale.com/syslog/nginx-ansible/
[14] https://docs.nginx.com/nginx-management-suite/installation/infrastructure-as-code/configuration/
[15] https://github.com/nephelaiio/ansible-playbooks-nginx
