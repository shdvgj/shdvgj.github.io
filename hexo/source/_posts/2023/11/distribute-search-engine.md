---
title: 分布式搜索引擎笔记
date: 2023-11-28 11:18:22
categories: 
- 学习
tags:
- 分布式搜索引擎
---

### 什么是分布式搜索引擎？

分布式搜索引擎是一种搜索引擎系统，它利用多台计算机或服务器进行协作，共同处理搜索请求和索引数据，以提供更快速、更强大的搜索能力。
传统的搜索引擎通常是基于集中式架构构建的，其中单个服务器或集群负责处理所有搜索请求和索引数据。然而，随着互联网规模的不断扩大和数据量的急剧增长，集中式搜索引擎面临着许多挑战，如单点故障、可扩展性和性能瓶颈。
分布式搜索引擎通过将搜索和索引任务分发到多个节点上进行并行处理，充分利用计算资源和存储能力。它可以将索引数据分片存储在不同的服务器上，每个服务器负责处理自己所存储的数据片段。当用户提交搜索请求时，请求可以发送到任意一个节点，并且每个节点可以同时进行搜索操作，最后将结果汇总返回给用户。
分布式搜索引擎的好处包括：
- 高可扩展性：可以根据需要增加或减少节点，以适应不断增长的搜索负载和数据量。
- 高性能：利用并行处理和分布式存储，可以提供更快的搜索响应时间和更高的吞吐量。
- 容错性：由于数据和计算任务被分散到多个节点，系统具有更高的容错性，即使某个节点发生故障，搜索引擎仍然可以继续工作。
- 水平扩展：通过添加更多的节点，可以实现搜索引擎的水平扩展，而无需进行大规模的硬件升级。
- 分布式索引：索引数据可以分布在多个节点上，减轻了单个节点的负载压力，并提高了索引的性能和可用性。

### 常见搜索引擎

