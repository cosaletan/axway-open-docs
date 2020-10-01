{
    "title": "API Gateway post-upgrade scripts",
    "linkTitle": "API Gateway post-upgrade scripts",
    "weight": "140",
    "date": "2020-07-16",
    "description": "List of API Gateway scripts that you must run after upgrade your product."
}

After upgrading your API Gateway, you must run some scripts to fix issues or security vulnerabilities. This section lists the scripts, and their updates from 7.5.3 SP1 to 7.7.0, that you must run.

## `apigw_sp_post_install.sh`

This script must be executed after applying a service pack (SP) for API Gateway server installation. Currently, it mainly performs the following:

* Runs `updateNodeConfig`, that updates node manager configuration. On the **Protect Management Interfaces** policy, it changes the success node of Check Session to RBAC, removes the relative path `/api/rbac/logout` filter, and removes the **End Session** filter.
* Removes old API Gateway and third-party jars.
* Copies and renames some files, for example, `conf/acl.json`.

### `apigw_sp_post_install.sh` changes in 7.5.3

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.5.3SP1      | 14-Jun-2017 | Script removes old 7.5.3 jars.|
| 7.5.3SP2 | 31-Jul-2017 | - |
| 7.5.3SP3 | 01-Oct-2017 | Added removal of third-party jar files. |
| 7.5.3SP4 | 02-Nov-2017 | - |
| 7.5.3SP5 | 19-Jan-2018 | List of jars to remove was extended, cassandra jars added. |
| 7.5.3SP6 | 23-Mar-2018 | List of jars to remove was extended. |
| 7.5.3SP7 | 08-Jun-2018 | List of jars to remove was extended. |
| 7.5.3SP8 | 23-Aug-2018 | Update conf/acl.json, old acl.json renamed to acl.json.bak |
| 7.5.3SP9 | 15-Nov-2018 | Fixes APIGW login error from SP8, list of jars to remove was extended. |
| 7.5.3SP10 | 20-Feb-2019 | List of jars to remove was extended. |
| 7.5.3SP11 | 24-Jun-2019 | Fix traffic monitoring enabling recording of payload data, updated NM ES configuration, runs updateNodeConfig.py, list of jars to remove was extended. |
| 7.5.3SP12 | 25-Oct-2019 | List of jars to remove was extended. |
| 7.5.3SP13 | 01-May-2020 | Deletes system\lib\jython\Lib\this.py, fix post install bug from SP12, list of jars to remove was extended. |

### `apigw_sp_post_install.sh` changes in 7.6.2

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.6.2SP1 | 31-Oct-2018 | Removes old 7.6.2 & third-party jars, update conf/acl.json, old acl.json is renamed to acl.json.bak |
| 7.6.2SP2 | 14-Dec-2018 | - |
| 7.6.2SP3 | 17-Apr-2019 | List of jars to remove was extended |
| 7.6.2SP4 | 19-Jul-2019 | Fix traffic monitoring enabling recording of payload data, update NM ES configuration, runs updateNodeConfig.py, list of jars to remove was extended. |

### `apigw_sp_post_install.sh` changes in 7.7.0

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.7.0SP1 | 27-Aug-2019 | Removes old 7.7.0 & third-party jars, update conf/acl.json, old acl.json renamed to acl.json.bak |
| 7.7.0SP2 | 20-Dec-2019 | List of jars to remove was extended. |
| 7.7.0_SP20200130 | 06-Feb-2020 | List of jars to remove was extended. |
| 7.7.0_SP20200331 | 31-Mar-2020 | Fix group password stored in clear, executes updatePassphrases.py to update NM service config, list of jars to remove extended. |
| 7.7.0_SP20200530 | 27-May-2020 | Script was replaced by update_gw.sh script. |

## `apigw_analytics_sp_post_install.sh`

This script must be executed after applying service pack for API Gateway Analytics installation. Currently it mainly performs the following:

* Runs `updateAnalyticsConfig.py`, which updates the `fed/configs.xml` file to add default classes and packages into the Management API Servlet settings.
* Removes old API Gateway and third-party jars, jQuery files, CPP libraries.

### `apigw_analytics_sp_post_install.sh` changes in 7.5.3

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.5.3SP1      | 14-Jun-2017 | Script removes old 7.5.3 jars, executes updateAnalyticsConfig.py to update ES configuration in conf/listenersStore.xml |
| 7.5.3SP2 | 31-Jul-2017 | Added removal of third-party jar files. |
| 7.5.3SP3 | 01-Oct-2017 | List of jars to remove was extended, cassandra jars added. |
| 7.5.3SP4 | 02-Nov-2017 | - |
| 7.5.3SP5 | 19-Jan-2018 | Removes unused cpp libraries, list of jars to remove was extended.  |
| 7.5.3SP6 | 23-Mar-2018 | List of jars to remove was extended. |
| 7.5.3SP7 | 08-Jun-2018 | List of jars to remove was extended. |
| 7.5.3SP8 | 23-Aug-2018 | - |
| 7.5.3SP9 | 15-Nov-2018 | Removes jQuery files, list of jars to remove was extended. |
| 7.5.3SP10 | 20-Feb-2019 | List of jars to remove was extended, updateAnalyticsConfig.py script modified to add default classes/packages to Mgmt API Servlet Settings. |
| 7.5.3SP11 | 24-Jun-2019 | List of jars and jQuery files to remove was extended, list of default classes was extended in updateAnalyticsConfig.py script  |
| 7.5.3SP12 | 25-Oct-2019 | List of jars to remove was extended. |
| 7.5.3SP13 | 01-May-2020 | Fix defect when upgrading from SP3 to SP4, deletes system\lib\jython\Lib\this.py, fix post install bug from SP12, list of jars to remove was extended. |

