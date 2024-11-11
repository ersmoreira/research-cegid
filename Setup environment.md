# Setup business-os environment

## Table of Contents
- [Setup business-os environment](#setup-business-os-environment)
  - [Table of Contents](#table-of-contents)
  - [Pre-requisites](#pre-requisites)
  - [Install pre-requisites](#install-pre-requisites)
  - [Verify installed packages](#verify-installed-packages)
  - [How uninstall packages](#how-uninstall-packages)
  - [Install necessary packages](#install-necessary-packages)
  - [Setup necessary virtual environments](#setup-necessary-virtual-environments)


## Pre-requisites

- To have all the project working is necessary to have the following instaled:
>- Python
>- Node
>- Docker Desktop

- Python need to have the following packages 

>- pdm (version 2.18.1)
>- pipdeptree  

## Install pre-requisites

- **Python**
  Could download and install from [official site](https://www.python.org/downloads/) or [microsoft windows store](https://apps.microsoft.com/detail/9pjpw5ldxlz5?hl=pt-pt&gl=PT)  
  If need a little bit of details, could see [here](https://www.datacamp.com/pt/blog/how-to-install-python)
- **Node**
  Could download and install from [official site](https://nodejs.org)  
  If need a little bit of details, could see [here](https://www.alura.com.br/artigos/como-instalar-node-js-windows-linux-macos)
- **Docker**
  Could download and install from [official site](https://www.docker.com/)  
  If need a little bit of details, could see [here](https://docs.docker.com/desktop/install/windows-install/)

## Verify installed packages

To check which packages are installed execute the following command

``` bash
pip list
```
## How uninstall packages
If there are any packages installed other than those mentioned, they should be uninstalled  
- To uninstall one package:
```bash
pip uninstall <package_name>
```
- To uninstall all packages 
>```bash
>pip freeze > to_uninstall.txt
>```
>```bash
>pip uninstall -r to_uninstall.txt -y
>```

## Install necessary packages
To install execute the following commands
>``` bash
>pip install pdm==2.18.1
>```
>
>``` bash
>pip install pipdeptree
>```
>
>```bash
>pdm config check_update false
>```


## Setup necessary virtual environments

Virtual environments should be created by the following order 

>1. Cross_cutting
>1. Domain
>1. Persistence
>1. Request_engine
>1. Backend

To make setup of the necessary environment first is necessary to open the workspace on VS Code, and then do the following steps for each one:

>- CTRL + Shift + P
>- Create environment (follow instruction until ehe environment is created)
>- CTRL + Shift + P
>- Select interpreter (select python.exe file inside .venv/scripts/ )
>- If already exists, delete the following
>>- lib folder inside .venv folder
>>- pdm.lock file
>- CTRL + Shift + Ç
>- Select where open the terminal 
>- Execute the following commands: 
>>- pdm install -G:all
>>- pdm build
>
>NOTE: the last command is not necessary for backend

[Go Back](./Index.md)
