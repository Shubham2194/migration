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



