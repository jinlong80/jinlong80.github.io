# Homebrew

## Installation
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

## Installation Path
By default, Homebrew will install all packages into the following directory in all versions of Mac OS:
```
/usr/local/Cellar/
```

Additionally, Homebrew places symlinks into the following directory path:
```
/usr/local/opt/
```

The symbolic links of binaries found in '/usr/local/opt/' all point to their respective package in '/usr/local/Cellar/' as can be confirmed with ls and the -l flag:

```bash 
ls -l /usr/local/opt/
```

## Update Package
```bash
brew update
```

## Package
* Node

  Install
  ```bash
  brew install node
  ```

  Uninstall
  ```bash
  brew uninstall node
  ```

  Upgrade
  ```bash
  brew upgrade node
  ```

## Issue

* Execute 'brew postinstall node', but response 'The post-install step did not complete successfully', it means permission are wrong.
  ```bash
  Warning: The post-install step did not complete successfully
  You can try again using `brew postinstall node`
  ```
  Solutions: Change the permission
  ```bash
  sudo chown -R $(whoami) $(brew --prefix)/*
  ```

