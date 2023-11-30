# 分布式搜索引擎ElasticSearch实战-5

## 1. ES桶聚合后相关指标分析&text字段聚合方式

如果想要对text类型的字段进行分桶

1、给field增加keyword的子字段

```json
POST /index_customer/_mapping
{
    "properties": {
        "nickname": {
            "analyzer": "ik_max_word",
            "type": "text",
            "fields": {
                "pinyin": {
                    "analyzer": "pinyin",
                    "type": "text"
                },
                "keyword": {
                    "ignore_above": 256,
                    "type": "keyword"
                }
            }
        }
    }
}
# 在数据添加后增加子字段需要将index进行更新
POST /index_customer/_update_by_query
```

2、给field增加fielddata

```json
# fielddata是对text文本进行分词后的桶聚合
# 默认是false，打开会比较占内存，所以没有必要的情况
POST /index_customer/_mapping
{
    "properties": {
        "nickname": {
            "analyzer": "ik_max_word",
            "type": "text",
            "fielddata": true,
            "fields": {
                "pinyin": {
                    "analyzer": "pinyin",
                    "type": "text",
                    "fielddata": true
                },
                "keyword": {
                    "ignore_above": 256,
                    "type": "keyword"
                }
            }
        }
    }
}
```

分桶返回的参数分析

- doc_count_error_upper_bound：可能存在潜在的结果是聚合后结果排行第二的值

- sum_other_doc_count：表示本次聚合中还有多少没有统计展示出
  - 桶默认聚合展示10条
  - 可以使用size来调整条目数
  - 只能指定条目数，不能分页
- buckets：会根据结果的统计降序排列

size进行桶查询的展示

```json
POST /index_customer/_search
{
    "query": {
        "match_all": {}
    },
    "size": 0,
    "aggs": {
        "nickname_term": {
            "terms": {
                "field": "nickname",
                "size": 20
            }
        }
    }
}
```

当doc频繁有数据加入到文档中，并且这个field会频繁进行分桶，需要添加一个缓存配置

```json
# 频繁聚合查询，索引不断有新的doc加入
# "eager_global_ordinals": true
POST /index_customer/_mapping
{
    "properties": {
        "nickname": {
            "analyzer": "ik_max_word",
            "type": "text",
            "fielddata": true,
          	"eager_global_ordinals": true,
            "fields": {
                "pinyin": {
                    "analyzer": "pinyin",
                    "type": "text",
                    "fielddata": true,
                  	"eager_global_ordinals": true
                },
                "keyword": {
                    "ignore_above": 256,
                    "type": "keyword"
                }
            }
        }
    }
}
```

**分组基数查询**

```json
# cardinality统计桶分词的基数
POST /index_customer/_search
{
    "query": {
        "match_all": {}
    },
    "size": 0,
    "aggs": {
        "nickname_term": {
            "cardinality": {
                "field": "nickname"
            }
        }
    }
}
```

## 2. 桶range计算

就是一个区间值的查询

```json
POST POST /index_customer/_search
{
    "query": {
        "match_all": {}
    },
    "size": 0,
    "sort": [
        {
            "consume": "desc"
        }
    ],
    "aggs": {
        "city_count": {
            "terms": {
                "field": "city"
            }
        },
        "consume_range": {
            "range": {
                "field": "consume",
                "ranges": [
                    {
                        "to": 3000
                    },
                    {
                        "from": 3000,
                        "to": 6000
                    },
                    {
                        "from": 6000,
                        "to": 9000
                    },
                    {
                        "from": 9000
                    }
                ]
            }
        }
    }
}
```

直方图的聚合

```json
POST /index_customer/_search
{
    "query": {
        "match_all": {}
    },
    "size": 0,
    "sort": [
        {
            "consume": "desc"
        }
    ],
    "aggs": {
        "city_count": {
            "terms": {
                "field": "city"
            }
        },
        "consume_histogram": {
            "histogram": {
                "field": "consume",
                "interval": 2000,
                "extended_bounds": {
                    "min": 0,
                    "max": 20000
                }
            }
        }
    }
}
```

