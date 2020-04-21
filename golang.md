# GoLang

## Setup Go on Ubuntu

Update package on local system
```bash
sudo apt-get update; sudo apt-get -y upgrade
```

Check the latest [golang version](https://golang.org/dl/), and go to the prefered location to download.
```bash
cd /usr/local
```

Download the Go language binary archive file and untar
```bash
wget https://dl.google.com/go/go1.14.linux-amd64.tar.gz

tar -zxvf go1.14.linux-amd64.tar.gz
```

Setup Go Environment
```
nano $HOME/.profile

export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

source $HOME/.profile
```

Verify Installation
```bash
go version
```

Verify all configured environment variables
```bash
go env
```

## Reference 
[Install Go on Ubuntu](https://medium.com/better-programming/install-go-1-11-on-ubuntu-18-04-16-04-lts-8c098c503c5f)