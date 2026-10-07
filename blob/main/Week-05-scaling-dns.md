# Week 5: Scaling, Load Balancing, and DNS

## HarborTech Ticket Summary

This week’s HarborTech ticket focused on getting ready for a big increase in traffic. The current setup depends too much on one server and one main endpoint, which could become a problem if traffic gets too high or something fails. The goal was to look at scaling, load balancing, target health, and DNS failover to see what could help make the application more available.

## Client Impact

If traffic increases too much, the current server could become overloaded. This could make the application slow or even unavailable for users. Since the setup also depends on one main server and endpoint, there is a single point of failure. If that part goes down, customers may not be able to reach the application, which could affect normal business operations.

## Provided Ticket Evidence

The ticket said that traffic from the previous promotion reached about 92% CPU usage and that the next promotion could double the number of requests. It also proposed an Auto Scaling setup of 2 / 2 / 6, which means a minimum of two instances, a desired amount of two, and a maximum of six. The ticket also said that the proposed load balancer had two healthy test targets. This information came from the ticket and was not something I personally verified in AWS.

## AWS Commands Used

During my investigation, I used the AWS CLI to check for Auto Scaling groups and target groups. I ran `aws autoscaling describe-auto-scaling-groups --query 'AutoScalingGroups[].{Name:AutoScalingGroupName,Min:MinSize,Desired:DesiredCapacity,Max:MaxSize}' --output table` to check Auto Scaling. I also ran `aws elbv2 describe-target-groups --query 'TargetGroups[].{Name:TargetGroupName,TargetGroupArn:TargetGroupArn,Protocol:Protocol,Port:Port}' --output table` to check for target groups.

## AWS Evidence Collected

When I checked my AWS environment, the Auto Scaling command did not return any Auto Scaling groups. It just returned back to the CloudShell prompt. The target group command also did not return any target groups. Because there were no target groups showing in my environment, I could not personally check target health.

## Virtualization Connection

Virtualization makes it easier to create and remove virtual servers without needing a separate physical computer every time more capacity is needed. Auto Scaling can add more EC2 instances when traffic gets high and remove them when they are no longer needed. A load balancer can then spread traffic between those instances instead of sending everything to one server.

## Operational Analysis

The ticket shows that capacity could become a problem because the last promotion already reached 92% CPU usage and the next promotion is expected to bring even more traffic. The proposed 2 / 2 / 6 Auto Scaling setup could help by keeping at least two instances running and allowing more to be added if needed. However, my own AWS results did not show any Auto Scaling groups, so I could not confirm that scaling is currently set up.

The ticket also said that two test targets were healthy, but I did not see any target groups in my AWS environment. Because of that, I could not personally confirm the target health or that traffic was being spread between multiple servers. I also did not verify a working Route 53 failover setup, so there is still more that would need to be checked before saying DNS failover is ready.

## Recommendation

I would recommend that HarborTech continue with the scaling and load balancing plan, but everything should be checked before it is used in production. Auto Scaling could help with the expected traffic increase, and the load balancer could help spread that traffic across multiple servers. Route 53 failover should also be tested to make sure the backup endpoint can actually take over if the main one fails. Cost should also be watched since keeping more instances running will cost more money.

## Escalation Notes

Any production changes to Auto Scaling, load balancing, target groups, or Route 53 should be approved by the correct administrator or cloud team. As an intern, I would document what I found and recommend the next steps, but I would not make production changes without approval.

## Lessons Learned

This week taught me that I should not assume something is set up just because it is mentioned in a ticket. I need to check AWS and use the actual results as evidence. I also learned that Auto Scaling and load balancing do different jobs. Auto Scaling helps with capacity, while load balancing spreads traffic. Health checks show if a server is responding, but they do not automatically prove that DNS failover will work.

## Professional Vocabulary

Elasticity means adding or removing cloud resources when needed. Scalability means being able to handle more work. A load balancer spreads traffic between servers. A target group is the group of servers receiving that traffic. A health check tests if a server is working. An Auto Scaling group manages EC2 instances automatically. A launch template tells AWS how to create new instances. Desired capacity is the normal number of instances AWS tries to keep running. Route 53 is AWS’s DNS service, and failover means sending traffic to a backup when the main endpoint goes down.
