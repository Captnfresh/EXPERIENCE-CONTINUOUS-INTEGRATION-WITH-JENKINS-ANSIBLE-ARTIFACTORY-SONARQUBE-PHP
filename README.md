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







