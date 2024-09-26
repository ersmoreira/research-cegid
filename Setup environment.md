# Setup business-os environment

## Table of Contents
- [Setup business-os environment](#setup-business-os-environment)
  - [Table of Contents](#table-of-contents)
  - [Dependencies](#dependencies)
  - [Verify installed packages](#verify-installed-packages)
  - [How uninstall packages](#how-uninstall-packages)
  - [Install necessary packages](#install-necessary-packages)
  - [Setup necessary virtual environments](#setup-necessary-virtual-environments)


## Dependencies

To run the project is necessary to have python installed with the following packages 

>- pdm (version 2.18.1)
>- pipdeptree  

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