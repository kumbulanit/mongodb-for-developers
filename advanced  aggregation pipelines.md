### **MongoDB Lab: Advanced Aggregation Pipelines**

This lab will guide you through advanced aggregation techniques in MongoDB using the Aggregation Pipeline. You'll learn how to manipulate, analyze, and transform data using various stages in the pipeline, leveraging advanced operators and expressions to address complex data processing needs.

### **Lab Objectives**

By the end of this lab, you will be able to:
- Understand and use advanced stages of the aggregation pipeline.
- Manipulate and transform documents using `$match`, `$group`, `$lookup`, `$unwind`, `$facet`, `$bucket`, and more.
- Implement and optimize aggregations with indexing strategies.
- Use complex expressions and operators within aggregation stages.
- Apply aggregation best practices in real-world scenarios.

### **Prerequisites**
- A running MongoDB instance (local or cloud).
- MongoDB shell or a graphical MongoDB client (e.g., Compass, Robo 3T).
- Basic understanding of MongoDB and JavaScript syntax.

### **Exercise Overview**

1. **Exercise 1: Basic Aggregations**
   - Part 1: Filtering and Grouping Data
   - Part 2: Projecting and Sorting Data
2. **Exercise 2: Advanced Data Transformations**
   - Part 1: `$lookup` and `$unwind` for Data Enrichment
   - Part 2: Faceted Search with `$facet`
3. **Exercise 3: Complex Aggregations**
   - Part 1: Bucketing Data with `$bucket` and `$bucketAuto`
   - Part 2: Using Conditional Logic with `$cond`, `$ifNull`, and `$switch`
4. **Exercise 4: Performance Optimization**
   - Part 1: Indexing Strategies for Aggregations
   - Part 2: Pipeline Optimization Techniques

---

## **Exercise 1: Basic Aggregations**

### **Part 1: Filtering and Grouping Data**

1. **Create the database and collection**:

   ```javascript
   use ecommerce;

   db.orders.insertMany([
     { _id: 1, customer: "Alice", total: 150, status: "completed", items: ["laptop", "mouse"], orderDate: ISODate("2024-09-20") },
     { _id: 2, customer: "Bob", total: 200, status: "completed", items: ["keyboard", "monitor"], orderDate: ISODate("2024-09-21") },
     { _id: 3, customer: "Charlie", total: 50, status: "pending", items: ["mouse"], orderDate: ISODate("2024-09-22") },
     { _id: 4, customer: "Alice", total: 250, status: "completed", items: ["desk", "chair"], orderDate: ISODate("2024-09-23") }
   ]);
   ```

2. **Use `$match` and `$group` to filter completed orders and group by customer**:

   ```javascript
   db.orders.aggregate([
     { $match: { status: "completed" } },
     { $group: { _id: "$customer", totalSpent: { $sum: "$total" }, ordersCount: { $sum: 1 } } }
   ]);
   ```

   - **Explanation**: This pipeline filters completed orders and groups the data by customer, summing the total amount spent and counting the number of orders.

### **Part 2: Projecting and Sorting Data**

1. **Use `$project` to format the output and `$sort` to order results by total spent**:

   ```javascript
   db.orders.aggregate([
     { $match: { status: "completed" } },
     { $group: { _id: "$customer", totalSpent: { $sum: "$total" }, ordersCount: { $sum: 1 } } },
     { $project: { _id: 0, customer: "$_id", totalSpent: 1, ordersCount: 1 } },
     { $sort: { totalSpent: -1 } }
   ]);
   ```

   - **Explanation**: This extends the previous pipeline by formatting the output and sorting customers by the amount spent in descending order.

---

## **Exercise 2: Advanced Data Transformations**

### **Part 1: `$lookup` and `$unwind` for Data Enrichment**

1. **Create a new collection for customers**:

   ```javascript
   db.customers.insertMany([
     { _id: 1, name: "Alice", city: "New York" },
     { _id: 2, name: "Bob", city: "Los Angeles" },
     { _id: 3, name: "Charlie", city: "Chicago" }
   ]);
   ```

2. **Use `$lookup` to join `orders` with `customers`**:

   ```javascript
   db.orders.aggregate([
     { $lookup: { from: "customers", localField: "customer", foreignField: "name", as: "customerInfo" } },
     { $unwind: "$customerInfo" },
     { $project: { _id: 1, customer: 1, total: 1, city: "$customerInfo.city" } }
   ]);
   ```

   - **Explanation**: `$lookup` is used to enrich order data with customer details, and `$unwind` is used to flatten the resulting array.

### **Part 2: Faceted Search with `$facet`**

