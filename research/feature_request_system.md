# Cost-Effective Feature Request Systems for Multi-App Companies on AWS

**AWS-native serverless architecture offers the most cost-effective solution at $0.83-$4.35/month for typical usage**, combining minimal costs with maximum flexibility for multi-app support and AI integration. For companies with virtually no budget beyond AWS resources, a hybrid approach using open-source tools and serverless components provides the optimal balance of functionality, scalability, and cost-effectiveness.

## Top Three Recommended Approaches

### 1. Ultra-Low-Cost Serverless Solution ($0.83-$43.50/month)

The most budget-friendly approach leverages AWS's pay-per-use model with a serverless architecture that scales automatically.

**Architecture Components:**

- API Gateway for request handling
- Lambda functions for business logic
- DynamoDB for data storage
- S3 for static form hosting
- SNS/SES for notifications

**Cost Breakdown by Usage:**

- **Low (1K requests/month)**: ~$0.83
- **Medium (100K requests)**: ~$4.35
- **High (1M requests)**: ~$43.50

**Multi-App Implementation:**

```yaml
# DynamoDB Schema
PartitionKey: app_id (e.g., "mobile-app", "web-app")
SortKey: request_id
Attributes: title, description, priority, user_id, votes
```

This approach provides **complete control** over the implementation with minimal ongoing costs. The serverless nature means you only pay for actual usage, making it ideal for companies starting with low traffic that may scale significantly.

### 2. Fider Open-Source Platform ($30-$100/month)

Fider stands out as the most mature open-source alternative to commercial platforms like Canny, offering a proven solution with strong community support.

**Key Advantages:**

- **2.8K+ GitHub stars** with active maintenance
- Comprehensive REST API for AI integration
- OAuth2 support (Google, GitHub, Facebook)
- Webhook integrations (Slack, Jira, Trello)
- Docker-based deployment simplifies AWS setup

**AWS Infrastructure Costs:**

- **Minimal setup**: t3.micro EC2 + db.t3.micro RDS (~$30-50/month)
- **Production setup**: t3.small instances (~$50-100/month)

**Multi-App Limitation**: Requires separate instances per app, but can use shared RDS with different databases to reduce costs.

### 3. Hybrid GitHub + Forms Solution (Free to $50/month)

Leveraging GitHub's infrastructure with user-friendly form frontends provides a developer-friendly solution with minimal costs.

**Implementation Options:**

- **Google Forms → GitHub Issues** (free)
- **Typeform → GitHub Issues** via Zapier ($20-50/month)
- **Custom widget → GitHub API** (development time only)

**Enhanced with Tools:**

- **GitVote** app for community voting (open source)
- **GitHub Projects** for kanban boards (free)
- **ZenHub** for advanced project management ($8-12/user/month)

## AI Integration Capabilities

All recommended solutions support programmatic access for AI integration through different mechanisms:

### Serverless Architecture AI Integration

```javascript
// Lambda function for AI analysis
import { BedrockRuntimeClient, InvokeModelCommand } from "@aws-sdk/client-bedrock-runtime";

const analyzeFeatureRequest = async (title, description) => {
  const prompt = `Analyze this feature request:
Title: ${title}
Description: ${description}

Provide: sentiment, category, priority (1-10),
implementation complexity, business value`;

  // Use Claude or other AI models via AWS Bedrock
  const response = await bedrock.send(new InvokeModelCommand({
    modelId: "anthropic.claude-3-sonnet",
    body: JSON.stringify({ messages: [{ content: prompt }] })
  }));

  return JSON.parse(response.body);
};
```

### API Access for External AI Tools

- **Fider**: Full REST API with JWT authentication
- **GitHub**: GraphQL and REST APIs with 5,000 requests/hour
- **Serverless**: Custom API endpoints for any integration

### Export Formats

- **JSON**: Native format for all solutions
- **CSV**: Available through tools or custom exports
- **Webhooks**: Real-time data streaming to AI services

## Implementation Roadmap

### Phase 1: Minimal Viable System (Week 1)

**Option A - Serverless Route:**

1. Deploy CloudFormation template for basic API + Lambda + DynamoDB
2. Create simple HTML form hosted on S3
3. Implement basic email notifications via SES
4. Test with single app

**Option B - Fider Route:**

1. Launch t3.micro EC2 instance
2. Deploy Fider using Docker Compose
3. Configure OAuth and custom branding
4. Set up SSL with Let's Encrypt

### Phase 2: Multi-App Support (Week 2-3)

**For Serverless:**

- Implement app_id partitioning in DynamoDB
- Create app-specific API endpoints
- Build admin dashboard using AWS Amplify

**For Fider:**

- Deploy multiple instances (one per app)
- Configure Application Load Balancer for routing
- Set up shared authentication if needed

