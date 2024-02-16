# gitlab-ci
frontend
```
sudo nano .gitlab-ci.yml
```

```
stages:
  - pulling-repository
  - build
  - test
  - push
  - pulling-new-image
  - deploy

variables:
  VAR_DIREKTORI: "/home/jeri77/fe-dumbmerch"
  VAR_GIT_URL: "git@gitlab.com:jerryfernando/fe-dumbmerch.git"
  VAR_USER: "jeri77"
  VAR_IP: "103.127.97.172"
  VAR_IMAGE: "jerifernando/fe-dumbmerch-staging:latest"
  VAR_USER_DOCKER: "jerifernando"
  VAR_PASS_DOCKER: "Jeri223344"

Pulling code:
  stage : pulling-repository
  image: docker
  services:
    - docker:dind
  before_script:
    - "which ssh-agent || ( apt-get install openssh-client )"
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan $VAR_IP >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

  script :
    - ssh $VAR_USER@$VAR_IP "cd $VAR_DIREKTORI && git pull github staging && docker compose down && exit"
    - echo "pulling code success!"

Dockerize:
  stage : build
  image: docker
  services:
    - docker:dind
  before_script:
    - "which ssh-agent || ( apt-get install openssh-client )"
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan $VAR_IP >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

  script : 
    - ssh $VAR_USER@$VAR_IP "cd $VAR_DIREKTORI && docker build -t $VAR_IMAGE ."
    - ssh $VAR_USER@$VAR_IP "docker compose up -d && exit "
    - echo "building application success!"

Testing:
  stage : test
  image: docker
  services:
    - docker:dind
  before_script:
    - "which ssh-agent || ( apt-get install openssh-client )"
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan $VAR_IP >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

  script : 
    - ssh $VAR_USER@$VAR_IP "cd $VAR_DIREKTORI"
    - ssh $VAR_USER@$VAR_IP "wget --spider 103.127.97.172:3000"
    - echo "testing application success!"

Push image to registry:
  stage : push
  image: docker
  services:
    - docker:dind
  before_script:
    - "which ssh-agent || ( apt-get install openssh-client )"
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan $VAR_IP >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

  script : 
    - ssh $VAR_USER@$VAR_IP "cd $VAR_DIREKTORI && docker login -u $VAR_USER_DOCKER -p $VAR_PASS_DOCKER && docker push $VAR_IMAGE && exit"
    - echo "Push image to registry success!"

#Pull new image:
#  stage : pulling-new-image
#  image: docker
#  services:
#    - docker:dind
#  before_script:
#    - "which ssh-agent || ( apt-get install openssh-client )"
#    - eval $(ssh-agent -s)
#    - mkdir -p ~/.ssh
#    - chmod 700 ~/.ssh
#    - ssh-keyscan $VAR_IP >> ~/.ssh/known_hosts
#    - chmod 644 ~/.ssh/known_hosts
#    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

#  script : 
#    - ssh $VAR_USER@$VAR_IP "cd $VAR_DIREKTORI && docker image pull $VAR_IMAGE && exit"
#    - echo "Pull image from registry success!"

Deploying:
  stage : deploy
  image: docker
  services:
    - docker:dind
  before_script:
    - "which ssh-agent || ( apt-get install openssh-client )"
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan $VAR_IP >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'

  script : 
    - ssh $VAR_USER@$VAR_IP "cd $VAR_DIREKTORI && docker compose up -d && exit"
    - echo "Deploy success!"




```
