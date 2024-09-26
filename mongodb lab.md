Here's a comprehensive lab that covers the advanced topics you've mentioned related to MongoDB. The lab is structured into several exercises, each focusing on a set of MongoDB concepts and operations, along with practical tasks that demonstrate advanced techniques. The aim is to provide hands-on experience with document manipulation, querying, indexing, and other core MongoDB functionalities.

## **MongoDB Advanced Lab: Comprehensive Hands-on Guide**

### **Lab Objectives**

By the end of this lab, you will learn how to:
- Manipulate documents in MongoDB.
- Perform basic and advanced CRUD (Create, Read, Update, Delete) operations.
- Use MongoDB’s indexing strategies to optimize queries.
- Work with different document structures, including embedded documents and tree structures.
- Understand and implement advanced features like tailable cursors, two-phase commits, and auto-incrementing fields.
- Use aggregation pipelines, distinct queries, and MapReduce for data processing.
- Leverage advanced index types like geospatial, hashed, unique, and sparse indexes.

### **Prerequisites**
- A working MongoDB instance (installed locally or accessed through a cloud provider like MongoDB Atlas).
- Basic understanding of MongoDB shell commands or access to a MongoDB client (MongoDB Compass, Robo 3T, etc.).
- Basic knowledge of JavaScript syntax since MongoDB uses JavaScript-based queries.

---

## **Exercise 1: Document Structure and Datatypes**

### **Part 1: Creating and Inserting Documents**

1. **Create a database** called `labDB` and a collection called `books`.

2. **Insert sample documents** into the `books` collection that demonstrate various datatypes and embedded sub-documents.

   ```javascript
   use labDB;

   db.books.insertMany([
     {
       _id: ObjectId(),
       title: "The Great Gatsby",
       author: {
         name: "F. Scott Fitzgerald",
         birthYear: 1896,
         nationality: "American"
       },
       publishedYear: 1925,
       genres: ["Fiction", "Classic"],
       available: true,
       ratings: {
         average: 4.2,
         total: 1500
       },
       createdAt: ISODate("2024-09-24T10:30:00Z"),
       coverImage: BinData(0, "image_data_here")
     },
     {
       _id: ObjectId(),
       title: "To Kill a Mockingbird",
       author: {
         name: "Harper Lee",
         birthYear: 1926,
         nationality: "American"
       },
       publishedYear: 1960,
       genres: ["Fiction", "Historical"],
       available: false,
       ratings: {
         average: 4.8,
         total: 1800
       },
       createdAt: ISODate("2024-09-24T11:00:00Z"),
       coverImage: BinData(0, "image_data_here")
     }
   ]);
   ```

### **Part 2: Working with References and IDs**

1. **Create a collection** called `authors` and insert documents that represent authors.

   ```javascript
   db.authors.insertMany([
     {
       _id: ObjectId(),
       name: "F. Scott Fitzgerald",
       birthYear: 1896,
       nationality: "American",
       books: ["The Great Gatsby"]
     },
     {
       _id: ObjectId(),
       name: "Harper Lee",
       birthYear: 1926,
       nationality: "American",
       books: ["To Kill a Mockingbird"]
     }
   ]);
   ```

2. **Reference the author** in the `books` collection by using the author’s `_id`.

   ```javascript
   db.books.updateOne(
     { title: "The Great Gatsby" },
     { $set: { authorId: db.authors.findOne({ name: "F. Scott Fitzgerald" })._id } }
   );
   ```

### **Part 3: Using Keys and Embedded Sub-Documents**

1. **Insert a book with embedded sub-documents**.

   ```javascript
   db.books.insertOne({
     _id: ObjectId(),
     title: "1984",
     author: {
       name: "George Orwell",
       birthYear: 1903,
       nationality: "British"
     },
     publishedYear: 1949,
     genres: ["Dystopian", "Political Fiction"],
     available: true,
     reviews: [
       {
         reviewer: "John Doe",
         comment: "A timeless classic.",
         rating: 5
       },
       {
         reviewer: "Jane Smith",
         comment: "Profound and thought-provoking.",
         rating: 4
       }
     ],
     createdAt: ISODate("2024-09-25T08:00:00Z")
   });
   ```

---

## **Exercise 2: Querying, Updating, and Manipulating Documents**

### **Part 1: Basic Query and Update Operations**

1. **Query documents** to find all available books.

   ```javascript
   db.books.find({ available: true });
   ```

2. **Update a single document**: Mark a book as available.

   ```javascript
   db.books.updateOne({ title: "To Kill a Mockingbird" }, { $set: { available: true } });
   ```

3. **Update multiple documents**: Increase the total ratings count by 100 for all books published before 1950.

   ```javascript
   db.books.updateMany({ publishedYear: { $lt: 1950 } }, { $inc: { "ratings.total": 100 } });
   ```

