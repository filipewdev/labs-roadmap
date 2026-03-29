# AWS Mapping Reference

> For each tool used in the labs, this document shows: what it does,
> its AWS managed equivalent, and what your SAA exam should know about it.
> Study this alongside the labs — when you configure RabbitMQ locally,
> you are learning the concepts that SQS and SNS are built on.

---

## The Core Idea

Every lab tool has an AWS equivalent. The difference is:

- **Lab tool**: you manage the server, config, upgrades, backups, networking
- **AWS managed service**: AWS manages all that; you pay for convenience and scale

Understanding the lab tool deeply makes you a better architect of the AWS equivalent —
because you know what's happening underneath the managed abstraction.

---

## Networking (directly relevant to your networking studies)

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| Nginx (reverse proxy) | **ALB** (Application Load Balancer) | Layer 7 routing, path-based rules, host-based routing, SSL termination |
| Nginx (static files) | **S3 + CloudFront** | Origin + distribution, cache behaviors, TTL, OAI/OAC |
| HAProxy | **NLB** (Network Load Balancer) | Layer 4, TCP/UDP, static IPs, ultra-low latency |
| Self-managed Nginx cluster | **ALB + Auto Scaling Group** | Health checks → ASG replaces unhealthy instances |
| Nginx rate limiting | **WAF** (Web Application Firewall) | Rate-based rules, IP sets, managed rule groups |
| Let's Encrypt / Certbot | **ACM** (Certificate Manager) | Free certs, auto-renewal, integrates with ALB/CloudFront |
| DNS (local) | **Route 53** | Hosted zones, record types, routing policies (weighted, latency, failover, geolocation) |
| VPN / private network | **VPC** | Subnets (public/private), route tables, IGW, NAT Gateway, security groups vs NACLs |

**SAA exam networking concepts to know cold:**

```
VPC CIDR block → subnets (public has route to IGW, private has route to NAT GW)
Security group = stateful (response traffic auto-allowed)
NACL = stateless (must allow inbound AND outbound explicitly)
VPC Peering vs Transit Gateway (many VPCs)
PrivateLink vs VPC Peering (service vs network)
Route 53 routing policies — know all 6 types and when to use each
ALB vs NLB vs CLB — the key differences
```

---

## Compute & Containers

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| Docker (single container) | **ECS** (Fargate launch type) | Task definitions, services, serverless containers |
| Docker Compose | **ECS** (EC2 launch type) or **EKS** | Multi-container, service discovery, load balancer integration |
| Raspberry Pi server | **EC2** | Instance types, AMIs, placement groups, instance store vs EBS |
| Multi-arch Docker builds | **Graviton instances** (ARM) | Graviton3 = 40% better price/performance than x86 for many workloads |
| Dockerfile | **ECR** (Elastic Container Registry) | Image storage, lifecycle policies, vulnerability scanning |
| systemd service | **ECS Service** | Desired count, rolling deployments, circuit breaker |
| GitHub Actions CI | **CodePipeline + CodeBuild** | Source → Build → Deploy pipeline |

**SAA exam compute concepts:**

```
EC2 purchasing options: On-Demand, Reserved (1/3yr), Savings Plans, Spot, Dedicated
ECS vs EKS — when to recommend each
Fargate = serverless containers (no EC2 management)
Lambda = serverless functions (event-driven, 15min max)
Auto Scaling: target tracking vs step vs scheduled
```

---

## Databases

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| PostgreSQL (self-managed) | **RDS PostgreSQL** | Multi-AZ, read replicas, automated backups, parameter groups |
| PostgreSQL (high availability) | **Aurora PostgreSQL** | 6-copy replication across 3 AZs, serverless v2 option |
| PgBouncer | **RDS Proxy** | Connection pooling managed by AWS, IAM auth for Lambda |
| PostgreSQL read replica | **Aurora Global Database** | Cross-region read replicas, < 1s replication lag |
| MongoDB | **DocumentDB** | MongoDB-compatible (not identical), cluster-based |
| Redis | **ElastiCache for Redis** | Cluster mode, replication groups, node types |
| Redis (simpler) | **ElastiCache for Memcached** | Multi-threaded, simpler, no persistence |
| Time-series PostgreSQL | **Timestream** | Purpose-built, auto-scaling, SQL-ish query language |

