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

# Using the Data Crawler

**Important:** Starting on **11-03-2017**, it will no longer be possible to create a new instance of {{site.data.keyword.retrieveandrankshort}} on Bluemix. Existing service instances will be supported until **10-03-2018**. To continue using features, you will need to [migrate](/docs/services/discovery/migrate-dcs-rr.html).  **Note:** May not apply in select Dedicated environments.

The Data Crawler is a command-line tool that helps you take your documents from the repositories where they reside (for example, file shares, databases, Microsoft Sharepoint&reg;) and push them to the cloud to create a {{site.data.keyword.retrieveandrankshort}} index automatically. The following sections provide instructions to install, configure, and use the Data Crawler.
{: shortdesc}

## Downloading and installing the Data Crawler
{: #install}

The Data Crawler collects the raw data that is eventually used to form search results for the {{site.data.keyword.retrieveandrankshort}} service. When crawling data repositories, the Data Crawler downloads documents and metadata, starting from a user-specified seed URL. The Crawler discovers documents in a hierarchy or otherwise linked from the seed URL and enqueues them for retrieval.

1.  Open a browser and log in to your [{{site.data.keyword.Bluemix_notm}} account ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.ng.bluemix.net){: new_window}.
1.  From your {{site.data.keyword.Bluemix_notm}} Dashboard, select the {{site.data.keyword.retrieveandrankshort}} service you previously created with Kale.
1.  Click the **Download Data Crawler** link to download the Data Crawler.
1.  As an administrator, use the appropriate commands to install the archive file that you downloaded.
    -   On systems such as Red Hat and CentOS that use `rpm` packages, use a command such as the following:

    ```bash
    rpm -i /full/path/to/rpm/package/rpm-file-name
    ```
    {: pre}

    -   On systems such as Ubuntu and Debian that use `deb` packages, use a command such as the following:

    ```bash
    dpkg -i /full/path/to/deb/package/deb-file-name
    ```
    {: pre}

    -   The Data Crawler scripts are installed into `{installation directory}/bin`; for example, `/opt/ibm/crawler/bin`. They are also installed into `/usr/local/bin`. Ensure that `{installation_directory}/bin` and `/usr/local/bin` are on your `PATH` environment variable.

### Known limitations in this release

-   The Data Crawler may hang when running the Filesystem connector with an invalid or missing URL.
-   Configure the `urls_to_filter` value in the `crawler.conf` file such that all of the whitelisted URLs or RegExes are included in a single RegEx expression. See [Configuring crawl options](/docs/services/retrieve-and-rank/using-data-crawler.html#unique_1586055787) for more information.
-   The path to the configuration file passed in the `--config` (`-c`) option must be a qualified path. That is, it must be in the relative formats `config/crawler.conf` or `./crawler.conf`, or it must be an absolute path, `/path/to/config/crawler.conf`. Specifying just `crawler.conf` is possible only if the `orchestration_service.conf` file is in-lined instead of referenced using `include` in the `crawler.conf` file.
-   The Data Crawler is capable of ingesting data at volumes sufficiently large to trigger a known issue in the {{site.data.keyword.retrieveandrankshort}} service. The issue may cause some documents not to be indexed. The {{site.data.keyword.retrieveandrankshort}} service team is actively investigating this issue.

## Setting up the Data Crawler
{: #config_crawl_opts}

To set up the Data Crawler to crawl your repository, you must specify the appropriate input adapter in the `crawler.conf` file and then configure repository-specific information in the input adapter configuration files. First, you must copy the contents of the `{installation_directory}/share/examples/config` directory to a working directory on your local system, for example, `/home/config`.

-   Do not modify the provided configuration example files directly. Copy and then edit them. If you edit the example files in-place, your configuration might be overwritten when upgrading the Data Crawler or might be removed when uninstalling it.
-   References in this guide to files in the `config` directory, such as `config/crawler.conf`, refer to that file in your working directory, not in the installed `{installation_directory}/share/examples/config` directory.

Values specified in the following steps are the defaults in `config/crawler.conf`; they configure the Filesystem connector.

1.  Verify that you are running Java Runtime Environment (JRE) version 8 or higher. Run the command `java -version`, and look for `1.8`. If you are running something earlier than `1.8`, you need to upgrade Java by installing the Java Developer Kit (JDK) 8 from your package management system, from the [{{site.data.keyword.IBM_notm}} JDK ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/developerworks/java/jdk/){: new_window} web site, or from [java.com ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.java.com){: new_window}.

    > **Note:** To run the Data Crawler, your `JAVA_HOME` environment variable must be set correctly or not at all.
1.  Copy the configuration files you created with Kale into the `config` directory; for example:

    ```bash
    cp orchestration_service.conf config/orchestration
    cp orchestration_service_config.json config/orchestration
    ```
    {: pre}

1.  Open the `config/crawler.conf` file in a text editor.
    -   Set the `crawl_config_file` option to `connectors/filesystem.conf`.
    -   Set the `crawl_seed_file` option to `seeds/filesystem-seed.conf`.

    Save and close the `config/crawler.conf` file.
1.  Open the `seeds/filesystem-seed.conf` file in a text editor. Set the `value` attribute directly under the `name="url"` attribute to the file path that you want to crawl; for example:

    ```
    value="sdk-fs:///TMP/MY_TEST_DATA/"
    ```
    {: codeblock}

    Save and close the file. The other options in this file use appropriate defaults.
1.  If necessary, you can edit the `connectors/filesystem.conf` and `seeds/filesystem-seed.conf` files. However, you generally do not need to modify the defaults provided in these files.

After modifying these files, you are ready to crawl your data. Proceed to [Crawling your data repository](/docs/services/retrieve-and-rank/crawling-data.html) to continue.

## Configuring crawl options
{: #unique_1586055787}

The file `config/crawler.conf` contains information that tells the Data Crawler which files to use for its crawl (input adapter), where to send the collection of crawled files once the crawl is complete (output adapter), and other crawl management options. All file paths in this documentation are relative to the `config` directory except where noted.

To access the in-product manual for the `crawler.conf` file with the most up-to-date information, enter the following command from the Data Crawler installation directory:

```bash
man crawler.conf
```
{: pre}

### Input Adapter

You can set the following options for the input adapter in the `crawler.conf` file:

-   **`class`** - Internal use only; defines the Data Crawler input adapter class. Currently, the only input adapter that exists is the default value of `com.ibm.watson.crawler.connectorframeworkinputadapter.Crawl`.
-   **`config`** - Internal use only; defines the connector framework configuration. The default configuration key within this block that is passed to the chosen input adapter is `connector_framework`.

    The connector framework is what allows you to talk to your data. It could be internal data within the enterprise, or it could be external data on the web or in the cloud. The connectors allow access to a number of different data sources, while connecting is actually controlled by the crawling process.

    Data retrieved by the Connector Framework Input Adapter is cached locally. It is not stored in an encrypted format. By default, the data is cached to a temporary directory that is cleared on reboot and is readable only by the user who executed the crawler command.

    There is a chance that this directory can outlive the crawler if the connector framework goes away before it can clean up after itself. Carefully consider the location for your cached data; you can put it on an encrypted filesystem, but be aware of the performance implications of doing so. Only you can decide the appropriate balance between speed and security for your crawls.
-   **`crawl_config_file`** - The configuration file to use for the crawl. The default value is `connectors/filesystem.conf`.
-   **`crawl_seed_file`** - The crawl seed file to use for the crawl. The default value is `seeds/filesystem-seed.conf`.
-   **`id_vcrypt_file`** - The keyfile that is used for data encryption by the Crawler; the default key included with the crawler is `id_vcrypt`. Use the vcrypt script in the `bin` folder if you need to generate a new `id_vcrypt` file.
-   **`crawler_temp_dir`** - The Data Crawler's temporary folder for connector logs. The default value, `tmp`, is provided. If it does not already exist, the `tmp` folder is created in the current working directory.
-   **`extra_jars_dir`** - Adds a directory of extra JAR files to the connector framework classpath. The files are relative to the connector framework `lib/java` directory.
    -   This value must be `oakland` when using the SharePoint connector.
    -   This value must be `database` when using the Database connector.

    You can leave this value empty (specify an empty string, `""`) when using other connectors.
-   **`urls_to_filter`** - A whitelist of URLs to crawl, in regular expression form. The Data Crawler crawls only URLs that match one of the regular expressions provided.

    The domain list contains the most common top-level domains; add to it if necessary.

    The file extension-type list contains the file extensions that the Orchestration Service supports as of this release of the Data Crawler.

    Ensure that your seed URL domain is allowed by the filter. For example, if the seed URL looks like `http://testdomain.test.in`, add "`in`" to the domain filter.

    Ensure that your seed URL is not excluded by a filter; otherwise, the Crawler may hang.
-   **`max_text_size`** - The maximum size, in bytes, that a document can be before it is written to disk by the Connector Framework. Adjusting this value higher decreases the number of documents written to disk but increases the memory requirement. The default value is `1048576`.
-   **`extra_vm_params`** - Allows you to add extra Java parameters to the command that is used to launch the Connector Framework.
-   **`bootstrap_logging`** - Writes a connector framework startup log, which is useful for advanced debugging only. Possible values are `true` or `false`. The log file is written to `crawler_temp_dir`.

### Output Adapter

You can choose from a couple of output adapters. Set the output adapter by setting the `class`.

-   **`class`** - Internal use only; defines the Data Crawler output adapter class. The default value is `com.ibm.watson.crawler.orchestrationserviceoutputadapter.oneatatime.OrchestrationServiceOutputAdapter`.

    You can also set the value of this class to `com.ibm.watson.crawler.testoutputadapter.TestOutputAdapter` to run the Test Output Adapter.
-   **`config`** - Defines which configuration key to pass to the output adapter. The string must correspond to a key within this configuration object. In the following code example, the configuration key is `orchestration_service`:

    ```
    orchestration_service {
      include "orchestration_service.conf"
    }
    ```
    {: codeblock}

    You can also set this value to `test` instead of `orchestration_service`. If `config` is set to `test` instead of `orchestration_service`, the test output directory is `/tmp/crawler-test-output`:

    ```
    test {
      output_directory = "/tmp/crawler-test-output"
    }
    ```
    {: codeblock}

-   **`TestOutputAdapter`** - The Test Output Adapter writes a representation of the crawled files to disk in a specified location. To configure the Data Crawler to use `TestOutputAdapter`, you must set the value of `class` to `com.ibm.watson.crawler.testoutputadapter.TestOutputAdapter` and the value of `config` to `test`.
-   **`retry`** - Specifies the options for retry in case of failed attempts to push to the output adapter.
    -   `max_attempts` - The maximum number of retry attempts. The default value is `4`.
    -   `delay` - The minimum amount of delay between attempts in seconds. The default value is `1`.
    -   `exponent_base` - A factor that determines the growth of the delay time over each failed attempt. The default value is `2`.

    The Data Crawler uses the following formula:

    ```
    d(nth_retry) = delay * (exponent_base ^ nth_retry)
    ```
    {: codeblock}

    For example, the default settings are a delay of one second and an exponent base of two, which cause the second retry (the third attempt) to delay two seconds instead of one, and the next to delay four seconds.

    ```
    d(0) = 1 * (2 ^ 0) = 1 second
    d(1) = 1 * (2 ^ 1) = 2 seconds
    d(2) = 1 * (2 ^ 2) = 4 seconds
    d(3) = 1 * (2 ^ 3) = 8 seconds
    ```
    {: codeblock}

    With the default settings, a submission is attempted up to five times, waiting up to approximately 15 seconds. This time is approximate because there is additional time added in order to avoid having multiple resubmissions execute simultaneously. This "fuzzed" time is up to 10 percent, so the last retry in the previous example could delay up to 8.8 seconds. The wait time does not include the time spent connecting to the service, uploading data, or waiting for a response.

### Additional crawl management options

The following additional options are available for crawl management:

-   **`full_node_debugging`** - Activates debugging mode; possible values are `true` or `false`. Using `true` puts the full data of every document crawled into the logs.
-   **`logging.log4j.configuration_file`** - The configuration file to use for logging. In the sample `crawler.conf` file, this option is defined in `logging.log4j` and its default value is `log4j_custom.properties`. This option must be similarly defined whether using a `.properties` or a `.conf` file.
-   **`shutdown_timeout`** - Specifies the timeout value in minutes before shutting down the application. The default value is `10`.
-   **`output_limit`** - The highest number of indexable items that the Data Crawler tries to send simultaneously to the output adapter. This can be further limited by the number of cores available to do the work. It says that at any given point, no more than *x* indexable items are sent to the output adapter. The default value is `10`.
-   **`input_limit`** - Limits the number of URLs that can be requested from the input adapter at one time. The default value is `3`.
-   **`output_timeout`** - The amount of time in seconds before the Data Crawler gives up on a request to the output adapter and removes the item from the output adapter queue to allow more processing. The default value is `610`.

    Consider the constraints imposed by the output adapter, as those constraints relate to the limits defined here. The `output_limit` relates only to how many indexable objects can be sent to the output adapter at one time. Once an indexable object is sent to the output adapter, it is "on the clock," as defined by the `output_timeout` variable. It is possible that the output adapter itself has a throttle preventing it from being able to process as many inputs as it receives. For instance, the orchestration output adapter might have a connection pool that is configurable for HTTP connections to the service. If it defaults to `8`, for example, and if you set the `output_limit` to a number greater than `8`, you will have processes on the clock waiting for a turn to execute. You might then experience timeouts.
-   **`num_threads`** - The number of parallel threads that can be run at one time. This value can be either an integer, which specifies the number of parallel threads directly, or it can be a string with the format `"xNUM"`, specifying the multiplication factor of the number of available processors, for example, `"x1.5"`. The default value is `"30"`.

## What to do next

See [Configuring connector and seed options](/docs/services/retrieve-and-rank/configure-connector.html) to configure the Data Crawler to read your data repository.
