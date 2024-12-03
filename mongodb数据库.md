使用:
```mongodb
mongodb 想让局域网可以访问:

sudo nvim /etc/mongodb.conf
bindIp: 0.0.0.0

system restart mongodb


redis  想让局域网可以访问:

sudo vim /etc/redis/redis.conf

protected-mode no

bind 0.0.0.0 -::1


```


mongodb创建索引:
```mongodb
db.ozon_products.createIndex({
  "二级类别": 1,
  "三级类别": 1,
  "四级类别": 1,
  "28日销售额": -1,
  "28日订单均价": -1,
  "因缺货而错过的订单金额（₽）": -1,
  "发货仓库数量（个）": -1,
  "交货时间（天）": -1,
  "28天内目录浏览量（个）": -1,
  "28天内商品卡浏览量（个）": -1,
  "从目录到购物车的转化率 (%)": -1,
  "从商品卡到购物车的转化率 (%)": -1,
  "广告费用占比 (%)": -1,
  "28日销量": -1,
  "Updatetime": -1,
  "ID": -1
}, { name: "sales_index" });



db.ozon_products.createIndex({"二级类别": 1}, {name: 'second_category_index'});
db.ozon_products.createIndex({"三级类别": 1}, {name: 'third_category_index'});
db.ozon_products.createIndex({"四级类别": 1}, {name: 'fourth_category_index'});
db.ozon_products.createIndex({"28日销售额": -1}, {name: '28days_sales_index'});
db.ozon_products.createIndex({"28日订单均价": -1}, {name: 'average_order_price_index'});
db.ozon_products.createIndex({"因缺货而错过的订单金额（₽）": -1}, {name: 'missed_order_due_to_stock_index'});
db.ozon_products.createIndex({"发货仓库数量（个）": -1}, {name: 'warehouse_quantity_index'});
db.ozon_products.createIndex({"交货时间（天）": -1}, {name: 'deliver_time_index'});
db.ozon_products.createIndex({"28天内目录浏览量（个）": -1}, {name: '28days_catalog_views_index'});
db.ozon_products.createIndex({"28天内商品卡浏览量（个）": -1}, {name: '28days_product_card_views_index'});
db.ozon_products.createIndex({"从目录到购物车的转化率 (%)": -1}, {name: 'conversion_rate_from_catalog_index'});
db.ozon_products.createIndex({"从商品卡到购物车的转化率 (%)": -1}, {name: 'conversion_rate_from_product_card_index'});
db.ozon_products.createIndex({"广告费用占比 (%)": -1}, {name: 'advertising_cost_ratio_index'});
db.ozon_products.createIndex({"28日销量": -1}, {name: '28days_sales_volume_index'});
db.ozon_products.createIndex({"Updatetime": -1}, {name: 'update_time_index'});
db.ozon_products.createIndex({"ID": -1}, {name: 'id_index'});

```

MongoDB中，find方法返回的是一个Cursor对象，而不是一个列表或字典，所以不能直接将id_list用在$in操作符中。你需要先将id_list中的所有元素提取出来，然后放入一个列表中。以下是修改后的代码：
```python
@app.get("/get_save_product/")
async def SearchProduct(page: int = 1):
    skip = (page - 1) * 50
    cursor = collection_save_product.find({}, {"_id":0}).limit(50).skip(skip)
    id_list = [int(doc['ID']) for doc in cursor]  # 提取ID列表 要转化为int类型
    id_list = list(set(id_list))  #排除重复项
    ic(id_list)
    result = collection_product.find({"ID": {"$in": id_list}}, {"_id":0})
    return list(result)


```
monggoDB中的查询操作符:
```mongodb
MongoDB Query Operators
There are many query operators that can be used to compare and reference document fields.

Comparison
The following operators can be used in queries to compare values:

$eq: Values are equal
$ne: Values are not equal
$gt: Value is greater than another value
$gte: Value is greater than or equal to another value
$lt: Value is less than another value
$lte: Value is less than or equal to another value
$in: Value is matched within an array
Logical
The following operators can logically compare multiple queries.

$and: Returns documents where both queries match
$or: Returns documents where either query matches
$nor: Returns documents where both queries fail to match
$not: Returns documents where the query does not match
Evaluation
The following operators assist in evaluating documents.

$regex: Allows the use of regular expressions when evaluating field values
$text: Performs a text search
$where: Uses a JavaScript expression to match documents

```
mongodb需要创建索引才能更快查询,无索引全表扫描

```python
 db.ozon_category.find({类目产品数量:{$gt:10}}).explain()
{
  explainVersion: '2',
  queryPlanner: {
    namespace: 'ozon.ozon_category',
    indexFilterSet: false,
    parsedQuery: { '类目产品数量': { '$gt': 10 } },
    queryHash: 'F1E17B2B',
    planCacheKey: '49CE8833',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      queryPlan: {
        stage: 'COLLSCAN',
        planNodeId: 1,
        filter: { '类目产品数量': { '$gt': 10 } },
        direction: 'forward'
      },
      slotBasedPlan: {
        slots: '$$RESULT=s5 env: { s2 = Nothing (SEARCH_META), s1 = TimeZoneDatabase(Asia/Katmandu...America/Matamoros) (timeZoneDB), s3 = 1712755209933 (NOW), s7 = 10 }',
        stages: '[1] filter {traverseF(s4, lambda(l1.0) { ((l1.0 > s7) ?: false) }, false)} \n' +
          '[1] scan s5 s6 none none none none lowPriority [s4 = 类目产品数量] @"3b172592-c668-412d-bd77-676f6a39b971" true false '
      }
    },
    rejectedPlans: []
  },
  command: {
    find: 'ozon_category',
    filter: { '类目产品数量': { '$gt': 10 } },
    '$db': 'ozon'
  },
  serverInfo: {
    host: 'dav',
    port: 27017,
    version: '7.0.4',
    gitVersion: '38f3e37057a43d2e9f41a39142681a76062d582e'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600,
    internalQueryFrameworkControl: 'trySbeEngine'
  },
  ok: 1
}

```

