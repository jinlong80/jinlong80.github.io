# Docker

Easy to understand 'what is Docker' from this [article](https://www.edureka.co/blog/what-is-docker-container).

## Docker Swarm

If you heard about Kubernetes and [here](https://vexxhost.com/blog/kubernetes-vs-docker-swarm-containerization-platforms/) is the comparison between Kubernetes and Docker Swarm.

Below guide is based on `MAC` and `Digitalocean Droplet` VM setup enviroment.

### Setup Prerequisites

* Install Docker in your local machine, if not go to this [Official Docker site](https://docs.docker.com/#docker-products) download and install, available for MAC, Windows and Linux.

* Create a Droplet's Person Access Token, follow this [guide](https://www.digitalocean.com/docs/apis-clis/api/create-personal-access-token/).

* Install Docker Machine if not install before. [Others OS may refer here](https://docs.docker.com/machine/install-machine/).

  Download the Docker Machine binary and extract it to your PATH
  ```bash
  base=https://github.com/docker/machine/releases/download/v0.16.0 &&
    curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/usr/local/bin/docker-machine &&
    chmod +x /usr/local/bin/docker-machine
  ```
  Install bash completion scripts
  ```bash
  base=https://raw.githubusercontent.com/docker/machine/v0.16.0
  for i in docker-machine-prompt.bash docker-machine-wrapper.bash docker-machine.bash
  do
    sudo wget "$base/contrib/completion/bash/${i}" -P /etc/bash_completion.d
  done
  ```
  to load the file into current shell prompt
  ```bash
  source /etc/bash_completion.d/docker-machine-prompt.bash
  ```
  to enable docker-machine shell prompt, to edit ~/.bashrc
  ```bash
  nano ~/.bashrc
  ```
  then add the below line into ~/.bashrc
  ```bash
  PS1='[\u@\h \W$(__docker_machine_ps1)]\$ '
  ```
### Setup Steps
* Create Droplet through docker-machine.

  Create a Manager node with specfied to ubuntu-18-04-x64, private network enable, Singapore Region. The long number 1..2...3.. is the API token sample, you should replace with your own API token. dev-server-manager-1 is the Droplet name.
  ```bash
  docker-machine create --driver digitalocean --digitalocean-image ubuntu-18-04-x64 --digitalocean-private-networking --digitalocean-region sgp1 --digitalocean-access-token 1111111111111111111122222222222222222222333333333333333333333333 dev-server-manager-1
  ```

  Create two Worker node by Bash scripting. with node name dev-server-node-1 and dev-server-node-2.
  ```bash
  for i in 1 2; do docker-machine create --driver digitalocean --digitalocean-image  ubuntu-18-04-x64 --digitalocean-private-networking --digitalocean-region sgp1 --digitalocean-access-token 1111111111111111111122222222222222222222333333333333333333333333 dev-server-node-$i; done
  ```
  To list our the created Droplet by below command.
  ```bash
  docker-machine ls
  ```

* Configure Firewall Rules for Docker Swarm Traffic (Manager and Worker nodes).

  Access to the 'Swarm Manager node'.
  ```bash
  docker-machine ssh dev-server-manager-1
  ```
  
  Configure Firewall for Manager node.
  ```bash
  ufw allow 22/tcp
  ufw allow 2376/tcp
  ufw allow 2377/tcp
  ufw allow 7946/tcp
  ufw allow 7946/udp
  ufw allow 4789/udp
  ```

  Reload UFW
  ```bash
  ufw reload
  ```

  Access to the 'Swarm Worker node', remember do the following steps for dev-server-node-2.
  ```bash
  docker-machine ssh dev-server-node-1
  ```
  Configure firewall for Worker node
  ```bash
  ufw allow 22/tcp
  ufw allow 2376/tcp
  ufw allow 7946/tcp 
  ufw allow 7946/udp 
  ufw allow 4789/udp 
  ```

  Reload UFW
  ```bash
  ufw reload
  ```

  >TCP port 2376 for secure Docker client communication. This port is required for Docker Machine to work. Docker Machine is used to orchestrate Docker hosts.
  TCP port 2377. This port is used for communication between the nodes of a Docker Swarm or cluster. It only needs to be opened on manager nodes.
  TCP and UDP port 7946 for communication among nodes (container network discovery).
  UDP port 4789 for overlay network traffic (container ingress networking).


* Initialize Cluster Manager.

  Access to the Swarm Manager node
  ```bash
  docker-machine ssh dev-server-manager-1
  ```
  Configure node as the Swarm manager. 200.123.123.123 is the Droplet IP which would like set as manager, and keep the swarm token from the output, the swarm token will need to use for next setup.
  ```bash
  docker swarm init --advertise-addr 200.123.123.123
  ```

  Access to the Swarm Worker node
  ```bash
  docker-machine ssh dev-server-node-1
  ```

  Add node to cluster. 200.123.123.123 is the manager ip
  ```bash
  docker swarm join --token TWMDGE-1-111117bxv37f6rj4q5dibfj7wtttvqv11111hf1idn7g7s57qh-2n4n5i27ep9xgbtg2tzfgb56n 200.123.123.123:2377
  ```

  In the Manager node (dev-server-manager-1), to list out the node.
  ```bash
  docker node ls
  ```

## Docker Image
### Docker Hub
Build a custom and push to Docker Hub

Build image
```bash
docker build -t jinlong/backend . 
```

Push to Docker Hub
```bash
docker push jinlong/backend    
```
Copy yml compose file to manager
```bash
docker-machine scp docker-compose-emqx.yml dev-server-manager-1:~
```

Deploy to manager
```bash
docker-machine ssh dev-server-manager-1 "docker stack deploy -c docker-compose-emqx.yml emqx"
```

### Private Hub (Container Registry)
Through the .dockerfile to build the image and name it:
```bash
docker build -t registry.gitlab.com/ldms/nodus/server/nodus-2.0/container_registry .
```

Push the image to container registry:
```bash
docker push registry.gitlab.com/ldms/nodus/server/nodus-2.0/container_registry
```

Pull the image from the container registry:
```bash
docker pull registry.gitlab.com/ldms/nodus/server/nodus-2.0/container_registry/registration_service
```

Example to deploy the image from container registry:
```bash
docker-machine ssh dev-server-manager-1 "docker login -u jinlong@gmail.com -p akq-oMcL-jJfyHCGCAVd registry.gitlab.com && docker stack deploy -c docker-compose.yml registration_service --with-registry-auth”
```

>registry-auth option with docker service create, informs the manager to forward the registry credentials to swarm workers.
As per the documentation,
–registry-auth Send registry authentication details to Swarm agents
you don’t need to login in each swarm node individually. Think about bringing up new swarm workers. it should pull the images automatically when we scale the service and distributed to new swarm workers._

Microservice Project, build your custom docker image with Dockerfile
```docker
FROM node:12.16.1-alpine3.11

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
COPY package.json .
COPY package-lock.json .
# For npm@5 or later, copy package-lock.json as well
# COPY package.json package-lock.json .

RUN npm install

# Bundle app source
COPY . .

EXPOSE 8080
CMD [ "npm", "start" ]
```

docker-compose.yml
```yml
version: "3"
services:
  app:
    image: registry.gitlab.com/project/registration_service
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
    ports:
      - "4000:8080"
    networks:
      - webnet
networks:
  webnet:
```

Gitlab CI/CD
variables:

CI_BUILD_TOKEN: 
```text
akq-oMcL-jJfyHCGCAVd
```

CI_REGISTRY:
```text
registry.gitlab.com
```

CI_REGISTRY_IMAGE:
```text
registry.gitlab.com/project/registration_service
```

CI_REGISTRY_USER:
```text
jinlong@gmail.com
```

TLSCACERT:
```text
-----BEGIN CERTIFICATE----- MIICyjDDAbKgAwIBAgIRAKu7s+0xkku50wJekzmnldMwDQYJKoZIhvcNAQSSBQAw DjEMMAoGA1UEChMDamltMB4XDTIwMDMyOTE1MjUwMFoXDTIzMDMxNDE1MjUwMFow DjEMMAoGA1UEChMDamltMIIBIjANBgkqhkiG9w0BAQEFAAORTVEAMIIBCgKCAQEA gooIrTwQy1+z7rdX6S82X9d/exQfKKJrK/1j5i2rmdZWyUXL6dE+0QzCR7AYReeJ z3k2bKPbiPxl2Wk3drHc9T7hB8SMGVwfrHGPmCtBAlAs8xkUYZpoR7BsQNjzVaTk QJ4snmijGhZcmPJNF48pKe0bdKJStZut66PHG1/JBsB+cWpB8VFD6CDF4iM9siGT EwwwZmR/GRsAe0bFYtXZx+JaxzgYNEnvjkQ/cQ8kBwfbQnJvDYnIZOyY5C6W4rX7 qMKBCa6MB0QyjgEF0H76ofHnbl/0A7YLKD+ck62lEvdHofdpbI7Lu1OjqAASswx+ OldT0uXEXzQOoTF2qA9NewIDAQABoyMwITAOBgNVHQ8BAf8EBAMCAqwwDwYDVR0T AQH/BAUwAwEB/zANBgkqhkiG9w0BAQsFAAOCAQEAie8yJy8RTHpwnnzn87XOjum7 E4+U4ICOIXIiF1f+RnipZ+E8N+9jhEuzYxVA8kSQRIJzwDoX5Usf+yBcjB4Jp4wi vfLhrjmINbuCgkShn505KsvqYhlriXFooMHtgE0Fyj1MGJ2lIvJSnJ4UoL8oiyVp d89AGQN9/hUzg+/3cOWUrIJqmtD6ps4HKyUkA0kTieKzVGe5LeB078N/HZ98Qh1C 7KiKc47cSfSOWk5Ugodg0IovpgRM72lIj/7DEVe9hqEu4Cf3gZe7LKOyY1NUX8VD 6Q17f3OF28taD1uZarScCR+zQcmdkeCitQXKHE/Drp7RlKT49M1mSSqErysRUg== -----END CERTIFICATE-----
```

TLSCERT: 
```text
-----BEGIN CERTIFICATE----- MIIC6DDDAdCgAwIBAgIRAMMHTuG3HQ2M4ZWQuDpPwz8wDQYJKoZIhvcNAQDEBQAw DjEMMAoGA1UEChMDamltMB4XDTIwMDMyOTE1MjUwMFoXDTIzMDMxNDE1MjUwMFow GjWWDCYGA1UECgwPamltLjxib290c3RyYXA+MIIBIjANBgkqhkiG9w0BAQEFAAOC QQWSMIIBCgKCAQEAvKA8pPrmdooTIdVjhEEWFWG2odUz/8kSF/lnc4W6HZ1ti5QQ L1K6Qb/Bt8IQlF/+Ai/QPFaRRTRRwzyxh9rhheUe47zDcr7/BPhyXn2N3unx1mq+ MD89FBvBhyg6ocq+CwtZhiZ6LMNVVcn0oHY0dgtN18fuqJtmDyX9dhB841GOFyXg 2Oo3xwXW/Ac5GNLeOnyv889D0umBkkZpwMaIB0eRotJUNiOIzi9eZN7GdcmD04Fh 2Gtt5lLMRuz6XbXBGW8QnIcUssz2oiHUMiG0TsgGUKTUul119rG6QNEQ9AqnKyrM zegzzdql1S3kqHXEwlZySE5X6Le9fANymVpBswIDAQABozUwMzAOBgNVHQ8BAf8E BAMCB4AwEwYDVR0lBAwwCgYIKwYBBQUHAwIwDAYDVR0TAQH/BAIwADANBgkqhkiG 9w0BAQsFAAOCAQEAmvc8unFyzsYqklDJbtCetem85wulfSozdCcb2CB99pt284LB 33fJST5ZdGlZjnBF3L0Kbespku/mjm/8jfalS0XMA34jRcPb+q8YtKdMHHAI6J6Z 4P9FHMd0xmIPt6qgIs61Gl/m+MBQE7oSaZEx71pi5ebjHDfxjL6SWMunSnWs9rTL m7q+VlUZK6Fh2NBvziiBqFB+94a87sMAPjcJjobYrLnbmsYehNtEhl5Hg+OdML+L YUK9oz0iaMQ8A++IHhjsEPQbUXKpZNhFB+yzKrQ2Dp0OE+jzd9DDwni/7162Z7OG QaOwKaurAnB9+rU/cCLuKEeLSovxeg138JVuaw== -----END CERTIFICATE-----
```

TLSKEY:
```text
-----BEGIN RSA PRIVATE KEY----- MIIEpgIBDDKCAQEAvKA8pPrmdqqTIdVjhEEWFWG2odUz/8kSF/lncDHEDZ1ti5QQ L1K6Qb/Bt8IQlF/+Ai/QPFaECDERwzyxh9rhheUe47zDcr7/BPhyXn2N3unx1mq+ MD89FBvBhyg6ocq+CwtZhiZ6LMNVVcn0oHY0dgtN18fuqJtmDyX9dhB841GOFyXg 2Oo3xwXW/Ac5GNLeOnyv889D0umBkkZpwMaIB0eRotJUNiOIzi9eZN7GdcmD04Fh 2Gtt5lLMRuz6XbXBGW8QnIcUssz2oiHUMiG0TsgGUKTUul119rG6QNEQ9AqnKyrM zegzzdql1S3kqHXEwlZySE5X6Le9fANymVpBswIDAQABAoIBAQC4owUZ3Tswd5Go wa9LK0bJoT9bhE4FKDYc35ZDP3hiOtKbGVEKcak+aMzf6ge+pYQHcfQcy/cwzgUw nj2NJVUycofdFs4eiRUqMWQKWE5CELAYWZ5Xrd9EyrKScW+LJeEHAJIePZXEMWRb uHdLhsltbz1AZkWYJ50sYNyM8DC/dUtBuK8TfNct9PEtGSksgQcNpvDJNN2J1A31 2FiVpKJ7SF6tS9vQpyHE3FBd/yMhz5NEHZhQJZODD6eCLzUA6cigBYJUS2J2bKJK PkweshEF0HWf9NQAYqKgjUtFTZTxX5aEHyfFg8COTSU2UUu8TTc5qRQhqr/7utsh XeeERCEBAoGBAM2lbr/CMMjDEpWT/MvlNjrhpZmd2B8KIFTA/goxkDaURJQffPkg ikAH9X5F6hqvCKKoDp47CwKOauB+BtQZzN0DpUUVB4x+bgUvOH+4fthlgR6IlLE/ K/mBpWAUbZZsIAbzSyr8ZvVyqTlVcnUw0HjeG3LEE16wZEz1HD1u4G8BAoGBAOrP 6tYIfYIkovjt4zjTlvD4G8Js2UuN17COGp6YQQQ0LzvrS0NeylCdqE9cePt4+V+8 gKtquHkRhflONapE875CVq9qmXjJJLrKUqbrr7Ostb8CDnBTulXR018TT/WvPdmV hZzoQ9srhtcaAZLfmcHwAGCTmDcFMfmeXh6bUKSzAoGBALk9ncdKC1usrF7kkUCx VeowPFVl47oCCyGNPsi74KXpZOTwgEiA0UfgY3i9TaV/qY2rSI+rq70/wHI707bX Dry/fe3+hXA+qqgs4v3ZmRkgRfDVIuuOdfQonmkvvY9UEn1wRnOQLMZH5LtqhtGd iQx3Nc5pZPFUfuT7ndaQu8YBAoGBALeAZPV/DRZ3qyT+Q2RVdtA6FPeMOQRihPzl EEiAE89FOOSKrPapTVJS4wWiLqnXkIrQtX1AmYmDuD9sb2PxHsrA7R2JIQbTqnCP TKhF57lyb1YmNUJzLFuE7rqOTR6jJKK1xwUoPl85VNck67XsKCz3clQHBB17YeLB cKxalFshAoGBAJ0o6Hn+34wGPMmFyMfnY5/Ihir9W8LR3uz3DrKTHHgHdaHtQr57 i1V4i/1gbxF5ErCN1o3URu44JO+ixS7KQn65BBCX3dxSXqks5uOHmmeH8Q7UM7N8 D9vlr/Ghjap5Ei1NjBZPJIoxGzE9PQWERju7TmhgLqYKKCg0GiM6hCQP -----END RSA PRIVATE KEY-----
```

```yml
image: docker:19.03.8
#image: docker:git

stages:
  - build
  # - test
  - deploy

variables:
  CONTAINER_TEST_IMAGE: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG-$CI_COMMIT_SHORT_SHA
  CONTAINER_RELEASE_IMAGE: $CI_REGISTRY_IMAGE:latest

before_script:
  #- docker login -u $CI_REGISTRY_USER -p $CI_BUILD_TOKEN $CI_REGISTRY

build:
  stage: build
  services:
    - docker:19.03.8-dind
    #- docker:dind
  script:
    - echo "Building the app"
    - docker login -u $CI_REGISTRY_USER -p $CI_BUILD_TOKEN $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE .
    - docker push $CI_REGISTRY_IMAGE
      
#test:
#  stage: test
#  script:
#    - echo "Running tests"
#    - docker pull $CONTAINER_TEST_IMAGE
#    - docker run $CONTAINER_TEST_IMAGE /script/to/run/tests

deploy_staging:
  stage: deploy
  variables:
    DOCKER_HOST: tcp://121.89.93.56:2376
    DOCKER_TLS_VERIFY: 1
    SERVICE_NAME: registration_service
  script:
    - echo "Deploy to staging server"
    #- docker pull $CONTAINER_TEST_IMAGE
    #- docker tag $CONTAINER_TEST_IMAGE $CONTAINER_RELEASE_IMAGE
    #- docker push $CONTAINER_RELEASE_IMAGE
    - mkdir -p ~/.docker
    - echo "$TLSCACERT" > ~/.docker/ca.pem
    - echo "$TLSCERT" > ~/.docker/cert.pem
    - echo "$TLSKEY" > ~/.docker/key.pem
    - docker login -u $CI_REGISTRY_USER -p $CI_BUILD_TOKEN $CI_REGISTRY
    - docker stack deploy -c docker-compose.yml ${SERVICE_NAME} --with-registry-auth   
  environment: staging
  only:
    - master
    
deploy_prod:
  stage: deploy
  script:
    - echo "Deploy to production server"
    - docker pull $CONTAINER_TEST_IMAGE
    - docker tag $CONTAINER_TEST_IMAGE $CONTAINER_RELEASE_IMAGE
    - docker push $CONTAINER_RELEASE_IMAGE
  environment: production
  only:
  - tags
    
#deploy:
#  stage: deploy
#  script:
#    - ./deploy.sh
#  only:
#    - master
#  except:
#    - master    
```

## yml sample
### EMQX
EMQX with two cluster, and not to install EMQX to Manager node.
```yaml
version: '3'

services:
  emqx1:
    image: emqx/emqx:latest
    environment:
    - "EMQX_NAME=emqx"
    - "EMQX_HOST=node1.emqx.io"
    - "EMQX_CLUSTER__DISCOVERY=static"
    - "EMQX_CLUSTER__STATIC__SEEDS=emqx@node1.emqx.io, emqx@node2.emqx.io"
    networks:
      emqx-net:
        aliases:
        - node1.emqx.io
    ports:
        - 18083:18083
        - 1883:1883
    deploy:
          placement:
            constraints:
              - "node.hostname!=dev-server-manager-1"
  emqx2:
    image: emqx/emqx:latest
    environment:
    - "EMQX_NAME=emqx2"
    - "EMQX_HOST=node2.emqx.io"
    - "EMQX_CLUSTER__DISCOVERY=static"
    - "EMQX_CLUSTER__STATIC__SEEDS=emqx@node1.emqx.io, emqx@node2.emqx.io"
    networks:
      emqx-net:
        aliases:
        - node2.emqx.io
    deploy:
      placement:
        constraints:
          - "node.hostname!=dev-server-manager-1"

networks:
  emqx-net:
```

### Kafka
Kafka cluster

```yaml
version: '3.2'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    hostname: zookeeper
    volumes:
      - zoo-service-data:/tmp/zookeeper
    ports:
      - "2181:2181"
    networks:
      - kafka-net
    deploy:
      mode: global
      placement:
        constraints:
          - "node.hostname==dev-server-manager-1"

  kafka1:
    image: wurstmeister/kafka:2.12-2.4.1
    hostname: kafka1
    volumes:
      - kafka-service-logs-1:/tmp/kafka-logs
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "9096:9096"
    networks:
      - kafka-net
    deploy:
      mode: global
      placement:
        constraints:
          - "node.hostname==dev-server-manager-1"
    environment:
      KAFKA_BROKER_ID: 1
      HOSTNAME_COMMAND: "docker info | grep 'Node Address:' | cut -d' ' -f 4"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: INSIDE://:9092,OUTSIDE://_{HOSTNAME_COMMAND}:9096
      KAFKA_LISTENERS: INSIDE://:9092,OUTSIDE://:9096
      KAFKA_INTER_BROKER_LISTENER_NAME: INSIDE

  kafka2:
    image: wurstmeister/kafka:2.12-2.4.1
    hostname: kafka2
    volumes:
      - kafka-service-logs-2:/tmp/kafka-logs
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "9097:9097"
    networks:
      - kafka-net
    deploy:
      mode: global
      placement:
        constraints:
          - "node.hostname==dev-server-node-1"
    environment:
      KAFKA_BROKER_ID: 2
      HOSTNAME_COMMAND: "docker info | grep 'Node Address:' | cut -d' ' -f 4"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: INSIDE://:9092,OUTSIDE://_{HOSTNAME_COMMAND}:9097
      KAFKA_LISTENERS: INSIDE://:9092,OUTSIDE://:9097
      KAFKA_INTER_BROKER_LISTENER_NAME: INSIDE

  kafka3:
    image: wurstmeister/kafka:2.12-2.4.1
    hostname: kafka3
    volumes:
      - kafka-service-logs-3:/tmp/kafka-logs
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "9098:9098"
    networks:
      - kafka-net
    deploy:
      mode: global
      placement:
        constraints:
          - "node.hostname==dev-server-node-2"
    environment:
      KAFKA_BROKER_ID: 3
      HOSTNAME_COMMAND: "docker info | grep 'Node Address:' | cut -d' ' -f 4"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: INSIDE://:9092,OUTSIDE://_{HOSTNAME_COMMAND}:9098
      KAFKA_LISTENERS: INSIDE://:9092,OUTSIDE://:9098
      KAFKA_INTER_BROKER_LISTENER_NAME: INSIDE

  kafka-manager:
    image: sheepkiller/kafka-manager:latest
    ports:
      - "9000:9000"
    networks:
      - kafka-net
    deploy:
      placement:
        constraints:
          - "node.hostname==dev-server-manager-1"
    environment:
      ZK_HOSTS: zookeeper:2181
      
networks:
  kafka-net:

volumes:
  kafka-service-logs-1:
  kafka-service-logs-2:
  kafka-service-logs-3:
  zoo-service-data:
```

To verify below command
```yaml
  PORT_COMMAND: "docker port $(HOSTNAME) 9092/tcp | cut -d':' -f 2"
```

Test
```bash
bin/kafka-topics.sh --create --zookeeper 121.89.93.56:2181 --replication-factor 1 --partitions 1 --topic test
bin/kafka-topics.sh --describe --zookeeper 121.89.93.56:2181 --topic test
bin/kafka-topics.sh --list --zookeeper 121.89.93.56:2181
bin/kafka-console-producer.sh --broker-list 121.89.93.56:9096 --topic test
bin/kafka-console-consumer.sh --bootstrap-server 121.89.93.56:9096 --topic test --from-beginning
```

### SWARMPROM
```yaml
version: "3.3"

networks:
  net:
    driver: overlay
    attachable: true

volumes:
    prometheus: {}
    grafana: {}
    alertmanager: {}

configs:
  caddy_config:
    file: ./caddy/Caddyfile
  dockerd_config:
    file: ./dockerd-exporter/Caddyfile
  node_rules:
    file: ./prometheus/rules/swarm_node.rules.yml
  task_rules:
    file: ./prometheus/rules/swarm_task.rules.yml

services:
  dockerd-exporter:
    image: stefanprodan/caddy
    networks:
      - net
    environment:
      - DOCKER_GWBRIDGE_IP=172.18.0.1
    configs:
      - source: dockerd_config
        target: /etc/caddy/Caddyfile
    deploy:
      mode: global
      resources:
        limits:
          memory: 128M
        reservations:
          memory: 64M

  cadvisor:
    image: google/cadvisor
    networks:
      - net
    command: -logtostderr -docker_only
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /:/rootfs:ro
      - /var/run:/var/run
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    deploy:
      mode: global
      resources:
        limits:
          memory: 128M
        reservations:
          memory: 64M

  grafana:
    image: stefanprodan/swarmprom-grafana:5.3.4
    networks:
      - net
    environment:
      - GF_SECURITY_ADMIN_USER=${ADMIN_USER:-admin}
      - GF_SECURITY_ADMIN_PASSWORD=${ADMIN_PASSWORD:-admin}
      - GF_USERS_ALLOW_SIGN_UP=false
      #- GF_SERVER_ROOT_URL=${GF_SERVER_ROOT_URL:-localhost}
      #- GF_SMTP_ENABLED=${GF_SMTP_ENABLED:-false}
      #- GF_SMTP_FROM_ADDRESS=${GF_SMTP_FROM_ADDRESS:-grafana@test.com}
      #- GF_SMTP_FROM_NAME=${GF_SMTP_FROM_NAME:-Grafana}
      #- GF_SMTP_HOST=${GF_SMTP_HOST:-smtp:25}
      #- GF_SMTP_USER=${GF_SMTP_USER}
      #- GF_SMTP_PASSWORD=${GF_SMTP_PASSWORD}
    volumes:
      - grafana:/var/lib/grafana
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          memory: 128M
        reservations:
          memory: 64M

  alertmanager:
    image: stefanprodan/swarmprom-alertmanager:v0.14.0
    networks:
      - net
    environment:
      - SLACK_URL=${SLACK_URL:-https://hooks.slack.com/services/TOKEN}
      - SLACK_CHANNEL=${SLACK_CHANNEL:-general}
      - SLACK_USER=${SLACK_USER:-alertmanager}
    command:
      - '--config.file=/etc/alertmanager/alertmanager.yml'
      - '--storage.path=/alertmanager'
    volumes:
      - alertmanager:/alertmanager
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          memory: 128M
        reservations:
          memory: 64M

  unsee:
    image: cloudflare/unsee:v0.8.0
    networks:
      - net
    environment:
      - "ALERTMANAGER_URIS=default:http://alertmanager:9093"
    deploy:
      mode: replicated
      replicas: 1

  node-exporter:
    image: stefanprodan/swarmprom-node-exporter:v0.16.0
    networks:
      - net
    environment:
      - NODE_ID={{.Node.ID}}
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
      - /etc/hostname:/etc/nodename
    command:
      - '--path.sysfs=/host/sys'
      - '--path.procfs=/host/proc'
      - '--collector.textfile.directory=/etc/node-exporter/'
      - '--collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($$|/)'
      - '--no-collector.ipvs'
    deploy:
      mode: global
      resources:
        limits:
          memory: 128M
        reservations:
          memory: 64M

  prometheus:
    image: stefanprodan/swarmprom-prometheus:v2.5.0
    networks:
      - net
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention=${PROMETHEUS_RETENTION:-24h}'
    volumes:
      - prometheus:/prometheus
    configs:
      - source: node_rules
        target: /etc/prometheus/swarm_node.rules.yml
      - source: task_rules
        target: /etc/prometheus/swarm_task.rules.yml
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          memory: 2048M
        reservations:
          memory: 128M

  caddy:
    image: stefanprodan/caddy
    ports:
      - "3000:3000"
      - "9090:9090"
      - "9093:9093"
      - "9094:9094"
    networks:
      - net
    environment:
      - ADMIN_USER=${ADMIN_USER:-admin}
      - ADMIN_PASSWORD=${ADMIN_PASSWORD:-admin}
    configs:
      - source: caddy_config
        target: /etc/caddy/Caddyfile
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          memory: 128M
        reservations:
          memory: 64M
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000"]
      interval: 5s
      timeout: 1s
      retries: 5
```

### Visualizer
Visualizer

```yaml
version: "3"
services:
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "9090:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet
networks:
  webnet:
```

### MongoDb
MongoDb

```yaml
version: '3.1'

services:

  mongo:
    image: mongo:4.2-bionic
    restart: always

  mongo-express:
    image: mongo-express:0.54
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_PORT: 27017
```

## Docker Command
* Restart Docker
  ```bash
  sudo /etc/init.d/docker restart
  ```

* To access into Docker container, need to get the container id first:
  ```bash
  docker ps -a
  ```

  Inster the container id (1255075412) as below:
  ```bash
  docker exec -it 1255075412 sh
  ```
  You may hit tab twice to show the available command from the screen.

* Remove Docker Image with filer by name
  ```bash
  docker rmi $(docker images 'imagename' -q)
  ```

# Reference

[Docker Swarm and DigitalOcean](https://docs.docker.com/machine/drivers/digital-ocean/)

[Configure Firewall for Docker Swarm](https://www.digitalocean.com/community/tutorials/how-to-configure-the-linux-firewall-for-docker-swarm-on-ubuntu-16-04)

[Differernt between Docker Compose and Docker Stack](https://vsupalov.com/difference-docker-compose-and-docker-stack/) 

[Docker Swarm and DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-create-a-cluster-of-docker-containers-with-docker-swarm-and-digitalocean-on-ubuntu-16-04)

[Gitlab Container Registry Document](https://docs.gitlab.com/ee/user/packages/container_registry/)

[Gitlab Container Registry Blog](https://about.gitlab.com/blog/2016/05/23/gitlab-container-registry/)

[Docker Swarm Cluster for EMQX](https://github.com/emqx/emqx-docker/issues/13)

[EMQX Cluster](https://docs.emqx.io/broker/v3/en/cluster.html#)

[EMQX Cluster Tutorial](https://docs.emqx.io/tutorial/latest/en/cluster/whats_cluster.html)

[Kafka Docker](https://github.com/wurstmeister/kafka-docker/wiki/Connectivity)

[Running Kafka in Docker Machine](https://medium.com/@marcelo.hossomi/running-kafka-in-docker-machine-64d1501d6f0b)

[Microservice and Deploying it with Docker](https://medium.com/@cramirez92/build-a-nodejs-cinema-microservice-and-deploying-it-with-docker-part-1-7e28e25bfa8b)

[Microservice to a Docker Swarm Cluster](https://towardsdatascience.com/deploy-a-nodejs-microservices-to-a-docker-swarm-cluster-docker-from-zero-to-hero-464fa1369ea0#.548ni3uxv)



￼