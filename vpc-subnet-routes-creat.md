# vpc create 
aws ec2 create-vpc --cidr-block 10.10.0.0/16 \
	--tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=Ashish-vpc}]' \
	--output text --query Vpc.VpcId

# vpc create and copy vpc_id
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.10.0.0/16 \
	--tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=AWSCookbook201}]' \
	--output text --query Vpc.VpcId)
	

# describe vpc and filter for values
aws ec2 describe-vpcs --vpc-ids $VPC_ID --query Vpcs[0].CidrBlockAssociationSet[0].CidrBlock

# describe all vpcs for region exported in shell (AWS_REGION env variable) 
aws ec2 describe-vpcs 

# associate cidr block to existing vpc
aws ec2 associate-vpc-cidr-block --cidr-block 10.11.0.0/16 --vpc-id $VPC_ID

# delete VPC
aws ec2 delete-vpc --vpc-id $VPC_ID


## find out availability zones
aws ec2 describe-availability-zones --region $AWS_REGION


## Route table created
ROUTE_TABLE_ID=$(aws ec2 create-route-table --vpc-id $VPC_ID \
	--tag-specifications \
'ResourceType=route-table,Tags=[{Key=Name,Value=Ashish-route-table}]' \
	--output text --query RouteTable.RouteTableId)
	
## create two subnets
SUBNET_ID_1=$(aws ec2 create-subnet --vpc-id $VPC_ID \
	--cidr-block 10.10.0.0/24 --availability-zone ${AWS_REGION}a \
	--tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=Ashish-subnet-1}]' \
	--output text --query Subnet.SubnetId)
	
SUBNET_ID_2=$(aws ec2 create-subnet --vpc-id $VPC_ID \
	--cidr-block 10.10.1.0/24 --availability-zone ${AWS_REGION}b \
	--tag-specifications \
'ResourceType=subnet,Tags=[{Key=Name,Value=Ashish-subnet-2}]' \
	--output text --query Subnet.SubnetId)
	
## Associate route table
## It is good idea to have route table for every AZ
aws ec2 associate-route-table \
	--route-table-id $ROUTE_TABLE_ID --subnet-id $SUBNET_ID_1
aws ec2 associate-route-table \
	--route-table-id $ROUTE_TABLE_ID --subnet-id $SUBNET_ID_2


## Describe subnets
aws ec2 describe-subnets --subnet-ids $SUBNET_ID_1


# Cleanup
# delete subnets
aws ec2 delete-subnet --subnet-id $SUBNET_ID_1
aws ec2 delete-subnet --subnet-id $SUBNET_ID_2

# delete route table
aws ec2 delete-route-table --route-table-id $ROUTE_TABLE_ID

# delet vpc
aws ec2 delete-vpc --vpc-id $VPC_ID
