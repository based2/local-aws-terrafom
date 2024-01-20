# Local AWS infrastructure hosting simulator: AWS services on your laptop
## Why
* Align with the current hosting practices
* Learn infra hosting
* Learn Terraform tooling
* Test before deployment in rge Cloud
* Test migration to cloud architecture
* Check the limit
* Study cloud agnostic practices
* Cheaper
## What
* LocalStack python AWS simulator - Open source / Pro
  * LocalStack is a Python application designed to run as an HTTP request processor while listening on specific ports.
  * https://www.localstack.cloud/
* Terraform - Infrastructure as Code scripting tooling
  * https://www.terraform.io/
* Another interesting stack: https://www.openstack.org/ for Kubernetes

# Terraform - IaC: Infrastructure as Code
* Declarative language: resources in human-readable configuration files.
  * oriented towards difference reading
  * can use JSON and YAML within
* Provide access to service managements APIs: Cloud providers, SaaS and applications.
  * with Terraform providers: https://registry.terraform.io/
* Enable a workflow to create, update and delete configurations and resources
  * 1 Write - Define a resource in a Terraform file: S3.tf
    * new AWS S3 resource for storage
  * 2 Plan - Create a report of the resource to create, to update and to delete.
  * 3 Apply - Execute the proposed plan.
* Benefits
  * Structured and standardized definition of hosting projects
    * simpler reviews and sharing
    * capability to create modules and reuse existing modules
  * Enable capabilities to pre-validate the resources creation
    * security checks - checkov - https://www.checkov.io/
    * cost evaluation - infracost - https://github.com/infracost/infracost

https://developer.hashicorp.com/terraform/intro

 # LocalStack installation
 https://docs.localstack.cloud/getting-started/installation/
 https://github.com/localstack/localstack#installing

 Mode: Docker or Python

 Prerequisites
     AWS CLI
     Docker
     Terraform
     LocalStack

brew install python3
python3 -m pip install --upgrade localstack
localstack --version

https://docs.localstack.cloud/references/configuration/
DEBUG=1 localstack start
/home/username/.localstack
├── default.env
├── dev.env
└── pro.env
python -m localstack.cli.main --profile=dev start
python -m localstack.cli.main --profile=dev config show

docker-compose.yaml
version: "3.8"
services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME:-localstack-main}"
    image: localstack/localstack
    ports:
      - "127.0.0.1:4566:4566"            # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559"  # external services port range
    environment:
      # LocalStack configuration: https://docs.localstack.cloud/references/configuration/
      - DEBUG=${DEBUG:-0}
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"

version: '3.2'
services:
  localstack:
    image: localstack/localstack
    ports:
      - "4566:4566"
      - "4571:4571"
    environment:
      - SERVICES=s3,ec2,ecr
      - DEBUG=1
      - DATA_DIR=/tmp/localstack/data
    volumes:
      - "./tmp/localstack:/tmp/localstack"

docker-compose up


aws configure --profile localstack

aws s3 --endpoint-url http://localhost:4566 create-bucket mybucket

provider "aws" {
  endpoints {
    s3      = "http://localhost:4566"
    ec2     = "http://localhost:4566"
    ecr     = "http://localhost:4566"
  }
  region                      = "us-east-1"
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-bucket"
}

resource "aws_ec2_instance" "my_instance" {
  ami           = "ami-abcdef01"
  instance_type = "t2.micro"
}

resource "aws_ecr_repository" "my_repo" {
  name = "my-repo"
}

terraform init
terraform plan
terraform apply

resource "aws_s3_bucket" "s3_bucket" {
  bucket = format("%s-%s",var.bucket_name,var.env)
  acl    = "private"

  tags = {
    Name        = var.bucket_name
    Environment = var.env
  }
}

https://docs.localstack.cloud/references/podman/

https://docs.localstack.cloud/applications/

https://app.localstack.cloud/download
https://docs.localstack.cloud/user-guide/tools/localstack-desktop/


https://docs.localstack.cloud/references/coverage/

https://docs.localstack.cloud/user-guide/integrations/

https://blog.localstack.cloud/2023-11-16-announcing-localstack-30-general-availability/

https://reflectoring.io/aws-localstack/
> https://github.com/thombergs/code-examples/blob/master/aws/aws-terraform/aws-app-stack-cloud/modules/storage/main.tf

https://dev.to/marocz/my-journey-with-aws-localstack-and-terraform-a-local-aws-environment-setup-i50

Azure equiv

* Azure Functions Core Tools, a local version of the Azure Functions Runtime, allowing you to execute your Azure functions locally without deploying them.
  * https://github.com/Azure/azure-functions-core-tools
* Azure Storage Emulator, a local emulator of Azure storage.
  * https://learn.microsoft.com/en-us/azure/storage/common/storage-use-emulator
  * https://github.com/azure/azurite A lightweight server clone of Azure Storage, TypeScript, MIT
* Cosmos DB Emulator, a local emulator of CosmosDB.
  * https://learn.microsoft.com/en-us/azure/cosmos-db/local-emulator


src: https://stackoverflow.com/questions/53888830/local-cloud-stack-for-azure-similar-to-localstack-for-aws




