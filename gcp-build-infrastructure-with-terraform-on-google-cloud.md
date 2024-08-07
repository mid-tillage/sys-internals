# Build Infrastructure with Terraform on Google Cloud

# Table of Contents

1. [Introduction](#introduction)
2. [Scenario](#scenario)
3. [Task 1: Create the configuration files](#task-1-create-the-configuration-files)
4. [Task 2: Import infrastructure](#task-2-import-infrastructure)
5. [Task 3: Configure a remote backend](#task-3-configure-a-remote-backend)
6. [Task 4: Modify and update infrastructure](#task-4-modify-and-update-infrastructure)
7. [Task 5: Destroy resources](#task-5-destroy-resources)
8. [Task 6: Use a module from the Registry](#task-6-use-a-module-from-the-registry)
9. [Task 7: Configure a firewall](#task-7-configure-a-firewall)
10. [Conclusion](#conclusion)


# Introduction
This is the Google Cloud Build Infrastructure with Terraform on Google Cloud challenge, provided by Google at [cloudskillsboost](https://www.cloudskillsboost.google/) as part of their learning course.

This project uses key concepts such as infrastructure as code (IaC), resource management, and automation to streamline the deployment and maintenance of cloud infrastructure. The challenge ainvolves using Terraform to define, provision, and manage Google Cloud Platform (GCP) resources, including virtual machines, storage buckets, and networking components.

**Important note:** <u>this project is captured from the course "Google Cloud Build Infrastructure with Terraform on Google Cloud" challenge lab. The project descriptions are from the challenge's lab itself. Screenshots added to enrich explanations and processes.</u>


## Scenario
You are a cloud engineer intern for a new startup. For your first project, your new boss has tasked you with creating infrastructure in a quick and efficient manner and generating a mechanism to keep track of it for future reference and changes. You have been directed to use Terraform to complete the project.

For this project, you will use Terraform to create, deploy, and keep track of infrastructure on the startup's preferred provider, Google Cloud. You will also need to import some mismanaged instances into your configuration and fix them.

In this lab, you will use Terraform to import and create multiple VM instances, a VPC network with two subnetworks, and a firewall rule for the VPC to allow connections between the two instances. You will also create a Cloud Storage bucket to host your remote backend.

Note: At the end of every section, plan and apply your changes to allow your work to be successfully verified. Since we will be updating many terraform files in this lab make sure to use the correct file path and maintain the correct indentation.

## Task 1. Create the configuration files
1. In Cloud Shell, create your Terraform configuration files and a directory structure that resembles the following:

```
main.tf
variables.tf
modules/
└── instances
    ├── instances.tf
    ├── outputs.tf
    └── variables.tf
└── storage
    ├── storage.tf
    ├── outputs.tf
    └── variables.tf
```

2. Fill out the variables.tf files in the root directory and within the modules. Add three variables to each file: region, zone, and project_id.
3. Add the Terraform block and the Google Provider to the main.tf file. Verify the zone argument is added along with the project and region arguments in the Google Provider block.

Cloud Shell - main.tf:
![01 - main tf](https://github.com/user-attachments/assets/6313bd97-9fcb-44cd-b77b-f6fbe96c7ce0)

Cloud Shell - variables.tf:
![02 - variables tf](https://github.com/user-attachments/assets/4cbe3357-8103-4a39-aef3-755188d14933)

Cloud Shell - modules/instances/variables.tf:
![03 - instances variables tf](https://github.com/user-attachments/assets/5f46a801-e138-4ff1-911d-7aabd6939a64)

Cloud Shell - modules/storage/variables.tf:
![04 - storage variables tf](https://github.com/user-attachments/assets/8a2d981e-4b82-42e5-b831-7769a5948083)

4. Initialize Terraform.

## Task 2. Import infrastructure
1. In the Google Cloud Console, on the Navigation menu, click Compute Engine > VM Instances. Two instances named tf-instance-1 and tf-instance-2 have already been created for you.

Compute engine - VM Instances:
![05 - vm instances](https://github.com/user-attachments/assets/608d9710-a74b-4fbf-b496-0193b1bd5b04)

2. Import the existing instances into the instances module. To do this, you will need to follow these steps:
- First, add the module reference into the main.tf file then re-initialize Terraform.
- Next, write the resource configurations in the instances.tf file to match the pre-existing instances.
  - Name your instances tf-instance-1 and tf-instance-2.
  - For the purposes of this lab, the resource configuration should be as minimal as possible. To accomplish this, you will only need to include the following additional arguments in your configuration: machine_type, boot_disk, network_interface, metadata_startup_script, and allow_stopping_for_update. For the last two arguments, use the following configuration as this will ensure you won't need to recreate it:

```
metadata_startup_script = <<-EOT
        #!/bin/bash
    EOT
allow_stopping_for_update = true
```
Cloud Shell - modules/instances/instances.tf - Instance 1:
![06 - instances tf](https://github.com/user-attachments/assets/d70fbf91-2c38-4979-bb71-88dfaa1bfc5d)

Cloud Shell - modules/instances/instances.tf - Instance 2:
![07 - instances tf](https://github.com/user-attachments/assets/2e2ed417-434f-4708-80a6-8c24b5f98788)

  - Once you have written the resource configurations within the module, use the terraform import command to import them into your instances module.

![08 - terraform init and plan](https://github.com/user-attachments/assets/9e5b6274-71d0-43d0-9dc9-76e21d0406e2)

3. Apply your changes. Note that since you did not fill out all of the arguments in the entire configuration, the apply will update the instances in-place. This is fine for lab purposes, but in a production environment, you should make sure to fill out all of the arguments correctly before importing.

![09 - terraform apply](https://github.com/user-attachments/assets/70b32183-0734-45d7-a8e5-27c53d61243b)

![10 - terraform apply](https://github.com/user-attachments/assets/2dd7227b-48ec-4e4a-937a-76a293f75051)

----

**Side note:** As seen at the begginnig of the lab, the used version of hashicorp/google terraform was 3.5. Here we update it to 4.0:

Cloud Shell - terraform version update - main.tf:
![11 - updated terraform version](https://github.com/user-attachments/assets/9bccbe6b-8faa-4399-b1e6-c0969d93e276)

Cloud Shell - terraform init upgrade:
![12 - updated terraform version](https://github.com/user-attachments/assets/f5c04487-30be-464e-8f49-09058571c6b8)

## Task 3. Configure a remote backend
1. Create a Cloud Storage bucket resource inside the storage module.
2. Add the module reference to the main.tf file. Initialize the module and apply the changes to create the bucket using Terraform.

Cloud Shell - remote backend - main.tf:
![13 - remote backend](https://github.com/user-attachments/assets/cd8804b3-351d-4f48-a8e8-5b4fe67f0754)

Cloud Shell - terraform init and plan:
![14 - remote backend - init and plan](https://github.com/user-attachments/assets/a592c43a-89c5-4560-ada9-be33c3b3d442)

![15 - remote backend - plan](https://github.com/user-attachments/assets/958e67ae-9ec6-452e-9ec1-fde62764f244)

Cloud Shell - terraform apply:
![16 - remote backend - apply](https://github.com/user-attachments/assets/e0b24625-669e-4a85-9464-42a1b18162b4)

![17 - remote backend - apply](https://github.com/user-attachments/assets/cd16aaca-c111-483f-aeb4-4f286d2da631)

3. Configure this storage bucket as the remote backend inside the main.tf file. Be sure to use the prefix terraform/state so it can be graded successfully.
4. If you've written the configuration correctly, upon init, Terraform will ask whether you want to copy the existing state data to the new backend. Type yes at the prompt.

Cloud Shell - terraform init:
![18 - remote backend - gcs bucket - init](https://github.com/user-attachments/assets/e34bcc80-bd1e-473b-ace5-9807e811e836)

## Task 4. Modify and update infrastructure
1. Navigate to the instances module and modify the tf-instance-1 resource to use an e2-standard-2 machine type.
2. Modify the tf-instance-2 resource to use an e2-standard-2 machine type.
3. Add a third instance resource and name it Instance Name. For this third resource, use an e2-standard-2 machine type. Make sure to change the machine type to e2-standard-2 to all the three instances.

Cloud Shell - Modify instances machine type
![19 - update infrastructure - modify instances machine type](https://github.com/user-attachments/assets/e4fe2c5f-bda0-48ee-b630-ed29d93e1d7c)

Cloud Shell - Add new instance:
![20 - update infrastructure - new instance](https://github.com/user-attachments/assets/da9fd202-3b4b-48a7-bf98-c177d55f6a70)

5. Initialize Terraform and apply your changes.

Cloud Shell - Terraform plan and apply:
![21 - terraform plan](https://github.com/user-attachments/assets/0f1b5f18-8fea-4f80-a389-b96bbb170993)

![22 - terraform plan](https://github.com/user-attachments/assets/b95e9790-56e9-4f18-8941-91da6abaa0d4)

![23 - terraform apply](https://github.com/user-attachments/assets/40631c75-cc13-4052-a3d7-432bd5d12055)

![24 - terraform apply](https://github.com/user-attachments/assets/839f1e24-9acb-40c9-acbd-d1ad05dc27bc)

## Task 5. Destroy resources
Destroy the third instance by removing the resource from the configuration file. After removing it, initialize terraform and apply the changes.

Cloud Shell - Third instance removal - main.tf:
![25 - destroy third instance](https://github.com/user-attachments/assets/d63e6ca9-9d15-47d7-a463-265ac0fe0108)

Cloud Shell - Terraform plan and apply:
![26 - terraform plan](https://github.com/user-attachments/assets/e0cb67c0-39c3-4663-b0cd-13d63e51737f)

![27 - terraform plan](https://github.com/user-attachments/assets/7da1afd4-38d1-44f0-b2bc-b3a80ebfa5da)

![28 - terraform apply](https://github.com/user-attachments/assets/419ee296-9644-432d-9c11-f0b4ef526afa)

![29 - terraform apply](https://github.com/user-attachments/assets/8e0e6d79-245f-4ffb-940c-90f7f32989df)

## Task 6. Use a module from the Registry
1. In the Terraform Registry, browse to the Network Module.
2. Add this module to your main.tf file. Use the following configurations:
- Use version 6.0.0 (different versions might cause compatibility errors).
- Name the VPC, and use a global routing mode.
- Specify 2 subnets in the region, and name them subnet-01 and subnet-02. For the subnets arguments, you just need the Name, IP, and Region.
- Use the IP 10.10.10.0/24 for subnet-01, and 10.10.20.0/24 for subnet-02.
- You do not need any secondary ranges or routes associated with this VPC, so you can omit them from the configuration.

Cloud Shell - Add network module - main.tf:
![30 - network module](https://github.com/user-attachments/assets/f18da1ff-faab-44de-8aa2-44d7a2a42beb)

3. Once you've written the module configuration, initialize Terraform and run an apply to create the networks.

Cloud Shell - Terraform init, plan and apply:
![31 - terraform init](https://github.com/user-attachments/assets/d63179f0-bcf9-46b2-93ec-bd2639631d35)

![32 - terraform plan](https://github.com/user-attachments/assets/ff78cbdf-c11c-43a2-9d8c-730a007da974)

![33 - terraform plan](https://github.com/user-attachments/assets/e82751dd-770e-43d5-a202-28c749b1d10d)

![34 - terraform apply](https://github.com/user-attachments/assets/47f2db00-1256-446d-add5-b28d0146c8de)

5. Next, navigate to the instances.tf file and update the configuration resources to connect tf-instance-1 to subnet-01 and tf-instance-2 to subnet-02.

Cloud Shell - Connect network to instance - instances.tf
![35 - instances - update network interface](https://github.com/user-attachments/assets/ad0728fc-3a91-433c-b414-421df6bf4d38)

![36 - instances - update network interface](https://github.com/user-attachments/assets/f973c374-3db2-433d-98be-bfe3e852b22f)

Cloud Shell - Terraform plan and apply:
![37 - terraform plan](https://github.com/user-attachments/assets/d2775b2f-d1a5-435b-98ca-b557462f8bc7)

![38 - terraform apply](https://github.com/user-attachments/assets/fe392508-ad6b-435c-928d-1fc5f191c12e)

## Task 7. Configure a firewall
1. Create a firewall rule resource in the main.tf file, and name it tf-firewall.
- This firewall rule should permit the VPC Name network to allow ingress connections on all IP ranges (0.0.0.0/0) on TCP port 80.
- Make sure you add the source_ranges argument with the correct IP range (0.0.0.0/0).
- Initialize Terraform and apply your changes.

Cloud Shell - Firewall - main.tf
![39 - firewall](https://github.com/user-attachments/assets/a6e5be91-3c85-4275-91c0-ce9fa47465d8)

Cloud Shell - Terraform init, plan, and apply:
![40 - terraform init](https://github.com/user-attachments/assets/5ee74065-05bf-4f56-8d98-17091126fca1)

![41 - terraform plan](https://github.com/user-attachments/assets/66aefa95-2b98-4c9d-ab65-45d450bc8136)

![42 - terraform apply](https://github.com/user-attachments/assets/567a76ab-d624-4d6c-a341-7a67c2ac198b)

# Conclusion
The "Build Infrastructure with Terraform on Google Cloud" challenge provided a comprehensive introduction to using Terraform for managing Google Cloud Platform (GCP) resources. By leveraging key concepts such as infrastructure as code (IaC), resource management, and automation, the challenge demonstrated efficient methods for deploying and maintaining cloud infrastructure.

Throughout the project, various Terraform capabilities were explored, including the creation and management of virtual machines, networks, and storage resources. The use of modules facilitated the organization and reuse of configurations, while state management ensured consistency and accuracy in infrastructure deployment. Additionally, the integration of a remote backend with Google Cloud Storage highlighted best practices for managing Terraform state securely and reliably.

This hands-on experience emphasized the importance of automation in modern cloud infrastructure, enabling quick responses to changing requirements and minimizing manual intervention. The challenge underscored the value of using Terraform to achieve scalable, repeatable, and transparent infrastructure management, making it an essential tool for any cloud engineer.

Through practical exercises, this project solidified foundational knowledge and skills necessary for managing cloud infrastructure with Terraform, preparing participants for real-world scenarios in cloud engineering and DevOps roles.


