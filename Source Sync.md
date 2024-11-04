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
![alt text](<https://satechhubdevjapaneast001.blob.core.windows.net/workflows/SourceSync.jpg>)---


---

<h1 id="source-sync-api">Source Sync API</h1>
<ul>
<li><a href="#overview">Overview</a></li>
<li><a href="#get-started">Get started</a></li>
<li><a href="#api-configuration">Configuration</a></li>
<li><a href="#examples">Examples</a></li>
<li><a href="#documentation">Documentation</a></li>
</ul>
<h2 id="overview">Overview</h2>
<p>Source Sync is a component that allows the user to download files from the Source and upload these files to Target storage service.</p>
<h3 id="key-features">Key Features</h3>
<ul>
<li>Automatically adds/updates files from Source and into Target.</li>
<li>Azure Integration - Works smoothly with Azure services for secure and reliable operations.</li>
</ul>
<h4 id="q2-release">Q2 release</h4>
<ul>
<li>Added functionality of ADLS as a source along with existing SharePoint</li>
<li>New Swagger UI page with examples</li>
<li>Moved critical parameters to headers to improve security</li>
</ul>
<h2 id="get-started">Get started</h2>
<p>On deployment, the container/kubernetes will install the required packages and will start the API endpoint. The /sourcesync API should be called using POST method with the following body:</p>
<p><strong>Header for SharePoint:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
<span class="token string">"UserID"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span>
<span class="token string">"Password"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span>
<span class="token string">"TargetAdlsStorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span><span class="token punctuation">,</span>
</code></pre>
<p><strong>Body for SharePoint:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
	<span class="token string">"SourceType"</span><span class="token punctuation">:</span> <span class="token string">"SharePoint"</span><span class="token punctuation">,</span>
	<span class="token string">"DataSourceURL"</span><span class="token punctuation">:</span> <span class="token string">"https://nttdatagroup.sharepoint.com/teams/SRVS-AS-DIFT/"</span><span class="token punctuation">,</span>
	<span class="token string">"FolderName"</span><span class="token punctuation">:</span> <span class="token string">"Test_GenAI"</span><span class="token punctuation">,</span>
	<span class="token string">"RelativeURL"</span><span class="token punctuation">:</span> <span class="token string">"/teams/SRVS-AS-DIFT/SharedDocuments/Test_GenAI/Pytest/Excel_folder/TestCases-Techhub.xlsx"</span><span class="token punctuation">,</span>
	<span class="token string">"FileName"</span><span class="token punctuation">:</span> <span class="token string">"TestCases-Techhub.xlsx"</span><span class="token punctuation">,</span>
	<span class="token string">"TargetStorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"test"</span><span class="token punctuation">,</span>
	<span class="token string">"TargetAdlsStorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"storagetechhubnadev"</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Header for ADLS:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span><span class="token punctuation">,</span>
<span class="token string">"SourceAdlsStorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span>
<span class="token string">"TargetAdlsStorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span><span class="token punctuation">,</span>
</code></pre>
<p><strong>Body for ADLS:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
<span class="token string">"SourceType"</span><span class="token punctuation">:</span> <span class="token string">"ADLS"</span><span class="token punctuation">,</span>
<span class="token string">"SourceAdlsStorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"storagetechhubnadev"</span><span class="token punctuation">,</span>
<span class="token string">"TargetAdlsStorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"storagetechhubnadev"</span><span class="token punctuation">,</span>
<span class="token string">"FolderName"</span><span class="token punctuation">:</span> <span class="token string">"Test_GenAI"</span><span class="token punctuation">,</span>
<span class="token string">"RelativeURL"</span><span class="token punctuation">:</span> <span class="token string">"/test/Test_GenAI/Exploring Pytest.docx"</span><span class="token punctuation">,</span>
<span class="token string">"FileName"</span><span class="token punctuation">:</span> <span class="token string">"Exploring Pytest.docx"</span><span class="token punctuation">,</span>
<span class="token string">"TargetStorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"test-ppt-file"</span>
<span class="token punctuation">}</span>

