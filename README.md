# Deployless
Deployless is inspired by the Serverless framework and allows to deploy functions on Serverless Environments with strong focus on python and Openwhisk.

Deployless exists mainly because I missed the functionality to deploy python actions that need a virtual environment.

# Supported Platforms
- OpenWhisk

# Supported Languages
- Python 3.6

# Requirements 
- Python 3.7 (tested manually)
- Docker

# Setup
- clone the repository and move the `src/deployless.py` file to your `$PATH` using `mv src/deployless.py $PATH/deployless`
- in your project root create a `deployless.yaml` 

This is the configuration file of your deployless project. 
It contains all the actions and configurations for this actions.

The following configuration will create the actions `hello-world` and `requirements-action`:
```yaml
service_name: example

provider:
  platform: openwhisk
  ignore-certs: true
  api-host: 172.17.0.3:31001
  auth: 23bc46b1-71f6-4ed5-8c54-816aa4f8c502:123zO3xZCLrMN6v2BKK1dXYFpXlPkccOFqm12CdAsMgRU4VrNZ9lyGVCGuMDGIwP

actions:
  hello-world:
    main: src/testfunctions/hello-world/hello-world.py
    kind: python:3
    web: true
    dependencies: [src/utils/someutil.py]

  requirements-action:
    requirements: src/testfunctions/requirements-test/requirements.txt
    main: src/testfunctions/requirements-test/requirements-test.py
    kind: python:3
```
The dependencies keyword allows you to add a list of python files that are coupled to this action. They will be uploaded with this action and it will be able to access them. For the example of `hello-world` this happens by using `import someutil`.
If you want to test the function locally you can use the following:

```
try:
    import someutil
except ImportError:
    import src.utils.someutil
```

The minimal configuration is currently a provider containing platform, api-host, auth.

The minimal configuration for an action is currently the main file, containing a main method, and the kind.

# Usage
Run `deployless` in your project root to deploy your actions

Run `deployless --run <action_name>` to invoke a action and get its results

Run `deployless --clear` to delete all actions that are configured in your `deployless.yaml` 
###Missing Features
- actions
    - timeout configuration
    - memory configuration
- platform
    - use wsk file if api-host and auth are not provided
- framework
    - functionality to create projects using `--init`
- tests :) 

###Known issues
- sometimes the actions are deployed but are not returning binaries (edge case)
    - here you can just deploy again and it should work 
- Virtual environments for python actions is created and not removeable without sudo rights since it is created by a docker container to be compatible with openwhisk
