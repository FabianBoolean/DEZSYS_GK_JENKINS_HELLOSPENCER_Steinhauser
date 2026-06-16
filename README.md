# DEZSYS GK8.3 – Jenkins Hello Spencer Pipeline

## Overview

This project demonstrates a Jenkins CI/CD pipeline for a simple Python Flask application.

The goal was to automatically:

- load source code from GitHub
- install dependencies
- run tests
- start the application
- test the REST API endpoint
- finish the pipeline successfully

Repository:

```text
https://github.com/FabianBoolean/DEZSYS_GK_JENKINS_HELLOSPENCER_Steinhauser
```

---

# Technologies Used

- Jenkins
- Docker
- GitHub
- Python 3.11
- Flask
- Pytest
- curl

---

# Project Structure

The project contains:

```text
src/
tests/
Dockerfile
Jenkinsfile
requirements.txt
count.txt
```

### Screenshot – Project Structure

![Project Structure](screenshots/Bildschirmfoto%202026-05-29%20um%2017.28.25.png)

---

# Jenkins Pipeline Setup

A new Jenkins Pipeline project was created with the name:

```text
GK83-HelloSpencer-Pipeline
```

At first, the default configuration used an empty `Pipeline script`.

### Screenshot – Initial Pipeline Configuration

![Initial Pipeline Configuration](screenshots/Bildschirmfoto%202026-05-29%20um%2018.22.46.png)

---

# Pipeline from SCM

The pipeline configuration was changed to:

```text
Definition: Pipeline script from SCM
SCM: Git
Repository URL: https://github.com/FabianBoolean/DEZSYS_GK_JENKINS_HELLOSPENCER_Steinhauser.git
Branch Specifier: */main
Script Path: Jenkinsfile
```

This allows Jenkins to load the `Jenkinsfile` directly from the GitHub repository.

### Screenshot – Pipeline from SCM

![Pipeline from SCM](screenshots/Bildschirmfoto%202026-05-29%20um%2018.24.07.png)

---

# First Pipeline Execution

After saving the configuration, the first Jenkins build was started.

The first build did not finish successfully because the pipeline still contained a problematic long-running step.

### Screenshot – First Build

![First Build](screenshots/Bildschirmfoto%202026-05-29%20um%2018.25.45.png)

---

# Jenkinsfile

The `Jenkinsfile` defines the pipeline.

The pipeline uses a Python Docker image:

```groovy
agent {
    docker { 
        image 'python:3.11' 
        args '-p 5556:5556'
    }
}
```

The pipeline contains the following stages:

## Pre-Build Cleanup

Stops old Python processes before the pipeline starts.

## Checkout

Loads the source code from GitHub.

## Build

Installs the required Python packages:

```bash
pip install flask
pip install requests
pip install pytest
```

## Test

Runs the unit tests:

```bash
python -m pytest tests/test_hello.py -v
```

## Run

Starts the Flask application:

```bash
nohup python src/hello.py > app.log 2>&1 &
```

Then the API endpoint is tested:

```bash
curl http://localhost:5556/api/hello
```

Expected result:

```json
{
  "counter": 31,
  "message": "Hello Spencer",
  "status": "success"
}
```

## Test API

Runs the API tests:

```bash
python tests/test_api.py
```

## Post Actions

Stops the Flask application after the tests.

---

# Problem and Solution

The first pipeline version did not terminate correctly because it contained a step like:

```groovy
sleep infinity
```

This caused Jenkins to keep the build running.

The solution was to remove this step and let Jenkins clean up the running Flask process in the post action.

After this change, the pipeline completed successfully.

---

# Successful Pipeline Build

After fixing the Jenkinsfile, the pipeline executed successfully.

The console output shows:

```text
Ran 2 tests

OK
Finished: SUCCESS
```

### Screenshot – Successful Build

![Successful Build](screenshots/Bildschirmfoto%202026-05-29%20um%2018.31.27.png)

---

# Result

The final pipeline successfully performs the following steps:

1. Checkout source code from GitHub
2. Build the Python environment
3. Install dependencies
4. Run unit tests
5. Start the Flask application
6. Test the REST API
7. Clean up running processes
8. Finish with `SUCCESS`

---

# Conclusion

The CI/CD pipeline was successfully implemented with Jenkins. Jenkins automatically loads the project from GitHub, installs the required dependencies, runs tests, starts the Flask application and verifies the REST endpoint.

The final build ended with:

```text
Finished: SUCCESS
```