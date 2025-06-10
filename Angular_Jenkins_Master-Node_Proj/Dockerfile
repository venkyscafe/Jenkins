# Use an official Ubuntu base image
FROM ubuntu:latest

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive

# Update and install required dependencies
RUN apt update && apt install -y \
#   vim \
    openssh-server \
    openjdk-17-jre \
    nginx \
    curl

# Install Node.js (22.14.0)
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt install -y nodejs && \
    node -v

# Install specific npm version (10.9.2)
RUN npm install -g npm@10.9.2 && npm -v

# Install Angular CLI (16.2.16)
RUN npm install -g @angular/cli@16.2.16 && ng version

# Configure SSH
RUN mkdir -p /var/run/sshd && \
    sed -i 's/^#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    echo "root:password" | chpasswd

# Configure Nginx
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# Ensure SSH runs on shell startup
RUN echo "/usr/sbin/sshd" >> /root/.bashrc

# Expose necessary ports
EXPOSE 22 80

# Start SSH and Nginx services
CMD ["/bin/bash", "-c", "/usr/sbin/sshd && nginx"]

