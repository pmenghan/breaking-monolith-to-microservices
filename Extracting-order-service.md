# Extracting Orders Service from Monolith

## Step 1: Deploy Orders Service

SSH into Containers instance:
```bash
sudo su
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/order-container-main.zip
cd order-container

# Update configuration
# Modify Line 18 of orders.py - Update MongoDB credentials
# Modify Line 45 - Update with Public IP of monolith server

# Build and run Docker container
docker build -t orders .
docker run -p 5004:5004 -d --name orders orders
```

## Step 2: Update Payment Service

```bash
cd ~/ec2-user/payment-container

# Update Line 42 with Private IP of containers instance
# since orders is running on the same instance

# Rebuild and run payment container
docker build -t payment .
docker run -p 5005:5005 -d --name payment payment
```

## Step 3: Verify Container Status

```bash
docker ps
```

## Step 4: Update Monolith Service

1. Reboot monolith instance and SSH into it
```bash
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/monolith-without-payment-orders-main.zip
cd monolith-without-payment-orders

# Update configurations
# Edit app/config.py - Update MySQL password
# Edit app/endpoints.yaml - Update with public IP of containers instance

python3 run.py
```

## Testing
Access the application through browser and verify order placement functionality.
