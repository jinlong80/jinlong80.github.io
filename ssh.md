# SSH

## Command

* To list the SSH which is added
  ```bash
  ssh-add -l
  ```

* To specify which Id to add
  ```bash
  ssh-add ~/.ssh/id_rsa
  ```

* Keys need to be only readable by you:
  ```bash
  chmod 400 ~/.ssh/id_rsa
  ```

* If Keys need to be read-writable by you:
  ```bash
  chmod 600 ~/.ssh/id_rsa
  ```

## Others
### Upload an SSH Public Key to an Existing Droplet

```bash
echo "ssh-rsa EXAMPLEzaC1yc2E...GvaQ== root@199.199.72.72" \
>> ~/.ssh/authorized_keys

chmod -R go= ~/.ssh
chown -R $USER:$USER ~/.ssh
```

## Reference
[SSH Keys](https://docs.gitlab.com/ee/ci/ssh_keys/README.html)

[Add SSH Keys to Existing Droplet](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/to-existing-droplet/)