**SAA exam database concepts:**

```
RDS Multi-AZ: standby in another AZ, sync replication, automatic failover (~1-2 min)
RDS Read Replica: async replication, can be cross-region, can be promoted
Aurora: faster than RDS, storage auto-grows to 128TB, up to 15 read replicas
DynamoDB: key-value + document, serverless, single-digit ms, GSI/LSI
DynamoDB vs RDS: when relational structure and ACID matter → RDS; massive scale + simple access patterns → DynamoDB
ElastiCache: sits in front of DB (lazy loading) or application (write-through)
```

---

## Async Messaging

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| RabbitMQ | **SQS** (Standard or FIFO) | Decoupling, dead letter queues, visibility timeout, message retention |
| RabbitMQ topic exchange | **SNS** (fan-out to multiple SQS) | SNS → multiple SQS subscriptions = fan-out pattern |
| Kafka | **MSK** (Managed Streaming for Kafka) | Same Kafka API, managed brokers, MSK Connect |
| Kafka | **Kinesis Data Streams** | AWS-native alternative, shards = partitions, 24h–365d retention |
| MQTT broker (Mosquitto) | **IoT Core** | MQTT at scale, device shadows, rules engine, triggers Lambda |

**SAA exam messaging concepts:**

```
SQS Standard: at-least-once, best-effort ordering, up to 256KB
SQS FIFO: exactly-once, strict ordering, 300 TPS (3000 with batching)
Dead Letter Queue: max receive count → route to DLQ (same concept as lab)
SNS + SQS fan-out: one SNS topic → multiple SQS queues = parallel processing
Kinesis vs SQS: real-time analytics → Kinesis; decoupled workers → SQS
EventBridge: event bus, schema registry, replaces CloudWatch Events
```

---

## Observability

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| Prometheus | **CloudWatch Metrics** | Custom metrics, namespaces, dimensions, metric math |
| Grafana | **CloudWatch Dashboards** or **Amazon Managed Grafana** | Pre-built dashboards, cross-account, queries CloudWatch |
| Loki | **CloudWatch Logs** | Log groups, log streams, log insights queries, metric filters |
| Promtail | **CloudWatch Agent** or **Fluent Bit** | Ships logs/metrics from EC2/ECS to CloudWatch |
| OpenTelemetry | **AWS X-Ray** | Distributed tracing, service maps, sampling |
| Alertmanager | **CloudWatch Alarms** | Metric threshold → SNS → email/Lambda/etc |

**SAA exam observability concepts:**

```
CloudWatch: metrics (1min granularity, 15min default), alarms (OK/ALARM/INSUFFICIENT)
CloudWatch Logs Insights: SQL-like queries over logs
X-Ray: trace requests across Lambda, API Gateway, ECS, DynamoDB
CloudTrail: API call audit log (who called what API, when, from where)
VPC Flow Logs: network traffic in/out of ENIs (security analysis)
```

---

## API Gateway & Security

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| Kong | **API Gateway** (REST or HTTP) | Routes, stages, throttling, caching, authorizers |
| Kong JWT plugin | **API Gateway Lambda Authorizer** | Custom auth logic in Lambda before request reaches backend |
| Kong rate limiting | **API Gateway usage plans** | API keys, throttle (burst + rate), quotas |
| n8n | **Step Functions** | Visual state machines for workflows; or **EventBridge Pipes** |
| JWT auth | **Cognito** | User pools (auth), identity pools (AWS credential federation) |
| OAuth 2.0 | **Cognito hosted UI** | Social login, SAML/OIDC federation |

**SAA exam security concepts:**

```
IAM: users, roles, policies (identity-based vs resource-based)
STS: temporary credentials via AssumeRole
Cognito User Pools: sign-up/sign-in, MFA, JWT tokens
Cognito Identity Pools: swap Cognito/social token for temporary AWS credentials
KMS: key management, envelope encryption, automatic key rotation
Secrets Manager vs Parameter Store: SM auto-rotates, PS cheaper for non-secrets
WAF: L7 protection, integrates with CloudFront, ALB, API Gateway
Shield: DDoS protection (Standard free, Advanced paid)
```

