## DATM-59-04 Configure AEM with CONGA

### Overview

*   Configure AEM OSGi configuration
*   Deploy additional AEM packages
*   Generate configuration content packages

### Git project

*   [https://github.com/wcm-io-training/training-conga-exercise-aem](https://github.com/wcm-io-training/training-conga-exercise-aem)

### Introduction

The GIT project contains CONGA configuration definitions and two CONGA example environments:

*   `definition/src/main/dev-environments/development.yaml`
*   `environment/src/main/environments/prod.yaml`

The first one is used for testing in the local AEM instance. You will only touch this for this exercise.

The second one is an example for a PROD environment - it is not used in the exercise, but you can have a look what different configuration is generated for the different environment files.

Please not that typically the configuration definition is part of the application Maven module structure and released together with it. For the sake of simplicity no AEM application is included in this training repository, instead the application packages from the [wcm.io Sample Application](https://wcm.io/samples/) are used, which are available on Maven Central. The wcm.io Sample Application itself also contains a CONGA configuration definition, but this is not used in this exercise.

### Exercise

#### A) Deploy the sample application and predefined configuration

Exercise:

1.  Execute `mvn clean install` on the root of the training repository
2.  Deploy the application and configuration to your local AEM instance via the script `definition/packages-upload.sh`
    *   This Script uses the [CONGA AEM Maven Plugin](https://devops.wcm.io/conga/plugins/aem/) to deploy the generated AEM packages to your local AEM instance running at port 4502

Validation:

*   Open the sample application at [http://localhost:4502/editor.html/content/wcm-io-samples/en.html](http://localhost:4502/editor.html/content/wcm-io-samples/en.html)
*   Have a look at the generated configuration at
    *   `definition/target/configuration/development`
    *   `environment/target/configuration/prod`

#### B) Enable CRXDE Lite / DavEx Servlet

Exercise:

1.  By default, CRXDE Lite is disabled on an AEM instance started in "nosamplecontent" = production mode
2.  This can be enabled as described described here: [Enabling CRXDE Lite in AEM](https://docs.adobe.com/docs/en/aem/6-2/administer/security/security-checklist/enabling-crxde-lite.html)
3.  This OSGi configuration is also included in the CONGA AEM Definitions, but it is switched off by default
4.  Task: Find out the correct CONGA configuration parameter for this in [https://github.com/wcm-io-devops/conga-aem-definitions/tree/develop/conga-aem-definitions](https://github.com/wcm-io-devops/conga-aem-definitions/tree/develop/conga-aem-definitions)
5.  Enable CRXDE Lite for all nodes of the local environment `development.yaml` (the config definition which is used for the local deployment)
6.  Redeploy the application and configuration to your local AEM instance

Validation:

*   Open [http://localhost:4502/crx/de](http://localhost:4502/crx/de) and ensure you can see the whole content tree
*   Look for the OSGi configuration file `/apps/aem-cms-system-config/config.author/org.apache.sling.jcr.davex.config` in the repository and ensure it exists

#### C) Deploy Groovy Console on Author

Exercise:

1.  Citytech (recently aquired by Olsen Digital, now ICF Next) has published a nice tool for AEM: The "AEM Groovy Console": [https://github.com/icfnext/aem-groovy-console](https://github.com/icfnext/aem-groovy-console)
2.  The AEM ZIP package for deploying it is also available on Maven Central: [https://repo1.maven.org/maven2/com/icfolson/aem/groovy/console/aem-groovy-console/13.0.0/](https://repo1.maven.org/maven2/com/icfolson/aem/groovy/console/aem-groovy-console/13.0.0/)
3.  Please extend the role `wcm-io-samples-cms` to include a deployment of this Groovy Console Package
4.  Make the deployment of this file conditional using a configuration parameter, similar as it is already done for the sample content in the same role
5.  Deploy the Groovy Console only for the author node of the local environment `development.yaml`
6.  Redeploy the application and configuration to your local AEM instance

Validation:

*   Open [http://localhost:4502/apps/groovyconsole.html](http://localhost:4502/apps/groovyconsole.html) - the Groovy Console should be displayed

#### D) Restrict Access to Groovy Console to Administrators

Preparation - create two new users:

* Create a new non-admin user "author" with password "author" at http://localhost:4502/security/users.html
  * Assign this user to group "Authors" (content-authors)
* Create a new admin user "admin2" with password "admin2" at http://localhost:4502/security/users.html
  * Assign this user to group "Administrators" (administrators)

Exercise:

1.  If you look in http://localhost:4502/system/console/configMgr you will find a configuration "Groovy Console Configuration Service" which has a configuration parameter labeled with "Allowed Groups" - we want to set this to `administrators` to restrict the access to the Groovy console
2.  Enhance the role `wcm-io-samples-cms` to generate an additional OSGi configuration for this service that reconfigures this property to the desired value
3.  Redeploy the application and configuration to your local AEM instance

Validation:

*  Log into AEM using the author user
*  Open the Groovy Console http://localhost:4502/apps/groovyconsole.html - you should not longer be allowed to open or execute scripts
*  Log into AEM using the admin2 user
*  Open the Groovy Console http://localhost:4502/apps/groovyconsole.html - you can open scripts and execute them
  * e.g. the "samples/ListTemplates.groovy" script - you have to replace `/content/we-retail` with `/content/wcm-io-samples/en`

Please note: once an allowed group is configured the "admin" user itself cannot longer execute scripts - this is a bug in the Groovy Console ([#86](https://github.com/icfnext/aem-groovy-console/issues/86)). That's why we use "admin2" for validation.

#### E) Add additional logfile for JCR Query Debugging

Exercise:

1.  We want to debug JCR queries by setting DEBUG log level for these two log categories:
    *   `org.apache.jackrabbit.oak.query`
    *   `org.apache.jackrabbit.oak.plugins.index`
2.  In the role `wcm-io-samples-cms` you will find examples for defining additional loggers for the factory PID `org.apache.sling.commons.log.LogManager.factory`
3.  Create a new entry with the mentioned log categories, DEBUG log level, and a log file named `logs/query.log`
4.  Redeploy the application and configuration to your local AEM instance

Validation:

*   Ensure a `query.log` exists in the local AEM directory `crx-quickstart/logs`
*   Execute any query in AEM or CRXDE Lite an ensure new log entries are written in this log (please note that several AEM background services also execute queries form time to time, so you will see a log of other messages as well)

#### F) Make the languages displayed in AEM translator configurable

Exercise:

1.  AEM comes with a translator tool for managing i18n dictionaries: [http://localhost:4502/libs/cq/i18n/translator.html](http://localhost:4502/libs/cq/i18n/translator.html)
2.  By default it displays all languages. But as described [here](https://docs.adobe.com/docs/en/aem/6-2/develop/components/i18n/translator.html#Changing%20Languages%20Listed%20in%20the%20Dictionary%20Table) it is also possible to configure the list of languages displayed in the translator
  * This is possible by creating a node at `/etc/languages`, node type `nt:unstructured`, and set a property `languages` to list of language codes e.g. de,fr,es
3.  Generate an AEM package via CONGA automatically that creates this node and multivalued string property
4.  Add a new configuration parameter to the role `wcm-io-samples-cms` which contains a list of language codes. Set it to the default value "de", "en" in the role.
5.  Generate a new AEM package from the role `wcm-io-samples-cms` based on a Handlebars JSON template. This JSON file defines the content of the langages node. Use Handlebars logic to fill in the language codes defined in the configuration parameter. Use the `aem-contentpackage` post processor to transform the generated JSON to an AEM package.
6.  You can have a look at the file `wcm-io-samples-aem-cms-author-systemusers` generated by the same role as example - it uses the same mechanisms but is more complex.
7.  Configure the list of languages for all nodes to "de", "en", "fr", "it" in the environment `development.yaml`
8.  Redeploy the application and configuration to your local AEM instance

Validation:

*   Open [http://localhost:4502/libs/cq/i18n/translator.html](http://localhost:4502/libs/cq/i18n/translator.html) - you should see for columns with translations labelled DE, EN, FR, IT


#### G) Add ACLs for translator language configuration

Exercise:

1.  Currently, the updated list of languages in the Translator http://localhost:4502/libs/cq/i18n/translator.html is only visible for admin users - to fix this we need to add proper ACLs to the node to make it visible for other users as well
2.  In the JSON file describing the content of the node add a policy node like this (it applies `read` privilege to the build-in group `content-authors`):
```json
    "rep:policy": {
      "jcr:primaryType": "rep:ACL",
      "allow": {
        "jcr:primaryType": "rep:GrantACE",
        "rep:principalName": "content-authors",
        "rep:privileges": [
          "jcr:read"
        ]
      }   
    }
```
3.  Within the role file you have also set the "Access Control Handling" to "merge" mode for this content package to get the ACL applied - look up the property name in the documentation: https://devops.wcm.io/conga/plugins/aem/extensions.html
4.  Redeploy the application and configuration to your local AEM instance

Validation:

*   Log into AEM using the author user (created in step D)
*   Open http://localhost:4502/libs/cq/i18n/translator.html - you should see for columns with translations labelled DE, EN, FR, IT
