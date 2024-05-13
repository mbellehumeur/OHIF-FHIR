# OHIF-FHIR integration extension
- [OHIF-FHIR integration extension](#ohif-fhir-integration-extension)
  - [Introduction](#introduction)
  - [SMART on FHIR launch](#smart-on-fhir-launch)
  - [SMART Imaging Access](#smart-imaging-access)
  - [FHIRcast](#fhircast)
    - [Test side panel](#test-side-panel)
    - [How to use the extension in your FHIRcast integration](#how-to-use-the-extension-in-your-fhircast-integration)
      - [Installation](#installation)
      - [Subscribe to the hub](#subscribe-to-the-hub)
      - [Send an event to the hub](#send-an-event-to-the-hub)
      - [Receive events](#receive-events)
      - [Get Context](#get-context)
    - [PowerCast connector](#powercast-connector)
      - [Configuration](#configuration)
      - [Side panel](#side-panel)
    - [Example integration](#example-integration)
      - [Patient-open](#patient-open)
      - [ImagingStudy-open](#imagingstudy-open)

## Introduction
<div>

  <p><strong>The OHIF Viewer</strong> is a zero-footprint medical image viewer
provided by the <a href="https://ohif.org/">Open Health Imaging Foundation (OHIF)</a>. It is a configurable and extensible progressive web application with out-of-the-box support for image archives which support <a href="https://www.dicomstandard.org/using/dicomweb/">DICOMweb</a>.</p>
</div>
This extension is a collection of  FHIR integration clients and components such as the SMART on FHIR launch with patient context, SMART Imaging Access and FHIRcast.


## SMART on FHIR launch

The SMART on FHIR application programming interface (API) which enables an app written once to run anywhere in the healthcare system.  It describes a set of foundational patterns based on OAuth 2.0 for client applications to authorize, authenticate, and integrate with FHIR-based data systems.

 

The configuration is set in the config file in the 'smart:' section and contains the authorization and token endpoints.  
The launch can be configured to automatically subscribe to a FHIRcast hub and provide context to launch automatically a study or the patient study list.
```typescript
smart: [
  {
    name:'SMART',
    friendlyName:'SMART Health IT launch',
    enabled:true,
    issuer: "https://launch.smarthealthit.org/v/r4/fhir",
    jwks_uri: "https://launch.smarthealthit.org/keys",
    authorization_endpoint: "https://launch.smarthealthit.org/v/r4/auth/authorize",
    token_endpoint: "https://launch.smarthealthit.org/v/r4/auth/token",
    redirect_uri: '/callback',
    response_type: 'id_token token',
    scopes_supported: ["launch","launch/patient","launch/encounter","patient/*.*","user/*.*"],
    // ~ OPTIONAL
    fhircastSubscribe: true,
    fhircastHubNames: ["TEST"],
},
  ],
```

The .env file defines the application identifiers:
```env
REACT_APP_NAME_CLIENT_ID: '723928408739-k9k9r3i44j32rhu69vlnibipmmk9i57p',
REACT_APP_NAME_CLIENT_SECRET: 'client_secret',
```
where 'NAME' matches name of the entry in the configuration file.


The [SMART on FHIR](https://dev.smarthealthit.org/)® launch scenario can provide patient and encounter context and the topic to a FHIRcast session on start-up.  


Alternatively, test the SMART launch by navigating to the SMART launch sandbox: http://launch.smarthealthit.org and selecting a patient, provider and the app url which can be a local instance in debug mode.


## SMART Imaging Access

This is a client for the SMART Imaging project.  It allows the OHIF study list to use a FHIR server that has an 'ImagingStudy' endpoint as a datasource. 

The SMART Imaging project aims to provide a unified solution for accessing imaging studies alongside clinical data using a single authorization flow. This enables patients to have better access to their data, facilitates second opinions, streamlines data donations for research, and supports providers in their analysis with preferred tools and specialty-specific viewers


## FHIRcast
FHIRcast synchronizes healthcare applications in real time to show the same clinical content to a common user. It also enables sharing FHIR resources between all the  applications of a workflow even when running on a different computer.  Health IT users often work in many disparate applications at the same time (worklist, PACS,dictation,EMR, AI, ect).   The FHIRcast standard will help lower cost and accelerate real-time health IT  integration across desktop and mobile applications. Find out more at [fhircast.org](http://fhircast.org), [FHIR implementation guide](https://hl7.org/fhir/uv/fhircast/2024May/), [IHE Integrated Reporting Application](https://profiles.ihe.net/RAD/IRA/index.html) and [FHIRcast Javascript sandbox](https://github.com/fhircast/sandbox.js).

The extension allows publishing FHIR resources such as measurements, annotations (observations) and imaging selections to FHIRcast hubs.  It can also receive events from the hubs on websockets.

The extension includes a viewer side panel for troubleshooting FHIRcast connections and workflows.  
Hubs are configured in the config file in a fhircast section:
```typescript
fhircast: [
  {
    name:'TEST',
    friendlyName:'JavaScript Sandbox',
    enabled:true,
    events: ['open-patient-chart','close-patient-chart'],
    lease:999,
    hub_root:'http://localhost:5000',
    hub_endpoint: 'http://localhost:5000/api/hub',
    authorization_endpoint: 'http://localhost:5000/oauth/authorize',
    token_endpoint: 'http://localhost:5000/oauth/token',
  },
]
```
More than one hub can be configured and used by the viewer. 

The .env file defines the application identifiers:
```env
REACT_APP_NAME_CLIENT_ID: '723928408739-k9k9r3i44j32rhu69vlnibipmmk9i57p',
REACT_APP_NAME_CLIENT_SECRET: 'client_secret',
```
where 'NAME' matches name of the entry in the configuration file.


### Test side panel

The first step is to subscribe to the hub for a specific topic.  A FHIRcast topic is defined by the hub and is normally  a user identifier or a user session identifier. If you open multiple test clients using the same topic, you will see message transfers between them.   

 ![sidepanel](/images/fhircast-side-panel.png)

 

 Once the subscription is successful, the other elements of the panel are enabled.  
 The 'Get context' button queries the hub for the current context.  A typical use case is to query the hub after start-up and redirect the viewer to the current study automatically.

 The 'Publish' button is enabled after you select an event to send fom the drop down list.  
 The default events in the list are the IHE connectathon transactions required for the [IHE Integrated Reporting Application](https://profiles.ihe.net/RAD/IRA/index.html) profile.  
 The publish button will send to the hub and the response will be displayed in the text area below. 

### How to use the extension in your FHIRcast integration 

#### Installation
The side panel is named FhircastPanel.  It can be added to OHIF modes like other extensions in the routes section of the mode index file.  For example, to add to the left panel:

             leftPanels: ['fhir.panelModule.FhircastPanel']

#### Subscribe to the hub
```typescript
const hubSubscribeResponse = await fhircastSubscribe(hub,topic,fhircastCallback);   
    
```
'fhircastCallback' is a function called when receiving an event from the hub.

#### Send an event to the hub
```typescript
const hubPublishResponse = await fhircastPublish(hub,topic,fhircastMessage);
```
#### Receive events
Implement the callback function fhircastCallback:

```typescript
const fhircastCallback(event) {
if (event.event-type==='patient-open') {
    // Do something here
    }
}
```
#### Get Context
```typescript
const hubContextResponse = await fhircastGetContext(hub,topic);

```
The response in this case is a JSON object containing the context information.

### PowerCast connector
The PowerCast connector is  a utility of the Nuance PsOne reporting client.  It runs on Windows PC and provides a local endpoint to discover, authenticate and get a topic for their FHIRcast hub.  It also allows launching the PsOne client. Specifications here: 
[Nuance Powercast](https://connect2.nuancepowerscribe.com/psonesetup/PO-PowerCastIntegrationGuide.pdf).

#### Configuration
A 'powercastConnector' section in the configuration file defines the endpoints and ids for the connector:
```typescript
powercastConnector: [
  {
    name:'POWERCASTCONNECTOR',
    friendlyName:'Nuance PowerCast connector',
    enabled:true,
    connector_config_endpoint: 'http://localhost:9292/configuration',
    connector_login_endpoint: 'http://localhost:9292/login',
    // ~ OPTIONAL
    fhircastSubscribe: true,
    fhircastHubNames: ["POWERCAST"],
  },
]
```
The .env file defines the application identifiers:
```env
REACT_APP_NAME_CLIENT_ID: '723928408739-k9k9r3i44j32rhu69vlnibipmmk9i57p',
REACT_APP_NAME_CLIENT_SECRET: 'client_secret',
```
where 'NAME' matches name of the entry in the configuration file.

Below is an example response from the configuration endpoint:
```typescript
GET /configuration HTTP/1.1
Host: localhost:9292 HTTP/1.1 200 OK
Content-Type: application/json
{
  "authorization_endpoint": "https://nuance-auth0-server/oauth/authorize",
  "token_endpoint":         "https://nuance-auth0-server/oauth/token",
  "hub_endpoint":           "https://connect.nuance-powerscribe-server/powercast/api/hub",
  "topic" : ""
}
```
After the configuration is retrieved, the viewer is authorized to a Auth0 endpoint and obtains a bearer token to communicate with the FHIRcast hub.  The topic is only provided is someone is logged in the PsOne client.  Login can be automated with the bearer token and the connector login endpoint:

```typescript
GET/login?username=drXRay HTTP/1.1
Authorization Bearer oiwerualskjf34zx897ldh394...
Host: https://localhost:9292
HTTP/1.1 200 OK
Content-Type: application/json
{
  "authorization_endpoint": "https://nuance-auth0-server/oauth/authorize",
  "token_endpoint":         "https://nuance-auth0-server/oauth/token",
  "hub_endpoint":           "https://connect.nuance-powerscribe-server/powercast/api/hub",
  "topic", "B3668641BF717FF17B39F3A2B48C5.drXRay"
}
```
When a topic is obtained, subscription to the FHIRcast hub can be automated if enabled in the connector configuraiton settings.
The PowerCast subscription response contains the current context therefore a 'get-context' after subscription can be skipped in this case.

#### Side panel

### Example integration

#### Patient-open 
Adds "/?mrn=<patient id>" to the URL of the viewer.  This limits the study list to the requested patient. 

#### ImagingStudy-open 
Open the study normaly.