4. **Upsert a document**: If a book with the title "Brave New World" doesn't exist, insert it.

   ```javascript
   db.books.updateOne(
     { title: "Brave New World" },
     {
       $set: {
         author: { name: "Aldous Huxley", birthYear: 1894, nationality: "British" },
         publishedYear: 1932,
         available: true
       }
     },
     { upsert: true }
   );
   ```

### **Part 2: Removing Data**

1. **Remove a specific document**.

   ```javascript
   db.books.deleteOne({ title: "1984" });
   ```

2. **Remove a field from documents**.

   ```javascript
   db.books.updateMany({}, { $unset: { coverImage: "" } });
   ```

3. **Drop the entire collection**.

   ```javascript
   db.books.drop();
   ```

---

## **Exercise 3: Advanced Data Structures and Operations**

### **Part 1: Working with Tree Structures and Tailable Cursors**

1. **Create a tree structure** by inserting categories with subcategories as embedded documents.

   ```javascript
   db.categories.insertMany([
     {
       _id: ObjectId(),
       name: "Fiction",
       subcategories: [
         { name: "Classic" },
         { name: "Historical" }
       ]
     },
     {
       _id: ObjectId(),
       name: "Non-Fiction",
       subcategories: [
         { name: "Biography" },
         { name: "Self-Help" }
       ]
     }
   ]);
   ```

2. **Create a capped collection** and use a tailable cursor to continuously read new entries.

   ```javascript
   db.createCollection("logs", { capped: true, size: 10000 });

   db.logs.insertOne({ event: "User Login", timestamp: new Date() });

   const cursor = db.logs.find({}, { tailable: true, awaitData: true });
   cursor.forEach(doc => printjson(doc));
   ```

### **Part 2: Two-Phase Commits and Auto-Incrementing Sequence Field**

1. **Implement a two-phase commit** using transactions to ensure atomicity in updates.

2. **Create a counter collection** to manage auto-incrementing sequence fields.

   ```javascript
   db.counters.insertOne({ _id: "bookId", sequence_value: 0 });

   function getNextSequence(name) {
     const sequenceDocument = db.counters.findOneAndUpdate(
       { _id: name },
       { $inc: { sequence_value: 1 } },
       { returnNewDocument: true }
     );
     return sequenceDocument.sequence_value;
   }

   db.books.insertOne({
     _id: getNextSequence("bookId"),
     title: "New Book Title",
     author: { name: "New Author" }
   });
   ```

---

## **Exercise 4: Aggregation Techniques**

### **Part 1: Using Aggregation Pipelines**

1. **Perform basic aggregation** to calculate the average ratings.

   ```javascript
   db.books.aggregate([
     { $match: { available: true } },
     { $group: { _id: "$author.name", averageRating: { $avg: "$ratings.average" } } }
   ]);
   ```

2. **Enhance aggregation with indexing**: Create a compound index to optimize performance.

   ```javascript
   db.books.createIndex({ available: 1, "ratings.average": 1 });
   ```

### **Part 2: Using Distinct and Map-Reduce**

1. **Use distinct to find unique genres**.

   ```javascript
   db.books.distinct("genres");
   ```

2. **Perform a Map-Reduce operation** to count books by genre.

   ```javascript
   db.books.mapReduce(
     function() {
       this.genres.forEach(genre => emit(genre, 1));
     },
     function(key, values) {
       return Array.sum(values);
     },
     {
       out: "genre_counts"
     }
   );



   db.genre_counts.find();
   ```

---

## **Exercise 5: Indexing Strategies**

### **Part 1: Create and Use Indexes**

1. **Create different index types**: Single field, compound, multi-key, geospatial, hashed, unique, and sparse.

   ```javascript
   // Single Field Index
   db.books.createIndex({ title: 1 });

   // Compound Index
   db.books.createIndex({ title: 1, "author.name": 1 });

   // Multikey Index
   db.books.createIndex({ genres: 1 });

   // Geospatial Index
   db.locations.insertOne({ name: "Library", coordinates: [40.7128, -74.0060] });
   db.locations.createIndex({ coordinates: "2dsphere" });

   // Hashed Index
   db.books.createIndex({ title: "hashed" });

   // Unique Index
   db.authors.createIndex({ name: 1 }, { unique: true });

   // Sparse Index
   db.books.createIndex({ coverImage: 1 }, { sparse: true });
   ```

### **Part 2: Conditional Index Usage**

1. **Use conditional indexes** to filter and optimize specific queries.

   ```javascript
   db.books.createIndex({ available: 1, publishedYear: 1 }, { partialFilterExpression: { available: true } });
   ```

---

This lab provides a thorough exploration of MongoDB's features and prepares you for real-world scenarios where advanced data manipulation, querying, and indexing techniques are essential. Each exercise encourages experimentation with MongoDB commands, allowing you to gain a deep understanding of the database's capabilities.