</code></pre>
<h2 id="api-configuration">API Configuration</h2>
<h3 id="params">Params</h3>
<p><strong>Note:</strong> All parameters mentioned are case sensitive</p>
<h4 id="header">Header:</h4>
<ul>
<li>UserID (required) (string): Required when DataSource is SharePoint. User ID of the SharePoint where the documents are present. The user ID needs to have read/write access to SharePoint.</li>
<li>Password (required) (string): Required when DataSource is SharePoint. Password of the SharePoint UserID given in the previous param.</li>
<li>SourceAdlsStorageAccountKey: (required) (string): Required when DataSource is ADLS. Account Key of storage account from where the files have to be synced into the target.</li>
<li>TargetAdlsStorageAccountKey(required) (string): Account Key of target account where the files have to be synced for further processing.</li>
</ul>
<h4 id="body">Body:</h4>
<ul>
<li>SourceType (required) (string): Should be SharePoint/ADLS.</li>
<li>DataSourceURL (required) (string): Required when DataSource is SharePoint.  The URL where the SharePoint documents are present. It should contain the “/” at the end</li>
<li>FolderName (required) (list of strings): The folder in the URL which needs to be accessed. Only the folders specified will be returned.</li>
<li>RelativeURL (required) (string): Should contain the suffix after the sharepoint domain and the sitename. Ensure that the file type is also present in the filename.</li>
<li>FileName (required) (string): The name of the file to be download from source. Ensure that the file type is also present in the filename.</li>
<li>TargetStorageContainerName (required) (string): The Storage container name where the file should be stored.</li>
<li>TargetAdlsStorageAccountName (required) (string): The storage account name where the file should be stored</li>
<li>SourceAdlsStorageAccountName (required) (string): Required when DataSource is  ADLS.  The storage account name where the file should be fetched from.</li>
</ul>
<h3 id="result-params">Result Params</h3>
<ul>
<li>Success: If the API call has successfully found the files and returned the output, it updates the success along with the following parameters:
<ul>
<li>ADLSPath: Defaulted to “add”.</li>
<li>BlobURL: Specifies the URL of the file in target storage</li>
</ul>
</li>
<li>Failures: If the API call is unsuccessful, it updates the failures in the API response:
<ul>
<li>Message: Provides the reason of failure</li>
<li>Start Time: The time at which the failure occured</li>
<li>Folder: The folder in which the failure occured</li>
<li>Exception: The error message which can be used to debug and find the failure.</li>
</ul>
</li>
</ul>
<h3 id="response-codes">Response codes</h3>
<ul>
<li>200: Success</li>
<li>500: Internal server error. Occurs if the parameter values are incorrect like the foldername, URL etc.</li>
<li>400: If the parameters is incorrect or a required parameter is not found in the request body.</li>
</ul>
<h2 id="examples">Examples</h2>
<h3 id="example-for-sharepoint">Example for SharePoint</h3>
<p><strong>Header for SharePoint:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
<span class="token string">"UserID"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span>
<span class="token string">"Password"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span>
<span class="token string">"TargetAdlsStorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span><span class="token punctuation">,</span>
</code></pre>
<p><strong>Body for SharePoint:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
	<span class="token string">"SourceType"</span><span class="token punctuation">:</span> <span class="token string">"SharePoint"</span><span class="token punctuation">,</span>
	<span class="token string">"DataSourceURL"</span><span class="token punctuation">:</span> <span class="token string">"https://nttdatagroup.sharepoint.com/teams/SRVS-AS-DIFT/"</span><span class="token punctuation">,</span>
	<span class="token string">"FolderName"</span><span class="token punctuation">:</span> <span class="token string">"Test_GenAI"</span><span class="token punctuation">,</span>
	<span class="token string">"RelativeURL"</span><span class="token punctuation">:</span> <span class="token string">"/teams/SRVS-AS-DIFT/SharedDocuments/Test_GenAI/Pytest/Excel_folder/TestCases-Techhub.xlsx"</span><span class="token punctuation">,</span>
	<span class="token string">"FileName"</span><span class="token punctuation">:</span> <span class="token string">"TestCases-Techhub.xlsx"</span><span class="token punctuation">,</span>
	<span class="token string">"TargetStorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"test"</span><span class="token punctuation">,</span>
	<span class="token string">"TargetAdlsStorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"storagetechhubnadev"</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Result:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>  
<span class="token string">"Output"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>  
<span class="token string">"ADLSPath"</span><span class="token punctuation">:</span> <span class="token string">"Test_GenAI/Pytest/Excel_folder/TestCases-Techhub.xlsx"</span><span class="token punctuation">,</span>  
<span class="token string">"BlobURL"</span><span class="token punctuation">:</span><span class="token string">"https://storagetechhubnadev.blob.core.windows.net/test/Test_GenAI/TestCases-Techhub.xlsx"</span><span class="token punctuation">,</span>  
<span class="token string">"Status"</span><span class="token punctuation">:</span> <span class="token string">"Success"</span>  
<span class="token punctuation">}</span>
</code></pre>
<h3 id="example-for-adls">Example for ADLS</h3>
<p><strong>Header for ADLS:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span><span class="token punctuation">,</span>
<span class="token string">"SourceAdlsStorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span>
<span class="token string">"TargetAdlsStorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxx"</span><span class="token punctuation">,</span>
</code></pre>
<p><strong>Body for ADLS:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
<span class="token string">"SourceType"</span><span class="token punctuation">:</span> <span class="token string">"ADLS"</span><span class="token punctuation">,</span>
<span class="token string">"SourceAdlsStorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"storagetechhubnadev"</span><span class="token punctuation">,</span>
<span class="token string">"TargetAdlsStorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"storagetechhubnadev"</span><span class="token punctuation">,</span>
<span class="token string">"FolderName"</span><span class="token punctuation">:</span> <span class="token string">"Test_GenAI"</span><span class="token punctuation">,</span>
<span class="token string">"RelativeURL"</span><span class="token punctuation">:</span> <span class="token string">"/test/Test_GenAI/Exploring Pytest.docx"</span><span class="token punctuation">,</span>
<span class="token string">"FileName"</span><span class="token punctuation">:</span> <span class="token string">"Exploring Pytest.docx"</span><span class="token punctuation">,</span>
<span class="token string">"TargetStorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"test-ppt-file"</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Result</strong></p>
<pre><code>{  
"Output": {  
"ADLSPath": "Test_GenAI/Exploring Pytest.docx",  
"BlobURL": "https://storagetechhubnadev.blob.core.windows.net/test-ppt-file/Test_GenAI/Exploring%20Pytest.docx" 
},  
"Status": "Success"  
}
</code></pre>
<h2 id="documentation">Documentation</h2>
<h2 id="process-flow">Process flow</h2>
<p><img src="https://satechhubdevjapaneast001.blob.core.windows.net/workflows/SourceSync.jpg" alt="alt text"></p>

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE5MTcxMTg2XX0=
-->