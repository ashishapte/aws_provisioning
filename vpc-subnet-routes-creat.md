# vpc create 
aws ec2 create-vpc --cidr-block 10.10.0.0/16 \
	--tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=Ashish}]' \
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