# Kubernetes on AWS using Kops
# 1. Launch Linux EC2 instance in AWS (Kubernetes Client)
# 2. Create and attach IAM role to EC2 Instance.
	Kops need permissions to access
	  S3
	  EC2
	  VPC
	  Route53
	  Autoscaling
	  etc..
# 3. Install Kops on EC2
	curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
	chmod +x kops-linux-amd64
	sudo mv kops-linux-amd64 /usr/bin/kops
# 4. Install kubectl
	curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
	chmod +x ./kubectl
	mkdir -p $HOME/bin
	cp ./kubectl $HOME/bin/kubectl
	export PATH=$HOME/bin:$PATH
	echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
	source $HOME/.bashrc
	kubectl version --short --client
# 5. Create S3 bucket in AWS
S3 bucket is used by kubernetes to persist cluster state, lets create s3 bucket using aws cli Note: Make sure you choose bucket name that is uniqe accross all aws accounts

	aws s3 mb s3://cloudtechmasters.ml.k8s --region us-east-1
# 6. Create hosted zone in AWS Route53
	Head over to aws Route53 and create hostedzone
	Choose name for example (cloudtechmasters.ml)
	Hit create
# 7. Create ssh key pair
This keypair is used for ssh into kubernetes cluster

	ssh-keygen
# 8. Create a Kubernetes cluster definition
	kops create cluster --zones=us-east-1a,us-east-1b,us-east-1c --name=cloudtechmasters.ml
# 9. Create kubernetes cluster
	kops update cluster --name cloudtechmasters.ml --yes
Above command may take some time to create the required infrastructure resources on AWS. Execute the validate command to check its status and wait until the cluster becomes ready

	kops validate cluster
For the above above command, you might see validation failed error initially when you create cluster and it is expected behaviour, you have to wait for some more time and check again.

# 10. Check nodes of the cluster
	kubectl get nodes
