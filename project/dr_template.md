# Infrastructure

## AWS Zones
Identify your zones here
- Primary Zone :   ```us-east-2```
    - AZS : `us-east-2a`, `us-east-2b`, `us-east-2c`
- Secondary Zone : ```us-west-1```
    - AZS : `us-west-1a`, `us-west-1c`

## Servers and Clusters

### Table 1.1 Summary

| Asset                | Purpose                                                | Size        | Qty | DR                                            |
| -------------------- | ------------------------------------------------------ | ----------- | --- | --------------------------------------------- |
| S3 Bucket            | store terraform state configuration                    | -           | 2   | Replicated in DR Region                       |
| EC2 Instance         | Deploying flask web application                        | t3.micro    | 6   | 3 in each region                              |
| VPC                  | Provide private cloud in Multi-AZs with IP, Subnet etc | -           | 2   | Created in multiple region and deployed to DR |
| Prometheus/Grafana   | Monitoring Stack                                       | -           | 2   | created in each regions                       |
| ALB                  | Application Load Balancer                              | -           | 1   | Created in multiple region and deployed to DR |
| RDS Cluster          | Managed MySQL DB clusters                              | -           | 2   | 1 in each region                              |
| RDS Aurora Instances | Store application data                                 | db.t2.small | 4   | 2 in each region                              |

### Descriptions
More detailed descriptions of each asset identified above.
- **S3 Bucket**: These buckets will be used for storing the backups of db's and to store the state of our terraform code.
- **EC2 Instances**: These instances will be used for deploying our web application. 
- **VPC (Virtual Private Cloud)**: This provides a private cloud with IPs, Availability Zones, Subnets as a foundation for other services.
- **Prometheus/Grafana**: This will monitor application servers and will be used get the system matrix.
- **Load Balancer**: Load balancers will be used to distribute the load on application servers.
- **RDS Cluster**:  will be used to store our application data.
- **RDS Aurora Instances**: reader and writer instance which can be flipped over in event of fail-over.

## DR Plan
### Pre-Steps:
List steps you would perform to setup the infrastructure in the other region. It doesn't have to be super detailed, but high-level should suffice.

- create S3 buckets in regions to maintain the terraform state.
- use *Terraform* (IaC tool) to deploy infrastructure to multiple regions with the same configurations.
- Ensure the infrastructure is set up and working in the DR site.

## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region

- fail-over application
    - Change site DNS to point to Zone 2 load balancer instead of Zone 1
- fail-over database
    - fail-over in the primary writer instance of db cluster in the region will flip the writer instance with the reader instance
    - deleting the regional db cluster in primary region will promote the replica db cluster in secondary region as regional db cluster
