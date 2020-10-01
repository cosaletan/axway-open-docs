{
"title": "CI/CD Example",
"linkTitle": "CI/CD Example",
"weight":"20",
"date": "2020-09-25",
"description": "Basic example that illustrates how to leverage YAML configuration in your CI/CD."
}

For the most basic of CI/CD pipelines you will need:

* the ability to build a deployment package (`.tar.gz` file) and push it to a repository such as Artifactory.
* a versioning mechanism that makes sense for your workflow, e.g. `SNAPSHOT` versions while in development, and `release` versions when configuration is tested and ready for promotion to staging and production environments.

This a possible way of using CI/CD to manage your YAML configuration:

* A simple build pipeline triggered by a user, or automatically via a `git push` to build the YAML `.tar.gz` and push it to Artifactory.
* A deploy pipeline can be triggered by a user, or automatically via the build pipeline to deploy the newly built `.tar.gz` file to an API Gateway runtime environment.
* A series of deploy pipelines may be chained to facilitate promotion of the configuration from dev through to production environments.

Below an example of workflow:

* A dev deploy pipeline may pull a YAML `.tar.gz` from Artifactory.
    * The deployment to the dev environment may use a default `values.yaml` for environmentalized fields.
    * The dev deploy pipeline deploys to the dev environment, and run tests on the dev environment.
* If the tests pass, a deployment to a test environment may occur automatically. A series of automated tests also run on the test environment.
* The `.tar.gz` deployed to the test environment may be changed to use different environmentalized values by overriding the `values.yaml` file.
* If the tests pass in the test environment, the pipeline could wait for user approval before deploying to production. Again the pipeline can override the environmentalized values in `values.yaml` so that they are suitable for the production environment.

These flows may be tailored to suit your own workflow. The YAML format is well suited to a CI/CD pipeline as the `yamles` and `projdeploy` CLI tools may be invoked along with standard Linux tooling to ensure the content of the deployed YAML `.tar.gz` contains what is needed for the target environment.

Modifications such as changing the `values.yaml`, or adding prod-only data such as private keys and certificates can be done by unzipping the `.tar.gz` and adding/removing/modifying the files in the tar as required. Then zipping up the tar file again before deployment.

For example the following commands take a prod `values.yaml` and prod certificates and keys from a secure location, place them into the `tar.gz`:

```bash
# Unzip the .tar.gz
gunzip -d $WORKSPACE/${params.ARTIFACTORY_TAR_GZ_FILENAME}
# Remove the default values.yaml from the tar
tar -vf ${ARTIFACTORY_TAR_FILENAME} --delete values.yaml
# Put the prod values.yaml into the tar
cp $SECURE_LOCATION/overrides/${params.OVERRIDE_VALUES_YAML_FILENAME} values.yaml
tar rvf ${ARTIFACTORY_TAR_FILENAME} values.yaml
# Put the prod certificates and keys into the tar
cp -R '$SECURE_LOCATION/src/overrides/Environment Configuration' .
tar rvf ${ARTIFACTORY_TAR_FILENAME} 'Environment Configuration'
# Zip the tar so we have a .tar.gz for deployment
gzip ${ARTIFACTORY_TAR_FILENAME}
Custom deployment archive properties could be added to the .tar.gz in a similar manner if required.
```

{{< alert title="Note" color="note">}}

* It is advised that you run the `yamles validate` tool on the final `.tar.gz` before deployment via your pipeline.
* The pipeline should use `projdeploy` for deployment purposes, once [validation](/docs/apim_yamles/yamles_cli/#how-to-validate-configuration-changes-in-the-yaml-configuration) passes.
* Both of these tools will run in a docker image with the **Package & Deploy Tools** installed. They do not need to be run in the server-side environment where the API Gateway runs.

{{< /alert>}}