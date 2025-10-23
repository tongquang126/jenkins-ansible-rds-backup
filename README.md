# 🧩 Jenkins-Ansible MySQL Export Automation

This project automates the **export of MySQL/Aurora databases** from **AWS Backup snapshots** to **Amazon S3** using **Ansible**, integrated within a **Jenkins CI/CD pipeline**.

It extends the functionality of the original [pository aurora-db-export](https://github.com/tongquang126/aurora-db-export)
 by introducing a Jenkins **pipeline (Jenkinsfile)** to orchestrate the execution of the Ansible playbooks in a controlled, repeatable, and auditable environment.

# 🚀 Overview
The pipeline enables DevOps teams to:
- Retrieve AWS Backup snapshots of MySQL or Aurora databases
- Restore a temporary RDS instance from snapshot
- Dump a specific MySQL database from temporary RDS instance
- Export the data to an Amazon S3 bucket
- Tear down the temporary resources after completion
- Automate the entire process through Jenkins pipeline stages

This setup improves operational consistency, reduces manual steps, and enables integration into enterprise CI/CD workflows.

## Project Architecture
### High-Level Components
```pgsql
+-----------------------------+
|        Jenkins Server       |
| (Controller + Pipeline Job) |
+-------------+---------------+
              |
              v
+-----------------------------+
| Jenkins Agent (label: ansible) |
|  - Acts as Ansible Controller   |
|  - Executes Playbook Stages     |
|  - Runs Python venv environment |
+-------------+-------------------+
              |
              v
+-----------------------------+
|     AWS Infrastructure      |
|  - RDS / Aurora Snapshot    |
|  - S3 Bucket (for export)   |
|  - AWS Secrets Manager      |
+-----------------------------+
```

## Workflow
Pipeline overview
<img width="1579" height="674" alt="image" src="https://github.com/user-attachments/assets/34d40e0b-00f4-4279-aa02-05bedf2e1f21" />
1. Jenkins pipeline triggers (manual or scheduled).
2. Agent labeled ansible initializes Python virtual environment.
3. Repository (Ansible project) is checked out from SCM (GitHub).
4. Required Ansible collections are installed from collections/requirements.yml.
5. Playbook (playbooks/main.yml) is executed:
   - Retrieves DB credentials from AWS Secrets Manager.
   - Identifies the latest AWS Backup snapshot.
   - Creates a temporary DB instance.
   - Dump a specific MySQL database from temporary RDS instance
   - Exports data to S3 bucket.
   - Cleans up temporary resources.
6. Jenkins archives logs and reports success/failure.
   
## Security Considerations
- Secrets are n**ever stored in Jenkins**; they are retrieved dynamically from **AWS Secrets Manager** via Ansible.
- Jenkins workspace is **cleaned up** after each run using cleanWs().
- The pipeline runs under l**east-privilege IAM roles** for both RDS and S3 operations.

##  Logs & Artifacts
- Ansible execution log: ansible_run.log
- Stored as Jenkins build artifact for troubleshooting.
- Can be viewed in Jenkins UI or downloaded for audit.

## Future Enhancements
- Add Slack or email notifications for job completion.
- Add parameterized builds for selecting snapshot ID or S3 destination.
- Integrate Terraform for infrastructure provisioning.
- Add automated testing for Ansible roles.

## 👨‍💻 Author
Tony Tong 
CloudOps Engineer | AWS & DevOps Automation
