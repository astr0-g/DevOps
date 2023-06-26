# DevOps

<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#docker-ubuntu">docker ubuntu</a></li>
    <li><a href="#nginx-ubuntu">nginx ubuntu</a></li>
    <li><a href="#lets-encrypt-ubuntu">let's encrypt ubuntu</a></li>
  </ol>
</details>

## docker-ubuntu

Update the apt package index:

```
sudo apt-get update
```

Install packages to allow apt to use a repository over HTTPS:

```
sudo apt-get install \
 apt-transport-https \
 ca-certificates \
 curl \
 gnupg \
 lsb-release
```

Add Docker’s official GPG key:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Use the following command to set up the stable repository:

```
echo \
    "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Update the apt package index again, and install the latest version of Docker Engine and containerd:

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Verify that Docker CE is installed correctly by running the hello-world image:

```
sudo docker run hello-world
```

### if Dockerize Django Application

Dockerfile(django uwsgi):

```
# Use a Python base image
FROM python:3.8

# Set the working directory
WORKDIR /app

# Install uWSGI
RUN pip install uwsgi

# Copy the requirements file and install dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy the Django project code
COPY . .

# Expose the port on which uWSGI will listen
EXPOSE 8000

# Start uWSGI with the specified options
CMD ["uwsgi", "--http", "0.0.0.0:8000", "--module", "myapp.wsgi", "--processes", "4", "--threads", "2"]
```

Next, create a requirements.txt file in your project directory, listing your application's dependencies. For instance, if your Django app uses Django 3.2 and Gunicorn, your requirements.txt should contain:

```
Django==3.2
gunicorn
```

Build your Docker image:

```
sudo docker build -t my_django_app .
```

After the build completes, list your images with:

```
sudo docker images
```

run docker:

```
sudo docker run -d -p 8000:8000 testproject_web
```

or (following option)

```
sudo docker run -it -p 8000:8000 testproject_web
```

Stop a Container: To stop the running container, use the docker stop command followed by the container ID:

```
sudo docker stop 2ff3746e3305
```

Start a Container: If the container has been stopped, you can start it again using the docker start command:

```
sudo docker start 2ff3746e3305
```

View Container Logs: You can view the logs generated by the container using the docker logs command followed by the container ID:

```
sudo docker logs 2ff3746e3305
```

Inspect Container Details: To retrieve detailed information about the container, including its configuration and network details, use the docker inspect command:

```
sudo docker inspect 2ff3746e3305
```

## nginx-ubuntu

If you haven't installed Nginx yet, you can do it with:

```
sudo apt update
sudo apt install nginx
```

You need to create a Nginx configuration file in /etc/nginx/sites-available/ directory. You can name it api.test.com. Use nano or vi or your favorite text editor to create and edit the file:

```
sudo nano /etc/nginx/sites-available/api.test.com
```

Paste the following into the file, replace api.test.com with your domain name:

```
server {
    listen 80;
    server_name api.test.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

To enable your site, you need to create a symbolic link from your site configuration in sites-available to the sites-enabled directory, which Nginx reads from during startup.

```
sudo ln -s /etc/nginx/sites-available/api.test.com /etc/nginx/sites-enabled/
```

(if exist)

```
sudo rm /etc/nginx/sites-enabled/api.test.com
```

Test and Restart Nginx

```
sudo nginx -t
```

```
sudo systemctl restart nginx
```

## lets-encrypt-ubuntu

First, make sure snapd is installed and up to date:

```
sudo apt update
sudo apt install snapd
sudo snap install core; sudo snap refresh core
```

Remove any old version of Certbot:

```
sudo apt-get remove certbot
```

Install Certbot:

```
sudo snap install --classic certbot
```

Link Certbot to your $PATH:

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Use Certbot to get a certificate:

```
sudo certbot --nginx
```

If the verification is successful, Certbot will ask how you'd like to configure your HTTPS settings:

- Secure: This setting will redirect all HTTP traffic to HTTPS.
- Flexible: This setting will not redirect HTTP traffic.
  Certbot will automatically renew your certificates before they expire. You can test automatic renewal with this command:

```
sudo certbot renew --dry-run
```
