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

# Reranking results
{: #top}

You can use the {{site.data.keyword.retrieveandrankshort}} query parser at runtime to return search results with updated rankings.
{: shortdesc}

## Runtime queries
{: #freeq}

The {{site.data.keyword.retrieveandrankshort}} query parser uses a specific syntax to return updated rankings for your query. By default, you issue the request to the `fcselect` request handler and pass in the ranker ID and the question:

```
/v1/solr_clusters/{solr_cluster_id}/solr/{collection_name}/fcselect?ranker_id={ranker_id}&q={question}
```
{: codeblock}

> **Important:**  Do not call the `/fcselect` request handler without a ranker ID; doing so can have unpredictable results and is therefore not recommended. Also, do not use the `/select` request handler; doing so returns standard Solr search results, which defeats the purpose of having trained the ranker. As discussed elsewhere in this documentation, standard Solr search results are unordered and might not contain the most significant results.

The `q` parameter defines the query. The syntax is different from standard Solr syntax as follows:

-   You can search for a single term or a phrase. You do not need to surround the phrase with double quotation marks as with Solr, but you can include phrases in the query; they are accounted for by the ranker models.
-   You cannot combine multiple terms with Boolean operators (for example, `AND`, `NOT`, and `OR`). If you want to require or exclude documents from results, use the Solr filter query syntax instead (for example, `fq=corpus:Wikipedia`). Using a filter can also improve performance.
-   Common query parameters, such as `rows`, `fl` (list of fields to return), and `wt` (response writer), are supported.

The following is an example full text query that searches against all fields:

```
q=When did John F. Kennedy join the Navy?
```
{: codeblock}

The following modifiers are not supported with the `/fcselect` request handler:

- Index IDs; for example, `/fcselect?q=id:1144`
- Wildcards, such as `*` and `?`
- Fuzzy searching (the machine-learning algorithms perform their own)
- Search by proximity
- Boosting terms in the query

## Reserved characters
{: #reserved}

The following characters have special usage in queries.

<table>
  <caption>Table 1. Reserved characters</caption>
  <tr>
    <th style="width:30%; text-align:left">Character</th>
    <th style="text-align:left">Usage</th>
  </tr>
  <tr>
    <td style="text-align:left">**Ampersand** (`&`)</td>
    <td style="text-align:left">Reserved by the HTML parser and used to separate parameters in the REST request.</td>
  </tr>
  <tr>
    <td style="text-align:left">**Colon** (`:`)</td>
    <td style="text-align:left">Escape a colon in a query with a backslash.</td>
  </tr>
  <tr>
    <td style="text-align:left">**Double-quotation marks** (`"`)</td>
    <td style="text-align:left">Escape double quotation marks in a query with a backslash in field queries.</td>
  </tr>
  <tr>
    <td style="text-align:left">**Backslash** (`\`)</td>
    <td style="text-align:left">Escape character. Escape a backslash in a query with another backslash (`\\`).</td>
  </tr>
</table>
