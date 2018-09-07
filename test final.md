# Documentation and other relevant links

- [XSA Introduction](https://github.wdf.sap.corp/xs2/xsa-docs/wiki)

- [XSA Wiki](https://wiki.wdf.sap.corp/wiki/display/xs2/Home)

- [Help Portal link to XSA dev guide](https://help.sap.com/viewer/4505d0bdaf4948449b7f7379d24d0f0d/2.0.01/en-US)

- [SAP Github XS2 repos](https://github.wdf.sap.corp/xs2)

- [What is CAP](https://github.wdf.sap.corp/pages/cap/)

- [What is CDS](https://github.wdf.sap.corp/pages/cap/CDS)

- [CAP issues](https://github.wdf.sap.corp/cap/issues/issues):
  Check the issues there in case you're stuck somewhere during installation, build or run. Consider creating a new issue
  in case you cannot find an existing solution. And consider adding the information about your CAP issue and the
  solution at the correct place in our project's documentation.

- [CAP matters](https://github.wdf.sap.corp/cap/matters/issues):
  This repo is meant to submit requirements to CAP, to get an overview of the CAP roadmap and current activities and to
  find updates and rollout materials in the content.

*Note:* Below I'm trying to avoid any deep links into the CDS and CAP documentation, because
the [respective repo](https://github.wdf.sap.corp/pages/cap) seems to be in continuous restructuring. Just start
searching from the links given above.

- [CAP PoC project Excise Duty](https://github.wdf.sap.corp/ICDCloudArchitcture/excise_duty);
  the project page for Excise Duty has a lot of interesting information around CAP development.

## Development Guidelines

- [CAP Development Guidelines](https://wiki.wdf.sap.corp/wiki/display/Indigo/Development+Guidelines)

# Tool-chain set-up

## Overview

This document provides the basic description how to manually set-up a local dev-environment in the CAP environment for
XSA deployment as we'll use it for CAP development.

*Note:* We also have another repo which provides build tools and documentation for an automated (but also limited)
set-up of a Docker-containerized dev-environment [here](https://github.wdf.sap.corp/SAPPERF/dev-env). Since having a
reliable and reproducable dev-environment does have a different focus than having a nice manually refined local
environment with e.g. debugger support that can be build based on the info given here, it can very well make sense to
have both options available on your local PC.

*Note:* As an update for the availability of the "Windows subsystem for Linux" (*WSL*) coming with the SAP Windows image
from May 2018 [that documentation](https://github.wdf.sap.corp/SAPPERF/dev-env) now also provides some hints how one can
use the WSL installation as the basis for a separate development environment only for the CAP development tools, either
with or without Docker.

## Git

- I'm using [PortableGit](https://git-scm.com/download/win) (warning: IANAL for whether that fits SWAP; a recent search
  through SRI showed a grey-listed Eclipse pluging from 2010 or the white listed FSF Git linked
  as [this](https://git-scm.com/), which should be reasonable close
  to the Portable Git download link [here](https://git-scm.com/download/win)).

  That installer includes the git command line executable plus many potentially helpful unix command line tools.

- Check [this SO-link](https://stackoverflow.com/questions/10418975/how-to-change-line-ending-settings) for how to set line
  endings for multiple developers.

- You can check your current settings, both from the system config and your user's global config, using:

  ```bash
  git config --list --show-origin
  ```

  The system config is defined in a file relative to your installation path, the user's global config is for Windows
  defined in `%HOMEDRIVE%%HOMEPATH%\.gitconfig`, and the project-local config is defined in
  `{project-folder}\.git\config`.

- *Proposal:* we all use the following settings (so also no proxy definition):

  ```bash
  git config {--global or --local} core.autocrlf true
  git config {--global or --local} http.sslVerify false
  ```

- *Environment:* No environment variables are required to run the git command line client, but you might like to add the
  path to the git executable and maybe also to the unix command line tools to your path.

  An usual issue when running git in a corporate environment are proxy settings. For me git worked fine in a shell where
  both environment variables `HTTP\_PROXY` and `HTTPS\_PROXY` were empty and where the respective config settings were
  also not set.

## HDBSQL command line client

There might come the day when you want to simply run some SQL against our HANA on ld4312. In this case `hdbsql` will
come in handy. I already had `hdbsql` installed, so I have no up-to-date info how to install that. `hdbsql` is helpful
for low-level access to the HANA, e.g. use something like:

  ```cmd.exe
  "C:\Program Files\SAP\hdbclient\hdbsql.exe" -n ld4312:30015 -u {user} -p {password} "select * from dummy"
  ```

- *Environment:* No environment variables are required to run the `hdbsql` command line client, but you might like to
  add the path to the executable to your path.

## Maven command line client

- [Download](https://maven.apache.org/download.cgi)

  *Note:* Maven itself is quite stable since long, so there will be no need to check for updates as often as for the
  other tools listed below.

- *Environment:* I added the following Maven-specific environment variables to the shell running CAP development:

  ```cmd.exe
  set M2_HOME={folder-where-maven-was-unpacked}
  set "PATH=%PATH%;%M2_HOME%\bin"
  ```

  There is also the optional environment variable `M2` which allows to set the target folder where Maven will
  install/cache packages locally. If not set, that defaults on Windows to `%USERPROFILE%\.m2\repository`.

- *Version info:* `mvn -version`

- *Command line help:* `mvn -help`

- Detailed info around the various SAP Nexus Maven repos can be found
  [here](https://wiki.wdf.sap.corp/wiki/pages/viewpage.action?pageId=895156806)

- *Note:* To avoid any release conflicts between developer systems it is crucial that all developers will use the same
  Maven repos since otherwise subtle build differences can be caused by release mismatches. Until decided otherwise,
  we'll start with the repo used in the settings file given here.

  Within SAP, we're not only using packages from the [global Maven repo](https://mvnrepository.com/repos/central), but
  we also have SAP-internal packages provided by the [SAP Nexus](http://nexus.wdf.sap.corp:8081/nexus/#), so we need to
  add that SAP Nexus to the list of repos visited by Maven by changing Maven user's settings file. The default user's
  settings file is often just a copy from the global settings file and will hence anyway not contain any manual changes,
  but is mostly an empty template with many comments about what needs to be configured.

  In case you still don't want to change your `settings.xml` file locally, you can also pass an alternative settings
  file on the command line. But note that since e.g. the MTA build tool calls Maven behind your back, setting a
  different file on the command line won't always be possible. There might still be options to avoid overriding your
  user or system settings file, but I didn't check this. See [here](https://maven.apache.org/settings.html) how Maven
  does process its settings files.

- You can check your current settings using (warning: wall of text...):

  ```bash
  mvn -X help:effective-settings
  ```

- If you only want to check the location of your setting files, the following should help:

  ```bash
  mvn -X help:effective-settings | grep settings.xml
  ```

- And if you only want to check the location of your local Maven repository, the following should help:

  ```bash
  mvn help:evaluate -Dexpression=settings.localRepository
  ```

  The default location for the user settings file is `${user.home}/.m2`, which on Windows resolves to
  `%USERPROFILE%\.m2\settings.xml` and on Linux resolves to `$HOME/.m2/settings.xml`. The default location for the local
  Maven repository (where Maven caches installed packages) is `${user.home}/.m2/repository`.

  We won't touch the system settings file (just for completeness: its default location is
  `${maven.home}/conf/settings.xml`, which on Windows resolves to `%M2_HOME%\conf\settings.xml`).

- *Proposal:* We all use the user settings as defined by the respective file in [our
  repo](https://github.wdf.sap.corp/SAPPERF/dev-env/blob/master/docker/config/maven-settings.xml). So ideally you can
  just copy that file to the location of your user settings file, if you already manually edited that user settings file
  you need to merge the content of both files.

- Another usual issue when running Maven in a corporate environment are proxy settings. For me Maven worked fine in a
  shell where both environment variables `HTTP\_PROXY` and `HTTPS\_PROXY` were empty and where the respective config
  settings were also not set.

## Node and npm command line client

- [Download for node](https://nodejs.org/download/release/v9.5.0/win-x64/)

- [Download for npm](https://github.com/npm/npm/releases)

- *Note:* There are multiple options to install `node` and `npm` separately or together. I do not have any
  recommendation.

- To update to the latest `npm` release:

  ```bash
  npm install -g npm@latest
  ```

  That installation command is using the `-g` flag to install `npm` as a global package. Per default global packages will
  be installed on Windows in `%APPDATA%\npm-cache`.

- *Environment:* I added the following `node`-specific and `npm`-specific environment variables to the shell running CAP
  development:

  ```cmd.exe
  set NODE_HOME={folder-where-node-was-unpacked}
  set "PATH=%PATH%;%NODE_HOME%"
  set "PATH=%APPDATA%\npm-cache\npm\{node-version}\package\bin;%PATH%"
  ```

- *Version info:* `node --version`, `npm --version`

- *Command line help:* `node -help`, `npm help`

- *Note:* As it is the case for Maven, we also have to make sure that we're using the same versions for the packages
  delivered by the Node package manager `npm`. Also for `npm`, we have a SAP-internal Nexus, that sits between our own PCs
  and the [global npm repo](https://registry.npmjs.org), so we also have to configure our local `npm` to refer to that SAP
  Nexus in addition to the global one. Until decided otherwise, we'll start with the repo used in the settings file
  given here.

  Again there are options to provide an alternative settings file on the `npm` command line, which would not work e.g. for
  invocation by MTAR. I did not check for options whether and how to avoid having to set the user-global `npm` settings.

- More documentation about the `npm` usage within SAP can be found
  [here](https://jam4.sapjam.com/groups/EenVqJLGu9ye3jXAmfhNW1/documents/SQlFvaXJzDg2EQlEA0HzSz/slide_viewer)
  and
  [here](https://github.wdf.sap.corp/ICDCloudArchitcture/excise_duty/blob/9799e245d8c690e7610a934e9449f286500d3bd3/README.md)

- The default location for the user's `npm` config file can be found by using:

  ```bash
  npm config get userconfig
  ```

  On Windows this returns `%USERPROFILE%\.npmrc` and on Linux it returns `$HOME/.npmrc`.

  And again even if we won't change the global `npm` config file, its location can be found by using:

  ```bash
  npm config get globalconfig
  ```

- *Proposal:* we all use the user config as defined by the respective file in
  [our repo](https://github.wdf.sap.corp/SAPPERF/dev-env/blob/master/docker/config/.npmrc). Using that we refer to the
  [milestone repo](http://nexus.wdf.sap.corp:8081/nexus/content/groups/build.milestones.npm) and not to the [release
  repo](http://nexus.wdf.sap.corp:8081/nexus/content/groups/build.releases.npm):

  Alternatively the following command is supposed to take care that the settings listed above will be added to the
  user config file:

  ```bash
  npm config set @sap:registry http://nexus.wdf.sap.corp:8081/nexus/content/groups/build.milestones.npm
  npm config set package-lock false
  npm config set strict-ssl false
  ```

- Another usual issue when running `npm` in a corporate environment are proxy settings. For me `npm` worked fine in a
  shell where both environment variables `HTTP\_PROXY` and `HTTPS\_PROXY` were empty and where the respective config
  settings were also not set.

- I had the issue that for whatever reason the `npm` start batch file created during `npm` install was generated
  incorrectly (error when started: "Cannot find module ..."). If you cannot start `npm` by invoking that batch file
  (location `%APPDATA%\npm-cache\npm\5.6.0\package\bin\npm.cmd`), ping me (Frank).

## MTA (Multi-Target Application) builder command line client

- Documentation for MTA can be found on
  [help.sap.com](https://help.sap.com/viewer/58746c584026430a890170ac4d87d03b/Cloud/en-US/ba7dd5a47b7a4858a652d15f9673c28d.html).

  The internal Wiki is [here](https://wiki.wdf.sap.corp/wiki/display/CXP/MTA+Build+Tool)

  The FAQ (with lots of info) is [here](https://wiki.wdf.sap.corp/wiki/display/xs2/MTA-FAQ)

- Various options exist to install the MTA command line client. Basically you just need the respective jar file and
  then some means to start the JVM with that jar file. I got the jar file from
  the [SAP Nexus UI](http://nexus.wdf.sap.corp:8081/nexus/#), using the "Quick Search for artifacts" for the
  `mta_archive_builder` and downloading the jar file.

  You can also use Maven to download that jar file:

  ```bash
  mvn dependency:get -Dartifact=com.sap.di:mta_archive_builder_core:{version}:jar
  ```

  Then for invocation you either need to find and run the jar in your local Maven repo or you need to figure out how to
  start the jar via Maven (I only found `mvn exec:java` to do that, but that will not run the jar just using a groupId
  and archetypeId, hence having no advantage over the first option).

- *Environment:* No environment variables are required to run the MTA command line client, but as it is a Java
  application it expects a correct set-up for the JVM. And it might be helpful to just hide the invocation of the JVM
  with the MTA jar file in a batch file.

- *Version info:* `java -jar {path-to-mta-jar} --version`

- *Command line help:* `java -jar {path-to-mta-jar} --help`

- Invocation to build the XSA-deployable - to be executed in the root of your project's folder:

  ```bash
  java -jar {path-to-mta-jar} --build-target=XSA build
  ```

- The MTA build tool does some magic when building a deployment - and that magic can go wrong. So as we do for Maven and
  `npm`, we also have to make sure that we're all using the same versions of the MTA builder. For the moment I have no
  better idea than having a well known place in our documentation where we define that common version. We might later
  find a way to handle that issue via Maven.

  The latest version I used during my test set-up were based on version 4.3.17.

- `mta` supports two formats of deployment descriptor files. Up to now I only used the `mta.yaml` format, as that was
  the auto-generated starting point of the `cds init` step.

- `MTA` stores a lot (or all) of its jar-file dependencies below your project folder, and it seems to update them with
  any invocation, so it should be sufficient to only keep MTAR's main jar file up-to-date.

## XSA command line client

- [Download](https://wiki.wdf.sap.corp/wiki/display/xs2/XSA+Downloads)

- [Important commands](https://wiki.wdf.sap.corp/wiki/display/xs2/Getting+Started)

- *Environment:* No environment variables are required to run the xs command line tool, but you might like to add the
  path to the executable to your path.

- *Version info:* `xs version`

- *Command line help:* `xs help`

- *Interesting commands:*

  - Login to xs, here login to org `SAPPERF` and space `DEV`:

    ```bash
    xs login -a https://ld4312:30030 -u {user} -p {password} -o SAPPERF -s DEV --skip-ssl-validation
    ```

  - List multi-target apps as deployed by you in your current org and space:

    ```bash
    xs mtas
    ```

  - The `xs` command line client will store user-related settings as the last provided `org` or `space` parameter in a
    file `%USERPROFILE%\.xsconfig`, so that you do not have to pass all parameters in later invocations.

- The parameters in login define the org and space area on XSA. User can prepare their own org and space using the XSA admin tool and     user **xsa_admin** :

  https://ld4312.wdf.sap.corp:30032/uaa-security/login

## CDX command line client

- There are various repos [below](https://github.wdf.sap.corp/cdx).

  Up to now I did not find any reason to manually install any CDX tool, whatever might be required seems to be installed
  during the installation of other tools.

## CDS command line client

- Since CDS is basically a set of `node` modules and some batch-files/scripts to run them, you need to have installed and
  configured `node` and `npm` correctly.

- Installation command (assuming that the SAP Nexus for `npm` is set in your .npmrc):

  ```bash
  npm install -g @sap/cds
  ```

  If for whatever reason you want to install CDS directly from the SAP Github repo, the following might work for you:

  ```bash
  npm install -g git+ssh://git://github.wdf.sap.corp/cdx/cds
  ```

  To install a specific release, you get the list of releases [here](https://github.wdf.sap.corp/cdx/cds/releasese)
  and you can install a specific release using

  ```bash
  npm install -g git+ssh://git://github.wdf.sap.corp/cdx/cds#rel/2.1.0
  ```

- *Environment:* since `cds` is installed as a global `npm` package, `npm` will make sure to make the batch/script files
  provided to start `cds` available without any additional environment settings.

- *Version info:* `cds version`

- *Command line help:* `cds help`

- The general installation documentation for CAP and CDS can be found
  [here](https://github.wdf.sap.corp/pages/cap/get-started/). I did not check the parts related to the Web IDE and
  related to VS code, but tried to follow the parts for the command line tools and the Eclipse installation.

- Some notes:

  - *Meta-note:* I'm not a `node` expert (and do not strive to become one), so what is written below might be incorrect.
    Please inform me in that case.

  - `npm` differentiates global and local packages, where the global packages are installed relative to a known path so
    that script files installed with such packages can be called without requiring a path.

    The installation command above is using the `-g` flag to install `cds` as a global package. That means that `cds`
    will be installed so that its scripts can be called without a pathname (assuming that the paths to `node` and `npm`
    are maintained). You can check the location of the globally installed package using:

    ```bash
    cds --version
    ```

  - This will - among other information - list the `cds` home directory, which will probably match
    `%NODE_HOME%\node_modules\@sap\cds`.

  - Packages in the `@sap` namespace do refer to the "scope" of the SAP Nexus. The list of packages in the SAP Nexus can
    be found [here](https://npm.dmzwdf.sap.corp/nexus/#browse/browse/assets:deploy.releases.npm).

## Tomcat server setup

When developing with CDS for Java, the service part will be deployed on a Tomcat server. During development there are
basically three options to use Tomcat to run your application/service:

1. Just deploy the service to XSA, it will then be started by the Tomcat server provided with XSA.

   *Pros:* No further prerequisits, will run the service where it will also run productively.

   *Cons:* Deployment takes some time and remote debugging will require at least some additional precautions.

1. Run a local Tomcat from within your Eclipse installation, using the SAP Neo SDK which includes a Tomcat (8) as
   described [here](https://github.wdf.sap.corp/pages/cap/Cookbook/99-Local-Dev/#run-in-eclipse).

   *Pros:* SAP proposed way to run Tomcat locally, no need to manually add files to your local Tomcat other than
   pointing to the Neo SDK. Local debugging should be simpler than remote debugging (on a Tomcat server shared with
   others).

   *Cons:* Eclipse server configuration via UI will not work, Tomcat manager UI not provided, so whatever you want to
   configure for that Tomcat you need to configure via one of the many Tomcat configuration files. Also you're not
   running the service where it will also run productively.

   I did not yet figure out a good workflow for myself using this option, but there is an end-to-end documentation (from
   Maik) how to install and use Eclipse with the CAP environment and the local Neo SDK
   [here](https://github.wdf.sap.corp/SAPPERF/documentation/blob/master/HowToStart_Dev_Local_Eclipse.md).

1. Run a local Tomcat from within your Eclipse (JEE) installation, using the standard Eclipse JEE Tomcat (9)
   as provided with the Eclipse bundle.

   *Pros:* Eclipse server configuration via UI will work and Tomcat manager UI is available, so most settings and
   actions can be done through a UI. Local debugging should be simpler than remote debugging (on a Tomcat server shared
   with others).

   Since that was the local installation I worked with, I should have the relevant steps assembled as a starting point
   to get that set-up running. (I've not yet decided whether I'll spend some more time to get that set-up partly
   automated including debugger support or whether I'll figure out the workflow with the SAP Neo SDK.)

   *Cons:* It takes some additional effort to make the required CAP/HANA specific files available to that Tomcat server
   installation, it might take additional precautions to provide different settings files for local and remote
   deployment. Also you're not running the service where it will also run productively.

   The following steps worked for my local Tomcat to provide the required CAP/HANA specific files:

   1. *Get the required jar files*: Since we need to maintain the list of missing jar files for creating the automated
      dev-environment as mentioned above anyway, you can just refer to the respective section of the Dockerfile
      [here](https://github.wdf.sap.corp/SAPPERF/dev-env/blob/master/host/dockerfile/Dockerfile-tomcat8), search for
      `RUN mvn dependency:copy`. This list then includes files such as `org.apache.tomcat:tomcat-jdbc:{version}:jar` or
      `com.sap.xs.java:xs-jdbc-datasource:{version}:jar`. If you don't want to run the docker set-up and don't want to
      fiddle around with manual `mvn dependency` calls, you can also find those files here:
      `\\DWDF207.wdf.sap.corp\NW_E_Perf\technology\11.Tools
      UI-X\05.NewDataModel\dev-env-prereqs\jar-files-required-for-local-tomcat`. Note that I won't always keep the
      files here up-to-date; for the time being this will be a manual update that can be done by anyone finding a
      version conflict; and one day we'll have that automated e.g. by Maven.

   1. *Make jar files available to Tomcat*: Once having access to these jar files, for my local Eclipse installation
       both of the following options worked fine when adding the jar files:

       1. To the library folder of the war file generated for the service you want to test (this folder holds the jar
          files used by our app deployed to Tomcat, e.g. our services). For the case of the repository folder holding
          the sources of the E2E pilot being `~\ipa\src\cap-e2e-pilot\ipa2` and assuming the default naming convention
          for the war-file (as `srv-0.1.0-SNAPSHOT`), that correct folder to put the jar files into would be
          `~\ipa\src\cap-e2e-pilot\ipa2\srv\target\srv-0.1.0-SNAPSHOT\WEB-INF\lib`. Don't forget to rebuild the war file
          after the jar files have been added (`java -jar {path-to-mta-jar} --build-target=XSA build`) and to then
          undeploy and deploy that updated war file into Tomcat.

       1. To the Tomcat library folder. For the case of Tomcat being installed in `~\apache-tomcat-9.0.5` that correct
          folder to put the jar files into would be `~\apache-tomcat-9.0.5\lib`.

      *Note:* Other then mentioned in the CAP Getting Started Guide it also worked to deploy a war file including those
      jars - there were no conflicts with the jar files already available on the Neo server.

      *Note:* It might be a 3rd option to make the missing jar files available to Tomcat by adding them as external jar
      files to the classpath of the run/debug configuration of the Tomcat server instead of copying them to one of the
      folders listed above. I didn't try yet whether that works.

**Some more notes:**

- Even the most current version of Tomcat 9 has issues with JDK9, so for now we seam to be bound to JDK8.

- Ideally we could wrap the Tomcat 9 setup into a Maven package, but there doesn't seem to be an official up-to-date
  Maven Tomcat plugin - instead there are various competing plugins with only minimal documentation. Going through the
  effort to find out the best way to mavenize Tomcat doesn't currently seem to be worth the effort.

- As an additional option, you can also try to work with the dockerized Tomcat as described in
  the [README](https://github.wdf.sap.corp/SAPPERF/dev-env/blob/master/README.md) of the repo describing the set-up and
  usage of the automated dev-environment. This documentation also provides information how to attach from an Eclipse
  installed natively on your PC (the host) to a Tomcat running in a Docker container. Even if I got that running, I did
  not yet collect sufficient experience about the potential limitations of that approach. So we have to find out,
  whether it will still make sense to also set-up a complete Eclipse project for local debugging.

  The information provided in the mentioned [README](https://github.wdf.sap.corp/SAPPERF/dev-env/blob/master/README.md)
  could also be good enough to connect to the remote Tomcat process running on ld4312 - in case we find a way to start
  that Tomcat in debug mode, and in case that running that Tomcat in debug mode would not impact performance too
  heavily; both points mentioned being not insignificant *ifs*...

- In case you run the Tomcat server from within Eclipse, you might not be able to see the complete Tomcat log output as
  Eclipse per default limits the size of the console output. You can either remove the limitation completely or change
  the console buffer size by changing the settings in Window -> Preferences -> Run/Debug -> Console.

## Getting the SAP Neo SDK

As noted above the *CAP Getting Started Guide* proposes to use the SAP Neo SDK for local testing and deployment of a
service or an app. If you want to run/test you can found the SDK [here](https://tools.hana.ondemand.com/#cloud).

Once the Neo SDK is installed locally, you can go to the ~/tools folder of the installation and invoke the `neo` script
or batch file for various commands.

- *Version info:* `neo version`

- *Command line help:* `neo help` or `neo help [group:]command`

# Testing and using the development environment

## Create a new project

Once the dev-environment has been set-up, you can test your installation by following the steps in the CAP's Getting
Started Guide. As we found out, there will be parts where incorrect code is generated and that will lead to errors
during testing. Since we will anyway soon have our own CAP artifacts to test the installation and since the CAP team is
still heavily changing the installation procedure, I'm not listing detailed workarounds below. Please ping me (Frank) in
case you're really stuck.

1. Initialize project named `ipa2`:

   ```bash
   cds init ipa2
   cd ipa2
   ```

1. Initialize database artifacts:

   ```bash
   cds init --db
   ```

1. Initialize service artifacts for Java runtime:

   ```bash
   cds init --java
   ```

1. Build MTA archive file:

   ```bash
   java -jar {path-to-mta-jar} --build-target=XSA build
   ```

1. Deploy to XSA:

   ```bash
   xs deploy ipa2.mtar
   ```

   *Note:* This will deploy the package to the XSA server, space and org referenced during `xs login`.
   During deployment, the deploy log written to the command shell will contain a line such as

   > Application "{xyz-srv}" started and available at "https://{server}:{port}"

   You can use the application name to further check the environment set for this application, using:

   ```bash
   xs env {xyz-srv}
   ```

   And you can open the service metadata page served by CAP on XSA by opening the URL https://{server}:{port}/odata/v2/
   in the browser. As we're currently not having a CA-certificate set-up on ld4312, you might get a browser warning when
   trying to open that URL. This page should then present a header such as "Odata EndPoints:" and a list of service
   endpoints, which in turn will return an XML description per service when opened.

   Sadly this will only work for services where we do not use a namespace. For services with a namespace - and that's
   what we're currently doing for the service implementation of the end-to-end pilot - you'll only get "No Odata
   EndPoints Exposed". Still even in this case reading the service metadata should work, just open the URL
   https://{server}:{port}/odata/v2/{namespace}.{servicename}/$metadata.

*Note:* For all code and URLs provided in this document and with the current version of the End-to-end Pilot we assume
the usage of OData V2, not OData V4. Even if OData V4 is available since a few years, it is not yet supported by Fiori,
so CAP and CDS default to supporting OData V2, and as we're planning to use Fiori UI we need to stick to that default.
According to information provided during the *CAP Consulting Hour*, the planned availability for support of OData V4 in
Fiori is end of 2018.

This has some consequences for our development:

- OData V4 seems to have significant advantages over OData V2, both feature-wise and performance-wise. So it will make
  sense to switch to OData V4, once available.

- There are significant API-differences between OData V2 and OData V4, so that we'll have to spend some time for that
  migration. The differences will probably mostly show up on the Fiori-UI side and for the integration tests (which
  access the backend using an OData client) - but there doesn't seem to be a way to avoid this (except not writing UIs
  and tests...).

- Once OData V4 becomes available, it will probably become available as a switch in the CDS compile infrastructure which
  is transparent for invocation by the MTAR; in that case the switch would at least not have big impact on the
  development infrastructure.

## Building and deploying the End-to-end Pilot

*Note:* The code that you can find in the respective folder is currently a very minimal first subset of the End-to-end
Pilot; it's just good enough to check your build environment.

As noted above, using the standard build for CDS and CAP might or might not work currently. As an alternative, you can
clone our source repo, then build and deploy that. The relevant steps in that case are, again assuming that you've
already logged into XSA (we recommend to use your own ORG or SPACE for testing purposes; how to get that see chapter
about the [XSA command line client](#xsa-command-line-client)):

   ```bash
   git clone https://github.wdf.sap.corp/SAPPERF/ipa.git
   cd ipa\src\cap-e2e-pilot\ipa2
   java -jar {path-to-mta-jar} --build-target=XSA build
   # If the mtar build returns an error, it seems to help to run it again...
   xs deploy ipa2.mtar
   xs env ipa2-srv
   ```

Once the mta archive has been deployed, you should again be able to open the service metadata page served by CAP on XSA
by opening the URL https://{server}:{port}/odata/v2/ in the browser, where the https://{server}:{port} is given in the
log of the `xs deploy command` in the line starting with "Application "ipa2-srv" started and available at".

If you want to edit the Java sources of the End-to-end pilot in Eclipse, the following might work - at least it did for
me (Frank):

1. Select File -> Open Projects From File System

1. Select the folder ipa\src\cap-e2e-pilot\ipa2\srv

1. Eclipse will now import the whole project, based on the pom.xml in the project folder.
   It might complain about errors in the pom.xml, which I for now just ignored.

1. You can now use Eclipse to edit the sources, but note that Eclipse will only build the class files but not the
   complete CAP artifacts.

   So either you spend some more time to set-up a better Eclipse/Maven integration which will also generate the complete
   required artifacts (and ideally also allow to start the local Tomcat with the services classes built in the project)
   (and even more ideally also extend this documentation as how to acchieve this) or you simply keep on compiling using
   the command line tools as noted [above](#building-and-deploying-the-end-to-end-pilot).

### How to Run test cases through terminal:

1.	After successful Maven build, Copy the war file from the target folder in project location and put that file in the webapps folder of local apache tomcat server. <br/>
![Run]( image/serverStartup.png )
2.	Start the server through terminal- Go to apache tomcat “bin” folder location and run command “startup”.
![Test]( image/testRun.png )
3.	After server is start – hit the localhost URL to check if everything works fine.
http://localhost:8080/srv-0.1.0-SNAPSHOT/odata/v2/com.sap.caps.ipa2.MeasurementService/User
4.	Start the tests – Go to testodatav2 project location where POM file is present and run command mvn test.

### How to Run test cases through Eclipse: Also refer below Reference Detail for more info
1.	There are two types of server – one is local tomcat server and other one is Neo java SDK server. Please refer below Reference Detail for installing both type server in eclipse.
2.	Follow all the steps present in link to start the server - https://github.wdf.sap.corp/SAPPERF/documentation/blob/master/HowToStart_Dev_Local_Eclipse.md
3.	After server is up – hit the localhost URL to check if everything works fine.
http://localhost:8080/srv-0.1.0-SNAPSHOT/odata/v2/com.sap.caps.ipa2.MeasurementService/User
4.	Start the tests – Go the teste2e project and run the project in maven test.

#### Reference Detail -
1. If you are using **local tomcat server**:
- How to add the server in eclipse– Add the local tomcat server into the eclipse by using link  https://help.eclipse.org/neon/index.jsp?topic=%2Forg.eclipse.stardust.docs.wst%2Fhtml%2Fwst-integration%2Fconfiguration.html. 
- In case of local tomcat server you need to add some external jars in local tomcat server lib folder- You can copy jars from this location 
"\\DWDF207.wdf.sap.corp\NW_E_Perf\technology\11.Tools UI-X\05.NewDataModel\dev-env-prereqs\jar-files-required-for-local-tomcat"
- How to deploy war file into the tomcat server – There are two ways to deploy it on the server. One is copy the war file from the target folder in project location and put that file in the webapps folder of local apache tomcat server. And second way is to deploy the war file through tomcat manager link - localhost:8080/manager/html”.


2. If you are using **NEO JAVA SDK server** – Refer below link plus here you don’t need to add any external jars 
https://www.sap.com/india/developer/tutorials/hcp-java-eclipse-setup.html

3.	**Issues** I face while working on eclipse: I was still getting 404 error error on hitting the localhost URL after sever start.
Issue get resolved by below steps – 
- First step, Right click on server and click on property- Click on Switch location and change to same as mentioned below : 
![First Step]( image/ecpliseIssue1.png )
- Then Second step, double click on server and in server location change the radio box to Use Tomcat installation – which create a wtswebapps folder in the server location where project get deployed.
![Second Step]( image/ecpliseIssue2.png )



# Tips and hints

- Where to find the tables generated for the entities and the views generated for the services in your development
  folder:

  These files will be generated into:

  - `~/db/src/gen/{service-basename}.hdbcds`

    - So somewhat surprisingly below `~/src` (not below `~/target`) and below `~/db` (not below `~/srv`)

    - This file seems to be only generated if you add the following line to your DB-entity definition:

      `using from '../srv/{service-basename}.cds';`

    - The views in the HANA DB will be created based on the information in this file

  - `~/srv/target/classes/edmx/Ipa2Service.xml`

    - And from there it will be copied to other target folders...

    - The service information provided as http://xyz/odata/v2 is been built based on the information in this file

- Where to find the tables generated for the DB-entities on the hana side:

  - In HANA studio, add a system for ld4312, using the user and password info given as "user" and "password" by `xs
    env`. Then below "Catalog" open the schema given as "schema" by `xs env` and expand the node "Tables". The tables
    should now be visible as `namespace.context.tablename`.

- Where to find the views generated for the services on the hana side:

  - In HANA studio, add a system for ld4312, using the user and password info given as "user" and "password" by `xs
    env`. Then below "Catalog" open the schema given as "schema" by `xs env` and expand the node "Views". The
    service-views should now be visible as `namespace.servicename.entityname`.

  *Note:* The mapping between the CAP identifiers and the HANA objects is described in the [CAP
  documentation](https://github.wdf.sap.corp/pages/cap/Advanced/2sql). The relevant part is quited below:

  > The dots in qualified names of CDS definitions are mapped to underscores in plain SQL. For example:

  ```cds
  namespace foo.bar;
  context Car {
    entity Wheel { diameter: Decimal; }
  }
  ```

  > unfolds to the following SQL DDL statement:

  ```sql
  CREATE TABLE foo_bar_Car_Wheel (
    diameter Decimal
  )
  ```

- How to get `cds` errors from the remote Tomcat server on ld4312:

  I did not yet find the Tomcat log subfolders in the filesystem of ld4312, but it seems that the Tomcat log is also
  exposed in the XSA log. So if you're sending a request to a service and you're getting an error response which tells
  you to check the logs, you can run something like the following:

  ```shell
  xs login ...
  xs logs ipa2-srv --last 200
  ```

- How to add debug output when running `cds`: set the environment variable `DEBUG=true`

- The following commands have been listed in the CAP issues as a way to force update of npm packages (to force update of
  the CDS package they need to run in the CAP folder where the file `package.json` is maintained):
  `npm install && npm update` or `rm -rf node_modules && npm install`.

# Naming conventions, guidelines

## SAP

- [CAP Best Practices](https://github.wdf.sap.corp/pages/cap/best-practices/)

- [Predictive Maintenance - Programming guidelines
  (2016)](https://wiki.wdf.sap.corp/wiki/display/PredMaint/Programming+guidelines)

- [SQL Script Basics](https://wiki.wdf.sap.corp/wiki/display/nabmi/SQL+Script+Basics)

- [CDS naming conventions](https://github.wdf.sap.corp/pages/cap/Cookbook/02-Best%20Practices/#naming-conventions)

  *Note:* the naming conventions listed below should match or extend the CDS naming conventions. If this is not the case
  and if this is not explicitly mentioned otherwise, please fix the documentation below or inform me (Frank).

## General

- For identifiers only use letters (a-zA-Z) and the underscore character (_) and digits (0-9). No quoted special
  characters and no quoted whitespace in identifiers. Do not use reserved [SQL
  keywords](http://www.sqlstyle.guide/#appendix) as identifiers. Do not use double or multiple subsequent underscores.

- Naming suffixes and prefixes:

  - Don't use type-prefixes for entities, e.g. not `vViewOnSomething` or `cvCalcViewOnSomething`, but use
    `viewOnSomething`; reason: it might become necessary to swap between tables and views. Also keep history in mind,
    see [Hungarian Notation](https://en.wikipedia.org/wiki/Hungarian_notation).

  - No prefix for application namespace, we have SQL-schemas and CDS-namespaces and CDS-contexts for that.

  - Usually no entity/table prefixes, but do prefix lookup tables with the name of the table they relate to for all
    non-generic lookup tables.

  - Use consistent property/column names and types for properties/columns with same semantics in different
    entities/tables.

  - Use standardized suffixes for typical property/column semantics and prefix them usually with the entity/table name
    as in:

    - `xyzId`, `xyz_id`: integer key, will usually be generated by a sequence

    - `xyzCode`, `xyz_code`: developer-defined short string describing a technical attribute, language EN

    - `xyzName`, `xyz_name`: user-defined name for an attribute that will also be visible to the end-user

    - `xyzDscr`, `xyz_dscr`: user-defined (potentially longer) description for an attribute that will also be visible to
      the end-user and that will have additional info for a named entity

    - `_uom`: for columns where the values are based on a common UoM, e.g. `xx_elapsed_time_nanosec`
      (`_milli`, `_micro`, `_nano`).

    - `_pct`: for columns holding percent values

    - `_status`: flag value or some other status of any entity type

    - `_total`: the total or sum of a collection of values

    - `_num`: denotes the field contains any kind of number

    - `_seq`: contains a contiguous sequence of values (other than the _id)

    - `_date`: denotes a column that contains the date of something; UTC date

    - `_time`: denotes a column that contains the time of something; UTC time

    - `_at`: denotes a column that contains the timestamp (date and time) only of something; UTC timestamp

    - `_epoch_sec`: denotes a column that contains the epoch time in seconds

    - `_epoch_millisec`: denotes a column that contains the epoch time in milliseconds

    - `_by`: denotes a column that contains a user-id

    - `_count`: a count

    - `_size`: the size of something such as a file size or clothing

    - `_addr`: an address for the record could be physical or intangible such as ip_addr

    - Otherwise use full words and avoid abbreviations whereever possible, especially those abbreviations with an
      uncommon meaning.

    *Note:* Defining this naming convention is a hard decision.

    The alternative is to not prefix property/column names: this leads to useless column names like "text", "name" and
    without prefixing the column names, greping for column names becomes tedious - just search "ID" through the whole
    code base when you only want to find the `projectId`. And more importantly this can lead to ugly errors in joins,
    just try to spot the error in the two code blocks below:

    ```sql
    select *
    from   cars car
    join   manufacturer mfg
    on     mfg.id = car.manufacturerid
    join   models mod
    on     mod.id = car.modelid
    join   colors col
    on     mfg.id = car.colorid
    ```

    ```sql
    select *
    from   cars car
    join   manufacturer mfg
    on     mfg.manufacturerid = car.manufacturerid
    join   models mod
    on     mod.modelid = car.modelid
    join   colors col
    on     mfg.manufacturerid = car.colorid
    ```

    Still the [CDS naming
    convention](https://github.wdf.sap.corp/pages/cap/Cookbook/02-Best%20Practices/#naming-conventions) proposes to
    **not** replicate the entity name in the property. This seems to be fine when working within CDS, but for the
    reasons mentioned above it has disadvantages when working like that in SQL. I'm currently defining the End-to-end
    pilot with a naming using `TableId`, not `Table.Id` - and we'll find out during the development of the End-to-end
    pilot whether that works or not.

## Java

Same conventions as for SUPA sources, see respective Eclipse set-up.

## CDS

Next to the [naming convention for
CDS](https://github.wdf.sap.corp/pages/cap/Cookbook/02-Best%20Practices/#naming-conventions), what is written below is
taken from the [CDS documentation](https://github.wdf.sap.corp/pages/cap/CDS/).

- 2 spaces, no tabs, maximum line length 100

- `lowercase` for language keywords

- `UpperCamelCase` for entities, services, namespace, (standard and self-defined) types, ...

- `lowerCamelCase` for variables, elements, properties/columns, ...

- Use `/* ... */` for comments, so that blocks of code can be commented out by `//`

- *Note:* We'll have to find out how working in SQL works with the CDS naming convention given above. Hand-writing SQL
  using the "schema"."someFancyObject" notation is plain ugly and hard to type - but using a non-standard naming
  convention for CDS is even worse.

  *Update on the note:* It seems to be case that the CDS team collected some feedback on the casing-issue that caused
  them to add a so called "plain-mode", which will be the default mode soon. In this mode, HANA DDIC names will no
  longer be generated as "schema"."someCoolNamespace.someFancyObject", but as SCHEMA.SOMECOOLNAMESPACE_SOMEFANCYOBJECT.
  As this will become the default mode and since it is nou clear whether the current "quoted mode" will stay be
  available for all future version, it will probably make sense to switch to "plain-mode" as soon as available. Sadly
  the limiting factor is the support on the Java-side, so we might have to do some rework, once the switch to
  "plain-mode" is available for us. I'll update this documentation, once the official documentation for that feature is
  available.

  *Later update on the note:* There is now some additional information on the SQL Name Mapping topic available, see
  [the documentation page here](https://github.wdf.sap.corp/cap/matters/blob/master/etc/SqlNameMapping.md) and the
  respective [Github issue here](https://github.wdf.sap.corp/cap/matters/issues/120).

  *Later update on the note (July 6th):* When fetching a new CAP environment for a recent test, I eventually cloned a
  version combination into the Docker-based environment which already defaulted to the new SQL Name Mapping
  "plain-mode". Since we currently do not have any hand-written SQL DDL or DML, the naming change is mostly transparent
  for us - it just comes up during testing, e.g. if you want to find test-entities in the HANA database or if you want
  to query specific entities by ID using a REST-URL.

  It turned out to be quite tricky to force the update to that new version in an already existing installation (e.g. the
  installation on your local PC) - where technically in a file `~/cds/lib/models/cdsv.js` the configuration variable
  `cds.config.data.sql_mapping` has to be defaulted with the value `"quoted"`. What finally worked both for my local
  installation and for the Docker-based environment was to change the required `npm` versions both for `cds` and
  `cds-compiler` to use the current Github-master - so this is the setting you're getting with the updated master of the
  End-to-end pilot. This seems anyway to be a reasonable default at least during development of the End-to-end pilot -
  as it will save us from debugging already fixed issues, just for the price of having to debug fresh errors not yet
  fixed in the master.

  A quick lackmus-test for your installation is to check the content of folder `~/src/cap-e2e-pilot/ipa2/db/src/gen/`
  for files named like `COM_SAP_CAPS_IPA2_MEASUREMENTSERVICE_USER.hdbcds` - this SCREAMING\_SNAKE\_CASE is used for the
  new "plain-mode".

- Use the following scheme for tables holding n:m references: If a natural term for such a relation exists, use that one
  (e.g. `service` instead of `car_mechanic_map`). Otherwise name it as `{abbrevtable1}_{abbrevtable2}`, where
  `{abbrevtable}` is a 2 to 5 character abbreviation of the entity/table name (if the name is not short enough anyway)
  and where the abbreviations are listed in alphabetical order. Abbreviations:

  + Release: `RLS`
  + Project: `PRJ`
  + Scenario: `SCN`
  + Test case: `TSC`
  + Measurement header: `MSH`
  + Step: none required, short enough
  + ...

  The alternative is to prefix column names with the full table name - and this is a lot to type - and more importantly
  table names are volatile in times of synonyms.

## SQL

- 4 spaces, no tabs, maximum line length 100

- `lowercase` for language keywords

- For identifiers which are **not** based on CDS, use `snake_case`, but not `camelCase` or `PascalCaseNames`

  *Note:* the decision to use lowercase both for SQL identifiers and your own identifiers/database objects is another
  hard decision.

  The alternatives are to use database object identifiers lowercase, SQL keywords uppercase - or vice versa.

  Even if this helps reading the code, it usually gets mixed up over time anyway, since it is not enforced by the tools.
  And since it's better to have a bad naming convention over having no naming convention, I state the proposal from
  above. And as an extra plus, the lowercase only is easier to type.

- For database object identifiers which are **not** based on CDS, use the following prefixes and suffixes:

  + `proc_` for SQL script procedures

  + `type_` for table types

  + `_pk` for table-scope primary key constraint; in total: `{tablename}_pk`

  + `_fk` for table-scope foreign key constraints; in total: `{from_tablename}_{to_tablename}_fk`

    If there are multiple FKs to the same table, use some semantic prefix for each FK column.

    *Note:* The [CDS naming
    convention](https://github.wdf.sap.corp/pages/cap/Cookbook/02-Best%20Practices/#naming-conventions) explicetly tells
    us to avoid foreign keys. The main arguments given are that foreign keys are imperative (?) and of no use for NoSQL,
    where the second argument is not relevant for us when using HANA. I (Frank) am not buying that, so we will need to
    find out with the end-to-end pilot what to do best..

  + other table-scope constraints: check: `_ck`, not null: `_nn`, unique: `_uq`, index: `_idx`

- Use `/* ... */` for comments, so that blocks of code can be commented out by `--`

Below some samples for indentation:

```sql
insert into albums (title, release_date, recording_date)
values ('Charcoal Lane', '1990-01-01 01:01:01.00000', '1990-01-01 01:01:01.00000'),
       ('The New Danger', '2008-01-01 01:01:01.00000', '1990-01-01 01:01:01.00000');

update albums
set    release_date = '1990-01-01 01:01:01.00000'
where  title = 'The New Danger';

select a.title,
       a.release_date, a.recording_date, a.production_date -- grouped dates together
from   albums as a
where  a.title = 'Charcoal Lane'
or     a.title = 'The New Danger';
```

```sql
(select  f.species_name,
         avg(f.height)   as average_height,
         avg(f.diameter) as average_diameter
from     flora as f
where    f.species_name = 'Banksia'
or       f.species_name = 'Sheoak'
or       f.species_name = 'Wattle'
group by f.species_name, f.observation_date)

union all

  ...
```

```sql
create procedure proc_xyz(
   in  value integer,
   in  currency nvarchar(10),
   out out_table type_table,
   out valid integer)
as begin
    ...
end;
```

*Note:* I found the following info on HANA SQL naming conventions and restrictions:

-	SAP HANA: You can write SQL syntax elements in upper case or lower case.

-	SQL: Unquoted identifier is case insensitive. Quoted identifier is case sensitive. Unquoted identifiers are
    basically converted to uppercase.

-	SAP HANA: To force the system to distinguish between upper-case and lower-case letters in database object names
    (such as table names), enter the name between double quotation marks,

-	SAP HANA: supports now table names and column names support with > 32 characters, probably 127 characters.

-	SAP HANA: A name cannot be an SAP HANA reserved word (relevant e.g. for the `RELEASE` entity, as `RELEASE` is also
    an SQL keyword).

-	SAP HANA: otherwise I did not find any hints in SAP doku, e.g. in the reference guide
    https://help.sap.com/doc/6254b3bb439c4f409a979dc407b49c9b/2.0.01/en-US/SAP_HANA_SQL_Script_Reference_en.pdf

## UI

Wherever the UI uses CDS-based artifacts, the conventions listed above apply. Additional UI-specific conventions can
also be added here.

## Additional proposals

  - Use singular names (instead of plural names) both for tables and columns.

    - Table/entity names: singular, e.g. `MeasurementHeader`, not `MeasurementHeaders`

    - Column/property names: singular, e.g. `rawValue`, not `rawValues`

  - Do not add a column with the same name as its table and vice versa.

  - Don't default to a surrogate primary key column, instead use concatenated/natural key - but only if changes of key
    column values cannot happen and if the table is not heavily joined/referenced.

  - For the surrogate primary key column use integer IDs instead of GUIDs. Use GUIDs only where IDs have to be generated
    by distributed clients and where debugability doesn't matter.

    So no "GUID as default", use GUIDs only if required; default to using integer IDs and sequences (maximum sequence
    value is 4611686018427387903). Reason: known to anyone who ever debugged a GUID-heavy application. There might also
    be a negatice performance impact of GUIDs compared to integer IDs and according to Panos there are still some
    hickups when using GUIDs in CAP artifacts **and** on the HANA SQL side.

    Where required, a table has its own sequence in form `{table_name}_seq`. It is only used to populate the
    corresponding primary key column.

    Again the [CDS naming
    convention](https://github.wdf.sap.corp/pages/cap/Cookbook/02-Best%20Practices/#naming-conventions) proposes to use
    GUIDs instead of sequence IDs. Since we're having a single application on a single database I don't see the given
    disadvantes of sequences applying to us. So we again can use the End-to-end pilot to find other whether we really
    need to use GUIDs.


## Some more guidelines from the internet

- http://www.sqlstyle.guide/

  + Quite complete overview and proposal incl. many code samples e.g. for formatting

- http://www.gplivna.eu/papers/naming_conventions.htm

  + Quite complete overview and proposal incl. further links

- http://leshazlewood.com/software-engineering/sql-style-guide/

  + Another quite complete overview with some helpful advice on code formatting, naming examples and so on

- https://justinsomnia.org/2003/04/essential-database-naming-conventions-and-style/

  + Another one with a lengthy discussion e.g. one the ={table_name}_id= vs =id= discussion.

- http://www.vertabelo.com/blog/technical-articles/naming-conventions-in-database-modeling,
  https://launchbylunch.com/posts/2014/Feb/16/sql-naming-conventions/,

  + And some more...

- https://stackoverflow.com/q/7662:

  + Using ID all over the place is LIVING HELL for anyone doing joins of multiple tables. There's no possible way that
    the slight advantage of knowing this is the PK outweighs the incredible annoyance of re-aliasing the dang ID column
    in every bloody query over and over again. If you want a way to denote PK in a table, make it the first column.

  + Denoting FKs in the names of columns is in my mind another solidly evil anti-pattern. => TODO: used above

  + Singular table names avoid bugs and mistakes caused by plural ambiguities.

  + Prefixing IDs with plural table names is ugly, using the singular name for that case is inconsistent.

  + There is much more discussion and arguments down that thread.

- https://stackoverflow.com/questions/7662/database-table-and-column-naming-conventions:

  + lots of discussions around naming for tables and columns

- https://softwareengineering.stackexchange.com/a/114734

  + sample code where =ID= instead of =TABLE_ID= can go wrong.

# TODOS

- Add info how to set-up Tomcat in Eclipse for local debugging

- Get local server - based on Neo SDK - running

