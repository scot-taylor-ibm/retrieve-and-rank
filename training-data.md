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

# Preparing training data
{: #top}

The {{site.data.keyword.retrieveandrankshort}} service learns from examples before it can rerank results that it has not seen before. The example data is referred to as *training data*.
{: shortdesc}

## Ground truth, relevance, and training data
{: #groundtruth}

To create training data, you start from your *ground truth*. Ground truth is the collection of questions that are matched to answers. For the {{site.data.keyword.retrieveandrankshort}} service, you label the answers with their relevance to the question. The relevance label helps the ranker determine which features are the most useful.

The questions, answers, and relevance labels are combined to create training data. You upload the training data to create and train a ranker.

## Overview of ranker training
{: #training_overview}

The {{site.data.keyword.retrieveandrankshort}} service provides two main methods to train rankers; these are discussed in the following section. Regardless of the method you use, the base process is the same:

1.  Ensure that you have the following:
    -   A running Solr cluster with a valid configuration
    -   A collection of data that the service can search for answers
    -   A list of questions that can be matched with varying degrees of relevance to answers in the collection
1.  Process the questions, answers, and relevance labels into a training-data set.
1.  Create and train a ranker by uploading the training-data set to it.
1.  Test the ranker's results against standard Solr results and against the needs of your end users.
1.  Refine your collection, questions, answers, and relevance labels according to the results of the ranker testing.
1.  Repeat Steps 2 through 5 until you have a ranker that meets your requirements.
1.  Deploy the ranker into production, preferably by using an application written in one of the {{site.data.keyword.retrieveandrankshort}} service APIs described in the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/){: new_window}.

## Methods for training a ranker
{: #methods}

There are two primary methods for training a ranker. The type of training data that you create and provide to train the ranker depends on the method you use:

-   Manually by using the [create ranker ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/?curl#create_ranker){: new_window} API; see [Manually training a ranker](#manual).
-   Semi-automatically by using the [`train.py`](#training) script; see [Training a ranker by using the train.py script](#script).

> **Note:** A web-based tool for ranker training is available at [https://watson-retrieve-and-rank.ng.bluemix.net/login ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://watson-retrieve-and-rank.ng.bluemix.net/login){: new_window}; for more information, see [Using the {{site.data.keyword.retrieveandrankshort}} Web Interface](/docs/services/retrieve-and-rank/ranker-tooling.html). This tool is limited to uploading HTML, PDF, and Microsoft&reg; Word (`.doc` or `.docx`) files as training data and has not been validated in an end-to-end {{site.data.keyword.retrieveandrankshort}} configuration and deployment. The information on this page does not necessarily apply to the {{site.data.keyword.retrieveandrankshort}} Web Interface.

## Common training requirements
{: #common}

Regardless of the method you use, creating, training, and using rankers has the following requirements.

### Ranker implementation details
{: #ranker_implement}

The following implementation details apply to the creation and training of rankers:

-   The maximum number of rankers per cluster is eight.
-   The maximum size for a ranker's training-data set is 300 MB. Using training-data sets larger than 300 MB results in the {{site.data.keyword.retrieveandrankshort}} service returning a 500 HTTP error (`Internal Server Failure`).
-   The larger the training-data set, the longer it takes to train a ranker. A 50-MB training file requires approximately 15 to 20 minutes to process.
-   By default, `fcselect`, the ranker request handler, returns 10 results, whether you use manual training or the `train.py` script. You can change the number of returned results by using the Solr `rows` parameter. For example, to return 30 results, use the following command:

    ```
    https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_name}/solr/{solr_collection_name}/fcselect&q={query}&wt=json&fl=id,title&rows=30
    ```
    {: codeblock}

-   In most cases, the higher the `rows` count, the better the performance, up to a value of approximately 100 rows. You are encouraged to try higher values for the `rows` parameter than the current default of 10.
-   Use the same `rows` setting during training and during runtime to ensure that the ranker model is consistent.

### Minimum training-data quality standards
{: #data_stds}

Regardless of whether you create the training data manually or by running the `train.py` script from a relevance file as mentioned later, the data must meet the following *minimal* quality criteria to be effective in improving the relevance of answers that are returned from the {{site.data.keyword.retrieveandrankshort}} service. Note that *minimal* does not mean *optimal*. You can optimize your training set, and therefore the performance and accuracy of your ranker, by adding more information to the set.

-   The file must contain at least 49 unique questions.
-   The number of feature vectors (that is, rows in your comma-separated values (CSV) training-data file) must be 10 times the number of features (that is, feature columns) in each row.

    If you are using the `train.py` script to generate features, the number of total records in the output file produced by `train.py` must be at least 50 times the number of fields that are identified in your Solr configuration. For example, if your collection defines five fields, you must have at least 250 records in your training data. This is because the `fcselect` call generates multiple features for each field and you must account for the additional features. Also, keep in mind that the number of candidate answers generated per query is dependent on the value of the `rows` parameter passed to `train.py` as well as your document collection&mdash;specifically, the number of documents in the collection that have some degree of overlap with the query text.
-   The relevance label must be a non-negative integer (between zero and some upper limit).
-   At least two different relevance labels must exist in the data and those labels must be well represented. A label is well represented if it occurs at least once for every 100 unique questions. For example, assume that you have 300 unique questions and you use a relevance scale of `1,2,3,4`. At least two distinct labels (for example, `1` and `4`) must each appear three times (0.01 X 300) in the training data.
-   At least 25 percent of the questions must have some label variety in the answer set. That is, all of the candidate answers for a single question cannot be labeled with a single relevance level.

    If you are using the `train.py` script, the script automatically generates some candidate answers with label 0 for your query, up to the value of the `rows` parameter. You can achieve adequate label diversity for the query by ensuring that your relevance file has a number of `answer_id`-`relevance_label` pairs with non-zero labels; the number of pairs must be less than the number of `rows` passed to `train.py`.
-   The relevance value zero has specific implications when training a ranker. Any documents labeled `0` are considered totally irrelevant to the question. That being said, the training data *must* contain some zero-labeled documents to strengthen the system's ability to search for relevant labels.

    If you use the `train.py` script to generate a training file for the ranker, the script automatically assigns a label value of `0` to documents that were retrieved from Solr but were not explicitly labeled in the relevance file passed to the script. Therefore, you do not need to explicitly include ground-truth examples that have a `0` label in the relevance file. The script assumes that if you did not explicitly label a document, it is not relevant to the query.

    However, if you train your ranker manually by creating a training-data file and posting it to the ranker, you *must* explicitly include zero-labeled examples.

## Manually training a ranker
{: #manual}

For optimal control over your training data, you can manually create a *training-data file* that you then pass to the [create ranker ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/?curl#create_ranker){: new_window} call to generate and train the ranker.

The [create ranker ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developercloud/retrieve-and-rank/api/v1/?curl#create_ranker){: new_window} API expects a CSV training-data file with feature vectors. Each feature vector represents a query-candidate answer pair and occupies a single row in the file. The first column of every row in the file is the query ID used to group together all of the candidate-answer feature vectors associated with a single query. The last column in the file is the *ground-truth label* (also called the *relevance label*), which indicates how relevant that candidate answer is to the query. The remaining columns are various features used to score the match between the query and the candidate answer. The file has the following format:

```
query_id, feature1, feature2, feature3,...,ground_truth
question_id_1, 0.0, 3.4, -900,...,0
question_id_1, 0.5, -70, 0,...,1
question_id_1, 0.0, -100, 20,...,3
...
```
{: codeblock}

The search API can be used to generate the feature values mentioned above by setting the `returnRSInput` parameter to `true` in the `fcselect` call.

### Manually creating a training-data file

The training-data file contains the following elements:

-   `q`: *(Required)* The question.
-   `gt`: *(Required)* The `answer_id` and `relevance_label` pairs.
-   `generateHeader:` A Boolean that indicates whether to generate headers for the training data.
-   `rows`: How many rows (or documents) to return. The default is `10`.

    The correct answer must exist in the result set. In other words, if you set the value to `50`, the correct answer must be in the first 50 rows.
-   `wt`: The Solr response writer. Set the output of the query to JSON by specifying `wt=json`.
-   `fl`: The list of fields to return. If you set `returnRSInput` to `true`, this parameter is ignored. If you set `returnRSInput` to `false`, set the value of `fl` to the unique key in the index. The value must match the field name value in your `schema.xml` file. For example, if the schema includes `<field name="id" type="string" indexed="true" stored="true" multiValued="false" />`, then include `fl=id` in each query.
-   `returnRSInput`: A Boolean that indicates whether the plugin returns the result set formatted as CSV. Set this to `true` to help create the training data.

Other requirements for manually generated training-data files include the following:

-   Feature names cannot contain commas, spaces, or colons.
-   Feature values must be valid floats in the range (1.7976931348623157e-308, 1.7976931348623157e+308).
-   Question IDs cannot contain commas, spaces, or colons.
-   Identical question IDs must be in a contiguous list; that is, all of the feature vectors related to a particular question ID must be on consecutive lines. For example, after question ID 4821 is listed in the file and you move to question ID 4822, you cannot later list question ID 4821 again.
-   Each line must have the same number of columns.

### Query requirements for training data
{: #format}

Make sure that your queries address the following requirements:

-   Create a query for each question in your ground truth.
-   Include the following values in the field list:
    -   Include the ID in your ground truth for the question that the query addresses. This parameter returns the value in response.
    -   Set the output of the query to JSON by including `wt=json`.

The following snippet shows the parameters that are required:

```
http://{machine}:8080/retrieve-and-rank/api/v1/solr_clusters/{cluster_id}/{collection_name}/fcselect?q=in practice, how close to reality are the assumptions that the flow in a hypersonic shock tube using nitrogen is non-viscous and in thermodynamic equilibrium.&gt=656,2,1313,2,1317,2,1316,1,1318,2,1319,2,1157,2,1274,2,1286,0&generateHeader=false&rows=50&returnRSInput=true&wt=json&fl=id
```
{: codeblock}

The parameters passed to the `fcselect` request handler in the previous example are broken down as follows:

```
q=in practice, how close to reality are the assumptions that the flow in a hypersonic shock tube using nitrogen is non-viscous and in thermodynamic equilibrium.
&gt=656,2,1313,2,1317,2,1316,1,1318,2,1319,2,1157,2,1274,2,1286,0
&generateHeader=false
&rows=1
&returnRSInput=true
&wt=json
&fl=id
```
{: codeblock}

Save each JSON response as a file with a `.json` extension.

When you have completed your training-data file, you can use it to create a ranker, as in the following example:

```bash
curl -X POST -u "{username}":"{password}"
-F training_data=@train.csv
-F training_metadata="{\"name\":\"My ranker\"}"
"https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/rankers"
```
{: pre}

## Training a ranker by using the train.py script
{: #script}

Although you can create the training data by issuing a query for each question in your ground truth, the {{site.data.keyword.retrieveandrankshort}} service simplifies the process by providing a script that does the work for you. The script converts the question, answer, and relevance information to training data for the ranker. It then uploads the training file and creates and trains the ranker.

The [`train.py`](#training) script uses a relevance file (or input file) with requirements that differ from the requirements for the training-data file used in the manual process. This file expects the query text and a series of `answer_id`-`relevance_label` pairs from the *ground truth collection*. The `answer_id` values must align with the Solr cluster ID that is used as the unique key of your documents in the collection. The `relevance_label` values must follow the training-data quality standards listed in [Minimum training-data quality standards](#data_stds). The file has the following format:

```
"my first query", "doc_id_1", "1", "doc_id_24", "3", "doc_id_7000", "1"
"my second query", "doc_id_36", "1", "doc_id_2", "3", "doc_id_3", "1"
```
{: codeblock}

When the `train.py` script converts the relevance file into training data for the ranker, it augments the candidate answer list from the relevance file with additional candidate answers that it generates by using the `fcselect` request-handler call. Because relevance labels are not provided for these other candidate answers, the label defaults to zero (that is, not relevant). See the note at [Minimum training-data quality standards](#data_stds) regarding the `0` label. The number of additional candidate answers used to augment the relevance file is determined by the `rows` parameter passed to the `train.py` script.

The answer ID must be the unique key of a document in the index. The unique key is defined in the `schema.xml` configuration file. For example, the `schema.xml` file of the <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/blank-example-solr-config.zip" download="blank-example-solr-config.zip">`blank-example-solr-config.zip` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> sample configuration set has the following line:

```xml
<field name="id" type="string" indexed="true" stored="true" required="true" />
```
{: codeblock}

### Relevance file formatting requirements

Make sure that your CSV relevance file adheres to the following data requirements:

-   The data is UTF-8 encoded.
-   The values are separated by a comma, do not contain spaces or colons, and are enclosed with double quotation marks.
-   Each record (question) is terminated by an end-of-line character, which is a special character or sequence of characters that indicate the end of a line.

### Running the script
{: #training}

The <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/retrieve-and-rank/train.py" download="train.py">`train.py` <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a> Python script takes the following arguments:

```bash
train.py -u {username:password} -i {relevance_file} -c {cluster_id}
-x {collection_name} [-r {solr_rows_per_query}] [-n {ranker_name}]
```
{: pre}

-   Replace `{username}` and `{password}` with your service credentials.
-   Replace `{relevance_file}` with the location of your relevance file.
-   Replace `{cluster_id}` and `{collection_name}` with your information.
-   *(Optional)* The `-r` argument specifies the number of answer results that the query returns. The correct answer must be in the results. The default value is `10`.
-   *(Optional)* Replace `{ranker_name}` with a name for the ranker that means something to you.

The `train.py` script requires Python major version 2 and fails if it is run under Python major version 3. To check the version of Python installed on your system, run the following command:

```bash
python --version
Python 2.7.10
```
{: pre}

If the output of the command begins with `Python 2`, as shown in the preceding command and example output, you are running a compatible version of Python. If the output begins with `Python 3`, you need to install a recent release of Python version 2 and point to it when you run the script, either manually (for example, with the full path such as `/usr/bin/python2.7/`) or automatically (for example, by updating your `PATH` environment variable).

Issue the command `python ./train.py -h` to see the help output.
{: tip}

For an example of running the script, see the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html#create-train).

### Output of the script

The script creates a file in your working directory called `trainingdata.txt`. That file is used to create the ranker. When it finishes, the script displays a new ranker ID and its status. Save the `ranker_id`, which you need to rerank your results at runtime.

### Explanation of the script
{: #script_explain}

The script performs the following actions to simplify interactions with the {{site.data.keyword.retrieveandrankshort}} service. This is a high-level overview of ranking operations in the script, not a line-by-line analysis or an explanation of simpler functions such as entering your username and password.

1.  From the command-line options specified, the script gathers critical information including the full path to your relevance file, the names of the Solr cluster and Solr collection, the ranker name, and the number of Solr rows that are included in each query.
1.  The script parses your relevance file and creates (opens) the `trainingdata.txt` file.
1.  For each row in the relevance file, the script generates a `curl` query against the Solr cluster and collection, using the number of Solr rows specified on the command line. It writes the response from Solr to the `trainingdata.txt` file. The `curl` command runs against the following URL:

    ```
    https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_name}/solr/{solr_collection_name}/fcselect&q=wing&wt=json&fl=id,title
    ```
    {: codeblock}

    By default, the `fcselect` request handler returns 10 results. You can change the number of returned results by adding the Solr `rows` parameter to the `curl` query. For example, to return 20 results, use the following command:

    ```
    https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_name}/solr/{solr_collection_name}/fcselect&q={query}&wt=json&fl=id,title&rows=20
    ```
    {: codeblock}

    See [Ranker implementation details](#ranker_implement) for more information about using the `rows` parameter.

1.  After the Solr queries have completed, the script parses the complete `trainingdata.txt` file and generates a `curl` query against the the ranker specified on the command line. The `curl` command runs against the following URL:

    ```
    https://gateway.watsonplatform.net/retrieve-and-rank/api/v1/solr_clusters/{solr_cluster_name}/solr/{solr_collection_name}/fcselect/rankers
    ```
    {: codeblock}

1.  As the ranker responds to queries, the script builds the responses into JSON output that it retains as a ranker ID.
1.  The script writes the compiled `trainingdata.txt` file to the directory in which the script was run.
1.  The script outputs the ranker ID that it generated in Step 5. Use this ranker ID to rerank the query results as documented in [Reranking results](/docs/services/retrieve-and-rank/reranking-results.html).

## Testing data requirements
{: #testing}

You can also submit testing data to validate a set of answers against a trained ranker. Testing data takes a CSV format that is similar to the training data. The CSV file that you submit as testing data includes answers from only a single Solr query. Each record in the testing file has two or more columns: The first column is an answer ID; the middle columns are the 1-*n* machine-learning feature values. Testing data does not include relevance labels. The testing data must meet the following requirements:

-   Except for relevance labels and contiguous question IDs, the format requirements for the testing data are the same as the [Query requirements for training data](#format).
-   The dimensionality requirements for the testing data are the same as the dimensionality requirements for training data.
-   The feature names and the number of features for each record in the testing data must match exactly with the training data that was used to generate the ranker. In other words, if there are 100 features in each record in your training data, the same 100 features must be in each record of your testing data.

## Working with ranker confidence scores
{: #ranker-conf}

The `fcselect` request handler optionally exposes a `ranker.confidence` value for each result returned by a particular query. The confidence score is a value between `0` and `1` that expresses the trained ranker's degree of belief that the answer is relevant to the query. You can generally assume that results with higher confidence scores are more likely to be relevant than results with lower confidence scores. Consequently, if you set a confidence threshold between `0` and `1` (for example, `0.50`) and provide only responses that have a confidence score above that threshold value, you can expect that the results will be more precise but have lower coverage (that is, a lower number of possible matches returned). For more information about how and when to set a confidence threshold value, see the article titled [How to select a threshold for acting using confidence scores ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://developer.ibm.com/watson/blog/2016/06/23/how-to-select-a-threshold-for-acting-using-confidence-scores/){: new_window}.

As noted earlier, if you discard all results below some threshold, you make the results more precise at the cost of reducing coverage. *Reducing coverage* means discarding some good results. For example, if 98 percent of all results below a score of `0.03` are irrelevant, and you discard all results below that score, then 2 percent of the results that you discard are relevant. For some applications, it is never acceptable to discard any relevant results. Such applications should not have a threshold for responding and instead should always respond regardless of the confidence score in the results.

There are many other potential uses for confidence scores besides setting a threshold and discarding results below that value. For example, you might want to warn users when the confidence scores are low. As another example, you might want to keep a log of all queries that produced results with low confidence scores, make a histogram of what keywords were disproportionately common in those queries, and use that histogram to drive new content creation. If, for example, many of your users ask questions that use the word "golf" and those users tend to get only low confidence results, you might want to add more golf-related content to your corpus.

To return the `ranker.confidence` for a set of returned results, add `ranker.confidence` to the `fl` parameter of the query; for example:

```
q=in practice, how close to reality are the assumptions that the flow in a hypersonic shock tube using nitrogen is non-viscous and in thermodynamic equilibrium.
&gt=656,2,1313,2,1317,2,1316,1,1318,2,1319,2,1157,2,1274,2,1286,0
&generateHeader=false
&rows=1
&returnRSInput=true
&wt=json
&fl=id,title,ranker.confidence
```
{: codeblock}

Sample output from the Cranfield set resembles the following examples:

```javascript
[
  {
    "id": "773",
    "title": [
      "q . app . math . 7, 1950, 381 . experiments on porous-wall cooling and flow separation control in a supersonic nozzle ."
    ],
    "ranker.confidence": 0.4921862317121547
  },
  {
    "id": "331",
    "title": [
      "effects of surface tension and viscosity on taylor instability ."
    ],
    "ranker.confidence": 0.3309682122734283
  },
  {
    "id": "1060",
    "title": [
      "buckled states of circular plates ."
    ],
    "ranker.confidence": 0.3237639882613359
  },
  {
    "id": "842",
    "title": [
      "an improvement on donnell's approximation for thin-walled circular cylinders ."
    ],
    "ranker.confidence": 0.27907049905649395
  },
  {
    "id": "1204",
    "title": [
      "experimental effect of bluntness and gas rarefaction on drag coefficients and stagnation heat transfer on axisymmetric shapes in hypersonic flow ."
    ],
    "ranker.confidence": 0.16337290319174727
  },
  {
    "id": "432",
    "title": [
      "theoretical damping in roll and rolling moment due to differential wing incidence for slender cruciform wings and wing-body combinations ."
    ],
    "ranker.confidence": 0.15538332255838191
  },
  {
    "id": "76",
    "title": [
      "flight measurement of wall pressure fluctuations and boundary-layer turbulence ."
    ],
    "ranker.confidence": 0.13225384719559052
  },
  {
    "id": "1073",
    "title": [
      "a practical method for numerical evaluation of solutions of partial differential equations of the heat-conduction type ."
    ],
    "ranker.confidence": 0.07157572510253142
  },
  {
    "id": "433",
    "title": [
      "application of two dimensional vortex theory to the prediction of flow fields behind wings of wing-body combinations at subsonic and supersonic speeds ."
    ],
    "ranker.confidence": 0.04429298720847902
  },
  {
    "id": "871",
    "title": [
      "steady-state creep through dislocation climb ."
    ],
    "ranker.confidence": 0
  }
]
```
{: codeblock}

```javascript
[
  {
    "id": "1312",
    "title": [
      "tabulated solutions of the equilibrium gas properties behind the incidents and reflected normal shock-wave in a shock-tube ."
    ],
    "ranker.confidence": 0.5063394020015944
  },
  {
    "id": "1286",
    "title": [
      "equilibrium real-gas performance charts for a hypersonic shock-tube wind-tunnel employing nitrogen ."
    ],
    "ranker.confidence": 0.29351517243375114
  },
  {
    "id": "140",
    "title": [
      "the determination of turbulent skin friction by means of pitot tubes ."
    ],
    "ranker.confidence": 0.22921956339636393
  },
  {
    "id": "317",
    "title": [
      "non-equilibrium flow of an ideal dissociating gas ."
    ],
    "ranker.confidence": 0.18736525124887166
  },
  {
    "id": "401",
    "title": [
      "inviscid hypersonic airflows with coupled non-equilibrium processes ."
    ],
    "ranker.confidence": 0.14780914991320596
  },
  {
    "id": "773",
    "title": [
      "q . app . math . 7, 1950, 381 . experiments on porous-wall cooling and flow separation control in a supersonic nozzle ."
    ],
    "ranker.confidence": 0.12797983453319173
  },
  {
    "id": "329",
    "title": [
      "various aerodynamic characteristics in hypersonic rarefied gas flow ."
    ],
    "ranker.confidence": 0.1274584060758575
  },
  {
    "id": "236",
    "title": [
      "criteria for thermodynamic equilibrium in gas flow ."
    ],
    "ranker.confidence": 0.09834181913275054
  },
  {
    "id": "1296",
    "title": [
      "non-equilibrium expansions of air with coupled chemical reactions ."
    ],
    "ranker.confidence": 0.05580442358036571
  },
  {
    "id": "259",
    "title": [
      "second order theory for unsteady supersonic flow past slender pointed bodies of revolution ."
    ],
    "ranker.confidence": 0
  }
]
```
{: codeblock}

The ranker automatically ranks the results from highest confidence to lowest confidence in the returned results. The reliability of the confidence scores can vary depending on the quality of the training set as well as mismatches between training and runtime conditions. However, as long as your training set has a modestly high quality and quantity, you generally find that results with higher confidence scores are more likely to be relevant than results with lower confidence scores.

## What to do next
{: #what-to-do-next}

See [Rerank your results](/docs/services/retrieve-and-rank/reranking-results.html) to return search results with different rankings.
