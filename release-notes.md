---

copyright:
  years: 2015, 2017
lastupdated: "2017-08-31"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Release notes
{: #top}

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to (migrate)[/docs/services/discovery/migrate-dcs-rr.html].  **Note:** May not apply in select Dedicated environments.

The release notes document the new features and changes that were included with each release of the {{site.data.keyword.retrieveandrankshort}} service. The information includes known limitations of the service.
{: shortdesc}

## Known issues
{: #known}

-   The cluster sizing enhancements introduced on 30 June 2016 are not fully enabled. Cluster sizes of 8 to 14 units are not currently available. You can still resize a cluster of 1 to 7 units provided that your `solrconfig.xml` file contains the following new line, as listed in [Configuring the {{site.data.keyword.retrieveandrankshort}} service](/docs/services/retrieve-and-rank/configure.html).

    ```xml
    <queryParser name="serializedQueryParser"
    class="com.ibm.watson.hector.plugins.qparser.WatsonPSSOLRQParserPlugin"/>
    ```
    {: codeblock}

    The example `solrconfig.xml` file provided with the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/blank-example-solr-config.zip" download="blank-example-solr-config.zip">`blank-example-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> configuration has been updated with the required new entry.

<!--

    The cluster sizing enhancements introduced on 30 June 2016 require a new entry in the `solrconfig.xml` file to enable cluster sizes of 8 to 14 units, although the entry can and should be applied to all clusters regardless of size. The entry is:

    ```xml
    <queryParser name="serializedQueryParser" class="com.ibm.watson.hector.plugins.qparser.WatsonPSSOLRQParserPlugin" />
    ```
    {: codeblock}

    If you have an existing configuration for which you want to enable larger cluster sizes and do not want to edit your `solrconfig.xml` file manually, you can run the following command to update the configuration:

    ```bash
    curl -X POST -u "{username}":"{password}"
    -d '{"add-queryparser":{"name":"serializedQueryParser","class":"com.ibm.watson.hector.plugins.qparser.WatsonPSSOLRQParserPlugin"}}'
    "{gateway}/v1/solr_clusters/{solr_cluster_id}/solr/{collection_name}/config" -H 'Content-type:application/json'
    ```
    {: pre}

-->

-   The `Try it out!` button on the [Upload Solr configuration ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://watson-api-explorer.mybluemix.net/apis/retrieve-and-rank-v1#!/solr_clusters/uploadConfig){: new_window} (`/v1/solr_clusters/{solr_cluster_id}/config/{config_name}`) command in the {{site.data.keyword.retrieveandrankshort}} API explorer does not work. This is due to a limitation in the [Swagger ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://swagger.io/){: new_window} code that is used to create the API explorer. The limitation prevents zip files from being uploaded.

    To upload a Solr configuration, see the [Upload Solr configuration ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/#upload_config){: new_window} example in the API reference.

## 21 September 2016
{: #21sep2016}

The HTTP request method for the search and search-and-rank methods has been changed from `GET` to `POST`. The `POST` request method does not encounter errors that the `GET` method can sometimes encounter with Solr searches. The change is documented in the {{site.data.keyword.retrieveandrankshort}} [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/){: new_window} and [API explorer ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://watson-api-explorer.mybluemix.net/apis/retrieve-and-rank-v1){: new_window}.

## 3 August 2016
{: #3aug2016}

The [{{site.data.keyword.retrieveandrankshort}} Web Interface ![External link icon](../../icons/launch-glyph.svg "External link icon")](/docs/services/retrieve-and-rank/ranker-tooling.html){: new_window} is now compatible with a ranker trained by using the `curl` and `train.py` commands described in the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html). To enable compatibility, use the refreshed versions of the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/blank-example-solr-config.zip" download="blank-example-solr-config.zip">`blank-example-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> and, if needed, the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/cranfield-solr-config.zip" download="cranfield-solr-config.zip">`cranfield-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> files to configure your cluster.

## 30 June 2016
{: #30jun2016}

-   The service has new APIs to show Solr cluster statistics and to resize Solr clusters. See [Sizing your {{site.data.keyword.retrieveandrankshort}} cluster](/docs/services/retrieve-and-rank/using-solr.html#sizingCluster) for details. Be sure to note the limitations of the resizing API before attempting to resize a cluster.
-   The service now has the ability to display the ranker-confidence score for the results of a query to a trained ranker. See [Working with ranker confidence scores](/docs/services/retrieve-and-rank/training-data.html#ranker-conf) for information.
-   The service has introduced tooling to simplify working with the service. See the following documentation for details:
    -   [Enhanced information retrieval](/docs/services/retrieve-and-rank/using-enhanced-retrieval.html) provides command-line tools for setting up a {{site.data.keyword.retrieveandrankshort}} instance and for uploading documents to a search index.
    -   The [{{site.data.keyword.retrieveandrankshort}} Web Interface](/docs/services/retrieve-and-rank/ranker-tooling.html) provides a web interface for setting up a {{site.data.keyword.retrieveandrankshort}} instance, uploading documents, and answering questions to train a ranker.
-   The service offers enhanced support (tokenization) for German in addition to previously supported languages. See [Configuring the {{site.data.keyword.retrieveandrankshort}} service](/docs/services/retrieve-and-rank/configure.html#nls) for details.

## Older releases
{: #previous}

-   [29 April 2016](#29apr2016)
-   [28 March 2016](#28mar2016)
-   [10 February 2016](#10feb2016)
-   [4 February 2016](#4feb2016)
-   [14 January 2016](#14jan2016)
-   [7 January 2016](#7jan2016)

### 29 April 2016
{: #29apr2016}

-   The service now provides enhanced support (tokenization) for French and Italian as well as for the languages listed in the updates for [7 January 2016](#7jan2016). See [Configuring the {{site.data.keyword.retrieveandrankshort}} service](/docs/services/retrieve-and-rank/configure.html#nls) for details.
-   A new sample application is available for the service. See [About the sample application](/docs/services/retrieve-and-rank/index.html#sample_app) for details.

### 28 March 2016
{: #28mar2016}

-   The service now provides enhanced support (tokenization and lemmatization) for Japanese as well as for the languages listed in the updates for [7 January 2016](#7jan2016).
-   The documentation incorrectly listed the maximum size of a ranker's training data set as 300 GB. The correct maximum size is 300 MB. The documentation has been corrected.
-   The output listed in the API explorer application for the **List Solr configurations** method (`GET /v1/solr_clusters/{solr_cluster_id}/config`) does not match the current output of the method. Although the syntax listed in the API explorer application is correct according to the specification, a known software defect displays the output incorrectly.

    The API explorer application lists the following output format for the method:

    ```javascript
    {
      "solr_configs": [
        {
          "config_name": "string"
        }
      ]
    }
    ```
    {: codeblock}

    The current version of the method lists the following output format:

    ```javascript
    {
      "solr_configs": [
        "string"
      ]
    }
    ```
    {: codeblock}

    where `string` is the name or names of the Solr configuration or configurations. The output of the method is planned to be corrected in an upcoming release.

### 10 February 2016
{: #10feb2016}

The service now enforces Apache Solr's recommendation that newly created collection names use only alphanumeric characters (**a-z**, **A-Z**, and **0-9**), periods (**.**), and underscores (**_**). If you use a collection name that includes other characters, the service returns an error.

The character constraints apply only to newly created collections. If you already have collections that include other characters such as dashes (**-**), you can continue to administer them by using the existing APIs without changing their names or deleting and re-creating them.

### 4 February 2016
{: #4feb2016}

The service's API reference includes information about the Java API for the {{site.data.keyword.retrieveandrankshort}} service.

### 14 January 2016
{: #14jan2016}

The documentation incorrectly stated that the maximum number of Solr servers in a cluster was 14. The correct maximum number is seven servers in a cluster. The documentation was updated with the correct information.

### 7 January 2016
{: #7jan2016}

-   The service offers enhanced support (tokenization and lemmatization) for the following languages in addition to English:

    -   Arabic (`ar`)
    -   Portuguese (`ptbr`)
    -   Spanish (`es`)

    For information about using these languages with the service, see [Configuring {{site.data.keyword.retrieveandrankshort}}](/docs/services/retrieve-and-rank/configure.html#nls). The service supports the same languages as standard Solr, but it provides enhanced support only for the languages listed above.
-   The service no longer blocks the `RELOAD` call from the Solr Collections API.
