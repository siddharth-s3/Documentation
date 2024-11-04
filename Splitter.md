# Text Splitter API
- [Overview](#overview)
- [Get started](#get-started)
- [Configuration](#api-configuration)
- [Examples](#examples)
- [Documentation](#documentation)


## Overview
Text Splitter is a component that enables users to divide a text file into smaller chunks while preserving HTML headers, table tags and metadata, using a specified chunk size and a recursive text splitting method.

### Key Features
-   Contextual chunking -  Chunk is split basis header tags to ensure that each chunk has a relevant semantic meaning.
-   Tokenization-Based Splitting: Ensure chunks do not exceed a specified chunk size. Each chunk has a token limit which can be specified.
-   Recursive Splitting - Further divides large chunks using a recursive method and is used if contextual chunking is unable to chunk as per chunk size specified.
-   Metadata Management - Maintains metadata for each chunk.

#### Q2 release

 - New Swagger UI page with examples
 - Moved critical parameters to headers to improve security

## Get started
On deployment, the container/kubernetes will install the required packages and will start the API endpoint. The /textsplitter API should be called using POST method with the following:

**Header:**
```json
"accept": "application/json"
"StorageAccountKey": "xxxx"
"Content-Type": "application/json"
```

**Body:**
```json
{
  "BlobPath": "inputtest/docparser/ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json",
  "ChunkSize": 1536,
  "ChunkOverlap": 100,
  "StorageAccountName": "eprbutdz5j75nvoghmx5",
  "StorageContainerName": "inputtest",
  "OutputFileName": "outputfilename",
  "OutputContainerName": "outputtest",
  "OutputFilePath": "splittertest/documents"
}
```

## API Configuration

### Params
**Note:** All parameters mentioned are case sensitive

#### Header: 
-   StorageAccountKey (required) (string): The access key for the Azure Storage Account.
#### Body: 
- BlobPath (required) (string): The path of the input file in the Azure Blob Storage. The input file should contain key value pairs as per the output of DocParser component. The input file must be a JSON. Do not mention the full path of the document. Check the DocParser output for the correct format of Blob Path.
- ChunkSize (required) (integer): The token size of each text chunk.
-   ChunkOverlap (required) (integer): The number of tokens that overlap between consecutive chunks.
-   StorageAccountName (required) (string): The name of your Azure storage account where the Blob Storage container resides.
-   StorageContainerName (required) (strings): The name of your container in Azure storage account where the document is stored. This parameter is expected to deprecate soon and we will only use BlobURL.
-   OutputFileName (optional) (string): A custom name for the output JSON file containing split (chunked) data. If this param is not provided then the output file name will be the same as "BlobPath" parameter, with "/" replaced by "_" and suffixed with the request time.
-   OutputContainerName (optional) (string):  The name of the Azure Blob Storage container where the split (chunked) JSON output will be stored. If this param is not provided then the file is stored in the container specified in "StorageContainerName" parameter.
-   OutputFilePath (optional) (string):Path for storing the output file in the Azure Blob Storage. If this param is not provided then the file path will become NULL.

### Result Params
-   Success: If the API call has successfully found the files and returned the output, it updates the success along with the following parameters: 
    -   Status: Indicated the operations status ("Success" or "Failure").
    -   Output: Provides the Azure Blob Storage path("Blobpath") of the split (chunked) document upon success.
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
**Header:**
```json
"accept": "application/json"
"StorageAccountKey": "xxxx"
"Content-Type": "application/json"
```

**Body:**
```json
{
  "BlobPath": "inputtest/docparser/ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json",
  "ChunkSize": 1536,
  "ChunkOverlap": 100,
  "StorageAccountName": "eprbutdz5j75nvoghmx5",
  "StorageContainerName": "inputtest"
}
```

**Result:**
```json
{
  "Output": {
    "BlobPath": "inputtest/inputtest_docparser_ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json_2024-11-04 21:21:31.json"
  },
  "Status": "Success"
}
```
### Example with output params

**Header:**
```json
"accept": "application/json"
"StorageAccountKey": "xxxx"
"Content-Type": "application/json"
```

**Body:**
```json
{
  "BlobPath": "inputtest/docparser/ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json",
  "ChunkSize": 1536,
  "ChunkOverlap": 100,
  "StorageAccountName": "eprbutdz5j75nvoghmx5",
  "StorageContainerName": "inputtest",
  "OutputFileName": "outputfilename",
  "OutputContainerName": "outputtest",
  "OutputFilePath": "splittertest/documents"
}
```

**Result:**
```json
{ 
"Output": 
	{
	"BlobPath":"outputtest/splittertest/documents/outputfilename.json"},
	"Status": "Success" 
	}
}
```

```
## Documentation

## Process flow
![alt text](https://satechhubdevjapaneast001.blob.core.windows.net/workflows/Splitter.png)
