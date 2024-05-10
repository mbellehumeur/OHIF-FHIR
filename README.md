# OHIF-FHIR integration extension
- [OHIF-FHIR integration extension](#ohif-fhir-integration-extension)
  - [Introduction](#introduction)
  - [SMART on FHIR launch](#smart-on-fhir-launch)
  - [SMART Imaging Access](#smart-imaging-access)
  - [FHIRcast](#fhircast)
    - [Using the side panel](#using-the-side-panel)
    - [How to use the extension in your FHIRcast integration](#how-to-use-the-extension-in-your-fhircast-integration)
      - [Installation](#installation)
      - [Subscribe to the hub](#subscribe-to-the-hub)
      - [Send an event to the hub](#send-an-event-to-the-hub)
      - [Receive events](#receive-events)
      - [Get Context](#get-context)
    - [Example integration](#example-integration)
      - [Patient-open](#patient-open)
      - [ImagingStudy-open](#imagingstudy-open)
      - [DiagnosticReport-update](#diagnosticreport-update)

## Introduction
<div>

  <p><strong>The OHIF Viewer</strong> is a zero-footprint medical image viewer
provided by the <a href="https://ohif.org/">Open Health Imaging Foundation (OHIF)</a>. It is a configurable and extensible progressive web application with out-of-the-box support for image archives which support <a href="https://www.dicomstandard.org/using/dicomweb/">DICOMweb</a>.</p>
</div>
This extension is a collection of  FHIR integration clients and components such as the SMART on FHIR launch with patinet context, SMART Imaging Access and FHIRcast.


## SMART on FHIR launch


When an application is being launched by an EMR, the [SMART on FHIR](https://dev.smarthealthit.org/)® launch scenario can provide patient and encounter context on start-up.  
Test the online SMART App launcher with a [preselected patient, provider and 'App Launch URL'](https://launch.smarthealthit.org/index.html?auth_error=&fhir_version_1=r2&fhir_version_2=r2&iss=&launch_ehr=1&launch_url=https%3A%2F%2Freporting-fhircast.azurewebsites.net%2F&patient=smart-4444001&prov_skip_auth=1&prov_skip_login=1&provider=COREPRACTITIONER1&pt_skip_auth=1&public_key=&sb=&sde=&sim_ehr=1&token_lifetime=15&user_pt=)
(https://launch.smarthealthit.org).  Click the green 'Launch App!' button in the lower right corner to initiate the launch.
![SMARTlaunch](/images/SMARTlaunch.png)

This launches the viewer within the simulated EHR.
![SMARTlaunched](/images/SMARTlaunched.png)

THE SMART launch can also provide the topic to a FHIRcast session.

Alternatively, test the SMART launch by navigating to the SMART launch sandbox: http://launch.smarthealthit.org and selecting a patient, provider and the app url which can be a local instance in debug mode.


## SMART Imaging Access

This is a client for the SMART Imaging project.  It allows the OHIF study list to use a FHIR server as a datasource. 

The SMART Imaging project aims to provide a unified solution for accessing imaging studies alongside clinical data using a single authorization flow. This enables patients to have better access to their data, facilitates second opinions, streamlines data donations for research, and supports providers in their analysis with preferred tools and specialty-specific viewers


## FHIRcast
FHIRcast synchronizes healthcare applications in real time to show the same clinical content to a common user. It also enables sharing FHIR resources between all the  applications of a workflow even when running on a different computer.  Health IT users often work in many disparate applications at the same time (worklist, PACS,dictation,EMR, AI, ect).   The FHIRcast standard will help lower cost and accelerate real-time health IT  integration across desktop and mobile applications. Find out more at [fhircast.org](http://fhircast.org), [FHIR implementation guide](https://hl7.org/fhir/uv/fhircast/2024May/), [IHE Integrated Reporting Application](https://profiles.ihe.net/RAD/IRA/index.html) and [FHIRcast Javascript sandbox](https://github.com/fhircast/sandbox.js).

The extension allows publishing FHIR resources such as measurements, annotations (observations) and imaging selections to FHIRcast hubs.  It can also receive events from the hubs on websockets.

The extension includes a viewer side panel for troubleshooting FHIRcast connections and workflows.  Hubs are configured in the DataSources file.  More than one hub can be configured and used by the viewer. 

### Using the side panel

The first step is to subscribe to the hub for a specific topic.  A FHIRcast topic is defined by the hub and is  typically a user identifier. Take care to  change it from the default when using an open public test hub.  If not, you may receive events from other subscribed users that are using the same default topic.


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

### Example integration

#### Patient-open 
Adds "/?mrn=<patient id>" to the URL of the viewer.  This limits the study list to the requested patient. 

#### ImagingStudy-open 
Open the study normaly.

#### DiagnosticReport-update







Martin Bellehumeur 
