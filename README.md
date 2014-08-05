CBI Examples
============

This repo contains example code that is used as an example to using the CBI
infrastructure at Eclipse. This repo contains an example Eclipse plugin which
is used to create an example feature and example p2 repository. The parent
pom.xml file contains an example pom configuration that covers the basic
fields required to upload the built artifacts to repo.eclipse.org as well as
sign the code using Eclipse infrastructure.

Building CBI Examples
=====================

Simply run:

    mvn clean verify


If building on Hudson / HIPP at Eclipse you can also sign the build by
passing the 'release' profile:

    mvn clean verify -Prelease


Creating a Hudson job
=====================

The following steps will create a Hudson job that does signing and uses
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
7. Enter the URL for your git repo: git://git.eclipse.org/gitroot/cbi/org.eclipse.cbi.examples.git
8. Under **Build** click **Add build step**, select **Invoke Maven 3**
9. Set **Goals** to be **clean verify**
10. Click **Advanced**
11. In Profiles enter **release**
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

cp -r $WORKSPACE/org.eclipse.cbi.examples.updatesite/target/repository $SITE_DIR
</pre>

21. Click **Save**

Promoting a build
-----------------

1. Click **Build Now** for the job if you have not already completed a build
2. Under **Build history** select the most recent build you want to promote
3. Click **Promotion Status**
4. Verify the parameters and click **Approve**
