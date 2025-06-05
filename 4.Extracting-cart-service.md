# Extracting Cart Service from Monolith

## Overview
Cart service is extracted from the monolith and communicates with Orders service and monolith through API calls.

## Step 1: Deploy Cart Service

SSH into Containers instance:
```bash
sudo su
mongo

# Initialize Cart Database
use cartdb
db.cart.insert({
    '_id': cartId, 
    'user_id': data['userId'], 
    'total_price': 0, 
    'state': 'ACTIVE'
})
exit

# Deploy Cart Service
cd /home/ec2-user/
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/cart-container-main.zip
cd cart-container

# Update configurations
# Edit Line 18 of cart.py - Update MongoDB password
# Edit Line 58 - Update monolith public IP
url = 'http://34.234.193.97:5000/price'

# Build and run Docker container
docker build -t cart .
docker run -p 5003:5003 -d --name cart cart
```

## Step 2: Update Orders Service

```bash
cd /home/ec2-user/order-container

# Modify Line 45 - Update with Private IP of container server and port 5003

# Rebuild and redeploy orders container
docker build -t orders .
docker rm -f orders
docker run -p 5004:5004 -d --name orders orders
```

## Step 3: Update Monolith Service

```bash
# Reboot monolith server and SSH into it
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/monolith-without-payment-orders-cart-main.zip
cd monolith-without-payment-orders-cart

# Update configurations
# Edit app/config.py - Update MySQL password
# Edit app/endpoints.yaml - Update with public IP of containers instance

python3 run.py
```

## Testing
Access the application through browser and verify the order placement functionality.
