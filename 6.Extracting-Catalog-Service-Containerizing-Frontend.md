# Extracting Catalog Service & Containerizing Frontend

## Overview
- Final extraction of Catalog service leaves monolith with just Frontend
- Frontend is also containerized as an independent service
- All services communicate via API calls
- Each service can use different technologies and databases

## Step 1: Deploy Catalog Service

```bash
# SSH into containers instance
sudo su
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/catalog-container-main.zip
cd catalog-container

# Update Line 16 - Add MongoDB password
docker build -t catalogue .
docker run -p 5001:5001 -d catalogue catalogue
```

## Step 2: Update Cart Service

```bash
cd /home/ec2-user/cart-container

# Update line 58 - Change to private IP of containers instance and port 5001
docker build -t cart .
docker rm -f cart
docker run -p 5003:5003 -d --name cart cart
```

## Step 3: Update Monolith

```bash
# Reboot monolith instance and SSH
sudo su
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/monolith-without-p-o-c-u-catalogue-master.zip
cd monolith-without-p-o-c-u-catalogue/

# Update app/endpoints.yaml with containers instance public IP
python3 run.py
```

## Step 4: Load Product Data

SSH into container instance:
```bash
sudo su
mongo admin -u appAdmin -p

use productDb

# Insert product data
db.product.insert({'_id': NumberInt(1), 'name': 'nike', 'category': 'shoe', 'price': 11, 'location': '/static/shoe.jpg'})
db.product.insert({'_id': NumberInt(2), 'name': 'iphone', 'category': 'mobile', 'price': 100, 'location': '/static/mobile.jpg'})
db.product.insert({'_id': NumberInt(3), 'name': 'titan', 'category': 'watch', 'price': 50, 'location': '/static/watch.jpeg'})
db.product.insert({'_id': NumberInt(4), 'name': 'philips', 'category': 'speaker', 'price': 75, 'location': '/static/speaker.jpeg'})
db.product.insert({'_id': NumberInt(5), 'name': 'adidas', 'category': 'tshirt', 'price': 60, 'location': '/static/tshirt.jpeg'})
db.product.insert({'_id': NumberInt(6), 'name': 'sony', 'category': 'tv', 'price': 1000, 'location': '/static/tv.jpg'})
db.product.insert({'_id': NumberInt(7), 'name': 'seagate', 'category': 'harddisk', 'price': 200, 'location': '/static/harddisk.jpg'})
db.product.insert({'_id': NumberInt(8), 'name': 'journal', 'category': 'book', 'price': 30, 'location': '/static/journal.jpg'})

exit
```

## Step 5: Deploy Frontend Container

```bash
git clone https://github.com/pravinmenghani1/breaking-monolith-to-microservices/blob/main/frontend-container-master.zip
cd frontend-container

# Update service endpoints with private IP of containers instance:
# Line 24 - catalogue
# Line 60 - user
# Line 82 - user
# Line xxx - cart
# Line 155,183 - orders
# Line 201 - payment

docker build -t frontend .
docker run -d -p 5000:5000 --name frontend frontend
```

## Testing
- Access application: `http://<Container Instance Public IP>:5000`
- Verify order placement functionality
- Monolith instance can now be terminated
