###### Migration of GCP VM to AWS using AWS Migration Service ###################################################

<img width="761" alt="image" src="https://github.com/user-attachments/assets/dffdb4ae-00b9-495a-935a-cf61f7b8e322" />


Step 1:
Create ssh keys Public key and Private key on your local(using putty gen , ssh-keygen)


Step 2:
Add Public key in GCP VM and Save Private in your local
(Go to GCP VM edit section and look for SSH keys and add SSH public key )


<img width="772" alt="image" src="https://github.com/user-attachments/assets/1d7eef86-1637-4d1a-a881-da1dfd66f8cb" />


Step 3:
Try accessing GCP from ssh -i <pem path> name@PublicIP_GCP_VM


Step 4:
Move to AWS and access AWS Migration Service


<img width="1460" alt="image" src="https://github.com/user-attachments/assets/01110e24-a369-4a9b-bd29-80dbd863ed2b" />


Step 5:
Check replication and launch template according to you requirements 

I am using default for everything for my POC

<img width="1099" alt="image" src="https://github.com/user-attachments/assets/991f3329-45f5-4cf7-beff-087628cfdc46" />


In lauch template i uncheck General launch settings > Activate instance type right-sizing and choose t3.medium and save template

<img width="1299" alt="image" src="https://github.com/user-attachments/assets/8a5cf6bc-83e5-4dc1-8951-f4f768690044" />


Step 6:
Check Post-launch template  and enable Install the Systems Manager agent and allow executing actions on launched servers if you are Prod

(i have disabled it as i will access it with SSH using Public key)


<img width="1581" alt="image" src="https://github.com/user-attachments/assets/9538f36a-7df2-4247-8097-f644756ed168" />


Step 7:
Now go to IAM and create IAM user with access key and secret access key
(Attach permission : AWSApplicationMigrationAgentinstallationPolicy)


<img width="1392" alt="image" src="https://github.com/user-attachments/assets/4b13ec2f-1cba-4d1c-8183-ad478d1303d5" />



Step 8:
Once create go to AWS migration and add access secret keys


<img width="1208" alt="image" src="https://github.com/user-attachments/assets/d35b1c3e-18e7-4e04-88b0-4e8a3db05ca6" />

Step 9:
Now we need to run the below commands one by one in GCP VM

sudo wget -O ./aws-replication-installer-init https://aws-application-migration-service-ap-southeast-1.s3.ap-southeast-1.amazonaws.com/latest/linux/aws-replication-installer-init
sudo chmod +x aws-replication-installer-init; sudo ./aws-replication-installer-init --region ap-southeast-1 --aws-access-key-id <your_access_key> --aws-secret-access-key <your_secret_key> --no-prompt



<img width="1667" alt="image" src="https://github.com/user-attachments/assets/c3c527c0-df5d-4c40-bf59-f3eebe8aee27" />

Step 10:
Once you run the agent , it will fetch all the storage, os etc from VM and create a migration server on AWS

<img width="1029" alt="image" src="https://github.com/user-attachments/assets/967c1916-a6d6-4c07-84d7-615d665074b4" />

Step 11:
Go to source servers user Aws Migration service you see you replication details.

<img width="1560" alt="image" src="https://github.com/user-attachments/assets/19165d79-c023-4e19-bd68-0ffd2dd9d7fa" />

Step 12:
Jump to ec2 service to cross verify

<img width="1220" alt="image" src="https://github.com/user-attachments/assets/bb842d3e-4b98-4d3e-ad20-e352361e95c4" />



After sometime we can see data transfer begins :)

<img width="1437" alt="image" src="https://github.com/user-attachments/assets/b65da1ae-9f85-4d51-b74a-ba55402d28ae" />


Step 13:
Once data transfer is done , Check in snapshot in ec2 console using which Volumes will be created

<img width="1369" alt="image" src="https://github.com/user-attachments/assets/1e18858b-bdbb-49e1-9ba1-c0c80ef5472d" />


<img width="1306" alt="image" src="https://github.com/user-attachments/assets/e144a381-7459-40cd-89ea-9dcbbc0cb45f" />

Step 14:
Once you see Initial replication finished

<img width="1667" alt="image" src="https://github.com/user-attachments/assets/a1acc6d3-4371-4247-8168-9fa994dae091" />

Step 15:
Now click on test and cutover , it will create a test server (this server is to test if everything is as expected in sever )

<img width="1397" alt="image" src="https://github.com/user-attachments/assets/5e7e3854-45cf-4e81-8762-d1ceaaca0319" />

Step 16:
Once you are sure , click on (Mark as Ready for Cutover) - this will remove test ec2

<img width="1382" alt="image" src="https://github.com/user-attachments/assets/a6efd78f-6cea-454a-a189-fe27042a8bcf" />

Step 17:
Once this is terminated go to AWS mgn and click on Launch cutover instance. (this will again launch a server with the same name and we can test that, this time let's attach eip to this server and SSH to it)

<img width="1409" alt="image" src="https://github.com/user-attachments/assets/242e0247-91d3-4d60-8cf3-26d3311371a4" />

<img width="1384" alt="image" src="https://github.com/user-attachments/assets/0299e396-0aed-4293-bffa-4175c790ac87" />

Step 18:
Go to Elastic IP section , Create and associate it to our VM. (check the status on mgn svc)

<img width="1333" alt="image" src="https://github.com/user-attachments/assets/3fb582e8-8ad4-429c-a465-37a9f30c994d" />


<img width="1446" alt="image" src="https://github.com/user-attachments/assets/d7f354b3-40e9-42f9-aaf9-547515d2ccf9" />

Step 19:
Check we have public IP now.

<img width="872" alt="image" src="https://github.com/user-attachments/assets/9f8819ac-94d9-4e4e-a0ee-a35f5db2db4e" />

Step 20:

SSH to it













