## Concourse CI Build Docker Images

This repo includes [Concourse CI](https://concourse-ci.org/) build configuration for the [Voting App repo](https://github.com/salmaniqbal/azure-voting-app-redis)

Councourse CI is an Open Source Configuration As Code CI/CD tool.  

Concourse uses `Makefiles` to allow users to create pipelines.

Concourse is primarliy driven from the [`fly` cli](https://concourse-ci.org/fly.html).

There's even a neat UI for users to keep an eye on the progress of their CI/CD or better yet, trigger a build/deployment.

Most importantly the whole of CI/CD pipeline is kept in Source Control. Gone are the days of tinkering with your build pipelines in the UI and then forgetting what was done!

---
### Run Concourse CI Locally

You can run concourse locally on your machine. Get the the `docker-compose` file from [here](wget https://raw.githubusercontent.com/starkandwayne/concourse-tutorial/master/docker-compose.yml).

Run:  
`docker-compose up`

You can access concourse by accessing: [http://127.0.0.1:8080/](http://127.0.0.1:8080/)  
__Click on the OS to download to download the *fly* CLI__
Have a look in the `docker-compose` file for the login credentinals.

Check [this](https://concoursetutorial.com/) website out for a series of excellent tutorials.

---
### Create Pipeline

Open your terminal, connect to concourse:  
`fly --target tutorial login --concourse-url http://127.0.0.1:8080 -u admin -p admin`  
`fly --target tutorial sync`

In the current repository, run the following command:  
`fly -t tutorial sp -p voting-app -c pipeline.yml -v docker-hub-username=yourusername -v docker-hub-password=yourpassword`

Unpause the pipeline:
`fly -t tutorial unpause-pipeline -p voting-app`

Navigate to [http://127.0.0.1:8080/](http://127.0.0.1:8080/), you should now see a pipline called voting-app created. Click on it, go into the 'Publish' part and kick off a new build by clicking the '+' button. This build will create a new image for the voting app and push it to your docker hub registry.


If you want to destroy a pipeline:  
`fly -t tutorial unpause-pipeline -p pipeline-name`

---
### CI/CD Pipeline

`voteapp-cicd.yml` creates a pipeline that builds the docker image from [Voting App repo](https://github.com/salmaniqbal/azure-voting-app-redis), pushes it to docker hub and then deploy to the Azure Kubernetes Cluster.

In order to create the pipeline, the secrets are taken from `params.yml` file. Fill the details here and run the following command to create the pipeline:  
`fly -t tutorial sp -p vote-cicd -c voteapp-cicd.yml --load-vars-from params.yml`  
`fly -t tutorial up -p vote-cicd`

---
### AWS Lambda Serverless Framework Pipeline

`serverless.yml` creates a pipeline that deploys an AWS lambda checked into [AWS Lambda python repo](https://github.com/salmaniqbal/python-serverless.git). The repo is initiated using [Serverless Framework](https://serverless.com/). Pipeline also uses Serverless Framework to deploy AWS Lambda.

Create pipeline using the following command:
`fly -t tutorial sp -p serverless-ci -c serverless.yml --load-vars-from params.yml`
`fly -t tutorial up -p serverless-ci`