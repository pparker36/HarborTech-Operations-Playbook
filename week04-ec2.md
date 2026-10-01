Week 4: EC2 Evidence Lab

Student: Pierre Parker

Ticket: TKT-2026-0004

Region: us-east-1


HarborTech Ticket Summary:
Riverside Goods had an EC2 web server that was running but could not be reached through its public IPv4 address. I used AWS CloudShell and Session Manager to check the instance, security group, Apache service, and webpage. The main issue was that the security group did not allow inbound HTTP traffic on TCP port 80. I added the missing rule, verified that the website returned HTTP/1.1 200 OK, completed a stop/start lifecycle test, and then cleaned up the lab resources.


Client Impact:
The client-facing impact was that the Riverside Goods website appeared unavailable because users could not reach it over HTTP. The EC2 instance itself was healthy, but the network access rule needed for port 80 was missing. Once I added the correct inbound rule, the webpage became reachable through the public IPv4 address.


Environment and Resource Names:
The lab was completed in the us-east-1 Region using VPC vpc-0923dc74fb93534cb and subnet subnet-09dee7c8ebb66cbb3 in us-east-1a. I used Amazon Linux AMI ami-0d27e0fb3bac4d724, instance type t3.micro, and the LabInstanceProfile. The working EC2 instance ID was i-05e01f2a2f320a124, and the working security group was sg-0cfdc2c1c0a998279. The initial public IPv4 address was 54.161.58.71, and after the stop/start lifecycle test the public IPv4 address changed to 54.144.68.201.


AWS Documentation Evidence:
For security groups, I used the Amazon Elastic Compute Cloud User Guide, PDF page 3314. The source explains that inbound HTTP and HTTPS rules allow web traffic from specified sources. This supported my finding that the missing TCP port 80 rule was blocking the website from outside the instance. For user data, I used the same AWS guide, PDF page 1739, which explains that user data scripts run during the initial launch by default. This supported how I used user data to install Apache, enable it, start it, and create the Riverside Goods webpage, but it did not prove that Apache was still running later. For the lifecycle test, I used PDF page 1552, which explains that when an instance is stopped and started again, it may be moved to a different host and assigned a new public IPv4 address. This matched my observation because the instance ID stayed the same while the public IPv4 address changed.


CloudShell Command Record:
I started by confirming my AWS identity with aws sts get-caller-identity and verifying the Region with echo $AWS_REGION, which returned us-east-1. I then discovered the available VPC with aws ec2 describe-vpcs --query 'Vpcs[*].[VpcId,CidrBlock,IsDefault]' --output table and the available subnets with aws ec2 describe-subnets --query 'Subnets[*].[SubnetId,VpcId,AvailabilityZone,MapPublicIpOnLaunch]' --output table. I selected VPC vpc-0923dc74fb93534cb and subnet subnet-09dee7c8ebb66cbb3.


I created a user-data.sh script that installed Apache with dnf install -y httpd, enabled the service, started it, and created the Riverside Goods HTML page. I retrieved the current Amazon Linux 2023 AMI through Systems Manager Parameter Store, which returned ami-0d27e0fb3bac4d724. I also checked the available instance profiles and selected LabInstanceProfile.


My first EC2 launch attempt failed because the earlier security group ID sg-0ca63fe33ca04bfaa no longer existed in the selected VPC. AWS returned an InvalidGroup.NotFound error. I kept that error as part of the troubleshooting record and created a new uniquely named security group, which returned sg-0cfdc2c1c0a998279. I confirmed that the new security group had no inbound rules by checking SecurityGroups[0].IpPermissions, which returned []. This was the intended starting condition because the lab required port 80 to be missing at first.


I then launched the EC2 instance using the Amazon Linux AMI, t3.micro, the selected subnet, the new security group, LabInstanceProfile, and the user data script. The new instance ID was i-05e01f2a2f320a124. I waited for the instance to reach the running state and retrieved the public IPv4 address 54.161.58.71. I then waited for the AWS status checks and confirmed that both the system status and instance status were ok. Finally, I checked the security group again and confirmed there were still no inbound permissions. When I tested the website with curl --connect-timeout 5 -v "http://$PUBLIC_IP", the connection timed out, reproducing the Riverside Goods symptom.


Baseline Evidence:
Evidence A showed the instance ID and AMI ID, which proved which EC2 instance and image were used, but it did not prove the website was working. Evidence B showed the public IPv4 address 54.161.58.71, which proved the instance had a public address, but it did not prove HTTP traffic could reach the server. Evidence C showed the instance was running and both AWS status checks were ok, which proved the EC2 instance was healthy from AWS's point of view, but it did not prove Apache was running. Evidence D showed that the security group had no inbound permissions, which proved TCP port 80 was not allowed. Evidence E showed that the external HTTP test timed out. That proved the connection failed, but the timeout by itself did not prove the exact reason for the failure.


Root-Cause Analysis:
The root cause was the missing inbound TCP port 80 rule in the security group. The instance was running and healthy, but the security group was not allowing inbound HTTP traffic. I did not rely only on the timeout to make this decision. I compared the EC2 health checks, the empty security group inbound rules, the failed external HTTP test, the Apache service status inside the instance, and the successful localhost page test. Inside the EC2 instance, systemctl status httpd --no-pager showed Apache as active (running), and curl http://localhost returned the Riverside Goods webpage successfully. This ruled out Apache being stopped or the webpage being missing. Because the application worked locally but outside HTTP traffic timed out, the missing security group rule was the strongest supported cause. Rebuilding the instance was not justified because the server and application were already working correctly.