1. **Use `$facet` to perform multiple aggregations within a single pipeline**:

   ```javascript
   db.orders.aggregate([
     {
       $facet: {
         totalByStatus: [
           { $group: { _id: "$status", totalAmount: { $sum: "$total" } } }
         ],
         averageOrderValue: [
           { $group: { _id: null, avgTotal: { $avg: "$total" } } }
         ]
       }
     }
   ]);
   ```

   - **Explanation**: `$facet` allows you to run multiple pipelines in parallel, which can be useful for generating summary statistics in a single query.

---

## **Exercise 3: Complex Aggregations**

### **Part 1: Bucketing Data with `$bucket` and `$bucketAuto`**

1. **Use `$bucket` to categorize orders based on total amount spent**:

   ```javascript
   db.orders.aggregate([
     {
       $bucket: {
         groupBy: "$total",
         boundaries: [0, 100, 200, 300],
         default: "Other",
         output: {
           count: { $sum: 1 },
           orders: { $push: "$$ROOT" }
         }
       }
     }
   ]);
   ```

   - **Explanation**: `$bucket` groups data into defined ranges (bins), making it useful for creating histograms or categorizing numeric data.

2. **Use `$bucketAuto` for automatic bucketing**:

   ```javascript
   db.orders.aggregate([
     {
       $bucketAuto: {
         groupBy: "$total",
         buckets: 3,
         output: {
           count: { $sum: 1 },
           averageTotal: { $avg: "$total" }
         }
       }
     }
   ]);
   ```

   - **Explanation**: `$bucketAuto` automatically calculates boundaries for the specified number of buckets.

### **Part 2: Using Conditional Logic with `$cond`, `$ifNull`, and `$switch`**

1. **Use `$cond` to categorize orders as 'High' or 'Low' based on total**:

   ```javascript
   db.orders.aggregate([
     {
       $project: {
         customer: 1,
         total: 1,
         category: {
           $cond: { if: { $gte: ["$total", 200] }, then: "High", else: "Low" }
         }
       }
     }
   ]);
   ```

2. **Use `$ifNull` to handle missing fields**:

   ```javascript
   db.orders.aggregate([
     {
       $project: {
         customer: 1,
         total: 1,
         discount: { $ifNull: ["$discount", 0] }
       }
     }
   ]);
   ```

3. **Use `$switch` for more complex conditional logic**:

   ```javascript
   db.orders.aggregate([
     {
       $project: {
         customer: 1,
         total: 1,
         statusDescription: {
           $switch: {
             branches: [
               { case: { $eq: ["$status", "completed"] }, then: "Order Completed" },
               { case: { $eq: ["$status", "pending"] }, then: "Order Pending" }
             ],
             default: "Unknown Status"
           }
         }
       }
     }
   ]);
   ```

---

## **Exercise 4: Performance Optimization**

### **Part 1: Indexing Strategies for Aggregations**

1. **Create indexes to improve the performance of aggregation queries**:

   ```javascript
   db.orders.createIndex({ customer: 1, status: 1 });
   db.orders.createIndex({ total: 1 });
   ```

2. **Analyze aggregation performance with `$explain`**:

   ```javascript
   db.orders.aggregate([
     { $match: { status: "completed" } },
     { $group: { _id: "$customer", totalSpent: { $sum: "$total" } } }
   ]).explain("executionStats");
   ```

   - **Explanation**: `$explain` helps to understand how an aggregation query uses indexes, allowing for further optimization.

### **Part 2: Pipeline Optimization Techniques**

1. **Use `$merge` to save aggregation results into a new collection**:

   ```javascript


   db.orders.aggregate([
     { $match: { status: "completed" } },
     { $group: { _id: "$customer", totalSpent: { $sum: "$total" } } },
     { $merge: { into: "customer_totals", whenMatched: "merge", whenNotMatched: "insert" } }
   ]);
   ```

   - **Explanation**: `$merge` allows you to write the results of an aggregation pipeline into a collection, useful for creating materialized views.

2. **Use `$redact` for data security by filtering sensitive information**:

   ```javascript
   db.orders.aggregate([
     {
       $redact: {
         $cond: {
           if: { $eq: ["$status", "completed"] },
           then: "$$DESCEND",
           else: "$$PRUNE"
         }
       }
     }
   ]);
   ```

   - **Explanation**: `$redact` is used for restricting data visibility based on certain conditions, enhancing data security.

---

This lab covers various advanced aggregation pipeline techniques, focusing on practical use cases and performance optimization. Experimenting with these stages and techniques will help you gain a deeper understanding of MongoDB's powerful data processing capabilities.