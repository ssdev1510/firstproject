### How to Run tests case through terminal:

1.	After successful Maven build, Copy the war file from the target folder in project location and put that file in the webapps folder of local apache tomcat server.
2.	Start the server through terminal- Go to apache tomcat “bin” folder location and run command “startup”.
3.	After server is start – hit the localhost URL to check if everything works fine.
http://localhost:8080/srv-0.1.0-SNAPSHOT/odata/v2/com.sap.caps.ipa2.MeasurementService/User
4.	Start the tests – Go to testodatav2 project location where POM file is present and run command mvn test.

### How to Run tests case through Eclipse:
1.	There are two types of server – one is local tomcat server and other one is Neo java SDK server. Please refer below Reference Detail for installing both type server in eclipse.
2.	Follow all the steps present in link to start the server - https://github.wdf.sap.corp/SAPPERF/documentation/blob/master/HowToStart_Dev_Local_Eclipse.md
3.	After server is up – hit the localhost URL to check if everything works fine.
http://localhost:8080/srv-0.1.0-SNAPSHOT/odata/v2/com.sap.caps.ipa2.MeasurementService/User
4.	Start the tests – Go the teste2e project and run the project in maven test.

#### Reference Document -
1.	Servers:
1.1 If you are using local tomcat server:
a.	How to add the server in eclipse– Add the local tomcat server into the eclipse by using link “https://help.eclipse.org/neon/index.jsp?topic=%2Forg.eclipse.stardust.docs.wst%2Fhtml%2Fwst-integration%2Fconfiguration.html. 
b.	In case of local tomcat server you need to add some external jars in local tomcat server lib folder- You can copy jars from this location 
[I'm a relative reference to a repository file](\\DWDF207.wdf.sap.corp\NW_E_Perf\technology\11.Tools UI-X\05.NewDataModel\dev-env-prereqs\jar-files-required-for-local-tomcat)
c.	How to deploy war file into the tomcat server – There are two ways to deploy it on the server. One is copy the war file from the target folder in project location and put that file in the webapps folder of local apache tomcat server. And second way is to deploy the war file through tomcat manager link - localhost:8080/manager/html”.


1.2	If you are using NEO JAVA SDK server – refer below link – here you don’t need to add any external jars 
https://www.sap.com/india/developer/tutorials/hcp-java-eclipse-setup.html

2.	Issue I face while working on eclipse – After succesfully server start -  I was still getting 404 error error on hitting the localhost URL.
Issue get resolved by below steps – First step, Right click on server and click on property- Click on Switch location and change to same as mentioned below : 

Then Second step, double click on server and in server location change the radio box to Use Tomcat installation – which create a wtswebapps folder in the server location where project get deployed.

3.	After server start – I hit the MeasurementService service-  I am getting below “No Connection to database” error.
Issue get resolved by adding missing jars in local tomcat server

