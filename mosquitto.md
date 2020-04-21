# Mosquitto

## Setup
* Linux
    
  Install Mosquitto Broker with latest version 1.6.8
  ```bash
  sudo apt-add-repository ppa:mosquitto-dev/mosquitto-ppa
  sudo apt-get update
  sudo apt-get install mosquitto
  ```

  Install Mosquitto Clients
  ```bash
  sudo apt-get install mosquitto-clients
  ```

* MAC OS:
  Install Mosquitto Broker with Brew
  ```bash
  brew install mosquitto

  /usr/local/sbin/mosquitto -c /usr/local/etc/mosquitto/mosquitto.conf
  ```

## Configure
[ref](http://www.steves-internet-guide.com/topic-restriction-mosquitto-configuration/)

Example 1- Listen on ports 1883 and 1884

Section Default listener
```text
port 1883
```
Section Extra listeners
```text
listener 1884
```

Example 2- Listen on ports 1883 and 8883 (SSL)
Section Default listener
```text
port 1883
```

Section Extra listeners
```text
listener 8883
```

Certificate based SSL/TLS support
(Linux)
```text
cafile /etc/mosquitto/certs/ca.crt
keyfile /etc/mosquitto/certs/server.key
certfile /etc/mosquitto/certs/server.crt
```

Example 3- Listen on ports 1883 and WebSockets (SSL)
Section Default listener
```text
port 1883
```

Section Extra listeners
```text
listener 9001
protocol websockets
```

### Sample

Configure Cert Location:
Replace mosquitto config file (/etc/mosquitto/conf.d/default.conf) as following:
```text
port 8883
cafile /root/app/mqtt/ca_certificates/mqtt_ca.crt
certfile /root/app/mqtt/server_certificates/mqtt_server.crt
keyfile /root/app/mqtt/server_certificates/mqtt_server.key
require_certificate true
connection_messages true
#log_type debug
#log_type error
#log_type warning
#log_type notice
#log_type information
#log_type subscribe
#log_type unsubscribe
#log_type websockets
#log_type none
log_type all
log_dest syslog
log_dest stdout
  ```
Config firewall for MQTT port 8883:
```bash
sudo ufw allow 8883
```

## Command

* Check status
  ```bash
  sudo systemctl status mosquitto.service
  ```

* Start
  ```bash
  sudo systemctl start mosquitto.service
  ```

* Restart
  ```bash
  sudo systemctl restart mosquitto.service
  ```

## Reference
[Install Mosquitto Broker on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-ubuntu-16-04)