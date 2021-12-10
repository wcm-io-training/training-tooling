## DATM-59-02 Configure CONGA environments

### Overview

*   Create a new environment
*   Define multiple nodes
*   Change configuration parameters

### Git project

*   [https://github.com/wcm-io-training/training-conga-exercise-basic](https://github.com/wcm-io-training/training-conga-exercise-basic)

### Exercise

1.  Create a new environment **prod.yaml**
    *   E.g. by deriving from dev.yaml
2.  Define two nodes **prod1** and **prod2** in this environment
    *   For both the roles tomcat and sample-app should be applied
3.  Change the max. heap space
    *   For node prod1: **1024m**
    *   For node prod2: **2048m**
4.  Change the **welcome message** for the prod environment to "Hello World, welcome on the prod environment"

### Validation

*   Regenerate the configuration
*   Check that all parameters were written to the files as expected (both setenv shell files and sample-app.xml deployment descriptor)
*   Deploy at least one of the two prod node config to your local Tomcat
*   Restart Tomcat
*   Open [http://localhost:8080/sample-app/](http://localhost:8080/sample-app/)
*   Check that the welcome message is updated, and total memory is increased according to the setting for the node
