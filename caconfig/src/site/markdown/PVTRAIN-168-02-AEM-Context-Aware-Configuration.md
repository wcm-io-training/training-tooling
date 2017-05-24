## PVTRAIN-168-02 AEM Context-Aware Configuration

### Overview

*   Configure Context Path Strategy and Persistence Strategy
*   Define configuration metadata and custom widget
*   Store configuration in tools/config page (only for German market)

### Git project

*   [https://github.com/wcm-io-training/training-caconfig-exercise-aem](https://github.com/wcm-io-training/training-caconfig-exercise-aem)

### Introduction

Deploy the application to your local AEM 6.3 instance running on port 4502 via the script `clean_install_deploy_package.sh`.

The repository contains a simple AEM application with:

*   homepage and a content template showing configuration parameters
*   Some simple preconfigured context-aware configuration classes
*   Sample content with two simple sites:
    *   [http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en.html)
    *   [http://localhost:4502/content/pv-training-caconfig/tenant-1/germany/de.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/germany/de.html)
*   The Configuration Editor template is also configured:
    *   [http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html)
    *   [http://localhost:4502/content/pv-training-caconfig/tenant-1/germany/de/tools/config.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/germany/de/tools/config.html)

There are no context paths configured, so the configuration editor is not functional initially.

### Exercise

#### A) Configure Context Path Strategy and Persistence Strategy

Exercise:

1.  Configure a [wcm.io Context Path Strategy](http://wcm.io/caconfig/extensions/context-path-strategies.html) for levels 1..5 for the template `/apps/pv-training/caconfig/templates/homepage`
    *   Derive conf path from content path by replacing "/content" with "/conf"
2.  Configure a [wcm.io Persistence Strategy](http://wcm.io/caconfig/extensions/persistence-strategies.html) for storing configurations as AEM Pages at `/conf`
3.  Define the configurations for both in `config-definition/src/main/templates/pv-training-caconfig-aem-cms/pv-training-caconfig-aem-cms-config.provisioning.hbs`
    *   Keep in mind you have to execute `clean_install_deploy_package.sh` again to get this configuration active in the AEM instance

Validation:

*   Open configuration editor for US page [http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html)
*   Enter some singleton configuration for "Training CAConfig Site Config"
    *   Also enter some configuration data in the nested "Shopping basket" configuration
*   Enter some configuration collection items for "de.provision.training.caconfig.aem.config.LinkList"
*   Ensure that the configuration was stored in the repository at
    *   `/conf/pv-training-caconfig/tenant-1/usa/en/sling:configs/de.provision.training.caconfig.aem.config.SiteConfig/jcr:content`
    *   `/conf/pv-training-caconfig/tenant-1/usa/en/sling:configs/de.provision.training.caconfig.aem.config.LinkList/*/jcr:content`
*   Download the sample content via `sample-content/sample-content_download.sh` you entered to commit it in your training repository

#### B) Define configuration metadata and custom widget

Exercise:

1.  Edit the configuration definition `de.provision.training.caconfig.aem.config.LinkList` and add labels and descriptions for the configuration class and for each property
2.  Define a custom path browser widget for the [wcm.io Context-Aware Configuraiton Editor](http://wcm.io/caconfig/editor/usage.html) with starts with the current configuration context path as root path for the property `pagePath`

Validation:

*   Open the configuration editor for US Page / Link list: [http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html#/de.provision.training.caconfig.aem.config.LinkList](http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html#/de.provision.training.caconfig.aem.config.LinkList)
*   Ensure configuration and property labels and descriptions are displayed
*   Click on the browse button beneath the "Page path" field and ensure it allows only to select pages from the current site, e.g. "Page 1" and "Page 2"

#### C) Store configuration in tools/config page (only for German market)

Exercise:

1.  Configure an **additional** [wcm.io Persistence Strategy](http://wcm.io/caconfig/extensions/persistence-strategies.html) for storing configurations as AEM Pages at `/tools/config` Pages relative to the site root as part of the content
    *   This strategy gets only active for context paths with `/tools/config` in the path
2.  Configure an **additional** [wcm.io Context Path Strategy](http://wcm.io/caconfig/extensions/context-path-strategies.html):
    *   This strategy should only be applied for content/context paths at `/content/pv-training-caconfig/tenant-1/germany` and below (only the German market)
    *   This strategy should derive an additional config reference at `<context-root-path>/tools/config/jcr:content`, e.g. `/content/pv-training-caconfig/tenant-1/germany/tools/config/jcr:content`
    *   To make sure this strategy has higher precedence for the German market assign it a higher service ranking value than the other strategy
3.  Define the configurations for both in `config-definition/src/main/templates/pv-training-caconfig-aem-cms/pv-training-caconfig-aem-cms-config.provisioning.hbs`
    *   Keep in mind you have to execute `clean_install_deploy_package.sh` again to get this configuration active in the AEM instance

Validation:

*   Open the configuration editor for the german site [http://localhost:4502/content/pv-training-caconfig/tenant-1/germany/de/tools/config.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/germany/de/tools/config.html) and enter some singleton configuration and collection configuration data
    *   Verify that the configuration data you entered was stored below `/content/pv-training-caconfig/tenant-1/germany/de/tools/config/jcr:content/sling:configs`
    *   Verify that it was **not** stored below `/conf/pv-training-caconfig/tenant-1/germany/de/sling:configs`
*   Open the configuration editor for the US site [http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html](http://localhost:4502/content/pv-training-caconfig/tenant-1/usa/en/tools/config.html) and change and save some configuration data
    *   Verify that it was stored below `/conf/pv-training-caconfig/tenant-1/usa/en/sling:configs`
    *   Verify that it was **not** stored below `/content/pv-training-caconfig/tenant-1/usa/en/tools/config/jcr:content/sling:configs`
*   You can also open the Context-Aware Configuration web console plugin to debug the context paths and configuration references for each content context path
    *   [http://localhost:4502/system/console/slingcaconfig?path=%2Fcontent%2Fpv-training-caconfig%2Ftenant-1%2Fusa%2Fen&configName=de.provision.training.caconfig.aem.config.SiteConfig&configNameOther=](http://localhost:4502/system/console/slingcaconfig?path=%2Fcontent%2Fpv-training-caconfig%2Ftenant-1%2Fusa%2Fen&configName=de.provision.training.caconfig.aem.config.SiteConfig&configNameOther=)
    *   [http://localhost:4502/system/console/slingcaconfig?path=%2Fcontent%2Fpv-training-caconfig%2Ftenant-1%2Fgermany%2Fde&configName=de.provision.training.caconfig.aem.config.SiteConfig&configNameOther=](http://localhost:4502/system/console/slingcaconfig?path=%2Fcontent%2Fpv-training-caconfig%2Ftenant-1%2Fgermany%2Fde&configName=de.provision.training.caconfig.aem.config.SiteConfig&configNameOther=)
*   Download the sample content via `sample-content/sample-content_download.sh` you entered to commit it in your training repository
