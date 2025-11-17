# DevOps Bootcamp
## By TechWorld with Nana

## Demo-01 - Automate AWS Infrastructure - Part 1

<details>
<summary>Prerequisites</summary>

The project files used in this demo can be found here:
https://gitlab.com/twn-devops-bootcamp/latest/12-terraform/terraform-learn

</details>

---

<details>
<summary>VPC & Subnet</summary>

1. On the project's folder, rename/create a new terraform variables file named "terraform.tfvars"
2. On the main.tf file, rename the variables, and update their references:

```
variable vpc_cidr_block {}
variable subnet_cidr_block {}
variable avail_zone {}
variable env_prefix{}
```
3. Update the "Name" tags with the new variable:

```
  tags = {
    Name: "${var.env_prefix}-<resource type>"
  }
```
4. Assign values to the variables in the terraform.tfvars file:

```
vpc_cidr_block = "10.0.0.0/16"
subnet_cidr_block = "10.0.10.0/24"
avail_zone = "you prefered AWS Availability Zone"
env_prefix = "dev"
```
5. Add the region to the provider block:

```
provider "aws" {
  region = <your prefered AWS Region>
}
```
6. Create the above infrastructure using terraform

```
terraform plan
terrafom apply --auto-approve
```
</details>

---

<details>
<summary>Route Table & Internet Gateway</summary>

1. On main.tf add a new Route Table Resouce

```
resource "aws_route_table" "myapp-route-table" {
  vpc_id = aws_vpc.myapp-vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.myapp-igw.id
  }
  tags = {
    Name: "${var.env_prefix}-rtb"
  }
}
```
2. On main.tf add a new Internet Gateway Resource

```
resource "aws_internet_gateway" "myapp-igw" {
  vpc_id = aws_vpc.myapp-vpc.id
  tags = {
    Name: "${var.env_prefix}-igw"
  }
}
```
3. Create the above components using terraform

```
terraform plan
terrafom apply --auto-approve
```
</details>

---

<details>
<summary>Subnet Association with Route Table</summary>

1. On main.tf file add a subnet association with route table Resource

```
resource "aws_route_table_association" "a-rtb-subnet-1" {
  subnet_id      = aws_subnet.myapp-subnet-1.id
  route_table_id = aws_route_table.myapp-route-table.id
}
```
2. Create the above component using terraform

```
terraform plan
terrafom apply --auto-approve
```
</details>

---

<details>
<summary>Use the Main Route Table</summary>

1. On main.tf file, remove the route table and the association resources
2. On main.tf file, add a new resource of type "aws_default_route_table"

```
resource "aws_default_route_table" "main-rtb" {
  default_route_table_id = aws_vpc.myapp-vpc.default_route_table_id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.myapp-igw.id
  }

  tags = {
    Name = "${var.env_prefix}-main-rtb"
  }
}
```
3. Create the above component using terraform

```
terraform plan
terrafom apply --auto-approve
```
</details>

---

<details>
<summary>Security Group</summary>

1. On main.tf file, add a Security Group Resource

```
resource "aws_security_group" "myapp-sg" {
  name        = "myapp-sg"
  vpc_id      = aws_vpc.myapp-vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.my_ip]
  }

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
    prefix_list_ids = []
  }

  tags = {
    Name = "${var.env_prefix}-sg"
  } 
}
```
2. Create the above component using terraform

```
terraform plan
terrafom apply --auto-approve
```
</details>

---

<details>
<summary>Use Default Security Group</summary>

1. On main.tf file, modify the security group resource to be of type "aws_default_security_group"

```
resource "aws_default_security_group" "default-sg" {
  vpc_id      = aws_vpc.myapp-vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.my_ip]
  }

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
    prefix_list_ids = []
  }

  tags = {
    Name = "${var.env_prefix}-default-sg"
  } 
}
```
2. Create the above component using terraform

```
terraform plan
terrafom apply --auto-approve
```
</details>

---

<details>
<summary>Prerequisites</summary>


</details>