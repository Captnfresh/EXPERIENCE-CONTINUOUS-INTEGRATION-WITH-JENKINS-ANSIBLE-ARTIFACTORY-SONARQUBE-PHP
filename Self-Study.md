# DevOps: Continuous Integration and Continuous Delivery (CI/CD)

## Introduction
In this self-study, I focused on understanding key **DevOps** concepts, specifically **Continuous Integration (CI)** and **Continuous Delivery/Deployment (CD)**. These concepts are critical for streamlining the development process and ensuring high-quality, automated software delivery.

## 1. Theoretical Concepts Before Practical Work
Before diving into hands-on work, it's important to understand the theoretical foundation behind DevOps. Key concepts to grasp include the **CI/CD pipeline**, the roles of various tools, and how to measure success. A deep understanding of these principles will allow you to explain the process to others or even in job interviews.

## 2. Compiled vs. Interpreted Programming Languages
- **Compiled Languages** (e.g., Java, .NET): These need to be compiled into an **executable file** (e.g., `.jar` in Java) before they can be run. The executable contains all the code and necessary libraries for the program to function.
- **Interpreted Languages** (e.g., PHP, JavaScript, Python): These can be run directly without the need for an executable file. However, deploying code directly from Git to servers is not optimal.
- **Best Practice**: It’s recommended to package the code and its dependencies into an archive (e.g., `.tar.gz` or `.zip`) for easier deployment.

## 3. What is Continuous Integration (CI)?
- **CI** is the practice of frequently merging developers' work into a shared **code repository** multiple times a day. This reduces conflicts and ensures that the codebase stays up-to-date and tested.
- **Merge Hell**: This happens when developers don’t frequently merge their work, leading to conflicts and issues.
- **Key Concepts in CI**:
  - **Commit Early, Push Often**: Developers should commit and push code regularly to avoid conflicts.
  - **Test Locally**: Developers should test their code locally before committing to avoid breaking others' work.
  - **CI Server (e.g., Jenkins)**: A dedicated server that picks up the code, runs the build, and executes tests. It provides visibility into the status of code.

## 4. Continuous Delivery (CD) vs. Continuous Deployment
- **Continuous Delivery**: The software is always ready to be deployed, but deployment requires manual intervention after QA checks are passed.
- **Continuous Deployment**: The deployment process is fully automated. Each commit triggers the deployment to users automatically, without manual intervention.

## 5. Real-World CI Pipeline Breakdown
A typical CI pipeline involves several stages:
1. **Version Control**: Developers commit their work to a shared repository after local testing.
2. **Build**: The CI system compiles or packages the code for deployment.
3. **Unit Testing**: Developers' tests are executed. If any test fails, the pipeline stops, and the issue must be fixed before continuing.
4. **Deploy to Artifact Repository**: The code is stored in a repository, making it ready for deployment.
5. **Auto Testing**: After unit tests, further automated tests (e.g., integration or penetration tests) are run to ensure code quality and security.
6. **Deploy to Production**: After successful tests, the code is deployed to the live environment (production).
7. **Measure & Validate**: After deployment, live user feedback is collected, and the application’s performance is measured.

## 6. Key Metrics for Validation
After the software is deployed, live users interact with the application, and feedback is gathered for improvements. Key metrics should be observed to ensure the software is functioning as expected.

## 7. Best Practices for CI/CD
To build a reliable and efficient CI/CD pipeline, the following principles should be followed:
- **Maintain a code repository**: Centralized storage for all project code.
- **Automate build processes**: This reduces manual work and errors.
- **Make builds self-tested**: Automatically test code to ensure it works as expected.
- **Commit daily**: Everyone should commit code regularly to keep the project updated.
- **Keep builds fast**: The build process should be efficient to save time.
- **Test in production-like environments**: Test code in environments that resemble the live system to catch potential issues early.
- **Automate deployment**: If confident, automate the deployment process for faster and more reliable releases.

## 8. Why Are We Doing Everything We Are Doing? - 13 DevOps Success Metrics