## 3. Pipeline聚合计算

pipeline就是对聚合分析再做一次聚合分析

```json
# 场景：从所有城市的平均消费中，拿出最低的那个城市
GET /index_customer/_search
{
    "query": {
        "match_all": {}
    },
    "size": 0,
    "sort": [
        {
            "consume": "desc"
        }
    ],
    "aggs": {
        "city_count": {
            "terms": {
                "field": "city"
            },
            "aggs": {
                "avg_consume": {
                    "avg": {
                        "field": "consume"
                    }
                }
            }
        },
        "min_consume_by_city": {
            "min_bucket": {
                "buckets_path": "city_count>avg_consume"
            }
        }
    }
}
# min_bucket / buckets_path 是关键字
# max_bucket / min_bucket / avg_bucket / sum_bucket / stats_bucket
```

## 4. Springboot整合Elasticsearch进行索引操作

POM中增加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

yaml配置

```yaml
# 端口一定要9300
spring:
  data:
    elasticsearch:
      cluster-name: icoding-es
      cluster-nodes: 47.92.163.109:9300
```

创建映射的po

```java
package com.icodingedu.po;

import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;

//indexName相当于给索引明名
//type相当于文档类型
@Data
@Document(indexName = "index_user",type = "_doc",shards = 3,replicas = 1)
public class UserBo {
    //index的doc的id和数据的id一致
    @Id
    private String id;

    //默认不是存储节点，要声明
    @Field(store = true,index = true,analyzer = "ik_max_word",searchAnalyzer = "ik_max_word")
    private String nickname;

    @Field(store = true)
    private Integer sex;

    @Field(store = true)
    private Double consume;

    @Field(store = true,index = true,analyzer = "ik_max_word",searchAnalyzer = "ik_max_word")
    private String review;
}
```

**创建索引的controller**

```java
package com.icodingedu.controller;

import com.icodingedu.po.UserBo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.elasticsearch.core.ElasticsearchTemplate;
import org.springframework.data.elasticsearch.core.query.IndexQuery;
import org.springframework.data.elasticsearch.core.query.IndexQueryBuilder;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class ESUserController {

    @Autowired
    ElasticsearchTemplate elasticsearchTemplate;

    @GetMapping("/create_index")
    @ResponseBody
    public String createIndex(){
        UserBo userBo = new UserBo();
        userBo.setId("1001");
        userBo.setConsume(1899.66);
        userBo.setNickname("空中雄鹰");
        userBo.setReview("icoding edu 艾编程课程非常不错，学起来很给力");
        userBo.setSex(1);

        IndexQuery indexQuery = new IndexQueryBuilder()
                .withObject(userBo)
                .build();
        elasticsearchTemplate.index(indexQuery);
        return "index/mapping/document 一起创建完成";
    }
}
```

**更新索引的mapping**

```java
// 只需要在po里加上字段既可以
// 创建的时候给赋值
// 更新的时候elasticsearchTemplate会根据po的变化判断是否更新
// 在elasticsearchTemplate.index(indexQuery)操作时如果没有index则新建，如果有就创建数据
```

**删除index**

```java
@GetMapping("/delete-index")
@ResponseBody
public String deleteIndex(){
  elasticsearchTemplate.deleteIndex(UserBo.class);
  return "删除成功";
}
```

**ElasticsearchTemplate一般用于对文档数据进行检索应用**

- 对于index的mapping还是使用json来创建
- ET的部分注解不一定生效

## 5. Springboot对ES文档进行操作

更新document

```java
    @GetMapping("/update")
    @ResponseBody
    public String updateIndex(){

        Map<String,Object> data = new HashMap<String,Object>();
        data.put("username","jackwang");
        data.put("consume",7888.99);

        IndexRequest indexRequest = new IndexRequest();
        indexRequest.source(data);

        UpdateQuery updateQuery = new UpdateQueryBuilder()
                .withClass(UserBo.class)
                .withId("1001")
                .withIndexRequest(indexRequest)
                .build();

        elasticsearchTemplate.update(updateQuery);
        return "更新成功";
    }
```

