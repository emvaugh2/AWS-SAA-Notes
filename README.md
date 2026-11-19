


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
