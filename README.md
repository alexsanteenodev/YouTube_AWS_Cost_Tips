# AWS Cost Optimization Tips

This repository contains the scripts and policies demonstrated in the video [Simple AWS Configuration Adjustments Save You Big Money](https://youtu.be/RQjh_JRZqqU). The video shows how to optimize your AWS costs by addressing three common cost leaks that often go unnoticed.

This repository contains scripts and policies to help you optimize your AWS costs by addressing three common cost leaks:

1. **AWS X-Ray Cost Optimization**

   - X-Ray is enabled by default in some services and charges per trace
   - Solution: Service Control Policy to deny trace submissions globally
   - Prevents unnecessary X-Ray costs in production environments

2. **Lambda CloudWatch Logs Optimization**

   - Lambda functions automatically log to CloudWatch, even for simple operations
   - AWS charges $0.50 per GB ingested plus storage per month
   - Solution: IAM policy to deny CloudWatch Logs actions for specific Lambda functions
   - Best for functions that don't need logging (health checks, simple jobs, etc.)

3. **NAT Gateway Cost Optimization**
   - Services in private subnets route internet traffic through NAT Gateway
   - AWS charges $0.045 per GB transferred plus hourly fees
   - Solution: Use VPC Endpoints to route traffic internally
   - Eliminates NAT Gateway charges for accessing AWS services

## Scripts and Policies

### 1. X-Ray Disabling Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": ["xray:PutTelemetryRecords", "xray:PutTraceSegments"],
      "Resource": "*"
    }
  ]
}
```

### 2. Lambda CloudWatch Logs Disabling Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "*"
    }
  ]
}
```

## Implementation Steps

1. **X-Ray Policy**

   - Create a Service Control Policy in AWS Organizations
   - Apply the policy to your organization's root
   - This will disable X-Ray across all accounts

2. **Lambda Logging Policy**

   - Create a custom policy in IAM
   - Attach the policy to Lambda execution roles
   - Apply only to functions that don't need logging

3. **VPC Endpoint Setup**
   - Create VPC Endpoints for required AWS services (S3, DynamoDB, etc.)
   - Configure route tables in private subnets
   - Update security groups as needed

## Cost Explorer Usage

Even in the free tier, you can use AWS Cost Explorer to:

- Monitor usage by service
- Track request volume and data transfer
- Analyze costs by custom tags
- Identify potential cost optimization opportunities

## Additional Resources

- [AWS Cost Explorer Documentation](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)
- [VPC Endpoints Documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html)
- [Service Control Policies Documentation](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html)
