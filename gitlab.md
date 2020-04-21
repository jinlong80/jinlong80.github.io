# Gitlab

## CI/CD

* Example of .gitlab-ci.yml file to build the nodejs project (npm install) at Gitlab Docker.
  ```yml
  #image: trion/ng-cli-karma
    
  cache:
    paths:
      - node_modules/

  before_script:
    ##
    ## Install ssh-agent if not already installed, it is required by Docker.
    ## (change apt-get to yum if you use an RPM-based image)
    ##
    - 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client -y )'

    ##
    ## Run ssh-agent (inside the build environment)
    ##
    - eval $(ssh-agent -s)

    ##
    ## Add the SSH key stored in SSH_PRIVATE_KEY variable to the agent store
    ## We're using tr to fix line endings which makes ed25519 keys work
    ## without extra base64 encoding.
    ## https://gitlab.com/gitlab-examples/ssh-private-key/issues/1#note_48526556
    ##
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -

    ##
    ## Create the SSH directory and give it the right permissions
    ##
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh

    ##
    ## Assuming you created the SSH_KNOWN_HOSTS variable, uncomment the
    ## following two lines.
    ##
    ##- echo "$SSH_KNOWN_HOSTS" > ~/.ssh/known_hosts
    ##- chmod 644 ~/.ssh/known_hosts

    ##
    ## Alternatively, use ssh-keyscan to scan the keys of your private server.
    ## Replace example.com with your private server's domain name. Repeat that
    ## command if you have more than one server to connect to.
    ##
    - ssh-keyscan 199.199.72.72 >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts

    ##
    ## You can optionally disable host key checking. Be aware that by adding that
    ## you are susceptible to man-in-the-middle attacks.
    ## WARNING: Use this only with the Docker executor, if you use it with shell
    ## you will overwrite your user's SSH config.
    ##
    ##- '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

    ##
    ## Optionally, if you will be using any Git commands, set the user name and
    ## and email.
    ##
    #- git config --global user.email "user@example.com"
    #- git config --global user.name "User name"
    
  deploy_stage:
    image: node:latest
    stage: deploy
    environment: Staging
    only:
      - master
    script:
      - npm install
      - npm -v
      - node -v
      - ls
      - scp -r * root@199.199.72.72:/root/project/backend/
  ```

* Example of .gitlab-ci.yml file to build the nodejs project (npm install) at own server.
  ```yml
  #image: trion/ng-cli-karma
    
  cache:
    paths:
      - node_modules/

  before_script:
    ##
    ## Install ssh-agent if not already installed, it is required by Docker.
    ## (change apt-get to yum if you use an RPM-based image)
    ##
    - 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client -y )'

    ##
    ## Run ssh-agent (inside the build environment)
    ##
    - eval $(ssh-agent -s)

    ##
    ## Add the SSH key stored in SSH_PRIVATE_KEY variable to the agent store
    ## We're using tr to fix line endings which makes ed25519 keys work
    ## without extra base64 encoding.
    ## https://gitlab.com/gitlab-examples/ssh-private-key/issues/1#note_48526556
    ##
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -

    ##
    ## Create the SSH directory and give it the right permissions
    ##
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh

    ##
    ## Assuming you created the SSH_KNOWN_HOSTS variable, uncomment the
    ## following two lines.
    ##
    ##- echo "$SSH_KNOWN_HOSTS" > ~/.ssh/known_hosts
    ##- chmod 644 ~/.ssh/known_hosts

    ##
    ## Alternatively, use ssh-keyscan to scan the keys of your private server.
    ## Replace example.com with your private server's domain name. Repeat that
    ## command if you have more than one server to connect to.
    ##
    - ssh-keyscan 199.199.72.72 >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts

    ##
    ## You can optionally disable host key checking. Be aware that by adding that
    ## you are susceptible to man-in-the-middle attacks.
    ## WARNING: Use this only with the Docker executor, if you use it with shell
    ## you will overwrite your user's SSH config.
    ##
    ##- '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

    ##
    ## Optionally, if you will be using any Git commands, set the user name and
    ## and email.
    ##
    ## - git config --global user.email "jinlong80@gmail.com"
    ## - git config --global user.name "jim"
    
  deploy_stage:
    image: node:latest
    stage: deploy
    environment: Staging
    only:
      - master
    script:
      - ssh root@199.199.72.72 "cd nodus-2.0/backend/ && git checkout master && git pull origin master && npm install && npm -v && node -v && ls && exit"
  ```