### `apigw_analytics_sp_post_install.sh` changes in 7.6.2

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.6.2SP1 | 31-Oct-2018 | Script does not exist. |
| 7.6.2SP2 | 14-Dec-2018 | Script does not exist. |
| 7.6.2SP3 | 17-Apr-2019 | Script was added, it removes old 7.6.2 jars and jquery .js files. |
| 7.6.2SP4 | 19-Jul-2019 | Added list of third-party jars to remove, executes updateAnalyticsConfig.py to update ES configuration fed/configs.xml, adds default classes/packages to Mgmt API Servlet Settings. |

### `apigw_analytics_sp_post_install.sh` changes in 7.7.0

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.7.0SP1 | 27-Aug-2019 | Removes old 7.5.3 jars instead of 7.7.0 (bug), executes updateAnalyticsConfig.py to update ES configuration fed/configs.xml, adds default classes/packages to Mgmt API Servlet Settings. |
| 7.7.0SP2 | 20-Dec-2019 | Removes correct 7.7.0 jars, fix defect when upgrading from SP3 to SP4, list of jars to remove was extended. |
| 7.7.0_SP20200130 | 06-Feb-2020 | Fixes apigw_analytics_sp_post_install.sh works, but returns an error, script updated for OneVersion |
| 7.7.0_SP20200331 | 31-Mar-2020 | List of jars to remove extended. |
| 7.7.0_SP20200530 | 27-May-2020 | List of jars to remove extended. |

## `update-apimanager.py`

This script updates the active deployment in the API Manager group. After running the script, you must recreate the API Manager project (common project, containing Server Settings) from the deployment. It's main goal is to remove availability of the static files used in API Manager UI:

* /home
* /login-failed
* /password-reset
* /registration-success
* /lib/jquery-2.2.4.min.js
* /logout
* /registration-failed
* /registration-success
* /request-forgotten-pw-failed
* /request-forgotten-pw-success
* /reset-forgotten-pw-failed

This script performs the following:

* Removes "API Portal" and "OAuth 2.0 Services" StaticFile entities.
* Verifies that the "OAuth" entities (for servlet service="OAuth 2.0 Services" and version "v1.2" and version="1.3") contain the `com.vordel.apiportal.api.portal.v1_2` and `com.vordel.apiportal.api.portal.v1_3` variables in the `jersey.config.server.provider.packages` property.
* Verifies that `system/conf/apiportal/portal.xml` and `system/conf/apiportal/oauthupdate.xml` files exist.
* Removes the **API Manager Protection** policy and any references to it.
* Imports config in `system/conf/apiportal/portal.xml` and `system/conf/apiportal/oauthupdate.xml`.
* If using the `--group` parameter, deploy the updated config to the group.
* Writes the updated `fed` file to the output archive specified via `--oa` parameter if not using the `--group` parameter.

This script was removed in 7.6.2 SP1 and SP2, it was moved into migration steps for API Portal Configuration. Then, it was added back in 7.6.2 SP3.

### `update-apimanager.py` changes in 7.5.3

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.5.3SP1      | 14-Jun-2017 | Script does not exist. |
| 7.5.3SP2 | 31-Jul-2017 | Script was added to protect Static Content in API Manager resources. |
| 7.5.3SP3 | 01-Oct-2017 | - |
| 7.5.3SP4 | 02-Nov-2017 | - |
| 7.5.3SP5 | 19-Jan-2018 | Fixed insecure password submission bug from SP2, APIMGR Static Content can only be accessed via GET Http Method and return 403 Bad Request for requests with query parameters |
| 7.5.3SP6 | 23-Mar-2018 | - |
| 7.5.3SP7 | 08-Jun-2018 | OAuth-Services requests allowed directory listing for non-logged users. |
| 7.5.3SP8 | 23-Aug-2018 | - |
| 7.5.3SP9 | 15-Nov-2018 | - |
| 7.5.3SP10 | 20-Feb-2019 | Script refactored; added verbose output when --debug option is used. |
| 7.5.3SP11 | 24-Jun-2019 | Fixed customizing headers in APIMGR bug, fixed script failing when a group was protected with a passphrase. |
| 7.5.3SP12 | 25-Oct-2019 | - |
| 7.5.3SP13 | 01-May-2020 | Fixed Client App Registry not being able to update after an SP was applied bug.  |

### `update-apimanager.py` changes in 7.6.2

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.6.2SP1 | 31-Oct-2018 | Script was removed, migration step for PortalConfiguration was added. |
| 7.6.2SP2 | 14-Dec-2018 | - |
| 7.6.2SP3 | 17-Apr-2019 | Script was added again. |
| 7.6.2SP4 | 19-Jul-2019 | Fixed script failing when a group was protected with a passphrase. |

### `update-apimanager.py` changes in 7.7.0

| Service pack |    Date     | Changes in script |
|---------------|-------------|-------------------|
| 7.7.0SP1 | 27-Aug-2019 | Script was added. |
| 7.7.0SP2 | 20-Dec-2019 | - |
| 7.7.0_SP20200130 | 06-Feb-2020 | Fixed script error when updating Client App Registry. |
| 7.7.0_SP20200331 | 31-Mar-2020 | - |
| 7.7.0_SP20200530 | 27-May-2020 | UX was improved; added option that allows user to specify project directory to run update. |
| 7.7.0_SP20200930 | 30-Sep-2020 | update-apimanager.py has been removed in this release. Updates to API Manager are applied via config upgrade in the Service Pack, through Policy Studio upgrade, or the projupgrade and upgrade-policy tools |
