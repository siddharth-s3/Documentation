#  Doc Loader API
- [Overview](#overview)
- [Get started](#get-started)
- [Configuration](#api-configuration)
- [Examples](#examples)
- [Documentation](#documentation)


## Overview
DocParser is a component that allows the user to  efficiently extract text, headings, and tables from the DOCX, PDF and PPTX  converting them into structured data for advanced processing.

### Key Features
-   Text and Heading Extraction - Extracts text and headings from DOCX, PDF and PPTX files while maintaining hierarchical structure of headings.
-   Table Parsing - Accurately parses and converts table from files to JSON format when necessary.
-   Metadata Integration - Incorporates custom metadata into the extracted content for enhanced document management.
-   Asynchronous Operations - Uses asynchronous programming for efficient processing.

#### Q2 release

 - Added functionality of image analysis using computer vision. Images in documents are read, saved and captions (dense and simple) and text is extracted from images. Image analysis only works for pdf files due to limitation of Azure Document Intelligence.
 - Added PPTX functionality for parsing.
 - New Swagger UI page with examples
 - Moved critical parameters to headers to improve security

## Get started
On deployment, the container/kubernetes will install the required packages and will start the API endpoint. The /docparser API should be called using POST method with the following body:

**Header:**
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
        "DataSourceURL":"https://xxx.sharepoint.com/teams/sitename/",
        "Folders": ["FolderName"],
        "RelativeURL":"/teams/sitename/Shared Documents/",
        "FileType": ["pdf", "docx"],
        "LastUpdated":"1999-01-01 00:00:00"
}
```

## API Configuration

### Params
**Note:** All parameters mentioned are case sensitive
- BlobURL (required) (string): The URL where the document file to be parsed is located.This should be a direct link to the file.
- FileName (required) (strings): The name of the file to be parsed.
-   ChunkSize (required) (integer): The intended token count of each chunk.
-   ADLSPath (required) (string): The path within Azure Data Lake Storage where the document file is located. This path helps identify the file within your storage account. This parameter is expected to deprecate soon and we will only use BlobURL.
-   StorageContainerName (required) (strings): The name of your container in Azure storage account where the document is stored. This parameter is expected to deprecate soon and we will only use BlobURL.
-   StorageAccountName (required) (string): The name of your Azure storage account where the Blob Storage container resides. This parameter is expected to deprecate soon and we will only use BlobURL.
-   StorageAccountKey (required) (string): The access key for the Azure Storage Account.
-   FormRecKey (required) (string): The API key required to authenticate with Azure Form Recognizer service.
-   FormRecEndpoint (required) (string): The endpoint URL for the Azure Form Recognizer service.
-   MetaData (optional) (object): Optional metadata associated with the file.
-   DownloadURL (optional) (string): A URL that provides a downloadable link to the orginal document file.
-   OutputFileName (optional) (string): A custom name for the output JSON file containing parsed data. If this param is not provided then the output file name will be the same as "ADLSPath" parameter, with "/" replaced by "_" and suffixed with the request time.
-   OutputContainerName (optional) (string):  The name of the Azure Blob Storage container where the parsed JSON output will be stored. If this param is not provided then the file is stored in the container specified in "StorageContainerName" parameter.
-   OutputFilePath (optional) (string):Path for storing the output file in the Azure Blob Storage. If this param is not provided then the file path will become NULL.

### Result Params
-   Success: If the API call has successfully found the files and returned the output, it updates the success along with the following parameters: 
    -   Status: Indicated the operations status ("Success" or "Failure").
    -   Output: Provides the Azure Blob Storage path("Blobpath") of the parsed document upon success.
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

### Example with no output params
Required Body:
```json
{
"BlobURL":"https://nucleusdifaimlstg.blob.core.windows.net/config/JsonFiles/ISPAL%20Replicate/Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
"FileName":"Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
"ChunkSize":1500,
"ADLSPath":"ISPAL Replicate/Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
"StorageAccountName":"nucleusdifaimlstg",
"StorageContainerName":"config/JsonFiles",
"StorageAccountKey":"xxxxxxxxx",
"FormRecKey":"xxxx",
"FormRecEndpoint":"https://nucleusdifgenai-documentintelligence.cognitiveservices.azure.com/"
}
```

Result:
```json
{
  "Failures": [],
  "Success": [
    {
    "Output": {
        "BlobPath": "config/ISPAL Replicate_Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx_2024-06-17 02:50:29.json"
    },
    "Status": "Success"
}
  ]
}
```
### Example with no output params, MetaData and DownloadURL
```json
{
"BlobURL":"https://nucleusdifaimlstg.blob.core.windows.net/config/JsonFiles/ISPAL%20Replicate/Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
"FileName":"Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
"ChunkSize":1500,
"ADLSPath":"ISPAL Replicate/Reference.AUTO.Nucleus.IS.RPA Infra Architecture Design.docx",
"StorageAccountName":"nucleusdifaimlstg",
"StorageContainerName":"config/JsonFiles",
"StorageAccountKey":"xxxxxxxxx",
"FormRecKey":"",
"FormRecEndpoint":"https://nucleusdifgenai-documentintelligence.cognitiveservices.azure.com/",
"MetaData":{
    "Author":"John Doe"
    },
"OutputFileName" : "ThisIsAwesome3",
"OutputContainerName": "config",
"OutputFilePath" : "JsonFiles"
}
```


Result:
```json
{
  "Failures": [],
  "Success": [
{
    "Output": {
        "BlobPath": "config/JsonFiles/ThisIsAwesome3.json"
    },
    "Status": "Success"
}
  ]
}
```

## Documentation

## Process flow
![alt text](https://satechhubdevjapaneast001.blob.core.windows.net/workflows/DocParser.png)
