# Deployment

- [Deployment](#deployment)
  - [Device Server and Shared Library Deployment](#device-server-and-shared-library-deployment)
    - [Simplified Deployment](#simplified-deployment)
    - [Dependencies](#dependencies)
    - [Configuration](#configuration)
      - [libhdbpp-timescale Shared Library](#libhdbpp-timescale-shared-library)
      - [hdbpp-health-check Device Server](#hdbpp-health-check-device-server)
  - [Service Deployment](#service-deployment)
    - [hdbpp-reorder-chunks (Required)](#hdbpp-reorder-chunks-required)
    - [hdbpp-ttl (Optional)](#hdbpp-ttl-optional)
    - [hdbpp-cluster-reporting (Optional)](#hdbpp-cluster-reporting-optional)

Deployment is composed of two phases, the binary components from the consolidated build (hdbpp-es etc) and the services.

## Device Server and Shared Library Deployment

Once built, all the Device Server and shared library binaries are available directly in the build directory. These must be installed as per the policy you have on site. At the most basic, they can be copied to /usr/local/bin or /usr/bin.

### Simplified Deployment

It is possible to build a single Event Subscriber or Config Manager binary without the need of the shared objects with this project. This is not possible when building the Event Subscriber or Config Manager project directly from its repo.

See the build guide for the required flags. Once built like this, the hdbpp_es/hdbpp_cm binary contains everything it needs to run.

Reference the build guide and look up the flags to bypass libhdbpp.

### Dependencies

The various components have the following system package dependencies:

- Tango Controls 9 or higher
- omniORB release 4 or higher
- libzmq3 or libzmq5
- libpq5

### Configuration

General hdb++ setup and configuration is outside the scope of this project. Please see the tango [readthedocs](https://tango-controls.readthedocs.io/en/latest/) project for general information.

#### libhdbpp-timescale Shared Library

The GitHub [README](https://github.com/tango-controls-hdbpp/libhdbpp-timescale) for this project details the configuration parameters that must be passed to the library from the hdbpp-es/hdbpp-cm Device Servers.

#### hdbpp-health-check Device Server

This Device Server is part of the hdbpp-timescale-project, and depends on the hdbpp-cluster-reporting Rest API. Once deployed, this Device Server requires several properties to be set correctly:

- RestAPIHost - The hostname of the server hosting the hdbpp-cluster-reporting Rest API.
- RestAPIPort - The port to access the hdbpp-cluster-reporting Rest API, default is 10666.
- RestAPIRootUrl - The root path of the endpoints. Default is /api/v1.

Without these, the hdbpp-health-check will not be able to connect and communicate with the Rest API.

Next enable the checks:

- EnableHostCheck - This will enable checking of the hosts and update the state of the hdbpp-health-check Device Server to Fault/Alarm + a message if it detects any errors.

## Service Deployment

At the minimum a production cluster needs to run the [hdbpp-reorder-chunks](../services/hdbpp-reorder-chunks) service to put the data tables in to the optimal order for querying.

Other services are optional.

### hdbpp-reorder-chunks (Required)

Follow the hdbpp-reorder-chunks [README](../services/hdbpp-reorder-chunks) for deployment. The recommended method is Docker deployment.

### hdbpp-ttl (Optional)

This is an optional service. Deploy this if you intend to use the Time To Live flag on attributes. This service will delete expired data when it is run.

Follow the hdbpp-ttl [README](../services/hdbpp-ttl) for deployment. The recommended method is Docker deployment.

### hdbpp-cluster-reporting (Optional)

This is an optional service. The [hdbpp-cluster-reporting](../services/hdbpp-cluster-reporting) service provides a Rest API that reports various hdb++ health metrics from the cluster. Its an important optional deployment for helping monitor and maintain the hdb++ TimescaleDb cluster.

Follow the hdbpp-cluster-reporting [README](../services/hdbpp-cluster-reporting) for deployment. The recommended method is Docker deployment.

A browser based front end is under development.
