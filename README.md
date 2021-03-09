# Hello Jenkins ##  
This is an implementation of task related to CI/CD and automation basics.

The task comprises following parts:  

1. Set up a virtual Linux-based environment with Vagrant.
2. Provision Jenkins CI server, its dependencies and other reqired files to the environment using Ansible
3. Set up Jenkins jobs which fetch application sources ( [jSudoku](https://sourceforge.net/projects/jsudoku/) in this case ) from [GitHub repository](https://github.com/VladislavKutas/jSudoku) and build it by schedule using Ant as build tool. Job should include code testing stage with [CheckStyle](https://checkstyle.sourceforge.io/) and [PMD](https://pmd.github.io/).


### Project structure ###

 ![Project Structure](project_structure.png)


1. VagrantFile is used to discribe VM configuration.  
   **VM Provider:** Oracle VirtualBox  
   **Vagrant Box:** [debian/contrib-buster64](https://app.vagrantup.com/debian/boxes/contrib-buster64) - a Debian 10 image with installed VirtualBox Guest Addition 
2. **Provision** directory contains all files requred to provison Jenkins to the VM.
3. **Ansible** directory contains Ansible-related files, such as inventory file, playbooks and roles.
4. **Passfile** - File with password to ansible vault. **Not included in the repository** 
5. **Roles** directory contains Ansible **Jenkins** role which is used to install and configure Jenkins on VM.
6. **hosts.yaml** - Ansible inventory file.
7. **provision_jenkins.yaml** - Ansible palybook whis is used to install and configure Jenknis unsing "Jenkins" role.


### Ansible 'Jenkins' role ###

The Ansible 'Jenkins' role  comprises the task, handkers and variables which used to install and configure Jenkins on VM and provide all reqired depenencies.  
The **main.yaml** file in **'defaults'** (8) directory set up role's variables which can be overridden in the playbook. The varibles are: 

`jenkins_home:/var/lib/jenkins`  
The Jenkins home directory. This variable allows you to override the default Jenkins home location - `/var/lib/jenkins`.

`jenkins_admin_username: admin`  
`jenkins_admin_password: admin`  
Default admin user credentials. You can log in as this user just after 'Jenkins' role will finish successfully.

`libraries_path: "libraries/"`
Path to `libraries` directory relative to **flies** directory. In this project the `libraries` directory contains files related to CheckStyle and PMD, but it is possible to add yours.

        jenkins_dependencies: 
         - openjdk-11-jdk
         - python-apt
         - git
         - ant
List of dependencies required to install Jenkins and build java application with Ant.

        jenkins_plugins: 
         - ant
         - credentials-binding
         - git
         - github
         - warnings-ng  
List of reqired plugins to clone repository from GitHub using SSH with private key, run ChekStyle and PMD tests, build sources with Ant and provide report with results of ChekStyle and PMD.

The encrypted file `github_creds.yaml` contains dictionary with GitHub SSH credentials. The format of the file is the following:  

        github_ssh_credentials:
        -
            id: 377e098e-d19d-4f7c-9a6e-16d679dede84
            username: your username 
            passphrase: your passphrase
            private_key: |
                -----BEGIN OPENSSH PRIVATE KEY-----
                ### Your ssh private key goes here ###
                -----END OPENSSH PRIVATE KEY-----
            description: your key description
This file in the repository is intentionally changed, and it is here only for demonstration purpose. 

### Usage ###
Just after Ansible 'jenkins' role successfully completed its work, Jenkins will be avalible on `http://192.168.1.101:8080` from the host and you can log in with credentials you set in the playbook. 

### Known Issues ###
Sometimes during [Jenkins Plugin installation](provision//ansible/roles/jenkins/tasks/install_jenkins_plugins.yaml) for some reason plugins are not installed but despite this ansible returns `state: present` for each plugin. Running Playbook again can help most of the time. This issue requires more research to figure out the reason.