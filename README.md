# CloudFormation Template
Single CloudFormation Template to setup:
1. VPC, Subnet and its associate resources,
2. A ELB attached to auto-scaling group with 2 web servers(Private Subnets in different AZs), 
3. A BastionHost within another auto-scaling group in Public subnet using the same EIP created even when the server gets replaced. 
4. A S3 Bucket with PublicRead and GetObject,PutObject for the webservers.
5. An RDS instance with PostgreSQL within a Private Subnet.
6. Network traffic:
    - web servers: allow ssh traffic from only the bastion host, allow http
    - traffic from the load balancer.
    - bastion: allow ssh connections from anywhere.
    - load balancer: allow http traffic from anywhere.
    - database: allow postgres traffic from web servers.
7. Outputs include:
    - Bastion host’s Elastic IP.
    - Load Balancer’s public DNS name.
 
 
- The Cloudformation template has the parameters section- which includes all the required parameters to be entered from the user to creating resources. Default values have been set to all the parameters. Validations are added for entering the CIDR range as well.

- Mappings are used inorder to pick up the AMI based on the region. Only the US regions' AMIs are included as mentioned.

- Metadata: AWS::CloudFormation::Interface is used for grouping parameters as required.

- Resources:
  1. VPC, Subnets, Route tables, Natgateways, InternetGateways, Security groups are all created based on the order it is required using the "DependsOn" api.
  2. S3 Bucket with PublicRead is created.
  3. Bucket Policy is created and assigned to the above S3 bucket.
  4. To provide GetObject and PutObject to the WebServers, an IAM role is created for the Instance Profile and add the principal arn to the Bucket Policy. 
  5. Two launchconfigurations, one for WebServers and another one for BastionHost is created. The AMI for the launchconfiguration is obtained by the !FindInMap function, which in turn refers to the Mappings. Note: The Amazon Linux is the ami which is used in this case.
     a. WebServer LaunchConfiguration- Userdata for yum update and installation of HTTPD is added, postgresql client is also installed using the user-data. Note: Postgresql server was required inorder to install the client. Chkconfig is also enabled for the services installed, so that the services will be running after the reboot of instance as well.
     b. BastionHost LaunchConfiguration- Userdata for yum update and installation of wget, pip is also added. It is required to install and run the aws-ec2-assign-elastic-ip module. 
        aws-ec2-assign-elastic-ip  is the module which is used to pick up the latest EIP created and assigning it to the instance. EIPs are charged when it is created but not associated to any instance, Note: There should be only one EIP which is  associated to the instance within the Bastion auto-scaling group. The module would pick the same EIP in that case.
  6. To create Postgresql rds instance within the Subnet Group, a DBsubnet group is created and then the rds instance is created.
  7. For Auto-Scaling, the scaling policies and cloudwatch Alarms have been created. 
  
  
 Estimation/guesstimate 
 - link to the calculation using s3 calculator for the above resources:
 https://calculator.s3.amazonaws.com/index.html#r=IAD&key=
calc-8480D0D4-E8F8-40E3-A6F0-40B7A6829868
