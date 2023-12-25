# [Mongodb Aggregation Pipelines](https://www.youtube.com/watch?v=fDTf1mk-jQg&list=PLu71SKxNbfoBGh_8p_NS-ZAh6v7HhYqHW&index=20&ab_channel=ChaiaurCode)

An [aggregation pipeline](https://www.w3schools.com/mongodb/mongodb_aggregations_intro.php) consists of one or more `stages` that process documents:

- Each `stage` performs an operation on the input documents. For example, a stage can filter documents, group documents, and calculate values.
- The documents that are output from a stage are passed to the next stage.
- An aggregation pipeline can return results for groups of documents. For example, return the total, average, maximum, and minimum values.

> Aggregation pipelines run with the `db.collection.aggregate()` method do not modify documents in a collection, unless the pipeline contains a `$merge` or `$out` stage.

## [Complete Aggregation Pipeline Examples](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/)

This section shows `aggregation pipeline` examples that use the following pizza `orders` collection:

```json
db.orders.insertMany( [
   { _id: 0, name: "Pepperoni", size: "small", price: 19,
     quantity: 10, date: ISODate( "2021-03-13T08:14:30Z" ) },
   { _id: 1, name: "Pepperoni", size: "medium", price: 20,
     quantity: 20, date : ISODate( "2021-03-13T09:13:24Z" ) },
   { _id: 2, name: "Pepperoni", size: "large", price: 21,
     quantity: 30, date : ISODate( "2021-03-17T09:22:12Z" ) },
   { _id: 3, name: "Cheese", size: "small", price: 12,
     quantity: 15, date : ISODate( "2021-03-13T11:21:39.736Z" ) },
   { _id: 4, name: "Cheese", size: "medium", price: 13,
     quantity:50, date : ISODate( "2022-01-12T21:23:13.331Z" ) },
   { _id: 5, name: "Cheese", size: "large", price: 14,
     quantity: 10, date : ISODate( "2022-01-12T05:08:13Z" ) },
   { _id: 6, name: "Vegan", size: "small", price: 17,
     quantity: 10, date : ISODate( "2021-01-13T05:08:13Z" ) },
   { _id: 7, name: "Vegan", size: "medium", price: 18,
     quantity: 10, date : ISODate( "2021-01-13T05:10:13Z" ) }
] )
```

### Calculate Total Order Quantity

The following aggregation pipeline example contains two stages and returns the total order quantity of medium size pizzas grouped by pizza name.

```js
db.orders.aggregate([
  { $match: { size: "medium" } },
  { $group: { _id: "$name", totalQuantity: { $sum: "$quantity" } } },
]);
```

```json
[
  { "_id": "Cheese", "totalQuantity": 50 },
  { "_id": "Vegan", "totalQuantity": 10 },
  { "_id": "Pepperoni", "totalQuantity": 20 }
]
```

> The `$match` stage filters the pizza order documents to pizzas with a size of medium and passes the remaining documents to the `$group` stage. The `$group` stage groups the remaining documents by pizza name. Uses `$sum` to calculate the total order quantity for each pizza name. The total is stored in the totalQuantity field returned by the aggregation pipeline.

### Calculate Total Order Value and Average Order Quantity

The following example calculates the total pizza order value and average order quantity between two dates.

```js
db.orders.aggregate([
  {
    $match: {
      date: { $gte: new ISODate("2020-01-30"), $lt: new ISODate("2022-01-30") },
    },
  },

  {
    $group: {
      _id: { $dateToString: { format: "%Y-%m-%d", date: "$date" } },
      totalOrderValue: { $sum: { $multiply: ["$price", "$quantity"] } },
      averageOrderQuantity: { $avg: "$quantity" },
    },
  },

  { $sort: { totalOrderValue: -1 } },
]);
```

```json
[
  { "_id": "2022-01-12", "totalOrderValue": 790, "averageOrderQuantity": 30 },
  { "_id": "2021-03-13", "totalOrderValue": 770, "averageOrderQuantity": 15 },
  { "_id": "2021-03-17", "totalOrderValue": 630, "averageOrderQuantity": 30 },
  { "_id": "2021-01-13", "totalOrderValue": 350, "averageOrderQuantity": 10 }
]
```

> The `$match` stage filters the pizza order documents to those in a date range specified using `$gte` and `$lt` and asses the remaining documents to the `$group` stage. The `$group` stage groups the documents by date using `$dateToString` for each group, calculates total order value using `$sum` and `$multiply` and average order quantity using `$avg` and finally passes the grouped documents to the `$sort` stage. The `$sort` stage sorts the documents by the total order value for each group in descending order (-1).

> [Aggregation Pipeline](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/)