1. [Elasticsearch](https://www.elastic.co/cn/elasticsearch)：Elasticsearch 是一个开源的分布式搜索和分析引擎，基于 Apache Lucene 构建。它具有强大的全文搜索能力、实时数据分析和可视化等功能。Elasticsearch 使用分布式架构，数据被分片存储在多个节点上，支持水平扩展和高可用性。它还提供了丰富的 RESTful API，使得与其他应用程序的集成变得简单。
2. [Apache Solr](https://solr.apache.org/)：Apache Solr 是一个基于 Apache Lucene 的开源搜索平台。它提供了强大的全文搜索、分布式索引和分布式查询的功能。Solr 支持多种数据格式和数据源，可以处理结构化和非结构化数据。它具有灵活的配置选项和可扩展性，可以通过添加更多的节点来扩展搜索能力。
3. [Apache Lucene](https://lucene.apache.org/)：Apache Lucene 是一个开源的全文搜索引擎库，提供了用于索引和搜索的核心功能。虽然 Lucene 本身是一个单机引擎，但它为构建分布式搜索引擎提供了基础。许多分布式搜索引擎，如 Elasticsearch 和 Solr，都是基于 Lucene 构建的。
4. Amazon CloudSearch：Amazon CloudSearch 是亚马逊提供的一项托管式分布式搜索服务。它使用分布式架构和自动化管理，提供了高度可扩展的搜索解决方案。CloudSearch 支持全文搜索、自定义搜索域、多语言支持和搜索结果排序等功能。它与其他 AWS 服务集成紧密，并提供简单的 API 和管理控制台。

### Elasticsearch介绍

#### 概念

- 索引index（当成数据库中的一张表）
- 类型type（V7.X之后没有类型）
- 文档document（相当于每天数据）
- 字段field
- 映射mappings（相当于表结构的类型定义）
- NRT（near real time）
- shard（数据分片）
- replica（备份分片）
- 倒排索引nverted Index：正排索引是通过key-value，倒排索引通过value-key来查找
- 分词：分词（Tokenization）是指将待索引的文本按照一定规则进行切分，将其分解成一个个词项（Tokens）的过程。分词是全文搜索的基础，它将文本转化为可索引的最小单位，使搜索引擎能够理解和处理文本数据。
  - 自带分词器
  - 第三方分词器插件

#### 原理

Elasticsearch的全文搜索底层原理主要涉及两个关键组件：倒排索引（Inverted Index）和分词器（Tokenizer）。

1. 倒排索引（Inverted Index）：
   - 倒排索引是Elasticsearch的核心数据结构，用于快速定位文档中包含特定词项的位置。它是一个将词项映射到文档的数据结构，而不是将文档映射到词项。
   - 在建立索引过程中，Elasticsearch会对文档中的每个字段进行分词处理，将文本分割成一个个词项。然后，针对每个词项，将其映射到包含该词项的文档及其位置信息上。
   - 倒排索引的建立和维护使得Elasticsearch能够快速定位包含特定词项的文档，从而实现高效的全文搜索。

2. 分词器（Tokenizer）：
   - 分词器是用于将文本拆分成词项的组件。Elasticsearch提供了多种内置的分词器，如标准分词器（Standard Tokenizer）、简单分词器（Simple Analyzer）、语言特定的分词器（如中文分词器、英文分词器等）。
   - 在索引和搜索过程中，文本会首先经过分词器的处理，将其拆分成独立的词项。分词器通常会去除停用词（如常见的无实际意义的词汇），进行大小写转换、词干提取等文本处理操作。

基于以上原理，当进行全文搜索时，Elasticsearch会执行以下步骤：
1. 输入查询语句。
2. 查询语句经过分词器处理，将其拆分成独立的词项。
3. 对每个词项，在倒排索引中查找包含该词项的文档和位置信息。
4. 根据查询语句的逻辑关系（如AND、OR）和权重（如词项的重要性），计算文档的相关性得分。
5. 根据得分对文档进行排序，返回搜索结果。

通过倒排索引和分词器的结合，Elasticsearch实现了快速、灵活且高效的全文搜索功能。同时，Elasticsearch还提供了丰富的查询语法和高级搜索功能，可以进行模糊匹配、通配符匹配、正则表达式搜索等，以满足各种复杂的搜索需求。

#### 常用操作

- 创建索引：使用PUT请求创建一个索引，并定义其字段映射和设置。
```json
PUT /my_index
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 2
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text"
      },
      "description": {
        "type": "text"
      }
    }
  }
}
```
- 索引文档：使用POST请求将文档索引到指定的索引中，可以指定文档ID或由ES自动生成。
```json
POST /my_index/_doc/1
{
  "title": "Elasticsearch Introduction",
  "description": "This is a tutorial on Elasticsearch."
}
```
- 获取文档：使用GET请求获取指定索引和文档ID的文档内容。
```json
GET /my_index/_doc/1
```
- 更新文档：使用POST或PUT请求更新指定索引和文档ID的文档内容。
```json
POST /my_index/_update/1
{
  "doc": {
    "description": "This is an updated tutorial on Elasticsearch."
  }
}
```
- 删除文档：使用DELETE请求删除指定索引和文档ID的文档。
```json
DELETE /my_index/_doc/1
```
- 搜索：使用POST请求执行搜索操作，可以使用各种查询语句和过滤条件来过滤和排序搜索结果。
```json
POST /my_index/_search
{
  "query": {
    "match": {
      "title": "Elasticsearch"
    }
  }
}
```
- 聚合：使用聚合查询对搜索结果进行分组、汇总和计算统计信息。
```json
POST /my_index/_search
{
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```
- 映射管理：管理索引的字段映射，包括定义字段类型、分词器、分析器等。
- 设置管理：管理索引的各种设置，包括分片和副本配置、刷新间隔、索引别名等。
- 批量操作：使用批量API一次性执行多个索引、更新或删除操作，提高效率。
- 高级搜索：使用诸如布尔查询、范围查询、模糊查询、正则表达式查询等高级查询语句进行复杂的搜索操作。
- 分词器和分析器：配置和使用不同类型的分词器和分析器来处理文本数据的分词和分析。
- 节点管理：监控和管理ES集群中的节点，包括节点状态、健康状况、性能指标等。
- 集群管理：管理ES集群的各种设置和配置，包括节点分配、集群状态、集群健康状况等。
- 安全性设置：配置访问控制、用户认证和权限管理，保护ES集群的安全性。

#### 查询语法

1. 简单查询：
   - 匹配查询（Match Query）：用于从指定字段中查找包含指定关键词的文档。
     ```json
     GET /my_index/_search
     {
       "query": {
         "match": {
           "title": "Elasticsearch"
         }
       }
     }
     ```
   - 词语查询（Term Query）：用于精确匹配一个词语，不进行分词。
     ```json
     GET /my_index/_search
     {
       "query": {
         "term": {
           "category": "books"
         }
       }
     }
     ```

2. 复合查询：
   - 布尔查询（Bool Query）：用于组合多个查询条件，支持逻辑运算符（MUST、MUST_NOT、SHOULD）。
     ```json
     GET /my_index/_search
     {
       "query": {
         "bool": {
           "must": [
             { "match": { "title": "Elasticsearch" } },
             { "term": { "category": "books" } }
           ],
           "must_not": [
             { "term": { "author": "Smith" } }
           ]
         }
       }
     }
     ```

3. 过滤查询：
   - 范围查询（Range Query）：用于匹配指定范围内的值。
     ```json
     GET /my_index/_search
     {
       "query": {
         "range": {
           "price": {
             "gte": 10,
             "lte": 100
           }
         }
       }
     }
     ```
   - 存在查询（Exists Query）：用于检查指定字段是否存在。
     ```json
     GET /my_index/_search
     {
       "query": {
         "exists": {
           "field": "description"
         }
       }
     }
     ```

4. 全文查询：
   - 多字段查询（Multi-match Query）：在多个字段中搜索指定关键词。
     ```json
     GET /my_index/_search
     {
       "query": {
         "multi_match": {
           "query": "Elasticsearch",
           "fields": ["title", "description"]
         }
       }
     }
     ```
   - 模糊查询（Fuzzy Query）：用于模糊匹配类似于指定关键词的文档。
     ```json
     GET /my_index/_search
     {
       "query": {
         "fuzzy": {
           "title": {
             "value": "Elastiksearch",
             "fuzziness": "AUTO"
           }
         }
       }
     }
     ```

5. 高级查询：
   - 正则表达式查询（Regexp Query）：使用正则表达式匹配文档字段的值。
     ```json
     GET /my_index/_search
     {
       "query": {
         "regexp": {
           "title": "elasticsearch.*"
         }
       }
     }
     ```
   - 前缀查询（Prefix Query）：用于匹配具有指定前缀的文档。
     ```json
     GET /my_index/_search
     {
       "query": {
         "prefix": {
           "title": "ela"
         }
       }
     }
     ```

这些示例只是Elasticsearch查询语法中的一小部分。ES还提供了许多其他类型的查询，如嵌套查询（Nested Query）、通配符查询（Wildcard Query）、模糊查询（Match Phrase Query）等，以及聚合、排序、分页等高级功能。要了解更多信息和更复杂的查询语法，请参阅Elasticsearch的官方文档。

#### 同时使用多个分词器

在Elasticsearch中，一个字段只能使用一种分词器进行分词。但是，你可以通过使用多个字段和合并查询的方式来实现一个字段同时使用两种分词器的效果。下面是一种实现方法：

1. 创建两个字段：
   在索引映射中，为该字段创建两个不同的子字段，每个子字段使用不同的分词器。例如，假设你有一个名为`content`的字段，你可以创建`content.standard`和`content.custom`两个子字段。

   示例索引映射：
   ````json
   {
     "mappings": {
       "properties": {
         "content": {
           "type": "text",
           "fields": {
             "standard": {
               "type": "text",
               "analyzer": "standard"
             },
             "custom": {
               "type": "text",
               "analyzer": "custom_analyzer"
             }
           }
         }
       }
     }
   }
   ```

2. 进行查询：
   在进行查询时，你可以使用合并查询（`bool`查询）来同时搜索两个子字段。使用`should`子句将两个子字段的查询条件组合起来。

   示例查询：
   ````json
   {
     "query": {
       "bool": {
         "should": [
           {
             "match": {
               "content.standard": "search term"
             }
           },
           {
             "match": {
               "content.custom": "search term"
             }
           }
         ]
       }
     }
   }
   ```

通过以上步骤，你可以实现一个字段同时使用两种分词器的效果。这种方法可以让你在查询时同时考虑两种不同的分词方式，以增加搜索的灵活性和准确性。