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
