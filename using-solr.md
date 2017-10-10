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

# Interaction with Standard Solr
{: #top}

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to [migrate](/docs/services/discovery/migrate-dcs-rr.html).  **Note:** May not apply in select Dedicated environments.

This page lists differences between standalone Solr and the Solr-as-a-Service functionality provided by the {{site.data.keyword.retrieveandrankshort}} service. Not all standard Solr operations and options are supported by the {{site.data.keyword.retrieveandrankshort}} service.
{: shortdesc}

## Sizing your Retrieve and Rank cluster
{: #sizingCluster}

A {{site.data.keyword.retrieveandrankshort}} cluster consists of one to seven <!-- 14 --> *units* on {{site.data.keyword.Bluemix_notm}}. In {{site.data.keyword.retrieveandrankshort}} terms, a *unit* is an allocation of RAM and storage as follows:

-   4 GB of RAM
-   32 GB of disk storage

The term *cluster size* in {{site.data.keyword.retrieveandrankshort}} refers to the number of units in a specific {{site.data.keyword.retrieveandrankshort}} cluster. The cluster size, in units, determines the amount of resources available to the Retrieve (Solr) component.

Creating a {{site.data.keyword.retrieveandrankshort}} cluster that is larger than the free cluster requires you to sign up for a paid service plan. For information about pricing, see the {{site.data.keyword.retrieveandrankshort}} [Pricing Plans ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.ng.bluemix.net/catalog/services/retrieve-and-rank/){: new_window} on {{site.data.keyword.Bluemix_notm}}.

### Important usage notes

You need to be aware of the following usage considerations:

-   The maximum number of units in a cluster is currently seven.
-   When adding documents to your collection, you can use approximately half of the cluster's storage capacity. For example, a cluster with the maximum number of seven units has a storage capacity of approximately 224 GB and can handle an index size of approximately 110 GB.
-   The units and measurements discussed in this section apply only to the Retrieve (Solr) component of the {{site.data.keyword.retrieveandrankshort}} service. They do not apply to rankers. Rankers have different requirements, which are listed in [Preparing training data](/docs/services/retrieve-and-rank/training-data.html). Similarly, {{site.data.keyword.retrieveandrankshort}} units refer only to instances of the {{site.data.keyword.IBM_notm}} {{site.data.keyword.watson}} {{site.data.keyword.retrieveandrankshort}} service; no other {{site.data.keyword.watson}} Developer Cloud services currently use the unit/cluster sizing pattern.
-   You might need to experiment with different cluster sizes before determining the optimal size for your solution. As a general rule, your indexed documents can use approximately half the amount of disk space available to the cluster. For example, if you have a one-unit cluster with 32 GB of storage, your search index can be approximately 16 GB in size. Larger indexes require larger clusters.
-   The free cluster that you can create to test the {{site.data.keyword.retrieveandrankshort}} demo application is a single reduced-size unit consisting of a maximum of 50 MB of disk storage. It does not guarantee any specific amount of RAM. The free cluster is meant only to run the demonstration application or small proof-of-concept applications. It cannot be used as a unit in a paid {{site.data.keyword.retrieveandrankshort}} cluster. *It is not intended for production use.*

### Getting usage statistics for your Solr cluster
{: #get_stats}

You can obtain disk-usage and memory-usage statistics for your Solr cluster by using the following cURL command:

```bash
curl -u "{username}":"{password}"
"https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/stats"
```
{: pre}

Common uses for cluster statistics include the following:

-   Verifying the capacity of a new or resized cluster (see [Resizing your Solr cluster](#resize) for information on resizing a cluster).
-   Checking the remaining capacity on a cluster to which you have added a new set of documents.
-   Checking the remaining capacity on a cluster that seems to be getting less responsive. The remaining capacity might be low, in which case you need to resize (expand) your cluster.
-   Standard monitoring of infrastructure components.
-   Troubleshooting a problematic cluster.

### Resizing your Solr cluster
{: #resize}

You can resize your Solr cluster by adding more units to it by using the following cURL command:

```bash
curl -X PUT -u "{username}":"{password}"
-H "Content-Type: application/json"
-d '{ "cluster_size": "{new_cluster_size}" }'
"https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/cluster_size"
```
{: pre}

For example, to resize a one-unit cluster to two units, use the following command:

```bash
curl -X PUT -u "{username}":"{password}"
-d '{ "cluster_size": "2" }'
-H "Content-Type: application/json"
"https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/cluster_size"
```
{: pre}

The resize operation currently has the following limitations:

-   You cannot resize a free cluster to a higher-capacity paid cluster. This support is expected in an upcoming release.
-   You cannot resize a paid cluster to a free cluster. No support is planned for this operation.
-   The resize operation is asynchronous and does not issue progress or completion messages. You can monitor its progress as described in [Checking the status of a cluster-resize operation](#check_resize_status).
-   You can resize a cluster downward; however, be *extremely careful* when planning and executing such an operation. Obtain [usage statistics](#get_stats) at every step of the operation to ensure that your downsized cluster can handle the load of your current cluster.

If you start a resize operation and then want to cancel it (for example, if you realize that you issued the resize operation against the wrong cluster), you can attempt to do so by issuing another resize method with the cluster size set to the cluster's initial value. Because resizing is an asynchronous operation, an attempt to cancel might or might not be successful. After a resize operation begins, it cannot be cancelled.

For example, to attempt to cancel the resize operation listed above, issue the following cURL command:

```bash
curl -X PUT -u "{username}":"{password}"
-H "Content-Type: application/json"
-d '{ "cluster_size": "1" }'
"https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/cluster_size"
```
{: pre}

To determine the success or failure of an attempt to cancel a resize operation, use the method described in [Checking the status of a cluster-resize operation](#check_resize_status).

### Checking the status of a cluster-resize operation
{: #check_resize_status}

Resizing a cluster is an asynchronous operation. To check the status of a resize operation, including when it has completed, use the following cURL command:

```bash
curl -u "{username}":"{password}"
"https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/cluster_size"
```
{: pre}

The output of the command includes the cluster ID, the current cluster size, the target cluster size, and a status message.

## High availability
{: #ha}

Many organizations require high availability for mission-critical solutions. The {{site.data.keyword.retrieveandrankshort}} service automatically provides high availability for Solr and the overall service, with no configuration or customization needed by you. Each {{site.data.keyword.retrieveandrankshort}} cluster runs in Solr's high-availability mode, meaning that each cluster is part of an active-active pair. During normal processing, both clusters in the pair actively process requests. If one of the clusters in the pair stops working for any reason, the remaining active cluster continues processing to provide uninterrupted service.

## Error messages
{: #errormsgs}

All of the methods provided by the {{site.data.keyword.retrieveandrankshort}} service interact with the Solr service to a greater or lesser degree. If Solr throws an error when you use these methods, the {{site.data.keyword.retrieveandrankshort}} service returns the error to you or your application directly, without any parsing. This enables standard Solr logs and applications to process the errors. For information about creating Solr applications with standard error handling, see the [Apache Solr Reference Guide ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cwiki.apache.org/confluence/display/solr/Client+APIs){: new_window}.

## Incompatible Solr operations
{: #incompatible}

Most of the Solr APIs are supported by the {{site.data.keyword.retrieveandrankshort}} service. However, some operations are incompatible. When an operation is not allowed, the service returns an error response.

### Blocked API calls

The following REST API calls are blocked when you use Java to issue a call to a Solr API through the {{site.data.keyword.retrieveandrankshort}} service:

-   Content stream operations that use the `stream`, `stream.url`, or `stream.file` parameters.
-   All calls to the Solr Collections API except `CREATE`, `DELETE`, `LIST`, and `RELOAD`.

### Elements that are not allowed in uploaded Solr configurations
{: #upload_config}

Uploading a Solr configuration file that includes any of the following elements causes the upload to fail:

-   Property `enableRemoteStreaming` set to `true:`

    ```xml
    <requestParsers enableRemoteStreaming="true" />
    ```
    {: codeblock}

-   Any elements of the following form:

    ```xml
    <updateHandler><listener ... /></updateHandler>
    ```
    {: codeblock}

-   Configs that do not include the `<updateLog />` element.
-   An overwritten `updateLog` directory, for example:

    ```xml
    <updateLog><str name="dir">${OVERWRITTEN}</str></updateLog>
    ```
    {: codeblock}

    The value must be `${solr.ulog.dir:}`.
-   An overwritten Solr data directory, for example:

    ```xml
    <dataDir>${OVERWRITTEN}</dataDir>
    ```
    {: codeblock}

    The value must be `${solr.data.dir:}`.
-   Any `<jmx ... />` elements.
-   The use of `AdminHandler`, for example:

    ```xml
    <requestHandler name="/admin/" class="solr.admin.AdminHandlers" />
    ```
    {: codeblock}

-   The use of `ReplicationHandler`, for example:

    ```xml
    <requestHandler name="/replication/" class="solr.ReplicationHandler" />
    ```
    {: codeblock}

### Elements that are not allowed when you update a Solr configuration
{: #update_config}

If you include any of the following elements when you update a configuration, the operation fails:

-   `set-user-property`, `unset-user-property`, `update-listener`, `add-listener`, or `delete-listener`.
-   `set-property` or `unset-property` for the following options:
    -   `requestDispatcher.requestParsers.enableRemoteStreaming`
    -   `jmx.agentId`
    -   `jmx.serviceUrl`
    -   `jmx.rootName`
-   `add-requesthandler`, `update-requesthandler`, or `delete-requesthandler` with the following classes:
    -   `solr.admin.AdminHandlers`
    -   `solr.ReplicationHandler`

### Solr features that are disabled by the service
{: #excluded}

The {{site.data.keyword.retrieveandrankshort}} service uses a customized and preconfigured version of Solr. As a result, the following common Solr features are intentionally disabled by the service:

-   Sharding
-   Certain configuration options in the Solr `schema.xml` and `solrconfig.xml` files, as listed previously in this document
