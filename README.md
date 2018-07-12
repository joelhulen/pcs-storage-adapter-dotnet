[![Build][build-badge]][build-url]
[![Issues][issues-badge]][issues-url]
[![Gitter][gitter-badge]][gitter-url]

Storage Adapter Overview
========================
This service allows management of of key-values, abstracting the storage service semantic and presenting a simple interface to store data of any format using [Azure Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/).

Values are organized in collections, it is possible to work on individual values and to fetch entire collections. Complex data structures are serialized by the clients and managed as simple text payload.

The service provides a RESTful endpoint to Create-Read-Update-Delete values, where each value has a unique Id referenced also as "Key", supporting concurrency.

IDs can be provided by the client or automatically generated by the service (i.e. Guids).

Dependencies
============
* [Azure Cosmos DB account](https://ms.portal.azure.com/#create/Microsoft.DocumentDB) with API type DocumentDB

How to use the microservice
===========================
## Quickstart - Running the service with Docker

1. Install Docker Compose: https://docs.docker.com/compose/install
1. Create your [Azure Cosmos DB account](https://ms.portal.azure.com/#create/Microsoft.DocumentDB) with API type **SQL**
1. Change the default consistency in your Azure Cosmos DB account to **STRONG**
1. Find your Azure Cosmos DB account connection string.  See [Update your Connection Strings](https://docs.microsoft.com/en-us/azure/cosmos-db/create-documentdb-dotnet#update-your-connection-string) 
if you need help finding it.
1. Store the "Azure Cosmos DB account connection string" in the [env-vars-setup](scripts)
   script, then run the script.
1. Run the Storage Adapter service using docker compose [docker-compose up](scripts)
1. Use an HTTP client such as [Postman](https://www.getpostman.com),
   to exercise the 
   [RESTful API](https://github.com/Azure/pcs-storage-adapter-dotnet/wiki/API-specs#create-key-value-with-post-id-generated-by-the-service)
   to create a collection.

## Running the service with Visual Studio or VS Code

The service can be started from any C# IDE and from the command line.
The only difference you might notice, is how environment variables
are configured. See the [Configuration and Environment variables](#configuration-and-environment-variables) documentation below for more information.

1. Create your [Azure Cosmos DB account](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
   with API type **SQL** and default consistency level **STRONG**
1. [Install .NET Core 2.x][dotnet-install]
1. Install any recent edition of Visual Studio (Windows/MacOS) or Visual Studio Code (Windows/MacOS/Linux).
    a. If you already have Visual Studio installed, then ensure you have
       [.NET Core Tools for Visual Studio 2017](https://www.microsoft.com/net/core#windowsvs2017) installed.
1. Open the solution in Visual Studio or VS Code
1. Define the following environment variables. See
   [Configuration and Environment variables](#configuration-and-environment-variables)
   for detailed information for setting these for your enviroment.
    * `PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING` = {your DocumentDB connection string}
1. Start the WebService project (e.g. press F5)
1. Using an HTTP client like [Postman](https://www.getpostman.com),
   use the
   [RESTful API](https://github.com/Azure/pcs-storage-adapter-dotnet/wiki/API-specs)
   to create a simulation.

## Running the service with IntelliJ Rider
1. Open the solution using the `pcs-storage-adapter.sln` file.
1. When the solution is loaded, go to `Run -> Edit Configurations` and
   create a new `.NET Project` configuration.
1. In the configuration select the WebService project
1. Add a new environment variable with name
   `PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING` storing your DocumentDB connection string.
1. Save the settings and run the configuration just created, from the IDE
   toolbar.
1. You should see the service bootstrap messages in IntelliJ Run window,
   with details such as the URL where the web service is running, plus
   the service logs.

## Project Structure

This microservice contains the following projects:
* **WebService.csproj** - C# web service exposing REST interface for storage
functionality
* **WebService.Test.csproj** - Unit tests for web services functionality
* **Services.csproj** - C# assembly containining business logic for interacting 
with Azure Cosmoze account with type DocumentDb
* **Services.Test.csproj** - Unit tests for services functionality
* **Solution/scripts** - contains build scripts, docker container creation scripts, 
and scripts for running the microservice from the command line


## Build and Run from the command line

The [scripts](scripts) folder contains scripts for many frequent tasks:

* `build`: compile all the projects and run the tests.
* `compile`: compile all the projects.
* `run`: compile the projects and run the service. This will prompt for
  elevated privileges in Windows to run the web service.

## Updating the Docker image

The `scripts` folder includes a [docker](scripts/docker) subfolder with the files
required to package the service into a Docker image:

* `Dockerfile`: docker images specifications
* `build`: build a Docker container and store the image in the local registry
* `run`: run the Docker container from the image stored in the local registry
* `content`: a folder with files copied into the image, including the entry point script

You can also start Device Simulation and its dependencies in one simple step,
using Docker Compose with the
[docker-compose.yml](scripts/docker/docker-compose.yml) file in the project:

```
cd scripts/docker
docker-compose up
```

The Docker compose configuration requires the IoTHub and StorageAdapter web serviceURL environment variables, described previously.

## Configuration and Environment variables

The service configuration is stored using ASP.NET Core configuration
adapters, in [appsettings.ini](WebService/appsettings.ini). The INI
format allows to store values in a readable format, with comments.
The application also supports inserting environment variables, such as
credentials and networking details.

The configuration file in the repository references some environment
variables that need to created at least once. Depending on your OS and
the IDE, there are several ways to manage environment variables:

1. If you're using Visual Studio or Visual Studio for Mac, the environment
   variables are loaded from the project settings. Right click on WebService,
   and select Options/Properties, and find the section with the list of env
   vars. See [WebService/Properties/launchSettings.json](WebService/Properties/launchSettings.json).
1. Visual Studio Code loads the environment variables from
   [.vscode/launch.json](.vscode/launch.json)
1. When running the service **with Docker** or **from the command line**, the
   application will inherit environment variables values from the system. 
   * [This page][windows-envvars-howto-url] describes how to setup env vars
     in Windows. We suggest to edit and execute once the
     [env-vars-setup.cmd](scripts/env-vars-setup.cmd) script included in the
     repository. The settings will persist across terminal sessions and reboots.
   * For Linux and MacOS, we suggest to edit and execute
     [env-vars-setup](scripts/env-vars-setup) each time, before starting the
     service. Depending on OS and terminal, there are ways to persist values
     globally, for more information these pages should help:
     * https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
     * https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
     * https://help.ubuntu.com/community/EnvironmentVariables
1. IntelliJ Rider: env. vars can be set in each Run Configuration, similarly to
  IntelliJ IDEA (https://www.jetbrains.com/help/idea/run-debug-configuration-application.html)

Contributing to the solution
============================
Please follow our [contribution guildelines](CONTRIBUTING.md).  We love PRs too.

Troubleshooting
===============

Feedback
========
Please enter issues, bugs, or suggestions as GitHub Issues here: https://github.com/Azure/pcs-storage-adapter-dotnet/issues.

[build-badge]: https://img.shields.io/travis/Azure/pcs-storage-adapter-dotnet.svg
[build-url]: https://travis-ci.org/Azure/pcs-storage-adapter-dotnet
[issues-badge]: https://img.shields.io/github/issues/azure/pcs-storage-adapter-dotnet.svg
[issues-url]: https://github.com/azure/pcs-storage-adapter-dotnet/issues
[gitter-badge]: https://img.shields.io/gitter/room/azure/iot-solutions.js.svg
[gitter-url]: https://gitter.im/azure/iot-solutions
