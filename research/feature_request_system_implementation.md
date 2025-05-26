# Deployment of Ultra-Low-Cost Serverless Feature Request System on AWS

## Architecture overview and data flow

The serverless feature request system leverages AWS managed services to create a cost-effective, scalable solution with minimal operational overhead. The architecture follows an event-driven pattern optimized for multi-tenant applications.

### Core Architecture Components

```
┌─────────────────┐    ┌─────────────────┐      ┌─────────────────┐
│   CloudFront    │    │   API Gateway   │      │   Lambda        │
│   (Frontend)    │───▶│   (HTTP API)    │─────▶│   Functions     │
│                 │    │                 │      │                 │
└─────────────────┘    └─────────────────┘      └─────────────────┘
         │                       │                       │
         ▼                       │                       ▼
┌─────────────────┐              │              ┌─────────────────┐
│       S3        │              │              │   DynamoDB      │
│  (Static Files) │              │              │  (Data Store)   │
└─────────────────┘              │              └─────────────────┘
                                 │                       │
                                 ▼                       ▼
                         ┌─────────────────┐    ┌─────────────────┐
                         │      SQS        │    │   DynamoDB      │
                         │   (Decoupling)  │    │   Streams       │
                         └─────────────────┘    └─────────────────┘
                                  │                       │
                                  ▼                       ▼
                                 ┌─────────────────────────┐
                                 │         SNS/SES         │
                                 │    (Notifications)      │
                                 └─────────────────────────┘
```

### Data Flow Pattern

**1. Request Submission Flow:**

- User submits feature request via web interface (S3/CloudFront)
- API Gateway validates and routes to Lambda function
- Lambda processes request with business logic validation
- Data persists to DynamoDB with multi-tenant isolation
- DynamoDB Streams trigger downstream processing
- SNS publishes events for notifications

**2. Query and Export Flow:**

- API Gateway handles authentication/authorization
- Lambda queries DynamoDB using optimized access patterns
- Results cached at API Gateway for performance
- Export functions generate AI-compatible formats

## 1. Architecture Outline

### API Gateway Configuration

Use **HTTP API** instead of REST API for 71% cost savings:

```yaml
FeatureRequestApi:
  Type: AWS::Serverless::HttpApi
  Properties:
    StageName: !Ref Environment
    CorsConfiguration:
      AllowMethods: ['GET', 'POST', 'PUT', 'DELETE']
      AllowHeaders: ['Content-Type', 'Authorization']
      AllowOrigins: ['*']
    Throttle:
      BurstLimit: 200
      RateLimit: 100
```

### Lambda Functions Architecture

Implement single-purpose functions for better scalability and debugging:

```yaml
Functions:
  CreateRequest:    # Handles new feature request submissions
  GetRequests:      # Retrieves filtered request lists
  UpdateRequest:    # Updates status, votes, comments
  ExportRequests:   # Generates AI-compatible exports
  ProcessWebhooks:  # Handles real-time integrations
```

### DynamoDB Single-Table Design

Optimized for multi-tenant access patterns:

```
Primary Key Structure:
PK: APP#{appId}
SK: REQUEST#{requestId}

GSI1 (Status Index):
GSI1PK: STATUS#{status}
GSI1SK: {timestamp}

GSI2 (Tenant Index):
GSI2PK: TENANT#{tenantId}
GSI2SK: APP#{appId}
```

### S3 Static Hosting

CloudFront distribution for global performance:

- Static frontend files (React/Vue)
- Presigned URLs for file attachments
- Origin Access Control for security

### SNS/SES Integration

Event-driven notification system:

- SNS topics for event fan-out
- SES for templated email notifications
- Lambda subscribers for webhook delivery

## 2. Code Structure

### Create Request Function

