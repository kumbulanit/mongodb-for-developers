Here's a structured outline for three distinct labs that focus on **Distinct**, **Aggregation Pipelines**, and **Map-Reduce** in MongoDB. Each lab is divided into parts that incorporate document structures, manipulation, and best practices.

### Lab 1: Distinct

**Objective**: Understand the usage of the `distinct` operation, and learn about its integration with document structures and indexing.

#### Part 1: Basic Usage of Distinct

1. **Setup**:
   ```javascript
   use indexingLab
   db.createCollection("products")
   ```

2. **Insert Sample Documents**:
   ```javascript
   db.products.insertMany([
       { name: "Laptop", category: "Electronics", price: 1000 },
       { name: "Smartphone", category: "Electronics", price: 500 },
       { name: "Chair", category: "Furniture", price: 150 },
       { name: "Desk", category: "Furniture", price: 300 },
       { name: "Tablet", category: "Electronics", price: 400 }
   ])
   ```

3. **Use Distinct**:
   - Find all distinct categories.
   ```javascript
   db.products.distinct("category")
   ```

4. **Discussion**:
   - Explain the importance of the `distinct` operation for getting unique values in a collection.
   - Discuss scenarios where `distinct` is beneficial for analytics.

#### Part 2: Integrating Indexing

1. **Create Index**:
   ```javascript
   db.products.createIndex({ category: 1 })
   ```

2. **Test Distinct with Index**:
   - Run the `distinct` command again and explain how the index improves performance.
   ```javascript
   db.products.distinct("category").explain("executionStats")
   ```

3. **Discussion**:
   - Discuss how indexing affects the performance of `distinct` queries and when to use it.

#### Part 3: Best Practices for Distinct

1. **Use Cases**:
   - Describe scenarios such as reporting and analytics where `distinct` can be effectively used.
  
2. **Performance Considerations**:
   - Highlight the impact of large datasets on the performance of `distinct` and recommend using indexed fields.
   - Discuss alternatives to `distinct` in specific scenarios (e.g., aggregation framework).

---

### Lab 2: Aggregation Pipelines

**Objective**: Learn about aggregation pipelines and how to manipulate documents using various stages.

#### Part 1: Basic Aggregation Pipeline

1. **Setup**:
   - Use the same `products` collection from Lab 1.

2. **Basic Aggregation**:
   - Calculate the average price of products.
   ```javascript
   db.products.aggregate([
       { $group: { _id: null, averagePrice: { $avg: "$price" } } }
   ])
   ```

3. **Discussion**:
   - Explain how aggregation pipelines work and the use of `$group` for summarizing data.

#### Part 2: Manipulating Documents

1. **Add Fields**:
   - Add a `discountedPrice` field for each product.
   ```javascript
   db.products.updateMany({}, { $set: { discountedPrice: { $multiply: ["$price", 0.9] } } })
   ```

2. **Aggregation with New Field**:
   - Calculate the total discounted price.
   ```javascript
   db.products.aggregate([
       { $group: { _id: null, totalDiscountedPrice: { $sum: "$discountedPrice" } } }
   ])
   ```

3. **Discussion**:
   - Discuss the flexibility of aggregation pipelines to manipulate and transform data.

#### Part 3: Best Practices for Aggregation Pipelines

1. **Indexing Considerations**:
   - Discuss how proper indexing can enhance the performance of aggregation pipelines, especially for `$match` and `$sort` stages.

2. **Efficiency**:
   - Explain the importance of filtering data as early as possible in the pipeline to reduce processing time.

3. **Use Cases**:
   - Provide examples of real-world scenarios where aggregation pipelines are commonly used (e.g., reporting, data transformation).

---

### Lab 3: Map-Reduce

**Objective**: Understand the Map-Reduce framework in MongoDB for processing large datasets.

#### Part 1: Basic Map-Reduce Usage

1. **Setup**:
   - Continue using the `products` collection.

2. **Map Function**:
   - Define a map function to emit category and price.
   ```javascript
   var mapFunction = function() {
       emit(this.category, this.price);
   };
   ```

3. **Reduce Function**:
   - Define a reduce function to calculate total sales for each category.
   ```javascript
   var reduceFunction = function(key, values) {
       return Array.sum(values);
   };
   ```

4. **Execute Map-Reduce**:
   - Run the Map-Reduce operation to get total sales per category.
   ```javascript
   db.products.mapReduce(
       mapFunction,
       reduceFunction,
       { out: "totalSales" }
   )
   ```

5. **Discussion**:
   - Explain how Map-Reduce works and its components (map, reduce, and output).

#### Part 2: Integrating Document Structures

1. **Modify Document Structure**:
   - Add a new field to the `products` collection (e.g., `sales`).
   ```javascript
   db.products.updateMany({}, { $set: { sales: 0 } })
   ```

2. **Run Map-Reduce Again**:
   - Update the map function to include the `sales` field.
   ```javascript
   var mapFunction = function() {
       emit(this.category, this.sales);
   };
   ```

3. **Execute Map-Reduce**:
   - Run the operation again to see how it adapts to the new document structure.

4. **Discussion**:
   - Discuss the importance of understanding document structure when designing Map-Reduce jobs.

#### Part 3: Best Practices for Map-Reduce

1. **Performance Considerations**:
   - Highlight scenarios where Map-Reduce is beneficial versus when to use aggregation pipelines.
  
2. **Use Cases**:
   - Provide examples where Map-Reduce is particularly useful (e.g., large datasets, complex computations).

3. **Indexing**:
   - Discuss how creating indexes on the fields being emitted can improve the performance of Map-Reduce operations.

---

### Conclusion
- **Review**: Summarize the key learnings from each lab, emphasizing how to leverage Distinct, Aggregation Pipelines, and Map-Reduce effectively in MongoDB.
- **Q&A**: Open the floor for any questions or clarifications regarding the labs.

These structured labs will provide a comprehensive understanding of MongoDB's functionalities, incorporating document structures, manipulation, and best practices, which are vital for effective database management.