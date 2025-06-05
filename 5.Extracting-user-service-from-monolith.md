# Extracting User Service from Monolith

## Overview
User service is extracted from monolith and communicates with the monolith through API calls.

## Step 1: Deploy User Service

SSH into containers instance:
```bash
sudo su
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/user-container-master.zip
cd user-container

# Update MongoDB credentials on line 20
client = pymongo.MongoClient("mongodb://appAdmin:Arka.1992@172.31.27.52:27017/")

# Build and run Docker container
docker build -t user .
docker run -p 5002:5002 -d --name user user

# Verify all containers are running
docker ps  # Should show payment, orders, cart, and user containers
```

## Step 2: Update Monolith Service

```bash
# Reboot monolith instance and SSH into it
sudo su
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/monolith-without-p-o-c-user-main.zip
cd monolith-without-p-o-c-user/

# Update configurations
# Edit app/config.py - Update MySQL credentials
# Edit app/endpoints.yaml - Update with container instance public IP

python3 run.py
```

## Testing
- Access the application through browser
- Note: Due to database migration, users need to register and login again as the user data hasn't been migrated to the new database
