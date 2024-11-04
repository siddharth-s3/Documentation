# Source Sync API
- [Overview](#overview)
- [Get started](#get-started)
- [Configuration](#api-configuration)
- [Examples](#examples)
- [Documentation](#documentation)


## Overview
Source Sync is a component that allows the user to download files from the Source and upload these files to Target storage service.

### Key Features
-   Automatically adds/updates files from Source and into Target.
-   Azure Integration - Works smoothly with Azure services for secure and reliable operations.

#### Q2 release

 - Added functionality of ADLS as a source along with existing SharePoint
 - New Swagger UI page with examples
 - Moved critical parameters to headers to improve security

## Get started
On deployment, the container/kubernetes will install the required packages and will start the API endpoint. The /sourcesync API should be called using POST method with the following body:

**Header for SharePoint:**
```json
"accept": "application/json"
"UserID": "xxx"
"Password": "xxx"
"TargetAdlsStorageAccountKey": "xxx",
```

**Body for SharePoint:**
```json
{
	"SourceType": "SharePoint",
	"DataSourceURL": "https://nttdatagroup.sharepoint.com/teams/SRVS-AS-DIFT/",
	"FolderName": "Test_GenAI",
	"RelativeURL": "/teams/SRVS-AS-DIFT/SharedDocuments/Test_GenAI/Pytest/Excel_folder/TestCases-Techhub.xlsx",
	"FileName": "TestCases-Techhub.xlsx",
	"TargetStorageContainerName": "test",
	"TargetAdlsStorageAccountName": "storagetechhubnadev"
}
```
**Header for ADLS:**
```json
"accept": "application/json",
"SourceAdlsStorageAccountKey": "xxx"
"TargetAdlsStorageAccountKey": "xxx",
```

**Body for ADLS:**
```json
{
"SourceType": "ADLS",
"SourceAdlsStorageAccountName": "storagetechhubnadev",
"TargetAdlsStorageAccountName": "storagetechhubnadev",
"FolderName": "Test_GenAI",
"RelativeURL": "/test/Test_GenAI/Exploring Pytest.docx",
"FileName": "Exploring Pytest.docx",
"TargetStorageContainerName": "test-ppt-file"
}

```

## API Configuration

### Params
**Note:** All parameters mentioned are case sensitive
#### Header: 
- UserID (required) (string): Required when DataSource is SharePoint. User ID of the SharePoint where the documents are present. The user ID needs to have read/write access to SharePoint.
- Password (required) (string): Required when DataSource is SharePoint. Password of the SharePoint UserID given in the previous param.
- SourceAdlsStorageAccountKey: (required) (string): Required when DataSource is ADLS. Account Key of storage account from where the files have to be synced into the target.
- TargetAdlsStorageAccountKey(required) (string): Account Key of target account where the files have to be synced for further processing.
#### Body: 
- SourceType (required) (string): Should be SharePoint/ADLS. 
-   DataSourceURL (required) (string): Required when DataSource is SharePoint.  The URL where the SharePoint documents are present. It should contain the "/" at the end
-   FolderName (required) (list of strings): The folder in the URL which needs to be accessed. Only the folders specified will be returned.
-   RelativeURL (required) (string): Should contain the suffix after the sharepoint domain and the sitename. Ensure that the file type is also present in the filename.
-   FileName (required) (string): The name of the file to be download from source. Ensure that the file type is also present in the filename.
- TargetStorageContainerName (required) (string): The Storage container name where the file should be stored.
- TargetAdlsStorageAccountName (required) (string): The storage account name where the file should be stored
- SourceAdlsStorageAccountName (required) (string): Required when DataSource is  ADLS.  The storage account name where the file should be fetched from.



### Result Params
-   Success: If the API call has successfully found the files and returned the output, it updates the success along with the following parameters: 
    -   ADLSPath: Defaulted to "add".
    -   BlobURL: Specifies the URL of the file in target storage
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

### Example for SharePoint

**Header for SharePoint:**
```json
"accept": "application/json"
"UserID": "xxx"
"Password": "xxx"
"TargetAdlsStorageAccountKey": "xxx",
```

**Body for SharePoint:**
```json
{
	"SourceType": "SharePoint",
	"DataSourceURL": "https://nttdatagroup.sharepoint.com/teams/SRVS-AS-DIFT/",
	"FolderName": "Test_GenAI",
	"RelativeURL": "/teams/SRVS-AS-DIFT/SharedDocuments/Test_GenAI/Pytest/Excel_folder/TestCases-Techhub.xlsx",
	"FileName": "TestCases-Techhub.xlsx",
	"TargetStorageContainerName": "test",
	"TargetAdlsStorageAccountName": "storagetechhubnadev"
}
```

**Result:**
```json
{  
"Output": {  
"ADLSPath": "Test_GenAI/Pytest/Excel_folder/TestCases-Techhub.xlsx",  
"BlobURL":"https://storagetechhubnadev.blob.core.windows.net/test/Test_GenAI/TestCases-Techhub.xlsx",  
"Status": "Success"  
}
```
### Example for ADLS
**Header for ADLS:**
```json
"accept": "application/json",
"SourceAdlsStorageAccountKey": "xxx"
"TargetAdlsStorageAccountKey": "xxx",
```

**Body for ADLS:**
```json
{
"SourceType": "ADLS",
"SourceAdlsStorageAccountName": "storagetechhubnadev",
"TargetAdlsStorageAccountName": "storagetechhubnadev",
"FolderName": "Test_GenAI",
"RelativeURL": "/test/Test_GenAI/Exploring Pytest.docx",
"FileName": "Exploring Pytest.docx",
"TargetStorageContainerName": "test-ppt-file"
}
```

**Result**
```
{  
"Output": {  
"ADLSPath": "Test_GenAI/Exploring Pytest.docx",  
"BlobURL": "https://storagetechhubnadev.blob.core.windows.net/test-ppt-file/Test_GenAI/Exploring%20Pytest.docx" 
},  
"Status": "Success"  
}
```

## Documentation

## Process flow
![alt text](<https://satechhubdevjapaneast001.blob.core.windows.net/workflows/SourceSync.jpg>)