```javascript
const AWS = require('aws-sdk');
const { v4: uuidv4 } = require('uuid');

const dynamoDb = new AWS.DynamoDB.DocumentClient({
    httpOptions: {
        agent: new https.Agent({ keepAlive: true })
    }
});

module.exports.createRequest = async (event) => {
    const timestamp = new Date().toISOString();
    const requestId = uuidv4();
    const { appId, title, description, priority = 'medium' } = JSON.parse(event.body);

    // Input validation
    if (!appId || !title || !description) {
        return {
            statusCode: 400,
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ message: "Missing required fields" })
        };
    }

    const item = {
        PK: `APP#${appId}`,
        SK: `REQUEST#${requestId}`,
        requestId,
        appId,
        title,
        description,
        priority,
        status: 'open',
        votes: 0,
        createdAt: timestamp,
        updatedAt: timestamp,
        GSI1PK: `STATUS#open`,
        GSI1SK: timestamp
    };

    await dynamoDb.put({
        TableName: process.env.TABLE_NAME,
        Item: item,
        ConditionExpression: 'attribute_not_exists(PK)'
    }).promise();

    // Publish to SNS for notifications
    await sns.publish({
        TopicArn: process.env.SNS_TOPIC_ARN,
        Message: JSON.stringify({ eventType: 'REQUEST_CREATED', ...item }),
        MessageAttributes: {
            appId: { DataType: 'String', StringValue: appId }
        }
    }).promise();

    return {
        statusCode: 201,
        body: JSON.stringify({ requestId, data: item })
    };
};
```

### Get Requests Function with Pagination

```javascript
module.exports.getRequests = async (event) => {
    const { appId, status, limit = 50, lastEvaluatedKey } = event.queryStringParameters || {};

    let params = {
        TableName: process.env.TABLE_NAME,
        Limit: parseInt(limit),
        ScanIndexForward: false
    };

    if (status) {
        // Query by status using GSI
        params.IndexName = 'GSI1';
        params.KeyConditionExpression = 'GSI1PK = :statusPK';
        params.FilterExpression = 'appId = :appId';
        params.ExpressionAttributeValues = {
            ':statusPK': `STATUS#${status}`,
            ':appId': appId
        };
    } else {
        // Query all requests for app
        params.KeyConditionExpression = 'PK = :appPK AND begins_with(SK, :prefix)';
        params.ExpressionAttributeValues = {
            ':appPK': `APP#${appId}`,
            ':prefix': 'REQUEST#'
        };
    }

    if (lastEvaluatedKey) {
        params.ExclusiveStartKey = JSON.parse(Buffer.from(lastEvaluatedKey, 'base64').toString());
    }

    const result = await dynamoDb.query(params).promise();

    return {
        statusCode: 200,
        headers: {
            "Content-Type": "application/json",
            "Cache-Control": "max-age=300"
        },
        body: JSON.stringify({
            items: result.Items,
            nextToken: result.LastEvaluatedKey ?
                Buffer.from(JSON.stringify(result.LastEvaluatedKey)).toString('base64') : null
        })
    };
};
```

### Update Request with Optimistic Locking

```javascript
module.exports.updateRequest = async (event) => {
    const { appId, requestId } = event.pathParameters;
    const updates = JSON.parse(event.body);
    const timestamp = new Date().toISOString();

    // Build update expression dynamically
    let updateExpression = 'SET updatedAt = :timestamp';
    let expressionAttributeValues = { ':timestamp': timestamp };
    let expressionAttributeNames = {};

    if (updates.status) {
        updateExpression += ', #status = :status, GSI1PK = :newGSI1PK';
        expressionAttributeValues[':status'] = updates.status;
        expressionAttributeValues[':newGSI1PK'] = `STATUS#${updates.status}`;
        expressionAttributeNames['#status'] = 'status';
    }

    if (updates.vote !== undefined) {
        updateExpression += ', votes = votes + :vote';
        expressionAttributeValues[':vote'] = updates.vote;
    }

    const params = {
        TableName: process.env.TABLE_NAME,
        Key: {
            PK: `APP#${appId}`,
            SK: `REQUEST#${requestId}`
        },
        UpdateExpression: updateExpression,
        ExpressionAttributeValues: expressionAttributeValues,
        ExpressionAttributeNames: Object.keys(expressionAttributeNames).length ? expressionAttributeNames : undefined,
        ReturnValues: 'ALL_NEW'
    };

    const result = await dynamoDb.update(params).promise();

    return {
        statusCode: 200,
        body: JSON.stringify({ data: result.Attributes })
    };
};
```

### Export Function for AI Tools

```javascript
module.exports.exportRequests = async (event) => {
    const { appId, format = 'json' } = event.queryStringParameters || {};

    // Paginated scan for all requests
    let items = [];
    let lastEvaluatedKey = null;

    do {
        const params = {
            TableName: process.env.TABLE_NAME,
            KeyConditionExpression: 'PK = :pk AND begins_with(SK, :prefix)',
            ExpressionAttributeValues: {
                ':pk': `APP#${appId}`,
                ':prefix': 'REQUEST#'
            },
            ExclusiveStartKey: lastEvaluatedKey
        };

        const result = await dynamoDb.query(params).promise();
        items = items.concat(result.Items);
        lastEvaluatedKey = result.LastEvaluatedKey;
    } while (lastEvaluatedKey);

    // Format for AI consumption
    const formattedData = {
        appId,
        exportedAt: new Date().toISOString(),
        totalRequests: items.length,
        requests: items.map(item => ({
            id: item.requestId,
            title: item.title,
            description: item.description,
            status: item.status,
            priority: item.priority,
            votes: item.votes,
            createdAt: item.createdAt
        }))
    };

    return {
        statusCode: 200,
        headers: {
            'Content-Type': format === 'csv' ? 'text/csv' : 'application/json',
            'Content-Disposition': `attachment; filename="features-${appId}.${format}"`
        },
        body: format === 'csv' ? convertToCSV(formattedData.requests) : JSON.stringify(formattedData)
    };
};
```

### DynamoDB Table Structure

```yaml
FeatureRequestsTable:
  Type: AWS::DynamoDB::Table
  Properties:
    BillingMode: PAY_PER_REQUEST  # Pay only for what you use
    AttributeDefinitions:
      - AttributeName: PK
        AttributeType: S
      - AttributeName: SK
        AttributeType: S
      - AttributeName: GSI1PK
        AttributeType: S
      - AttributeName: GSI1SK
        AttributeType: S
    KeySchema:
      - AttributeName: PK
        KeyType: HASH
      - AttributeName: SK
        KeyType: RANGE
    GlobalSecondaryIndexes:
      - IndexName: GSI1
        KeySchema:
          - AttributeName: GSI1PK
            KeyType: HASH
          - AttributeName: GSI1SK
            KeyType: RANGE
        Projection:
          ProjectionType: ALL
    StreamSpecification:
      StreamViewType: NEW_AND_OLD_IMAGES
    PointInTimeRecoverySpecification:
      PointInTimeRecoveryEnabled: true
