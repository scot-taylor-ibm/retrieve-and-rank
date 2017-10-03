---

copyright:
  years: 2015, 2017
lastupdated: "2017-08-29"

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

# Using Kale

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to (migrate)[/docs/services/discovery/migrate-dcs-rr.html].  **Note:** May not apply in select Dedicated environments.

Kale is a command-line tool that helps you quickly create, configure, and manage the {{site.data.keyword.watson}} services that you need to gather documents and query them: {{site.data.keyword.retrieveandrankshort}} and [{{site.data.keyword.documentconversionshort}}](/docs/services/document-conversion/index.html). The following sections provide instructions to install, configure, and use Kale, including creating the configuration used by the Data Crawler.
{: shortdesc}

## Downloading and installing Kale
{: #c_kale_install}

Kale is a command line tool that helps you quickly create and configure the {{site.data.keyword.documentconversionshort}} and {{site.data.keyword.retrieveandrankshort}} services. To download and install it:

1.  Download and install the file [`kale-x.y.z-standalone.jar` ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://ibm.biz/kale-jar){: new_window} to the location you prefer.
1.  Set up a command-line alias (shortcut) for Kale.

    Use the following as an example, where *&lt;install-directory&gt;* is a variable that represents the pathname of the installation directory:

    ```bash
    kale='java -jar <install-directory>/kale-x.y.z-standalone.jar'
    ```
    {: pre}

The following notes apply to using Kale:

-   Enter `kale help` for a list of all Kale commands. Enter `kale help {name of command}` for the help for that command.
-   Enter all commands in lowercase. Do not enter the curly braces (`{}`).
-   If you are in the Kale working directory and switch to another directory, you need to log in to Kale again.

## Logging into Kale

Do the following to log into Kale:

1.  Open a terminal or command-line window.
1.  Enter `kale login`.
1.  The endpoint for the Kale APIs appears. The default endpoint for {{site.data.keyword.watson}} services in the public version of {{site.data.keyword.Bluemix_notm}} is `https://api.ng.bluemix.net`. If the endpoint displayed is correct, press `Return`. If not, enter the desired endpoint and then press `Return`.
1. When prompted, enter your `username` and `password`. Use the same login information that you use for {{site.data.keyword.Bluemix_notm}}.

    > **Note:** {{site.data.keyword.IBM_notm}} employees can have a {{site.data.keyword.Bluemix_notm}} password that differs from their {{site.data.keyword.IBM_notm}} password. Alternatively, if an {{site.data.keyword.IBM_notm}} employee has single sign-on enabled, Kale prompts the employee to open a URL to obtain a passcode that the employee can use to log in.

After you have logged in, the `Current Environment` names are displayed:

-   `User`
-   `Endpoint`
-   `Organization`
-   `Space`

## Creating and configuring services with Kale
{: #c_kale_assemble}

Kale offers several commands, but if you would like to run all of the commands in one step, you can use the `kale assemble` command. This command does the following in a single operation:

-   Creates a {{site.data.keyword.documentconversionshort}} service
-   Creates a {{site.data.keyword.retrieveandrankshort}} service
-   Creates a Solr cluster
-   Creates a Solr collection
-   Sets the language of the collection

To create the services, cluster, and collection without setting the cluster size, enter

```bash
kale assemble {base-name} {language}
```
{: pre}

If you do not specify a `cluster-size`, `kale assemble` uses the "free" 300 MB cluster when creating the Solr cluster. You can create only one free cluster. A new space is created to store the components. For information about cluster pricing, see [Sizing your {{site.data.keyword.retrieveandrankshort}} cluster](/docs/services/retrieve-and-rank/using-solr.html#sizingCluster) and the [Pricing Plans ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.ng.bluemix.net/catalog/services/retrieve-and-rank/){: new_window} on {{site.data.keyword.Bluemix_notm}}.

-   `base-name` is the name used for your services. It does not need to contain a hyphen, but the name should not include spaces.
-   `language` is the language of your document collection. Available languages are `english`, `german`, `spanish`, `arabic`, `brazilian`, `french`, `italian`, and `japanese`. You must enter the languages as shown, in lowercase.

To create the services, collection, and a cluster of a specific size, enter

```bash
kale assemble {base-name} {language} {cluster-size}
```
{: pre}

You can provision services with the Premium plan by setting the premium flag:

```bash
kale assemble {base-name} {language} --premium
```
{: pre}

> **Note:** Premium provisioning is not currently available for the {{site.data.keyword.retrieveandrankshort}} service. Even if the `premium` flag is set, {{site.data.keyword.retrieveandrankshort}} is provisioned using the Standard plan.

After you have run `kale assemble`, enter `kale list` to display the results:

- `document_conversion_service`: `{name}`
- `retrieve_and_rank_service`: `{name}`
- `cluster`: `{name}`
- `solr_configuration`: `{language}`
- `collection`: `{name}`

> **Note:** If `kale assemble` fails at any stage, it rolls back to the original state by deleting the services and space it created. One reason `kale assemble` could fail is that you have specified a `base-name` that duplicates an existing service, space, or cluster. If you prefer to use individual Kale commands (instead of `kale assemble`), see [Additional Kale commands](/docs/services/retrieve-and-rank/using-kale.html#c_kale_commands).

## Testing the conversion of documents with Kale
{: #c_kale_test}

This step is optional. It is a quick test to check how the {{site.data.keyword.documentconversionshort}} service will convert your documents. It is not to be used to convert all of your documents, and the converted test documents will not be used by the {{site.data.keyword.retrieveandrankshort}} service. In production, your documents are uploaded with the Data Crawler and converted with the {{site.data.keyword.documentconversionshort}} service.

You can convert one or several documents with the command. Supported file types are PDFs, Microsoft Word documents, and HTML pages.

```bash
kale dry-run {file1} {file2} {file3} ...
```
{: pre}

The converted documents are JSON files that can be found in the `converted` directory of your current working directory. The files in the `converted` directory include the complete path name of each test document. You can open and review the JSON files, which include the conversion schema plus the document text.

## Creating the Data Crawler configuration with Kale
{: #c_kale_config}

The Data Crawler requires two files to convert your documents in the format required by the {{site.data.keyword.watson}} services you created earlier. You create these configuration files with Kale. After the Data Crawler is installed, you copy these files into the `config` directory of the Data Crawler working directory.

1.  Enter `kale create crawler-configuration`.
1.  The following files are created in the local directory:
    -   `orchestration_service.conf`
    -   `orchestration_service_config.json`

## What to do next

See [Using the Data Crawler](/docs/services/retrieve-and-rank/using-data-crawler.html) to get started by [Downloading and installing the Data Crawler](/docs/services/retrieve-and-rank/using-data-crawler.html#install).

## Additional Kale commands
{: #c_kale_commands}

If you prefer, you can use individual Kale commands instead of `kale assemble` (as described in [Creating and configuring services](#c_kale_assemble)) to create your {{site.data.keyword.documentconversionshort}} service, {{site.data.keyword.retrieveandrankshort}} service, Solr cluster, and Solr collection, and to set the language of the collection. Follow these steps:

1.  Select an organization (if you have only one organization in {{site.data.keyword.Bluemix_notm}}, skip this step). If you do not know the names of your organizations or spaces, enter `kale list organizations` or `kale list spaces`.
    1.  Enter `kale list organizations` to see the organizations that are available to you.
    1.  Enter `kale select organization {organization}` to select an organization. You cannot create a new organization with Kale.

1.  Select or create a space (if you have only one space in {{site.data.keyword.Bluemix_notm}}, skip this step).
    1.  Enter `kale list spaces` to see the spaces you have available.
    1.  Enter `kale select space {space}` to select a space. Alternatively, to create a new space, enter `kale create space {space_name}`; if you create a new space, it is automatically selected.

1.  Use Kale to create instances of two {{site.data.keyword.watson}} services: {{site.data.keyword.documentconversionshort}} and {{site.data.keyword.retrieveandrankshort}}.
    1.  The {{site.data.keyword.documentconversionshort}} service takes your documents and converts them into a format the {{site.data.keyword.retrieveandrankshort}} service can use. Create an instance of the service as follows:

    ```bash
    kale create document_conversion {service_name}
    ```
    {: pre}

    1.  The {{site.data.keyword.retrieveandrankshort}} service provides search capabilities. Create an instance of the service as follows:

    ```bash
    kale create retrieve_and_rank {service_name}
    ```
    {: pre}

After your {{site.data.keyword.retrieveandrankshort}} service instance is created, you need to do a small amount of configuration.

1.  Create a cluster. A Solr cluster manages your search collections. Enter `kale create cluster {cluster_name} --wait`. The command displays `...` until the cluster is created.

    Creating a cluster can take some time. To check the status of the cluster creation, enter `kale list services`. If the cluster is created, the message is `status: READY`. If the cluster is not yet ready, the message is `status: NOT_AVAILABLE`.
1.  Create a Solr configuration. A Solr configuration identifies how to index your documents so that you can search the important fields. The configuration identifies the language of the document in your collection. A collection can contain documents in only a single language. Enter kale `create solr-configuration {language}` where `{language}` is one of the following: `english`, `german`, `spanish`, `arabic`, `brazilian`, `french`, `italian`, or `japanese`. (Enter the language in lowercase, as shown.)
1. A collection is the location of your data in the cloud. Only one collection is required. To create a collection, enter `kale create collection {collection_name}`.

You have now finished creating and configuring instances of the {{site.data.keyword.documentconversionshort}} and {{site.data.keyword.retrieveandrankshort}} services. Enter `kale list` to display the following details:

-   `document_conversion_service`: `{name}`
-   `retrieve_and_rank_service`: `{name}`
-   `cluster`: `{name}`
-   `solr_configuration`: `{language}`
-   `collection`: `{name}`
