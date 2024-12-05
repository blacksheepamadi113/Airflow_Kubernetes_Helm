# Airflow_Kubernetes_Helm Intro
A repository for deploying data pipelines on apache airflow , using terraform, kubernetes and helm.

# ARCHITECTURE
![DEVOPS1](https://github.com/user-attachments/assets/5ecd18cc-7cd5-4477-b588-d37433bd7c6a)

The solution involves the use of two Virtual Private Clouds (VPCs) to facilitate communication between different components. In one VPC, we have an Amazon Elastic Kubernetes Service (EKS) cluster and EKS node groups, while the other VPC is dedicated to hosting Amazon RDS database instances and a DB subnet group.

The RDS instances are positioned within a public subnet, but their security settings are configured to only allow incoming connections from the EKS node groups located in the EKS VPC. This means that the RDS instances do not accept traffic from any other sources, ensuring a high level of security.

Within the EKS VPC, the application's pods run in the EKS node group. To provide external access to this application, an internet-facing load balancer is employed. This load balancer acts as the gateway for users to access the application, while the RDS database instances remain protected and isolated, only accessible to the EKS node groups.

The EKS node groups also leverage EBS CSI drivers to enable dynamic provisioning of persistent volumes for the pods. These drivers allow for the automatic creation and management of EBS volumes, ensuring that each pod can have its own storage resources when needed. This dynamic provisioning of persistent volumes enhances the flexibility and scalability of the EKS cluster's storage infrastructure, providing efficient and on-demand storage resources for the applications running on the EKS node groups.

# DAG RUN
The default Helm chart was customized by overriding its configuration with a custom Helm chart specified in the helm/values.yaml file. Within this custom configuration, we provided Airflow with information about the location of its Directed Acyclic Graphs (DAGs). These DAGs are managed using a Git Sync process, and they are stored in the dags folder. This customization ensures that Airflow is aware of where to find and sync its workflow definitions from the specified Git repository.
I also deployed a sample DAG which consists of a DummyOperator and a BashOperator. The execution of this dag inside the Airflow Cluster is shown below.
![DEVOPAIRFLOW](https://github.com/user-attachments/assets/05cfff84-56a5-478b-ba14-6ac3354b05af)
The dag was being accessed via a load balancer service deployed in the EKS cluster.

# PODS AND SERVICES
There were a number of pods and services deployed in the Kubernetes cluster. The webserver , triggerer, scheduler, pgbouncer, stastd, Redis, worker, EBS CSI driver, amongst others were deployed. Webserver was used to provide a web-based user interface to interact with and manage Airflow. Users can use the webserver to view DAGs, trigger and monitor task execution, and manage Airflow configurations. Triggerer is a part of Airflow's scheduler that allows for the scheduling of workflows (DAGs) in response to external events. The scheduler in Airflow is responsible for orchestrating the execution of tasks within DAGs at specified times and intervals. PG Bouncer was used as a light weight proxy for the RDS instance deployed in the RDS VPC. Redis is an in-memory data store that is often used as a caching layer or message broker. It can be used for tasks like storing Airflow metadata, managing task queues, and more. StatsD is a network daemon that listens for statistics, like counters and timers, sent over UDP or TCP. Workers are responsible for executing the tasks defined in DAGs. The EBS (Elastic Block Store) Container Storage Interface (CSI) driver is responsible for managing the dynamic provisioning of EBS volumes and their attachment to pods in the cluster. It ensures that persistent storage resources are available for applications running in Kubernetes.

# BOTTLENECKS
The bottleneck is that on first run, the workflow would have to be run twice. The first workflow fails because the eks cluster has not been created with Terraform Apply but subsequent workflows succeed. Improvements would be made to this, going forward.

# CONCLUSION
Thank you for taking this journey with me. Please don't hesitate to reach out if you have any questions or need further assistance.

