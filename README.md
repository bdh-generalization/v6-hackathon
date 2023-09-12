# v6-hackathon

## Prerequisites
- Make sure you have installed vantage6 locally following [the tutorial](https://github.com/CARRIER-project/vantage6-local-setup).
- Make sure you have the newest version of vantage6

## Preparation
Create a fresh collaboration and and 3 nodes to work on.

## Introduction
We are going to develop 3 types of algorithms for vantage6 and test them out on our local system.
- A simple parallel algorithm with no communication between nodes
- An algorithm that consists of 2 parts, communicating with the legacy method
- An algorithm with node-to-node communication.

## Algorithm cookiecutter
There is a [cookiecutter for vantage6 algorithms](https://github.com/vantage6/cookiecutter_algorithm_python) that can help you create your own algorithm.
Follow the instructions in the repo to start your own. Name the algorithm and the repo `v6_hackathon_[YOUR_NAME]`. Adding your name will make sure you won't overwrite your colleagues algorithm in the docker registry later.

### Using poetry
Personally I prefer to use poetry to manage dependencies. In order to use this, you will need to replace the requirements.txt with a pyproject.toml.
This can be done automatically as follows:
- Go into the folder you have just created with the cookiecutter
- Execute `poetry init`
- Follow the wizard

In our case, make sure you have `vantage6-client = "3.11.0"`

Then install the dependencies with
```shell
poetry install
```

## Building our algorithm container
```shell
docker build -t some_name .
```


## Simple parallel algorithm


## 2 part algorithm

## Algorithm with node-to-node communication
For this algorithm we have to make sure that vpn is correctly set up.
