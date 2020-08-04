# EMQX

## Setup

Support Ubuntu 18.04, 16.04

Install the required dependency
```bash
sudo apt update && sudo apt install -y \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common
```

Add the GPG key for EMQ X
```bash
curl -fsSL https://repos.emqx.io/gpg.pub | sudo apt-key add -
```

Set up the stable repository
```bash
sudo add-apt-repository \
"deb [arch=amd64] https://repos.emqx.io/emqx-ce/deb/ubuntu/ \
$(lsb_release -cs) \
stable"
```

Update apt package index
```bash
sudo apt update
```

Install the latest version of EMQ X
```bash
sudo apt install emqx
```

## Command
* Start EMQX
  ```bash
  emqx start
  EMQ X Broker v4.0.3 is started successfully!
  ```

* Check status
  ```bash
  emqx_ctl status
  Node 'emqx@127.0.0.1' is started
  emqx 4.0.3 is running
  ```

* systemctl start
  ```bash
  sudo systemctl start emqx
  ```

* service start
  ```bash
  sudo service emqx start
  ```

## Configuration

Configuration file path
  * Configuration file path: /etc/emqx
  * Log file path: /var/log/emqx
  * Data file path: ``/var/lib/emqx`


## EMQX Dashboard
Emqx Dashboard default credential:
```text
acess port number:18083
username: admin
passwrod: public
```

## Reference
[Install EMQX on Ubuntu](https://docs.emqx.io/broker/v3/en/install.html#ubuntu)

[EMQX Articles](https://medium.com/@emqtt)

[EMQX Dashboard Source Code](https://github.com/emqx/emqx-dashboard)

[EMQX Setup With Server/Client Certification](https://abawchen.gitlab.io/chore/2019/06/17/emqx-tls-setup.html)

[Using Lets Encrypt Certificates in EMQ](https://medium.com/@emqtt/using-lets-encrypt-certificates-in-emq-b11e0e57efa6)

[EMQX Shared Group Topic](https://docs.emqx.io/tutorial/latest/en/advanced/share_subscribe.html)

[EMQX Shared Group Topic Tutorial](https://juejin.im/post/5dca4d4751882573db10552f)
