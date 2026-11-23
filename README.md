# Security-of-the-Pipeline-Note-DevSecOps-
Here is my note after finishing security of the pipeline module from tryhackme
# Intro to Pipeline Automation

## Source Code Storage and Version Control

### Source Code Storage Considerations

1. How can we perform access control for our source code?
2. How can we make sure that changes made are tracked?
3. Can we integrate our source code storage system with our development tools?
4. Can we store and actively use multiple different versions of our source code?
5. Should we host our source code internally, or can we use an external third party to host our code?

### Why Version Control?

1. We are often integrating new features into our software.
2. An entire development team is working on the code, not just one developer. To ensure that we can integrate the changes from multiple developers, version control is required.

### Git vs. SubVersion (SVN)

* The two most common source code storage and version control systems are **Git** and **SubVersion (SVN)**.
* **Git** is a *distributed* source control tool, meaning that each contributor will have their own copy of the source code.
* **SVN** is a *centralised* source control tool, meaning the control of the repo is managed centrally.

---

## Dependency Management & Automated Testing

* **Dependency Manager (Package Manager):** Required to manage libraries and SDKs.
* **External Dependencies:** Publicly available libraries and SDKs. These are hosted on external dependency managers such as **PyPi** for Python, **NuGet** for .NET, and **Gems** for Ruby libraries.
* **Internal Dependencies:** The management of internal dependencies is a bit more tricky. For these, we can use tools such as **JFrog Artifactory** or **Azure Artifacts**.
* **Integration Testing:** While unit tests focus on small parts of the application, integration testing focuses on how these small parts work together.

---

## Continuous Integration & Deployment (CI/CD)

### CI/CD Pipeline Actions

* **Starting Trigger:** The action that kicks off the pipeline process. For example, a push request is made to a specific branch.
* **Building Actions:** Actions taken to build both the project and the new feature.
* **Testing Actions:** Actions that will test the project to ensure that the new feature does not interfere with any of the current features of the application.
* **Deployment Actions:** Should a pipeline succeed, the deployment actions detail what should happen with the build. For example, it should then be pushed to the Testing Environment.
* **Delivery Actions:** As CI/CD processes have evolved, the focus is now no longer just on the deployment itself, but all aspects of the delivery of the solution. This includes actions such as monitoring the deployed solution.

---

## Environments & Deployment Strategies

| Environment | Description | Stability | Security Posture | May it contain customer data? |
| --- | --- | --- | --- | --- |
| **DEV** (Development) | The playground for developers. This environment is the most unstable as developers are continuously pushing new code and testing it. From a security standpoint, this environment has the weakest security. Access control is usually laxer, and developers often have direct access to the infrastructure itself. The likelihood of the development environment being compromised is high, but if there is adequate segregation, the impact of such a compromise should be low. | Unstable | Weakest | No |
| **UAT** (User Acceptance Testing) | Used to test the application or select features before they are pushed to production. These include unit tests that ensure the developed feature behaves as expected, and can (and should) include security tests as well. Although more stable than DEV, it can often still be fairly unstable. Certain security hardening controls are introduced, but it is still not as hardened as PreProd or PROD. | Semi-Stable | Second Weakest | No |
| **PreProd** (Pre-Production) | Used to mimic production without actual customer/user data. This environment is kept stable and used to perform the final tests before the new feature is pushed to production. From a security standpoint, PreProd's security should technically mirror PROD, although this is not always the case. | Stable | Second Strongest | No |
| **PROD** (Production) | The most sensitive environment. This is the current active environment that serves users or customers and must be kept stable. No updates should be performed here without proper change management. Security is the strongest: only a select few employees or services have the ability to make changes, and it must be hardened to prevent outsider threats from "malicious" users. | Stable | Strongest | Yes |
| **DR/HA** (Disaster Recovery / High Availability) | Depending on the criticality of the system, there may be a DR or HA environment. If the switchover is instantaneous, it is usually called an **HA** environment (often used for critical applications like Online Banking where downtime incurs large penalties). If some small downtime is allowed, it is called a **DR** environment, meant to recover from a disaster in production. DR and HA environments should be exact mirrors of PROD in both stability and security. | Stable | Strongest | Yes |

### Deployment Strategies

* **Blue/Green Environments:** Used for a Blue/Green deployment strategy when pushing an update to PROD. Instead of having a single PROD instance, there are two: the **Blue** environment is running the current application version, and the **Green** environment is running the newer version.
* **Canary Environments:** Used to smooth the PROD deployment process. Two environments are created, and users are gradually moved to the new environment.

---

## Source Code Security

### Platforms: GitHub Actions & GitLab

* **GitHub Actions:** Launched in 2018 as GitHub’s way of evolving to give developers powerful automation and Continuous Integration & Deployment. It comprises "Workflows," where you can declare (via actions) how to build the code through the development pipeline.
* **GitLab:** An open-core company that provides a DevOps software package combining the ability to develop, secure, and operate software in a single application.
* Uses **"runners"** (agents that execute jobs) to enable continuous integration and continuous deployment.
* Emphasizes reliability with features such as a built-in container registry, built-in continuous deployment to Kubernetes, and GitLab Pages for hosting static websites.



