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

# Crawling your data repository
{: #deploy_crawl}

Once you have properly configured the Data Crawler options, you can run a crawl against your data repository by entering the `crawler` command. The Data Crawler prompts you with documentation that explains what to do. You can run a test crawl or an actual crawl, in addition to other crawl options.
{: shortdesc}

> **Note:** Never run the Crawler as `root` unless you need access to files that only `root` can read.

## Running a test crawl

Enter the following command to perform a test crawl:

```bash
crawler testit
```
{: pre}

The Data Crawler crawls only the seed URL and displays any enqueued URLs. If the seed URL results in indexable content (for example, it is a document), that content is sent to the output adapter and is displayed on the screen. If the seed URL retrieval causes URLs to be enqueued, those URLs are displayed and no content is sent to the output adapter. By default, five enqueued URLs are displayed.

You can also specify a custom configuration file as an option for the `crawl` command, for example:

```bash
crawler testit --config {config/myconfigfile.conf}
```
{: pre}

The path to the configuration file passed in the `--config` option must be a qualified path. That is, it must be in a relative format, such as `config/myconfigfile.conf` or `./myconfigfile.conf`, or be an absolute path, such as `/path/to/config/myconfigfile.conf`. Specifying just `myconfigfile.conf` is possible only if the `orchestration_service.conf` file is in-lined instead of referenced using `include` in the `crawler.conf` file.

Additionally, you can set the limit for the number of enqueued URLs that are displayed as an option for the `testit` command, for example:

```bash
crawler testit --limit {number}
```
{: pre}

## Running a crawl

Enter the following command to run a crawl with the default configuration file (`crawler.conf`):

```bash
crawler crawl
```
{: pre}

You can also specify a custom configuration file as an option for the `crawl` command, for example:

```bash
crawler crawl --config {config/myconfigfile.conf}
```
{: pre}

The path to the configuration file passed in the `--config` option must be a qualified path. That is, it must be in relative format, such as `config/myconfigfile.conf` or `./myconfigfile.conf`, or be an absolute path, such as `/path/to/config/myconfigfile.conf`. Specifying just `myconfigfile.conf` is possible only if the `orchestration_service.conf` file is in-lined instead of referenced using `include` in the `crawler.conf` file.

## What to do next

See [Testing enhanced information retrieval](/docs/services/retrieve-and-rank/testing-enhanced-retrieval.html) to test the complete solution.
