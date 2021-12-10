## DATM-59-03 Define CONGA Roles and Templates

### Overview

*   Update roles and introduce variants
*   Change templates
*   Create new parameters
*   Define tenants

### Git project

*   [https://github.com/wcm-io-training/training-conga-exercise-basic](https://github.com/wcm-io-training/training-conga-exercise-basic)

### Exercise

#### A) Include link list URLs in configuration

1.  The role sample-app declares a parameter **`sampleApp.linkList`** (with default value)
2.  Update the deployment descriptor template (`context.xml.hbs`) to include the value of this parameter
3.  Be aware that the parameter is an array of multiple string values (URL). Put the URLs separated by "," in the "link-list" parameter in the XML file
4.  Define multiple link list URLs in the dev environment

#### B) Make instance ID configurable

1.  The role tomcat declares an optional **`instanceId`** parameter (commented out)
2.  Update the templates setenv batch files to set a Java system environment parameter `sample.instanceId` with the configured value for this parameter
    *   Generate script lines like this for Windows (`setenv.bat.hbs`)

        ```
        REM Set example instance ID
        SET CATALINA_OPTS=%CATALINA_OPTS% -Dsample.instanceId=xyz
        ```

    *   Generate script lines like this for Unix (`setenv.sh.hbs`)

        ```
        # Set example instance ID
        CATALINA_OPTS="$CATALINA_OPTS -Dsample.instanceId=xyz"
        ```

3.  Make sure that these new lines are only included in the batch file if a value for `instanceId` is configured (condition)
4.  Set the instanceId in the dev environment to **test**
5.  Set the instanceId in the prod environment to the **node name** of each node

#### C) Introduce role variants and define tenants

1.  Define two variants "single" and "multitenant" in the sample-app role
    *   single = Singe application deployment without tenants
    *   multitenant = Deploy multiple webapps to a single tomcat instance - one for each tenant with configuration for each tenant
2.  Assign the existing files sample-app.xml and sample.app.war to the variant "single"
3.  Create two new file definitions for the variant "multitenant"
    *   Deployment descriptor `xyz.xml` - one file per tenant with "multiply" plugin - xyz is the tenant name
    *   WAR file `xyz.war` - one file per tenant with "multiply" plugin - xyz is the tenant name
4.  Change the dev environment to use the "single" variant for the sample-app roles
5.  Change the prod environment to use the "multitenant" variante for the sample-app roles
6.  Define two tenants "tenant1" and "tenant2"
7.  Change the welcome message to "Hello World, welcome xyz!" - xyz is the tenant name
8.  Define one link URL that is used for all tenants. Define additional link URLs for each tenant and merge the list with the global URL. For each tenant both global and tenant-specific link URLs should be displayed.

### Validation

*   A) Make sure the list of link URLs is displayed properly in the webapp and the links are clickable
*   B) Make sure "test" is displayed as instance ID on the dev environment, and "prod1" / "prod2" for the nodes on the prod environment
*   C) Deploy the multitenant configuration of the prod environment to your Tomcat
    *   Open URLs [http://localhost:8080/tenant1](http://localhost:8080/tenant1) and [http://localhost:8080/tenant2](http://localhost:8080/tenant2)
    *   Both should show different values for the welcome message and link list
