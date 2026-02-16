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
      b.BackendServer Configuration (travel-memory-be)
          Install Packages (Git,Nginx,-Node.js)
              sudo yum update -y
	            sudo yum install git -y
              sudo yum install nginx -y
              curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
              sudo yum install nodejs -y
              node -v

          Clone Repository
              git clone https://github.com/UnpredictablePrashant/TravelMemory.git
              cd TravelMemory/backend
              npm install

          Edit .env file with MongoDB Atlas connection string & Port
             PORT=3000
             MONGO_URI=mongodb+srv://admin:admin1234@cluster0.iz9ritv.mongodb.net/travelmemory?retryWrites=true&w=majority

          Start Backend server- http://<BackendEC2-Public-IP>:3000
              npm install -g pm2
              pm2 start index.js
              pm2 save
              now test with http://<BackendEC2-Public-IP>:3000

            
      b.NGINX Reverse Proxy Setup(Backend)
          Edit configuration
             sudo nano /etc/nginx/nginx.conf
             <img width="403" height="300" alt="image" src="https://github.com/user-attachments/assets/72d6b83d-6e66-41a0-865c-000c1010ac95" />

          Restart nginx ,now accessible by http://<BackendEC2-Public-IP>
             sudo systemctl restart nginx
             sudo systemctl enable nginx

         --now test with http://<EC2_PUBLIC_IP>

          
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
        Create AMI from EC2 travel-memory-fe
    b.Create 2 Launch Template:
        Launch Template for Backend
        Launch Template for Frontend
    c.Create 2 Target Group :
        TG-travel-memory-be with respective EC2 instance (travel-memory-be)
        TG-travel-memory-fe with respective EC2 instance (travel-memory-fe)
    d.Create 2 Application Load Balancer:
        ALB for Backend with respective Traget group (TG-travel-memory-be)
        ALB for Frontend with respective Traget group (TG-travel-memory-fe)
    e.Create 2 Auto Scaling Group 
        ASG-travel-memory-be & ASG-travel-memory-fe 
           * Scaling Configuration 1 minimum to 2 desired 
           * Dynamic Scaling policies based on CPU utilization (5%)
        This ensures automatic scaling during high traffic.
    
3.Domain Setup with Cloudflare
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     Add domain in Cloudflare dashboard with proper nameservers( travelmemoryapp.online with 2 nameservers) .
     Create DNS Records - A record & CNAME Record
       <img width="398" height="107" alt="image" src="https://github.com/user-attachments/assets/d6d32f65-2cd2-49d3-975c-0dfe84372878" />
     Now the application is accessible via: http://travelmemoryapp.online
