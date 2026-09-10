# Week 1: Cloud Operations Onboarding

## HarborTech Ticket Summary
Ticket ONB-2026-0001 was mainly about making sure my AWS environment was ready before moving on to the next part of the HarborTech work. 
I needed to check that I could get into my AWS Academy and start the Learner Lab without having any problems. 
I also needed to make sure I was using the correct AWS Region and understand what I could and could not do with the permissions I was given 
and While checking everything, 
I found that I could not create a new IAM user. At first, I thought this might be a problem, but I found out while researching that this is normal for the Learner Lab. 
The lab has certain restrictions because it is a practice environment for multiple students. 
So, this was not something that needed to be fixed.

## Client Impact
Making sure the environment works before starting client support is important because 
I do not want to find out that something is wrong when I am already working on a clients issues. 
If I cannot access something I need, that could slow me down and make it harder to help the client, which is bad for clientele.

Checking everything ahead of time also gives me a chance to understand the system and its limits, which can be helpful in very many ways. 
I can find out what I have permission to use and what I am not allowed to change. 
This can also help prevent mistakes that could cause bigger problems later.

## AWS Services Involved
There were a few different AWS things involved in this review. 
AWS Academy is where I was able to access the training environment. 
The Learner Lab gives me a controlled place to practice using AWS environment.

IAM stands for Identity and Access Management. 
IAM controls users, roles, and permissions in AWS. 
I found that I could not create a new IAM user because the Learner Lab limits what students can do.

I also had to pay attention to the AWS Region. 
The lab was started in us-west-2, which was the Region I was supposed to use. 
The AWS account is the environment where the resources are being created and managed.

I also looked at AWS documentation, including the AWS CLI Command Reference, 
so I could get more familiar with where to find commands and information when I need it if i need help.

## Virtualization Connection
Virtualization makes it possible to manage computers, storage, and other resources through software instead of having to work directly with the physical hardware. 
This is one of the reasons cloud computing is useful because resources can be created 
and managed without physically being in front of the server, which can save time and money sometimes.

Even though I do not have to deal with the physical hardware, there are still limits that I have to follow. 
Things like the AWS account, Region, permissions, and budget still matter. 
For example, I could not create an IAM user because I did not have the permission to do it in 
the Learner Lab.

This showed me that just because something is in the cloud does not mean there are no limits. 
There are still boundaries that help keep everything organized and secure and running properly.

## Evidence Reviewed
I checked several things during the Week 1 review. 
Firstly, I checked my AWS Academy access and was able to get into the Learner Lab. 
I was also able to start the lab in the correct us-west-2 Region.

I looked at the IAM restrictions and confirmed that I could not create a new IAM user. 
I also checked the LabRole and LabInstanceProfile, which are part of the setup used by the Learner Lab.

I reviewed how the lab session works and what happens when the session timer runs out.
I also looked at the budget and learned that the amount shown does not update right away. 
It can take around 8 to 12 hours, so I should not assume the number on the screen is showing my exact current usage.

I also looked at what happens if the lab is reset. 
Resetting the lab can remove resources that were created, 
and it does not simply give the used budget back. 
Because of that, I need to be careful not to reset the lab by accident.

I also checked the AWS documentation and looked at the AWS CLI Command Reference. 
The GitHub Operations Playbook is also something I need to be familiar with because it can 
help me know what steps to follow during support work.

## Operational Analysis
Based on what I checked, I believe my environment is ready for the next step. 
I was able to access AWS Academy, start the Learner Lab, and use the required Region 
without having any major problems. I also learned that not every restriction means something is broken. For example, I could not create an IAM user, but I found out that this was expected in the Learner Lab. A verified finding is something I actually checked, like successfully starting the lab. An assumption would be something I believe works without actually testing it. 
This showed me why it is important to have evidence before saying that something is working.

## Recommendation
Based on the checks I completed, I would say that my environment is ready for Week 2 support work. 
I was able to access the AWS Academy account and start the Learner Lab in the correct Region. 
I also understand some of the main restrictions and rules that I need to follow. 
The next thing I need to do is continue using the HarborTech Operations Playbook 
and make sure I follow the correct steps when working on support tasks. 
I also need to remember to stop resources 
when I am finished using them so I do not waste the lab budget.

## Escalation Notes
Right now, I do not think an escalation is needed. 
I did not find any major access or environment problems that would stop me from continuing with the work. 
The IAM issue was looked into and turned out to be a normal restriction of the Learner Lab. 
Since I was able to start the lab and use the environment, 
there is not currently anything that needs to be sent to the instructor for help.

## Lessons Learned
Week 1 taught me that being ready for cloud operations is more than just being able to log in. 
I need to know what I can access, what I cannot access, and what rules I need to follow. 
I also learned that an error or restriction does not always mean something is broken. 
Sometimes it is there on purpose for security or to protect the lab. 
I learned that having evidence is important 
because I should be able to show how I know something is working instead of just assuming it. 
Looking at the AWS documentation and Operations Playbook
also showed me that it is better to look up information 
when I am unsure instead of just guessing.

## Professional Vocabulary
Virtualization means using software to manage computers, storage, and other resources without directly working with the physical hardware. 
Evidence is something I actually checked that helps show whether something is working. 
A finding is something I discovered after checking or testing something. 
An assumption is something I believe is true but have not actually confirmed yet. 
Escalation means sending a problem to an instructor, manager, or someone else who can help fix it. 
A sandbox is a controlled environment where I can practice without working directly in a real production system. 
A Region is the AWS location where resources are being used, such as us-west-2. 
IAM stands for Identity and Access Management and controls users, roles, and permissions in AWS. 
An Operations Playbook is a guide that gives instructions and steps to follow when doing technical or support work.
