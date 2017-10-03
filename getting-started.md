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

# Getting started tutorial
{: #sample}

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to (migrate)[/docs/services/discovery/migrate-dcs-rr.html].  **Note:** May not apply in select Dedicated environments.

This tutorial guides you through how to create and train the {{site.data.keyword.retrieveandrankfull}} service with sample data. The tutorial uses a predefined data set to demonstrate the capabilities of the service, but you can use the same steps to create a service instance that uses your own data.
{: shortdesc}

To complete the tutorial, you use the publicly available test data that is called the [Cranfield collection ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://ir.dcs.gla.ac.uk/resources/test_collections/cran/){: new_window}. The collection contains abstracts of aerodynamics journal articles, a set of questions about aerodynamics, and indicators of how relevant an article is to a question.

To complete the tutorial, you need the following:

-   **cURL.** You can install the version of cURL for your operating system from [curl.haxx.se ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://curl.haxx.se/){: new_window}. You must install the version that supports the Secure Sockets Layer (SSL) protocol. Make sure to include the installed binary file on your `PATH` environment variable.
-   **Python version 2.** To check whether Python is installed, enter `python --version` at a command prompt and ensure that the version number starts with `2`. If you need to install Python, see [Downloading Python ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://wiki.python.org/moin/BeginnersGuide/Download){: new_window}.

## Step 1: Log in, create the service, and get your credentials
{: #credentials}

If you already know the credentials for your {{site.data.keyword.retrieveandrankshort}} service instance, skip this step.
{: tip}

1.  Go to the [{{site.data.keyword.retrieveandrankshort}} service ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/catalog/services/retrieve-and-rank/){: new_window} and either sign up for a free Bluemix account or log in.
1.  After you log in, enter `retrieve-and-rank-tutorial` in the **Service name field** of the {{site.data.keyword.retrieveandrankshort}} page. Click **Create**.
1.  Copy your credentials:
    1.  Click **Service credentials**.
    1.  Click **View credentials** under **Actions**.
    1.  Copy the `username` and `password` values.

## Step 2: Create a cluster
{: #create-cluster}

To use the {{site.data.keyword.retrieveandrankshort}} service, you must create a Solr cluster. A Solr cluster manages your search collections, which you create later in the tutorial.

1.  Issue the following cURL command to call the "Create Solr cluster" method. Replace `{username}` and `{password}` with the service credentials you copied in Step 1:

    ```bash
    curl -X POST -u "{username}":"{password}" \
    -d "" \
    "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters"
    ```
    {: pre}

    The response includes the cluster ID.
1.  Copy the `solr_cluster_id`. You need it later in the tutorial.

The response also includes the cluster availability status. The cluster must be ready before you can use it. Continue with the tutorial, which checks the status later.

The free cluster that you can create to test the {{site.data.keyword.retrieveandrankshort}} demo application is a single reduced-size unit consisting of a maximum of 50 MB of disk storage. It does not guarantee any specific amount of RAM. The free cluster is meant only to run the demonstration application or small proof-of-concept applications. It cannot be used as a unit in a paid {{site.data.keyword.retrieveandrankshort}} cluster. *It is not intended for production use.* See [Sizing your {{site.data.keyword.retrieveandrankshort}} cluster](/docs/services/retrieve-and-rank/using-solr.html#sizingCluster) for more information.

## Step 3: Create a collection and add documents
{: #create-collection}

A Solr collection is a logical index of the data in your documents. A collection is a way to keep data separate in the cloud. In this step, you create a collection, associate it with a configuration, and upload and index your documents.

1.  Check the status of your cluster. Issue the following command to retrieve the status of the cluster that you created in Step 2. Replace `{username}`, `{password}`, and `{solr_cluster_id}` with the information you copied earlier:

    ```bash
    curl -u "{username}":"{password}" \
    "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}"
    ```
    {: pre}

    If the `solr_cluster_status` is `READY`, you can create a collection. If it is not ready, check the status every few minutes.
1.  Download the sample <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/cranfield-solr-config.zip" download="cranfield-solr-config.zip">`cranfield-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> configuration set. The Solr configuration identifies how to index the documents so that you can search the important fields. For the tutorial, you edited the default Solr configuration files to work with the Cranfield collection.
1.  Issue these commands to create a collection:
    1.  Upload the sample configuration that you downloaded in Step 2. Name the configuration `example_config`:

        ```bash
        curl -X POST -u "{username}":"{password}" \
        -H "Content-Type: application/zip" \
        --data-binary @{path_to_file}/cranfield-solr-config.zip \
        "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/config/example_config"
        ```
        {: pre}

    1.  Create a collection that is named `example_collection` and associate it with the `example_config` configuration:

        ```bash
        curl -X POST -u "{username}":"{password}" \
        -d "action=CREATE&name=example_collection&collection.configName=example_config" \
        "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/solr/admin/collections"
        ```
        {: pre}

        You now have a Solr cluster that holds a collection and configuration. Your Solr instance is ready for documents.
1.  Add the documents that are to be searched:
    1.  Download the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/cranfield-data.json" download="cranfield-data.json">`cranfield-data.json` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> file. For this tutorial, you converted the Cranfield collection documents to JSON format.
    1.  Issue the following command to upload the `cranfield-data.json` data to the `example_collection` collection. Replace `{username}`, `{password}`, `{solr_cluster_id}`, and `{path_to_file}` with your information:

        ```bash
        curl -X POST -u "{username}":"{password}" \
        -H "Content-Type: application/json" \
        --data-binary @{path_to_file}/cranfield-data.json \
        "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/solr/example_collection/update"
        ```
        {: pre}

    > **Important:** If you use the `cranfield-data.json` sample file as a model for adding your own data, be sure to include the commit statement at the end of your file.

## Step 4: Create and train the ranker
{: #create-train}

To return the most relevant documents at the top of your results, the {{site.data.keyword.retrieveandrankshort}} services uses a machine-learning component called a ranker. You send queries to the trained ranker.

The ranker learns from examples before it can rerank results from queries that it hasn't seen before. Collectively, the examples are referred to as "ground truth."

1.  Download the sample <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/cranfield-gt.csv" download="cranfield-gt.csv">`cranfield-gt.csv` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> ground truth file.

    The file is a set of questions that a user might ask about the documents. The file provides the example information to train the ranker about questions and relevant answers.

    For each question, there is at least one identifier to an answer (the document ID). Each document ID includes a number to indicate how relevant the answer is to the question. The document ID points to the answer in the `cranfield-data.json` file that you downloaded in Step 3.
1.  Download the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/train.py" download="train.py">`train.py` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> Python script file.

    The script takes care of the details of converting the ground truth file to training data for the ranker. It then uploads the training file and creates and trains the ranker.

    > **Note:** The script requires Python major version 2, not major version 3, to run successfully.
1.  Navigate to the downloaded `train.py` script.
1.  Run the `train.py` script. Replace `{username}`, `{password}`, `{path_to_file}`, and `{solr_cluster_id}` with your information:

    ```bash
    python ./train.py -u {username}:{password} \
    -i {path_to_file}/cranfield-gt.csv -c {solr_cluster_id} \
    -x example_collection -n "example_ranker"
    ```
    {: pre}

    The script takes less than five minutes to finish. When the script is done, a new ranker ID and status are displayed in the script window. For example:

    ```javascript
    {
      "name": "example_ranker",
      "url": "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/rankers/6C76AF-ranker-43",
      "ranker_id": "6C76AF-ranker-43",
      "created": "2015-09-21T18:01:57.393Z",
      "status": "Training",
      "status_description": "The ranker instance is in its training phase, not yet ready to accept requests"
    }
    ```
    {: codeblock}

1.  Copy the `ranker_id`. You need it later in the tutorial.

Continue with the tutorial, which checks the status of the ranker training later.

> **Note:** When you set up a {{site.data.keyword.retrieveandrankshort}} service instance that uses your own data, you can [create training data manually](/docs/services/retrieve-and-rank/training-data.html#manual) instead of using the `train.py` script.

## Step 5: Retrieve some answers
{: #retrieve}

While you're waiting for the ranker to finish training, you can search your documents. This search, which uses the "Retrieve" part of the {{site.data.keyword.retrieveandrankshort}} service, does not use the machine-learning ranking features. It is a standard Solr search query.

You can search your collection from your browser or by issuing a cURL command. For the tutorial, paste the following URL into your browser. Replace `{username}`, `{password}`, and `{solr_cluster_id}` with your information.

```
https://{username}:{password}@gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/solr/example_collection/select?q=what is the basic mechanism of the transonic aileron buzz&wt=json&fl=id,title
```
{: codeblock}

The `/select` request handler in the cURL command indicates that this is a standard Solr search query, not a query that uses the Rank portion of the {{site.data.keyword.retrieveandrankshort}} service. Standard Solr search results are unordered and might not contain the most significant results. Step 6 uses the `/fcselect` request handler with a trained ranker to obtain results that are ordered (ranked) by significance.

Your query returns the 10 most relevant results from Solr. To try other queries, look in the `cranfield-gt.csv` file that you downloaded in Step 4. Copy a question from the first column and paste it as the value of the `q` parameter in your browser. For example:

```
&q=can the three-dimensional problem of a transverse potential
flow about a body of revolution be reduced to a two-dimensional
problem.&wt=json&fl=id,title
```
{: codeblock}

## Step 6: Rerank the results
{: #rank}

1.  Check the status of the ranker until you see a status of `Available`. With this sample data, training takes about five minutes and started when the script finished in Step 4.

    Issue the following command to retrieve the status of the ranker. Replace `{username}` and `{password}` with your information. Replace `{ranker_id}` with the information you copied in Step 4.

    ```bash
    curl -u "{username}":"{password}" \
    "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/rankers/{ranker_id}"
    ```
    {: pre}

1.  Query the ranker to review the reranked results, now that the ranker is trained.

    An example follows. It is similar to the query in Step 5, but the `select` parameter is changed to `fcselect`. Using `fcselect` runs the request against the trained ranker instead of against Solr as in Step 5. When you call `fcselect`, you must specify the ID of the trained ranker against which the request is to run.

    See [Reranking results](/docs/services/retrieve-and-rank/reranking-results.html#unsupported) for a list of standard Solr query modifications that are not supported by `/fcselect`. You can use any standard Solr query modifications with `/select`.

    Paste the following URL into your browser. Replace `{username}`, `{password}`, `{solr_cluster_id}`, and `{ranker_id}` with your information:

    ```
    https://{username}:{password}@gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}/solr/example_collection/fcselect?ranker_id={ranker_id}&q=what is the basic mechanism of the transonic aileron buzz&wt=json&fl=id,title
    ```
    {: codeblock}

    Note the use of the `/fcselect` request handler in the cURL command. This indicates the query uses the ranker specified by the value of the `ranker_id` parameter to return results in descending order of significance according to the training data that you provided to the ranker.

    The query returns your reranked search results in JSON format. You can compare these results against the results you got with the simple search in Step 5.

    After evaluating the reranked search results, you can refine them by repeating Steps 4, 5, and 6. You can also add new documents, as described in Step 3, to broaden the scope of the search. Repeat the process until you are completely satisfied with the results. This can require multiple iterations of refining and reranking.

    Try the [demo ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://retrieve-and-rank-demo.mybluemix.net/rnr-demo/dist/#/){: new_window}, which also uses the Cranfield collection, to see the difference between the Solr search results and the reranked results.
    {: tip}

1.  Experiment with other queries. Look in the `cranfield-gt.csv` file that you downloaded in Step 4. Copy a question from the first column and use it to replace the value of the `q` parameter in your browser. For example:

    ```
    &q=can the three-dimensional problem of a transverse potential
    flow about a body of revolution be reduced to a two-dimensional
    problem.
    ```
    {: codeblock}

## Step 7: Clean up
{: #delete}

You might want to delete the Solr components and ranker that you created in this tutorial. To clean up, you delete the cluster that you created in Step 2 and the ranker that you created in Step 4.

In the following examples, replace `{username}`, `{password}`, `{solr_cluster_id}`, and `{ranker_id}` with your information.

1.  Delete the cluster:

    ```bash
    curl -i -X DELETE -u "{username}":"{password}" \
    "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_id}"
    ```
    {: pre}

    When the cluster is deleted, the method returns HTTP response 200.

1.  Delete the ranker:

    ```bash
    curl -X DELETE -u "{username}":"{password}" \
    "https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/rankers/{ranker_id}"
    ```
    {: pre}

    When the ranker is deleted, the method returns an empty JSON object.

## What to do next
{: #what-to-do-next}

Now that you have a basic understanding of how to use the {{site.data.keyword.retrieveandrankshort}} service, dive deeper:

-   Configure the service to [use your own data](/docs/services/retrieve-and-rank/overview.html).
-   Explore the API in detail in the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/){: new_window}.
-   Learn about alternative methods of setting up a {{site.data.keyword.retrieveandrankshort}} instance, including
    -   [Building an enhanced information retrieval solution](/docs/services/retrieve-and-rank/using-enhanced-retrieval.html)
    -   [Using the {{site.data.keyword.retrieveandrankshort}} Web Interface](/docs/services/retrieve-and-rank/ranker-tooling.html)