```

### S3 Static Form Hosting

```yaml
StaticWebsiteBucket:
  Type: AWS::S3::Bucket
  Properties:
    BucketName: !Sub "${AWS::StackName}-frontend"
    WebsiteConfiguration:
      IndexDocument: index.html
      ErrorDocument: error.html

CloudFrontDistribution:
  Type: AWS::CloudFront::Distribution
  Properties:
    DistributionConfig:
      Origins:
        - DomainName: !GetAtt StaticWebsiteBucket.RegionalDomainName
          Id: S3Origin
          S3OriginConfig:
            OriginAccessIdentity: !Sub "origin-access-identity/cloudfront/${CloudFrontOAI}"
      DefaultRootObject: index.html
      DefaultCacheBehavior:
        TargetOriginId: S3Origin
        ViewerProtocolPolicy: redirect-to-https
        CachePolicyId: 658327ea-f89d-4fab-a63d-7e88639e58f6  # Managed-CachingOptimized
```

### SNS/SES Integration

```javascript
// Process DynamoDB Streams for notifications
module.exports.processStream = async (event) => {
    for (const record of event.Records) {
        if (record.eventName === 'INSERT') {
            const newItem = AWS.DynamoDB.Converter.unmarshall(record.dynamodb.NewImage);

            // Send SNS notification
            await sns.publish({
                TopicArn: process.env.SNS_TOPIC_ARN,
                Subject: `New Feature Request: ${newItem.title}`,
                Message: JSON.stringify({
                    eventType: 'NEW_REQUEST',
                    appId: newItem.appId,
                    requestId: newItem.requestId,
                    title: newItem.title
                })
            }).promise();
        }
    }
};

