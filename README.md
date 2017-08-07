# Single CloudFormation Template to setup:
# 1. VPC, Subnet and its associate resources,
# 2. A ELB attached to auto-scaling group with 2 web servers(Private Subnets in different AZs), 
# 3. A BastionHost within another auto-scaling group in Public subnet using the same EIP created even when the server gets replaced. 
# 4. A S3 Bucket with PublicRead and GetObject,PutObject for the webservers.
# 5. An RDS instance with PostgreSQL within a Private Subnet.
# 6. Network traffic:
#    - web servers: allow ssh traffic from only the bastion host, allow http
#    - traffic from the load balancer.
#    - bastion: allow ssh connections from anywhere.
#    - load balancer: allow http traffic from anywhere.
#    - database: allow postgres traffic from web servers.
# 7. Outputs include:
#    - Bastion host’s Elastic IP.
#    - Load Balancer’s public DNS name.



