# OHIF-FHIR integration extension

## Introduction
<div>

  <p><strong>The OHIF Viewer</strong> is a zero-footprint medical image viewer
provided by the <a href="https://ohif.org/">Open Health Imaging Foundation (OHIF)</a>. It is a configurable and extensible progressive web application with out-of-the-box support for image archives which support <a href="https://www.dicomstandard.org/using/dicomweb/">DICOMweb</a>.</p>
</div>
This extension is a collection of  FHIR integration clients and components such as SMART Imaging Access and FHIRcast.


## FHIRcast
FHIRcast synchronizes healthcare applications in real time to show the same clinical content to a common user. It also allows the sharing of FHIR objects to the applications connected to a hub.  Health IT users often work in many disparate applications at the same time, for example, worklist, PACS,dictation,EMR, AI, ect.   The FHIRcast standard will help lower cost and accelerate health IT  integration across desktop and mobile applications. Find out more at [fhircast.org](http://fhircast.org) and  [IHE Integrated Reporting Application](https://profiles.ihe.net/RAD/IRA/index.html).


THE OHIF FHIRcast client api allows publishing information such as measurements, annotations and FHIR imaging selection to FHIRcast hubs.  It can also receive events such as 'imagingstudy-open' and redirect the viewer.

The extension includes a viewer side panel for troubleshooting FHIRcast connections and workflows.

Hubs are configured in the DataSources file.  More than one hub can be configured and used by the client. 

### Using the side panel

The first step is to subscribe to the hub for a specific topic.  A FHIRcast topic is typically a user name. Take care to  change it from the default when using an open public test hub.  If not, you may receive events from other subscribed users that have the same topic.


 ![sidepanel](/images/fhircast-side-panel.png)

 
 
 Once the subscription is successful, the other elements of the panel are enabled.  
 The 'Get context' button queries the hub for the current context.  A typical use case is to query the hub after start-up and redirect the viewer to the current study automatically.

### How to use the extension in your FHIRcast integration 

#### Installation
The side panel is named FhircastPanel.  It can be added to OHIF modes like other extensions in the routes section of the mode index file.  For example, to add to the left panel:

             leftPanels: ['fhir.panelModule.FhircastPanel']

#### Subscribe to the hub
const hubSubscribeResponse = subscribe(
    '<hub name in configuration>',
    topic,
    fhircastCallback    // callback function to receice and 
                        //process events received on the websocket hub connection.
    );

#### Send an event to the hub:
const hubPublishResponse = publishFhircast(fhircastMessage,topic,hub);

#### Receive events:
Implement the callback function fhircastCallback:

const fhircastCallback(event) {
if (event.event-type==='patient-open') {
    // Do something here
}

#### Get Context:

### Example integration

#### Events:
On patient-open:  Open the latest study of the patient ( same behavior as IID) with the study side panel open and set to "All".
On open study: Open the study normaly.



## SMART Imaging Access

This is a client for the SMART Imaging project.  It allows the ohif study list to use a FHIR server as a datasource. 

The SMART Imaging project aims to provide a unified solution for accessing imaging studies alongside clinical data using a single authorization flow. This enables patients to have better access to their data, facilitates second opinions, streamlines data donations for research, and supports providers in their analysis with preferred tools and specialty-specific viewers


### SMART for EHR
This is a for the classic SMART implentations.  It provides a panel that displays information from the EHR FHIR server on the patient being viewed.


## CDS hooks
CDS hooks service is not implemented.


Martin Bellehumeur 
## License 
MIT
