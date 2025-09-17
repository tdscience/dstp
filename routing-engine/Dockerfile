# ---- Build Stage ----
# Use a standard Ubuntu image as a 'builder' to get the OTP files
FROM ubuntu:22.04 as builder

# Install only the tools needed to download and unpack
RUN apt-get update && apt-get install -y wget unzip && rm -rf /var/lib/apt/lists/*

# Download and unpack OTP into a temporary location
WORKDIR /source
RUN wget https://github.com/ITSLeeds/TDS/releases/download/0.20.1/otp_TDS.zip && \
unzip otp_TDS.zip

# ---- Final Stage ----
# Start from a clean, official OpenJDK base image, which is minimal and secure
FROM eclipse-temurin:8-jre-focal

# Install Nginx, Certbot, and supervisor for process management
RUN apt-get update && apt-get install -y \
    nginx \
    certbot \
    python3-certbot-nginx \
    supervisor \
    && rm -rf /var/lib/apt/lists/*

# Set the working directory
WORKDIR /app

# Copy the OTP application files from the 'builder' stage
COPY --from=builder /source/otp_TDS/ .

# Ensure the graphs directory exists, as OTP will look for it
RUN mkdir -p graphs

# Create Nginx configuration
RUN rm -f /etc/nginx/sites-enabled/default

# Create initial Nginx config for HTTP (will be updated by certbot for HTTPS)
RUN echo 'server {\n\
    listen 80;\n\
    server_name otp.robinlovelace.net;\n\
\n\
    location / {\n\
        proxy_pass http://localhost:8080;\n\
        proxy_set_header Host $host;\n\
        proxy_set_header X-Real-IP $remote_addr;\n\
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;\n\
        proxy_set_header X-Forwarded-Proto $scheme;\n\
    }\n\
}' > /etc/nginx/sites-available/otp

# Enable the site
RUN ln -s /etc/nginx/sites-available/otp /etc/nginx/sites-enabled/

# Create supervisor configuration to manage both OTP and Nginx
RUN echo '[supervisord]\n\
nodaemon=true\n\
user=root\n\
\n\
[program:otp]\n\
command=java -Xmx3g -jar /app/otp-1.5.0-shaded.jar --router west-yorkshire --graphs /app/graphs --server --port 8080\n\
directory=/app\n\
autostart=true\n\
autorestart=true\n\
stdout_logfile=/var/log/otp.log\n\
stderr_logfile=/var/log/otp.log\n\
\n\
[program:nginx]\n\
command=/usr/sbin/nginx -g "daemon off;"\n\
autostart=true\n\
autorestart=true\n\
stdout_logfile=/var/log/nginx_supervisor.log\n\
stderr_logfile=/var/log/nginx_supervisor.log' > /etc/supervisor/conf.d/supervisord.conf

# Create startup script for SSL certificate generation
RUN echo '#!/bin/bash\n\
set -e\n\
\n\
# Wait for OTP to start\n\
echo "Waiting for OTP to start..."\n\
sleep 30\n\
\n\
# Check if SSL certificate exists, if not create it\n\
if [ ! -f /etc/letsencrypt/live/otp.robinlovelace.net/fullchain.pem ]; then\n\
    echo "SSL certificate not found. Obtaining certificate..."\n\
    \n\
    # Create webroot directory\n\
    mkdir -p /var/www/html\n\
    \n\
    # Start nginx temporarily\n\
    nginx\n\
    \n\
    # Get SSL certificate\n\
    certbot --nginx -d otp.robinlovelace.net --non-interactive --agree-tos --email admin@robinlovelace.net\n\
    \n\
    # Stop nginx so supervisor can manage it\n\
    nginx -s stop\n\
    \n\
    echo "SSL certificate obtained successfully"\n\
else\n\
    echo "SSL certificate already exists"\n\
fi\n\
\n\
# Start supervisor to manage both services\n\
exec /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf' > /startup.sh

RUN chmod +x /startup.sh

# Expose ports
EXPOSE 80 443 8080

# Use the startup script as entrypoint
CMD ["/startup.sh"]