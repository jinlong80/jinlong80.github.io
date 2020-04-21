# Visual Studio Code

## Extension
  Rmate
  Remote access to server via terminal

  Command to Install at remote ubuntu server
  ```bash
  sudo curl -o /usr/local/bin/rmate https://raw.githubusercontent.com/aurora/rmate/master/rmate
  sudo chmod +x /usr/local/bin/rmate
  ```

  Connect to remote server
  ```bash
  ssh -R 52698:127.0.0.1:52698 root@199.199.72.72
  ```

  Open remote server’s file
  ```bash
  rmate -p 52698 xxx.js
  ```

## Reference
[Rmate](https://medium.com/@prtdomingo/editing-files-in-your-linux-virtual-machine-made-a-lot-easier-with-remote-vscode-6bb98d0639a4)
