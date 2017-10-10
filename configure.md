---

copyright:
  years: 2015, 2017
lastupdated: "2017-09-02"

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

# Configuring the Retrieve and Rank service
{: #top}

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to [migrate](/docs/services/discovery/migrate-dcs-rr.html).  **Note:** May not apply in select Dedicated environments.

You can use the sample `schema.xml` and `solrconfig.xml` files as a starting point for your own configuration with the {{site.data.keyword.retrieveandrankfull}} service.
{: shortdesc}

## Modify the configuration
{: #schema}

You configure the retrieve component by updating the Apache Solr configuration. In most cases, you need to update only the default `schema.xml` configuration file, which describes the structure of your data. The settings in this file identify the fields that your documents contain and how you index and query those fields. You configure the {{site.data.keyword.retrieveandrankshort}} service to help rank your search results. For details about how to configure Solr in general, search for schema design in the [Apache Solr reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cwiki.apache.org/confluence/display/solr/Apache+Solr+Reference+Guide){: new_window}.

To define fields for your documents, perform the following steps:

1.  Download and extract the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/blank-example-solr-config.zip" download=" blank-example-solr-config.zip">`blank-example-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> configuration set. This configuration is based on the default Solr configuration files and includes updates to take advantage of the {{site.data.keyword.retrieveandrankshort}} service.
1.  Configure field types by defining them in the `schema.xml` file. For each field that you want to make available in the query, update the field types as follows:
    1.  Open the `schema.xml` file in a text editor.
    1.  Find the line that defines the `id` field: <!-- This should be line 113 -->

        ```xml
        <field name="id" type="string" indexed="true" stored="true" required="true"/>
        ```
        {: codeblock}

    1.  Modify the fields below this line to match those that are in your database.

        -   For text fields in English, you can use the example `watson_text_en` field type. That field type is configured to be included with the {{site.data.keyword.retrieveandrankshort}} query parser. For example, to define a field called `full_name`, include the following line:

        ```xml
        <field name="full_name" type="watson_text_en" indexed="true" stored="true"/>
        ```
        {: codeblock}

        -   If you need a field type other than the `watson_text_en` field, you can define your own field type.

    To see an example schema that works with the service, download the sample configuration for the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html#create-collection).
    {: tip}

1.  Save the `schema.xml` file.

After you configure the {{site.data.keyword.retrieveandrankshort}} service, index your documents. For more information, see the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html#create-collection).

## The watson_text_en field type
{: #sample_config}

You can use the {{site.data.keyword.retrieveandrankshort}} query parser to train a rank learner and improve the ranking of your search results. To make the plug-in available to your query, update your Solr configuration files.

The `watson_text_en` field type is used for text fields in English. The field type is configured to be included with the {{site.data.keyword.retrieveandrankshort}} query parser. This custom field type overrides the need to include the three attributes that are required when you define your own field.

```xml
<fieldType name="watson_text_en" indexed="true" stored="true" class="com.ibm.watson.hector.plugins.fieldtype.WatsonTextField">
  <analyzer type="index">
    <tokenizer class="solr.StandardTokenizerFactory"/>
    <filter class="solr.LowerCaseFilterFactory"/>
    <filter class="solr.EnglishPossessiveFilterFactory"/>
    <filter class="solr.KeywordMarkerFilterFactory" protected="protwords.txt"/>
    <filter class="solr.PorterStemFilterFactory"/>
  </analyzer>
  <analyzer type="query">
    <tokenizer class="solr.StandardTokenizerFactory"/>
    <filter class="solr.StopFilterFactory"
      ignoreCase="true"
      words="lang/stopwords_en.txt"/>
    <filter class="solr.LowerCaseFilterFactory"/>
    <filter class="solr.EnglishPossessiveFilterFactory"/>
    <filter class="solr.KeywordMarkerFilterFactory" protected="protwords.txt"/>
    <filter class="solr.PorterStemFilterFactory"/>
  </analyzer>
</fieldType>
```
{: codeblock}

## Define your own field types
{: #fieldtypes}

You might need to define your own text field if the `watson_text_en` type is not sufficient. For each field that you want to make available to the {{site.data.keyword.retrieveandrankshort}} query parser, update the field types in your `schema.xml` file.

1.  Define the field type that you want to query:
    1.  In each field type, make sure that the following attribute values of the `fieldType` element are set:
        -   `omitNorms="false"`
        -   `omitTermFreqAndPositions="false"`
        -   `storeOffsetsWithPositions="true"`
    1.  Index all stop words and selectively filter them out at runtime to provide better results.

    The following example illustrates these settings:

    ```xml
    <fieldType name="contentsFieldType" indexed="true"
      stored="false" omitNorms="false" omitTermFreqAndPositions="false"
      storeOffsetsWithPositions="true" class="solr.TextField">
      <analyzer type="index">
        <tokenizer class="solr.StandardTokenizerFactory"/>
        <filter class="solr.StandardFilterFactory"/>
        <filter class="solr.EnglishPossessiveFilterFactory"/>
        <filter class="solr.LowerCaseFilterFactory"/>
        <filter class="solr.StopFilterFactory"
          ignoreCase="true" words="stopwords.txt" />
        <filter class="solr.PorterStemFilterFactory"/>
      </analyzer>
    </fieldType>
    ```
    {: codeblock}

    To provide the query parser with the field types to use when generating queries, you also list the field types as values in the `textFieldTypes` parameter. The parameter is part of the `fcQueryParser` query parser in the `solrconfig.xml` file.
1.  *(Optional)* Configure sets of fields so that you can search or exclude fields and improve performance.

    For example, if your documents have fields called `title`, `abstract`, and `body`, you can configure these fields as a set with the same field type. You can also create a separate field, `fullDoc`, to combine these other fields and give it a different field type.

    In your query, you can exclude the `fullDoc` field if it provides no value in results. You can also exclude any of the individual fields.

    The following example illustrates these elements:

    ```xml
    <field name="title" type="text_en"/>
    <field name="abstract" type="text_en"/>
    <field name="body" type="text_en"/>
    <field name="fullDoc" type="text_en2"/>

    <copyField source="title" dest="fullDoc"/>
    <copyField source="abstract" dest="fullDoc"/>
    <copyField source="body" dest="fullDoc"/>
    ```
    {: codeblock}

    To configure a set of fields, follow these steps:
    1.  Define multiple fields with one type for them all.
    1.  Define a separate field with a different type.
    1.  Aggregate those fields to a separate field type with the `copyField` element.

### Configure the solrconfig.xml file
{: #solrconfig}

If you define custom field types, list them in your `solrconfig.xml` file. The `solrconfig.xml` file must include entries for the `fcselect` request handler, which provides the ranker functionality for the {{site.data.keyword.retrieveandrankshort}} service. The entries are shown in the following example.

In the `textFieldType` attribute of the `str` element, list the field types to use the {{site.data.keyword.retrieveandrankshort}} query parser. Each value in `textFieldType` must match the `name` attribute of the field type that you configured in the `schema.xml` file:

```xml
<queryParser name="fcQueryParser" class="com.ibm.watson.hector.plugins.qparser.WatsonFCQParser">
  <str name="textFieldTypes">contentsFieldType</str>
</queryParser>
<searchComponent name="fcFeatureGenerator" class="com.ibm.watson.hector.plugins.ss.FCFeatureGeneratorComponent"/>

<queryParser name="serializedQueryParser" class="com.ibm.watson.hector.plugins.qparser.WatsonPSSOLRQParserPlugin"/>

<!-- feature centric document search -->
<requestHandler name="/fcselect" class="com.ibm.watson.hector.plugins.ss.FCSearchHandler">
  <lst name="defaults">
    <str name="defType">fcQueryParser</str>
  </lst>
  <arr name="last-components">
    <str>fcFeatureGenerator</str>
  </arr>
</requestHandler>
```
{: codeblock}

The line that reads `<queryParser name="serializedQueryParser" class="com.ibm.watson.hector.plugins.qparser.WatsonPSSOLRQParserPlugin"/>` supports cluster resizing. For more information, see [Sizing your {{site.data.keyword.retrieveandrankshort}} cluster](/docs/services/retrieve-and-rank/using-solr.html#sizingCluster) and the [Release notes](/docs/services/retrieve-and-rank/release-notes.html).

## Enable language support (optional)
{: #nls}

The {{site.data.keyword.retrieveandrankshort}} service provides enhanced support for English and several other languages by using tokenization and lemmatization as well as other text analytics features. The service uses these features as part of Solr's indexing and querying process in a {{site.data.keyword.retrieveandrankshort}} implementation. For information on tokens and lemmas, see the [Apache Solr reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cwiki.apache.org/confluence/display/solr/Apache+Solr+Reference+Guide){: new_window}.

The {{site.data.keyword.retrieveandrankshort}} service supports all of the same languages that standard Solr supports. However, the service provides enhanced support (tokenization and lemmatization) for only specific languages as listed in the following table.

<table style="width:80%">
<caption>Table 1. Language support</caption>
<tr>
  <th style="width:50%; text-align:left">Language</th>
  <th style="width:25%; text-align:center">Tokens</th>
  <th style="width:25%; text-align:center">Lemmas</th>
</tr>
<tr>
  <td style="text-align:left">
    English (<code>en</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    Yes
  </td>
</tr>
<tr>
  <td style="text-align:left">
    Spanish (<code>es</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    Yes
  </td>
</tr>
<tr>
  <td style="text-align:left">
    Brazilian Portuguese (<code>ptbr</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    No
  </td>
</tr>
<tr>
  <td style="text-align:left">
    Japanese (<code>ja</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    No
  </td>
  </tr>
<tr>
  <td style="text-align:left">
    Arabic (<code>ar</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    No
  </td>
</tr>
<tr>
  <td style="text-align:left">
    French (<code>fr</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    No
  </td>
</tr>
<tr>
  <td style="text-align:left">
    Italian (<code>it</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    No
  </td>
</tr>
<tr>
  <td style="text-align:left">
    German (<code>de</code>)
  </td>
  <td style="text-align:center">
    Yes
  </td>
  <td style="text-align:center">
    No
  </td>
</tr>
</table>

You can enable language support by modifying the Solr `schema.xml` file, as described in [Modify the configuration](#schema). For each language that you are using, add an XML stanza such as the following to `schema.xml`. For each language, you must specify the `language` code and, optionally, specify Boolean values for the `tokens` and `lemmas` parameters. The value for the `tokens` parameter indicates whether or not to extract the tokens. The value of the `lemmas` parameter indicates whether or not to extract the lemmas.

```xml
<fieldType name="text_en_tokens_and_lemmas" class="solr.TextField">
  <analyzer type="index">
    <tokenizer class="com.ibm.watson.search.analysis.sire.SireTokenizerFactory" tokens="true" lemmas="true" language="en"/>
  </analyzer>
  <analyzer type="query">
    <tokenizer class="com.ibm.watson.search.analysis.sire.SireTokenizerFactory" tokens="true" lemmas="true" language="en"/>
  </analyzer>
</fieldType>
```
{: codeblock}

For example, to add support for Spanish and Arabic, add the following to the `schema.xml` file:

```xml
<fieldType name="text_es_tokens_and_lemmas" class="solr.TextField">
  <analyzer type="index">
    <tokenizer class="com.ibm.watson.search.analysis.sire.SireTokenizerFactory" tokens="true" lemmas="true" language="es"/>
  </analyzer>
  <analyzer type="query">
    <tokenizer class="com.ibm.watson.search.analysis.sire.SireTokenizerFactory" tokens="true" lemmas="true" language="es"/>
  </analyzer>
</fieldType>

<fieldType name="text_ar_tokens_and_lemmas" class="solr.TextField">
  <analyzer type="index">
    <tokenizer class="com.ibm.watson.search.analysis.sire.SireTokenizerFactory" tokens="true" lemmas="false" language="ar"/>
  </analyzer>
  <analyzer type="query">
    <tokenizer class="com.ibm.watson.search.analysis.sire.SireTokenizerFactory" tokens="true" lemmas="false" language="ar"/>
  </analyzer>
</fieldType>
```
{: codeblock}

If neither tokens nor lemmas are specified, the service returns only tokens. Attempting to enable lemmas for a language that does not support lemmas causes the service to throw an exception.

## What to do next
{: #what-to-do-next}

-   Create a cluster, create a collection, and add documents. For details, see the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html#create-cluster).
-   Prepare your training data. For details, see [Preparing training data](/docs/services/retrieve-and-rank/training-data.html).
