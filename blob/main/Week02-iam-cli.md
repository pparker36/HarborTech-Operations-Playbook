# Week 2: IAM and AWS CLI Investigation

## HarborTech Ticket Summary
TKT-2026-0002 involves an access issue reported by Riverside Goods. Their inventory coordinator, Marcus Webb, successfully logs into the AWS console using his assigned IAM credentials, but he encounters an AccessDenied error whenever he attempts to perform his assigned inventory work (specifically, listing the riverside-inventory S3 bucket). The onboarding and permission records reveal that Marcus has valid authentication credentials, but his user account completely lacks any assigned permission policies or job-function group memberships.

## Client Impact
Because Marcus lacks permission policies or group memberships, he cannot view files, read reports, or upload inventory data. This prevents him from executing his role as Inventory Coordinator. While the client proposed attaching AmazonS3FullAccess directly to resolve the ticket quickly, doing so exposes the environment to unnecessary risk by granting broad access across all S3 resources instead of maintaining appropriate operational boundaries.

## AWS Services Involved
AWS Identity and Access Management (IAM): Used to manage identities (users, roles, groups) and enforce permission policies.

Amazon S3: The cloud storage service containing the riverside-inventory bucket targeted for object listing, reading, and uploading.

AWS CloudShell / AWS CLI: Browser-based terminal environment and command-line tool used to run diagnostic commands (aws sts get-caller-identity, aws iam get-role, aws iam list-attached-role-policies) to inspect identities, trust policies, and attached permissions.

AWS Regions: The geographical infrastructure context where cloud services and S3 buckets are hosted.

## Virtualization Connection
Identity and access controls act as the security gatekeepers for software-defined cloud resources. In virtualized cloud environments, hypervisors and control planes rely strictly on IAM authorization layers to determine which users or services can provision, modify, or read virtual compute instances, storage volumes, virtual private clouds (VPCs), and software-defined networking components. Proper IAM scoping ensures multi-tenant isolation and security boundary enforcement.

## Evidence Reviewed
Evidence A (Authentication): Marcus successfully logs into the Riverside Goods AWS console with his assigned IAM user credentials.

Evidence B (Business Requirement): Marcus only needs access to list the riverside-inventory bucket, read inventory report objects, and upload approved files; he does not administer EC2 or other buckets.

Evidence C (Permission Record): Onboarding records indicate the user exists, but has no job-function group membership or directly attached permission policies.

Evidence D (Request Result): Direct requests to list the inventory bucket return AccessDenied.

Client Proposal: Requesting the attachment of AmazonS3FullAccess directly to Marcus.

CLI Caller Identity & Role Inspection: Running aws sts get-caller-identity, aws iam get-role --role-name LabRole, and aws iam list-attached-role-policies to evaluate trust relationships versus permission sources.

## Operational Analysis
The evidence clearly distinguishes authentication from authorization. Authentication answers "Who are you?"—which Marcus successfully passes when he logs in with his valid credentials. Authorization answers "What are you allowed to do?"—which fails because no permission policies or group memberships are attached to his account. The permission gap is a complete lack of scoping, leading to the AccessDenied state on the target S3 bucket.

## Recommendation
Reject the client's proposal to attach AmazonS3FullAccess. Instead, implement a least-privilege custom policy applied via an appropriate group or user policy that restricts access strictly to the riverside-inventory bucket.

## Escalation Notes
Internal Escalation Note: Looking into this ticket, our review shows that Marcus is actually logging in just fine, and his authentication is right, but he is getting hit by an access denied wall because there's no permission policy attached to his account. The direction we need to follow is least-privilege scoping, meaning we should reject the client's proposal to give Marcus full access and instead restrict him only to the Riverside inventory bucket so he can list objects, read reports, and upload approved files. We still need to figure out the exact JSON code for the policy and the specific resource ARNs. Because this involves making changes in a client production environment, this change needs to be reviewed and done by an authorized HarborTech team member rather than an intern, ensuring we stay fully compliant with security best practices, avoid mess-ups, and keep everything locked down tight and secure.


## Lessons Learned
Week 2 demonstrated that successful authentication does not guarantee operational capability. Investigating authorization failures requires dissecting IAM policies, differentiating trust relationships from permission boundaries, and strictly adhering to the principle of least privilege. Using the AWS CLI (sts and iam namespaces) provides transparent, verifiable evidence for troubleshooting access gaps without relying on broad administrative shortcuts.

## Professional Vocabulary
Authentication: Verifying the identity of a user or service (e.g., logging in with credentials or multi-factor authentication).

Authorization: Determining what actions and resources an authenticated identity is permitted to access.

IAM (Identity and Access Management): The AWS service responsible for securely managing access to services and resources.

Policy: A document formatted in JSON that defines permissions, specifying allowed or denied actions and resources.

Least Privilege: A core security principle granting users only the minimum permissions necessary to complete their job functions.

AccessDenied: An explicit error message returned when an identity attempts an action without the required authorization.

AWS CLI (Command Line Interface): A unified tool to manage AWS services from the command line shell.

CloudShell: A browser-based, pre-authenticated shell environment for running AWS CLI commands.

Caller Identity: Information identifying the current IAM user, role, or assumed session making AWS API requests.

Resource Scope: The specific boundary or target resources (like a single S3 bucket ARN) that a policy applies to.



