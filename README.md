# OHIF-FHIR integration extension

## Introduction

This OHIF viewer extension is a collection of  FHIR integration clients and components such as SMART Imaging Access and FHIRcast.


## FHIRcast
FHIRcast synchronizes healthcare applications in real time to show the same clinical content to a common user. For example, a radiologist often works in three disparate applications at the same time (a radiology information system, a PACS and a dictation system), she wants each of these three systems to display the same study or patient at the same time.  Find out more at [fhircast.org](http://fhircast.org).


THE OHIF FHIRcast client api allows publishing information such as measurements, annotations and FHIR imaging selection to FHIRcast hubs.  It can also receive events such as 'imagingstudy-open' and redirect the viewer.

The extension includes a viewer side panel that can be added to the viewer for manual testing and debugging FHIRcast connections and workflows.

Hubs are configured in the DataSources file.  More than one hub can be configured and used by the client. 

### Using the side panel

The side panel can be added to OHIF modes like other extensions in the index file.

 ![sidepanel](/images/fhircast-side-panel.png)

 The first step is to subscribe to the hub for a specific topic .  A FHIRcast topic is typically a user name. Once the subscribtion is successful, the other elements of the panel are enabled.  
 The 'Get context' button queries the hub for the current context.  A normal use case is to query the hub immediately after start-up/subscription and to redirect the viewer to the current patient/study automatically.

### How to use the extension in your FHIRcast integration 

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
