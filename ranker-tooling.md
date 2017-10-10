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

# Using the Retrieve and Rank Web Interface
{: #top}

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to [migrate[(/docs/services/discovery/migrate-dcs-rr.html).  **Note:** May not apply in select Dedicated environments.

You can use the {{site.data.keyword.retrieveandrankshort}} Web Interface to create a cluster and collection, upload documents and questions, and train and test rankers. The following steps walk you through getting started with the service in a typical use case for the {{site.data.keyword.retrieveandrankshort}} Web Interface.
{: shortdesc}

## Tool prerequisites
{: #tool-prereqs}

Prerequisites for using the {{site.data.keyword.retrieveandrankshort}} Web Interface include the following:

-   A [{{site.data.keyword.Bluemix_notm}} account ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://apps.admin.ibmcloud.com/manage/trial/bluemix.html?cm_mmc=WatsonDeveloperCloud-_-LandingSiteGetStarted-_-x-_-CreateAnAccountOnBluemixCLI){: new_window}.
-   A set of documents in any combination of HTML, PDF, or Microsoft Word format. When users of your application ask questions, these are the documents from which the {{site.data.keyword.retrieveandrankshort}} service searches and ranks answers.

    > **Note:** The {{site.data.keyword.retrieveandrankshort}} Web Interface uses the [{{site.data.keyword.documentconversionshort}} service ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/watson/developercloud/document-conversion.html){: new_window} to convert input files. See the [{{site.data.keyword.documentconversionshort}} service documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/docs/services/document-conversion/index.html){: new_window} for information about customizing how the {{site.data.keyword.retrieveandrankshort}} Web Interface converts different types of input files into well-chunked answers.
-   A list of questions that pertain to the corpus and reflect the types of queries your users will ask. The list must be in text format, one question per line. In general, the more questions, the better the quality of the trained ranker, but a minimum of 50 questions is required to get started.
-   If you plan to use the {{site.data.keyword.retrieveandrankshort}} Web Interface with the Cranfield data collection provided as an example for the {{site.data.keyword.retrieveandrankshort}} service, make configuration changes to your Solr cluster with the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/cranfield-solr-config.zip" download="cranfield-solr-config.zip">`cranfield-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> file.

## Logging in and setting up services

1.  Go to [https://watson-retrieve-and-rank.bluemix.net/login](https://watson-retrieve-and-rank.bluemix.net/login). <!-- If you are accessing the tool from another service, use the URL https://watson-retrieve-and-rank.ng.bluemix.net/login?region={bluemix_region}&service_guid={id}, where {bluemix_region} is the Bluemix region of your service and {id} is the service GUID of the service from which you're accessing the tool. You can find these values on your Bluemix dashboard. -->
1.  The tool prompts you to select a {{site.data.keyword.documentconversionshort}} service instance to convert your source documents for the {{site.data.keyword.retrieveandrankshort}} service. Select a {{site.data.keyword.documentconversionshort}} service instance from the drop-down list and click **Connect to service**. See the [{{site.data.keyword.documentconversionshort}} service documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/docs/services/document-conversion/index.html){: new_window} for information about that service.
1.  The tool prompts you to select a {{site.data.keyword.retrieveandrankshort}} service instance. Select one from the drop-down list and and click **Connect to service**.
1.  If your {{site.data.keyword.retrieveandrankshort}} service instance does not already have a cluster associated with it, the tool prompts you to create a cluster by supplying a name and a size. Provide a name for the cluster and select the cluster size from the drop-down menu. If you are unsure what cluster size to use, click the help link below the drop-down menu or see [Sizing your {{site.data.keyword.retrieveandrankshort}} cluster](/docs/services/retrieve-and-rank/using-solr.html#sizingCluster).

    > **Note:** Cluster pricing is based on the size of the cluster. See your {{site.data.keyword.Bluemix_notm}} dashboard for details.
1.  When you have finished selecting your cluster options, click **Create**. The tool starts creating the cluster as a background task.
1.  After you have created a cluster, or if you already had a cluster in your {{site.data.keyword.retrieveandrankshort}} service instance, the tool displays the **Clusters** page. You can select an existing cluster (including the one you created in the previous step) or create a new cluster.
1.  If you select an existing cluster that already has a collection associated with it, you can select that collection for use with the tool. Otherwise, when the cluster has been created and initialized, you can create a new collection. This exercise assumes that you want to create a new collection. To do so, click **Create a new collection**.
1.  The tool prompts you for a collection name. Enter a name and click **Create**. The tool creates the collection.
1.  When the collection is ready, click <code>&rarr;</code> (the right-pointing arrow button) to enter the collection.

## Taking the tutorial

1.  The tool prompts you to take a short tutorial to familiarize yourself with the use of the tool. If this is your first time using the tool, IBM *strongly* recommends that you take the tutorial; it will help you navigate easily through the remaining steps to use the tool and train a ranker quickly and efficiently.
1.  Follow the steps of the tutorial as directed. The tutorial is self-guided and is therefore not detailed in this documentation.
1.  After you complete the tutorial, click **Return to Tasks** to see what the next task is. This exercise assumes that the next task is uploading documents to your new collection.

The tool is task-based, providing you with tasks that most efficiently advance the capabilities of the trained ranker. If you are ever uncertain what to do next, click the **Tasks** tab and perform one or more of the tasks listed there.

## Uploading documents

1.  The tool displays an **Upload documents** panel. Click **Import**, then import the documents that you want to upload to your collection by dragging or clicking documents into the **Import documents** dialog box. Documents can be in any combination of HTML, PDF, and Microsoft Word format.

    The dialog box includes a **Split my documents up into individual answers for me** checkbox that defaults to selected. For this tutorial, retain the default by leaving the checkbox selected. When this option is enabled, the {{site.data.keyword.documentconversionshort}} service splits up the documents into individual answers before passing them to the {{site.data.keyword.retrieveandrankshort}} service for storage and indexing.
1.  The **Import documents** dialog box shows you the progress of the upload. During the upload process, the tool uses the {{site.data.keyword.documentconversionshort}} service to convert your source files to the JSON format used by the {{site.data.keyword.retrieveandrankshort}} service.
1.  As the tool uploads the selected documents, it displays their names on the page behind the dialog box. When the selected documents are uploaded, the **Import documents** dialog box tells you how many documents it uploaded. You can either upload more documents (up to the limits specified in [Sizing your {{site.data.keyword.retrieveandrankshort}} cluster](/docs/services/retrieve-and-rank/using-solr.html#sizingCLuster)) or click **Finish**. For the purposes of this exercise, click **Finish**.

## Importing questions

1.  The tool displays a badge on the **Tasks** tab to indicate that you have another task to perform. Click the tab. The tool displays the **Upload representative questions** dialog box.
1.  Click **Import** in the dialog box. The tool opens the **Add questions** dialog box. The tool takes questions in the form of a text file with one question per line.
1.  The tool imports the questions and displays them under the heading **All Questions** on the **Content** tab. The tool analyzes the questions against your uploaded documents and starts determining which questions need to be answered first for the best results.
1.  While the tool analyzes and orders the question list, it displays a badge on the **Tasks** tab. Click the tab. The tool displays the **Active Learning** dialog box.

## Answering questions

1.  Click **Start** in the **Active Learning** dialog box to start rating the first 50 questions selected by the tool.
1.  The tool displays the first question followed by four possible answers selected from the uploaded documents. Rate each answer on a scale of one to four stars, as demonstrated in the tutorial. When you have finished, click **Submit Ratings**. Alternatively, you can click one of **Do not include this question in Watson's training**, **I can't rate these**, or **Add another answer** for a particular question. The tutorial discusses these alternatives.
1.  When you complete rating the answers for the first question and submit or otherwise dispose of the question, the tool immediately shows you the next question and answer set. Continue rating answers for each question in turn. The tool displays your progress by percentage on the upper left of the tool panel.
1.  When you have finished rating answers for the first 50 questions, the tool displays a **Task Complete** acknowledgment. Click **Return to Tasks** to continue with the next step. Additional tasks typically include rating new sets of questions, which the tool categorizes as a background task. The tool also enables you to view answers that you have already provided for various questions and to review the documents in the collection.
1.  You can review your overall progress by clicking the **Performance** tab toward the right of the tool's top bar. Return to the **Tasks** tab to continue to rate answers for questions and help the {{site.data.keyword.watson}} service learn.
1.  As you continue to answer questions, the tool evaluates the best strategies for getting the best possible answers and presents different tasks based on those strategies. For example, for a question whose answers all rated only one star, the tool might ask you to search for a better answer. In general, for the best results, after you complete a given task, you can check your progress; but then return to the **Tasks** tab and follow the tool's prompts and requests presented there.

## Training and testing a ranker

1.  When you have answered enough questions to satisfy the tool's training requirements, the **Tasks** tab displays a **Train a ranker** dialog box. Click **Train** in the dialog box to begin training a ranker with the information you have provided.

    > **Note:** Each ranker has an associated price, as does training a ranker. See your {{site.data.keyword.Bluemix_notm}} dashboard for details.
1.  The tool displays the progress of the ranker's training. Training can take anywhere from a few minutes to over an hour, depending on the size and complexity of the training set. You can either wait for the ranker to finish being trained or perform other tasks on the **Tasks** tab.
1.  When the ranker's training is complete, a new task asks you to review the results. Click on the **Performance** tab. The tool shows you the accuracy of the new ranker compared to the accuracy of a base Retrieve (Solr) search and any previous rankers you have trained. Click on the ranker's bar chart for more details about it.
1.  If you want to test your new ranker, click the **Content** tab, and then click **Try Out Watson** to ask questions and receive answers from different rankers and the untrained Retrieve service.
1.  It is exceptionally rare for the first trained ranker to be accurate enough to become the production ranker. To improve future versions of the ranker, return to the **Tasks** tab to answer more questions. You can also add more questions to the question list and more documents to the collection.
1.  Iterate over these steps until you have a ranker with the accuracy your application needs. You can then continue to query the ranker by using either the tool or a {{site.data.keyword.retrieveandrankshort}} application.

The {{site.data.keyword.retrieveandrankshort}} Web Interface uses a `rows` setting of `30`. An application that uses the same ranker needs to use the same setting to ensure the consistency of ranked results. See [Preparing training data](/docs/services/retrieve-and-rank/training-data.html#script_explain) for information about the `rows` command.

## Performing other tasks

-   As described in [Ranker implementation details](/docs/services/retrieve-and-rank/training-data.html#ranker_implement), there is a limit to the number and size of rankers per cluster. To clean up older, less accurate rankers, go to the **Performance** tab, click on a ranker's bar chart, and then click **Delete ranker**. Even after the tool deletes a ranker, it retains a record of the ranker's efficiency so that you can continue to compare new versions against previous versions.
-   At any point, you can review and manage your documents, questions, and answers by going to the **Content** tab.

<!--

## Configuring the Web Interface to work with the Cranfield collection

If you want to use the {{site.data.keyword.retrieveandrankshort}} Web Interface with the Cranfield collection provided as an example with the {{site.data.keyword.retrieveandrankshort}} base service, you must update the Solr cluster's configuration by editing a couple of configuration files as follows. See [Configuring the {{site.data.keyword.retrieveandrankshort}} service](/docs/services/retrieve-and-rank/configure.html) for an overview of configuration processes and requirements. The default Cranfield configuration is available for download at [cranfield-solr-config.zip](https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/cranfield-solr-config.zip).

- Edit the configuration's `solrconfig.xml` file as follows:

    Change

    ```xml
    <schemaFactory class="ClassicIndexSchemaFactory"/>
    ```
    {: codeblock}

    to

    ```xml
    <schemaFactory class="ManagedIndexSchemaFactory">
      <bool name="mutable">true</bool>
    </schemaFactory>
    ```
    {: codeblock}

-  Edit the configuration's `schema.xml` file as follows:

    Remove commented lines 179 through 184. That is, delete:

    <pre><code data-copy="false">&lt;!&ndash;&ndash; uncomment the following to ignore any fields that
      don't already match an existing
      field name or dynamic field, rather than reporting them as an error.
      alternately, change the type="ignored" to some other type e.g. "text"
      if you want unknown fields indexed and/or stored by default &ndash;&ndash;&gt;
    &lt;!&ndash;&ndash;dynamicField name="*" type="ignored" multiValued="true"&ndash;&ndash;&gt;</code></pre>

-->
