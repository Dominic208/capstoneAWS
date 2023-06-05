# capstoneAWS
Phase I
First thing for me to do is to create a VPC

Configuration settings:
	IPv4 CIDR Block-10.0.0.0/16
1 availability zone
1 public & private subnet
Subnet CIDR Block settings:
10.0.0.0/24
10.0.1.0/24
1 NAT gateway
No VPCs
DNS Hostnames-enabled
DNS Resolution-enabled

Additional setting configuration:
Adding 2 more subnets-public2 & private2
I will be using the second availability zone for each 
CIDR IPv4 Public-10.0.2.0/24
CIDR IPv4 Private-10.0.3.0/24
	Route Tables:
		Selecting lab-rtb-private1
		Edit subnet association and also select private2

		Selecting lab-rtb-public
		Edit subnet association and also select public2

		Save each of the settings

Creating security group settings:
	Create a security group
Choose the VPC that was created
Add an inbound rule 
HTTP
Anywhere IPv4





Launching an EC2:
	AMIs
Amazon Linux 
Amazon Linux 2023 AMI
	Instance type is t2.micro
	vockey key pair

	Edit network settings:
Network: vpc
Subnet: public2
Auto Assign public IP is enabled
	Firewall:
Select existing 
Common security groups I selected the one I created 
	Advanced details:
Scrolling to the bottom and placing the script in user data


*At first when I tried to deploy the EC2, it ended up not working so I had to create a new one then it worked*











Phase II 
Next I'll be needed to create load balancers so the web server won't be so slow.

		Creating an AMI:
In the EC2 instance I click actions>Image & templates>Create image. I'll name it and put in a description before I click create.
	
		Creating a load balancer:
	In target groups, I will choose a name and for my VPC I'll choose the one I made before. 

Once it's made, then I will create an application load balancer
	VPC: My VPC
	Availability Zone: 1st one
Subnet: public1
	Availability Zone: 2nd one
Subnet: public2

Security groups: Web Security
*Note I am removing the default selected*
Listener HTTP 80-Default action to forward to LabGroup

		Creating Launch Configurations:
		
		AMI: Web Server AMI
		Instance type: t2.micro 
		enable Monitoring
		Security group: Web Security Group
		Key pair: vockey
		*I'll need to check the box for the acknowledgement*

	In actions, I will click Auto Scaling group and configure:
		Network: My VPC
		Subnet: Private 1 and Private 2
	Load Balancing-optional pane: Attach to an existing load balancer-Select LabGroup
	Additional settings: enable group metrics collection within CloudWatch
	Group Size:
Desired capacity: 2
Minimum capacity: 2
Maximum capacity: 6
		Scaling policies:

Metric type: Average CPU Utilization
Target value: 60

Then I will be adding a tag
		Value: Lab Instance
	
To see if I did it right I then will check the instances tab and I see that there are 2 new instances named Lab Instance.

I then go to Target Groups>choosing LabGroup>Targets tab. I see 2 Lab Instance targets now. Clicking load balancers, I'll copy the DNS name (A Record). I see that it is working now

Next I test it in cloudwatch to see if it works:
In EC2 Ill click Auto Scaling Groups:
	enable Lab Auto Scaling Groups
	On the bottom I'll choose Automatic Scaling
	enable LabScalingPolicy
	Actions>Edit-Target Value: 50
	Click Update
	
Then I'll go to CloudWatch>click All alarms. I see that there are 2 alarms created


Returning to the web application browser, clicking Load Test I'll be able to generate high loads and see it in CloudWatch















Phase III
Next I'll need to make a launch template:
	
	Configuration:
		Guidance: Provide guidance
		AMI: Amazon Linux 2023 x86_64
		Type: t2.micro
		Key: vockey
		Subnet: None included
		Adv. Network: Add network interface
		Auto assign public IP: enable
		Security group: EC2
		Delete on Term: Yes
		
	Then I create an Auto Scaling group
	I click view>choose the link>actions>create auto scaling group
	
	Configuration:
		Launch Template: My template
		Version: Latest
		VPC: My VPC
		Subnets: Public1
	
	Next I'll go to my instance dashboard and select choose instances running.

		


















		Phase IV

I will now get to test a make sure everything is running correctly



Now as we can see it jumps from 2 Lab Instances>4 Lab Instances as traffic gets higher and then jumps back down to 2 Lab Instances as traffic decreases