### Storing Credentials Recommendations

1. Ensure that credentials follow the principle of least privilege from code to deployment.
2. Avoid sharing the same credentials across multiple contexts to maintain accountability and simplify privilege management.
3. Use temporary credentials whenever possible, and establish procedures to rotate static credentials and detect stale credentials periodically.
4. Ensure credentials are only used under predefined conditions, such as limiting usage to a specific IP address or identity.
5. Detect secrets pushed to and stored in code repositories using Integrated Development Environments (IDE) plugins, automatic scanning, and periodic repository commit scans.
6. Use built-in vendor options or third-party tools to prevent secrets from being printed to console outputs during builds, and ensure existing outputs do not contain secrets.
7. Verify that secrets are removed from artifacts, such as container image layers and binaries.

### Environment Variables & Best Practices

1. Avoid hardcoding sensitive information in code; use environment variables instead.
2. Regularly review and rotate credentials stored in environment variables.
3. Limit access to environment variables to authorized personnel only.
4. Environment variables should be set according to the principle of least privilege.
5. Implement monitoring and auditing mechanisms to track changes to environment variables.
6. If implementing a secrets manager solution, review its encryption mechanisms and check if it's a good fit for your development environments.

---

## Git, Branches, and the Ugly

### How Git Works

* Git does not store changesets or differences; instead, it keeps **snapshots** of the content.
* When you make a commit in Git, it creates a commit object that includes a pointer to the snapshot of the content, along with information like author details, commit message, and parent commit(s) pointer(s).
* **Branches** in Git represent isolated lines of development. When you create a local branch from a remote branch, **tracking branches** are created automatically by Git (local branches linked to remote branches).

### Common Git Commands

* **Clone a repository:**
```bash
git clone <repository_url>

```


* **Clone a specific branch:**
```bash
git clone --branch <name_of_branch> <repository_url>

```


* **Update remote-tracking branches:**
```bash
git fetch

```


* **List both local and remote branches** (an asterisk `*` indicates the currently checked-out branch):
```bash
git branch -a

```


* **List only remote branches:**
```bash
git branch -r

```


* **Stage changes** (tells Git to include the changes made to the file in the next commit):
```bash
git add <filename>

```


* **Commit changes** (creates a new commit in the Git history with the staged changes):
```bash
git commit -m "commit message"

```


* **Push changes** (updates the remote repository with local changes; `origin` refers to the repository from which the project was initially cloned):
```bash
git push origin <branch_name>

```



### Managing Secrets in GitLab

1. Go to your GitLab project and navigate to the **Settings** page.
2. Click on the **CI/CD** tab and then click on **Variables**.
3. Click on **Add variable** and enter the name and value of your environment variable. Make sure to mark the variable as **Protected**.
4. Click on **Add variable** to save the variable.
5. In your code (e.g., `nostromo.go`), access the value of the environment variable using:
```go
os.Getenv("variable_name")

```



---

## CI/CD and Build Security

### Eight Fundamentals for CI/CD

1. A single source repository
2. Frequent check-ins to the main branch
3. Automated builds
4. Self-testing builds
5. Frequent iterations
6. Stable testing environments
7. Maximum visibility
8. Predictable deployments anytime

### A Typical CI/CD Pipeline

1. Developer workstations
2. Source code storage solution
3. Build orchestrator
4. Build agents
5. Environments

### GitLab CI/CD Configuration (`.gitlab-ci.yml`)

* In GitLab, project automation is defined in the `.gitlab-ci.yml` file, which contains the steps performed automatically when a new commit is made to the repo.
* GitLab uses **runners** to execute the tasks configured in the project.
* Usually, there are three stages to any CI pipeline: **build**, **test**, and **deploy**.

#### Build Job Example

```yaml
build-job:
  stage: build
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"

```

#### Test Jobs Example

```yaml
test-job1:
  stage: test
  script:
    - echo "This job tests something"

test-job2:
  stage: test
  script:
    - echo "This job tests something, but takes more time than test-job1."
    - echo "After the echo commands complete, it runs the sleep command for 20 seconds"
    - echo "which simulates a test that runs 20 seconds longer than test-job1"
    - sleep 20

```

#### Deployment Job Example

```yaml
deploy-prod:
  stage: deploy
  script:
    - echo "This job deploys something from the $CI_COMMIT_BRANCH branch."
    - echo "Making the website directory"
    - mkdir -p /tmp/time/cicd
    - echo "Copying the website files"
    - cp website_src/* /tmp/time/cicd/
    - echo "Hosting website using a screen"
    - screen -d -m php -S 127.0.0.1:8081 -t /tmp/time/cicd/ &
    - echo "Deployment complete! Navigate to http://localhost:8081/ to test!"
  environment: production

```
