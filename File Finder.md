# File Finder API
- [Overview](#overview)
- [Get started](#get-started)
- [Configuration](#api-configuration)
- [Examples](#examples)
- [Documentation](#documentation)


## Overview
File Finder is a component that allows the user to find all the files in a folder in the source data.

### Key Features
-   The component connects to sources and can retrieve all file details of folder(s) specified
-   Date Filter - A modified date can be provided and the files modified after that date will be returned.
-   File Type filter - File types can be provided as an input and only the provided file types will be returned

#### Q2 release

 - Added functionality of ADLS as a source along with existing SharePoint
 - New Swagger UI page with examples
 - Moved critical parameters to headers to improve security

## Get started
On deployment, the container/kubernetes will install the required packages and will start the API endpoint. The /filefinder API should be called using POST method with the following headers and body:

**Header for SharePoint:**
```json
"accept": "application/json"
"UserID": "xxx"
"Password": "xxx"
"Content-Type": "application/json"
```

**Body for SharePoint:**
```json
{
		"SourceType": "SharePoint",
        "DataSourceURL":"https://xxx.sharepoint.com/teams/sitename/",
        "Folders": ["FolderName"],
        "RelativeURL":"/teams/sitename/Shared Documents/",
        "FileType": ["pdf", "docx"],
        "LastUpdated":"1999-01-01 00:00:00"
}
```
**Header for ADLS:**
```json
"accept": "application/json"
"SourceStorageAccountKey": "xxxx"
"Content-Type": "application/json"
```

**Body for ADLS:**
```json
{
  "SourceType": "ADLS",
  "SourceStorageAccountName": "SourceStorageAccountName",
  "Folders": [
    "folder1",
    "folder2"
  ],
  "ContainerName": "SourceContainerName",
  "FileType": [
    "ppt",
    "pdf"
  ],
  "LastUpdated": "1900-01-01 00:00:00"
}
```

## API Configuration

### Params
**Note:** All parameters mentioned are case sensitive
#### Header: 
-	UserID (required) (string): Required when DataSource is SharePoint. User ID of the SharePoint where the documents are present. The user ID needs to have read/write access to SharePoint
-	Password (required) (string): Required when DataSource is SharePoint. Password of the SharePoint UserID given in the previous param.
-	SourceStorageAccountKey (required) (string): Required when DataSource is ADLS. Storage Account Key for ADLS to read files from the container.
#### Body: 
- SourceType (required) (string): Should be SharePoint/ADLS. 
-   DataSourceURL (required) (string): Required when DataSource is SharePoint. This is the URL where the SharePoint documents are present. It should contain the "/" at the end
-   Folders (required) (list of strings): The folder in the URL which needs to be accessed. Only the folders specified will be returned.
-   RelativeURL (required) (string): Required when DataSource is SharePoint. Should contain the suffix after the sharepoint domain and the sitename. It should contain the "/" at the end for SharePoint.
-   FileType (required) (list of strings): Specify the file types which should be read and returned.
-   LastUpdated (optional) (string): Specify the last updated date. The files modified after the last updated date are returned. Format should be "yyyy-mm-dd hh:mm:ss".
- ContainerName

### Result Params
-   Success: If the API call has successfully found the files and returned the output, it updates the success along with the following parameters: 
    -   Action: Defaulted to "add".
    -   DownloadURL: Specifies the URL of the file in source
    -   FileName: Provides the filename without the full path
    -   FolderName: provides the foldername where the file is present
    -   ServerRelativeURL: Returns the relative path. i.e. the sitename and the foldername(s)
-   Failures: If the API call is unsuccessful, it updates the failures in the API response:
    -   Message: Provides the reason of failure
    -   Start Time: The time at which the failure occured
    -   Folder: The folder in which the failure occured
    -   Exception: The error message which can be used to debug and find the failure.



### Response codes
-   200: Success
-   500: Internal server error. Occurs if the parameter values are incorrect like the foldername, URL etc.
-   400: If the parameters is incorrect or a required parameter is not found in the request body. 

## Examples

### Example with multiple folders for SharePoint
**Header**:
```json
"accept": "application/json"
"UserID": "xxx"
"Password": "xxx"
"Content-Type": "application/json"
```
**Body:**
```json
{
		"SourceType": "SharePoint",
        "DataSourceURL":"https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/",
        "Folders": ["ISPAL Replicate, Apps"],
        "RelativeURL":"/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/",
        "FileType": ["pdf", "docx"],
        "LastUpdated":"1999-01-01 00:00:00"
}
```

**Result:**
```json
{
  "Failures": [],
  "Success": [
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program Access analytics from your mobile without AirWatch.pdf",
      "FileName": "ICS Analytics Program Access analytics from your mobile without AirWatch.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program Access analytics from your mobile without AirWatch.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program Access analytics from your mobile with AirWatch.pdf",
      "FileName": "ICS Analytics Program Access analytics from your mobile with AirWatch.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program Access analytics from your mobile with AirWatch.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/RE_ NCC _ Guardian Life -June 2022 1.pdf",
      "FileName": "RE_ NCC _ Guardian Life -June 2022 1.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/RE_ NCC _ Guardian Life -June 2022 1.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program User Access_453566046208_1702184959553_.pdf",
      "FileName": "ICS Analytics Program User Access_453566046208_1702184959553_.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program User Access_453566046208_1702184959553_.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/RE_ NCC _ Guardian Life -June 2022.pdf",
      "FileName": "RE_ NCC _ Guardian Life -June 2022.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/RE_ NCC _ Guardian Life -June 2022.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program User Access_659271680000_1702184961894_.pdf",
      "FileName": "ICS Analytics Program User Access_659271680000_1702184961894_.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/ICS Analytics Program User Access_659271680000_1702184961894_.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/RE_ NCC _ Guardian Life -June 2022 2.pdf",
      "FileName": "RE_ NCC _ Guardian Life -June 2022 2.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/RE_ NCC _ Guardian Life -June 2022 2.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/FW_ Mandatory Compliance Status Report.pdf",
      "FileName": "FW_ Mandatory Compliance Status Report.pdf",
      "FolderName": "Apps/Yammer",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Yammer/FW_ Mandatory Compliance Status Report.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Microsoft Forms/ARB intake form/Question/BRD_NCC Application Services_ Adaptive Capaci_Abbai Eadara.docx?d=wf5fce9361c3b48c590798fd2521c6a12",
      "FileName": "BRD_NCC Application Services_ Adaptive Capaci_Abbai Eadara.docx",
      "FolderName": "Apps/Microsoft Forms/ARB intake form/Question",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Microsoft Forms/ARB intake form/Question/BRD_NCC Application Services_ Adaptive Capaci_Abbai Eadara.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Microsoft Forms/ARB intake form/Question/BRD_NCC Application Services_ SLA breach - Pr_Abbai Eadara.docx?d=wba0348f5ce8449bf8568b8791cd136d2",
      "FileName": "BRD_NCC Application Services_ SLA breach - Pr_Abbai Eadara.docx",
      "FolderName": "Apps/Microsoft Forms/ARB intake form/Question",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Microsoft Forms/ARB intake form/Question/BRD_NCC Application Services_ SLA breach - Pr_Abbai Eadara.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Microsoft Forms/ARB intake form/Question/BRD_NCC Application Services_CMDB Analytics_v_Abbai Chowdhary Eada.docx?d=w1ce51fab32d34e3b9d72ddf23b18113c",
      "FileName": "BRD_NCC Application Services_CMDB Analytics_v_Abbai Chowdhary Eada.docx",
      "FolderName": "Apps/Microsoft Forms/ARB intake form/Question",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/Apps/Microsoft Forms/ARB intake form/Question/BRD_NCC Application Services_CMDB Analytics_v_Abbai Chowdhary Eada.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx?d=w45cd968b99b2498e98f118592094c4d6",
      "FileName": "Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.NUCLEUS.IS.Azure + Dynamics 365 + Online Services - Public & Government - SOC 2 Type II + C5 + CSA Star Report (04-01-2022 to 3-31-2023)(2).pdf",
      "FileName": "Reference.AUTO.NUCLEUS.IS.Azure + Dynamics 365 + Online Services - Public & Government - SOC 2 Type II + C5 + CSA Star Report (04-01-2022 to 3-31-2023)(2).pdf",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.NUCLEUS.IS.Azure + Dynamics 365 + Online Services - Public & Government - SOC 2 Type II + C5 + CSA Star Report (04-01-2022 to 3-31-2023)(2).pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Adjudication Overview and Purpose - Pre-reqs.docx?d=w61e4e4a1411646beb1ff542ecae1999f",
      "FileName": "Reference.AUTO.Nucleus.IS.Adjudication Overview and Purpose - Pre-reqs.docx",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Adjudication Overview and Purpose - Pre-reqs.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Architecture Diagrams.docx?d=wb5514f5fe36e4288a83d6bc7aa6cb43d",
      "FileName": "Reference.AUTO.Nucleus.IS.Architecture Diagrams.docx",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Architecture Diagrams.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Automox Questionnaire.docx?d=wb4e164707ab241d788a46f372eaff07e",
      "FileName": "Reference.AUTO.Nucleus.IS.Automox Questionnaire.docx",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Automox Questionnaire.docx"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.NUCLEUS.IS.Kore SOC 2 Type2 Report 2023.pdf",
      "FileName": "Reference.AUTO.NUCLEUS.IS.Kore SOC 2 Type2 Report 2023.pdf",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.NUCLEUS.IS.Kore SOC 2 Type2 Report 2023.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.NUCLEUS.IS.NTT DATA-InfrastructureCloudSecurity_ISO27001.pdf",
      "FileName": "Reference.AUTO.NUCLEUS.IS.NTT DATA-InfrastructureCloudSecurity_ISO27001.pdf",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.NUCLEUS.IS.NTT DATA-InfrastructureCloudSecurity_ISO27001.pdf"
    },
    {
      "Action": "add",
      "DownloadURL": "https://nttdatagroup.sharepoint.com/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Automation Deployment Playbook Rapidstart Version.docx?d=w92d181c6f9d440e78899f552e494851a",
      "FileName": "Reference.AUTO.Nucleus.IS.Automation Deployment Playbook Rapidstart Version.docx",
      "FolderName": "ISPAL Replicate",
      "ServerRelativeURL": "/teams/srvs-ics-analyticsandperformancemanagement/Shared Documents/ISPAL Replicate/Reference.AUTO.Nucleus.IS.Automation Deployment Playbook Rapidstart Version.docx"
    }
  ]
}
```
### Example for ADLS

**Header for ADLS:**
```json
"accept": "application/json"
"SourceStorageAccountKey": "xxxx"
"Content-Type": "application/json"
```

**Body for ADLS:**
```json
{
  "SourceType": "ADLS",
  "SourceStorageAccountName": "vcbyfpr1rmrmtni1qhc7",
  "Folders": [
    "GenAIOutputs/Splitter"
  ],
  "ContainerName": "generic",
  "FileType": [
    "pdf",
    "txt"
  ],
  "LastUpdated": "1900-01-01 00:00:00"
}
```
**Response**


```json
{
  "Failures": [],
  "Success": [
    {
      "Action": "add",
      "DownloadURL": "https://vcbyfpr1rmrmtni1qhc7.blob.core.windows.net/generic/GenAIOutputs/Splitter/Errors/Splitter_2024-10-02 04:23:43.txt",
      "FileName": "Splitter_2024-10-02 04:23:43.txt",
      "FolderName": "GenAIOutputs/Splitter/Errors",
      "ServerRelativeURL": "generic/GenAIOutputs/Splitter/Errors/Splitter_2024-10-02 04:23:43.txt"
    }
  ]
}
```
```

## Documentation

## Process flow
![alt text](<https://satechhubdevjapaneast001.blob.core.windows.net/workflows/FileFinderComponent.png>)
