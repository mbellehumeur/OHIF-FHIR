# OHIF-FHIR integration extension
- [OHIF-FHIR integration extension](#ohif-fhir-integration-extension)
  - [Introduction](#introduction)
  - [SMART on FHIR launch](#smart-on-fhir-launch)
    - [Test side panel](#test-side-panel)
    - [How to use](#how-to-use)
  - [SMART Imaging Access](#smart-imaging-access)
    - [Test side panel](#test-side-panel-1)
    - [How to use](#how-to-use-1)
  - [FHIRcast](#fhircast)
    - [FHIRcast Service](#fhircast-service)
      - [Overview](#overview)
      - [Events](#events)
      - [API](#api)
    - [Test side panel](#test-side-panel-2)
    - [FHIRcast conferencing and context sharing](#fhircast-conferencing-and-context-sharing)
    - [How to use](#how-to-use-2)
      - [Installation](#installation)
      - [Auto-reconnect on websocket close](#auto-reconnect-on-websocket-close)
    - [Medplum hub](#medplum-hub)
    - [Philips Software hub](#philips-software-hub)
    - [Siemens Healthineers hub](#siemens-healthineers-hub)
    - [Nuance Powerscribe hub](#nuance-powerscribe-hub)
      - [PowerCast connector](#powercast-connector)
        - [Configuration](#configuration)
        - [Test side panel](#test-side-panel-3)
    - [Example integration](#example-integration)
      - [Patient-open](#patient-open)
      - [ImagingStudy-open](#imagingstudy-open)
      - [DiagnosticReport-open](#diagnosticreport-open)

## Introduction
<div>

<p><strong>The OHIF Viewer</strong> is a zero-footprint medical image viewer
provided by the <a href="https://ohif.org/">Open Health Imaging Foundation (OHIF)</a>. It is a configurable and extensible progressive web application with out-of-the-box support for image archives which support <a href="https://www.dicomstandard.org/using/dicomweb/">DICOMweb</a>.</p>
</div>
This extension is a collection of client libraries, services and test tools for FHIR based integrations such as SMART on FHIR® launch with patient context, SMART Imaging Access and FHIRcast.  They aim to facilitate FHIR based workflows and data exchange.  They could be useful for IHE/FHIR connectathons and integration demonstrations such as the RSNA Imaging AI in Practice (IAIP) demo.



## SMART on FHIR launch

The SMART on FHIR application programming interface (API) enables an app written once to run anywhere in the healthcare system.  It describes a set of foundational patterns based on OAuth 2.0 for client applications to authorize, authenticate, and integrate with FHIR-based data systems.

 

The configuration is set  in a 'smart:' section of the config file. 
The configuration elements described so forth can be found in the example file config_fhir.js.

The launch can be configured to automatically subscribe to a FHIRcast hub and provide context to launch a study or the patient study list.
```typescript
smart: [
  {
    name:'SMART',
    friendlyName:'SMART Health IT launch',
    enabled:true,
    issuer: 'https://launch.smarthealthit.org/v/r4/fhir',
    jwks_uri: 'https://launch.smarthealthit.org/keys',
    authorization_endpoint: 'https://launch.smarthealthit.org/v/r4/auth/authorize',
    token_endpoint: 'https://launch.smarthealthit.org/v/r4/auth/token',
    redirect_uri: '/callback',
    response_type: 'id_token token',
    scopes_supported: ['launch','launch/patient','launch/encounter','patient/*.*','user/*.*'],
    // ~ OPTIONAL
    fhircastSubscribe: true,
    fhircastHubNames: ['TEST'],
},
  ],
```

The .env file defines the application identifiers:
```env
REACT_APP_NAME_CLIENT_ID=723928408739-k9k9r3i44j32rhu69vlnibipmmk9i57p
REACT_APP_NAME_CLIENT_SECRET=client_secret
```
where 'NAME' matches name of the entry in the configuration file.


The [SMART on FHIR](https://dev.smarthealthit.org/)® launch scenario can provide patient and encounter context and the topic to a FHIRcast session on start-up.  


Alternatively, test the SMART launch by navigating to the SMART launch sandbox: http://launch.smarthealthit.org and selecting a patient, provider and the app url which can be a local instance in debug mode.

### Test side panel
### How to use 


## SMART Imaging Access

This is a client for the SMART Imaging project.  
It allows to populate the OHIF study list from two  FHIR servers:  the patient data from a clinical FHIR server and the ImagingStudy resources from  a FHIR server dedicated to imaging.  The images themselves reside on a DICOM server. 

https://github.com/sync-for-science/imaging

The SMART Imaging project aims to provide a unified solution for accessing imaging studies alongside clinical data using a single authorization flow. This enables patients to have better access to their data, facilitates second opinions, streamlines data donations for research, and supports providers in their analysis with preferred tools and specialty-specific viewers.



### Test side panel
### How to use 

## FHIRcast
FHIRcast synchronizes healthcare applications in real time to show the same clinical content to a common user. It also enables sharing FHIR resources between all the  applications of a workflow even when running on a different computer.  Health IT users often work in many disparate applications at the same time (worklist, PACS,dictation,EMR, AI, ect).   The FHIRcast standard will help lower cost and accelerate real-time health IT  integration across desktop and mobile applications. Find out more at [fhircast.org](http://fhircast.org), [FHIR implementation guide](https://hl7.org/fhir/uv/fhircast/2024May/), [IHE Integrated Reporting Application](https://profiles.ihe.net/RAD/IRA/index.html) and [FHIRcast Javascript sandbox](https://github.com/fhircast/sandbox.js).

The extension allows publishing FHIR resources such as measurements, annotations (observations) and imaging selections to FHIRcast hubs.  It can also receive events from the hubs on websockets.

A viewer side panel is included for  troubleshooting FHIRcast connections and workflows.  

You can experiment with 'FHIRcast conferences' with the demonstration client here: [FHIRcast OHIF client](https://hub-fhircast.azurewebsites.net/ohif).

This client connects automatically to the [Javascript hub](https://hub-fhircast.azurewebsites.net/).  
After start-up the OHIF client will fetch context from the hub and  automatically open the study specified (if there is one).  
It is configured to automatically send imagingstudy-open and imagingstudy-close messages.  Therefore when you open multiple clients, they will follow each other; open and close studies in sync.  
This could be useful for remote conferences like a tumor board where the users want to view the same studies but need control of viewing conditions or some user use a different viewer software to view the same patient.



### FHIRcast Service
#### Overview
This service handles communication to FHIRcast hubs.  It includes an API to get tokens, subscribe to a topic, publish events and get the current context.  Events received from the hub are published internally using the pubSub mechanism.
Automation of connection and reconnection to the hub on loss of websocket is available.  Therefore the token and  topic subscription APIs are not necessary for most implementations.   

The default hub and autoStart/autoReconnect settings are configured in the fhircast section of the configuration file.  Hubs are also configured in this section:
 

```typescript
fhircast: {
      defaultHub:'TEST-LOCAL',
      autoStart: true,
      autoReconnect:true,
      sendImagingStudyOpen: true,
      sendImagingStudyClose: true,
      hubs: [
        {
          name:'TEST-LOCAL',
          friendlyName:'JavaScript Sandbox',
          productName: 'JS_SANDBOX',
          enabled:true,
          events: ['imagingstudy-open','imagingstudy-close', ],
          lease:999,
          hub_endpoint: 'http://localhost:5000/api/hub',
          authorization_endpoint: 'http://localhost:5000/oauth/authorize',
          token_endpoint: 'http://localhost:5000/oauth/token',
        },
        {
          ...
        }
``` 
The service can also be configured to automatically send ImagingStudy-open and ImagingStudy-close events with the sendImagingStudyOpen and sendImagingStudyClose settings.


The service activity can be monitored in the browser console with the verbose option and filtering on 'Fhir':

 ![browserConsole](/images/browserConsole.png)
#### Events
There are five events that get publish in `FhircastService`:

| Event                 | Description                                            |
| --------------------- | ------------------------------------------------------ |
| FHIRCAST_MESSAGE      | Fires when a message is received                       |
| HUB_SUBSCRIBED        | Fires when successfully subscribed                     |
| HUB_UNSUBSCRIBED      | Fires when successfully unsubscribed                   |
| WEBSOCKET_CLOSE       | Fires when the websocket is closed                     |
| TOKEN_ACQUIRED        | Fires when a token was retrieved from the hub          |

The FHIRCAST_MESSAGE event is the one most client would implement.  The other events could be used to display status of the connection to the hub.  The code snipplet below shows how to subscribe to the PubSub event.  Notice that the subscribe function here is from PubSub.  The fhircast subscribe function is called 'fhircastSubscribe'.
```typescript
    const subscriptions = [];

    [fhircastEvent, hubSubscribed, hubUnsubscribed,websocketClose,].forEach(evt => {
      const { unsubscribe } = FhircastService.subscribe(evt, () => {      
        if ( evt==='tokenAcquired' ) {
          setTokenAcquired(true);
        }
        if (evt==='hubSubscribed') {
          sethubConnected(true);
        }
        if (evt==='hubUnsubscribed'  ) {
          sethubConnected(false);
        }
        if ( evt==='websocketClose' ) {
          sethubConnected(false);
        }
        if (evt==='fhircastMessage') {
          console.debug(' fhircastMessage event received.');
        }
      });
      subscriptions.push(unsubscribe);
    });
    return () => {
      subscriptions.forEach(unsub => {
        unsub();
      });
    };
```

#### API



- `fhircastPublish(fhirMessage)`:  This method sends the provided FHIRcast message to the hub and returns the hub response.status.  The message id, topic and timestamp values will be set/overwriten by the service before being sent. 
```typescript
        let fhirMessage={
            timestamp: '',
            id: '',
            event: {
              'hub.topic': '',
              'hub.event': 'imagingstudy-close',
              context: [...]
            }
        }
```

- `getContext()`:  This method fetches the current context from the hub and returns the current context in JSON. 

- `getToken()`:   This method fetches the token from the hub and set it internally.  If the hub provides a topic and also sets it.  If autoConnect is set, the method will automatically subscribe to the hub. 

- `fhircastSubscribe()`: Subscribes to the hub with the currently set topic and the events configured for the hub.

- `fhircastUnsubscribe()`: Unsubscribes from  the hub.
  
- `setHub(hubName:string)`: Sets the current hub to the one specified by hubName.  Also unsubscribes from current hub if connected. Returns true if the hub is found in the configuration.

- `getHub()`: Retrieve the hub currently in use.  Returns hub object.
 
- `setTopic(topic: string)`: Sets the current hub to the one specified by hubName.  Returns true if no errors.
  
- `getTopic()`: Retrieve the topic currently in use.  Returns a string.





### Test side panel
When you open the  test side panel, you should be already conected to the hub and subscribed to a topic.
You can select another hub from the drop down box.  The entries come from the configuration file.
Next you subscribe to the hub for a specific topic.  A FHIRcast topic is normally defined by the hub and  is typically a secret user identifier or a user session identifier. If you open multiple test clients using the same topic, perhaps one on your PC and one on your tablet, you will see the messages going across.  
In theory, several users can subscribe to a same topic and have a 'conference' or 'classroom' type of session.

 ![sidepanel](/images/fhircast-side-panel.png)
 
 Once the subscription is successful, the other elements of the panel are enabled.  
 The 'Get context' button queries the hub for the current context.  A typical use case is to query the hub after start-up and redirect the viewer to the current study automatically.

 The 'Publish' button is enabled after you select an event to send fom the drop down list.  
 The default events in the list are the IHE connectathon transactions required for the [IHE Integrated Reporting Application](https://profiles.ihe.net/RAD/IRA/index.html) profile.  
 The publish button will send to the hub and the response will be displayed in the text area below. 

### FHIRcast conferencing and context sharing

FHIRcast conferencing and context sharing is a feature of the JavaScript hub.  It allows users to direct their events to another user or a  group of users.  Users can then view the same studies in sync but each using their own viewer and interacting independently with the data sets.  Since we are using the FHIRcast, it should work with all viewers that support FHIRcast out of the box.

To start a conference, use the menu option in the top tight corner of the viewer:

 ![menuOption](/images/menuOption.png)


This will open an iframe thats loads a small 'conference management' web page from the hub.  The same web page can be accesses outside the client if needed.


![startConference](/images/startConference.png)


Then choose a conference title and select/unselect the users that shall receive the ImagingStudy-open and ImagingStudy-close events that the conference lead will generate.


Upon receiving the first event of a conference, a modal window shows up to allow the user to approve the reception of the conference events.


To end or exit the conference, use the same options menu again.




### How to use 

#### Installation
The side panel is named FhircastPanel.  It can be added to OHIF modes like other extensions in the routes section of the mode index file.  For example, to add to the left panel:
 
leftPanels: ['fhir.panelModule.FhircastPanel']

#### Auto-reconnect on websocket close
When the websocket is closed due to network disruption or hub restart, the callback function will return special event 'WEBSOCKET-CLOSED'.  This is used to automatically resubscribe to the hub and open a new websocket.


### Medplum hub
Medplum is a headless EHR development plaform that includes a FHIRcast hub.   To work with the Medplum hub,  create a client id and secret in the 'Project' section of the Medplum app portal under the 'clients' tab.  Use 'client_credentials' for the oauth2 authentification type and configure those in the environment variables (client_id, client_secret) of the OHIF project.  

### Philips Software hub
To configure the Philips hub, set the product name to 'PHILIPS' in the configuration.  This hub is generally available during connectathons.
```typescript
        {
          name:'PHILIPS',
          friendlyName:'Philips FHIRcast hub',
          productName: 'PHILIPS',
          enabled:true,
          events: ['patient-open','patient-close','imagingstudy-open','imagingstudy-close'],
          lease:999,
          hub_endpoint: 'http://92.108.246.183:9421/api/sync/fhircast',
      }
```
### Siemens Healthineers hub
The conformance statement can be found here: [syngo.via VB80A HL7 conformance](https://www.siemens-healthineers.com/services/it-standards/hl7-digital-and-automation/syngo-via).  To configure the Siemens hub, set the product name to 'SIEMENS' in the configuration. Connectivity test with this hub is pending.

### Nuance Powerscribe hub
#### PowerCast connector
The PowerCast connector is  a utility of the Nuance PsOne reporting client.  It runs on Windows PC and provides a local endpoint for discovery and login.  It can launch the PsOne client and also provide the FHIRcast topic. Specifications are here: 
[Nuance Powercast](https://connect2.nuancepowerscribe.com/psonesetup/PO-PowerCastIntegrationGuide.pdf).

##### Configuration
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
    fhircastHubNames: ['POWERCAST'],
  },
]
```
The .env file defines the application identifiers:
```env
REACT_APP_NAME_CLIENT_ID=723928408739-k9k9r3i44j32rhu69vlnibipmmk9i57p
REACT_APP_NAME_CLIENT_SECRET=client_secret
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
After the configuration is retrieved, the viewer is authorized to a Auth0 endpoint and obtains a bearer token to communicate with the FHIRcast hub.  The topic is only provided if someone is logged in the PsOne client.  Login can be automated with the bearer token and the connector login endpoint:

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
When a topic is obtained, subscription to the FHIRcast hub can be automated if enabled in the connector configuration settings.
The PowerCast subscription response contains the current context of the hub.

##### Test side panel

### Example integration
THe side panel can demonstrate handling of a number of events.

#### Patient-open 
Adds "/?mrn=<patient id>" to the URL of the viewer.  This limits the study list to the requested patient. 

#### ImagingStudy-open 
Opens the study normaly using the StudyInstanceUIDs parameter.

#### DiagnosticReport-open 
Adds "/?accession=<accession number>" to the URL of the viewer.  This limits the study list to the requested matching studies. 






