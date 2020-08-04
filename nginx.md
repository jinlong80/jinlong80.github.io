# Nginx

## Setup Steps
Update package for local system
```bash
sudo apt update
```

Install Nginx
```bash
sudo apt install nginx
```

## Configure firewall
List the available application
```bash
sudo ufw app list
```

The output:
```bash
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```

Enable the application and check for status
```bash
sudo ufw allow 'Nginx HTTP'

sudo ufw allow ‘OpenSSH’

sudo ufw status
```

## Command
To stop your web server
```bash
sudo systemctl stop nginx
```

To start the web server when it is stopped
```bash
sudo systemctl start nginx
```

To stop and then start the service again
```bash
sudo systemctl restart nginx
```

If you are simply making configuration changes, Nginx can often reload without dropping connections
```bash
sudo systemctl reload nginx
```

By default, Nginx is configured to start automatically when the server boots. If this is not what you want, you can disable this behavior
```bash
sudo systemctl disable nginx
```

To re-enable the service to start up at boot:
```bash
sudo systemctl enable nginx
```

To check the ngix status
```bash
systemctl status nginx
```

To checj nginx version
```bash
nginx --v
```

## Reference
[Install Nginx on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)




