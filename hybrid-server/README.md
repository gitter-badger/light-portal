# Light Portal Hybrid Server

This is the [light-hybrid-4j](https://github.com/networknt/light-hybrid-4j) 
server generated by [light-codegen](https://github.com/networknt/light-codegen) to 
host all services built on top of light-hybrid-4j framework.
 
Please note that we only use the generator once and will maintain hybrid-server
module manually afterward. 
 
When light-portal is deployed, there might not be a lot of volume in the beginning. 
We will be using only one light-hybrid-server to host all the services built on top
of light-hybrid-4j. Once volume is getting heavy, we will be using two servers: one
for query side services and one for command side services. Each server can be scaled
to multiple instances based on the read-only load or write-only load. Also, we have
an option to move one or two hot services to a separate server and scale it accordingly
later on.

The config.json can be found in [model-config](https://github.com/networknt/model-config) 
repo at https://github.com/networknt/model-config/tree/master/hybrid/portal-server 


The command to generate the server:

```
java -jar codegen-cli/target/codegen-cli.jar -f light-hybrid-4j-server -o ~/networknt/light-portal/hybrid-server -c ~/networknt/model-config/hybrid/portal-server/config.json
```

Please note that you have to checkout [model-config](https://github.com/networknt/model-config) 
and [light-portal](https://github.com/networknt/light-portal) in the same workspace
networknt. And make sure you run the command in [light-codegen](https://github.com/networknt/light-codegen)
folder in the same workspace. Above command assume that the workspace is ~/networknt


## Start server

For light-portal, we put all light-hybrid-4j services into hybrid-service folder as
a list of jar files. When we start the light-hybrid-4j server, we need to include
these jars in the classpath. 

If you are developing hybrid services for light-portal, you should use the test server
in test folder to test your service locally. If you do want to run your service with
this server, please update the command line below to add the jar file from your project

The following command line to start the server with all the services in hybrid-service
folder. The command line should be executed in light-portal/hybrid-server folder.

```
java -cp target/pserver-1.0.0.jar:../hybrid-service/* com.networknt.server.Server
```

If you only want to test one service jar file, then your can include the jar file into 
the class path as below.

```
java -cp target/pserver-1.0.0.jar:~/networknt/light-codegen/codegen-web/target/codegen-web-1.3.4.jar com.networknt.server.Server
```

## Test

Given that light-codegen/codegen-web is one of the services, to ensure your hybrid-server
is working, we are going to call one of the action in codegen-web to verify if the server
is functioning.

```
curl -X POST \
  http://localhost:8080/api/json \
  -H 'cache-control: no-cache' \
  -H 'postman-token: 8fd2f7de-d50a-5b4e-a110-0cd62c239302' \
  -d '{"host":"lightapi.net","service":"codegen","action":"listFramework","version":"0.0.1"}'
```

and the expected result should be

```
[
    "light-hybrid-4j-server",
    "light-graphql-4j",
    "light-rest-4j",
    "light-hybrid-4j-service"
]
```
