#Globus Online - Java API

This tutorial will show you how to use the Globus Online java API.

##Requirements
We assume that you are working on a Mac or linux machine and have access to a terminal or command line.

###Accounts
- A globus online account. Some steps require authentication to Globus Nexus API with you globus online account. If you don't have a globus online account, make one [here](https://www.globusid.org/create).

###Java compiler
On the UI make sure you have the righ java comiler. To check the version of your java compiler:

```sh
javac -version
```

If you don't have a java compiler, first install one using the command below: (Cask is a pakkage manager used for instlling java on linux)

```sh
brew update
berw cask install java
```

###Bouncy Castle
Bouncy Castle is a collection of API's that is required for BCTransferAPIClient (and subclasses). You can download the latest bcprov jar for your JDK release [here](http://www.bouncycastle.org/latest_releases.html) and place it in a lib sub directory. If you are using Maven (next step) to build your project, it will automatically download Bouncy Castle.


###Maven 
Install Maven that is required to build the code. Maven is a build tool for java projects that can download and fetch the dependencies automatically (currently just Bouncy Castle).

```sh
brew install maven
```

##Example Run

Clone the transfer-api-client-java project from github:
 
```
git clone https://github.com/globusonline/transfer-api-client-java.git
cd transfer-api-client-java
```
Make sure you incorporate the following classes in your java project to run the transfer api client:

1) **BaseTransferAPIClient**. This class is a basic client for interacting with the Globus Online Transfer API as a single user, using x509 authentication. Does not make any assumptions about how the application will parse data or handle key and trust stores.

2) **BCTransferAPIClient** which is an extension to the base client (class "BaseTransferAPIClient") which supports reading PEM files using Bouncy Castle, so the client cert/key don't have to be converted to PKCS12. 


3) **JASONTransferAPIClient** which is a client which parses JSON response into org.json.JSONObject, from json.org.

###Build and Compile
Use Maven to download the dependencies and build/compile the client tool (this will automatically download the BounyCastle):

```
mvn clean install
```
This will generate a jar file in the `target` directory. Copy that to the lib folder of your project.

```
mkdir lib
cp ~/.m2/repository/org/bouncycastle/bcprov-jdk15on/1.54/bcprov-jdk15on-1.54.jar lib/
```
###GoAuth
New clients should use GoAuth for authentication.
GoAuth access tokens must be acquired from the Globus Nexus API. For testing, the simplest method is to use a command line tool like curl or wget. Here's an example using curl, which will prompt for your GO password:

```
export GO_USERNAME="zarrabi"
curl --user $GO_USERNAME 'https://nexus.api.globusonline.org/goauth/token?grant_type=client_credentials' > token.txt
```

The curl command gets the token from the website and saves it in a file. The result is a JASON. As specified in the command, the results will be written in token.txt file in the same directory. 

Retrive the globus online token and set the environment variables (`GO_USERNAME`, `ACCESS_TOKEN`) for Globus Online.

```
export ACCESS_TOKEN=$(cat token.txt |tr ',' '\n'|grep "access_token\""| awk -F': ' '{print $2}'|tr -d '"')
echo $ACCESS_TOKEN
```


### Run the transfer tool
To run the GO java transfer tool


```
java -cp lib/bcprov-jdk15on-1.54.jar:target/transfer-api-client-java-0.10.9-SNAPSHOT.jar org.globusonline.transfer.GoauthExample $GO_USERNAME "$ACCESS_TOKEN"
```

-------------
To install the globusonline cli (command line interface) on mac, first you  need to install Anaconda (a python package manager). 

Download and install Anaconda 2.7 (Graphical installer)https://www.continuum.io/downloads#osx

Then install globus command line interface 

```python
pip install globus-clipip show globus-cliglobus list-commands