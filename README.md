# EXPERIENCE-CONTINUOUS-INTEGRATION-WITH-JENKINS-ANSIBLE-ARTIFACTORY-SONARQUBE-PHP

### Project Overview:
This project is part of your DevOps work and builds upon your previous knowledge of **Ansible**, a tool used for automating server management. In this project, you'll be dealing with multiple environments for different stages of the software development lifecycle. These environments will simulate various real-world processes, such as testing, deployment, and security checks.

The main focus initially is to set up and configure servers for these environments:

- **CI (Continuous Integration)**
- **Dev (Development)**
- **Pentest (Penetration Testing)**

### Key Environments:
1. **CI (Continuous Integration)**:  
   In this environment, your code gets automatically tested every time a change is made. It ensures that any new code doesn't break existing functionality. Typically, there will be an automated pipeline here that runs tests, builds the project, and checks that everything works.

2. **Dev (Development)**:  
   This is where developers actively work on new features or fix bugs. The Dev environment is often used by developers to test their code before it goes to the next stage (like CI or integration testing). You'll need a setup where developers can deploy their changes and verify them.

3. **Pentest (Penetration Testing)**:  
   This environment is used for security testing. Penetration testing involves simulating cyber-attacks to find vulnerabilities in the system before hackers can exploit them. The Pentest environment may also be used for performance and load testing, depending on how the organization decides to handle these tests. To perform effective penetration testing, you’ll need specific security tools and configurations on these servers.

4. **SIT (System Integration Testing)**:  
   This is a type of testing where different components of the system are tested together to ensure that they work as expected when integrated. It's mostly about ensuring that the system components work well together.

5. **UAT (User Acceptance Testing)**:  
   UAT is done by the users or clients to ensure the software meets their requirements and expectations. It’s typically the last step before deploying the product to production.

### Cost Considerations:
Rather than spinning up all the servers at once for every environment (which can be expensive), you'll create servers for the environments you're working on at that moment. For example:
- If you are setting up the **Dev environment**, you don't need to create servers for **Pentest**, **SIT**, or **UAT** immediately. You'll create just enough servers for **Dev** and expand as you move to other environments.

### Project Setup Workflow:
- Start by focusing on the **CI** and **Dev** environments first. Once you've set up and configured these, move to the **Pentest** environment to handle security testing.
- As you progress, you may need additional servers for **SIT** and **UAT**, but they don't require much special setup since they're mostly about hosting the web application.

The focus of this project is to use **Nginx** as a **reverse proxy** to manage and route traffic for different environments and tools within your DevOps infrastructure. Each environment (such as **CI**, **Dev**, **Pentest**, etc.) and its tools (such as **Jenkins**, **SonarQube**, **Artifactory**) will be accessible through **subdomains**. 

In addition, **Ansible** will be used to automate the setup and management of these environments.

### Reverse Proxy Setup with Nginx:
In this project, **Nginx** will act as a **reverse proxy**. This means that Nginx will handle incoming web requests and route them to the appropriate internal server based on the **subdomain** used in the request. 

