CBI Tycho Example
=================

This repo contains an example to test the CBI infrastructure at Eclipse.
It contains an Eclipse plugin which is used to create a feature and a p2 repository.
The parent pom.xml covers the basic configuration required to upload the built artifacts to
repo.eclipse.org as well as sign the code using Eclipse infrastructure.

Building CBI Tycho Example
==========================

Simply run:

    mvn clean verify


If building on the CI at Eclipse you can also sign the build by
passing the 'release' profile:

    mvn clean verify -Prelease


Creating a Jenkins job
======================

The following steps will create a Jenkins job that does signing and uses
the promoted builds plugin to do build promotion to the CBI project's
downloads area.

Requirements
------------

- Promoted Builds plugin

Create a job to do a basic build
--------------------------------

1. Click **New Job**
2. Enter a job name
3. Select **Build a free-style software job**
4. Click **OK**
5. For JDK, select jdk1.7.0-latest
6. Under **Source Code Management** select **Git**
7. Enter the URL for your git repo: https://github.com/eclipse-cbi/eclipse-cbi-tycho-example
8. Under **Build** click **Add build step**, select **Invoke top-level Maven targets**
9. Set **Goals** to be **clean verify -Prelease**
12. Click **Save**

At this point test that the build is able to build successfully by clicking
**Build Now**

After the build is successful go back to the job **Configure** screen.

13. Check **Promote builds when...
14. Check **Only when manually approved**
15. Enter the list of Approvers (email, comma delimited)
16. Click **Add Parameter > String Parameter**
17. Enter **SITE_DIR** for Name
18. Enter **/home/data/httpd/download.eclipse.org/cbi/examples/updatesite** for
    Default Value (Modify to point to some directory in your project's
    downloads area).
19. Under **Actions** click **Add action**
20. Click **Execute Shell** below is an example shell script

<pre>
#!/bin/bash

if [ -d "$SITE_DIR" ]; then
  rm -rf $SITE_DIR
fi

cp -r $WORKSPACE/org.eclipse.cbi.tycho.example.updatesite/target/repository $SITE_DIR
</pre>

21. Click **Save**

Promoting a build
-----------------

1. Click **Build Now** for the job if you have not already completed a build
2. Under **Build history** select the most recent build you want to promote
3. Click **Promotion Status**
4. Verify the parameters and click **Approve**

Example job
-----------

Freestyle job
* https://ci.eclipse.org/cbi/job/eclipse-cbi-tycho-example-job/

Pipeline job
* https://ci.eclipse.org/cbi/job/eclipse-cbi-tycho-example-pipeline-job/
