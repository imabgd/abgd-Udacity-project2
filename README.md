# Overview

This project will give you an opportunity to demonstrate your ability to perform continuous delivery for a Python-based machine learning application using the Flask web framework. You will apply the skills you have acquired in this course to operationalize a Machine Learning Microservice API.


## Project Plan
Please refer to following Links for Project Planning:

* A link to a Trello board for task tracking of the project
	https://trello.com/b/9VNP1ghv/todo
* A link to a spreadsheet that includes the original and final project plan
	https://drive.google.com/file/d/1vCmnPmU8vT62l98Dc3_tZdOrWL-aXz9n/view?usp=sharing


## Instructions
 
### Architectural Diagram (Shows how key parts of the system work)
In this project, you will build a Github repository from scratch and create a scaffolding that will assist you in performing both Continuous Integration and Continuous Delivery. You'll use Github Actions along with a Makefile, requirements.txt and application code to perform an initial lint, test, and install cycle. Next, you'll integrate this project with Azure Pipelines to enable Continuous Delivery to Azure App Service.
You are given a pre-trained, sklearn model that has been trained to predict housing prices in Boston according to several features, such as average rooms in a home and data about highway access, teacher-to-pupil ratios, and so on. If you're curious, you can read more about the data, which was initially taken from Kaggle, on the data source site. This project tests your ability to operationalize a Python flask app—in a provided file, app.py—that serves out predictions (inference) about housing prices through API calls. This project could be extended to any pre-trained machine learning model, such as those for image recognition and data labeling.
![image](https://user-images.githubusercontent.com/74898057/102742846-f8d9e500-437b-11eb-9283-1b06277a3c4a.png)

### CI: Set Up Azure Cloud Shell
In this step, you'll set up a cloud-based development structure using Azure Cloud Shell. You will create a Makefile, tests, and application scaffolding. For this initial project structure, you can use the scaffolding code from the examples below. Once you complete this step you will be able to test code locally in the Azure Cloud Shell. This is a local continuous integration step.
![image](https://user-images.githubusercontent.com/74898057/102742655-6fc2ae00-437b-11eb-96ba-9d0396dfc7ef.png)

    * Create a Github Repo (if not created)
    * Open Azure Cloud Shell
    * Create ssh-keys in Azure Cloud Shell
    * Upload ssh-keys to Github
    * Clone Repository in Azure Cloud Shell
![image](https://user-images.githubusercontent.com/74898057/102755310-377b9980-4394-11eb-81cc-1e8e91d575b6.png)

    abhijeet@Azure:~$ git clone git@github.com:imabgd/abgd-Udacity-project2.git
    Cloning into 'abgd-Udacity-project2'...
    The authenticity of host 'github.com (13.234.176.102)' can't be established.
    RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'github.com,13.234.176.102' (RSA) to the list of known hosts.
    remote: Enumerating objects: 4, done.
    remote: Counting objects: 100% (4/4), done.
    remote: Compressing objects: 100% (4/4), done.
    remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
    Receiving objects: 100% (4/4), done.
    abhijeet@Azure:~$

* Create scaffolding for project (if not created)
  - Makefile Should look similar to the file below

```bash
install:
	pip install --upgrade pip &&\
		pip install -r requirements.txt

test:
	python -m pytest -vv test_hello.py


lint:
	pylint --disable=R,C hello.py

all: install lint test
```

  - requirements.txt
  The requirements.txt should include:

```bash
pylint
pytest
```

* Create a python virtual environment and source it if not created

```bash
python3 -m venv ~/.myrepo
source ~/.myrepo/bin/activate
```

* Create initial `hello.py` and `test_hello.py`

hello.py
```python
def toyou(x):
    return "hi %s" % x


def add(x):
    return x + 1


def subtract(x):
    return x - 1
```

test_hello.py
```python
from hello import toyou, add, subtract


def setup_function(function):
    print("Running Setup: %s" % {function.__name__})
    function.x = 10


def teardown_function(function):
    print("Running Teardown: %s" % {function.__name__})
    del function.x


### Run to see failed test
#def test_hello_add():
#    assert add(test_hello_add.x) == 12

def test_hello_subtract():
    assert subtract(test_hello_subtract.x) == 9

```


* Run `make all` which will install, lint and test code.
![image](https://user-images.githubusercontent.com/74898057/102755961-2e3efc80-4395-11eb-9eed-93bae484c940.png)

### CI: Configure GitHub Actions
Configuring a SaaS build server like GitHub Actions is an essential step for any software project that wants to apply DevOps best practices. This completes the final section of Continous Integration and enables us to then move on to the later step of Continuous Delivery once this is complete.
This diagram shows how code can be tested automatically by enabling GitHub Actions. The push change to GitHub triggers the GitHub Actions container, which in turn runs a series of commands.
![image](https://user-images.githubusercontent.com/74898057/102742766-bd3f1b00-437b-11eb-9efd-438f3acb1e9c.png)

1. Enable Github Actions
Go to your Github Account and enable Github Actions.

2. Replace yml code
Replace the pythonapp.yml code with the following scaffolding code
Setup Github Actions in `pythonapp.yml`

```yaml
name: Python application test with Github Actions

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.5
      uses: actions/setup-python@v1
      with:
        python-version: 3.5
    - name: Install dependencies
      run: |
        make install
    - name: Lint with pylint
      run: |
        make lint
    - name: Test with pytest
      run: |
        make test
```
![image](https://user-images.githubusercontent.com/74898057/102756560-1a47ca80-4396-11eb-8398-74ce9efef606.png)
![Python application test with Github Actions](https://github.com/imabgd/abgd-Udacity-project2/workflows/Python%20application%20test%20with%20Github%20Actions/badge.svg)


### Continuous Delivery on Azure
This will involve setting up Azure Pipelines to deploy the Flask starter code to Azure App Services.
![image](https://user-images.githubusercontent.com/74898057/102742801-dc3dad00-437b-11eb-9557-5ecca4317113.png)

Clone the Repo and Create a Flask ML Service

In your Azure Cloud shell environment, clone the GitHub repository you created.
1. Set up virtualenv:

        python3 -m venv ~/.flask-ml-azure
        source ~/.flask-ml-azure/bin/activate

2. Run

        make install

3. Create an app service and initially deploy your app in Cloud Shell:

        az webapp up -n <your-appservice>
![image](https://user-images.githubusercontent.com/74898057/102757150-e4571600-4396-11eb-9164-21169e444c5e.png)

4. Verify the deployed application works by browsing to the deployed url
Go to https://<your-appservice>.azurewebsites.net/ and you should see the same output as in the screenshot below:
![image](https://user-images.githubusercontent.com/74898057/102757525-75c68800-4397-11eb-831c-3f66616ed210.png)
![image](https://user-images.githubusercontent.com/74898057/102757671-adcdcb00-4397-11eb-9ea4-193b948774a2.png)


5. Perform Prediction
Change the line in make_predict_azure_app.sh to match the deployed prediction:

      -X POST https://<yourappname>.azurewebsites.net:$PORT/predict
A successful prediction will look like this:
![image](https://user-images.githubusercontent.com/74898057/102758074-2f255d80-4398-11eb-89d7-4201904a57ee.png)

6. Create an Azure DevOps project

Next, we'll need to create an Azure DevOps project and connect to Azure. The screenshots below show the steps, but if you need to, you can also refer to the official documentation for more detail.
	
A. Create new project and name it
	
B. Ensure you set up a new service connection via Azure Resource Manager and Pipeline
          ![image](https://user-images.githubusercontent.com/74898057/102759064-9e4f8180-4399-11eb-93a1-9f9445d3ac17.png)
	
C. Select Pipeline and create a new one.
          ![image](https://user-images.githubusercontent.com/74898057/102759257-df479600-4399-11eb-8cbe-f71f00febbe1.png)
	
D. Create the GitHub Integration
	
E. Configure Python to Linux Web App on Azure
	
F: Set Up the Continuous Delivery Workflow
              This process will create a YAML file azure-pipelines-yml
              ![image](https://user-images.githubusercontent.com/74898057/102759389-0c944400-439a-11eb-8fe0-2ca24fcbe0e5.png)
              ![image](https://user-images.githubusercontent.com/74898057/102759564-4c5b2b80-439a-11eb-85d3-43e10443803a.png)
              ![image](https://user-images.githubusercontent.com/74898057/102759661-6ac12700-439a-11eb-9134-414195c4f457.png)

G: Add a Quality Control Gate
              Now we'll see how we can add a lint step to gate your code against syntax failure. Follow along with the video below to extend the current configuration 
              to add a quality control gate with pylint.
              
                - script: |
                python -m venv antenv
                source antenv/bin/activate
                make install
                make lint
                workingDirectory: $(projectRoot)
                displayName: 'Run lint tests'


* Output of streamed log files from deployed application

      az webapp log tail -n <appservice name>

		(.flask-ml-azure) abhijeet@Azure:~/abgd-Udacity-project2$ az webapp log tail -n abgd-flask
		2020-12-21T01:10:01  Welcome, you are now connected to log-streaming service.

		Starting Log Tail -n 10 of existing logs ----

		/home/LogFiles/__lastCheckTime.txt  (https://abgd-flask.scm.azurewebsites.net/api/vfs/LogFiles/__lastCheckTime.txt)
		12/21/2020 00:46:28


		/home/LogFiles/kudu/trace/aa12661d08fa-0b1bfd9d-7d47-45e6-8a45-d0fd10c3852c.txt  (https://abgd-flask.scm.azurewebsites.net/api/vfs/LogFiles/kudu/trace/aa12661d08fa-0b1bfd9d-7d47-45e6-8a45-d0fd10c3852c.txt)
		2020-12-21T00:41:47  Startup Request, url: /api/zipdeploy?isAsync=true, method: POST, type: request, pid: 178,1,5, SCM_DO_BUILD_DURING_DEPLOYMENT: True, ScmType: None


		/home/LogFiles/2020_12_21_lw0sdlwk00002Q_default_docker.log  (https://abgd-flask.scm.azurewebsites.net/api/vfs/LogFiles/2020_12_21_lw0sdlwk00002Q_default_docker.log)
		2020-12-21T01:02:16.798288292Z [2020-12-21 01:02:16,797] INFO in app: JSON payload: %s json_payload
		2020-12-21T01:02:16.807036650Z [2020-12-21 01:02:16,806] INFO in app: inference payload DataFrame: %s inference_payload
		2020-12-21T01:02:16.812537287Z [2020-12-21 01:02:16,812] INFO in app: Scaling Payload: %s payload
		2020-12-21T01:02:16.872270487Z 172.16.0.1 - - [21/Dec/2020:01:02:16 +0000] "POST /predict HTTP/1.1" 200 35 "-" "curl/7.64.0"
		2020-12-21T01:02:16.875871911Z 172.16.0.1 - - [21/Dec/2020:01:02:16 +0000] "GET / HTTP/1.1" 200 32 "-" "ReadyForRequest/1.0 (AppInit)"

		2020-12-21T01:04:49.739498070Z 172.16.0.1 - - [21/Dec/2020:01:04:49 +0000] "GET / HTTP/1.1" 200 32 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:84.0) Gecko/20100101 Firefox/84.0"

		2020-12-21T01:04:50.074546950Z 172.16.0.1 - - [21/Dec/2020:01:04:50 +0000] "GET /favicon.ico HTTP/1.1" 404 232 "https://abgd-flask.azurewebsites.net/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:84.0) Gecko/20100101 Firefox/84.0"



		/home/LogFiles/2020_12_21_lw0sdlwk00002Q_docker.log  (https://abgd-flask.scm.azurewebsites.net/api/vfs/LogFiles/2020_12_21_lw0sdlwk00002Q_docker.log)
		2020-12-21T00:46:28.401Z INFO  - Starting container for site
		2020-12-21T00:46:28.401Z INFO  - docker run -d -p 3075:8000 --name abgd-flask_0_0691d454 -e WEBSITE_SITE_NAME=abgd-flask -e WEBSITE_AUTH_ENABLED=False -e WEBSITE_ROLE_INSTANCE_ID=0 -e WEBSITE_HOSTNAME=abgd-flask.azurewebsites.net -e WEBSITE_INSTANCE_ID=259c5b1f5e30f64a53265be8c593f432beb4dcc61d04b03bf7a50db047ff9451 -e HTTP_LOGGING_ENABLED=1 appsvc/python:3.7_20201112.3

		2020-12-21T00:46:34.189Z INFO  - Initiating warmup request to container abgd-flask_0_0691d454 for site abgd-flask
		2020-12-21T00:46:51.375Z INFO  - Waiting for response to warmup request for container abgd-flask_0_0691d454. Elapsed time = 17.1859248 sec
		2020-12-21T00:47:14.938Z INFO  - Container abgd-flask_0_0691d454 for site abgd-flask initialized successfully and is ready to serve requests.


		/home/LogFiles/webssh/pm2.log  (https://abgd-flask.scm.azurewebsites.net/api/vfs/LogFiles/webssh/pm2.log)



## Enhancements

More Test Cases can be added with Pytest

## Demo 
https://youtu.be/6YEH5kTqXiE
