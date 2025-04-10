# migration of GCP VM to AWS using AWS Migration Service

Step 1:
Create Public key on local putty gen , keygen multiple options

Step 2: 
Add Public key in GCP VM and Save Private in your local

Step 3:
Go to GCP VM edit section and look for SSH keys and create SSH public key 


<img width="772" alt="image" src="https://github.com/user-attachments/assets/1d7eef86-1637-4d1a-a881-da1dfd66f8cb" />


Step 4:
Try accessing GCP from ssh -i <pem path> name@PublicIP_GCP_VM


Step 5:
Move to AWS and access AWS Migration Service


<img width="1460" alt="image" src="https://github.com/user-attachments/assets/01110e24-a369-4a9b-bd29-80dbd863ed2b" />


Step 6:
Check replication and launch template according to you requirements 

I am using default for everything for my POC

<img width="1099" alt="image" src="https://github.com/user-attachments/assets/991f3329-45f5-4cf7-beff-087628cfdc46" />


In lauch template i uncheck General launch settings > Activate instance type right-sizing and choose t3.medium and save template

<img width="1299" alt="image" src="https://github.com/user-attachments/assets/8a5cf6bc-83e5-4dc1-8951-f4f768690044" />


Step 7:
Check Post-launch template  and enable Install the Systems Manager agent and allow executing actions on launched servers if you are Prod

(i have disabled it as i will access it with SSH using Public key)


<img width="1581" alt="image" src="https://github.com/user-attachments/assets/9538f36a-7df2-4247-8097-f644756ed168" />


Step 8:
Now go to IAM and create IAM user with access key and secret access key
(Attach permission : AWSApplicationMigrationAgentinstallationPolicy)


<img width="1392" alt="image" src="https://github.com/user-attachments/assets/4b13ec2f-1cba-4d1c-8183-ad478d1303d5" />



Step 9:
Once create go to AWS migration and add access secret keys


<img width="1208" alt="image" src="https://github.com/user-attachments/assets/d35b1c3e-18e7-4e04-88b0-4e8a3db05ca6" />

Step 10:
Now we need to run the below commands one by one in GCP VM

sudo wget -O ./aws-replication-installer-init https://aws-application-migration-service-ap-southeast-1.s3.ap-southeast-1.amazonaws.com/latest/linux/aws-replication-installer-init
sudo chmod +x aws-replication-installer-init; sudo ./aws-replication-installer-init --region ap-southeast-1 --aws-access-key-id <your_access_key> --aws-secret-access-key <your_secret_key> --no-prompt



<img width="1667" alt="image" src="https://github.com/user-attachments/assets/c3c527c0-df5d-4c40-bf59-f3eebe8aee27" />

Step 11:
Once you run the agent , it will fetch all the storage, os etc from VM and create a migration server on AWS

<img width="1036" alt="image" src="https://github.com/user-attachments/assets/ae36a526-9c6e-4309-b3cb-d22f75ce8595" />



