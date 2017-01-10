## About Mojio

[Mojio](https://www.moj.io/developer/) is an on-board diagnostics service in which you access to a great deal of data about the driver and their vehicle to create apps and services to improve the end user experience.Mojio has an open platform that is completely scalable and hardware agnostic, Mojio provides all the elements needed to launch your connected car service. And Mojio has many Apis that are sufficient to control the users, their vehicles, and the vehicle trips.

## Purpose of the scriptr.io connector for mojio
The purpose of this connector is to simplify and streamline the way you access mojio's APIs from scriptr.io, by providing you with a few native objects that you can directly integrate into your own scripts.This will hopefully allow you to create sophisticated applications.

## Components

- mojio/config: Script that contains authorizationUrl, accessTokenUrl, client_id, client_secret, addStateToRedirectUrl, grant_type, response_type, accessTokenFieldName, refreshTokenFieldName, scope, and redirect_uri.

- mojio/client : generic http client that handles the communication between scriptr.io and mojio's APIs.

- mojio/mojioManager : This object has methods that gets a specific mojio ```getMojio()```, update an existing mojio ```updateMojio()```, delete an existing mojio ```deleteMojio()```, get all mojios ```getAllMojios()```, claim a mojio ```claimMojio()```, update an existing Mojio's wifi settings ```updateMojioWiFiRadioSettings()```, get the status of a Mojio update request ```gettMojioTransactionStatus()```, and gets an individual mojio's capabilities ```gettMojioCapabilities()```.

- mojio/vehicleManager : This object has methods that gets activity settings for vehicle ```getVehicleSettings()```, update activity settings for vehicle ```updateVehicleSettings()```, save activity settings for vehicle ```saveVehicleSettings()```, get details about a specific vehicle ```getVehicleDetails()```, update an existing vehicle ```updateVehicle()```, delete an existing vehicle ```deleteVehicle()```, get all vehicles ```getAllVehicles()```, create a new vehicle ```createVehicle()```, get the current address of a vehicle ```getVehicleCurrentAddress()```, get all vehicle's trips ```getVehicleTrips()```, get a summary of a vehicle's trips ```getVehicleTripsSummary()```, get the vin details about the vehicle ```getVehicleVin()```, get the service schedule for the vehicle ```getVehicleServiceSchedule()```, get the next service schedule for the vehicle ```getVehicleNextServiceSchedule()```, get the current statistics for the vehicle ```getVehicleStats()```, get an aggregate of the Vehicle's data ```getVehicleAggregate()```, and update details for a specific DTC code on a vehicle ```updateVehicleDTCCodeDetails()```.

- mojio/userManager : This object has methods that gets current user details ```getMyDetails()```, get a specific user ```getUserDetails()```, update an existing user. ```updateUser()```, get all users ```GetAllUsers()```, get et all vehicles the user owns ```GetUserVehicles()```, get all mojios the user owns ```getUserMojios()```, get all trips the user owns ```getUserTrips()```, get all groups the user owns ```getUserGroups()```, get all of a user's phone numbers ```getUserPhoneNumbers()```, add a phone number to a user ```addPhoneNumber()```, get a user's phone number details ```getUserPhoneNumberDetails()```, create or update a phone number on a user ```updateUserPhoneNumber()```, delete a phone number from a user ```deleteUserPhoneNumber()```, get all of a user's emails ```getUserEmails()```, add an email to a user ```addUserEmail()```, get a user's email details ```getUserEmail()```, create or update a user's email ```updateUserEmail()```, and delete an email from a user ```deleteUserEmail()```.

- mojio/geofenceManager : This object has methods that create a new geofence ```createGeofence()```,get all geofences accessible to the current user ```getAllGeofences()```, get an individual geofence's details ```getGeofence()```, delete an existing geofence ```deleteGeofence()```, and update an existing geofence ```updateGeofence()```.

- mojio/tripManager : This object has methods that get a specific trip ```getTrip()```, get all trips ```getAllTrips()```, update an existing trip ```updateTrip()```, and delete an existing trip ```deleteTrip()```.

- mojio/appManager : This object has method that get the app information ```getApps()```.

- mojio/activityStreams : This object has method that get all activities related to resources of this type ```getActivities()```.

### How To Use
- Deploy the aforementioned scripts in your scriptr account, in a folder named "mojio",
- Create a developer account and an application at [mojio](https://www.moj.io/developer/),
- Once done, make sure to copy/paste the values of your application Key and application secret in the corresponding variables of the "mojio/config" file (respectively client_id and client_secret). Also replace"YOUR_AUTH_TOKEN" with your actual scriptr.io auth token in the "redirect_uri" variable.
- Create an end user (driver) account (https://accounts.moj.io/account/signin/mojio).
- Create a test script in scriptr, or use the script provided in mojio/test/. 

### Obtain end users access tokens from mojio

#### Step 1
From a front-end application, send a request to the ```/mojio/getRequestCodeUrl``` script, passing the ```username``` parameter. 
The username can be the actual end user's mojio username or another username he decides to use in your IoT application. 
The result returned by the aforementioned script should resemble the following:
```
>> curl -X POST  -F username=test1 -H 'Authorization: bearer VDYzRkExMkUwNTpzY3JpcHRyOjhGNDk3RERFNzk0OTk5Q0U3ODJCRTMxNkE4QUVCMTJG' 'https://api.scriptrapps.io/mojio/authorization/getRequestCodeUrl'
{
	"metadata": {
		"requestId": "458034fd-3b42-406e-88e3-554d70cf3ef9",
		"status": "success",
		"statusCode": "200"
	},
	"result": "https://accounts.moj.io/oauth2/authorize?client_id=a71c5fff-1231-4fce-9072-38a9fb6641c4&response_type=code&scope=full&state=87f9bb&redirect_uri=https%3A%2F%2Fapi.scriptr.io%2Fmojio%2Fauthorization%2FgetAccessToken%3Fauth_token%3DVDYzRkExMkUwNQ%3D%3D"
}
```

#### Step 2

From the front-end, issue a request to the URL obtained at step 1. This redirects your end user to the mojio's login page, 
where he has to enter his credentials then authorize the application on the requested scope. 
Once this is done, xee automatically calls back the ```mojio/getAccessToken``` script, providing it with an access and a refresh token that it stores in your scriptr.io's global storage. The tokens are also returned by the script.

### Use the connector

In order to use the connector, you need to import mojio module.

Then create a new instance of the classes, defined in this module (we assume that we already otbained an access token for the given user) example:

Mojio Trips:
```
try{
  //1-requiring tripManager object
  var tripManager = require("../tripManager");
  //2-creating a tripManager instance
  var tm = new tripManager.TripManager({"username":"test1"});
  //3-using one of tripManager Apis by calling them as a function.
  return tm.getAllTrips();
  catch(exception) {
  return exception;
}
```
Mojio Vehicles:
```
try{
  //1-requiring vehicleManager object
  var tripManager = require("../tripManager");
  //2-creating a vehicleManager instance
  var vm = new vehicleManager.VehicleManager({"username":"test1"});
  //3-using one of vehicleManager Apis by calling them as a function.
  return vm.getAllVehicles();
  catch(exception) {
  return exception;
}
```
Mojio Users:
```
try{
  //1-requiring userManager object
  var userManager = require("../userManager");
  //2-creating a userManager instance
  var um = new userManager.UserManager({"username":"test1"});
  //3-using one of userManager Apis by calling them as a function.
  var updateUserPhoneNumber = um.updateUserPhoneNumber("56a63a5c-bc75-4650-a9c0-b774342e7501","96176672736",{"sendVerification":"true","pin":"4431"});
  return um.getMyDetails();
  catch(exception) {
  return exception;
}
```
Mojio Mojios:
```
try{
  //1-requiring mojioManager object
  var mojioManager = require("../mojioManager");
  //2-creating a mojioManager instance
  var um = new mojioManager.MojioManager({"username":"test1"});
  //3-using one of mojioManager Apis by calling them as a function.
  return mm.getAllMojios();
  catch(exception) {
  return exception;
}
```
Mojio Geofences:
```
try{
  //1-requiring geofenceManager object
  var geofenceManager = require("../geofenceManager");
  //2-creating a geofenceManager instance
  var gm = new geofenceManager.GeofenceManager({"username":"test1"});
  //3-using one of geofenceManager Apis by calling them as a function.
  return gm.deleteGeofence("5a21b91d-6ef8-4f88-b48b-86b1107359fb");
  catch(exception) {
  return exception;
}
```
