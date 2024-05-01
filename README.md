# OHIF-FHIR integration extension

## Description 

This ohif extension is a collection of  FHIR integration clients and components such as SMART Imaging Access and FHIRCast.

### SMART Imaging Access

This client allows the ohif study list to use a FHIR server as a datasource. 

The SMART Imaging project aims to provide a unified solution for accessing imaging studies alongside clinical data using a single authorization flow. This enables patients to have better access to their data, facilitates second opinions, streamlines data donations for research, and supports providers in their analysis with preferred tools and specialty-specific viewers


### FHIRCast


This client api allows publishing information such as measurements and annotations to FHIRCast hubs.  It can also receive events from hubs such as 'open-imaging-study' and redirect the viewer.

A side panel can be added to the ohif viewer for manual testing and debugging FHIRCast connections and workflows.

Hubs are configured in the DataSources file.  More than one hub can be configured and used by the client.  

On Open patient:  Open the latest study of the patient ( same behavior as IID) with the study side panel open and set to "All".
On open study: Open the study normaly.



### SMART for EHR
This is a for the classic SMART implentations.  It provides a panel that displays information from the EHR FHIR server on the patient being viewed.


### CDS hooks
CDS hooks service is currently not implemented.  It would allow the viewer to be launched upon an EHR CDS event such as open-patient or order-select.


## Author 
Martin Bellehumeur 
## License 
MIT
