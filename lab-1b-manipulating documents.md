### **Lab: MongoDB CRUD Operations with Conditional and Advanced Options**

In this lab, you'll perform various CRUD (Create, Read, Update, Delete) operations in MongoDB while exploring advanced features like conditional queries and upserts.

---

### **Lab Setup**

1. **Ensure MongoDB is installed and running** on your machine or within a Docker/Kubernetes environment.
2. **Access MongoDB** using MongoDB Shell (`mongo`) or MongoDB Compass.

---

### **Lab 1: Query Operations**

1. **Insert Documents into Collection:**
   - Start by creating a new collection called `students` and insert some documents.
   
   ```javascript
   db.students.insertMany([
      { name: "Alice", age: 22, major: "Computer Science", gpa: 3.8 },
      { name: "Bob", age: 24, major: "Mathematics", gpa: 3.5 },
      { name: "Charlie", age: 23, major: "Physics", gpa: 3.9 }
   ]);
   ```

2. **Basic Query (Read Operation):**
   - Query all documents in the `students` collection.

   ```javascript
   db.students.find().pretty();
   ```

3. **Conditional Query (with Advanced Filters):**
   - Find students whose `gpa` is greater than 3.7.

   ```javascript
   db.students.find({ gpa: { $gt: 3.7 } }).pretty();
   ```

4. **Query with Multiple Conditions (Logical Operators):**
   - Find students who are older than 22 and have a `gpa` of at least 3.5.

   ```javascript
   db.students.find({ $and: [ { age: { $gt: 22 } }, { gpa: { $gte: 3.5 } } ] }).pretty();
   ```

5. **Query Specific Fields (Projection):**
   - Find students but only return the `name` and `gpa` fields.

   ```javascript
   db.students.find({}, { name: 1, gpa: 1, _id: 0 }).pretty();
   ```

---

### **Lab 2: Insert Operations**

1. **Basic Insert:**
   - Insert a new student document.

   ```javascript
   db.students.insertOne({ name: "David", age: 21, major: "Biology", gpa: 3.6 });
   ```

2. **Insert Multiple Documents:**
   - Insert multiple student documents at once.

   ```javascript
   db.students.insertMany([
      { name: "Emma", age: 25, major: "Engineering", gpa: 3.7 },
      { name: "Frank", age: 20, major: "Philosophy", gpa: 3.4 }
   ]);
   ```

---

### **Lab 3: Update Operations**

1. **Basic Update (updateOne):**
   - Update Alice's `gpa` to 3.9.

   ```javascript
   db.students.updateOne({ name: "Alice" }, { $set: { gpa: 3.9 } });
   ```

2. **Update Multiple Documents (updateMany):**
   - Increase the `gpa` of all students by 0.1 where their current `gpa` is less than 3.6.

   ```javascript
   db.students.updateMany({ gpa: { $lt: 3.6 } }, { $inc: { gpa: 0.1 } });
   ```

3. **Conditional Update:**
   - Update Bob’s major to "Statistics" only if his `gpa` is 3.5.

   ```javascript
   db.students.updateOne({ name: "Bob", gpa: 3.5 }, { $set: { major: "Statistics" } });
   ```

4. **Advanced Update (array fields):**
   - Add a new field called `courses` with an array of values to Alice’s document.

   ```javascript
   db.students.updateOne({ name: "Alice" }, { $set: { courses: ["Math", "Science", "English"] } });
   ```

---

### **Lab 4: Remove Operations**

1. **Basic Remove (deleteOne):**
   - Delete the document where the `name` is "Frank."

   ```javascript
   db.students.deleteOne({ name: "Frank" });
   ```

2. **Remove Multiple Documents (deleteMany):**
   - Delete all students with a `gpa` less than 3.5.

   ```javascript
   db.students.deleteMany({ gpa: { $lt: 3.5 } });
   ```

---

### **Lab 5: Upsert Operations**

1. **Upsert Example:**
   - Update Emma’s `major` to "Electrical Engineering." If she doesn’t exist, insert the document.

   ```javascript
   db.students.updateOne(
      { name: "Emma" },
      { $set: { major: "Electrical Engineering" } },
      { upsert: true }
   );
   ```

2. **Conditional Upsert:**
   - Update or insert a new document where the `name` is "George" and the `gpa` is 3.3.

   ```javascript
   db.students.updateOne(
      { name: "George" },
      { $setOnInsert: { name: "George", age: 26, major: "History", gpa: 3.3 } },
      { upsert: true }
   );
   ```

---

### **Lab 6: Removing Databases, Fields, and Collections**

1. **Remove Fields from a Document:**
   - Remove the `courses` field from Alice's document.

   ```javascript
   db.students.updateOne({ name: "Alice" }, { $unset: { courses: "" } });
   ```

2. **Drop an Entire Collection:**
   - Drop the `students` collection.

   ```javascript
   db.students.drop();
   ```

3. **Drop a Database:**
   - Drop the `companyDB` database.

   ```javascript
   db.dropDatabase();
   ```

---

### **Lab 7: Conditional and Advanced Options**

1. **Conditional Query with Operators:**
   - Find all students with either a `gpa` greater than 3.8 or `age` less than 22.

   ```javascript
   db.students.find({ $or: [ { gpa: { $gt: 3.8 } }, { age: { $lt: 22 } } ] }).pretty();
   ```

2. **Update with Conditional Logic:**
   - Increase the `age` of all students who are 22 or older by 1.

   ```javascript
   db.students.updateMany({ age: { $gte: 22 } }, { $inc: { age: 1 } });
   ```

---

### **Conclusion:**
In this lab, you have explored various MongoDB CRUD operations, including advanced options such as conditional queries, upsert, and removal of fields or collections. These operations are crucial for managing documents and collections effectively in MongoDB.