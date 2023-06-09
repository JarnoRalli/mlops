# 1 Docker

This directory explains how you can run self-hosted CI runners in Azure.

# 2 Linux

In order to use a self-hosted Linux based CI runner, you first need to create a docker image and then run
a container based on that image.

## 2.1 Create a Docker Image

In order to create a Docker image used as a self-hosted CI runner, execute the following:

```bash
docker build -t dockeragent:latest .
```

## 2.2 Launch the Docker Container

Now that you have created an image, you can run the container as follows:

```bash
docker run -e AZP_URL=<Azure DevOps instance> -e AZP_TOKEN=<PAT token> -e AZP_AGENT_NAME=mydockeragent dockeragent:latest --once
```

, where
* `<Azure DevOps instance>` the URL of your DevOps instance, e.g. `https://dev.azure.com/<ORGANIZATION NAME>`
* `<PAT token>` is an access token that is required by the container to get access to your DevOps instance

