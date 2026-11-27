11/25/2026

Security:
CloudHSM (Cloud Hardware Security Module) - all the keys are stored in the HSM on the device and they never leave the device. Basically a centralized key management on your actual computer. AWS hosts the HSM in the cloud though. You can install the HSM client on your devices. 
Certificate Manager - this passes the certificates in the Private Certificate Authority to your services. Its like a literal server. It serves the certificates. These are for certificates that will be used for the internet. 
Private Certificate Authority - think of this like the root CA. The Certificate Manager interacts with it to hand certificates to the servers. These are for tickets that will be used internally (private). 
Secrets Manager - basically the same as Azure Key Vault. 

CloudTrail - it's an auditing system for APIs so it can track what users are doing. You can store the audits in S3 or CloudWatch. 

GuardDuty - uses AI and ML to analyze logs for security threats. It looks for suspicious activity. It gives the event a severity score as well. You can also give GuardDuty a trusted IP list (whitelist) and a threat IP list. 

IAM - this is all authentication and authorization. 
- Each person in your organization, you have to create an IAM User for them and assign them access.
- Groups are like users. Just, a group of them.
- The policy tells each IAM User what they'll have access to. Here's the format. 1.) You have your action which is Allow or Deny. 2.) You have what your allowed (or denied) to do. So think read or list an S3 bucket or write to an EC2 instance. It's the explicit definition of WHAT you're allowed to do. 3.) Finally, this says WHAT RESOURCE you're allowed to perform (or denied action) against. So think the actual resource (ARN which is the same thing as an Azure ID) or least of devices.
- Roles - this is when a user or resource needs temporary access (basically temp elevated permissions) to perform an operations. Remember, you can apply this to resources like EC2 instances too. So 1.) create the role. 2.) Create the policy (Allow, what action, on which resource) for the role and attach it to the role. 3.) Attach the role to the user or resource (Service: STS, Actions: AssumeRole, Resources: <insert-name-of-the-role-you-created>). Then, for your specific resource (lets say the EC2 instance), find your resource and click the dropdown button in whatever it's regular window is in (so not the IAM page). Click Security > Modify IAM role.

IAM Identity Center (SSO) - it's SSO so you can have your IAM user access across different AWS accounts (think management groups and subscriptions in Azure and having SSO across all of them). 

11/21/2026

Security:

Cognito - offers authentication at the APPLICATION layer. Think YouTube and stuff, not AWS resources. 
Directory Service - AWS's version of Microsoft's Active Directory. Just on AWS. You can actually link this with your on-prem AD. 
Verified Permissions - what the hell is this used for? Just ask AI. This was confusing. 
Security Lake - collects logs from all sources in the cloud and on-prem so you can have them all in one location. They're stored in an S3 bucket. Then you can use other AWS services to run queries against it. 
WAF (web application firewall) - they use WebACLs in AWS to create rules. Remember, this is all L7. 
Shield and Shield Advanced - it protects your AWS service from DDoS attacks. Probably will never use this. 
Network Firewall - you have to deploy endpoints in the subnets. But yeah it's like, NGFW just in AWS. Has it's own firewall subnet as well. Must configure the route table for this as well. 
Firewall Manager - used to manage firewalls across different ACCOUNTS. Basically the FMC for AWS. 

Config - it allows you to traffic the configurations of your services in AWS. Good for security and compliance policies. Also good for auditing. (Use Terraform instead of this. It tracks drifting but forget this ish). Also gives you pre-config rules to help you out.
Artifacts - you can see services that are compliant and download the reports on it. Basically this is for audit reports. You can just download them. Think giving this information to the government or a client. 
Inspector - automatically scans workloads for vulnerabilities (EC2, ECS, and Lambda). Finds Package, Code, and Network vulnerabilities. 
Macie - this is for Personal Identifiable Information (PII) (think credit cards, SSN, etc). Macie scans your S3 buckets to make sure it doesn't contain this type of info and alerts you. 
Security Hub - this is also a centralized dashboard of your security findings. It also does compliance checks. 
KMS (Key Management Services) - manages the encryption keys between your services. Rotation, deletion, etc. 
CloudHSM - 



11/20/2026

