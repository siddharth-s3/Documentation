---


---

<h1 id="text-splitter-api">Text Splitter API</h1>
<ul>
<li><a href="#overview">Overview</a></li>
<li><a href="#get-started">Get started</a></li>
<li><a href="#api-configuration">Configuration</a></li>
<li><a href="#examples">Examples</a></li>
<li><a href="#documentation">Documentation</a></li>
</ul>
<h2 id="overview">Overview</h2>
<p>Text Splitter is a component that enables users to divide a text file into smaller chunks while preserving HTML headers, table tags and metadata, using a specified chunk size and a recursive text splitting method.</p>
<h3 id="key-features">Key Features</h3>
<ul>
<li>Contextual chunking -  Chunk is split basis header tags to ensure that each chunk has a relevant semantic meaning.</li>
<li>Tokenization-Based Splitting: Ensure chunks do not exceed a specified chunk size. Each chunk has a token limit which can be specified.</li>
<li>Recursive Splitting - Further divides large chunks using a recursive method and is used if contextual chunking is unable to chunk as per chunk size specified.</li>
<li>Metadata Management - Maintains metadata for each chunk.</li>
</ul>
<h4 id="q2-release">Q2 release</h4>
<ul>
<li>New Swagger UI page with examples</li>
<li>Moved critical parameters to headers to improve security</li>
</ul>
<h2 id="get-started">Get started</h2>
<p>On deployment, the container/kubernetes will install the required packages and will start the API endpoint. The /textsplitter API should be called using POST method with the following:</p>
<p><strong>Header:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
<span class="token string">"StorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxxx"</span>
<span class="token string">"Content-Type"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
</code></pre>
<p><strong>Body:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"BlobPath"</span><span class="token punctuation">:</span> <span class="token string">"inputtest/docparser/ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json"</span><span class="token punctuation">,</span>
  <span class="token string">"ChunkSize"</span><span class="token punctuation">:</span> <span class="token number">1536</span><span class="token punctuation">,</span>
  <span class="token string">"ChunkOverlap"</span><span class="token punctuation">:</span> <span class="token number">100</span><span class="token punctuation">,</span>
  <span class="token string">"StorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"eprbutdz5j75nvoghmx5"</span><span class="token punctuation">,</span>
  <span class="token string">"StorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"inputtest"</span><span class="token punctuation">,</span>
  <span class="token string">"OutputFileName"</span><span class="token punctuation">:</span> <span class="token string">"outputfilename"</span><span class="token punctuation">,</span>
  <span class="token string">"OutputContainerName"</span><span class="token punctuation">:</span> <span class="token string">"outputtest"</span><span class="token punctuation">,</span>
  <span class="token string">"OutputFilePath"</span><span class="token punctuation">:</span> <span class="token string">"splittertest/documents"</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="api-configuration">API Configuration</h2>