删除document

```java
    @GetMapping("/delete/{id}")
    @ResponseBody
    public String deleteDocument(@PathVariable("id") String uid){
        elasticsearchTemplate.delete(UserBo.class,uid);
        return "删除id:"+uid;
    }
```

根据id获得doc数据

```java
    @GetMapping("/get/{id}")
    @ResponseBody
    public String getIndex(@PathVariable("id") String uid){

        GetQuery query = new GetQuery();
        query.setId(uid);

        UserBo userBo = elasticsearchTemplate.queryForObject(query,UserBo.class);
        return userBo.toString();
    }
```

## 6. Springboot对ES文档进行分页查询

```java
// ES中已有的index映射对象
package com.icodingedu.po;

import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;

@Data
@Document(indexName = "index_customer",type = "_doc")
public class CustomerPo {
    @Id
    private String id;

    @Field(store=true)
    private Integer age;

    @Field(store=true)
    private String username;

    @Field(store=true)
    private String nickname;
    @Field(store=true)
    private Float consume;

    @Field(store=true)
    private String desc;

    @Field(store=true)
    private Integer sex;

    @Field(store=true)
    private String birthday;

    @Field(store=true)
    private String city;

    @Field(store=true)
    private String faceimg;
}
```

查询分页的controller

```java
    @GetMapping("/list")
    @ResponseBody
    public String getList(){
        //3.定义分页
        Pageable pageable = PageRequest.of(0,2);
        //2.定义query对象
        SearchQuery query = new NativeSearchQueryBuilder()
                .withQuery(QueryBuilders.matchQuery("desc","艾编程 学习"))
                .withPageable(pageable)
                .build();
        //1.先写查询
        AggregatedPage<CustomerPo> customerPos = elasticsearchTemplate.queryForPage(query,CustomerPo.class);
        System.out.println("总页数："+customerPos.getTotalPages());
        System.out.println("总记录数："+customerPos.getTotalElements());
        List<CustomerPo> customerPoList = customerPos.getContent();
        for (CustomerPo customerPo:customerPoList) {
            System.out.println(customerPo.toString());

        }
        return "查询完成";
    }
```

## 7. Springboot对ES文档实现高亮查询

```java
//目前已加入高亮的字符，但会报错，无法获得值
    @GetMapping("/listhiglight")
    @ResponseBody
    public String getListHighLight(){
        //4.定义高亮的字符
        String preTag = "<font color='red'>";
        String postTag = "</font>";
        //3.定义分页
        Pageable pageable = PageRequest.of(0,2);
        //2.定义query对象
        SearchQuery query = new NativeSearchQueryBuilder()
                .withQuery(QueryBuilders.matchQuery("desc","艾编程 学习"))
                .withHighlightFields(new HighlightBuilder.Field("desc").preTags(preTag).postTags(postTag))
                .withPageable(pageable)
                .build();
        //1.先写查询,参数里增加高亮的实现
        AggregatedPage<CustomerPo> customerPos = elasticsearchTemplate.queryForPage(query, CustomerPo.class, new SearchResultMapper() {
            @Override
            public <T> AggregatedPage<T> mapResults(SearchResponse searchResponse, Class<T> aClass, Pageable pageable) {
                return null;
            }

            @Override
            public <T> T mapSearchHit(SearchHit searchHit, Class<T> aClass) {
                return null;
            }
        });
        System.out.println("总页数："+customerPos.getTotalPages());
        System.out.println("总记录数："+customerPos.getTotalElements());
        List<CustomerPo> customerPoList = customerPos.getContent();
        for (CustomerPo customerPo:customerPoList) {
            System.out.println(customerPo.toString());

        }
        return "查询完成";
    }
```

实现高亮完整代码

