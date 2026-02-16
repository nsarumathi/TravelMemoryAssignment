**#Travel Memory Application Deployment on AWS**
The Travel Memory Application is a full-stack web application built using the MERN Stack:   

       > MongoDB – Database       
       > React.js – Frontend framework     
       > Node.js – Runtime environment
        
This project demonstrates deployment of the application on Amazon EC2, implementing:

        * Reverse proxy with NGINX
        * Load balancing
        * Multiple instances for scalability
        * Custom domain integration using Cloudflare
  
**Repository Used:** 🔗 https://github.com/UnpredictablePrashant/TravelMemory

Setup:
--------
1.AWS EC2 Setup for Frontend & Backend
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      a.Launch 2 instances as (travel-memory-be & travel-memory-fe))
          Launch instance using Amazon Linux 2023 ,t2.micro instance with Security Group:Allow SSH (22),Allow HTTP (80),Allow your backend port (3000)
          <img width="1918" height="457" alt="EC1 EC2" src="https://github.com/user-attachments/assets/7481e81a-fce8-476a-bd2f-1a85c48cd196" />

      b.BackendServer Configuration (travel-memory-be)
          Install Packages (Git,Nginx,-Node.js)
              sudo yum update -y
              sudo yum install git -y
              sudo yum install nginx -y
              curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
              sudo yum install nodejs -y
              node -v
				<img width="1918" height="842" alt="EC1_BE1" src="https://github.com/user-attachments/assets/f3c98326-e05e-47c9-8e9a-ce72056e47c6" />
				<img width="1917" height="832" alt="EC1_BE2" src="https://github.com/user-attachments/assets/2b0317cb-ffb4-424a-a7a1-acd75e70b241" />

          Clone Repository
              git clone https://github.com/UnpredictablePrashant/TravelMemory.git
              cd TravelMemory/backend
              npm install
				<img width="1918" height="812" alt="EC1_BE3" src="https://github.com/user-attachments/assets/2a457c02-2742-491c-ae23-123788a21c9f" />


          Edit .env file with MongoDB Atlas connection string & Port
             PORT=3000
             MONGO_URI=mongodb+srv://admin:admin1234@cluster0.iz9ritv.mongodb.net/travelmemory?retryWrites=true&w=majority

          Start Backend server- http://<BackendEC2-Public-IP>:3000
              npm install -g pm2
              pm2 start index.js
              pm2 save
				<img width="1918" height="711" alt="EC1_BE6" src="https://github.com/user-attachments/assets/1200ad18-a3cb-41aa-9d0e-6d8c529b8089" />

              now test with http://<BackendEC2-Public-IP>:3000
              <img width="1397" height="240" alt="EC1_BE7" src="https://github.com/user-attachments/assets/4d42a281-2705-48af-a195-f9f5901ed52a" />

            
      b.NGINX Reverse Proxy Setup(Backend)
          Edit configuration
             sudo nano /etc/nginx/nginx.conf
             <img width="403" height="300" alt="image" src="https://github.com/user-attachments/assets/72d6b83d-6e66-41a0-865c-000c1010ac95" />

          Restart nginx ,now accessible by http://<BackendEC2-Public-IP>
             sudo systemctl restart nginx
             sudo systemctl enable nginx

         --now test with http://<EC2_PUBLIC_IP>
         <img width="1292" height="257" alt="EC1_BE9" src="https://github.com/user-attachments/assets/b7365cdc-0c8f-4b01-8bf2-7f8ad5ceff7f" />


          
      c.FrontendServer Configuration (travel-memory-fe)
          Install Packages (Git,Nginx,-Node.js)
              sudo yum update -y
	            sudo yum install git -y
              sudo yum install nginx -y
              curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
              sudo yum install nodejs -y
              node -v

          Clone Repository
              git clone https://github.com/UnpredictablePrashant/TravelMemory.git
              cd TravelMemory/frontend
              npm install
              <img width="1918" height="822" alt="EC2_FE1" src="https://github.com/user-attachments/assets/a35c728e-e3ab-4cea-87a2-dca5026cc26b" />


           Edit BaseUrl in url.js file as Backend-Loadbalancer DNS
              const baseUrl = "http://YOUR_BACKEND_LOAD_BALANCER_DNS";

           Build React App
              npm build

           Move Build files to Nginx directory
             sudo rm -rf /usr/share/nginx/html/*
             sudo cp -r build/* /usr/share/nginx/html/

           Restart Nginx
             sudo systemctl restart nginx
              --now test with http://<FRONTEND_PUBLIC_IP>

      
2.Scaling the Application
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
		  To ensure scalability and high availability:
		    a.Create 2 AMI's :
		        Create AMI from EC2 travel-memory-be
				 <img width="1918" height="777" alt="EC1_AMI1" src="https://github.com/user-attachments/assets/3e2805d4-5447-4ff7-bb1b-4227b088bb6f" />

		        Create AMI from EC2 travel-memory-fe
				<img width="1917" height="788" alt="ec2_ami1" src="https://github.com/user-attachments/assets/575d40f1-c468-4cd6-85ea-41424a8f3cf8" />

		    b.Create 2 Launch Template:
		        Launch Template for Backend
				<img width="1918" height="655" alt="EC1-LT1" src="https://github.com/user-attachments/assets/83668951-f733-451d-a464-232c77b701fb" />

		        Launch Template for Frontend
				<img width="1918" height="517" alt="EC2-LT1" src="https://github.com/user-attachments/assets/4b898bf4-fad0-4e63-8bb2-ec348e313e5b" />

		    c.Create 2 Target Group :
		        TG-travel-memory-be with respective EC2 instance (travel-memory-be)
				<img width="1918" height="832" alt="EC1_TG1" src="https://github.com/user-attachments/assets/b92ca9d3-54e1-4913-8d83-3bb8c93a9db2" />

		        TG-travel-memory-fe with respective EC2 instance (travel-memory-fe)
				<img width="1918" height="762" alt="EC2-TG1" src="https://github.com/user-attachments/assets/10a2fbe8-b699-45d3-b3f0-19dc2008cb77" />

		    d.Create 2 Application Load Balancer:
		        ALB for Backend with respective Traget group (TG-travel-memory-be)
			    <img width="1918" height="847" alt="EC1_LB2" src="https://github.com/user-attachments/assets/b1f4156a-7101-4e04-84f7-132f3c6751d6" />

		        ALB for Frontend with respective Traget group (TG-travel-memory-fe)
				<img width="1918" height="832" alt="EC2-LB1" src="https://github.com/user-attachments/assets/309a9b8e-080a-4e46-87ea-47abac44f054" />

		    e.Create 2 Auto Scaling Group 
		           * Scaling Configuration 1 minimum to 2 desired 
		           * Dynamic Scaling policies based on CPU utilization (5%)
		        This ensures automatic scaling during high traffic.
				ASG-travel-memory-be 
		        <img width="1918" height="848" alt="EC1_ASG1" src="https://github.com/user-attachments/assets/d972997d-787c-4733-b79d-fba524ab7c3d" />

				ASG-travel-memory-fe 
				<img width="1918" height="831" alt="EC2-ASG1" src="https://github.com/user-attachments/assets/0f20b489-0d61-4e82-95d5-039ec3ded871" />

			f.Multiple Instance via ASG
				Totally:2 instance created via respective launch template
				<img width="1918" height="547" alt="ASG-Output" src="https://github.com/user-attachments/assets/11b54c9b-5c1b-4945-b5ee-ed07d2ac5195" />
	
				ASG-travel-memory-be Instances:
				<img width="1918" height="831" alt="ASG-Output3" src="https://github.com/user-attachments/assets/d416b313-dbd8-48b5-aa0a-502f106f38bb" />
	
				ASG-travel-memory-fe Instances:
	            <img width="1917" height="782" alt="ASG-Output1" src="https://github.com/user-attachments/assets/59e6be59-b009-4900-9f36-757ed13fa312" />

    
3.Domain Setup with Cloudflare
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     Add domain in Cloudflare dashboard with proper nameservers( travelmemoryapp.online with 2 nameservers) .
     <img width="1918" height="865" alt="CF-2" src="https://github.com/user-attachments/assets/f55f88ad-42be-4b1d-ae7c-c93ac61036a1" />

     Create DNS Records - A record & CNAME Record

		Type 	Name	Target
		A 	     @	   Frontend Load Balancer IP
		CNAME	www	   Backend Load Balancer DNS

     <img width="1918" height="853" alt="CF-1" src="https://github.com/user-attachments/assets/af05d00c-b520-42cc-a79a-7d1cb435d313" />

     Now the application is accessible via: http://travelmemoryapp.online
     <img width="1625" height="928" alt="CF-3" src="https://github.com/user-attachments/assets/d3f7d488-3bbf-4b55-b1f8-6cbcd114e3ab" />
