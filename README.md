# Airflow_Kubernetes_Helm Intro
A repository for deploying data pipelines on apache airflow , using terraform, kubernetes and helm.

# ARCHITECTURE
![DEVOPS1](https://github.com/user-attachments/assets/5ecd18cc-7cd5-4477-b588-d37433bd7c6a)

The solution involves the use of two Virtual Private Clouds (VPCs) to facilitate communication between different components. In one VPC, we have an Amazon Elastic Kubernetes Service (EKS) cluster and EKS node groups, while the other VPC is dedicated to hosting Amazon RDS database instances and a DB subnet group.

The RDS instances are positioned within a public subnet, but their security settings are configured to only allow incoming connections from the EKS node groups located in the EKS VPC. This means that the RDS instances do not accept traffic from any other sources, ensuring a high level of security.

Within the EKS VPC, the application's pods run in the EKS node group. To provide external access to this application, an internet-facing load balancer is employed. This load balancer acts as the gateway for users to access the application, while the RDS database instances remain protected and isolated, only accessible to the EKS node groups.

The EKS node groups also leverage EBS CSI drivers to enable dynamic provisioning of persistent volumes for the pods. These drivers allow for the automatic creation and management of EBS volumes, ensuring that each pod can have its own storage resources when needed. This dynamic provisioning of persistent volumes enhances the flexibility and scalability of the EKS cluster's storage infrastructure, providing efficient and on-demand storage resources for the applications running on the EKS node groups.


