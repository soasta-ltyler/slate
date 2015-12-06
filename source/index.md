---
title: SOASTA CloudTest CloudService API Documenation

language_tabs:
  - shell
  - SCommand

toc_footers:
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

This document describes how to use the CloudService API. 

# Authentication

## Getting a Token
> To authorize, You will need to request a Security Token from CloudTest:

```shell
curl -X PUT -H "Content-type: application/json" --data-binary '{"userName":"user", "password":"hi"}' "https://<hostname>/concerto/services/rest/RepositoryService/v1/Tokens"
```
### HTTP Request 

`GET https://<hostname>/concerto/services/rest/RepositoryService/v1/Tokens`

## Authentication Header
Add the following header to all of your requests
`X-Auth-Token:1234-ABC`

> Make sure to replace `1234-ABC` with your Token.

#Scommand 
## Options
`name (STRING)`  This is the name of the Object (Grid/RSDB, etc) you wish to interact with.
`wait (BOOLEAN)` This command will tell SCommand if it should wait until the Object you are interacting with is in a 'Ready' status. For Grids, this means that the Grid has been ALLOCATED and CHECKED. For TestEnvironments this means that both of the Test Environment servers have been ALLOCATED and CHECKED.
`timeout (INTEGER)` The time in seconds you want SCommand to wait for the Object you are interacting with. Default (600 seconds)
`terminateOnFailure (BOOLEAN)`   If the Object you are interacting with exhausts (timeout), then will tell Scommand if it should automatically terminate the failed gridDefault (true)
`strictSSL=BOOLEAN(true/false)`  Default: false : set SCommand to enforce strict SSL
`printStatus=BOOLEAN(true/false)` Default: true  : Toggles SCommmand's ability to print status updates to console


Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

# Grids

In order to interact with a grid you will need to know the ID, which is retrived via the Repository API (insert link to that here). 
For SCommand we will make that request for you, so you will only need to specify the name. 

## Start Grid

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/grid/<id>/intances
```

```ruby
scommand start-grid url=https://<hostname>/concerto username=user password=password name="ec2 grid"
```

> The above command returns JSON structured like this:

```json
{
"instanceID": "4cc3d2ca7abfc560:-3ca6245e:15163e5c497:-729c",
"numberAllocatedCollectors": 0,
"numberAllocatedLoadGeneratorServers": 0,
"numberAllocatedResultsServers": 0,
"numberCheckedCollectors": 0,
"numberCheckedLoadGeneratorServers": 0,
"numberCheckedResultsServers": 0,
"state": "ALLOCATING",
"time": "12/2/15 2:57 PM"
}
```

Starts the grid. 

<aside class="notice">
At this time you can't create Grids via the API. You will need to set the grid up via the UI first. 
</aside>

### HTTP Request

`POST: http://<hostname>/concerto/services/rest/CloudService/v1/grid/<id>/intances`

## Get Grid Instance

```shell
curl -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/grid/<id>/instances/<instanceID>
```

> The above command returns JSON structured like this:

```json
{
"id": "1416",
"instanceID": "4cc3d2ca7abfc560:-3ca6245e:15164d42ae6:-5f2a",
"numberAllocatedCollectors": 0,
"numberAllocatedLoadGeneratorServers": 1,
"numberAllocatedResultsServers": 0,
"numberCheckedCollectors": 0,
"numberCheckedLoadGeneratorServers": 0,
"numberCheckedResultsServers": 0,
"state": "CHECKING",
"time": "12/2/15 3:07 PM"
}
```

This endpoint retrieves a specific Grid.


### HTTP Request

`GET http://<hostname>/concerto/services/rest/CloudService/v1/grid/<id>/instances/<instanceID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | ID of the Grid
InstanceID | ID of the grid instance (From POST Call)


## List All Grid Instances

```shell 
curl -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/grid/instances/`
```

> The above command returns JSON structured like this:

```json
[
{
  "id": "1416",
  "instanceID": "4cc3d2ca7abfc560:-3ca6245e:15164d42ae6:-5f2a",
  "numberAllocatedCollectors": 0,
  "numberAllocatedLoadGeneratorServers": 0,
  "numberAllocatedResultsServers": 0,
  "numberCheckedCollectors": 0,
  "numberCheckedLoadGeneratorServers": 0,
  "numberCheckedResultsServers": 0,
  "state": "TERMINATED",
  "time": "12/2/15 2:55 PM"
}
]
```

