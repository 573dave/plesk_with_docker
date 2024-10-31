# 🌐 Deploying Nextcloud with Docker on Plesk

Easily deploy **Nextcloud** using Docker on an Ubuntu VPS managed by **Plesk Obsidian**. This guide walks you through setting up Nextcloud in a Docker container, configuring a subdomain in Plesk, setting up SSL, and creating a reverse proxy to seamlessly integrate with your domain.

## 🚀 Features

- **Quick Deployment**: Set up Nextcloud with minimal effort using Docker Compose.
- **Plesk Integration**: Manage your subdomains and SSL certificates through Plesk's user-friendly interface.
- **Secure Access**: Utilize Let's Encrypt for SSL certificates to ensure secure connections.

## 📝 Prerequisites

- **Ubuntu VPS** with root access.
- **Plesk Obsidian 18.0.64** installed.
- **Docker** and **Docker Compose** installed on your server.
- A **domain name** pointed to your server's IP address.

## 📦 Installation

### 1. Clone the Repository

SSH into your server and clone this repository:

git clone https://github.com/573dave/plesk_with_docker.git
cd plesk_with_docker
2. Configure Environment Variables
Copy the example environment file and update it with your configurations:

bash
Copy code
cp .env.example .env
Edit the .env file:

ini
Copy code
# .env

# PostgreSQL database configuration
POSTGRES_DB=nextcloud_db                # Database name
POSTGRES_USER=nextcloud_user            # Database username
POSTGRES_PASSWORD=your_secure_password  # Database password (change this)

# Nextcloud configuration
NEXTCLOUD_ADMIN_USER=admin                   # Nextcloud admin username
NEXTCLOUD_ADMIN_PASSWORD=your_admin_password # Nextcloud admin password (change this)
NEXTCLOUD_TRUSTED_DOMAINS=nextcloud.yourdomain.com # Your Nextcloud subdomain
3. Deploy the Docker Containers
Start the containers using Docker Compose:

bash
Copy code
docker-compose up -d
This will launch two containers:

Nextcloud: The application container.
PostgreSQL: The database container.
🌐 Plesk Configuration
1. Create a Subdomain
In Plesk:

Go to Websites & Domains.
Click Add Subdomain.
Enter nextcloud as the subdomain name.
Select your main domain from the dropdown.
Click OK.
2. Obtain an SSL Certificate
Navigate to Websites & Domains > nextcloud.yourdomain.com.
Click on SSL/TLS Certificates.
Click Get it free under Let's Encrypt.
Ensure both "Secure the domain" and "Secure wildcard domain" are checked.
Enter your email address.
Click Install.
3. Configure Reverse Proxy
Access Apache & Nginx Settings:
Go to Websites & Domains > nextcloud.yourdomain.com > Apache & Nginx Settings.
Add Proxy Rules:
For Nginx (as a reverse proxy):

In the Additional Nginx directives section, add:

nginx
Copy code
location / {
    proxy_pass http://127.0.0.1:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Proto https;
}
For Apache:

In the Additional directives for HTTPS, add:

apache
Copy code
<IfModule mod_proxy.c>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
    RequestHeader set X-Forwarded-Proto https
</IfModule>
4. Adjust Firewall Settings
Ensure that your server's firewall allows incoming connections on ports 80 and 443.

🔧 Testing
Visit https://nextcloud.yourdomain.com in your web browser. You should see the Nextcloud setup page.

📂 Data Persistence
The docker-compose.yml file is configured to mount volumes for persistent storage:

Nextcloud data is stored in ./nextcloud_data.
PostgreSQL data is stored in ./db_data.
🛠️ Troubleshooting
View Logs: Check container logs with:

bash
Copy code
docker-compose logs -f
Check Ports: Ensure no other services are using port 8080.

Restart Containers:

bash
Copy code
docker-compose restart
🤝 Contributing
Contributions are welcome! Please open an issue or submit a pull request.

📄 License
This project is licensed under the MIT License.
