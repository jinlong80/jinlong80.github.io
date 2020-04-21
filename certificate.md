# Certificate

## Generate Certificate
* Generate CA Cert:

  Generate root CA key (without DES)
  ```bash
  openssl genrsa -out ca.key 2048
  ```

  Generate root CA cert: 
  ``` bash
  openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt
  ```

* Generate Server Cert:

  Generating a server key
  ```bash
  openssl genrsa -out server.key 2048
  ```

  Generating a certificate signing request (CSR)
  >Note Common Name need to key in Server's IP address
  ```bash
  openssl req -new -key server.key -out server.csr
  ```

  Signing the CSR with the root CA file
  ```bash
  openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 3650
  ```

* Generate Client Cert:

  Generating a client key
  ```bash
  openssl genrsa -out client.key 2048
    ```

  Generating a certificate signing request (CSR), Note Common Name need to follow Server's IP address
  ```bash
  openssl req -new -key client.key -out client.csr
  ```

  Signing the CSR with the root CA file
  ```bash
  openssl x509 -req -in mqtt_client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -days 3650
  ```