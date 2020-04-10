# aws-grafana-influxdb-ecs-cfn
AWS CloudFormation templates to deploy ECS service containing Grafana and InfluxDB

## Deployment:
Deploys the following:
- Deploys ECS cluster and a container instance connected to the cluster.
- ECS container instance pulls container configuration files from S3 bucket.
- Deploys ECS Service containing Grafana and InfluxDB containers.

## Steps:
- Create an S3 bucket with name say influxdb-grafana-config.
- Push the configuration files in the folder influxdb-grafana-config to S3 bucket.
```
cd influxdb-grafana-config
aws s3 sync . s3://influxdb-grafana-config
```
- Deploy the CloudFormation Template cfn-template.yaml.
    - NOTE: Based on the region that you are deploying the stack in, you will need to update the mapping
    RegionECSOptimizedAMI with AMI id for the relevant region.
    - If you choose a different bucket to deploy the configuration files to, then update the value of param
    ServerConfigBucketName. Else, leave the default value.
    - Type in the name of the ECS Container Instance type.
    - Enter the IP address range (CIDR) to whom you want to provide access to. This creates a Security Group for the
    Container Instance for ports 22, 443 and 80.
    - Select the VPC and Subnet where you want the deployment.
- Once the deployment is complete, with default settings, you can access the Grafana UI on port 443 of the container
instance. InfluxDB is on port 80.
- All the influxDB data is mounted to mount influxdb-data. For persistency and applications requiring to store huge
quantities of data, mount an EBS volume to the same directory as the mount.
    - If there is a failure, and another container is brought up, then the data is not lost.
    - If there is a new deployment, then data can be moved over to the new deployment easily by attaching the EBS
    volumne to the container instance of new deployment, and mounting to the data drive.