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
- The latest project files for the analytics subsystem. The project files are the files used for the installation of the APIC analytics installation as documented in the [IBM API Connect document: Configuring the Analytics subsystem](https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=subsystem-configuring-analytics). This document will be adding details to those files (e.g apiconnect-up-v10.yml) in order to modify the current analytics subsystem configurations.

1.	Create a new file titled analytics-extra-values.yaml in the same myProject directory where the original install files were created, where the apiconnect-up-v10.yml is located.  

![image](https://user-images.githubusercontent.com/66093865/207155240-d6f1bdb8-52ac-4cd8-ac35-a4395a786690.png)  

2. In the analytics-extra-values.yaml, the following section describes:  
  a. Removal of the request and response bodies due PII data on them, and   
  b. Analytics offload to splunk.  
```  
spec:
  ingestion:
    filter: |
      mutate {
        remove_field => ["request_body", "response_body"]
      }
  external:
    offload:
      enabled: true
      output: |
        syslog {
          host => "10.x.x.x"
          port => 514
          protocol => "udp"
          id => "OFFLOAD_apievent"
          appname => "apimanagement"
          msgid => "%{org_id}"
          facility => "user-level"
          severity => "informational"
          codec => "json"
          sourcehost => "splunk.mycompany.com"
        }

```  

3. Open the apiconnect-up-v10.yml file to add the extra-values-file stanza under the “[i]kind: apic.ibm.com/AnalyticsSubsystem[i]” [i]spec:[i] section as shown in the diagram.  
![image](https://user-images.githubusercontent.com/66093865/207156855-6b07ac5d-9905-4cba-8640-1b8cc73c8696.png)  

4. Once the file is set, validate that apiconnect-up-v10.yml configurations are valid by opening a command prompt to the myProject directory and issuing the following command:  

```  
apicup subsys get analyt --validate
```  
Example:  
![image](https://user-images.githubusercontent.com/66093865/207155086-24348970-4097-4ab0-b9f0-824876d834e4.png)  

5.	Once, the myProject side is validated as done above, validate the analytics subsystem, by first logging into any of the analytics subsystems you’re about to update and then issuing the apic health-check command. Remember to use sudo -i before the health-check command.
If there are no issues and is healthy, the response should look as follows:  
![image](https://user-images.githubusercontent.com/66093865/207157799-d629e4a9-e06b-4488-8eef-acd8eb1cbfdb.png)  

6.	After the subsystem is validated healthy, then go back to the terminal which the myProject resides. Issue the install command to update the analytics:  
```  
apicup subsys install analyt
```  

Wait for about 5 minutes, and the install should update for all the nodes in the cluster by itself automatically.

7.	You may verify that the update has successfully completed by 2 ways:  
  
  a. From the analytics subsystem, issue the following command to check that the analytics main operator yaml has the newly updated “remove_field” syntax with the following command:  
```  
k describe analyticsCluster | grep remove_field
```  
You should receive the following results:  
![image](https://user-images.githubusercontent.com/66093865/207158282-e135d054-8fe3-45e9-ba23-28c372ece353.png)  
  
  b. Navigate to the APIM catalog, and check in the analytics webui page to verify that analytic events do not contain the request and response bodies anymore.  
  




