

### **Lab: Exploring the Default `_id` Index in MongoDB**

This lab will help you understand and work with the default `_id` index, demonstrating how it enhances query performance and the uniqueness constraint.

#### **Objective**

- Understand the behavior of the `_id` index in MongoDB.
- Explore query performance when using the `_id` index.
- Learn how to verify and utilize the default `_id` index.

#### **Step-by-Step Instructions**

1. **Set Up MongoDB**

   Ensure MongoDB is installed and running on your local environment or cloud service (e.g., MongoDB Atlas). Access the MongoDB shell or a client like MongoDB Compass.

2. **Create a Collection and Insert Documents**

   Create a collection named `inventory` and insert several sample documents. MongoDB will automatically index the `_id` field.

   ```javascript
   // Create and insert multiple documents into 'inventory'
   db.inventory.insertMany([
     { item: "book", qty: 10, price: 15 },
     { item: "pen", qty: 50, price: 1.5 },
     { item: "notebook", qty: 20, price: 5 },
   ]);
   ```

3. **Check the Existing Indexes**

   Use the `getIndexes()` method to see the indexes on the `inventory` collection.

   ```javascript
   // List all indexes in the 'inventory' collection
   db.inventory.getIndexes();
   ```

   **Expected Output:**

   ```json
   [
     {
       "v": 2,
       "key": { "_id": 1 },
       "name": "_id_",
       "ns": "test.inventory"
     }
   ]
   ```

   This output shows that there is a default `_id` index on the collection.

4. **Query Using the `_id` Index**

   Perform a query using the `_id` field to see how the index speeds up the search.

   ```javascript
   // Insert another document and get its _id
   const newDoc = db.inventory.insertOne({ item: "eraser", qty: 30, price: 2 });
   const insertedId = newDoc.insertedId;

   // Query using the automatically created _id index
   const result = db.inventory.findOne({ _id: insertedId });
   printjson(result);
   ```

5. **Measure Query Performance with Explain**

   Use the `explain()` method to analyze the query plan and see how MongoDB uses the `_id` index.

   ```javascript
   // Explain the query plan to verify index usage
   db.inventory.find({ _id: insertedId }).explain("executionStats");
   ```

   **Expected Analysis:**

   The `explain()` output should indicate that the query used an `IXSCAN` (Index Scan) on the `_id` index, confirming that the index is optimizing the query.

6. **Attempt to Insert a Duplicate `_id`**

   Try inserting a document with a duplicate `_id` to demonstrate the uniqueness constraint.

   ```javascript
   // Attempt to insert a document with a duplicate _id
   db.inventory.insertOne({
     _id: insertedId, // This _id already exists
     item: "duplicate item",
     qty: 5,
     price: 10
   });
   ```

   **Expected Result:**

   MongoDB will throw a `DuplicateKey` error, showing that the `_id` index enforces uniqueness.

7. **Update a Document Using `_id`**

   Use the `_id` field to update a specific document.

   ```javascript
   // Update the document identified by _id
   db.inventory.updateOne(
     { _id: insertedId },
     { $set: { qty: 25, price: 1.75 } }
   );

   // Confirm the update
   printjson(db.inventory.findOne({ _id: insertedId }));
   ```

   This shows how `_id` can be used efficiently in updates.

#### **Conclusion**

This lab demonstrates the automatic creation and utility of the `_id` index in MongoDB. By understanding how `_id` indexing works, you can leverage it to enhance your database’s performance for common operations such as querying, updating, and enforcing uniqueness constraints.