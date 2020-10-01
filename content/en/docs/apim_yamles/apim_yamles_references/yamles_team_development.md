{
"title": "Team Development with YAML configuration",
"linkTitle": "Team Development with YAML configuration",
"weight":"90",
"date": "2020-09-25",
"description": "Team Development with YAML configuration"
}

Common and API projects created using Policy Studio [Team Development](/docs/apigtw_devops/team_dev_practices/#enable-team-development-in-policy-studio) functionality can be converted into YAML configurations, in the same way as any other XML federated configuration. The converted YAML projects will validate successfully, but may need the `--allow-invalid-ref` option to pass validation as some of these projects will contain entities that reference entities in another project.

The `projpack` and `progupgrade` tools cannot be used with the YAML entity store. The equivalent of `projpack` can be done using standard command line tooling. There is no support or need yet for the upgrade of YAML configurations.

The `projdeploy` tool can be used with YAML configuration but the input into this is a YAML `.tar.gz` file built using standard tooling. The design of the YAML configuration lends itself well to Team Development for many use cases. The natural split of the configuration into separate files allows multiple developers on a team to more easily change the same YAML configuration project without causing merge difficulties. Merge conflicts that do occur are more easily understood and resolved as files are smaller and more human readable.

There is still a need to support multiple teams or developers working on completely separate YAML configuration projects. A team of developers may work on a project that contains common corporate security policies. These security policies should be reusable across many other projects in the company. Such reuse can be facilitated in YAML mode by simply copying the required files into the correct directory location in the final YAML configuration that will be contained in the `.tar.gz` file that gets deployed. This could be managed via a CI/CD pipeline if required.

The following example walks through a very simple example and shows how a merged `.tar.gz` file can be created for deployment from two separate projects. Let's assume our starting point is is a pair of projects that were created in XML format in Policy Studio using the Team Development templates, one using the `Common Project` and the other using the `API Project`.

The `common-security` project created using the `Common Project` template, contains a `Common security` policy as follows:

![Team development](/Images/apim_yamles/yamles_team_dev.png)

* The `test-api` project, created using the **API Project** template contains a simple policy named `Test` with a `Set Message` and `Reflect` filter.
* A listener on port `8080` on path `/test` is linked to this policy.
* The `common-security` project is added as a Team Development project dependency via **File→Manage Dependencies**.
* The `Common security` policy is then selected as a G`lobal Request Policy` (by right-clicking the policy as selecting **Set as Global Request Policy**).

![Team development](/Images/apim_yamles/yamles_team_dev_2.png)

Both of these projects can be converted separately to YAML using the `yamles fed2yaml` option.

```
./yamles fed2yaml --fed-url federated:file:/home/user/apiprojects/common-security/configs.xml -o ~/team-dev/common-security --targz ~/team-dev/archives/common-security.tar.gz
./yamles fed2yaml --fed-url federated:file:/home/user/apiprojects/test-api/configs.xml -o ~/team-dev/test-api --targz ~/team-dev/archives/test-api.tar.gz
```

They may be separately validated as follows:

```
./yamles validate --yaml-url yaml:file:/home/user/team-dev/common-security
./yamles validate --yaml-url yaml:file:/home/user/team-dev/test-api --allow-invalid-ref
```

Note the use of `--allow-invalid-ref` for the `test-api` project as it has a reference to an entity in the `common-security` project in the `System/Global Properties.yaml` file:

```yaml
---
type: GlobalProperties
fields:
  name: Global Properties
children:
- type: ReferenceProperty
  fields:
    name: system.policy.request
    value: /Policies/Common security
```

The `/Policies/Common security.yaml` file exists in the `common-security` project.

In order to create a YAML configuration that is complete, we need to create a merged `.tar.gz`. This could be done by manually copying the  `/Policies/Common security.yaml` file from `common-security` project into the  `test-api` project, and then creating the `.tar.gz` from there.

CI/CD pipelines for the `common-security` and `test-api` projects may each publish a `.tar.gz` into Artifactory. These need to get merged before deployment.

The `test-api.tar.gz` and `common-security.tar.gz` can be merged by extracting the two `.tar.gz` files to a tmp directory and then creating a new tar with the result thus merging, see below.
(The tar `--concatenate` command resulted in duplicate files in the resulting `.tar.gz` file which will not work).

```
NEW_TMP_DIR=`mktemp -d` \
   && CURR_DIR=$PWD \
   && tar -C $NEW_TMP_DIR -xvf ~/team-dev/archives/common-security.tar.gz \
   && tar -C $NEW_TMP_DIR -xvf ~/team-dev/archives/test-api.tar.gz \
   && cd $NEW_TMP_DIR \
   && tar -zcvf $CURR_DIR/merged.tar.gz * \
   && cd $CURR_DIR\
   && rm -Rf $NEW_TMP_DIR
```

The `merged.tar.gz` will have the policies from the `test-api` and `common-security` projects in the `/Policies directory`:

![Team development](/Images/apim_yamles/yamles_team_dev_3.png)

The `/System/Global Properties.yaml` will have the same content as above. Note that ordering of the `common-security.tar.gz` and `test-api.tar.gz` in the merge command above is **extremely important**. If the ordering was the other way around then the `/System/Global Properties.yaml` in the `merged.tar.gz` would be from the `common-security.tar.gz` which would mean that there was no global request policy set.

The `merged.tar.gz` should validate without the `--allow-invalid-ref option`:

```
./yamles validate --targz ./merged.tar.gz
```

The `merged.tar.gz` can be deployed in the usual way via `managedomain` or `projdeploy`.

This simplistic approach to merging projects may break down if there are merge conflicts between projects, for examples policies have the same name. There is no out-of-the-box tooling provided to check these scenarios for YAML configurations. You also need to ensure that any entities that are referenced by policies exist in merged `.tar.gz` file. This may become awkward to manage if there are a large number entities or projects to consider. It may also be awkward to manage merges if you need a yaml file from the first `.tar.gz` and a different yaml file from the second `.tar.gz`, where both the files exist in both the `.tar.gz` files, as simply ordering the `.tar.gz` files as above will not work.