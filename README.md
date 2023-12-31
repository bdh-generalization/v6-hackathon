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

Example `pyproject.toml`:
```toml
[tool.poetry]
name = "poetry-algorithm"
version = "0.1.0"
description = ""
authors = ["Djura Smits <djura.smits@gmail.com>"]
license = "Apache 2.0"
readme = "README.md"
packages = [{include = "poetry_algorithm"}]

[tool.poetry.dependencies]
python = "^3.10"                                                                                                                                                                            
vantage6-client = "3.11.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.4.2"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

Then install the dependencies with
```shell
poetry install
```

## Building our algorithm container
```shell
docker build -t some_name .
```

## Quick test
If you are now trying to run our docker image it will give an error because it is not running in the vantage6 ecosystem. You should get the following:
```shell
>> docker run some_name

Traceback (most recent call last):
  File "<string>", line 1, in <module>
info > wrapper for poetry_algorithm
  File "/usr/local/lib/python3.10/site-packages/vantage6/tools/wrapper.py", line 150, in docker_wrapper
    wrapper.wrap_algorithm(module, load_data, use_new_client, log_traceback)
  File "/usr/local/lib/python3.10/site-packages/vantage6/tools/wrapper.py", line 278, in wrap_algorithm
    input_file = os.environ["INPUT_FILE"]
  File "/usr/local/lib/python3.10/os.py", line 680, in __getitem__
    raise KeyError(key) from None
KeyError: 'INPUT_FILE'

```


## Simple parallel algorithm

Algorithm stub:


```python
import pandas as pd
from vantage6.client import ContainerClient


def RPC_mean(data: pd.DataFrame, *args, **kwargs):
  pass

```

## 2 part algorithm
Function stub:
```python
def count_total_records(client: ContainerClient, data: pd.DataFrame, *args, **kwargs)
  
```
Important functions:
- `client.get_organizations_in_my_collaboration()`
- `client.post_task('name doesn't matter', 'test', collaboration_id=client.collaboration_id,
                            input_={'method': 'count_records'}, organization_ids=org_ids)`

If you want to call this algorithm you should just call it for _one_ single organization. This one will be running the primary (master) container.

In your input you need to indicate it is a master algorithm with `{"method": "my_method", "master": True}`

## Debugging
If you want to print some info to debug your algorithm you can use the follwing:
```python
from vantage6.common import info
```

```python
info('Some information')
```

## Algorithm with node-to-node communication
For this algorithm we have to make sure that vpn is correctly set up.

### Setting up vpn on the vantage6 server
We need to point to the vpn server from our vantage6 server. You can see how to configure it in the [server configuration documentation](https://docs.vantage6.ai/en/main/server/configure.html).
The most important parts are these:
```yaml
    vpn_server:
        # the URL of your VPN server
        url: https://your-vpn-server.ext

        # OATH2 settings, make sure these are the same as in the
        # configuration file of your EduVPN instance
        redirect_url: http://localhost
        client_id: your_VPN_client_user_name
        client_secret: your_VPN_client_user_password

        # Username and password to acccess the EduVPN portal
        portal_username: your_eduvpn_portal_user_name
        portal_userpass: your_eduvpn_portal_user_password
```
We will use an existing eduvpn server.

### Setting up vpn on the vantage6 node
You need to add the vpn subnet to your node confguration (see [node admin docs](https://docs.vantage6.ai/en/main/node/configure.html))

After the configurations have been changed, restart the vantage6 server and all nodes:

```shell
# From the docker-compose directory
docker-compose down

vnode stop --all

docker-compose up -d

# Start all nodes
vnode start
vnode start
vnode start
```

### Checking the connections
You can check the connections by using the [n2n-diagnostics algorithm](https://github.com/vantage6/v6-diagnostics)

Check the [v6 docs](https://docs.vantage6.ai/en/main/algorithms/concepts.html#networking) for some info on developing with n2n communication.
Check the [containerclient api](https://docs.vantage6.ai/en/main/function-docs/client.html#vantage6.client.ContainerClient) to find the right functions for n2n.
