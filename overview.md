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

# Overview
{: #overview}

After you set up, train, and query the sample data in the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html), you can use the service with your own data.
{: shortdesc}

## Process at a glance
{: #process}

To set up the {{site.data.keyword.retrieveandrankshort}} service for your data, follow this process:

1.  Modify the [configuration](/docs/services/retrieve-and-rank/configure.html). Modify the Apache Solr configuration to match your data.
1.  Create a cluster and collection and add your documents. For details about creating a cluster and collection and about adding documents, see the [Getting started tutorial](/docs/services/retrieve-and-rank/getting-started.html#create-cluster) page.
1.  Prepare [training data](/docs/services/retrieve-and-rank/training-data.html) and train a ranker. Assemble your ground truth and training data. Then train the ranker.
1.  [Rerank](/docs/services/retrieve-and-rank/reranking-results.html) your results. For each reranked answer, query your Solr collection with the answer ID to fetch the text of the answer. Then, present the answer or answers to your users.