```java
    @GetMapping("/listhiglight")
    @ResponseBody
    public String getListHighLight(){
        //4.定义高亮的字符
        String preTag = "<font color='red'>";
        String postTag = "</font>";
        //3.定义分页
        Pageable pageable = PageRequest.of(0,2);
        //2.定义query对象
        SearchQuery query = new NativeSearchQueryBuilder()
                .withQuery(QueryBuilders.matchQuery("desc","艾编程 学习"))
                .withHighlightFields(new HighlightBuilder.Field("desc").preTags(preTag).postTags(postTag))
                .withPageable(pageable)
                .build();
        //1.先写查询,参数里增加高亮的实现
        AggregatedPage<CustomerPo> customerPos = elasticsearchTemplate.queryForPage(query, CustomerPo.class, new SearchResultMapper() {
            @Override
            public <T> AggregatedPage<T> mapResults(SearchResponse searchResponse, Class<T> aClass, Pageable pageable) {
                List<CustomerPo> customerPoList = new ArrayList<CustomerPo>();
                SearchHits searchHits = searchResponse.getHits();
                for (SearchHit h: searchHits) {
                    HighlightField highlightField = h.getHighlightFields().get("desc");
                    String desc = highlightField.fragments()[0].toString();
                    CustomerPo customerPoHighlight = new CustomerPo();
                    customerPoHighlight.setAge((Integer)h.getSourceAsMap().get("age"));
                    customerPoHighlight.setBirthday(h.getSourceAsMap().get("birthday").toString());
                    customerPoHighlight.setCity(h.getSourceAsMap().get("city").toString());
                    customerPoHighlight.setConsume(Float.valueOf(h.getSourceAsMap().get("consume").toString()));
                    customerPoHighlight.setDesc(desc);//这就是把高亮的字段替换给原字段
                    customerPoHighlight.setFaceimg(h.getSourceAsMap().get("faceimg").toString());
                    customerPoHighlight.setId(h.getSourceAsMap().get("id").toString());
                    customerPoHighlight.setNickname(h.getSourceAsMap().get("nickname").toString());
                    customerPoHighlight.setSex((Integer)h.getSourceAsMap().get("sex"));
                    customerPoHighlight.setUsername(h.getSourceAsMap().get("username").toString());
                    customerPoList.add(customerPoHighlight);
                }
                if(customerPoList.size()>0){
                    return new AggregatedPageImpl<>((List<T>) customerPoList);
                }
                return null;
            }

            @Override
            public <T> T mapSearchHit(SearchHit searchHit, Class<T> aClass) {
                return null;
            }
        });
        System.out.println("总页数："+customerPos.getTotalPages());
        System.out.println("总记录数："+customerPos.getTotalElements());
        List<CustomerPo> customerPoList = customerPos.getContent();
        for (CustomerPo customerPo:customerPoList) {
            System.out.println(customerPo.toString());

        }
        return "查询完成";
    }
```

## 8. Springboot对ES文档进行数据排序

只需要加入排序的构建就ok了

```java
    @GetMapping("/list")
    @ResponseBody
    public String getList(){
        //4.加入排序构建
        SortBuilder sortBuilder1 = new FieldSortBuilder("consume")
                .order(SortOrder.DESC);
        SortBuilder sortBuilder2 = new FieldSortBuilder("age")
                .order(SortOrder.ASC);

        //3.定义分页
        Pageable pageable = PageRequest.of(0,6);
        //2.定义query对象
        SearchQuery query = new NativeSearchQueryBuilder()
                .withQuery(QueryBuilders.matchQuery("desc","学习"))
                .withPageable(pageable)
                .withSort(sortBuilder1)
                .withSort(sortBuilder2)
                .build();
        //1.先写查询
        AggregatedPage<CustomerPo> customerPos = elasticsearchTemplate.queryForPage(query,CustomerPo.class);
        System.out.println("总页数："+customerPos.getTotalPages());
        System.out.println("总记录数："+customerPos.getTotalElements());
        List<CustomerPo> customerPoList = customerPos.getContent();
        for (CustomerPo customerPo:customerPoList) {
            System.out.println(customerPo.toString());

        }
        return "查询完成";
    }
```

![](elasticsearch.md)