<h3 id="params">Params</h3>
<p><strong>Note:</strong> All parameters mentioned are case sensitive</p>
<h4 id="header">Header:</h4>
<ul>
<li>StorageAccountKey (required) (string): The access key for the Azure Storage Account.</li>
</ul>
<h4 id="body">Body:</h4>
<ul>
<li>BlobPath (required) (string): The path of the input file in the Azure Blob Storage. The input file should contain key value pairs as per the output of DocParser component. The input file must be a JSON. Do not mention the full path of the document. Check the DocParser output for the correct format of Blob Path.</li>
<li>ChunkSize (required) (integer): The token size of each text chunk.</li>
<li>ChunkOverlap (required) (integer): The number of tokens that overlap between consecutive chunks.</li>
<li>StorageAccountName (required) (string): The name of your Azure storage account where the Blob Storage container resides.</li>
<li>StorageContainerName (required) (strings): The name of your container in Azure storage account where the document is stored. This parameter is expected to deprecate soon and we will only use BlobURL.</li>
<li>OutputFileName (optional) (string): A custom name for the output JSON file containing split (chunked) data. If this param is not provided then the output file name will be the same as “BlobPath” parameter, with “/” replaced by “_” and suffixed with the request time.</li>
<li>OutputContainerName (optional) (string):  The name of the Azure Blob Storage container where the split (chunked) JSON output will be stored. If this param is not provided then the file is stored in the container specified in “StorageContainerName” parameter.</li>
<li>OutputFilePath (optional) (string):Path for storing the output file in the Azure Blob Storage. If this param is not provided then the file path will become NULL.</li>
</ul>
<h3 id="result-params">Result Params</h3>
<ul>
<li>Success: If the API call has successfully found the files and returned the output, it updates the success along with the following parameters:
<ul>
<li>Status: Indicated the operations status (“Success” or “Failure”).</li>
<li>Output: Provides the Azure Blob Storage path(“Blobpath”) of the split (chunked) document upon success.</li>
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
<h3 id="example-with-no-output-params">Example with no output params</h3>
<p><strong>Header:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
<span class="token string">"StorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxxx"</span>
<span class="token string">"Content-Type"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
</code></pre>
<p><strong>Body:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"BlobPath"</span><span class="token punctuation">:</span> <span class="token string">"inputtest/docparser/ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json"</span><span class="token punctuation">,</span>
  <span class="token string">"ChunkSize"</span><span class="token punctuation">:</span> <span class="token number">1536</span><span class="token punctuation">,</span>
  <span class="token string">"ChunkOverlap"</span><span class="token punctuation">:</span> <span class="token number">100</span><span class="token punctuation">,</span>
  <span class="token string">"StorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"eprbutdz5j75nvoghmx5"</span><span class="token punctuation">,</span>
  <span class="token string">"StorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"inputtest"</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Result:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"Output"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"BlobPath"</span><span class="token punctuation">:</span> <span class="token string">"inputtest/inputtest_docparser_ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json_2024-11-04 21:21:31.json"</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token string">"Status"</span><span class="token punctuation">:</span> <span class="token string">"Success"</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="example-with-output-params">Example with output params</h3>
<p><strong>Header:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token string">"accept"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
<span class="token string">"StorageAccountKey"</span><span class="token punctuation">:</span> <span class="token string">"xxxx"</span>
<span class="token string">"Content-Type"</span><span class="token punctuation">:</span> <span class="token string">"application/json"</span>
</code></pre>
<p><strong>Body:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"BlobPath"</span><span class="token punctuation">:</span> <span class="token string">"inputtest/docparser/ChickfilA_food-safety-emergency-response-playbook.pdf_2024-10-04 14_07_11_.json"</span><span class="token punctuation">,</span>
  <span class="token string">"ChunkSize"</span><span class="token punctuation">:</span> <span class="token number">1536</span><span class="token punctuation">,</span>
  <span class="token string">"ChunkOverlap"</span><span class="token punctuation">:</span> <span class="token number">100</span><span class="token punctuation">,</span>
  <span class="token string">"StorageAccountName"</span><span class="token punctuation">:</span> <span class="token string">"eprbutdz5j75nvoghmx5"</span><span class="token punctuation">,</span>
  <span class="token string">"StorageContainerName"</span><span class="token punctuation">:</span> <span class="token string">"inputtest"</span><span class="token punctuation">,</span>
  <span class="token string">"OutputFileName"</span><span class="token punctuation">:</span> <span class="token string">"outputfilename"</span><span class="token punctuation">,</span>
  <span class="token string">"OutputContainerName"</span><span class="token punctuation">:</span> <span class="token string">"outputtest"</span><span class="token punctuation">,</span>
  <span class="token string">"OutputFilePath"</span><span class="token punctuation">:</span> <span class="token string">"splittertest/documents"</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Result:</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span> 
<span class="token string">"Output"</span><span class="token punctuation">:</span> 
	<span class="token punctuation">{</span>
	<span class="token string">"BlobPath"</span><span class="token punctuation">:</span><span class="token string">"outputtest/splittertest/documents/outputfilename.json"</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
	<span class="token string">"Status"</span><span class="token punctuation">:</span> <span class="token string">"Success"</span> 
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<pre><code>## Documentation

## Process flow
![alt text](https://satechhubdevjapaneast001.blob.core.windows.net/workflows/Splitter.png)
</code></pre>