---

## Storage

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| Local filesystem | **EBS** | Block storage, attached to one EC2, multiple volume types (gp3, io2, st1) |
| NFS share | **EFS** | Managed NFS, multi-AZ, mounts to multiple EC2 simultaneously |
| Backups to cloud storage | **S3** | Object storage, 11 9s durability, storage classes |
| Offsite backup | **S3 Glacier** | Archival, retrieval minutes to hours, Glacier Instant/Flexible/Deep |

**SAA exam storage concepts:**

```
S3 storage classes: Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier Instant, Flexible, Deep Archive
S3 lifecycle rules: transition between classes, expire objects
EBS vs EFS vs S3: block vs file vs object storage
EBS gp3 vs io2: throughput/IOPS tradeoffs
Multi-part upload for > 100MB objects
S3 Transfer Acceleration: CloudFront edge → S3 (faster for long distance)
```

---

## IoT / Edge

| Lab tool | AWS equivalent | SAA exam relevance |
|---|---|---|
| MQTT + Mosquitto | **IoT Core** | Device connections, message broker, rules engine |
| Raspberry Pi running model | **Greengrass** (edge Lambda) | Run Lambda/containers on-device, sync with cloud |
| Local YOLO inference | **Rekognition** | Managed image/video analysis API (no model management) |
| Pi as a gateway | **IoT Core Device Gateway** | MQTT + WebSocket, millions of devices |

---

## Your AWS SAA Exam Quick Wins

Things that come up repeatedly in SAA questions:

1. **High availability = Multi-AZ**. DR = cross-region. Know the difference.
2. **Stateless + Auto Scaling Group** is the answer to 80% of scalability questions.
3. **S3 is not a filesystem** — it's object storage. Cannot be mounted like EBS.
4. **Security groups are stateful; NACLs are stateless.** Write this on your scratch paper.
5. **Lambda max 15 minutes** — if the task takes longer, use ECS/Fargate.
6. **SQS decouples** — if the question mentions "spikes" or "async", SQS is probably involved.
7. **CloudFront caches** — if the question mentions "reduce latency globally", CloudFront.
8. **RDS Multi-AZ is for availability, read replicas are for performance.** Not interchangeable.
9. **IAM roles for EC2** — never store AWS credentials on an instance. Attach a role.
10. **Encryption at rest** = KMS. **Encryption in transit** = TLS/HTTPS. Know which services support each.

---

## Recommended Study Pairing

| Lab | Study this AWS topic alongside it |
|---|---|
| Lab 04 (WebSocket / Go networking) | VPC fundamentals: subnets, IGW, security groups, ENIs |
| Lab 05 (Nginx) | ALB: listeners, target groups, path routing, health checks |
| Lab 06 (Prometheus + Grafana) | CloudWatch: metrics, alarms, dashboards, Managed Grafana |
| Lab 08 (PostgreSQL) | RDS: Multi-AZ, read replicas, RDS Proxy, Aurora |
| Lab 09 (Redis) | ElastiCache: Redis cluster mode, replication groups |
| Lab 11 (RabbitMQ) | SQS: standard vs FIFO, DLQ, visibility timeout |
| Lab 12 (Kafka) | MSK + Kinesis: shards vs partitions, consumer groups |
| Lab 13 (Container internals) | ECS task definitions, Fargate CPU/memory limits (← cgroups!) |
| Lab 14 (Docker Compose) | ECS services, ALB integration, service discovery |
| Lab 15 (Auth/JWT) | Cognito User Pools, Lambda Authorizers, API Gateway |
| Lab 16 (Kong) | API Gateway: stages, throttling, usage plans, authorizers |
| Lab 18 (Linux admin) | EC2: AMIs, user data scripts, Systems Manager (SSM) |
| Lab 24 (Arduino + MQTT) | IoT Core: device registry, topics, rules engine |