```monggodb
 ozon>db.ozon_category.find({类目产品数量:{$gt:10}}).explain()  //.explain() 打印执行序列

  stage: 'COLLSCAN', 表示全表扫描


 ozon> db.ozon_category.createIndex({"类目产品数量":1,"28日销量":-1})   //1表示升序,-1表示降序
类目产品数量_1_28日销量_-1

.createIndex() 创建索引
  .getIndexes() 显示所有索引


  ozon> db.ozon_category.getIndexes()
[
  { v: 2, key: { _id: 1 }, name: '_id_' },
  {
    v: 2,
    key: { '类目产品数量': 1, '28日销量': -1 },
    name: '类目产品数量_1_28日销量_-1'
  }
]

//       stage: 'IXSCAN', 表示索引扫描


```
```mongodb
ozon> db.ozon_category.find({"类目产品数量":{"$gt":10},"28日销量":{"$gt":100}}).explain()
{
  explainVersion: '2',
  queryPlanner: {
    namespace: 'ozon.ozon_category',
    indexFilterSet: false,
    parsedQuery: {
      '$and': [ { '28日销量': { '$gt': 100 } }, { '类目产品数量': { '$gt': 10 } } ]
    },
    queryHash: 'C3605837',
    planCacheKey: 'C25E2E1E',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      queryPlan: {
        stage: 'FETCH',     //
        planNodeId: 2,
        inputStage: {
          stage: 'IXSCAN',    //是全表扫描还是索引扫描
          planNodeId: 1,
          keyPattern: { '类目产品数量': 1, '28日销量': -1 },
          indexName: '类目产品数量_1_28日销量_-1',
          isMultiKey: false,   //单键还是多键索引
          multiKeyPaths: { '类目产品数量': [], '28日销量': [] },
          isUnique: false,
          isSparse: false,
          isPartial: false,
          indexVersion: 2,
          direction: 'forward',
          indexBounds: { '类目产品数量': [ '(10, inf.0]' ], '28日销量': [ '[inf.0, 100)' ] }
        }
      },

```

![1733214216979.png](https://gitss.oss-cn-shenzhen.aliyuncs.com/md/1733214216979.png)
![1733214232523.png](https://gitss.oss-cn-shenzhen.aliyuncs.com/md/1733214232523.png)


```mongodb
在MongoDB中，除了普通的单字段索引外，还有几种特殊类型的索引，包括复合索引、全文索引、过期索引和地理位置索引。下面我将详细介绍每种索引的特点和用法。

复合索引（Compound Index）：

复合索引是基于多个字段创建的索引。
它允许在多个字段上建立索引，提高查询性能。
复合索引的字段顺序很重要，它决定了索引的排序方式。
创建复合索引的命令示例：
db.collection.createIndex({ field1: 1, field2: -1 })
上述命令在 "field1" 和 "field2" 字段上创建了一个复合索引，其中 "field1" 按升序排序，"field2" 按降序排序。
全文索引（Text Index）：

全文索引用于对字符串内容进行全文搜索。
它允许在文本字段上执行模糊匹配和相关度排序的查询。
创建全文索引的命令示例：
db.collection.createIndex({ field: "text" })
上述命令在 "field" 字段上创建了一个全文索引。
使用全文索引进行搜索的示例：
db.collection.find({ $text: { $search: "search terms" } })
过期索引（TTL Index）：

过期索引用于自动删除过期的文档。
它基于文档中的时间戳字段，并在指定的时间间隔后自动删除过期的文档。
创建过期索引的命令示例：
db.collection.createIndex({ "timestamp": 1 }, { expireAfterSeconds: 3600 })
上述命令在 "timestamp" 字段上创建了一个过期索引，并设置过期时间为3600秒（1小时）。
当文档的 "timestamp" 字段的值超过当前时间减去过期时间时，MongoDB会自动删除该文档。
地理位置索引（Geospatial Index）：

地理位置索引用于存储和查询地理空间数据。
MongoDB支持两种类型的地理位置索引：2d索引和2dsphere索引。
2d索引用于平面地理坐标（经度和纬度），适用于平面地图上的点。
2dsphere索引用于球面地理坐标（经度和纬度），适用于地球表面上的点。
创建2d索引的命令示例：
db.collection.createIndex({ location: "2d" })
创建2dsphere索引的命令示例：
db.collection.createIndex({ location: "2dsphere" })
使用地理位置索引进行查询的示例：
db.collection.find({ location: { $near: [longitude, latitude] } })
这些特殊类型的索引提供了针对不同需求的索引支持，如多字段查询、全文搜索、自动数据过期和地理位置查询等。根据实际的应用场景和查询模式，选择合适的索引类型可以显著提高查询性能和功能。


```