DevOps aims to enhance the velocity, quality, and performance of software delivery. As we move quickly with continuous delivery and deployment, we must be cautious not to introduce errors. By tracking key metrics, we can identify potential bottlenecks and maintain the stability of the application while improving our delivery speed. Below are 13 important DevOps metrics that help track the success of the CI/CD pipeline.

### 1. Deployment Frequency
This metric tracks how often deployments occur. The goal is to have **smaller, frequent deployments** rather than large, infrequent ones. Smaller deployments are easier to test and release, reducing the risk of errors. Tracking deployments in both production and non-production environments (e.g., QA or pre-production) is important for identifying bottlenecks early.

### 2. Lead Time
Lead time measures the amount of time from starting a work item to deploying it to production. This metric is crucial for understanding how quickly new features or fixes can be shipped from development to production.

### 3. Customer Tickets
Customer support tickets and feedback are essential indicators of application quality and performance. Monitoring these tickets helps identify bugs or issues reported by users, which could impact the software’s reliability.

### 4. Percentage of Passed Automated Tests
To improve development velocity, teams should heavily rely on automated **unit and functional tests**. Tracking the percentage of passed automated tests helps measure how stable and well-tested the codebase is, ensuring that changes don't break existing functionality.

### 5. Defect Escape Rate
This metric tracks how many software defects make it to production. Ideally, defects should be caught during testing phases (QA), but some may escape to production. A lower defect escape rate means higher confidence in the quality of the code shipped.

### 6. Availability
Monitoring application availability is essential to ensure that the application is not down unexpectedly. Any unplanned outages should be tracked and minimized. A **status page** can be used to track and display the availability of the application.

### 7. Service Level Agreements (SLA)
Many companies have SLAs that define the expected performance and availability of applications. Tracking compliance with SLAs ensures that the company is meeting user expectations. Even if no formal SLA exists, non-functional requirements like performance and availability are still critical to track.

### 8. Failed Deployments
Deployments that cause outages or major issues for users are a serious concern. Tracking failed deployments helps identify patterns and improves the deployment process. Planning for **Mean Time To Failure (MTTF)** is critical to mitigate the risk of major disruptions.

### 9. Error Rates
Tracking the **error rates** within the application is essential to identify quality problems or performance issues. Errors (or exceptions) can indicate bugs, system failures, or issues with the database, network, or other critical components. Monitoring error rates helps identify areas of concern, such as:
- Bugs: New exceptions thrown after a deployment.
- Production Issues: Problems like database connection issues or query timeouts.

### 10. Application Usage & Traffic
This metric tracks user traffic and system interactions after a deployment. Unexpected spikes or drops in traffic could indicate issues, such as **DDoS attacks** or routing problems. Regular monitoring of traffic patterns ensures that the application is performing as expected.

### 11. Application Performance
Monitoring application performance is critical before, during, and after deployments. Tools like **New Relic, AppDynamics, or DataDog** can help identify performance problems, hidden errors, and bottlenecks. It's important to establish baseline performance metrics so that any deviations can be easily spotted.

### 12. Mean Time to Detection (MTTD)
MTTD measures the time it takes to detect a problem after it occurs. Fast detection is essential to prevent a major outage from going unnoticed. Robust **application monitoring** and **observability tools** help teams quickly identify and address issues.

### 13. Mean Time to Recovery (MTTR)
MTTR tracks how long it takes to recover from failures. The quicker a system can be restored after a failure, the less impact it will have on the business and users. MTTR is typically measured in hours (business hours, not calendar hours). Minimizing MTTR is crucial for maintaining a smooth user experience.

By tracking these 13 DevOps metrics, teams can monitor and improve the efficiency, quality, and reliability of their CI/CD processes. These metrics provide valuable insights into the performance of both the **development pipeline** and the **deployed application**. Adopting these practices allows teams to quickly identify and address issues, ensuring that high-quality software is delivered to users at a fast pace.


## Conclusion
This study covered the essentials of **CI/CD** in DevOps, focusing on the processes and best practices involved in continuous integration and continuous delivery. Understanding these concepts is crucial for automating software delivery, reducing errors, and increasing productivity in development teams.