Storage:
Elastic Block Storage - both mountable and bootable. 
- They're separate from the EC2 instance so you can detach them.
- They're resilient within an AZ. Have to be in the same AZ as your EC2 instance too.
- To move storage from one AZ to another, create a snapshot (this should be in the actions dropdown list of the volume) (Create volume from snapshot) and then move it to a different region.

Instance Store Volumes - this is temporary storage for you EC2. It's a physical disk on the machine so if the EC2 is deleted, then you're smoked. 
Elastic File System (EFS) Storage - Uses NFSv4. You deploy EFS into subnets and they become Target Mounts. The EC2s can send data to these Target Mounts (the mounts are just an IP address in that specific subnet). 
- You need to use `dnf install amazon-efs-utils` to work with EFS from the CLI.

S3 is like Dropbox or Google Drive. Bucket - folder/directory. Objects go in the bucket. Objects are just files (any type of file. Audio, Vido, text, ANYTHING). The Key is the file name and the Value is the file data. Each object has these things. 
Versioning - you enable this on the bucket and it applies to all objects in the bucket. But this is for backups and making sure you don't overwrite files. When you upload a file, you get a version ID. If you upload the same file (or at least same Key), the new file will get a different version ID. That's it. 

11/19/2026

Compute:

AMI - Amazon Machine Image. Autoscaling groups - new EC2 instances will be deployed if your traffic increases. 
Cluster Placement Groups - when AWS places your servers in the same physicaly data center rack as one another for the lowest latency. You can also have Partition Placement Groups and Spread Placement Groups for memory and disaster recovery. 
Elastic Network Interface (ENI) - just remember you can detach these from EC2s just like in Azure. The primary ENI is always fixed to an EC2 instance though. It's the default one that's deployed to the VM. The secondary ENI can be detached. You can also attach SGs to the ENI as well. 

Elastic Beanstalk - this is like a PaaS. It handles all of the infrastructure deployment (VMs, networking, EC2s, storage, autoscaling, etc) so you can just upload your web application. It supports a bunch of different runtimes like Docker and PHP. 
AWS Elastic Beanstalk - Azure App Service

AWS Lightsail - this is like the EC2 instance launch wizard but even easier. Its the easiest way to spin up a VM for your application and have low billing for it. We'll never use this but it's here. Think of this when you don't want to deal with AWS networking which (I ain't scared of that shit). 

Elastic Container Services - handles deploying the container, monitoring, and container networking. Remember, this is specific to AWS so migrating efforts will be more difficult. Reasons why you may want to use EKS. 
- Tasks - so you'll create a Dockerfile and upload it to your repo. You then tell ECS what image you want to use and what configurations you need for your container. It's the blueprint that describes how containers should launch. It asks how much CPU/MEM and which images, ports, and volumes to attach to it. It's like a running container and that you're defining. You create the Task Definition which then becomes the Task. The Task is the running container. Task = container. Task Definition - the blueprint.

- ECS Services - ensures that a certain number of Tasks are running at all times. We tell the service how many instances of that task (container or Pod) that we want running. Think of this as the HPA. It restarts containers that have exited/crashed. It can also restart a task on a different EC2 instance if the initial VM failed.
- If you update your image and want to push that to your tasks and your ECS service, go to your Service and press Update. It should have a Force new deployment option. Check that box. Or, if you make a new Task definition, you'll have a new revision number. Make sure you update your service with the new revision number. The Task definition will stay the same here but you should have a drop down with the new revision number.

Elastic Kubernetes Services - basically AWS just manages the control plane (API Server, Scheduler, Controller Manager, etcd) but you have to handle the worker nodes. You have 3 options for worker nodes: Self-managed nodes (you manually deploy the EC2 instances), managed node group (AWS deploys the EC2 instances for you), and Fargate (AWS handles all compute for you). 

App Runner - you can just upload your code AWS will do the rest to make your application run. Think of you handing AWS a container though. You give AWS the container and it runs it for you. 

AWS Batch - when you have jobs or tasks of different sizes and times that need to be run. You can just give Batch a literal batch of jobs and AWS handles the underlying compute to make it happen. 



Networking:

