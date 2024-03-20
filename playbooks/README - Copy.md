"# Ansible-config" 

Ansible-Automate Project
Ansible Configuration Management - Automate Project7 to 10
You have been implementing some interesting projects up until now, and that is awesome.
In Projects 7 to 10 you had to perform a lot of manual operations to set up virtual servers, install and configure requiredsoftware and deploy your web application.
This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with Ansible Configuration Management, at the same time you will become confi dent with writing code using declarative languages such as YAML.

Let us get started!

Instructions On How To Submit Your Work For Review And Feedback
To submit your work for review and feedback - follow
this instruction
.
Ansible Client as a Jump Server (Bastion Host)
A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would beinside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server - it provides better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets- Public subnet has public IPaddresses and Private subnet is only reachable by private IP addresses.


![Architecture](./Image/Architecture.png)

Tasks
Install and configure Ansible client to act as a Jump Server/Bastion Host
Create a simple Ansible playbook to automate servers configuration

1.  Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

2.  In your GitHub account create a new repository and name it ansible-config-mgt.
Install and configure ansible on ec2 instance

3. - Install and Configure Ansible on EC2 Instance
1.Update the Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

```markdown
sudo apt update
sudo apt install ansible
```

Check your Ansible version by running
```markdown
ansible --version
```

![Result](./Image/version.png)

Install jenkins 

Step 1: Update the System Package Repository
Open a terminal and run the following command to ensure your package list is updated:

```sh
sudo apt update
```
Step 2: Install Java
Jenkins requires Java to be installed. You can install the default Java Development Kit with the following command:

```sh
sudo apt install default-jdk -y
```
Run Jenkins Debian Packages
This is the Debian package repository of Jenkins to automate installation and upgrade. To use this repository, first add the key to your system (for the Weekly Release Line):

```markdown
  sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
Then add a Jenkins apt repository entry:

```sh
 echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
```

 ```sh
  sudo apt-get update
  sudo apt-get install fontconfig openjdk-17-jre
  sudo apt-get install jenkins
 ```
To check if Jenkins is installed and running, run the following command:
```sh
sudo systemctl status jenkins
```
If the Jenkins service is not running or active, run the following command to start it:
```sh
sudo systemctl enable --now jenkins
```

![Results](./Image/Jenkins.png)

4.  Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.

Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

Configure Webhook in GitHub and set webhook to trigger ansible build.

Configure a Post-build job to save all (**) files, like you did it in Project 9.

Test your setup by making some change in  file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

```markdown
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
```

Note: Trigger Jenkins project execution only for /main (master) branch.

Now your setup will look like this:

![Architecture](./Image/Architecture_1.png)

Step 2 – Prepare your development environment using Visual Studio Code
First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC), you can get it here.

After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

```bash
git clone <ansible-config-mgt repo link>
```
![GITCLONE](./Image/version.png)

Step 3 - BEGIN ANSIBLE DEVELOPMENT**


1. In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

**Tip: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool – include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)**


2. Checkout the newly created feature branch to your local machine and start building your code and directory structure

3. Create a directory and name it `playbooks` – it will be used to store all your playbook files.

4. Create a directory and name it `inventory` – it will be used to keep your hosts organised.


5. Within the playbooks folder, create your first playbook, and name it common.yml

6. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) `dev`, `staging`, `uat`, and `prod` respectively.


## **Step 4 – Set up an Ansible Inventory**


An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.


Save below inventory structure in the `inventory/dev` file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.


Note: Ansible uses TCP port 22 by default, which means it needs to `ssh` into target servers from `Jenkins-Ansible` host – for this you can implement the concept of ssh-agent. Now you need to import your key into `ssh-agent`:


```bash
eval `ssh-agent -s`

ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below, you should see the name of your key

```bash
ssh-add -l
```
Now, ssh into your `Jenkins-Ansible` server using ssh-agent

```bash
ssh -A ubuntu@public-ip
```
Also note, that your Load Balancer user is `ubuntu` and user for RHEL-based servers is `ec2-user`.

Update your `inventory/dev.yml` file with this snippet of code:

```bash
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```


## CREATE A COMMON PLAYBOOK


## **Step 5 – Create a Common Playbook**


It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in `inventory/dev`.

In `common.yml` playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your `playbooks/common.yml` file with following code:

```bash
---
- name: update web, nfs and db servers
  hosts: webservers, nfs
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb, db
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install **wireshark** utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses `root` user to perform this task and respective package manager: `yum` for RHEL 8 and `apt` for Ubuntu.


## **Step 6 – Update GIT with the latest code**

Push all the changes made to the directories and files from local machine to Github.

Commit your code into GitHub:

1. use git commands to add, commit and push your branch to GitHub.
```bash
git status

git add <selected files>

git commit -m "commit message"
```
2. Create a Pull request (PR)

3. Wear a hat of another developer for a second, and act as a reviewer.

4. If the reviewer is happy with your new feature development, merge the code to the master branch.

5. Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once the code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server as shown below.

![checking ansible build](./Image/Ansible_build.png)

## RUN FIRST ANSIBLE TEST


## **Step 7 – Run first Ansible test**


Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

Connect to your jenkins-ansible server via VScode (configure the .ssh/config file with your jenkins server information)

```bash
ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/playbooks/common.yml
```

![Run first ansible test](./Image/Ansible_playbook.png)


At the end of this project we have implemented a solution that is shown below

![Final Architecture](./Image/final_architecture.PNG)