# Problems found while implementing tests pipeline

- [Problems found while implementing tests pipeline](#problems-found-while-implementing-tests-pipeline)
  - [Tests environment on docker](#tests-environment-on-docker)
  - [Problems found on the approach](#problems-found-on-the-approach)
    - [Services can't access each other](#services-cant-access-each-other)
    - [Tests not running](#tests-not-running)
  - [Run tests on pipeline](#run-tests-on-pipeline)
    - [Env files missing](#env-files-missing)
    - [Tests not run](#tests-not-run)
      - [Not access error](#not-access-error)
      - [Abort on Exit](#abort-on-exit)


## Tests environment on docker

To implement test environment i create a dockerfile that implement tests part. 
- Install all needed dependencies
- Run tests  
  
Also create a docker-compose file to include all the services needed for that:
- Database
- Backend
- Migrations
- Automation Tests  

## Problems found on the approach 

### Services can't access each other
Automation Tests service needs to access endpoints available on backend service. 
To do that is necessary to assure that is necessary to declare a network on docker-compose file and in all services indicate thats it uses that network

Example:

```
services:
  app:
    image: node:14
    container_name: my_node_app
    working_dir: /usr/src/app
    volumes:
      - ./:/usr/src/app
    ports:
      - "3000:3000"
    command: npm start
    networks:
      - my_custom_network

  db:
    image: mysql:5.7
    container_name: my_mysql_db
    environment:
      MYSQL_ROOT_PASSWORD: secret_password
      MYSQL_DATABASE: my_app_db
      MYSQL_USER: user
      MYSQL_PASSWORD: user_password
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - "3306:3306"
    networks:
      - my_custom_network

networks:
  my_custom_network:
    driver: bridge

volumes:
  db_data:
```
In addition, it is necessary that in the url instead of use localhost (http://localhost/api/v1), use the name of the service (http://service/api/v1) 

### Tests not running

The command that run tests, it was placed on Dockerfile.  
Because of that, command doesn't runs when mount docker-compose.
To solve this problem, it was moved for a command in the docker-compose file

## Run tests on pipeline

To do this, it as implemented a new pipeline only for tests.  

### Env files missing 
As env files are not being upload for remote repository, when mount docker environment and copy everything env file are not take in consideration.  
For a first approach, and to can run pipeline, .gitignore file of Automation Tests (playwright folder) was changed to upload this files

```
!.env
!.env.tests
```

### Tests not run 

#### Not access error

The cause of this is because when there are a depends_on: backend on automation_tests service, but only assure that there are a service with the name backend and not that api is already available.  
To solve this, it was implemented HealthCheck on backend service
```
  healthcheck:
    test: ["CMD", "curl", "-f", "http://localhost:80/api/v1/agent"]
    interval: 10s
    timeout: 5s
    retries: 5
```
And, in automation_tests all the dependencies have condition
```
    depends_on:
      backend:
        condition: service_healthy
      test_db:
        condition: service_started
      migration:
        condition: service_started
```

#### Abort on Exit

The cause of this is because when migration service ends, send and exit 0 and is finished.  
When occurs an exit on pipeline (ok or not), by default pipeline aborts.

- First approach was to put on task that runs docker-compose abortOnContainerExit=false
  The result was: 
  Pipeline doesn't abort never. This means that when pipeline is always running 

- Next, to solve this problem, the first approach was removed.
  Instead of that, the dependency of service migration on automation_tests service was changed
  ```
    depends_on:
    backend:
      condition: service_healthy
    test_db:
      condition: service_started
    migration:
      condition: service_completed_successfully
  ```
  **Explanation of depends_on conditions**
  - service_started: Assure that the service it was already started
  - service_healthy: Assure that the specified service has healthcheck specified and returning ok 
  - service_completed_successfully: Assure that service runs completely and end ok (exit 0)
  