This endpoint retrieves a list of started Grids 


### HTTP Request

`GET http://<hostname>/concerto/services/rest/CloudService/v1/grid/instances/`

## Terminate Grid

```shell
curl -X DELETE -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/grid/instances/<instanceID>
```

```ruby
scommand terminate-grid url=https://<hostname>/concerto username=user password=password name="ec2 grid"
```
> The above command does not return any JSON


This endpoint terminates a grid


### HTTP Request

`DELETE http://<hostname>/concerto/services/rest/CloudService/v1/grid/instances/<instanceID>`

### Query Parameters
Parameter | Description
--------- | -----------
InstanceID | ID of the grid instance (From POST Call)


# Results Databases (RSDB)

In order to interact with a result database you will need to know the ID, which is retrived via the Repository API (insert link to that here). 


## Get RSDB State

Returns the State of the RSDB

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/
```

> The above command returns JSON structured like this:

```json
{
"state": "RUNNING",
"time": "12/2/15 3:13 PM"
}
```

Gets the RSDB State. 

<aside class="notice">
At this time you can't create RSDBs via the API. You will need to set the RSDB up via the UI first. 
</aside>

### HTTP Request

`GET: http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/<state>`

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the RSDB

## Start RSDB

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/start

 
```ruby
scommand start-rsdb url=https://<hostname>/concerto username=user password=password name="my rsdb"
```

> The above command returns JSON structured like this:

```json
{
"state": "ALLOCATING",
"time": "12/2/15 3:13 PM"
}
```

Starts the RSDB. 

<aside class="notice">
At this time you can't create RSDBs via the API. You will need to set the RSDB up via the UI first. 
</aside>

### HTTP Request

`PUT: http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/<state>`

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the RSDB
state | The State you wish to set the RSDB (Accepted values: start, stop, terminate)


## Terminate RSDB

```Curl 
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/terminate
```

```ruby
scommand terminate-rsdb url=https://<hostname>/concerto username=user password=password name="my rsdb"
```

> The above command returns JSON structured like this:

```json
{
"state": "TERMINATING",
"time": "12/2/15 3:13 PM"
}
```

Terminates the RSDB. 

<aside class="notice">
If the RSDB is allocating this method will stop the RSDB deployment, then terminate it.  
</aside>

### HTTP Request

`PUT: http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/<state>`

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the RSDB
state | The State you wish to set the RSDB (Accepted values: start, stop, terminate)


# Test Environment 

In order to interact with a result database you will need to know the ID, which is retrived via the Repository API (insert link to that here). 

## Get Test Environment State

Returns the State of the RSDB

```shell 
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/environment/<id>/state/
```

> The above command returns JSON structured like this:

```json
{
"state": "RUNNING",
"time": "12/2/15 3:13 PM"
}
```

Gets the Test Environment State. 

<aside class="notice">
At this time you can't create Test Environment via the API. You will need to set the Test Environment up via the UI first. 
</aside>

### HTTP Request

`GET: http://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/`

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the RSDB



## Start Test Environment

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" https://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/start
```

```ruby
scommand start-env url=https://<hostname>/concerto username=user password=password name="my env"
```

> The above command returns JSON structured like this:

```json
{
"state": "ALLOCATING",
"time": "12/2/15 3:13 PM"
}
```

Starts the Test Environment. 

<aside class="notice">
At this time you can't create Test Environment via the API. You will need to set the Test Environment up via the UI on CloudTest Manager first. 
</aside>

### HTTP Request

`PUT: https://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/<state>`

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the Test Environment
state | The State you wish to set the Test Environment (Accepted values: start, stop, terminate)


## Terminate Test Environment

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" https://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/terminate
```

```ruby
scommand terminate-env url=https://cloudtestmanager.soasta.com/concerto username=user password=password name="my env"

```

> The above command returns JSON structured like this:

```json
{
"state": "TERMINATING",
"time": "12/2/15 3:13 PM"
}
```

Terminates the Test Environment. 

<aside class="notice">
If the Test Environmentis allocating this method will stop the Test Environment deployment, then terminate it.  
</aside>

### HTTP Request

`PUT: http://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/<state>'

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the Test Environment
state | The State you wish to set the Test Environment (Accepted values: start, stop, terminate)




