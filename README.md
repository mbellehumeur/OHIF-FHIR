# OHIF-FHIR integration extension

## Introduction

This ohif extension is a collection of  FHIR integration clients and components such as SMART Imaging Access and FHIRCast.


## FHIRcast
FHIRcast synchronizes healthcare applications in real time to show the same clinical content to a common user. For example, a radiologist often works in three disparate applications at the same time (a radiology information system, a PACS and a dictation system), she wants each of these three systems to display the same study or patient at the same time. More information on the FHIRcast standard please visit the home page: fhircast.org.

THE OHIF FHIRCast client api allows publishing information such as measurements, annotations and FHIR imaging serlection  to FHIRcast hubs.  It can also receive events such as 'imagingstudy-open' and redirect the viewer.

THe extension includes a side panel can added to the viewer for manual testing and debugging FHIRcast connections and workflows.

Hubs are configured in the DataSources file.  More than one hub can be configured and used by the client. 

### Using the sige panel

### How to use the extension in your FHIRcast integration 

#### Subscribe to the hub
const hubPublishResponse = subscribe(
    '<hub name in configuration>',
    fhircastCallback    // callback function to receice and 
                        //process events received on the websocket hub connection.
    );

### Send an event to the hub:
const hubPublishResponse = publishFhircast(fhircastMessage,hub);

### Receive events:
Implement the callback function fhircastCallback:

fconst fhircastCallback(event) {
if (event.event-type==='patient-open') {
    // Do something here
}

}

### Events:
On Patient-open:  Open the latest study of the patient ( same behavior as IID) with the study side panel open and set to "All".
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
