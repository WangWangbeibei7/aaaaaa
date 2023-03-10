1.import key-pari 
 npm i @aws-cdk/aws-ec2 @aws-cdk/aws-iam @aws-cdk/aws-s3-assets cdk-ec2-key-pair 
 
2.key-pari save 
 aws secretsmanager get-secret-value \ --secret-id ec2-ssh-key/nakagaki/private \ --query SecretString \ --output text > xxxx.pem 