![image](https://github.com/user-attachments/assets/08a6f45a-67bf-434e-8eba-74a9c465a013)

### CI Environment
![image](https://github.com/user-attachments/assets/a58ed9d1-4699-4599-ad79-6a30906e668e)

### Other Environment from lower to higher
![image](https://github.com/user-attachments/assets/5edfbaa8-92ba-4a40-b188-e528cb854095)


For example:
- When a user visits `https://ci.devops.com`, Nginx will route the request to the **CI server**.
- When a user visits `https://todo.devops.com`, Nginx will route the request to the **Todo Web Application** server.

The goal is to ensure that **Nginx** is the entry point for all requests, and it intelligently directs traffic to the correct server or application based on the **subdomain**.

### DNS Requirements:
For example, To set up the DNS records for each environment and tool when we have **devops.com** as the main domain. You'll need to create subdomains for each environment and tool that your project uses. These subdomains will be mapped to specific internal servers and tools.

Here’s the revised DNS setup for different environments and tools using **devops.com**:

| Service            | Subdomain                                    |
|--------------------|----------------------------------------------|
| Jenkins            | `https://ci.devops.com`                     |
| SonarQube          | `https://sonar.devops.com`                   |
| Artifactory        | `https://artifacts.devops.com`               |
| Production Tooling | `https://tooling.devops.com`                 |
| Pre-Prod Tooling   | `https://tooling.preprod.devops.com`         |
| Pentest Tooling    | `https://tooling.pentest.devops.com`         |
| UAT Tooling        | `https://tooling.uat.devops.com`             |
| SIT Tooling        | `https://tooling.sit.devops.com`             |
| Dev Tooling        | `https://tooling.dev.devops.com`             |
| Production WebApp  | `https://todo.devops.com`                   |
| Pre-Prod WebApp    | `https://todo.preprod.devops.com`           |
| Pentest WebApp     | `https://todo.pentest.devops.com`            |
| UAT WebApp         | `https://todo.uat.devops.com`               |
| SIT WebApp         | `https://todo.sit.devops.com`               |
| Dev WebApp         | `https://todo.dev.devops.com`               |

These DNS entries ensure that each environment (CI, Dev, Pre-Prod, etc.) and each tool (Jenkins, SonarQube, Artifactory) has its own subdomain, making it easy to access these services through clear and manageable URLs.

### Ansible Inventory:
In **Ansible**, the **inventory** file is where you define all your servers and group them logically. Ansible will use these groups to apply configuration changes, updates, and run playbooks on the appropriate servers.

For each environment, you’ll have a separate inventory file that defines which servers belong to each environment and service. Here's what the inventory files might look like for each environment:

#### 1. **CI Inventory File**:
This file contains the servers for **Jenkins**, **SonarQube**, **Artifactory**, and **Nginx** in the **CI** environment.

```ini
[jenkins]
<Jenkins-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[sonarqube]
<SonarQube-Private-IP-Address>

[artifact_repository]
<Artifact-Repository-Private-IP-Address>
```

#### 2. **Dev Inventory File**:
This file contains servers for **Tooling**, **Todo Web App**, **DB**, and **Nginx** in the **Dev** environment.

```ini
[tooling]
<Tooling-Web-Server-Private-IP-Address>

[todo]
<Todo-Web-Server-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<DB-Server-Private-IP-Address>
```

#### 3. **Pentest Inventory File**:
The **pentest** inventory introduces the concept of **grouping** related servers under a parent group. This way, you can manage multiple servers at once, while also having the flexibility to run tasks on individual servers if needed.

```ini
[pentest:children]
pentest-todo
pentest-tooling

[pentest-todo]
<Pentest-for-Todo-Private-IP-Address>

[pentest-tooling]
<Pentest-for-Tooling-Private-IP-Address>
```

### Grouping with **pentest:children**:
- The line `[pentest:children]` groups the two subgroups: **pentest-todo** and **pentest-tooling**. This allows you to run Ansible tasks across both subgroups simultaneously by targeting the parent group, **pentest**.
- This grouping ensures that you can easily manage both **pentest-todo** and **pentest-tooling** together while still having the option to run tasks on each subgroup individually.

### Group Variables (`group_vars`):
- **group_vars** are used to define variables that apply to a particular group of servers. This helps to avoid repetition and ensures that all servers in a group share the same settings.
- For example, if **pentest-todo** and **pentest-tooling** need some common configuration (such as a security tool or user settings), you can define these settings in a **group_vars** file.

Here’s an example of a **group_vars** file for the **pentest** group:

```yaml
# group_vars/pentest.yml
common_security_tool: "Nmap"
security_scan_schedule: "daily"
```

This configuration ensures that both **pentest-todo** and **pentest-tooling** inherit these variables, making it easier to maintain consistent configurations across servers.

### Important Observations:
1. **Different OS Users and Python Configurations**:
   - Different operating systems (e.g., **Ubuntu** vs. **RedHat/CentOS**) may require different **ansible_user** settings or paths for the **python interpreter**.
   - If you have a mix of OS types (Ubuntu, CentOS), you may need to customize the `ansible_user` and `ansible_python_interpreter` settings for each group in the **inventory** file. However, if all your servers are running **Ubuntu**, this may not be necessary.

2. **Flexibility with Groups**:
   - Using **group_vars** and **pentest:children**, Ansible provides flexibility to apply configurations and run tasks on multiple servers at once, or target specific servers for individual tasks.
   - Grouping allows you to manage servers in a more organized way, especially when you have multiple environments or different types of services.


### Key Takeaways:
- **Continuous Integration (CI)** and **Development (Dev)** environments are set up first, followed by the **Pentest** environment for security testing.
- Environments like **SIT** and **UAT** are simpler and don’t require complex configurations.
- Set up servers as you progress through the stages of the project to save on cloud costs.


## **Ansible Roles for CI Environment**:
In a DevOps pipeline, **Ansible roles** are essential because they allow us to define tasks and configurations for each service in a modular, reusable way. In this case, we need to add roles for **SonarQube** and **Artifactory** to our CI environment to ensure proper setup and configuration.

Let's start by understanding **SonarQube** and **Artifactory**, then we can look at how to create the Ansible roles for these services.

---

### **1. SonarQube Role**:
**SonarQube** is an open-source tool for continuous inspection of code quality. It helps developers find bugs, security vulnerabilities, and other issues in their code through static analysis. It supports multiple languages and integrates with other tools in your CI/CD pipeline, such as **Jenkins**. By running SonarQube, you get valuable feedback that helps keep your codebase clean, secure, and maintainable.

#### **Why do we need SonarQube?**
SonarQube helps in identifying problems in the code early on, before they make their way to production. Specifically, it:
- Performs **automatic code reviews** through static analysis.
- Detects **bugs**, **code smells**, and **security vulnerabilities** in the code.
- Helps maintain a **high code quality standard** through continuous inspection.

For example:
- **Bugs**: Problems in the code that could lead to failures or crashes.
- **Code smells**: Code that may work but is inefficient or difficult to maintain.
- **Security vulnerabilities**: Potential issues that could compromise the security of the application.

#### **Setting Up SonarQube Manually**:
To set up **SonarQube** manually, you would generally need to:
1. Install the **SonarQube server** on the designated machine.
2. Configure the **SonarQube scanner** on the CI server (e.g., Jenkins) to analyze your code.
3. Set up projects in SonarQube for different repositories, and configure quality gates (rules for acceptable code quality).
4. Integrate SonarQube with your CI/CD pipeline (using plugins like the SonarQube Jenkins plugin).

##### **Ansible Role for SonarQube**:
The **Ansible role** for SonarQube will automate this setup. It will install the SonarQube server, configure the necessary files, and ensure that it’s ready to work with your code repositories. Here's an outline of what the role will do:

- **Install SonarQube**: Download and install SonarQube and its dependencies.
- **Start the SonarQube server**: Ensure that SonarQube is up and running, accessible via the web interface.
- **Configure SonarQube**: Set up essential configurations such as database settings, ports, and authentication.
- **Integrate with Jenkins**: Ensure that SonarQube is integrated into Jenkins by configuring the SonarQube plugin in Jenkins.

#### Example of SonarQube Role Structure:
```
roles/
  sonarqube/
    tasks/
      main.yml
    files/
      sonar.conf
    templates/
      sonar.properties.j2
    handlers/
      main.yml
```

In this structure:
- **tasks/main.yml** would define the installation and setup tasks.
- **files/sonar.conf** contains configuration files for SonarQube.
- **templates/sonar.properties.j2** is a Jinja2 template for dynamically generating configuration files.
- **handlers/main.yml** would define tasks like restarting SonarQube after changes.

---

### **2. Artifactory Role**:
**Artifactory** is a product by **JFrog** that acts as a **binary repository manager**. After you build your code (for example, using **Jenkins**), you typically get **artifacts** (e.g., compiled code, libraries, dependencies). Artifactory helps manage and store these artifacts for future use. It ensures that every build artifact is safely stored, versioned, and easily retrievable.

#### **Why do we need Artifactory?**
Artifactory plays a key role in the **DevOps pipeline** because it helps in managing the **artifacts** generated during the build process. It provides several key benefits:
- **Versioning and storage** of build artifacts.
- **Centralized access** to artifacts across different teams.
- **Secure management** of artifacts, including controlling access to certain versions of the artifacts.
- **Integration** with other tools, such as **Jenkins**, to ensure artifacts are automatically pushed after every build.

Artifactory is essential for ensuring that you can reliably use the exact same version of a dependency across different environments (e.g., from dev to production).

#### **Setting Up Artifactory Manually**:
To set up **Artifactory** manually, you would typically:
1. Install **Artifactory** on a server.
2. Configure it to store **Maven**, **Docker**, **npm**, or other types of artifacts.
3. Set up **repositories** in Artifactory to organize and manage different artifact types.
4. Integrate Artifactory with your **CI/CD pipeline** (such as Jenkins) so that build artifacts are pushed and pulled automatically.

##### **Ansible Role for Artifactory**:
The **Ansible role** for Artifactory automates the entire installation and configuration process. It ensures that Artifactory is properly installed, configured, and ready to store artifacts from your build processes.

- **Install Artifactory**: Download and install the necessary software (e.g., through Docker or direct installation).
- **Configure Repositories**: Set up repositories to organize and store different types of artifacts.
- **Integrate with Jenkins**: Set up Artifactory integration within Jenkins to push and pull build artifacts automatically.

#### Example of Artifactory Role Structure:
```
roles/
  artifactory/
    tasks/
      main.yml
    files/
      artifactory.conf
    templates/
      artifactory.config.j2
    handlers/
      main.yml
```

In this structure:
- **tasks/main.yml** defines the installation and setup tasks for Artifactory.
- **files/artifactory.conf** contains Artifactory configuration files.
- **templates/artifactory.config.j2** is a Jinja2 template to dynamically generate configuration files.
- **handlers/main.yml** defines handlers like restarting the Artifactory service after configuration changes.

---

## Configuring Ansible For Jenkins Deployment

In previous projects, you have been launching Ansible commands manually from a CLI. Now, with Jenkins, we will start running Ansible
from Jenkins UI.

**To do this**
1. Set up SSH-agent

```
eval `ssh-agent -s`
ssh-add <Path to Private keykey.pem>
ssh-add -l 
ssh -A ubuntu@<Private IP address of Jenkins Server>
```

2. Navigate to Jenkins URL
```
<Jenkins-server-public-IP>:8080
```

3. Install & Open Blue Ocean Jenkins Plugin
In the Jenkins dashboard, click on **Manage Jenkins** -> **Manage plugins** and search for `Blue Ocean plugin` from Available Plugins. Install and open Blue Ocean plugin

![image](https://github.com/user-attachments/assets/261a864f-63c8-4cb3-a54b-769c292d06aa)

![image](https://github.com/user-attachments/assets/cd42e3a7-1412-4aa2-b0fe-b5293d099b75)

![image](https://github.com/user-attachments/assets/e7a6df40-cc26-406c-a2be-7fe38344948f)

![image](https://github.com/user-attachments/assets/f633d785-7b83-4e14-a5a3-85d6e7e98e1b)


5. Create a new pipeline and select Github

![image](https://github.com/user-attachments/assets/55d53590-49b2-4392-bf37-e63166c108e0)


6. Connect Jenkins with GitHub

7. Login to GitHub & Generate an Access Token

![image](https://github.com/user-attachments/assets/a9fd0011-51e0-4131-a4ec-3e1d567bb90e)

![image](https://github.com/user-attachments/assets/cebf92d1-99e1-4b4c-b3d0-2967b8c0fbfc)

![image](https://github.com/user-attachments/assets/8f473e0b-6889-45f9-9c29-7baf6c4b611a)

![image](https://github.com/user-attachments/assets/85f452d0-d994-4b0e-bce8-df34dcf2155b)

![image](https://github.com/user-attachments/assets/af23ce43-ece7-48ac-a519-0bafcf5ad434)

![image](https://github.com/user-attachments/assets/4d661f08-bf6d-4b9c-b20b-c6bebbb2f07e)

![image](https://github.com/user-attachments/assets/ba8891db-0feb-4e40-bd49-b7064d002c45)


8. Copy Access Token

![image](https://github.com/user-attachments/assets/57f2d0fe-ae38-40e2-80b4-af63fc0a65a9)

9. Paste the token and connect

10. Create a new Pipeline

![image](https://github.com/user-attachments/assets/7c31c7a4-9d6c-4cde-9be7-9078916ae1d7)


At this point you may not have a Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console.

![image](https://github.com/user-attachments/assets/a5c38554-e5f3-4380-8ae4-f1dc438f9e6b)


### Here is the newly created pipeline. It takes the name of your GitHub repository

![image](https://github.com/user-attachments/assets/4b4964e8-1d59-4045-936c-f92b52816e4a)


## Let us create the Jenkinsfile
In Vscode, inside the Ansible project, create a new directory and name it deploy, create a new file Jenkinsfile inside the directory. Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage.

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```

![image](https://github.com/user-attachments/assets/c57cacdc-43cb-4abb-bb13-2b067436e734)

Now go back into the Ansible pipeline in Jenkins, and select configure

![image](https://github.com/user-attachments/assets/be9a96ff-f75a-4850-8e57-3beabf4da60b)


Scroll down to Build Configuration section and specify the location of the Jenkinsfile at `deploy/Jenkinsfile`

![image](https://github.com/user-attachments/assets/e38e4081-54c6-4bd4-86fd-365b5b7a048b)

Back to the pipeline again, this time click `Build now`.

![image](https://github.com/user-attachments/assets/b2e2508f-7984-4971-b658-7b32aa01a68b)

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

![image](https://github.com/user-attachments/assets/7b2041d4-963f-4da7-9898-f749c624504d)

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from Blue Ocean interface. Click on Open Blue Ocean

![image](https://github.com/user-attachments/assets/ffec67b4-53eb-455f-b6e3-bb1332588628)
> Notice that this pipeline is a multibranch one. This means, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.


Let us see this in action.

1. Create a new git branch and name it `feature/jenkinspipeline-stages`

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```

![image](https://github.com/user-attachments/assets/69c124b5-2943-444d-9a28-ef67ccf3c588)

2. To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

3. Click on the "Administration" button

4. Navigate to the Ansible project and click on Scan repository now

5. Refresh the page and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.

![image](https://github.com/user-attachments/assets/edfb6703-7196-478b-a45f-3e8f8fa2ab02)

6. In Blue Ocean, you can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.

![image](https://github.com/user-attachments/assets/36f4e02f-a61c-4721-a0e2-673893c177ed)

## A QUICK TASK FOR YOU!

```
1. Create a pull request to merge the latest code into the main branch
2. After merging the PR, go back into your terminal and switch into the main branch.
3. Pull the latest change.
4. Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build
and test stages)
   * Package
   * Deploy
   * Clean up
5. Verify in the Blue Ocean that all the stages are working, then merge your feature branch to your main branch
6. Eventually, your main branch should have a successful pipeline like this in blue ocean
```

### Your new `Jenkinsfile` should look like this below:
```
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Package') {
            steps {
                echo 'Packaging...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
        stage('Clean up') {
            steps {
                echo 'Cleaning up...'
            }
        }
    }
}

```

### And your main branch should have a successful pipeline like this in blue ocean:
![image](https://github.com/user-attachments/assets/e1e6f5fb-0edf-40db-8ba8-6f37c31e7d93)


## Running Ansible Playbook from Jenkins

Now that you have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:

1. Installing Ansible on Jenkins

```
sudo apt update
sudo apt upgrade -y
```
2. Installing Required Dependencies

```
sudo apt install software-properties-common -y
```

3. Adding the Ansible PPA (Personal Package Archive)

```
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

4. Verify the Installation

```
sudo apt update
sudo apt install ansible -y
ansible --version
```
![image](https://github.com/user-attachments/assets/dff05bbe-86dc-48c1-b42c-085a5d6ef96d)

5. Installing Ansible plugin in Jenkins UI On the dashboard page, Click on Manage Jenkins >>> Manage plugins >>> Available type in ansible and install without restart

![image](https://github.com/user-attachments/assets/a59141fc-a1a5-4146-9633-3b16ad5d0689)

![image](https://github.com/user-attachments/assets/9a25c787-ddea-4989-ae41-1ae6efac3178)
 

#### Now we can add a name and the path ansible is installed on the jenkins server.Firstly get the path in which ansible was installed on your Jenkins server. 

```
 which ansible
```
![image](https://github.com/user-attachments/assets/168a9578-5aad-48c5-a1cd-62626836fe82)


#### Click on Dashboard >>> Manage Jenkins >>> Tool Configuration >>> Add Ansible >>> Add file path. 

![image](https://github.com/user-attachments/assets/64601186-a8fa-4ac1-b454-2d1e792bb9d7)

![image](https://github.com/user-attachments/assets/922bee02-c8e2-4833-b8ea-cc738449e5b6)


6. Creating `Jenkinsfile` from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully)
   * Let's delete the content of current `Jenkinsfile` and create a new `Jenkinsfile` from scratch to run the ansible playbook against the dev environment.
   * To do this let's ensure git module is checking out SCM from main branch.

```
pipeline {
    agent any

    environment {
        ANSIBLE_CONFIG = "${WORKSPACE}/deploy/ansible.cfg"
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {
        stage("Initial cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/Captnfresh/ansible-config-mgt.git'
            }
        }

        stage('Prepare Ansible For Execution') {
            steps {
                sh """
                echo ${WORKSPACE}
                if grep -q '^roles_path=' ${WORKSPACE}/deploy/ansible.cfg; then
                    sed -i "s|^roles_path=.*|roles_path=${WORKSPACE}/roles|" ${WORKSPACE}/deploy/ansible.cfg
                else
                    echo "roles_path=${WORKSPACE}/roles" >> ${WORKSPACE}/deploy/ansible.cfg
                fi
                """
            }
        }


        stage('Test SSH Connections') {
            steps {
                script {
                    def hosts = [
                        [group: 'uat-webservers', ip: '172.31.26.143', user: 'ec2-user'],
                        [group: 'uat-webservers', ip: '172.31.18.71', user: 'ec2-user'],
                        [group: 'lb', ip: '172.31.18.31', user: 'ubuntu'],
                        [group: 'db', ip: '172.31.30.166', user: 'ubuntu']
                    ]
                    for (host in hosts) {
                        sshagent(['ssh-ansible']) {
                            sh "ssh -o StrictHostKeyChecking=no -i /home/ubuntu/.ssh/key.pem ${host.user}@${host.ip} exit"
                        }
                    }
                }
            }
        }

        stage('Run Ansible playbook') {
            steps {
                sshagent(['ssh-ansible']) {
                    ansiblePlaybook(
                        become: true,
                        credentialsId: 'ssh-ansible',
                        disableHostKeyChecking: true,
                        installation: 'ansible',
                        inventory: "${WORKSPACE}/inventory/dev.yml",
                        playbook: "${WORKSPACE}/playbooks/site.yml"
                    )
                }
            }
        }

        stage('Clean Workspace after build') {
            steps {
                cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
            }
        }
    }
}


```

![image](https://github.com/user-attachments/assets/4fe780e3-d875-439a-8d0d-273cf3df54b5)

---
## Let's do a Line by Line explanation of the script above

### Pipeline Declaration

```
pipeline {
    agent any
```

* pipeline {}: This defines the start of the pipeline script.
* agent any: This specifies that the pipeline can run on any available Jenkins agent (node).

### Environment Variables
```
environment {
    ANSIBLE_CONFIG = "${WORKSPACE}/deploy/ansible.cfg"
    ANSIBLE_HOST_KEY_CHECKING = 'False'
}
```

* environment {}: Sets environment variables for the entire pipeline.
* ANSIBLE_CONFIG: Points to the Ansible configuration file (ansible.cfg) located in the deploy directory of the current workspace.
* ANSIBLE_HOST_KEY_CHECKING: Disables strict host key checking, preventing SSH from prompting for key confirmation.


### Stages Declaration

```
stages {
```

* stages {}: A block that contains all the stages of the pipeline.


### Stage: Initial Cleanup

```
stage("Initial cleanup") {
    steps {
        dir("${WORKSPACE}") {
            deleteDir()
        }
    }
}
```

* stage("Initial cleanup"): This defines a stage for cleaning the workspace.
* dir("${WORKSPACE}"): This points to the current workspace directory.
* deleteDir(): This deletes all files and folders within the workspace to ensure a clean slate for the build.


### Stage: Checkout SCM

```
stage('Checkout SCM') {
    steps {
        git branch: 'main', url: 'https://github.com/AyopoB/ansible-config-mgt.git'
    }
}
```

* stage('Checkout SCM'): This pulls the source code from the Git repository.
* git branch: 'main', url: ...: This specifies the branch (main) and the GitHub repository URL to clone into the workspace.


### Stage: Prepare Ansible For Execution

```
stage('Prepare Ansible For Execution') {
    steps {
        sh """
        echo ${WORKSPACE}
        if grep -q '^roles_path=' ${WORKSPACE}/deploy/ansible.cfg; then
            sed -i "s|^roles_path=.*|roles_path=${WORKSPACE}/roles|" ${WORKSPACE}/deploy/ansible.cfg
        else
            echo "roles_path=${WORKSPACE}/roles" >> ${WORKSPACE}/deploy/ansible.cfg
        fi
        """
    }
}
```

* stage('Prepare Ansible For Execution'): Prepares Ansible for the pipeline run by updating the roles_path in the ansible.cfg file.
* sh """ ... """: Executes the enclosed shell script:
  - Prints the WORKSPACE path.
  - grep -q '^roles_path=' ...: Checks if roles_path is already defined in ansible.cfg.
  - sed -i: Updates roles_path if it exists.
  - echo ... >>: Appends roles_path if it doesn't exist.


### Stage: Test SSH Connections

```stage('Test SSH Connections') {
    steps {
        script {
            def hosts = [
                [group: 'uat-webservers', ip: '172.31.26.143', user: 'ec2-user'],
                [group: 'uat-webservers', ip: '172.31.18.71', user: 'ec2-user'],
                [group: 'lb', ip: '172.31.18.31', user: 'ubuntu'],
                [group: 'db', ip: '172.31.30.166', user: 'ubuntu']
            ]
            for (host in hosts) {
                sshagent(['ssh-ansible']) {
                    sh "ssh -o StrictHostKeyChecking=no -i /home/ubuntu/.ssh/key.pem ${host.user}@${host.ip} exit"
                }
            }
        }
    }
}
```

* stage('Test SSH Connections'): Verifies that SSH connections to all target servers work properly.
* script {}: Allows executing custom Groovy scripts.
* def hosts: Defines a list of servers, including group names, IP addresses, and SSH usernames.
* for (host in hosts): Iterates over the list of servers.
* sshagent(['ssh-ansible']): Uses the ssh-ansible credentials to authenticate via SSH.
* sh "ssh -o StrictHostKeyChecking=no ...": Executes an SSH command to test connectivity to each server.


### Stage: Run Ansible Playbook

```
stage('Run Ansible playbook') {
    steps {
        sshagent(['ssh-ansible']) {
            ansiblePlaybook(
                become: true,
                credentialsId: 'ssh-ansible',
                disableHostKeyChecking: true,
                installation: 'ansible',
                inventory: "${WORKSPACE}/inventory/dev.yml",
                playbook: "${WORKSPACE}/playbooks/site.yml"
            )
        }
    }
}
```

* stage('Run Ansible playbook'): Runs the Ansible playbook.
* sshagent(['ssh-ansible']): Uses the ssh-ansible credentials for Ansible.
* ansiblePlaybook: Executes the Ansible playbook with the following options:
* become: true: Enables privilege escalation (e.g., sudo).
* credentialsId: 'ssh-ansible': Uses the specified credentials for SSH.
* disableHostKeyChecking: true: Skips SSH host key verification.
* installation: 'ansible': Specifies the Ansible installation to use.
* inventory: Points to the inventory file (dev.yml).
* playbook: Specifies the playbook file (site.yml) to execute.


### Stage: Clean Workspace After Build

```
stage('Clean Workspace after build') {
    steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
    }
}
```

* stage('Clean Workspace after build'): Cleans up the workspace after the pipeline execution.
* cleanWs(...): Removes all files and directories in the workspace, with options to clean in specific scenarios:
  - cleanWhenAborted: Clean if the pipeline was aborted.
  - cleanWhenFailure: Clean if the pipeline failed.
  - cleanWhenNotBuilt: Clean if the pipeline was skipped.
  - cleanWhenUnstable: Clean if the pipeline completed but with warnings.


### Summary

* Initial cleanup: Ensures a fresh workspace.

* Checkout SCM: Pulls the latest code.

* Prepare Ansible: Configures Ansible for the pipeline.

* Test SSH Connections: Verifies connectivity to all servers.

* Run Ansible playbook: Deploys infrastructure or applications. Ansible playbook. : - Uses the sshagent step to ensure the SSH key is available for Ansible. - Runs the ansiblePlaybook step with the specified parameters . To ensure jenkins properly connects to all servers, you will need to install another plugin known as ssh agent , after that, go to manage jenkins > credentials > global > add credentials , usee ssh username and password , fill out the neccesary details and save.

* Clean Workspace: Cleans up files post-build.

* Now back to your inventory/dev.yml , update the inventory with thier respective servers private ip address

* Ensure that the Git plugin is configured correctly in Jenkins:
  - Go to Manage Jenkins → Global Tool Configuration. Ensure the Git executable is set up properly (e.g., /usr/bin/git).

* Specify credentials for cloning:
  - Create GitHub credentials in Jenkins under Manage Jenkins > Manage Credentials.


### Update SIT Inventory

Define servers in the sit inventory file:
```
[tooling]
SIT-Tooling-Web-Server-Private-IP-Address

[todo]
SIT-Todo-Web-Server-Private-IP-Address

[lb]
SIT-Nginx-Private-IP-Address

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
SIT-DB-Server-Private-IP-Address
```


### Add Parameters to Jenkinsfile

1. Introduce parameters for environment and tags:

```
pipeline {
    agent any
    parameters {
       string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
       string(name: 'tags', defaultValue: '', description: 'Ansible tags to limit execution')
     }
}
```
![image](https://github.com/user-attachments/assets/6243a432-d612-4eb5-87f7-d0b139b57651)


2. Update the inventory path with this : ${inventory}

3. Default values ensure the dev environment is used if no other value is specified.

4. Update the jenkins file to included the ansible tags before it runs playbook:

![image](https://github.com/user-attachments/assets/36d7bcbe-b26d-4d67-872e-a78318b7317f)

---

### Step 6: Test and Deploy to Other Environments

Test Ansible Locally by running the playbook with parameters to test the changes locally:

```
ansible-playbook -i inventory/dev.yml playbook.yml --tags "webserver"
```

* Execute Pipeline in Jenkins
* Go to the pipeline job in Jenkins.
* Select Build with Parameters.
* Input values for the environment (e.g., sit) and tags (e.g., webserver).
* Click Build Now to execute the deployment.

![image](https://github.com/user-attachments/assets/1324b19a-3a4e-40fc-8a86-fbbba9a3754d)

![image](https://github.com/user-attachments/assets/6990fec5-0c5f-42af-aa32-343b7b794315)


### Step 7: Debugging tips

#### Common Issues and Fixes
##### Code Not Updated in Workspace:
  * Ensure cleanWs() is called at the start of the pipeline.
  * Check the workspace manually on the Jenkins server.

##### Environment Variables Not Set:
  * Verify ANSIBLE_CONFIG is correctly set.
     
##### Branch Mismatch:
  * Confirm the correct branch is being used with git branch in the Jenkins workspace.

---


### CI/CD Pipeline for PHP TODO Application

We previously set up a tooling website deployment through Ansible. Now, we are adding another PHP application to our infrastructure management. This application includes unit tests, making it ideal for demonstrating an end-to-end CI/CD pipeline.

The deployment goal is to use Artifactory for storing and deploying code artifacts, bypassing direct Git integration.

#### Install Artifactory

Install artifactory on Ubuntu 24.04 LTS by following this guide [Install artifactory on Ubuntu 24.04 LTS](https://www.fosstechnix.com/install-jfrog-artifactory-on-ubuntu-24-04-lts/)

![image](https://github.com/user-attachments/assets/1a860607-19c1-4627-954d-caf7764aac8a)

cd /opt/artifactory-oss-6.9.6

sudo ./bin/artifactory.sh start

http://:8081

### Phase 1: Prepare Jenkins

#### 1. Fork the Repository
Fork the following repository into your GitHub account: https://github.com/StegTechHub/php-todo.git

![image](https://github.com/user-attachments/assets/0b37010e-a408-4a91-aed4-b600e12eadbb)

#### 2. Install PHP and Dependencies
On your Jenkins server, install PHP, its dependencies, and the Composer tool. You can do this manually initially and update Ansible later:

```
sudo apt update
sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
```
![image](https://github.com/user-attachments/assets/fd00be0b-b73b-4d47-93cf-27c834962a34)

#### 3. Install Required Jenkins Plugins

I. Plot Plugin: For displaying test reports and code coverage.

![image](https://github.com/user-attachments/assets/b8e0da0f-5c59-45b9-b64c-70983cc9b6e6)

II. Artifactory Plugin: For uploading code artifacts to Artifactory.

![image](https://github.com/user-attachments/assets/35201083-63dc-48cd-bca2-f6d38758b16b)


#### 4. Configure Artifactory in Jenkins
I. Go to the Jenkins UI, manage Jenkins> system> Jfrog Platform Instances.
II. Add Artifactory server details (ID, URL, and credentials).
III. Test the connection to ensure it’s working.

![image](https://github.com/user-attachments/assets/bbe1d88d-3307-461d-92b5-c0253e5fb237)

IV. Create a local repository and call it todo-dev-local, set the repository type to generic

![image](https://github.com/user-attachments/assets/c9eddc64-f957-4e54-900c-a8554234f986)


### Phase 2: Integrate Artifactory Repository with Jenkins

#### 1. Create a Dummy Jenkinsfile: 

Add a basic `Jenkinsfile` to the repository and Install mysql client

```
sudo apt update
sudo apt install mysql-client -y
```

#### 2. Set Up a Multibranch Pipeline

Use the Blue Ocean interface to create a multibranch Jenkins pipeline.

![image](https://github.com/user-attachments/assets/0de2723d-3a95-46ec-9b83-b70f5654e9cf)


#### 3. Configure the Database

I. Update the `env_vars/dev.yml` file to include the todo app database named `homestead`:

```
# MySQL configuration for the development environment
mysql_root_username: "root"
mysql_root_password: "Password.123"

# Define databases and users to be created for the dev environment
mysql_databases:
  - name: homestead
    encoding: utf8
    collation: utf8_general_ci

mysql_users:
  - name: homestead
    host: "%"
    password: Password.123
    priv: "*.*:ALL,GRANT"
```

>Also update your security group for port 3306 to allow inbound traffic from your jenkins server

II. Save the actual details as environment variables in the Jenkins UI Navigate to Manage jenkins > System > Global properties > Environment variables

![image](https://github.com/user-attachments/assets/30a92d3d-fd74-4ecd-acd9-58366d17e05e)

III. Run the playbook:

```
cd ansible-config-mgt
ansible-playbook -i inventory/dev.yml playbooks/site.yml
```

IV. Navigate to the db server and verify the creation of the databases and users.

```
sudo mysql -u root -p #(enter password 'Passw0rd123#' when prompted)

# Verify databases
SHOW DATABASES;

#Verify users
SELECT User, Host FROM mysql.user;

#Verify privileges
SHOW GRANTS FOR 'username'@'hostname'; #(Replace 'username' and 'hostname' with the actual username and hostname you want to check.)
```


#### 4. Update the `Jenkinsfile`: 

```
pipeline {
  agent any
  stages {
    stage('Initial cleanup') {
      steps {
        dir(path: "${WORKSPACE}") {
          deleteDir()
        }

      }
    }

    stage('Checkout SCM') {
      steps {
        git(branch: 'main', url: 'https://github.com/AyopoB/php-todo.git')
      }
    }

    stage('Prepare Dependencies') {
      steps {
        sh 'mkdir -p bootstrap/cache'
        sh 'chmod -R 775 bootstrap/cache'
        sh 'mv .env.sample .env'
        sh 'composer install'
        sh 'php artisan migrate'
        sh 'php artisan db:seed'
        sh 'php artisan key:generate'
      }
    }

  }
}
```

## NOTE: If the above fails it might be due to composer compatibility issues with your php version, for that try the step below to fix the potential issues:

1. Downgrade your PHP version to 7.4 if possible. Add the Required Repository Ubuntu does not include older PHP versions in its default repositories. Use the ondrej/php PPA (a popular source for PHP packages):

```
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:ondrej/php
sudo apt update
```

2. Install PHP 7.4 Install PHP 7.4 and any necessary modules:

```
sudo apt install -y php7.4 php7.4-cli php7.4-fpm php7.4-mbstring php7.4-xml php7.4-curl php7.4-mysql
```

3. Update Alternatives Register PHP 7.4 with the update-alternatives system:

```
sudo update-alternatives --install /usr/bin/php php /usr/bin/php7.4 74
sudo update-alternatives --install /usr/bin/php php /usr/bin/php8.3 83
```

4. Switch to PHP 7.4 Use update-alternatives to select PHP 7.4:

```
sudo update-alternatives --config php
```

5. Verify the PHP Version Confirm the active PHP version:

```
php -v
```

### Notice the Prepare Dependencies section: 

1. Sets up the .env file, installs libraries with Composer, and initializes the database using php artisan commands.

2. After running php artisan migrate, check the database tables with SHOW TABLES to verify creation.

> * The required file by PHP is .env so we are renaming .env.sample to .env
> * Composer is used by PHP to install all the dependent libraries used by the application
> * php artisan uses the .env file to setup the required database objects - (After successful run of this step, login to the database, run show tables and you will see the tables being created for you)

![image](https://github.com/user-attachments/assets/37e0924e-f46c-466b-a043-1f9125bf3aaf)

3. Now, Let's add unit test to it:

```
pipeline {
    agent any
    stages {
        stage("Initial Cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/AyopoB/php-todo.git'
            }
        }
        stage('Prepare Dependencies') {
            steps {
                sh 'mv .env.sample .env'
                sh '''
                    mkdir -p bootstrap/cache
                    mkdir -p storage/framework/sessions
                    mkdir -p storage/framework/views
                    mkdir -p storage/framework/cache                        
                    chown -R jenkins:jenkins bootstrap storage 
                    chmod -R 775 bootstrap storage 
                '''
                sh 'composer install'
                sh 'php artisan migrate'
                sh 'php artisan db:seed'
                sh 'php artisan key:generate'
            }
        }
        stage('Execute Unit Tests') {
            steps {
                sh './vendor/bin/phpunit'
            }
        }
    }
}
```

![image](https://github.com/user-attachments/assets/63dd53fe-d1f1-4a10-88a3-d6f6d9600eec)


### Phase 3: Code Quality Analysis

1. Set Up phploc phploc is a tool for code quality analysis in PHP. Install it and configure Jenkins to plot the data. PHPLOC (PHP Lines Of Code) is a tool for measuring the size and complexity of PHP projects. The output of the data will be saved in the /build/logs/phploc.csv file.

2. Add Code Analysis to the Jenkinsfile

```
stage('Code Analysis') {
    steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'
    }
}
```

![image](https://github.com/user-attachments/assets/e6727d3f-ca97-4e7e-9e60-0b4679a2c893)


3. Configure the Plot Plugin Use the Plot plugin to graphically display phploc data in Jenkins. Add the following stage to the Jenkinsfile:

```
stage('Plot Code Coverage Report') {
      steps {

            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)                          ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of code', yaxis: 'Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'      
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'


      }
    }
```
> You should now see a Plot menu item on the left menu. Click on it to see the charts. (The analytics may not mean much to you as it is meant to be read by developers. So, you need not worry much about it – this is just to give you an idea of the real-world implementation).

![image](https://github.com/user-attachments/assets/4a1ee07f-6b94-4568-a04d-dde375a5551e)

![image](https://github.com/user-attachments/assets/0a3e2fa2-8afe-4261-beb8-fa05bbc0adbb)


4. Bundle the application code for into an artifact (archived package) upload to Artifactory

```
stage ('Package Artifact') {
    steps {
            sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
     }
    }
```

![image](https://github.com/user-attachments/assets/59a69288-1c0e-4a7e-b214-7fda628200db)


5. Publish the resulted artifact into Artifactory

```
stage ('Upload Artifact to Artifactory') {
          steps {
            script { 
                 def server = Artifactory.server 'artifactory-server'                 
                 def uploadSpec = """{
                    "files": [
                      {
                       "pattern": "php-todo.zip",
                       "target": "todo-dev-local/php-todo",
                       "props": "type=zip;status=ready"

                       }
                    ]
                 }""" 

                 server.upload spec: uploadSpec
               }
            }
  
        }
```

![image](https://github.com/user-attachments/assets/8cc9ba7a-3531-499b-b36b-f2ce72f6738e)


6. Confirm upload on Artifactory:

![image](https://github.com/user-attachments/assets/eb0aad40-8a9a-4b7b-aaf7-7793e4752200)


7. Deploy the application to the dev environment by launching Ansible pipeline

```
stage ('Deploy to Dev Environment') {
    steps {
    build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
    }
  }
```
> The build job used in this step tells Jenkins to start another job. In this case it is the ansible-project job, and we are targeting the main branch. Hence, we have ansible-project/main. Since the Ansible project requires parameters to be passed in, we have included this by specifying the parameters section. The name of the parameter is env and its value is dev. Meaning, deploy to the Development environment.

We need to get some things in place first.

- First create an rhel ec2 instance for the development todo webserver named DEV-todo-webapp.

- Include it's private IP in the dev environment inventory file.

![image](https://github.com/user-attachments/assets/166a1bc9-7c11-4a69-a200-ea152d1ed5e2)

> But how are we certain that the code being deployed has the quality that meets corporate and customer requirements? Even though we have implemented Unit Tests and Code Coverage Analysis with phpunit and phploc, we still need to implement Quality Gate to ensure that ONLY code with the required code coverage, and other quality standards make it through to the environments.

> To achieve this, we need to configure SonarQube - An open-source platform developed by SonarSource for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities

---

### SonarQube Installation

#### Understanding SonarQube and Software Quality
Before diving into the installation of SonarQube, it is crucial to understand the concepts it addresses:

#### Software Quality
Software quality refers to the extent to which a software component, system, or process meets specified requirements based on user needs and expectations.

#### Software Quality Gates
Quality gates are acceptance criteria presented as a predefined set of quality measures that a software project must satisfy to progress to the next stage of its lifecycle. SonarQube facilitates the creation of these quality gates, ensuring only high-quality software is shipped.

#### Importance of SonarQube in DevOps
In DevOps pipelines, speed is essential for software delivery. However, the quality of delivery must not be compromised. SonarQube addresses this by enabling the setup of quality gates, ensuring adherence to predefined quality standards. In this project, we will use the predefined "Sonar Way" quality gate. Custom quality gates can also be defined in collaboration with software testers, developers, and project leads.

---

### SonarQube Installation on Ubuntu 20.04 with PostgreSQL Backend
This guide walks through the manual installation of SonarQube version 7.9.3 with PostgreSQL as its backend database. The installation process involves configuring the Linux kernel for optimal performance, installing dependencies, setting up PostgreSQL, and configuring SonarQube. Automation using Ansible is recommended for production environments.


#### Step 1: Tune Linux Kernel
To optimize the performance of SonarQube, certain kernel parameters need to be adjusted. These changes can be made either for the current session or permanently.

##### Temporary Changes

Execute the following commands:

```
sudo sysctl -w vm.max_map_count=262144
sudo sysctl -w fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```

##### Permanent Changes

To make these changes persistent:

1. Edit the file `/etc/security/limits.conf`.
2. Append the following lines:

```
sonarqube - nofile 65536
sonarqube - nproc 4096
```
   
#### Step 2: Update and Upgrade System Packages

```
sudo apt-get update
sudo apt-get upgrade -y
```

#### Step 3: Install Required Packages: 

Install wget and unzip: 

```
sudo apt-get install wget unzip -y
```


#### Step 4: Install OpenJDK and Java Runtime Environment (JRE) 17

1. SonarQube is Java-based, so installing Java is a prerequisite.

```
sudo apt-get install openjdk-17-jdk -y
sudo apt-get install openjdk-17-jre -y
```

2. Set Default JDK

```
sudo update-alternatives --config java
```
From the list, select OpenJDK 17 by entering the corresponding number.

3. Verify Java Installation

```
java -version
```
![image](https://github.com/user-attachments/assets/34b686f3-e3b0-4fca-a572-c0cdd040698f)


#### Step 5: Install and Setup postgresql for Sonarqube

1. Add Postgresql repo to the repo list

```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

2. Download Postgresql software

```
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```

3. Install the Postgresql databases server:

```
sudo apt-get -y install postgresql postgresql-contrib
```

4. Start Postgresql server

```
sudo systemctl start postgresql
```

5. Enable Postgresql to start automatically on reboot

```
sudo systemctl enable postgresql
```

6. Check Postgresql status

```
sudo systemctl status postgresql
```


#### Step 6: Configure Postgre

1. Change password for default postgres user. Pass in your intended password (Passw0rd123#)

```
sudo passwd postgres
```

2. switch to postgres user

```
su - postgres
```

3. Create new user
```
createuser sonar
```

4. Switch to posgresql shell

```
psql
```

5. Set a password for the newly created postgresql user 

```
ALTER USER sonar WITH ENCRYPTED password 'sonar';
```

6. Create a new database for PostgreSQL database by running:

```
CREATE DATABASE sonarqube OWNER sonar;
```

7. Grant all privileges to sonar user on sonarqube Database.

```
grant all privileges on DATABASE sonarqube to sonar;
```

8. Exit from the psql shell:

```
\q
```

9. Switch to root user:

```
exit
```


#### Step 7: Install Sonarqube on Ubuntu 24.04.LTS

You can find the opensource distributions [here](https://binaries.sonarsource.com/?prefix=Distribution/sonarqube/)

```
# Navigate to the /tmp directory and download the installation files
cd /tmp && sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.8.100196.zip

# unzip the archive to the /opt directory
sudo unzip sonarqube-9.9.8.100196.zip -d /opt

# Rename extracted setup to /opt/sonarqube directory
sudo mv /opt/sonarqube-9.9.8.100196 /opt/sonarqube
```

#### Configure Sonarqube: 

Sonarqube cannot be run as root user. Hence, we will create a group and user to run sonarqube


1. Create a group sonar

```
sudo groupadd sonar
```

2. Now add a user with control over the /opt/sonarqube directory

```
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube -R
```

3. Open SonarQube configuration file using your favourite text editor (e.g., nano or vim)

```
sudo vim /opt/sonarqube/conf/sonar.properties

# Find the following: (Make sure to uncomment them and add the username and password of the postgresql database )
#sonar.jdbc.username=
#sonar.jdbc.password=
#sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
```

4. Edit the sonar script file and set it a

```
sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
```

5. Paste the following under the APP_NAME

```
RUN_AS_USER=sonar
```

![image](https://github.com/user-attachments/assets/57df1050-79dd-47d3-8897-8628ef8c1257)


![image](https://github.com/user-attachments/assets/421f4c0c-75ac-4768-8e61-c24cbdb5e142)


#### Start SonarQube

1. Switch to sonar user

```
sudo su sonar
```

2. Move to the script directory

```
cd /opt/sonarqube/bin/linux-x86-64/
```

3. Run the script to start sonarqube

```
./sonar.sh start
```

4. Run the script to check sonarqube status

```
./sonar.sh status
```

5. To check SonarQube logs, navigate to /opt/sonarqube/logs/sonar.log directory

```
tail /opt/sonarqube/logs/sonar.log
```

![image](https://github.com/user-attachments/assets/48cd7762-1a60-4cb9-acec-3de8fdf21584)


#### Configure SonarQube as a Systemd Service

```
# Stop the currently running SonarQube service
cd /opt/sonarqube/bin/linux-x86-64/
./sonar.sh stop

# exit the session
exit

# Create a systemd service file for SonarQube to run as System Startup.
sudo vi /etc/systemd/system/sonar.service

# Add the following configuration:
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target

# Save the file and exit
:wq

# Control the service with systemctl
sudo systemctl start sonar
sudo systemctl enable sonar
sudo systemctl status sonar
```

![image](https://github.com/user-attachments/assets/0db1fa25-eb3b-407b-ac29-b49a5d787df7)

![image](https://github.com/user-attachments/assets/e7c4a87c-19ed-4271-bcd8-7c4535406fae)


#### Access SonarQube

1. Open your browser and navigate to the SonarQube instance:
>Firstly add an inbound rule for port 9000 in your AWS security group

```
http://<server_IP>:9000
```
Replace `<server_IP>` with your server's IP address.

![image](https://github.com/user-attachments/assets/ba8693ce-cf42-460f-b119-854f80f2bec9)

2. Login with the default credentials:
   * Username: `admin`
   * Password: `admin`

![image](https://github.com/user-attachments/assets/586d7e9b-793f-4335-940d-177759fba310)

3. Change the default password for better security.


### Configuring SonarQube and Jenkins for Quality Gate

#### Steps to Configure SonarQube in Jenkins

#### 1. Install SonarScanner Plugin

* Go to Manage Jenkins > Plugins > Available Plugins.
* Search for SonarScanner and install it.

#### 2. Add SonarQube Server in Jenkins

- Navigate to Manage Jenkins > Configure System.

- Under "SonarQube Servers":
  * Add a new server configuration.
  * Provide a name (e.g., sonarqube).
  * Input the server URL (e.g., http://<SonarQube-Server-IP>:9000).
  * Add authentication token generated in the next step.

![image](https://github.com/user-attachments/assets/edbb3127-c423-4e49-ae8d-143134e718f6)


#### 3. Generate Authentication Token in SonarQube

* Log into SonarQube and navigate to `User > My Account > Security > Generate Tokens`.
* Create a token and copy it.

![image](https://github.com/user-attachments/assets/8cd9b445-e93d-41d7-b5d8-7ddf02aed81c)

![image](https://github.com/user-attachments/assets/db07e03a-eb84-4ec1-98e5-4d4a42dfd24f)

![image](https://github.com/user-attachments/assets/8f6f7072-0a6f-4948-a9ac-55b8b0afad66)


#### 4. Configure Webhook for Quality Gate

* In SonarQube, navigate to Administration > Configuration > Webhooks > Create.
* Add the Jenkins server URL for the webhook: http://<JENKINS_HOST>/sonarqube-webhook/.
* Save the configuration.

![image](https://github.com/user-attachments/assets/976f951a-187d-4d62-a076-a9a99d3837e2)

* Configure SonarQubeScanner tool: `Manage jenkins > Tools > Add SonarQube scanner`


### Update Jenkins Pipeline for SonarQube

#### 1. Quality Gate Stage in Jenkinsfile

* Add the following snippet to your `Jenkinsfile`:

```
stage('SonarQube Quality Gate') {
    environment {
        scannerHome = tool 'SonarQubeScanner'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
    }
}
```

![image](https://github.com/user-attachments/assets/8e59753c-0eab-4bb9-963f-109b69694b76)
>NOTE: The above step will fail because we have not updated `sonar-scanner.properties

#### 2. Now, we configure sonar-scanner.properties

* Locate the scanner configuration directory on the Jenkins server:

```
cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/conf/

sudo vi sonar-scanner.properties
```

* Update the file with your project configuration. Example for `php-todo`:

```
sonar.host.url=http://<SonarQube-Server-IP>:9000
sonar.projectKey=php-todo
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
sonar.php.exclusions=**/vendor/**
sonar.php.coverage.reportPaths=build/logs/clover.xml
sonar.php.tests.reportPath=build/logs/junit.xml
```

>HINT: To know what exactly to put inside the sonar-scanner.properties file, SonarQube has a configurations page where you can get some directions.


* Examine Scanner Tool: Verify the scanner setup:

```
cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/bin
ls -latr
```

You should see files like `sonar-scanner` and `sonar-scanner-debug`.

* Run the build again

![image](https://github.com/user-attachments/assets/d97b5ef4-2e68-425d-883b-56d4100eb939)


* Generate Pipeline Syntax:
To generate additional code snippets for SonarQube integration:
  - Navigate to Dashboard > Pipeline Syntax in Jenkins.
  - Click on steps then, Select withSonarQubeEnv.
  - Generate and add the required block to your pipeline.

![image](https://github.com/user-attachments/assets/fdba2786-4e29-4492-8371-d33bd54a193b)

  - Run the build again

![image](https://github.com/user-attachments/assets/3ee1ce40-0178-4b9a-bb0f-7dc6344e703e)



### Implementing Quality Gate Conditions

#### 1. Update Pipeline to Include Branch Filtering and Timeout

Modify the `Jenkinsfile` to check branch names and enforce quality gate conditions:

```
stage('SonarQube Quality Gate') {
    when {
        branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"
    }
    environment {
        scannerHome = tool 'SonarQubeScanner'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
        }
        timeout(time: 1, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    }
}
```
To test, create different branches and push to GitHub. You will realise that only branches other than develop, hotfix, release, main, or master will be able to deploy the code.

Create multiple branches and commit, push the new code.

![image](https://github.com/user-attachments/assets/b0b52594-a977-446c-aed9-e1670ee0b2c6)


For Branch `main`

![image](https://github.com/user-attachments/assets/6fcc539b-038b-4474-91b1-f4236d8d9e6f)
>Notice that with the current state of the code, it cannot be deployed to Integration environments due to its quality. In the real world, DevOps engineers will push this back to developers to work on the code further, based on SonarQube quality report. Once everything is good with code quality, the pipeline will pass and proceed with sipping the codes further to a higher environment.


## Complete the following tasks to finish Project 14

1. Introduce Jenkins agents/slaves – Add 2 more servers to be used as Jenkins slave. Configure Jenkins to run its pipeline jobs randomly on any available slave nodes. Let's add 2 more servers to be used as Jenkins slave and install java in them.

```
sudo apt update
sudo apt install default-jdk
```

2. Configure webhook between Jenkins and GitHub to automatically run the pipeline when there is a code push. Let's Configure the new nodes on Jenkins Server. Navigate to Dashboard > Manage Jenkins > Nodes, click on New node and enter a Name and click on create.

To connect to slave_one completed this fields and save.

Name: slave_one Remote root directory: /opt/build (This can be any directory for the builds) Labels: slave_one save

![image](https://github.com/user-attachments/assets/f4879e85-cfda-4ca2-8ec4-161d4f8eae9f)

To connect to slave_one, click on the slave_one and if you finsih configuration save it you see

![image](https://github.com/user-attachments/assets/41b78bf2-a230-4c8c-86f3-36fe9b63b675)

Use either options. In this case, I use the first option
>before running please check yor public ip addres of your jenkin is same as the if not go to Dashboard> manage Jenkin > systme and update current IP

```
sudo mkdir -p /opt/build
sudo chown -R ubuntu:ubuntu /opt/build
sudo chmod -R 755 /opt/build
ls -ld /opt/build
```

To make it run in background and &

![image](https://github.com/user-attachments/assets/c8b442fa-2c4b-45e3-8f59-a63e21b32656)



### Now slave_two

![image](https://github.com/user-attachments/assets/5e28c877-c981-44f1-8777-07f622cb2434)

3. Deploy the application to all the environments in order to deploy to all environment we Add these stages to our existing Jenkins pipeline script

Add the following configuration which is related to the php-todo project to the sonar.properties file

```
sonar.host.url=http://<SonarQube-Server-IP-address>:9000
sonar.projectKey=php-todo
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
sonar.php.exclusions=**/vendor/**
sonar.php.coverage.reportPaths=build/logs/clover.xml
sonar.php.tests.reportPath=build/logs/junit.xml
```













### Summary:
- **Nginx** will act as a reverse proxy to handle web traffic and route it to the correct internal server based on the subdomain.
- DNS entries need to be created for each service and environment under the **devops.com** domain to allow easy access.
- **Ansible Inventory** files define which servers belong to which environments, and you can group servers to manage them more efficiently.
- **group_vars** can be used to set shared variables for groups of servers, ensuring consistency and reducing redundancy in your configurations.
- **pentest:children** allows grouping related servers together (such as **pentest-todo** and **pentest-tooling**) to manage them both at once, while still having the ability to target individual servers.
- **DevOps** involves automating and managing servers for various environments that simulate different stages of the software development lifecycle.
- You’ll work with servers for **CI**, **Dev**, and **Pentest** environments initially, then move to **SIT** and **UAT**.
- You don’t need to create all servers at once—create just enough for the environment you're working on to minimize costs.
- **Pentest** is unique because it requires specific security tools and configurations, while **SIT** and **UAT** are more about testing and user validation.
- In your **CI environment**, you will need to configure both **SonarQube** and **Artifactory** to ensure that:
- **SonarQube** helps you maintain high code quality by automatically reviewing your code for bugs, vulnerabilities, and other issues.
- **Artifactory** manages your build artifacts, ensuring that every build is safely stored and accessible for other environments (e.g., from Dev to Prod).

By creating **Ansible roles** for each of these tools, you can automate the installation, configuration, and integration processes, making your setup more efficient, consistent, and scalable.

In simpler terms:
- **SonarQube** makes sure your code quality is high, helping you catch bugs and vulnerabilities early.
- **Artifactory** ensures that the code you build is stored safely and can be reused whenever needed in the pipeline.

These tools, configured via **Ansible roles**, are key to automating the process of setting up a robust CI/CD pipeline, enabling faster and more reliable software delivery.



This setup allows you to build your project step by step, ensuring that the environments needed for each stage of development and testing are in place without incurring unnecessary costs.