Corrective Action:
I made the smallest supported change by adding inbound TCP port 80 to the existing security group with aws ec2 authorize-security-group-ingress --group-id "$SG_ID" --protocol tcp --port 80 --cidr 0.0.0.0/0. AWS returned Return: true, and the new rule showed TCP protocol, port 80, and source 0.0.0.0/0. I did not rebuild the server, reinstall Apache, or change the AMI because none of those actions were supported by the evidence.


Verification Evidence:
After adding the rule, I checked the security group again and confirmed that TCP port 80 was now allowed from 0.0.0.0/0. I repeated the HTTP test with curl --connect-timeout 5 -v "http://$PUBLIC_IP". This time the connection succeeded and the server returned HTTP/1.1 200 OK, along with Server: Apache/2.4.68 (Amazon Linux). The Riverside Goods webpage also displayed correctly. This before-and-after result confirmed that the security group change fixed the reachability problem.
IMDSv2 and Guest Evidence

I connected to the EC2 instance using Session Manager with aws ssm start-session --target "$INSTANCE_ID". Inside the instance, I ran systemctl status httpd --no-pager, which showed Apache as active (running). I also ran curl http://localhost, and the Riverside Goods webpage loaded successfully. This confirmed that the web server was working from inside the guest operating system.

I then requested an IMDSv2 token and used it to retrieve the instance ID from the EC2 metadata service. The returned instance ID was i-05e01f2a2f320a124, matching the instance ID from CloudShell. This evidence answered a different question than the CloudShell AWS CLI output because CloudShell showed how AWS viewed and configured the resource from the control plane, while the guest evidence showed what was actually happening inside the server.


Stop/Start Lifecycle Test:
Before stopping the instance, I recorded the instance ID as i-05e01f2a2f320a124, the public IPv4 address as 54.161.58.71, and confirmed that the Riverside Goods webpage was working. I stopped the instance with aws ec2 stop-instances --instance-ids "$INSTANCE_ID" and waited for it to reach the stopped state. I then started the same instance again with aws ec2 start-instances --instance-ids "$INSTANCE_ID", waited for it to return to running, and waited for the status checks to become healthy.

After the restart, the instance ID was still i-05e01f2a2f320a124, but the public IPv4 address changed to 54.144.68.201. I tested the new address with curl, and the server returned HTTP/1.1 200 OK along with the Riverside Goods webpage. This showed that the instance identity and EBS-backed webpage data persisted, while the auto-assigned public IPv4 address changed.


Cleanup Evidence:
After finishing the required testing, I terminated the disposable EC2 instance with aws ec2 terminate-instances --instance-ids "$INSTANCE_ID" and waited with aws ec2 wait instance-terminated --instance-ids "$INSTANCE_ID". I verified that the final instance state was terminated. After that, I deleted the lab security group with aws ec2 delete-security-group --group-id "$SG_ID". AWS returned "Return": true, confirming that security group sg-0cfdc2c1c0a998279 was deleted successfully. No dependency error occurred during cleanup.


Escalation and Change-Control Notes:
No escalation was required because I was able to identify, correct, and verify the issue using the tools and permissions available in the Learner Lab. The issue was limited to a missing security group rule, and the supported correction resolved the problem without needing another team.
One command from the lab that I would not run against a production client workload without authorization is aws ec2 terminate-instances --instance-ids "$INSTANCE_ID". Terminating a production instance could cause a service outage and could permanently remove a server that supports users or business systems. Before running that command in production, I would follow the organization's normal change-management process and get approval from the appropriate system owner, application owner, manager, or change advisory team. I would also verify backups, EBS snapshots, AMIs, attached volumes, networking, security groups, and other recovery information before proceeding.


Lessons Learned:
This lab showed me why troubleshooting should be based on evidence instead of assumptions. A connection timeout only proves that the connection failed; it does not prove the exact cause. I had to compare information from the AWS control plane and from inside the EC2 instance before making a change. I also learned that the smallest supported correction is usually better than rebuilding a working server. Since Apache was already running and the webpage worked locally, changing only the security group was the safest and most direct fix.
The lifecycle test also showed me that an auto-assigned public IPv4 address should not be treated as permanent. The same EC2 instance and EBS-backed webpage data remained after the stop/start cycle, but the public IPv4 address changed. I also learned that failed commands can still be useful evidence because they show what was tried, what went wrong, and how the issue was corrected.


Professional Vocabulary:
Amazon EC2 is AWS's virtual server service, while an AMI is the template used to launch an EC2 instance. A VPC is the virtual network where AWS resources are placed, and a subnet is a smaller IP range inside that VPC. A security group is a stateful virtual firewall that controls allowed inbound and outbound traffic. An inbound rule controls traffic entering a resource, such as allowing HTTP on TCP port 80. EBS is persistent block storage used by EC2, and user data is a set of startup instructions that can run when an instance launches. AWS CloudShell is a browser-based command environment used to run AWS CLI commands, while Session Manager is an AWS Systems Manager feature used to connect to an EC2 instance. IMDSv2 is the token-based EC2 Instance Metadata Service, which can provide details such as the instance ID from inside the server. The control plane is the AWS management layer used to view and change cloud resources, while the guest operating system is the OS running inside the EC2 instance. Root cause means the main underlying reason for a problem, corrective action is the change used to fix it, verification is the evidence that proves the fix worked, lifecycle refers to the states an EC2 instance moves through, escalation means passing an issue to another team when needed, and change control is the process used to approve and document production changes.