// SES email handler
module.exports.sendEmail = async (event) => {
    for (const record of event.Records) {
        const message = JSON.parse(record.Sns.Message);

        await ses.sendTemplatedEmail({
            Source: 'noreply@example.com',
            Template: 'FeatureRequestNotification',
            Destination: {
                ToAddresses: await getSubscribers(message.appId)
            },
            TemplateData: JSON.stringify({
                title: message.title,
                appId: message.appId,
                link: `https://app.example.com/requests/${message.requestId}`
            })
        }).promise();
    }
};
```

## 3. Deployment Considerations

### AWS SAM Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Parameters:
  Environment:
    Type: String
    Default: dev
    AllowedValues: [dev, staging, prod]

Globals:
  Function:
    Runtime: nodejs18.x
    MemorySize: 512  # Right-sized for cost optimization
    Timeout: 30
    Environment:
      Variables:
        TABLE_NAME: !Ref FeatureRequestsTable
        SNS_TOPIC_ARN: !Ref NotificationTopic
        ENVIRONMENT: !Ref Environment

Resources:
  # API Gateway - Use HTTP API for cost savings
  FeatureRequestApi:
    Type: AWS::Serverless::HttpApi
    Properties:
      StageName: !Ref Environment
      CorsConfiguration:
        AllowMethods: ['*']
        AllowHeaders: ['*']
        AllowOrigins: ['*']

  # Lambda Functions with specific IAM policies
  CreateRequestFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: src/
      Handler: handlers.createRequest
      Events:
        ApiEvent:
          Type: HttpApi
          Properties:
            ApiId: !Ref FeatureRequestApi
            Path: /requests
            Method: POST
      Policies:
        - DynamoDBCrudPolicy:
            TableName: !Ref FeatureRequestsTable
        - SNSPublishMessagePolicy:
            TopicName: !GetAtt NotificationTopic.TopicName

  # DynamoDB Stream processor
  StreamProcessorFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: src/
      Handler: handlers.processStream
      Events:
        Stream:
          Type: DynamoDB
          Properties:
            Stream: !GetAtt FeatureRequestsTable.StreamArn
            StartingPosition: TRIM_HORIZON
            MaximumBatchingWindowInSeconds: 10
      Policies:
        - SNSPublishMessagePolicy:
            TopicName: !GetAtt NotificationTopic.TopicName
```

### IAM Roles and Permissions

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:Query"
      ],
      "Resource": [
        "arn:aws:dynamodb:*:*:table/FeatureRequests",
        "arn:aws:dynamodb:*:*:table/FeatureRequests/index/*"
      ],
      "Condition": {
        "ForAllValues:StringLike": {
          "dynamodb:LeadingKeys": ["APP#${aws:userid}*"]
        }
      }
    }
  ]
}
```

### Environment Variables and Configuration

```yaml
EnvironmentVariables:
  Common:
    REGION: !Ref AWS::Region
    LOG_LEVEL: !If [IsProduction, "INFO", "DEBUG"]

  PerFunction:
    CreateRequestFunction:
      RATE_LIMIT: "100"
      MAX_REQUEST_SIZE: "10240"

    ExportFunction:
      MAX_EXPORT_SIZE: "10000"
      EXPORT_BUCKET: !Ref ExportBucket
```

### Security Best Practices

**1. API Security:**

- Enable AWS WAF for DDoS protection
- Implement rate limiting at API Gateway
- Use Cognito User Pools for authentication
- Enable request validation

**2. Data Protection:**

```yaml
# Enable encryption at rest
DynamoDBEncryption:
  SSESpecification:
    SSEEnabled: true
    SSEType: KMS
    KMSMasterKeyId: alias/aws/dynamodb

# Secrets management
DatabaseSecret:
  Type: AWS::SecretsManager::Secret
  Properties:
    KmsKeyId: !Ref KMSKey
