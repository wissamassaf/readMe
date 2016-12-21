## About Amazon EC2

[Amazon EC2](https://aws.amazon.com/ec2/?hp=tile&so-exp=below)Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers.
Amazon EC2’s simple web service interface allows you to obtain and configure capacity with minimal friction. It provides you with complete control of your computing resources and lets you run on Amazon’s proven computing environment. Amazon EC2 reduces the time required to obtain and boot new server instances to minutes, allowing you to quickly scale capacity, both up and down, as your computing requirements change. Amazon EC2 changes the economics of computing by allowing you to pay only for capacity that you actually use. Amazon EC2 provides developers the tools to build failure resilient applications and isolate themselves from common failure scenarios.



## Components
- aws/config: Script that contains accessKey,accessSecret,region, availabilityZone, service, version, notificationEmail, stateCheckIntervalInSec, and channelName.
- aws/httpclient : generic http client that handles the communication between scriptr.io and AWS's APIs, the authorization's signature, and parsing the parameters sent to the aws Apis.
- aws/AWSManager : this is the main object to interact with. It has methods that create instances of other objects to use, along with createTags, createChannel, and a generic method that calls any aws api.
- aws/InstanceManager: This object can be used to run , describe, describe state, and terminate an instance, along with a method that schedules a script  aws/instanceStateChecker.
- aws/instanceStateChecker: This script is a scheduled script that runs according to the stateCheckIntervalInSec variable defined in the config file that checks the state of the instance, and inform the user via email when the instance reaches the desired state(running, terminated). Also the script will create tags, and subscribe another script.
- aws/NetworkManager: This object can be used to create, describe, delete Vps, and create, describe, delete subnet.
- aws/SecurityManager: This object can be used to create, delete, describe a keypair, and create, delete , describe security groups, along with authorize security group ingress, and authorize security group egress methods.
- aws/VolumeManager: This object can be used to create, delete, describe, attach, and describe the state of a volume, along with a method that schedules a script  aws/volumeStateChecker.
- aws/volumeStateChecker: This script is a scheduled script that runs according to the stateCheckIntervalInSec variable defined in the config file that checks the state of the created volume, and inform the user via email when the volume reaches the desired state(available). Also the script will create tags, and subscribe another script.

## Authentication:
Scriptr will handle the authentication and signatures which are required to do any api call with aws so you won't have to worry about signature or authorization with aws.
## How to use
- Use the Import Modules feature to deploy the aforementioned scripts in your scriptr account, in a folder named "modules/aws".
- Create a developer account and an application at [aws](https://aws.amazon.com/ec2/).
- Once done, make sure to specify the accessKey,accessSecret,region, availabilityZone, service, version, notificationEmail, stateCheckIntervalInSec, and channelName in "config" file.
- Create a test script in scriptr. 


### Use the connector

In order to use the connector, you need to import the main module: ```modules/aws/AWSManager``` as described below:
```
var awsModule = require("modules/aws/AWSManager");
```
Then create a new instance of the AWSManager class, defined in this module(Note: this instance can take a customized config file):
```
var myManager = new awsModule.AWSManager(config);
```
The AWSManager class provides many methods, such as:
```
getInstanceManager
getNetworkManager
getVolumeManager
getSecurityManager
createTags

```
Once you create new instance of the AWSManager class, you can create other instances of other objects in this connector by simply calling ```getInstanceManager()``` to create an instance of InstanceManager, or by calling ```getNetworkManager()``` to create an instance of NetworkManager, or by calling ```getVolumeManager()``` to create an instance of VolumeManager, or by calling ```getSecurityManager()``` to create an instance of SecurityManager.
```
var config = require("aws/config");
var awsManager = require("aws/AWSManager");
var am = new awsManager.AWSManager(config);
var im = am.getInstanceManager();
var vm = am.getVolumeManager();
var sm =am.getSecurityManager();
var nm = am.getNetworkManager();
var runInstanceParams = {
  "ImageId":"ami-3444ed54",
  "InstanceType":"t2.micro",
  "Placement":{
    "AvailabilityZone":config.availabilityZone
  }
}
im.runInstance(runInstanceParams,[{"Key":"Name","Value":"batata"}],{"text":"test"});
im.terminateInstance({"InstanceId":"i-0512128f7a73ef61f"});
im.describeInstanceState({"InstanceId":"i-0444d5651ee59360d"});

nm.deleteSubnet({"SubnetId":"subnet-bf4f5dc9"});
nm.describeSubnet({"SubnetId":"subnet-bf4f5dc9"});
nm.describeVpc({"VpcId":"vpc-7a9f471d"});
var subnetParams = {
   "CidrBlock": "10.0.0.0/16",
  	"VpcId": "vpc-7a9f471d"
};
nm.createSubnet(subnetParams,[{"Key":"Name","Value":"batata"}]);
var vpcParams = {
  "CidrBlock": "10.0.0.0/16"
}
nm.createVpc(vpcParams,[{"Key":"Name","Value":"batata"}]);
nm.deleteVpc({"VpcId": "vpc-e19d4586"});

var params = {
  "GroupName":"testGroup1",
 "GroupDescription":"this is another testing group"
};

var ingressParams = {
  "GroupName":"testGroup",
  "IpPermissions":[
   {"IpProtocol":"tcp",
    "FromPort":"2376",
    "ToPort":"2376",
    "IpRanges":[
      {"CidrIp":"0.0.0.0/0" 
      }
    ]
   },{
    "IpProtocol":"tcp",
    "FromPort":"22",
    "ToPort":"22",
    "IpRanges":[
      {"CidrIp":"0.0.0.0/0" 
      }
    ]
   }
  ],
}
sm.deleteSecurityGroup({"GroupName":"testGroup1"});
sm.createSecurityGroup(params,null,null,tagParams);
sm.describeSecurityGroup({"GroupName":"testGroup"});

var params = {
  "GroupName":"testGroup",
  "GroupDescription":"this is a testing group"
}



var ingressParams = {
  "GroupName":"testGroup",
  "IpPermissions":[
   {"IpProtocol":"tcp",
    "FromPort":"2376",
    "ToPort":"2376",
    "IpRanges":[
      {"CidrIp":"0.0.0.0/0" 
      }
    ]
   },{
    "IpProtocol":"tcp",
    "FromPort":"22",
    "ToPort":"22",
    "IpRanges":[
      {"CidrIp":"0.0.0.0/0" 
      }
    ]
   }
  ],
}
sm.createSecurityGroup(params,ingressParams);
sm.deleteSecurityGroup({"GroupName":"testGroup"})
sm.authorizeSecurityGroupIngress(params);
```
