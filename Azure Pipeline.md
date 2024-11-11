# Azure Pipeline


## Table of Contents

- [Azure Pipeline](#azure-pipeline)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [What is Azure Pipelines?](#what-is-azure-pipelines)
  - [Setting Up a Pipeline](#setting-up-a-pipeline)
    - [Example of a Pipeline with Stages](#example-of-a-pipeline-with-stages)
  - [Useful Commands for Azure Pipelines with Python and Playwright](#useful-commands-for-azure-pipelines-with-python-and-playwright)
    - [Setting Up the Virtual Environment](#setting-up-the-virtual-environment)
    - [Running Unit Tests](#running-unit-tests)
    - [Running UI Tests](#running-ui-tests)
  - [Explanation of Key Azure Pipeline Commands](#explanation-of-key-azure-pipeline-commands)

## Introduction

I'm focus on creating build pipeline using Azure Pipelines for Python project, including automated tests with Playwright.  

## What is Azure Pipelines?

Azure Pipelines is a powerful service in Azure DevOps that allows to build, test, and deploy code automatically in various environments, supporting multiple languages and frameworks.  
It simplifies Continuous Integration (CI) and Continuous Delivery (CD) for development teams.

## Setting Up a Pipeline

In Azure Pipelines, stages allow to split process into phases like **build**, **unit testing**, **UI testing**, **E2E testing**, etc. .  
Each stage can be run in sequence or in parallel.

### Example of a Pipeline with Stages

```yaml
trigger:
  branches:
    include:
      - main  # Triggers the pipeline on commits to the 'main' branch

pool:
  vmImage: 'ubuntu-latest'  # Uses an Ubuntu VM to run the pipeline

stages:
  - stage: Build
    jobs:
      - job: BuildApp
        steps:
          - task: UsePythonVersion@0
            inputs:
              versionSpec: '3.x'
              addToPath: true

          - script: |
              python -m venv env       # Creates a virtual environment
              source env/bin/activate  # Activates the virtual environment
              pip install -r requirements.txt  # Installs dependencies
            displayName: 'Install Dependencies'

  - stage: Test
    dependsOn: Build  # This stage depends on the success of the Build stage
    jobs:
      - job: UnitTests
        steps:
          - script: |
              source env/bin/activate  # Activates the virtual environment
              pytest                   # Runs unit tests
            displayName: 'Run Unit Tests'

  - stage: UITests
    dependsOn: Test  # This stage depends on the success of the Test stage
    jobs:
      - job: UIAutomation
        steps:
          - script: |
              source env/bin/activate
              pytest --headless --browser=chromium  # Runs UI tests tests in headless mode
            displayName: 'Run UI Tests'
```

## Useful Commands for Azure Pipelines with Python and Playwright

### Setting Up the Virtual Environment

A virtual environment is crucial for isolating your Python dependencies:

```yaml
- script: |
    python -m venv env  # Creates a virtual environment named 'env'
    source env/bin/activate  # Activates the virtual environment
    pip install -r requirements.txt  # Installs project dependencies
  displayName: 'Set up Virtual Environment and Install Dependencies'
```

### Running Unit Tests

To run unit tests with pytest, you can use the following command in the pipeline:

```yaml
- script: |
    source env/bin/activate  # Activates the virtual environment
    pytest  # Runs unit tests using pytest
  displayName: 'Run Unit Tests'
```

### Running UI Tests

Playwright is a popular tool for browser automation and UI testing.  
Here's how can install and run Playwright in Azure Pipelines:

- Install Playwright and its dependencies:

  ```yaml
  - script: |
      source env/bin/activate
      pip install playwright  # Installs Playwright
      playwright install  # Downloads browsers (chromium, firefox, webkit)
    displayName: 'Install Playwright and Browsers'
  ```

- Run Playwright tests in headless mode:

  ```yaml
  - script: |
      source env/bin/activate
      npx playwright test --headless --browser=chromium  # Run Playwright tests in headless mode
    displayName: 'Run Playwright Tests'
  ```

## Explanation of Key Azure Pipeline Commands

- **trigger**
The `trigger` section defines when the pipeline should run. In this example, it triggers for any commits to the `main` branch.

```yaml
trigger:
  branches:
    include:
      - main
```

- pool
The pool defines the environment in which the pipeline runs.  
Here, we are using the `ubuntu-latest` virtual machine image.

```yaml
pool:
  vmImage: 'ubuntu-latest'
```

- stages
Stages allow to organize pipeline into logical phases such as build, test, and deployment.

```yaml
stages:
  - stage: Build
  - stage: Test
  - stage: PlaywrightTests
```

- dependsOn
This specifies that a stage should only run after the completion of another stage.  
For example, `Test` runs only after `Build` is successful.

```yaml
dependsOn: Build
```

- script
The `script` keyword allows to execute shell commands directly in the pipeline.  
For example, installing dependencies or running tests:

```yaml
steps:
  - script: |
      python -m venv env
      source env/bin/activate
      pip install -r requirements.txt
  displayName: 'Install Dependencies'
```

- task
`task` refers to predefined steps available in Azure Pipelines.  
For instance, the `UsePythonVersion` task sets the Python version for the pipeline.

```yaml
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.x'
    addToPath: true
```

[Go Back](./Index.md)