```

**3. Network Security:**

- Use VPC endpoints for AWS services
- Enable CloudTrail for audit logging
- Implement least privilege IAM policies

## 4. Cost Optimization

### Pricing Breakdown (Monthly)

**Ultra-Low Usage (< 10K requests/month):**

```
API Gateway HTTP: FREE (1M free tier)
Lambda: FREE (1M requests + 400K GB-seconds)
DynamoDB: FREE (25GB + 25 RCU/WCU)
S3: $0.50 (5GB static files)
CloudFront: FREE (1TB transfer)
Total: ~$0.50/month
```

**Medium Usage (100K requests/month):**

```
API Gateway HTTP: $0.10
Lambda: $2.00
DynamoDB On-Demand: $8.75
S3: $2.30
CloudFront: $5.00
Total: ~$18.15/month
```

**High Usage (1M requests/month):**

```
API Gateway HTTP: $1.00
Lambda: $15.00
DynamoDB On-Demand: $87.50
S3: $5.00
CloudFront: $10.00
Total: ~$118.50/month
```

### Cost Optimization Strategies

**1. Lambda Optimization:**

```javascript
// Memory optimization
const OPTIMAL_MEMORY = 512; // Found via AWS Lambda Power Tuning

// Connection reuse
const dynamoDb = new AWS.DynamoDB.DocumentClient({
    httpOptions: { agent: new https.Agent({ keepAlive: true }) }
});

// Minimize cold starts
exports.handler = async (event) => {
    // Handler code
};
```

**2. DynamoDB Optimization:**

- Use on-demand for unpredictable workloads
- Implement item-level TTL for old data
- Optimize GSI projections
- Use batch operations

**3. Caching Strategy:**

```yaml
ApiGatewayMethod:
  Properties:
    CachingEnabled: true
    CacheTtlInSeconds: 300
    CacheKeyParameters:
      - method.request.querystring.appId
```

## 5. Scalability and Performance

### Auto-scaling Configuration

```yaml
# Lambda concurrent execution limits
ConcurrentExecutionLimit:
  CreateFunction: 100  # Prevent runaway costs
  ProcessFunction: 50   # Throttle background processing

# DynamoDB auto-scaling (if using provisioned)
ScalableTarget:
  Type: AWS::ApplicationAutoScaling::ScalableTarget
  Properties:
    MinCapacity: 5
    MaxCapacity: 1000
    TargetTrackingScalingPolicy:
      TargetValue: 70.0
      ScaleInCooldown: 60
      ScaleOutCooldown: 60
```

### Performance Optimizations

**1. Cold Start Mitigation:**

- Use Node.js or Python for fastest cold starts
- Keep deployment packages small (<10MB)
- Use Lambda Layers for dependencies
- Consider provisioned concurrency for critical paths

**2. Query Optimization:**

```javascript
// Efficient pagination
const params = {
    TableName: TABLE_NAME,
    KeyConditionExpression: 'PK = :pk',
    ExpressionAttributeValues: { ':pk': `APP#${appId}` },
    Limit: 50,
    ProjectionExpression: 'requestId, title, status, priority, votes'
};
```

**3. Caching Implementation:**

```javascript
// In-memory cache for Lambda
const cache = new Map();
const CACHE_TTL = 300000; // 5 minutes

function getCached(key) {
    const cached = cache.get(key);
    if (cached && Date.now() - cached.timestamp < CACHE_TTL) {
        return cached.data;
    }
    return null;
}
```

### Monitoring and Observability

```yaml
# CloudWatch Alarms
HighErrorRateAlarm:
  Type: AWS::CloudWatch::Alarm
  Properties:
    MetricName: Errors
    Statistic: Sum
    Period: 300
    EvaluationPeriods: 1
    Threshold: 10
    AlarmActions:
      - !Ref SNSAlertTopic

# X-Ray tracing
Tracing:
  Function:
    Tracing: Active
```

## Complete deployment example

```bash
# Clone repository
git clone https://github.com/your-org/serverless-feature-requests
cd serverless-feature-requests

# Install dependencies
npm install

# Deploy to AWS
sam build
sam deploy --guided \
  --stack-name feature-requests-prod \
  --parameter-overrides Environment=prod \
  --capabilities CAPABILITY_IAM

# Output will include:
# - API Gateway URL: https://abc123.execute-api.region.amazonaws.com/prod
# - CloudFront URL: https://d123456.cloudfront.net
# - S3 Bucket: feature-requests-prod-static
```

This architecture provides an ultra-low-cost serverless solution that scales automatically, supports multi-tenant isolation, and includes AI-friendly export capabilities. The pay-per-use model ensures costs remain minimal during low usage periods while supporting automatic scaling for growth.
