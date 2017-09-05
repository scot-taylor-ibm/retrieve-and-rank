---

copyright:
  years: 2015, 2017
lastupdated: "2017-09-01"

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

# Configuring Orchestration service options
{: #config_orch}

The Orchestration service tells the Data Crawler how to manage crawled files. To access the in-product manual for the `orchestration-service.conf` file with the most up-to-date information, enter the following command from the Data Crawler installation directory:
{: shortdesc}

```bash
man orchestration_service.conf
```
{: pre}

Default options can be changed directly by opening the `config/orchestration/orchestration-service.conf` file and providing the following values specific to your use case:

-   **`http_timeout`** - The timeout in seconds for the document read/index operation. The default is `585`.
-   **`concurrent_upload_connection_limit`** - The number of simultaneous connections allowed for uploading documents. The default is `10`. In general, make this value greater than or equal to the `output_limit` that is set when configuring crawl options.
-   **`base_url`** - The URL to which your crawled documents are to be sent.
-   **`endpoint`** - The location of your crawled document collection at the base URL.
-   **`username`** - The username to authenticate to the endpoint location.
-   **`password`** - The password to authenticate to the endpoint location.

    > **Important:** Do *not* use the `vcrypt` program shipped with the Data Crawler to encrypt this password.
-   **`config_file`** - The configuration file that the Orchestration service uses. This is the file that you created by using the Kale command-line tool.

The Orchestration Service Output Adapter can send statistics to allow {{site.data.keyword.IBM_notm}} to better understand and serve its users. You can set the following options for the `send_stats` variable:

-   **`jvm`** - Java Virtual Machine (JVM) statistics that are sent include the Java vendor and version as reported by the JVM that is used to execute the Data Crawler. The value is either `true` or `false`. The default value is `true`.
-   **`os`** - Operating system (OS) statistics that are sent include OS name, version, and architecture as reported by the JVM that is used to execute the Data Crawler. The value is either `true` or `false`. The default value is `true`.

## What to do next

See [Crawling your data repository](/docs/services/retrieve-and-rank/crawling-data.html) to run a crawl against your data repository.
