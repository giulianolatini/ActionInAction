# Hands-on: a docker container action in action

In this hands-on lab you will create a docker container action that uses input and output parameters. Furthermore, you will create a CI build that tests the action everytime y change is made to one of the files.

The lab consists of the following parts:

1. [Use the template to create a new repo](#use-the-template-to-create-a-new-repo)
2. [Create the dockerfile for the action](#create-the-dockerfile-for-the-action)
3. [Create the action.yml file](#create-the-actionyml-file)
4. [Create the entrypoint.sh file](#create-the-entrypointsh-file)

## Use the template to create a new repo

In this repository, under [Code](/), click on 'Use this template' and and select [Create new repository](/generate).

Pick your GitHub username as the owner and enter `MyActionInAction` as the repository name. Make the repository public and click `Create repository from template`.

## Create the dockerfile for the action

Create a [new file](new/main?filename=Dockerfile) called `Dockerfile``. Add the following content:

```Docker
# Container image that runs your code
FROM alpine:latest

# Copies your code file from your action repository to the filesystem path '/' of the container
COPY entrypoint.sh /entrypoint.sh

# Make the script executable
RUN chmod +x entrypoint.sh

# Code file to execute when the docker container starts up ('entrypoint.sh')
ENTRYPOINT ["/entrypoint.sh"]
```

The dockerfile defines the docker container for the action. You could also use an existing image but we want to build everything from scratch. We use the latest alpine image and just copy a simple shell script into the container.

Commit the file.

## Create the action.yml file

Create a [new file](new/main?filename=action.yml) called `action.yml`. Add the following content and replace the placeholder `{GitHub user name}` with your GitHub user name:

```YAML
name: '{GitHub user name}'s Action in Action'
description: 'Greet's someone and returns always 42.'
inputs:
  who-to-greet:  # id of input
    description: 'Who to greet'
    required: true
    default: 'World'
outputs:
  answer: # id of output
    description: 'The answer to everything (always 42)'
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.who-to-greet }}
```

This action file defines the action and the input and output parameters. The `runs` section is the part that defines the action type - in this case we use `docker` together with a `Dockerfile` instead of an image.

Commit the file.

## Create the entrypoint.sh file

Create a [new file](new/main?filename=entrypoint.sh) called `entrypoint.sh`. Add the following content:

```Bash
#!/bin/sh -l

echo "Hello $1"
echo "answer=42" >> $GITHUB_OUTPUT

```

The script just writes `Hello` and the input parameter `who-to-greet` to the console and sets the output parameter `answer` to 42.
