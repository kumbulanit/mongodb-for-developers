Creating a lab to demonstrate different indexing methods in MongoDB is a great way to understand their functionality and best-use scenarios. Below is a structured outline for three separate labs focusing on the different types of indexes: 

### Lab 1: Basic Indexes (Default `_id`, Single Field, and Unique Index)

**Objective**: Understand and implement the Default `_id` index, Single Field index, and Unique index.

#### Prerequisites
- MongoDB installed and running.
- MongoDB shell (or any MongoDB client).

#### Steps

1. **Create a Database and Collection**:
   ```bash
   use indexingLab
   db.createCollection("books")
   ```

2. **Insert Sample Documents**:
   ```javascript
   db.books.insertMany([
       { title: "The Great Gatsby", author: "F. Scott Fitzgerald", publishedYear: 1925 },
       { title: "1984", author: "George Orwell", publishedYear: 1949 },
       { title: "To Kill a Mockingbird", author: "Harper Lee", publishedYear: 1960 },
       { title: "The Catcher in the Rye", author: "J.D. Salinger", publishedYear: 1951 }
   ])
   ```

3. **Default `_id` Index**:
   - Explain that MongoDB automatically creates a unique index on the `_id` field when a document is created.

4. **Single Field Index**:
   - Create an index on the `author` field.
   ```javascript
   db.books.createIndex({ author: 1 })
   ```
   - **Test the index**:
   ```javascript
   db.books.find({ author: "George Orwell" }).explain("executionStats")
   ```

5. **Unique Index**:
   - Add a unique index on the `title` field.
   ```javascript
   db.books.createIndex({ title: 1 }, { unique: true })
   ```
   - **Test the unique index**:
   ```javascript
   db.books.insert({ title: "The Great Gatsby", author: "New Author" }) // This should throw a duplicate key error.
   ```

6. **Discussion**:
   - Discuss scenarios where Single Field and Unique indexes are beneficial.

### Lab 2: Advanced Indexes (Compound, Multikey, and Sparse Index)

**Objective**: Explore Compound indexes, Multikey indexes, and Sparse indexes.

#### Prerequisites
- Continue using the `indexingLab` database created in Lab 1.

#### Steps

1. **Compound Index**:
   - Create a compound index on `author` and `publishedYear`.
   ```javascript
   db.books.createIndex({ author: 1, publishedYear: 1 })
   ```
   - **Test the index**:
   ```javascript
   db.books.find({ author: "Harper Lee", publishedYear: 1960 }).explain("executionStats")
   ```

2. **Multikey Index**:
   - Modify the documents to include genres.
   ```javascript
   db.books.updateMany(
       {},
       { $set: { genres: ["Fiction", "Classic"] } }
   )
   ```
   - Create a multikey index on the `genres` array.
   ```javascript
   db.books.createIndex({ genres: 1 })
   ```
   - **Test the index**:
   ```javascript
   db.books.find({ genres: "Fiction" }).explain("executionStats")
   ```

3. **Sparse Index**:
   - Add a new field `rating` to some documents.
   ```javascript
   db.books.updateMany(
       { title: "1984" },
       { $set: { rating: 4.5 } }
   )
   ```
   - Create a sparse index on the `rating` field.
   ```javascript
   db.books.createIndex({ rating: 1 }, { sparse: true })
   ```
   - **Test the index**:
   ```javascript
   db.books.find({ rating: { $exists: true } }).explain("executionStats")
   ```

4. **Discussion**:
   - Discuss the scenarios in which Compound, Multikey, and Sparse indexes are advantageous.

### Lab 3: Specialized Indexes (Geospatial, Hashed Index)

**Objective**: Implement and understand Geospatial and Hashed indexes.

#### Prerequisites
- Continue using the `indexingLab` database created in previous labs.

#### Steps

1. **Geospatial Index**:
   - Add location data to the documents.
   ```javascript
   db.books.updateMany(
       {},
       { $set: { location: { type: "Point", coordinates: [-73.935242, 40.730610] } } }
   )
   ```
   - Create a 2dsphere index for geospatial queries.
   ```javascript
   db.books.createIndex({ location: "2dsphere" })
   ```
   - **Test the index**:
   ```javascript
   db.books.find({
       location: {
           $near: {
               $geometry: {
                   type: "Point",
                   coordinates: [-73.935242, 40.730610]
               },
               $maxDistance: 5000
           }
       }
   }).explain("executionStats")
   ```

2. **Hashed Index**:
   - Create a hashed index on the `_id` field to distribute documents evenly.
   ```javascript
   db.books.createIndex({ _id: "hashed" })
   ```
   - **Test the index**:
   ```javascript
   db.books.find({ _id: ObjectId("60d5ec49f1a2b45e5c4e8f7e") }).explain("executionStats")
   ```

3. **Discussion**:
   - Discuss the use cases for Geospatial and Hashed indexes.

### Conclusion
- **Review and Discuss**: Summarize the labs, discussing the best scenarios for each index type and emphasizing the importance of choosing the right index based on query patterns and data structure. 

This structured approach allows learners to grasp the different indexing methods in MongoDB practically, facilitating better understanding through hands-on experience.