# GCP Cybersecurity Attack Response Example

# Table of Contents

1. [Introduction](#introduction)
2. [Scenario](#scenario)
3. [Data Breach's Response and Recovery](#data-breachs-response-and-recovery)
    - [Task 1: Analyze the Data Breach and Gather Information](#task-1-analyze-the-data-breach-and-gather-information)
    - [Task 2: Fix the Compute Engine Vulnerabilities](#task-2-fix-the-compute-engine-vulnerabilities)
    - [Task 3: Fix Cloud Storage Bucket Permissions](#task-3-fix-cloud-storage-bucket-permissions)
    - [Task 4: Limit Firewall Ports Access](#task-4-limit-firewall-ports-access)
        - [Restrict SSH Access](#restrict-ssh-access)
    - [Task 5: Fix the Firewall Configuration](#task-5-fix-the-firewall-configuration)
        - [Customize Firewall Rules](#customize-firewall-rules)
        - [Enable Logging](#enable-logging)
    - [Task 6: Verify Compliance](#task-6-verify-compliance)
4. [Conclusion](#conclusion)

# Introduction
This is the Google Cloud Cybersecurity Certificate Capstone project, provided by Google at [cloudskillsboost](https://www.cloudskillsboost.google/) as part of their cybersecurity certificate program.

This project uses key concepts such as cloud security principles, risk management, identifying vulnerabilities, incident management, and crisis communications in an interactive capstone project. 

**Important note:** <u>this project is captured from the module "The capstone project". The project descriptions are from the course's module itself. Screenshots added to enrich explanations and processes.</u>

## Scenario
Cymbal Retail is a market powerhouse currently operating 170 physical stores and an online platform across 28 countries. They reported $15 billion in revenue in 2022, and currently employ 80,400 employees across the world.

Cymbal Retail boasts a vast customer base with a multitude of transactions happening daily on their online platform. The organization is committed to the safety and security of its customers, employees, and its assets, ensuring that its operations meet internal and external regulatory compliance expectations in all the countries it operates in.

Recently, the company has experienced a massive data breach. As a junior member of the security team, you’ll help support the security team through the lifecycle of this security incident. You'll begin by identifying the vulnerabilities related to the breach, isolate and contain the breach to prevent further unauthorized access, recover the compromised systems, remediate any outstanding compliance related issues, and verify compliance with frameworks.

# Data breach's response and recovery
## Steps
First, we’ll examine the vulnerabilities and findings in Google Cloud Security Command Center. Next, we’ll shut the old VM down, and create a new VM from a snapshot. Then, we’ll revoke public access to the storage bucket and switch to uniform bucket-level access control. Next, we’ll limit the firewall ports access and fix the firewall rules. Finally, we’ll run a report to verify the remediation of the vulnerabilities.

## Task 1. Analyze the data breach and gather information
One morning, the security team detects unusual activity within their systems. Further investigation into this activity quickly reveals that the company has suffered a massive security breach across its applications, networks, systems, and data repositories. Attackers gained unauthorized access to sensitive customer information, including credit card data, and personal details. This incident requires immediate attention and thorough investigation. The first step towards understanding the scope and impact of this breach is to gather information and analyze the available data.

In this task, we examine the vulnerabilities and findings in Google Cloud Security Command Center to determine how the attackers gained access to the data, and which remediation steps to take.

Security Command Center - PCI DSS report:
![08 - PCI DSS Report](https://github.com/user-attachments/assets/e75e3392-e92a-43e0-affa-63d41609faba)

As we examine the PCI DSS 3.2.1 report, notice that it lists the rules that are non-compliant, which relate to the data breach:

- **Firewall rule logging should be enabled so you can audit network access:** This medium severity finding indicates that firewall rule logging is disabled, meaning that there is no record of which firewall rules are being applied and what traffic is being allowed or denied. This is a security risk as it makes it difficult to track and investigate suspicious activity.
- **Firewall rules should not allow connections from all IP addresses on TCP or UDP port 3389:** This high severity finding indicates that the firewall is configured to allow Remote Desktop Protocol (RDP) traffic for all instances in the network from the whole internet. This is a security risk as it allows anyone on the internet to connect to the RDP port on any instance in the network.
- **Firewall rules should not allow connections from all IP addresses on TCP or SCTP port 22:** This high severity finding indicates that the firewall is configured to allow Secure Shell (SSH) traffic to all instances in the network from the whole internet. SSH is a protocol that allows secure remote access to a computer. If an attacker can gain access to a machine through SSH, they could potentially steal data, install malware, or disrupt operations.
- **VMs should not be assigned public IP addresses:** This high severity finding indicates that a particular IP address is actively exposed to the public internet and is potentially accessible to unauthorized individuals. This finding is considered a potential security risk because it could allow attackers to scan for vulnerabilities or launch attacks on the associated resource.
- **Cloud Storage buckets should not be anonymously or publicly accessible:** This high severity finding indicates that there is an Access Control List (ACL) entry for the storage bucket that is publicly accessible which means that anyone on the internet can read files stored in the bucket. This is a high-risk security vulnerability that needs to be prioritized for remediation.
- **Instances should not be configured to use the default service account with full access to all Cloud APIs:** This medium severity finding indicates that a particular identity or service account has been granted full access to all Google Cloud APIs. This finding is considered a significant security risk because it grants the identity or service account the ability to perform any action within the Google Cloud environment, including accessing sensitive data, modifying configurations, and deleting resources.

Since we're focusing on identifying and remediating the issues related to the security incident, please disregard the following findings as they do not relate to the remediation tasks:

- **VPC Flow logs should be Enabled for every subnet VPC Network:** There are a number of low severity findings for Flow Logs disabled. This indicates that Flow Logs are not enabled for a number of subnetworks in the Google Cloud project used for this lab. This is a potential security risk because Flow Logs provide valuable insights into network traffic patterns, which can help identify suspicious activity and investigate security incidents.
- **Basic roles (Owner, Writer, Reader) are too permissive and should not be used:** This medium severity finding indicates that primitive roles are being used within the Google Cloud environment. This is a potential security risk because primitive roles grant broad access to a wide range of resources.
- **An egress deny rule should be set:** This low severity finding indicates that no egress deny rule is defined for the monitored firewall. This finding raises potential security concerns because it suggests that outbound traffic is not restricted, potentially exposing sensitive data or allowing unauthorized communication.

The following table pairs the rules listed in the report with their corresponding findings category:

| Findings category         | Rule                                                                                       |
|---------------------------|--------------------------------------------------------------------------------------------|
| Firewall rule logging disabled | Firewall rule logging should be enabled so you can audit network access                    |
| Open RDP port             | Firewall rules should not allow connections from all IP addresses on TCP or UDP port 3389  |
| Open SSH port             | Firewall rules should not allow connections from all IP addresses on TCP or SCTP port 22   |
| Public IP address         | VMs should not be assigned public IP addresses                                             |
| Public bucket ACL         | Cloud Storage buckets should not be anonymously or publicly accessible                     |
| Full API access           | Instances should not be configured to use the default service account with full access to all Cloud APIs |
| Flow logs disabled        | VPC Flow logs should be Enabled for every subnet VPC Network                               |
| Primitive roles used      | Basic roles (Owner, Writer, Reader) are too permissive and should not be used              |
| Egress deny rule not set  | An egress deny rule should be set                                                          |

**Note:** overall, these findings indicate a critical lack of security controls and non-compliance with essential PCI DSS requirements; they also point to the vulnerabilities associated with the data breach.

**Next**, navigate to the Security Command Center, and filter the findings for further examination and analysis of the vulnerabilities in the Google Cloud environment.

Security Command Center - Findings:
![18 - Filter - Compute instance and Storage bucket](https://github.com/user-attachments/assets/c02f2a57-f17c-4cfb-8fee-556ea539ff20)

The following active findings pertaining to the storage bucket should be listed:

- **Public bucket ACL:** This finding is listed in the PCI DSS report, and indicates that anyone with access to the internet can read the data stored in the bucket.
- **Bucket policy only disabled:** This indicates that there is no explicit bucket policy in place to control who can access the data in the bucket.
- **Bucket logging disabled:** This indicates that there is no logging enabled for the bucket, so it will be difficult to track who is accessing the data.
- **Malware bad domain:** This finding indicates that a domain known to be associated with malware was accessed from the google.compute.instance named cc-app-01. Although this finding is considered to be of low severity, it indicates that malicious activity has occurred on the virtual machine instance and that it has been compromised.
- **Compute secure boot disabled:** This medium severity finding indicates that secure boot is disabled for the virtual machine. This is a security risk as it allows the virtual machine to boot with unauthorized code, which could be used to compromise the system.
- **Default service account used:** This medium severity finding indicates that the virtual machine is using the default service account. This is a security risk as the default service account has a high level of access and could be compromised if an attacker gains access to the project.
- **Public IP address:** This high severity finding is listed in the PCI DSS report and indicates that the virtual machine has a public IP address. This is a security risk as it allows anyone on the internet to connect to the virtual machine directly.
- **Full API access:** This medium severity finding is listed in the PCI DSS report, and indicates that the virtual machine has been granted full access to all Google Cloud APIs.

These findings indicate the virtual machine was configured in a way that left it very vulnerable to the attack. To remediate these findings you'll shut the original VM (cc-app-01) down, and create a VM (cc-app-02) using a clean snapshot of the disk. The new VM will have the following settings in place:

- No compute service account
- Firewall rule tag for a new rule for controlled SSH access
- Secure boot enabled
- Public IP address set to None

Security Command Center - Findings - Vulnerabilities associated to Google Compute Firewall:
![19 - Filter - Compute firewall](https://github.com/user-attachments/assets/270cfa08-d4e7-4e86-a52e-2cca5061589b)

The following active findings should be listed that pertain to the firewall:

- **Open SSH port:** This high severity finding indicates that the firewall is configured to allow Secure Shell (SSH) traffic to all instances in the network from the whole internet.
- **Open RDP port:** This high severity finding indicates that the firewall is configured to allow Remote Desktop Protocol (RDP) traffic to all instances in the network from the whole internet.
- **Firewall rule logging disabled:** This medium severity finding indicates that firewall rule logging is disabled. This means that there is no record of which firewall rules are being applied and what traffic is being allowed or denied.

## Task 2. Fix the Compute Engine vulnerabilities
In this task, you'll shut down the vulnerable VM cc-app-01, and create a new VM from a snapshot taken before the malware infection. VM snapshots are effective in restoring the system to a clean state, and ensures that the new VM will not be infected with the same malware that compromised the original VM.

Compute Engine - VM Instances Dashboard:
![20 - Compute Engine VM Instances cc-app-01](https://github.com/user-attachments/assets/17d4076b-1d85-4a21-802e-c59ce0525498)

### Infected VM Stop
Compute Engine - VM Instances Dashboard - Stopping infected VM:
![22 - Compute Engine VM Instances cc-app-01 - Stop action](https://github.com/user-attachments/assets/fc760b62-2af8-4683-be16-ea388917c2ea)

Compute Engine - VM Instances Dashboard - Stop confirmation:
![23 - Compute Engine VM Instances cc-app-01 - Stop confirmation](https://github.com/user-attachments/assets/361bdabb-8090-4fed-afca-f102b8fe2665)

### New VM Creation
Compute Engine - New VM Instance - Configuration - VM Name, Region and Zone, and Machine type:
![24 - Compute Engine VM Instances cc-app-02 - Snapshot creation](https://github.com/user-attachments/assets/fa9ea463-2442-417e-8034-ed86f5e562e5)

Compute Engine - New VM Instance - Configuration - Machine configuration policies:
![25 - Compute Engine VM Instances cc-app-02 - Snapshot creation](https://github.com/user-attachments/assets/8e3bded4-52d8-4127-9177-84444a9594e3)

Compute Engine - New VM Instance - Configuration - Boot Disk:
![26 - Compute Engine VM Instances cc-app-02 - Snapshot creation - Snapshot  source selection](https://github.com/user-attachments/assets/45ced93a-255f-451d-b03b-d8f012972676)

Compute Engine - New VM Instance - Configuration - Identity API access:
![27 - Compute Engine VM Instances cc-app-02 - Snapshot creation - Service Account configuration](https://github.com/user-attachments/assets/af8b2831-5a7d-4e3b-8dac-925111733db7)

Compute Engine - New VM Instance - Configuration - Networking:
![28 - Compute Engine VM Instances cc-app-02 - Networking configuration](https://github.com/user-attachments/assets/76732603-be09-43a9-b96b-26e6f74b5998)

Compute Engine - New VM Instance - Configuration - Networking - Network interfaces:
![29 - Compute Engine VM Instances cc-app-02 - Networking configuration](https://github.com/user-attachments/assets/695c6e45-421a-4358-93ac-8d22798150bf)

The new VM cc-app-02 should now be created from the cc-app01-snapshot. (It may take a few minutes for the new VM to be created.)
    
Compute Engine - VM Instances dashboard:
![30 -  Compute Engine VM Instances cc-app-02 - Instance created](https://github.com/user-attachments/assets/a8c83b14-10d9-4db8-9423-974e92fb1282)

### Secure Boot Enable
Now, turn Secure Boot on for the new VM cc-app-02 to address the Secure Boot disabled finding.

Compute Engine - VM Instances - Edit cc-app-02 - Security and access:
![34 - Compute Engine VM Instances cc-app-02 - Secure Boot configuration](https://github.com/user-attachments/assets/3b7edeb7-ea88-4e80-8e21-1da2fe42c2cd)

The cc-app-02 VM instance will restart and the Secure Boot disabled finding will be remediated.

### Restart new VM cc-app-02:
Compute Engine - VM Instances - Start/Resume cc-app-02 instance:
![35 - Compute Engine VM Instances cc-app-02 - Instance restart](https://github.com/user-attachments/assets/b1491d4b-7bfa-40f5-9870-3bdad63cfe57)

Compute Engine - VM Instances - cc-app-02 instance running confirmation:
![36 - Compute Engine VM Instances cc-app-02 - Instance restart](https://github.com/user-attachments/assets/089f0d43-2709-4025-8079-104837004c3e)

### Compromised VM Deletion
Delete the compromised VM cc-app-01.

Compute Engine - VM Instances - cc-app-01 instance deletion confirmation:
![37 - Compute Engine VM Instances cc-app-01 - Instance deletion](https://github.com/user-attachments/assets/33464faa-c299-4650-a8fd-a45a23cb2408)

By following these steps, you have effectively created a new VM from the snapshot, ensuring it is free from malware and misconfigurations. You also deleted the compromised VM, eliminating the source of the security breach.

## Task 3. Fix Cloud Storage bucket permissions
In this task, you'll revoke public access to the storage bucket and switch to uniform bucket-level access control, significantly reducing the risk of data breaches. By removing all user permissions from the storage bucket, you can prevent unauthorized access to the data stored within.

### Cloud Storage - Bucket Overview
Cloud Storage - Buckets dashboard:
![38 - Cloud Storage](https://github.com/user-attachments/assets/33257199-cc4a-431b-ba54-3c0be7476c71)

Cloud Storage - Buckets - Bucket details:
![39 - Cloud Storage - Bucket](https://github.com/user-attachments/assets/25e33a6a-8cb8-4fe2-b4d1-ddb82981bd24)

You'll note there is a myfile.csv file in the publicly accessible bucket. This is the file that contains the sensitive information that was dumped by the malicious actor. Perform the following steps to address the Public bucket ACL finding.

**Modify storage bucket access:** Switch the access control to uniform and remove permissions for the allUsers principals from the storage bucket to enforce a single set of permissions for the bucket and its objects. You'll also need to ensure that users who rely on basic project roles to access the bucket won't lose their access.

### Pevent Public Access
Cloud Storage - Bucket - Disable Public Access confirmation dialog:
![42 - Cloud Storage - Bucket - Prevent Public Access](https://github.com/user-attachments/assets/adaed1b4-97ab-42d6-b76f-bc5fd987f679)

Cloud Storage - Bucket - Successful Public Access Policy disabled notification:
![43 - Cloud Storage - Bucket - Prevent Public Access - Confirmation](https://github.com/user-attachments/assets/809b2b94-9ad8-4669-9093-2a91985f0bf6)

### Set Uniform Access Control
Cloud Storage - Bucket - Access Control Edit form:
![46 - Cloud Storage - Bucket - Uniform Access Control](https://github.com/user-attachments/assets/8b3cfd94-7f12-4126-8818-ef43a8008c98)

### Remove All-Users Permissions
Removing All-Users permissions:
![47 - Cloud Storage - Bucket -  Policy - All User  Permission Removal](https://github.com/user-attachments/assets/a1fa3491-624b-498c-a2c5-059a26734f77)

By following these steps, you have effectively prevented public access to the bucket, switched to uniform bucket-level access control, and removed all user permissions, addressing the Public bucket ACL, Bucket policy only disabled, and Bucket logging disabled findings.

## Task 4. Limit firewall ports access
In this task, you'll restrict access to RDP and SSH ports to only authorized source networks to minimize the attack surface and reduce the risk of unauthorized remote access.

Exercise extreme caution before modifying overly permissive firewall rules. The rules may be allowing legitimate traffic, and improperly restricting it could disrupt critical operations. In this lab, ensure the Compute Engine virtual machine instances tagged with target tag "cc" remain accessible via SSH connections from the Google Cloud Identity-Aware Proxy address range (35.235.240.0/20). To maintain uninterrupted management access, create a new, limited-access firewall rule for SSH traffic before removing the existing rule allowing SSH connections from any address.

### Restrict SSH access
Create a new firewall rule. This rule must restrict SSH access to only authorized IP addresses from the source network 35.235.240.0/20 to compute instances with the target tag cc.

Network Security - Firewall Policies - VPC Firewall Rules - New Firewall Rule summary:
![55 - Firewall - New Firewall Rule](https://github.com/user-attachments/assets/fcbd3be0-10d3-4ded-85bc-4001315dd046)

Network Security - Firewall Policies - VPC Firewall Rules - New Firewall Rule creation notification:
![56 - Firewall - New Firewall Rule](https://github.com/user-attachments/assets/8c8ca76f-84e6-4bcf-a439-9f012637ba3e)

## Task 5. Fix the firewall configuration
In this task, you'll delete three specific VPC firewall rules that are responsible for allowing unrestricted access to certain network protocols, namely ICMP, RDP, and SSH, from any source within the VPC network. Then, you'll enable logging on the remaining firewall rules.

### Customize firewall rules
Delete the default-allow-icmp, default-allow-rdp, and default-allow-ssh firewall rules. These rules are overly broad and by deleting them, you'll allow for a more secure and controlled network environment.

Network Security - Firewall Policies - VPC Firewall Rules - Rule default-allow-ssh deletion dialog:
![57 - Firewall Rules - Delete default-allow-ssh](https://github.com/user-attachments/assets/94212e24-fe41-46aa-b8a7-0104bbacd69e)

Network Security - Firewall Policies - VPC Firewall Rules - Rule list panel after default-allow-icmp, default-allow-rdp, and default-allow-ssh firewall rules deletion:
![59 - Firewall Rules - After Deletion](https://github.com/user-attachments/assets/bbafac58-9730-4b3d-aa6a-c58535fc3d37)

By deleting these rules, you have restricted access to these protocols, limiting the potential for unauthorized access attempts and reducing the attack surface of your network.

### Enable logging
Enable logging for the remaining firewall rules limit-ports (the rule you created in a previous task) and default-allow-internal.

Network Security - Firewall Policies - VPC Firewall Rules - Rule list panel with **Logs - On** for both rules:
![60 - Firewall - Logs Enabled](https://github.com/user-attachments/assets/8bfea7f8-42b4-481a-8681-98cace4c217a)

Enabling logging allows you to track and analyze the traffic that is allowed by this rule, which is likely to be internal traffic between instances within your VPC.

## Task 6. Verify compliance
After diligently addressing the vulnerabilities identified in the PCI DSS 3.2.1 report, it's crucial to verify the effectiveness of your remediation efforts. In this task, you'll run the report again to ensure that the previously identified vulnerabilities have been successfully mitigated and no longer pose a security risk to the environment.

Security Command Center - PCI DSS report after remediation:
![61 - PCI DSS Report - After Remediation](https://github.com/user-attachments/assets/bf8a0cdb-824e-4a1d-934c-593ad158700a)

All major vulnerabilities are now resolved.

## Conclusion
You have helped the security team at Cymbal Bank to mitigate the impact of the data breach, address the identified vulnerabilities, and significantly enhanced the security posture of Cymbal Bank’s Google Cloud environment.

First, you examined and analyzed the vulnerabilities and findings in Google Cloud Security Command Centre.

Next, you shut the old VM down and created a new VM from a snapshot taken before the malware infection.

Then, you fixed the cloud storage permissions by revoking public access to the storage bucket and switching to uniform bucket-level access control. You also removed all user permissions from the storage bucket.

Next, you fixed the firewall rules by deleting the default-allow-icmp, default-allow-rdp, and default-allow-ssh firewall rules, and enabling logging for the remaining firewall rules.

Finally, you run a compliance report to confirm that the vulnerability issues have been remediated.