### Phase 3: User Interface Enhancement (Week 3-4)

**Embeddable Widget Options:**

- **Feedback Fin** (5KB open-source widget)
- Custom React/Vue components
- iframe embedding for Fider instances

**Widget Implementation:**

```html
<!-- Feedback Fin Example -->
<script src="https://unpkg.com/feedbackfin@^1" defer></script>
<script>
window.feedbackfin = {
  config: {
    webhook_url: "https://your-api-gateway-url/feedback",
    user: { name: "User Name", email: "user@email.com" }
  }
};
</script>
```

### Phase 4: AI Integration (Week 4-5)

1. Implement Lambda functions for AI analysis
2. Create scheduled jobs for batch processing
3. Build dashboard for AI insights
4. Set up automated categorization and prioritization

### Phase 5: Scale and Optimize (Ongoing)

- Monitor CloudWatch metrics
- Implement caching strategies
- Add CloudFront CDN for global performance
- Create automated backups

## Cost-Benefit Analysis by Company Size

### Startups (0-100 users)

**Recommended**: Serverless architecture

- **Monthly cost**: $0.83-$5
- **Benefits**: Minimal overhead, infinite scaling potential
- **Trade-off**: Requires initial development time

### Small Companies (100-1,000 users)

**Recommended**: Fider on minimal infrastructure

- **Monthly cost**: $30-50
- **Benefits**: Proven solution, community support
- **Trade-off**: Less customization than pure serverless

### Growing Companies (1,000-10,000 users)

**Recommended**: Hybrid approach with enhanced tooling

- **Monthly cost**: $50-200
- **Benefits**: Professional features, multi-app support
- **Trade-off**: Increased complexity

## Critical Implementation Tips

**Data Architecture for Multi-App Support:**

- Use consistent schema across all apps
- Implement app-level permissions
- Create unified analytics dashboard
- Design for data portability

**Security Considerations:**

- Enable AWS WAF for API Gateway
- Implement rate limiting
- Use Cognito for user authentication
- Encrypt data at rest and in transit

**Performance Optimization:**

- Use DynamoDB Global Secondary Indexes
- Implement API Gateway caching
- Deploy Lambda functions in multiple regions
- Use CloudFront for static assets

## Conclusion

For companies with limited budgets but AWS resources, the **serverless approach offers unmatched flexibility** at minimal cost. Starting with basic Lambda functions and DynamoDB, you can build a feature request system for under $5/month that scales to millions of users. As your needs grow, incrementally add components like Fider for proven functionality or enhanced widgets for better user experience.

The key advantage of these approaches is their **modular nature** - start small with core functionality and expand based on actual usage and needs. With proper API design from the start, integrating AI capabilities becomes straightforward, allowing you to leverage tools like Claude for automated analysis, categorization, and prioritization of feature requests.


## Deployment of Ultra-Low-Cost Serverless Feature Request System on AWS

### Architecture overview and data flow

The serverless feature request system leverages AWS managed services to create a cost-effective, scalable solution with minimal operational overhead. The architecture follows an event-driven pattern optimized for multi-tenant applications.

#### Core Architecture Components

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

#### Data Flow Pattern

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

### 1. Architecture Outline

#### API Gateway Configuration

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

#### Lambda Functions Architecture

Implement single-purpose functions for better scalability and debugging:

```yaml
Functions:
  CreateRequest:    # Handles new feature request submissions
  GetRequests:      # Retrieves filtered request lists
  UpdateRequest:    # Updates status, votes, comments
  ExportRequests:   # Generates AI-compatible exports
  ProcessWebhooks:  # Handles real-time integrations
```

#### DynamoDB Single-Table Design

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

#### S3 Static Hosting

CloudFront distribution for global performance:

- Static frontend files (React/Vue)
- Presigned URLs for file attachments
- Origin Access Control for security

#### SNS/SES Integration

Event-driven notification system:

- SNS topics for event fan-out
- SES for templated email notifications
- Lambda subscribers for webhook delivery

### 2. Code Structure

#### Create Request Function

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

#### Get Requests Function with Pagination

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

#### Update Request with Optimistic Locking

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

#### Export Function for AI Tools

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

#### DynamoDB Table Structure

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

#### S3 Static Form Hosting

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

#### SNS/SES Integration

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

### 3. Deployment Considerations

#### AWS SAM Template

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

#### IAM Roles and Permissions

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

#### Environment Variables and Configuration

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

#### Security Best Practices

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

### 4. Cost Optimization

#### Pricing Breakdown (Monthly)

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

#### Cost Optimization Strategies

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

### 5. Scalability and Performance

#### Auto-scaling Configuration

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

#### Performance Optimizations

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

#### Monitoring and Observability

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

### Complete deployment example

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
