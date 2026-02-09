## Configure Apache/Nginx on an EC2 instance.

In this project, we will deploy a web server using Amazon EC2. We will launch an EC2 instance, install Nginx, and configure it to serve a basic website.

### Steps to Set Up an EC2-based Web Server:

### Step 1: Launch an EC2 Instance
1. Go to the AWS Management Console → EC2.
2. Click Launch Instance.
3. Configure the instance:
   ○ Name: web-server-demo
   ○ AMI: Choose Ubuntu 
   ○ Instance Type: t2.micro (Free Tier eligible)
   ○ Key Pair: Create a new key pair or use an existing one.
   ○ Security Group: Allow SSH (port 22) and HTTP (port 80).
4. Click Launch

![alt text](ec2-instance-setup-part-1.png)

![alt text](ec2-instance-setup-part-2.png)

![alt text](ec2-instance-setup-part-3.png)

### Step 2: Connect to the EC2 Instance
Open a terminal and run:

  ```
  ssh -i your-key.pem ubuntu@your-instance-public-ip
  ```
### Step 3: Install Nginx

Update the package repository:

  ```
  sudo apt update -y
  ```

Install Nginx:

  ```
  sudo apt install nginx -y
  ```

Start and enable Nginx:

  ```
  sudo systemctl start nginx
  sudo systemctl enable nginx
  ```

Verify installation:

  ```
  sudo systemctl status nginx
  

  sudo nginx -T | grep -A5 root                    # It told us where nginx serves files from and which index files have priority
  sudo rm /var/www/html/index.nginx-debian.html    # Remove Ubuntu’s default nginx welcome page
  ```

### Step 4: Deploy a Web Page  
Edit the default index file:

  ```
  sudo nano /var/www/html/index.html   

  <h1>Welcome to My Web Server</h1>
  ```

Reload Nginx so changes gets applied:

  ```
  sudo systemctl reload nginx
  ```

### Step 6: Access the Web Server
Open a browser and go to:

  ```
  http://your-instance-public-ip

  ```

![alt text](nginx-installed-view-on-browser.png)  

You should see "Welcome to My Web Server, installed Nginx !!"


*Good to know*

- Amazon Linux / RHEL / CentOS, not Ubuntu - (/usr/share/nginx/html) 

- Ubuntu paths (/var/www/html)

- Nginx serves files from /var/www/html

- It looks for index files in this exact order:

```
index.html

index.htm

index.nginx-debian.html
```
Whichever exists first is what the browser gets.