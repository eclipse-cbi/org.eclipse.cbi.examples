# CBI Tycho Example

This repo contains an example to test the CBI infrastructure at Eclipse.
It contains an Eclipse plugin which is used to create a feature and a p2 repository.
The parent pom.xml covers the basic configuration required to upload the built artifacts to
repo.eclipse.org as well as sign the code using Eclipse infrastructure.

## Building CBI Tycho Example

Simply run:

<pre>mvn clean verify</pre>

If building on the CI infrastructure at Eclipse you can also sign the build by
passing the 'release' profile:

<pre>mvn clean verify -Prelease</pre>


## Creating a Jenkins freestyle job

The following steps will create a Jenkins freestyle job that does signing and deploys
the build artifacts to the CBI project's downloads area.

1. Click **New Job**
2. Enter a job name, e.g. 'eclipse-cbi-tycho-example-job'
3. Select **Build a free-style software job**
4. Click **OK**
5. For JDK, select `openjdk-jdk17-latest`
6. Select **This project is parameterized**
7. Click **Add Parameter > String Parameter**
   * Name: `SITE_DIR` 
   * Default Value: `/home/data/httpd/download.eclipse.org/your-project/some/subdir`
     *  (Modify to point to some directory in your project's
    downloads area).
8. Click **Add Parameter > Boolean Parameter**
    * Name: `DEPLOY`
9. Under **Source Code Management** select **Git**
    * Enter the URL for your git repo: e.g. `https://github.com/eclipse-cbi/eclipse-cbi-tycho-example`
    * Select branch `main`
10. Under **Build Environment** select **SSH Agent**
    * From the drop-down menu select `ssh://<bot-username>@projects-storage.eclipse.org`
11. Under **Build** click **Add build step**, select **Invoke top-level Maven targets**
    * Set **Goals** to be `clean verify -Prelease -B`
12. Click **Add build step** again, select **Execute Shell**
13. Below is an example shell script:

<pre>
#!/bin/bash

login="&lt;bot-username&gt;@projects-storage.eclipse.org"

if [[ "${DEPLOY}" == true ]]; then
  echo "Deploying..."
  #check if dir already exists
  if ssh ${login} test -d ${SITE_DIR}; then
    ssh ${login} rm -rf ${SITE_DIR}
  fi
  ssh ${login} mkdir -p ${SITE_DIR}
  scp -r org.eclipse.cbi.tycho.example.updatesite/target/repository ${login}:${SITE_DIR}
fi
</pre>

&lt;bot-username&gt; is "genie.&lt;shortname&gt;". E.g. for Eclipse CBI it's "genie.cbi".

14. Click **Save**

At this point test that the build is able to build successfully by clicking
**Build Now with**

### Deploying a build


1. Click **Build with Parameters**
2. Select **DEPLOY** checkbox
4. Click on **Build**

## Example jobs

Freestyle job
* https://ci.eclipse.org/cbi/job/eclipse-cbi-tycho-example-job/

Pipeline job
* https://ci.eclipse.org/cbi/job/eclipse-cbi-tycho-example-pipeline-job/
