# Azure Cognitive Search

## Settings
In [Portal Azure](https://portal.azure.com/), create the resources below in the sabe Resource Group.

1. Create  a new Azure AI Search Service
Price Tier: Basic.

2. Create a new Azure AI Service
* By checking this box I acknowledge that I have read and understood all the terms below: Selected

3. Create a new Storage Account


Go to your storage account, Settings > Configuration > Allow Blob anonymous access set to Enabled
Go to Data Storage > Container, Create a new Container 'carsreview', upload documentos from [inputs folder](https://github.com/faulycoelho/azure-ai-cognitive-search/tree/main/inputs).

In Settings, copy the *connectionString*

4. Go to your 'Azure AI Search Service', Click on 'Import Data'.

5. On the <b>Connect to your data page </b>, in the  <b>Data Source</b> list, select  <b>Azure Blob Storage </b>. Complete the data store details with the following values:

* <b>Data Source</b>: Azure Blob Storage
* <b>Data source name</b>: car-reviews-customer-data
* <b>Data to extract</b>: Content and metadata
* <b>Parsing mode</b>: Default
* <b>Connection string</b>: *Select Choose an existing connection. Select your storage account, select the carreviews container, and then click Select.
* <b>Managed identity authentication</b>: None
* <b>Container name</b>: this setting is auto-populated after you choose an existing connection.
* <b>Blob folder</b>: Leave this blank.
* <b>Description </b>: Reviews for Cars


Select Next: <b>Add cognitive skills (Optional)</b>.

In the <b>Attach Cognitive Services</b> section, select your Azure AI services resource.

In the <b>Add enrichments</b> section:
* Change the <b>Skillset name</b> to <b>car-skillset</b>.
* Select the checkbox <b>Enable OCR and merge all text into merged_content field</b>.
Note It’s important to select Enable OCR to see all of the enriched field options.

* Ensure that the <b>Source data field</b> is set to <b>merged_content</b>.
* Change the <b>Enrichment granularity level</b> to <b>Pages (5000 character chunks)</b>.
* Don’t select Enable incremental enrichment
* Select the following enriched fields:

 


Cognitive Skill	Parameter |	Field name
--------------------------|--------------
Detect sentiment	 	  |sentiment
Extract key phrases	 	  |keyphrases




6. Under <b> Save enrichments to a knowledge store</b>, select:
* Documents
* Pages
* Key phrases
* Entities


7. Select <b>Azure blob projections: Document</b>. A setting for Container name with the 'carsreview' container auto-populated displays. Don’t change the container name.

8. Select <b>Next: Customize target index</b>. Change the <b>Index name</b> to <b>car-index</b>.

9. Ensure that the <b>Key</b> is set to <b>metadata_storage_path</b>. Leave <b>Suggester name</b> blank and <b>Search mode</b> autopopulated.

Review the index fields’ default settings. Select <b>filterable</b> for all the fields that are already selected by default.
![image](https://github.com/faulycoelho/azure-ai-cognitive-search/assets/37049426/bf70c9b5-815f-4b09-b71e-b38c4a223513)


10. Select <b>Next: Create an indexer</b>.

11. Change the <b>Indexer name</b> to <b>car-indexer</b>.

12. Leave the <b>Schedule</b> set to <b>Once</b>.


13. Expand the <b>Advanced options</b>. Ensure that the <b>Base-64 Encode Keys</b> option is selected, as encoding keys can make the index more efficient.

14. Select Submit to create the data source, skillset, index, and indexer. The indexer is run automatically and runs the indexing pipeline, which:
* Extracts the document metadata fields and content from the data source.
* Runs the skillset of cognitive skills to generate more enriched fields.
* Maps the extracted fields to the index.

15. Return to your Azure AI Search resource page. On the left pane, under <b>Search Management</b>, select <b>Indexers</b>. Select the newly created <b>car-indexer</b>. Wait a minute, and select <b>Refresh</b> until the <b>Status</b> indicates success.


16. Select the indexer name to see more details.
![image](https://github.com/faulycoelho/azure-ai-cognitive-search/assets/37049426/54be6c13-0b90-4e08-aad9-209dc75c18a9)

<h1>Query the index</h1>

Use the Search explorer to write and test queries. Search explorer is a tool built into the Azure portal that gives you an easy way to validate the quality of your search index. You can use Search explorer to write queries and review results in JSON.

1. In your Search service’s <i>Overview page</i>, select <b>Search explorer</b> at the top of the screen.
2. Notice how the index selected is the car-index you created. Below the index selected, change the view to JSON view.

![image](https://github.com/faulycoelho/azure-ai-cognitive-search/assets/37049426/89679bf7-0a62-4234-8f12-fe4734d9ec71)

In the <b>JSON query editor</b> field, copy and paste:
```
{
    "search": "*",
    "count": true
}
 ```
1. Select <b>Search</b>. The search query returns all the documents in the search index, including a count of all the documents in the <b>@odata.count</b> field. The search index should return a JSON document containing your search results.

2. Now let’s filter by sentiment. In the <b>JSON query editor</b> field, copy and paste
```
{
  "search": "sentiment:'positive'",
  "count":true  
}
```

3. Select <b>Search</b>. The query searches all the documents in the index and filters for reviews with a Positve sentiment. You should see 9 in the @odata.count field.

