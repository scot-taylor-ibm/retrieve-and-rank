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

# Testing your enhanced information retrieval solution
{: #c_eir_testing}

After you have done the following, the {{site.data.keyword.retrieveandrankshort}} service instance has your data:

-   Created and configured the {{site.data.keyword.retrieveandrankshort}} and {{site.data.keyword.documentconversionshort}} services with Kale
-   Downloaded the Data Crawler and copied the configuration files to it
-   Uploaded your documents and run a crawl

You can test the accuracy of the enhanced information retrieval solution by doing a search for terms that you are confident will return results. Enter the following command:

```bash
kale search {field:value}
```
{: pre}

For example, `kale search body:John` returns documents from the collection that contain the string `John` in their `body` field. The fields available for search depend on the Solr configuration that you used to create the collection. In general, a configuration contains `body` and `title` fields. The command displays the names of the documents that contain the search term.

You have now completed an enhanced information retrieval solution that you can integrate with other applications or services.

## What to do next

-   See the [sample {{site.data.keyword.retrieveandrankshort}} application ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/watson/developercloud/doc/ega_docs/rnr_ega_index.shtml){: new_window} for a working application with which you can integrate your crawled data.
-   Improve your results by training the ranker in the {{site.data.keyword.retrieveandrankshort}} service. See [Preparing training data](/docs/services/retrieve-and-rank/training-data.html) for more information.
