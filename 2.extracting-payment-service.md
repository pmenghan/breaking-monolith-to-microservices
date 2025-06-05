# Extracting Payment Service from Monolith

## Overview
- Payment service is extracted from the monolith
- It operates as a separate entity with its own MongoDB database
- Communication with monolith occurs via API calls

## Step 1: EC2 Instance Setup

1. Provision EC2 instance with following specifications:
   - Name: container
   - Ports: 5005-5500 open
   - Image: Amazon Linux 2

2. Install required packages:
```bash
sudo amazon-linux-extras install docker
sudo service docker start
sudo usermod -a -G docker ec2-user
sudo chkconfig docker on
sudo yum install -y git
sudo yum install python3
sudo -H pip3 install -r requirements.txt
```

## Step 2: MongoDB Installation

1. Create MongoDB repository file:
```bash
sudo vi /etc/yum.repos.d/mongodb-org-4.4.repo
```

Add the following content:
```ini
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```

2. Install MongoDB:
```bash
sudo yum install -y mongodb-org
```

3. Configure MongoDB:
```bash
# Edit /etc/mongod.conf
# Change bindIP to 0.0.0.0
# Add security configuration:
security:
  authorization: enabled
```

4. Initialize MongoDB:
```bash
service mongod start
mongo

# Create admin user
use admin
db.createUser({
  user: "appAdmin",
  pwd: passwordPrompt(),
  roles: [ 
    { role: "userAdminAnyDatabase", db: "admin" },
    "readWriteAnyDatabase" 
  ]
})

# Initialize payment database
use paymentdb
db.payment.insert({'_id': 0, 'order_id': 0})
exit
```

## Step 3: Deploy Payment Service

```bash
git clone https://github.com/edureka-tutorials/payment-container.git](https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/payment-container-main.zip
cd payment-container

# Update configuration
# Edit line 42 - Update monolith server IP:
url = 'http://52.3.249.40:5000/update-order-status'

# Edit line 18 - Update MongoDB credentials:
client = pymongo.MongoClient("mongodb://appAdmin:Arka.1992@172.31.27.52:27017/")

# Build and run Docker container
docker build -t payment .
docker run -p 5005:5005 -d payment
```

## Step 4: Update Monolith Service

SSH into monolith server and execute:
```bash
git clone [https://github.com/edureka-tutorials/monolith-without-payment.git](https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/monolith-without-payment-master.zip)
cd monolith-without-payment

# Update container server IP in app/endpoints.yaml:
paymentUrl: 'http://54.197.142.197:5005/payment'

python3 run.py
```

## Step 5: Testing

Access the application:
```
http://<monolith_ip>:5000
```
Test the functionality by placing an order.
