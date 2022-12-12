# APIC_Analytics-Adding-Configurations
API Connect Analytics: Adding extra-values configurations  

This article will showcase an example of modifying the API Connect analytics configuration.  
The specific modifications are:  
1. Remove the request and response bodies of all transactions in the analytics, and
2. offloading the analytics to a off-device log target like Splunk.  
  
NOTE: The instructions of this article assumes that the API Connect environment has already been installed and running. This article will be modifying the API Connect Analytics subsystem.

More details about the extra values may be found in the [IBM Documentation site: Creating Analytics extra-values](https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=subsystem-creating-analytics-extra-values-file)  

Prerequisites:
- API Connect environment already setup on any platform (e.g. OVA, OCP, CP4I). NOTE: This tutorial will be using an OVA platform example.  
- The latest project files for the analytics subsystem. During the installation of APIC, 

1. 
  

