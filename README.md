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
        git branch: 'main', credentialsId: '6610fbef-537f-49d7-ab4b-e7e57776dffe', url: 'https://github.com/Captnfresh/ansible-config-mgt'
      }
    }
      stage('Test SSH Connection') {
            steps {
                sshagent(['private_key']) {  // 'ansible' is the ID of the credentials
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.91.90 exit'
                }
            }
        }
   


    stage('Run Ansible playbook') {
      steps {
       ansiblePlaybook credentialsId: 'private_key', disableHostKeyChecking: true, installation: 'ansible-config-mgt', inventory: 'inventory/dev.yml', playbook: 'playbooks/site.yml', vaultTmpPath: ''
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