Dont use the classic load balancer in AWS. Application Load Balancers are L7. In Elastic Load Balancers, you choose which VPC and AZ you want to send traffic to. Then, an LB node will be put in the subnet in that AZ that will load balance traffic. Cross-Zone Load Balancing allows LB Nodes to send traffic to backend pools regardless of what subnet and AZ the LB node is in. "Load Balancers in public subnets can forward requests to resources in private subnets." A listener listens for a specific field or piece of information. So basically it tells your LB what port to listen on and then you choose which target group to send it to. Then a target group is where you'll send the request (basically the associated backend pool compute). 

You can use VPNs to connect to your on-prem resources. You have to deploy a VPN Gateway on the AWS side and a Customer Gateway on the on-prem side. The Customer Gateway and VPN Gateway will both have a public IP address so yuo can create an IPsec tunnel between the two. You can use dynamic routing protocols for the routing (BGP). 

AWS Direct Connect = Azure ExpressRoute. AWS Privatelink = Azure Private Endpoint. AWS CloudFront = Azure Front Door or CDN. 

With VPC Peering, you do have to add routes to the routing table to get to another VPC's subnets. They aren't automatically added to the routing table like with Azure peering. So you have to generate the peering request, accept it, putting the routing in both VPC's routing tables, and then you should have connectivity. 

AWS CloudFront - the origin is the source location for content that will be cached by CloudFont. The content will be cached in the edge location. Distribution - configuration unit/block in CloudFront

Lambda@Edge - basically just having Azure Functions at an edge location for CloudFront. Don't worry about this. 
Global Accelerator - like CloudFront but your user is routed to the AWS backbone as quickly as possible so they can access applications in the AWS ecosystem faster. Not the same as caching at the edge. 
AWS Route 53 - managed DNS service. You can actually purchase domains here as well. You have your DNS records in your `hosted zoned`. Then you get 4 AWS DNS servers for that hosted zone that will take care of your DNS requests. 

11/18/2026

Networking:

VPC - virtual private cloud. Subnets, routing tables, firewalls, and gateways. They're specific to a specific region as well. VPCs can't talk to other VPCs by default (think VNet peering). 

You get a default VPC in every region you have available to you. AWS gives this to you so you can just set up a VM real quick. THey use the 172.31.0.0/16 CIDR. In each AZ, you get a default subnet that's a /20 so 172.31.32.0 /20 for example. 

You get a default IGW that points to 0.0.0.0/0. So by default the subnets have access to the internet. You also have a default SG and NACL. 

Subnets belong to one AZ and one AZ only. Anything resource that doesn't need to have network access, put it in a private subnet. If it needs internet or needs to talk to other resoruces, use public subnets. 

Subnets are private by default but you can change them to public. You have to attach an IGW to it. Then create a custom route table and point the destination traffic to the IGW. Then attach the route table to it. You also need to enable your server to have a public IP. 

NAT Gateway - use this instead of an IGW if you only want outbound access but no inbound internet access. IGW allows both directions for servers. NAT Gateway only goes one way. 

To use it, you need to create an IGW, attach to your VPC, and then make a public subnet with a default route pointing to your IGW, then deploy your NAT Gateway to that public subnet. The NAT Gateway is only for one subnet so there's no HA here. Keep that in mind. 

OMG that lab was long as hell. Just a bunch of VPC, subnets, IGWs, and NAT Gateways. Make sure you go over the NAT Gateway configuration one more time. 

VMs in subnets will be given a private subnet. They'll also get a domain name. To query the DNS server, your servers will send traffic to either 169.254.169.253 (AWS DNS server) or the second IP address in the subnet. That's why those first few IP addresses are reserved. Private subnets get DNS entries by default but public subnets don't. You need to enableDnsHostnames and enableDnsSupport (look into this more if you need to later. Most companies have a dedicated DNS server). Enable DNS resolution allows you to use the DNS server. 

Security Groups vs. NACLs - NACLS filter traffic entering and leaving a subnet. That don't filter traffic WITHIN a subnet (so any server in the same subnet can communicate with each other even if there is a NACL. NACLS are `STATELESS!!!!!`. Security Groups are stateful (so the normal stuff you do). Security Groups, however, are mini firewalls for individual resources (so not at the subnet level) (so like a specific EC2 instance or load balanacer). 
