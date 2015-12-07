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

```shell
curl -X PUT -H "Content-type: application/json" --data-binary '{"userName":"user", "password":"hi"}' "https://<hostname>/concerto/services/rest/RepositoryService/v1/Tokens"
```
```json
{"token":"abc123"}
```


### HTTP Request 

`GET https://<hostname>/concerto/services/rest/RepositoryService/v1/Tokens`

JSON Payload:
`{"userName:"user", "password":"hi"}`

Headers:
`-H Content-type: application/json`

<aside class="notice">If your CloudTest instance uses a Self Signed certificate, you will need to request a token without strict ssl set. For curl this is -k</aside>

## Authentication Header
The following header must be included in all of your requests to the CloudSerivce API. 

`X-Auth-Token:1234-ABC`

<aside class="notice">Make sure to replace `1234-ABC` with your Token.</aside>

#Scommand 

## Options
Parameter | Format | Default | Description |
--------- | ------- | --------- |----------- |
name | String | None | This is the name of the Object (Grid/RSDB, etc) you wish to interact with.
wait | Boolean | True | This command will tell SCommand if it should wait until the Object you are interacting with is in a 'Ready' status. 
timeout | Integer | 600 | The time in seconds you want SCommand to wait for the Object you are interacting with.
terminateOnFailure | Boolean | True | If the Object you are interacting with exhausts (timeout), then will tell Scommand if it should automatically terminate the failed Object
strictSSL | Boolean | False | set SCommand to enforce strict SSL
printStatus | Boolean | True | Toggles SCommmand's ability to print status updates to console

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

Parameter | Description
--------- | -----------
ID | ID of the Grid

## Get Grid Instance

```shell
curl -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/grid/<id>/instances/<instanceID>
```

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

### States

State | Description | Final State
--------- | ----------- | ---------
LOADED |  The grid has been loaded and is ready to start. | T
ALLOCATING |  The grid is allocating servers.  | F
ALLOCATED | The grid has finished allocating servers, with no failures. | F
ALLOCATED_WITH_FAILURES  | The grid has finished allocating servers, with some failures. | T
ALLOCATE_FAILED | The grid failed to allocate any servers. | T
CHECKING  | The grid is in the process of performing server health checks. | F
CHECKED | The grid has finished performing server health checks, with no failures. | T
CHECKED_AND_TERMINATING | The allocated servers are checked and the pending server are terminating. | F
CHECKED_AND_DEPLOYING | The allocated servers are checked and the pending servers are still deploying. | F
CHECKED_WITH_FAILURES | The grid has finished performing server health checks, with some failures. | T
STARTING_MONITORS | The grid is in the process of setting up monitoring for the allocated servers. | F
CANCELLING | The grid is in the process of cancelling. | F
CANCELLED | The grid has been cancelled. | T
TERMINATING | The grid is in the process of terminating. | F
TERMINATED | The grid has been terminated. | T
TERMINATED_WITH_FAILURES | The grid has been terminated, with some failures. | T



## List All Grid Instances

```shell 
curl -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/grid/instances/`
```


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


This endpoint terminates a grid


### HTTP Request

`DELETE http://<hostname>/concerto/services/rest/CloudService/v1/grid/instances/<instanceID>`

### Query Parameters
Parameter | Description
--------- | -----------
InstanceID | ID of the grid instance (From POST Call)


# Results Databases (RSDB)

In order to interact with a result database you will need to know the ID, which is retrived via the [Repository API](http://cdn.soasta.com/productresource/api/repository_api/)


## Get RSDB State

Returns the State of the RSDB

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/
```

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
```
 
```ruby
scommand start-rsdb url=https://<hostname>/concerto username=user password=password name="my rsdb"
```


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

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" http://<hostname>/concerto/services/rest/CloudService/v1/rsdb/<id>/state/terminate
```

```ruby
scommand terminate-rsdb url=https://<hostname>/concerto username=user password=password name="my rsdb"
```


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

In order to interact with a result database you will need to know the ID, which is retrived via the [Repository API](http://cdn.soasta.com/productresource/api/repository_api/)

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

### Test Environment States


State | Description | Final State
--------- | ----------- | --------
ERROR_BUILD_MISMATCH  | Extracted build does not match the environment's build setting | T
TERMINATED_WITH_FAILURES |  Servers encountered failures when stopping | T
CANCELLING | Canceling environment servers while launch is in progress | F
CANCELLED | Environment servers have been canceled while launch is in progress | T
LOADED | The environment is loaded. | T
ALLOCATING_DATABASE | The environment is allocating a database server | F
ALLOCATED_DATABASE | The environment has allocated a database server | F
CHECKED_DATABASE | The environment has checked the database server | F
ALLOCATING_MAIN | The environment is allocating a main server | F
ALLOCATED_MAIN | The environment has allocated a main server | F
CHECKED_MAIN | The environment has checked the main server | F
RUNNING | The environment is running. | T
TERMINATED | The environment is stopped. | T
TERMINATING | The environment is stopping. | F
PROCESS_FAILED | Unable to talk to the resultsservice. | T
VOLUME_NOT_FORMATTED | The database volume needs to be formatted. | T
VOLUME_NOT_AVAILABLE | The volume is not available | F
EXTERNALLY_TERMINATED | Instance was terminated by external means. | T


## Start Test Environment

```shell
curl -X POST -H "X-Auth-Token:1234-ABC" https://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/start
```

```ruby
scommand start-env url=https://<hostname>/concerto username=user password=password name="my env"
```

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

`PUT: http://cloudtestmanager.soasta.com/concerto/services/rest/CloudService/v1/environment/<id>/state/<state>`

### Query Parameters
Parameter | Description
--------- | -----------
ID | ID of the Test Environment
state | The State you wish to set the Test Environment (Accepted values: start, stop, terminate)




