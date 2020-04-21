# NPM

## Setup
  * Install for Ubuntu version 16.04. [ref](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04)
  ```bash
  sudo apt-get update
  curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
  sudo apt-get install -y nodejs
  sudo apt-get install npm 
  ```
 
  * Install for Ubuntu version 18.04. [ref](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04)
  ```bash
  sudo apt update
  sudo apt install nodejs
  sudo apt install npm
  nodejs -v
  ```

## Command

Update node version
```bash
sudo npm install n -g
```
Update to the latest stable version:
```bash
sudo n stable
```

Update to the latest version:
```bash
sudo n latest
```

List NPM Installed Package
For project dependencies use
```bash
npm list --depth=0
```

For global dependencies use:
```bash
npm list -g --depth=0
```

## Node Packages List
Install forever
```bash
sudo npm install forever -g
```

Install console-stamp
```bash
npm install console-stamp
```

Install Firebase Admin
  Copy serviceAccount.json into the path, at the path execute following command
```bash
npm install firebase-admin --save
```

Install MongoDb
```bash
npm install mongodb
```

Install pem
```bash
npm install pem
```

Install child process
```bash
npm i childprocess
```

Install MQTT
```bash
npm install mqtt --save
```

Install node-cache
```bash
npm install node-cache --save
```

Install deepmerge
```bash
npm install deepmerge
```

Install Node Mailer
```bash
npm install node-mailer
```

Install Email Template V2
```bash
npm install --save email-templates-v2
```

Install simple ssh
```bash
npm install -g simple-ssh
```

Install angular http server
```bash
npm install -g angular-http-server
```