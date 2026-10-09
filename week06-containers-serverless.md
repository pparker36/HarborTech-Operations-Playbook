# Week 6: Containers and Serverless Computing

## HarborTech Ticket Summary

For Week 6, HarborTech reviewed a ticket from Bright Path Community Services about whether they really need to keep using an EC2 server for their workloads. Right now, the client has a daily report and a small REST API running on the same EC2 instance. The report only runs once every morning, and the API gets requests at different times throughout the day. Since the server spends a lot of time sitting idle, the main question is whether a different compute model would make more sense instead of keeping the server running all the time.

## Client Impact

Using a server that doesn't match the workload can waste money and create more work for the IT team. With EC2, the client has to think about keeping the operating system updated, checking the server, and making sure it stays available even when the applications aren't doing much. If the API starts getting more traffic, one server could also become a problem. On the other hand, moving to a different service without testing it could cause errors or make the applications less reliable. I think the goal should be to find something that handles the work properly without making things more complicated or expensive than they need to be.

## AWS Services Involved

The main service being reviewed is Amazon EC2 because that's where the two workloads currently run. AWS Lambda is another option because it can run code when a schedule or request triggers it, without the client having to manage a full server. Amazon API Gateway could receive the API requests and pass them to Lambda. Containers, including services like Amazon ECS with AWS Fargate, are another possible way to package and run an application without managing it exactly like a traditional EC2 server.

IAM execution roles are important because Lambda needs permission to access only the AWS resources its code actually uses. Event-driven computing means something like a schedule or an API request starts the work. Amazon EventBridge Scheduler could start the daily report each morning. AWS Step Functions could help if a job eventually needs several steps with retries or decisions, although the ticket doesn't show that this is needed yet. Monitoring through Amazon CloudWatch would help the team check logs and errors. VPC-connected Lambda would only be needed if the function has to reach private resources inside a VPC.

## Virtualization Connection

A virtual machine like EC2 gives us a full operating system to manage, even though AWS takes care of the physical hardware underneath it. Containers work differently because they package an application and its dependencies while sharing the host operating system's kernel. This can make deployments more consistent and easier to move. Serverless takes things further because AWS handles more of the infrastructure behind running the code. We would still have to manage the application itself, but we wouldn't need to spend as much time maintaining a server. I learned that the best option depends on how much control the application actually needs.

## Evidence Reviewed

The evidence provided in the ticket shows that the daily report runs once each morning, finishes quickly, and doesn't need to keep local information between runs. It also says the EC2 instance sits idle for long periods. For the API, the ticket says it is a small REST API that receives intermittent requests and returns lightweight responses. Those details matter because neither workload appears to need a server actively doing work all day.

The ticket also included a separate comparison scenario where a continuously running application needs a custom operating-system package and host-level troubleshooting access. That scenario helps explain why EC2 might still be needed for some applications, but it isn't listed as one of Bright Path's two current workloads. I have not included made-up Lambda test results, request counts, IAM checks, logs, or VPC findings. Those would need to be collected from the actual AWS environment before any migration.

## Operational Analysis

Based on the ticket evidence, the current EC2 setup seems like more server than these two small workloads need. The daily report looks like a good match for Lambda because it starts on a schedule, runs briefly, and doesn't need to save state locally. The REST API may also work well with API Gateway and Lambda because requests only arrive from time to time. In that setup, API Gateway would accept the requests and Lambda would run the application code.

Containers could be useful if the API has special dependencies, needs a particular runtime setup, or doesn't work well within Lambda's limits. EC2 would make more sense for a workload that really needs control over the host operating system. Step Functions might be useful for a more complicated report with multiple connected steps, but the ticket doesn't give enough evidence to recommend it now. This is a recommendation based on the provided workload description, not proof that a migration will work without changes.

## Recommendation

I would recommend evaluating AWS Lambda first for Bright Path's daily reporting task, with a scheduled trigger through EventBridge Scheduler. For the small REST API, I would look at Amazon API Gateway with Lambda. I think both choices make sense because the report runs quickly and the API only receives occasional requests. This could reduce the need to keep an EC2 instance running all the time and cut down on server maintenance.

AWS would handle more of the underlying infrastructure, but HarborTech would still be responsible for writing and updating the code, setting up IAM permissions, protecting the API, monitoring errors, and checking that everything works. Before making a final decision, I would want to verify the report's actual runtime, the API's request volume and response time needs, any application dependencies, network access, and the estimated cost. I would also test the applications in a safe environment first. I wouldn't shut down the current EC2 instance until the replacement has been tested and approved.

## Escalation Notes

Since I'm working in an intern role, I would not make a production migration or major security change on my own. I would bring the proposed Lambda and API Gateway setup to my supervisor or the appropriate HarborTech team for approval. I would also escalate anything involving new IAM permissions, access to private VPC resources, API security settings, or changes to the client's live application. The ticket gives enough information to suggest a direction, but not enough to approve a production move yet.

## Lessons Learned

Week 6 helped me understand that not every application needs its own server running all day. I learned that Lambda is useful when code only needs to run for a short time or when an event triggers it. API Gateway can handle incoming API requests, and containers can help package applications that need more control over their runtime. I also learned that serverless doesn't mean there is no responsibility left for the IT team. We still have to think about code, security, permissions, and monitoring. The biggest thing I took away is that we should look at what the workload actually does before choosing a compute service.

## Professional Vocabulary

- **Container:** A way to package an application with the files and dependencies it needs so it can run more consistently in different environments.
- **Serverless Computing:** Running code or services without having to manage the underlying servers ourselves. AWS still uses servers, but it handles more of that work.
- **AWS Lambda:** An AWS service that runs code when it gets triggered, such as by a schedule or a request.
- **API Gateway:** A service that receives API requests and sends them to the right backend, such as a Lambda function.
- **Execution Role:** An IAM role that gives a Lambda function permission to use the AWS resources it needs.
- **Event Trigger:** Something that starts an action, like a scheduled time or an incoming API request.
- **Step Functions:** An AWS service that connects multiple steps in a workflow and can help manage retries and failures.
- **VPC-Connected Lambda:** A Lambda function configured to access resources through a VPC, such as a private database.
- **Runtime:** The environment needed to run application code, such as a supported Python version.
- **Abstraction:** Hiding some of the technical details so we can focus more on the application instead of the hardware or servers behind it.
