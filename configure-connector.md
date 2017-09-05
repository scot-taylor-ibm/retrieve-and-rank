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

# Configuring connector and seed options
{: #modify_config}

When crawling data, the Data Crawler first identifies the type of data repository (connector) and the user-specified starting location (seed) at which to begin downloading information. Seeds are the starting points of a crawl and are used by the Data Crawler to retrieve data from the resource that is identified by the connector.
{: shortdesc}

Typically, seeds configure URLs to access protocol-based resources such as fileshares, SMB shares, databases, and other data repositories that are accessible by various protocols. Moreover, different seed URLs have different capabilities. Seeds can also be repository-specific to enable crawling of specific third-party applications such as customer relationship management (CRM) systems, product life-cycle (PLC) systems, content-management systems (CMS), cloud-based applications, and web database applications.

> **Important:** When using the Data Crawler, data repository security settings are ignored.

To crawl your data correctly, you must ensure that the Data Crawler is properly configured to read your data repository. The Data Crawler provides connectors to support data collection from the following repositories:

-   [Filesystem](#config_fs)
-   [Databases via JDBC](#config_db)
-   [CMIS (Content Management Interoperability Services)](#config_cmis)
-   [SMB (Server Message Block), CIFS (Common Internet Filesystem), or Samba fileshares](#config_smb)
-   [SharePoint and SharePoint Online](#config_sp)

A connector configuration template is also provided, which allows you to customize a connector.

To access the in-product manual with the most up-to-date information for the connector and seed configuration files, enter the following commands from the Data Crawler installation directory:

-   For connector configuration options:

    ```bash
    man crawler-options.conf
    ```
    {: pre}

-   For crawl seed configuration options:

    ```bash
    man crawler-seed.conf
    ```
    {: pre}

## Configuring Filesystem crawl options
{: #config_fs}

The Filesystem Connector allows you to crawl files that are local to the Data Crawler installation.

### Configuring the Filesystem Connector

The basic configuration options that are required to use the Filesystem connector follow. To set these values, open the file `config/connectors/filesystem.conf` and modify the following values specific to your use cases:

-   **`protocol`** - The name of the connector protocol that is used for the crawl. Use `sdk-fs` for this connector.
-   **`collection`** - This attribute is used to unpack temporary files. The default value is `crawler-fs`.
-   **`logging-config`** - Specifies the file used for configuring logging options; it must be formatted as a `log4j` XML string.
-   **`classname`** - The Java class name for the connector. The value to use this connector must be `plugin:filesystem.plugin@filesystem`.

### Configuring the Filesystem Crawl Seed

The following values can be configured for the Filesystem crawl seed file. To set these values, open the file `config/seeds/filesystem-seed.conf` and specify the following values specific to your use cases:

-   **`url`** - A newline-separated list of files and folders to crawl. UNIX users can use a path such as `/usr/local/`. Note that URLs must start with `sdk-fs://`. For example, to crawl `/home/watson/mydocs`, use the value `sdk-fs:///home/watson/mydocs`. *The the third `/` is necessary!*

    The filesystems used by Linux, UNIX, and UNIX-like computer systems can contain special types of files, such as block and character device nodes and files that represent named pipes. These files cannot be crawled because they do not contain data; they serve as device or I/O access points. Attempting to crawl such files generates errors during the crawl.

    To avoid such errors, exclude the `/dev` directory in any top-level crawl on a Linux, UNIX, or UNIX-like filesystem. If present on the system that you are crawling, also exclude temporary system directories such as `/proc`, `/sys`, and `/tmp`, which contain transient files and system information.
-   **`hops`** - Internal use only.
-   **`default-allow`** - Internal use only.

## Configuring Database crawl options
{: #config_db}

The database connector allows you to crawl a database by executing a custom SQL command and creating one document per row (record) and one content element per column (field). You can specify a column to be used as a unique key, as well as a column containing a timestamp representing the last-modification date of each record. The connector retrieves all records from the specified database and can also be restricted to specific tables, joins, and so on in the SQL statement.

The Database connector allows you to crawl the following databases. The connector retrieves all records from the specified database and table.

-   {{site.data.keyword.IBM_notm}} DB2
-   {{site.data.keyword.mysql}}
-   Oracle PostgreSQL
-   Microsoft SQL Server
-   Sybase
-   Other SQL-compliant databases via a JDBC 3.0-compliant driver

### JDBC Drivers

The Database connector ships with Oracle Java Database Connectivity (JDBC) driver version 1.5. All third-party JDBC drivers shipped with the Data Crawler are located in the `connectorFramework/crawler-connector-framework-#.#.#/lib/java/database` directory of your Data Crawler installation; you can add, remove, and modify them as necessary. You can also use the `extra_jars_dir` setting in the `crawler.conf` file to specify another location.

-   **DB2 JDBC Drivers:** The Data Crawler does not ship with the JDBC drivers for DB2 due to licensing issues. However, all DB2 installations in which you have installed JDBC support include the JAR files that the Data Crawler requires in order to be able to crawl a DB2 installation. To crawl a DB2 instance, you must copy these files into the appropriate directory in your Data Crawler installation so that the Database connector can use them. To enable the Data Crawler to crawl a DB2 installation:
    1.  Locate the `db2jcc.jar` and license (typically, `db2jcc_license_cu.jar`) JAR files in your DB2 installation.
    1.  Copy these files to the `connectorFramework/crawler-connector-framework-#.#.#/lib/java/database` subdirectory of your Data Crawler installation directory. You can use the `extra_jars_dir` setting in the `crawler.conf` file to specify another location.
-   **{{site.data.keyword.mysql}} JDBC Drivers:** The Data Crawler does not ship with the JDBC drivers for {{site.data.keyword.mysql}} because of possible licensing issues if they are delivered as part of the product. However, downloading the JAR file that contains the {{site.data.keyword.mysql}} JDBC drivers and integrating that JAR file into your Data Crawler installation is straightforward:
    1.  Use a web browser to visit the {{site.data.keyword.mysql}} download site. Locate the source and binary download link for the archive format that you want to use (typically `zip` for Microsoft Windows systems or a `gzip` tarball for Linux systems). Click the link to initiate the download process. Registration might be required.
    1.  Use the appropriate `unzip archive-file-name` or `tar zxf archive-file-name` command to extract the contents of that archive based on the type and name of the archive file that you download.
    1.  Change to the directory that was extracted from the archive file and copy the JAR file from this directory to the `connectorFramework/crawler-connector-framework-#.#.#/lib/java/database` subdirectory of your Data Crawler installation directory. You can use the `extra_jars_dir` setting in the `crawler.conf` file to specify another location.

### Configuring the Database Connector

The basic configuration options that are required to use the Database connector follow. To set these values, open the file `config/connectors/database.conf` and modify the following values specific to your use cases:

-   **`protocol`** - The name of the connector protocol used for the crawl. The value for this connector is based on the database system that is to be accessed.
-   **`collection`** - This attribute is used to unpack temporary files.
-   **`classname`** - The Java class name for the connector. The value to use this connector must be `plugin:database.plugin@database`.
-   **`logging-config`** - Specifies the file that is used for configuring logging options; it must be formatted as a `log4j` XML string.

### Configuring the Database Crawl Seed

The following values can be configured for the Database crawl seed file. To set these values, open the file `config/seeds/database-seed.conf` and specify the following values specific to your use cases:

-   **`url`** - The URL of the table or view to retrieve. The value defines your custom SQL database seed URL. The structure is
    -  `database-system://host:port/database?[per=num]&[sql=SQL]`

    Testing a seed URL shows all of the enqueued URLs. For example, testing the following URL for a database containing 200 records:
    -   `sqlserver://test.mycommpany.com:1433/WWII_Navy/?per=100&sql=select_*_from_vessel&`

    shows the following enqueued URLs:
    -   `sqlserver://test.mycommpany.com:1433/WWII_Navy/?key-val=0&`
    -   `sqlserver://test.mycommpany.com:1433/WWII_Navy/?key-val=100&`
    -   `sqlserver://test.mycommpany.com:1433/WWII_Navy/?key-val=200&`

    While testing, the following URL shows the data retrieved from row 43:
    -   `sqlserver://test.mycompany.com:1433/WWII_Navy/?per=1&key-val=43`
-   **`hops`** - Internal use only.
-   **`default-allow`** - Internal use only.
-   **`user-password`** - The credentials for the database system. The username and password need to be separated by a `:`, and the password must be encrypted by using the `vcrypt` program that is shipped with the Data Crawler. For example, `username:[[vcrypt/3]]passwordstring`.
-   **`max-data-size`** - The maximum size of the data for a document. This is the largest block of memory that can be loaded at one time. Increase this limit only if you have sufficient memory on your computer.
-   **`filter-exact-duplicates`** - Internal use only.
-   **`timeout`** - Internal use only.
-   **`jdbc-class`** (Extender option) - When specified, this string overrides the JDBC class that is used by the connector when `(other)` is chosen as the database system.
-   **`connection-string`** (Extender option) - When specified, this string overrides the automatically generated JDBC connection string. This allows you to provide more detailed configuration for the database connection, such as load-balancing or SSL connections. For example, `jdbc:netezza://127.0.0.1:5480/databasename`.
-   **`save-frequency-for-resume`** (Extender option) - Specifies the name of a column or associated label, which lets the Data Crawler resume a crawl or do a partial refresh. The seed saves the name of this column at regular intervals as it proceeds with the crawl, and saves it again once the last row of your database has been crawled. When resuming or refreshing a crawl, the crawl begins with the row that is identified in the saved value for this field.

## Configuring CMIS crawl options
{: #config_cmis}

The Content-Management Interoperability Services (CMIS) connector lets you crawl CMIS-enabled Content-Management System (CMS) repositories, such as Alfresco, Documentum, or {{site.data.keyword.IBM_notm}} Content Manager, and index the data that they contain.

### Configuring the CMIS Connector

The basic configuration options that are required to use the CMIS connector follow. To set these values, open the file `config/connectors/cmis.conf` and specify the following values specific to your use cases:

-   **`protocol`** - The name of the connector protocol that is used for the crawl. The value to use this connector must be `cmis`.
-   **`collection`** - This attribute is used to unpack temporary files.
-   **`dns`** - Unused option.
-   **`classname`** - The Java class name for the connector. Use `plugin:cmis-v1.1.plugin@connector` for this connector.
-   **`logging-config`** - Specifies the file that is used for configuring logging options; it must be formatted as a `log4j` XML string.
-   **`endpoint`** - The service endpoint URL of a CMIS-compliant repository. For example, the URL structures for SharePoint are the following:
    -   For AtomPub binding: `http://yourserver/_vti_bin/cmis/rest?getRepositories`
    -   For WebServices binding: `http://yourserver/_vti_bin/cmissoapwsdl.aspx`
-   **`username`** - The user name of the CMIS repository user that is used to access the content. This user must have access to all of the target folders and documents to be crawled and indexed.
-   **`password`** - The password of the CMIS repository user that is used to access the content. Do *not* encrypt the password; provide it in plain text.
-   **`repositoryid`** - The ID of the CMIS repository that is used to access the content for that specific repository.
-   **`bindingtype`** - Identifies the type of binding that is is to be used to connect to a CMIS repository. The value is either `AtomPub` or `WebServices`.
-   **`authentication`** - Identifies the type of authentication mechanism that is to be used while contacting a CMIS-compatible repository: `Basic HTTP Authentication`, `NTLM`, or `WS-Security(username_token)`.
-   **`enable-acl`** - Enables retrieving ACLs for crawled data. If you are not concerned about security for the documents in this collection, disabling this option increases performance by not requesting this information with the document and not retrieving and encoding this information. The value is either `true` or `false`.
-   **`user-agent`** - A header that is sent to the server when crawling documents.
-   **`method`** - The method (`GET` or `POST`) by which parameters are to be passed.
-   **`url-logging`** - The extent to which crawled URLs are to be logged. Possible values are
    -   `full-logging` - Log all information about the URL.
    -   `refined-logging` - Log only the information necessary to browse the crawler log and for the connector to function correctly; this is the default value.
    -   `minimal-logging` - Log only the minimum amount of information necessary for the connector to function correctly.

    Setting this option to `minimal-logging` reduces the size of the logs and achieves a slight performance increase due to the smaller I/O associated with minimizing the amount of logged data.
-   **`ssl-version`** - Specifies a version of SSL to use for HTTPS connections. By default, the strongest available protocol is used.

### Configuring the CMIS Crawl Seed

The following values can be configured for the CMIS crawl seed file. To set these values, open the file `config/seeds/cmis-seed.conf` and modify the following values specific to your use cases:

-   **`url`** - The URL of a folder from the CMIS repository that is to be used as a starting point of the crawl, for example: `cmis://alfresco.test.com:8080/alfresco/cmisatom?folderToProcess=workspace://SpacesStore/guid`.

    To crawl from the root folder, you need to give the URL as `cmis://alfresco.test.com:8080/alfresco/cmisatom?folderToProcess=`.
-   **`at`** - Unused option.
-   **`default-allow`** - Internal use only.

## Configuring SMB/CIFS/Samba crawl options
{: #config_smb}

The Samba connector allows you to crawl Server Message Block (SMB) and Common Internet Filesystem (CIFS) fileshares. This type of fileshare is common on Windows networks and is also provided through the open source project Samba.

### Configuring the Samba Connector

The basic configuration options that are required to use the Samba connector follow. To set these values, open the file `config/connectors/samba.conf` and specify the following values specific to your use cases:

-   **`protocol`** - The name of the connector protocol that is used for the crawl. The value to use this connector is `smb`.
-   **`collection`** - This attribute is used to unpack temporary files.
-   **`classname`** - The Java class name for the connector. The value to use this connector must be `plugin:smb.plugin@connector`.
-   **`logging-config`** - Specifies the file that is used for configuring logging options; it must be formatted as a `log4j` XML string.
-   **`username`** - The Samba username with which to authenticate. If provided, the domain and password must also be provided. If not provided, the guest account is used.
-   **`password`** - The Samba password with which to authenticate. If the username is provided, this field is required. The password must be encrypted by using the `vcrypt` program that is shipped with the Data Crawler.
-   **`archive`** - Enables the Samba connector to crawl and index files that are compressed within archive files. The value is either `true` or `false`; the default value is `false`.
-   **`max-policies-per-handle`** - Specifies the maximum number of Local Security Authority (LSA) policies that can be opened for a single RPC handle. These policies define the access permissions that are required to query or modify a particular system under various conditions. The default value for this option is `255`.
-   **`crawl-fs-metadata`** - Enabling this option causes the Data Crawler to add a VXML document that contains the available filesystem metadata about the file (creation date, last modified date, file attributes, and so on).
-   **`enable-arc-connector`** - Unused option.
-   **`disable-indexes`** - A newline-separated list of indexes to disable, which can result in a faster crawl:
    -   `disable-url-index`
    -   `disable-error-state-index`
    -   `disable-crawl-time-index`
-   **`exact-duplicates-hash-size`** - Sets the size of the hash table that is used for resolving exact duplicates. Be very careful when modifying this number. Specify a prime number; larger numbers can provide faster lookups but require more memory, while smaller sizes can slow down crawls but substantially reduce memory usage.
-   **`user-agent`** - Unused option.
-   **`timeout`** - Unused option
-   **`n-concurrent-requests`** - The number of requests that are to be sent in parallel to a single IP address. The default is `1`.
-   **`enqueue-persistence`** - Unused option.

### Configuring the Samba Crawl Seed

The following values can be configured for the Samba crawl seed file. To set these values, open the file `config/seeds/samba-seed.conf` and specify the following values specific to your use cases:

-   **`url`** - A newline-separated list of shares to crawl, for example:

    ```
    smb://share.test.com/office
    smb://share.test.com/cash/money/change
    smb://share.test.com/C$/Program Files
    ```
    {: codeblock}

-   **`hops`** - Internal use only.
-   **`default-allow`** - Internal use only.

## Configuring SharePoint crawl options
{: #config_sp}

> **Important:** The SharePoint connector requires Microsoft SharePoint Server 2007 (MOSS 2007), SharePoint Server 2010, SharePoint Server 2013, or SharePoint Online.

The SharePoint connector allows you to crawl SharePoint objects and index the information that they contain. An object such as a document, user profile, site collection, blog, list item, membership list, or directory page can be indexed with its associated metadata. For list items and documents, indexes can include attachments.

-   The SharePoint connector respects the `noindex` attribute on all SharePoint objects, regardless of their specific type (blogs, documents, user profiles, or other). A single document is returned for each result.
-   The SharePoint account that you use to crawl your SharePoint sites must have at least full read-access privileges.

### Configuring the SharePoint Connector

The basic configuration options that are required to use the SharePoint connector follow. To set these values, open the file `config/connectors/sharepoint.conf` and modify the following values specific to your use cases:

-   **`protocol`** - The name of the connector protocol that is used for the crawl. The value to use this connector is `io-sp`.
-   **`collection`** - This attribute is used to unpack temporary files.
-   **`classname`** - The Java class name for the connector. Use `plugin:io-sharepoint.plugin@connector` for this connector.
-   **`logging-config`** - Specifies the file that is used for configuring logging options; it must be formatted as a `log4j` XML string.
-   **`seed-url-type`** - Identifies the type of SharePoint object to which the provided seed URLs point: site collections or web applications (also known as virtual servers).
    -   `Site Collections` - If the Seed URL Type is set to `Site Collections`, only the children of the site collection referred to by the URL are crawled.
    -   `Web Applications` - If the Seed URL Type is set to `Web Applications`, all of the site collections (and their children) belonging to the web applications referred to by each URL are crawled.
-   **`auth-type`** - The authentication mechanism to use when contacting the SharePoint server: `BASIC`, `NTLM2`, `KERBEROS`, or `CBA`. The default authentication type is `NTLM2`.
-   **`spUser`** - The username of the SharePoint user that is used to access the content. This user must have access to all the target sites and lists to be crawled and indexed, and it must be able to retrieve and resolve the associated permissions. It is better to enter it with the domain name, for example: `MYDOMAIN\\Administrator`.
-   **`spPassword`** - The password of the SharePoint user that is used to access the content. The password must be encrypted by using the `vcrypt` program that is shipped with the Data Crawler.
-   **`cba-sts`** - The URL for the Security Token Service (STS) endpoint against which to attempt to authenticate the crawl user. For SharePoint on-premise with ADFS, specify your ADFS endpoint. If the Authentication Type is set to `CBA` (Claims Based Authentication), this field is required.
-   **`cba-realm`** - The relaying party trust identifier to use when requesting a security token from the STS. This is sometimes known as the "AppliesTo" value or the "Realm". For SharePoint Online, specify the URL to the root of the SharePoint Online instance (for example, `https://mycompany.sharepoint.com`). For ADFS, this is the ID value for the Relying Party Trust between SharePoint and ADFS (for example, `"urn:SHAREPOINT:adfs"`).
-   **`everyone-group`** - When specified, this group name is used in the ACLs when access is to be given to everyone. This field is required when crawling user profiles is enabled.

    > **Note:** Security is not yet respected by the {{site.data.keyword.retrieveandrankshort}} service.
-   **`user-profile-master-url`** - The base URL that the connector uses to build links to user profiles. Configure this field to point to the display form for user profiles. If the token `%FIRST_SEED%` is encountered, it is replaced with the first seed URL. The value is required when crawling user profiles is enabled.
-   **`urls`** - A newline-separated list of HTTP URLs of SharePoint web applications or site collections to crawl.
-   **`ehcache-config`** - Unused option.
-   **`method`** - The method (`GET` or `POST`) by which parameters are to be passed.
-   **`cache-types`** - Unused option.
-   **`cache-size`** - Unused option.
-   **`enable-acl`** - Enables crawling of SharePoint user profiles. Accepted values are `true` or `false`; the default value is `false`.

### Configuring the SharePoint Crawl Seed

The following additional values can be configured for the SharePoint crawl seed file. To set these values, open the file `config/seeds/sharepoint-seed.conf` and specify the following values specific to your use cases:

-   **`url`** - A newline-separated list of URLs of SharePoint web applications or site collections to crawl. For example:

    ```
    io-sp://a.com
    io-sp://b.com:83/site
    io-sp://c.com/site2
    ```
    {: codeblock}

    The sub-sites of these sites are also crawled (unless they are excluded by other crawling rules).
-   **`filter-url`** - A newline-separated list of URLs of SharePoint web applications or site collections to crawl. For example:

    ```
    http://a.com
    http://b.com:83/site
    http://c.com/site2
    ```
    {: codeblock}

-   **`hops`** - Internal use only.
-   **`n-concurrent-requests`** - Internal use only.
-   **`delay`** - Internal use only.
-   **`default-allow`** - Internal use only.
-   **`seed-protocol`** - Sets the seed protocol for children of the site collection. This value is necessary when the site collection's protocol is SSL, HTTP, or HTTPS. This value must be the same as the site collection's protocol.

## What to do next

See [Configuring Orchestration service options](/docs/services/retrieve-and-rank/configure-orchestration.html) to tell the Data Crawler how to manage crawled files.
