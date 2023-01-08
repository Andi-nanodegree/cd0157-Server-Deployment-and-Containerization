test

eksctl create cluster --name simple-jwt-api --nodes=2 --version=1.24 --instance-types=t2.medium --region=us-east-2

kubectl get nodes

aws sts get-caller-identity --query Account --output text

eksctl delete cluster simple-jwt-api  --region=us-east-2

Fetch - Get the current configmap and save it to a file:
# Windows - The file will be created in the current working directory
kubectl get -n kube-system configmap/aws-auth -o yaml > aws-auth-patch.yml

Edit - Open the aws-auth-patch.yml file using any editor, such as VS code editor:
# Windows
code aws-auth-patch.yml

Add the following group in the data → mapRoles section of this file. YAML is indentation-sensitive, therefore refer to the snapshot below for a correct indentation:
 -   mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::133638015127:role/eksctl-simple-jwt-api-nodegroup-n-NodeInstanceRole-1HC46CZXPZ94S
      username: system:node:{{EC2PrivateDNSName}}
    - groups:
      - system:masters
      rolearn: arn:aws:iam::133638015127:role/UdacityFlaskDeployCBKubectlRole
      username: build      
Don't forget to replace the <ACCOUNT_ID> with your AWS account Id. Do not copy-paste the code snippet from above. Instead, look at this sample aws-auth-patch.yml file and the snapshot below to stay careful with the indentations.
Screenshot of the file aws-auth-patch.yml in the editor, with the mapRoles section highlighted in yellow. - groups is indented more than mapRoles, and - system:masters is indented one more level.
File aws-auth-patch.yml in the editor. Notice the indentation of the highlighted part.

Update - Update your cluster's configmap:
# Windows
kubectl patch configmap/aws-auth -n kube-system --patch "$(cat aws-auth-patch.yml)"

The command above must show you configmap/aws-auth patched as a response.
Troubleshoot - In case of the following error, re-run the above three steps beginning from the kubectl get command.
Error from server (Conflict): Operation cannot be fulfilled on configmaps "aws-auth": the object has been modified; please apply your changes to the latest version and try again

Check the health of your clusters nodes:
kubectl get nodes
Get the cluster node status
Get the cluster node status


GITHUB token! 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

Delete variable from AWS parameter store:
aws ssm delete-parameter --name JWT_SECRET