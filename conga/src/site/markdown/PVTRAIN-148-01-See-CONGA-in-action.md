## PVTRAIN-148-01 See CONGA in action

### Overview

*   Execute CONGA via Maven
*   Have a first look at the role definitions, templates and environments
*   Inspect the generated configuration

### Git project

*   [https://github.com/wcm-io-training/training-conga-exercise-basic](https://github.com/wcm-io-training/training-conga-exercise-basic)

### Exercise

#### Build sample application and CONGA configuration

1.  Go to the root of the GIT repository and execute:

    ```
    mvn clean install
    ```

    This builds the sample application and generates all configuration.

2.  Have a look at the roles and templates contained in the example
    *   `definition/src/main/roles`
    *   `definition/src/main/templates`
3.  Have a look at the environment
    *   `environment/src/main/environments`
4.  Have a look at the generated configuration  

    *   `environment/target/configuration/dev/localhost`

#### Deploy generated configuration to Tomcat 8

1.  [Download Tomcat 8](https://tomcat.apache.org/download-80.cgi) ("Core ZIP" version) and extract to local hard drive
2.  Check `environment/src/main/environments/dev.yml` and update the `jvm.path` and `tomcat.path` variables to your local Java and Tomcat paths.
3.  Regenerate the CONGA configuration
4.  Copy the generated configuration from `environment/target/configuration/dev/localhost` to your local Tomcat path
    *   This also includes the sample application (sample-app.war)
5.  Start Tomcat 8 via `startup.bat` (Windows) / `startup.sh` (Unix-based systems)
6.  Open URL [http://localhost:8080/sample-app/](http://localhost:8080/sample-app/)
