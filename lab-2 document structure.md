### **Lab: MongoDB Document Manipulation and Advanced Features**

#### **Objective:**
This lab focuses on MongoDB's advanced features, such as handling data types, document references, IDs, keys, embedded sub-documents, and more. You will perform tasks using MongoDB to get hands-on experience with these features.

### **Lab 1: Inserting Documents and Working with Data Types**

1. **Insert an employee document** with different MongoDB data types:
   ```javascript
   db.employees.insertOne({
      _id: ObjectId(),
      name: "John Doe",
      age: 30,
      hireDate: ISODate("2020-05-01T08:30:00Z"),
      department: "Engineering",
      salary: NumberDecimal("75000.00"),
      manager: null,
      active: true,
      projects: ["MongoDB Migration", "Cloud Deployment"],
      personalInfo: {
         address: "123 Main St, Anytown",
         phone: "555-1234",
         email: "john.doe@example.com"
      }
   })
   ```

2. **Explanation of Data Types:**
   - `_id`: ObjectId
   - `hireDate`: ISODate
   - `salary`: NumberDecimal
   - `active`: Boolean
   - `projects`: Array
   - `personalInfo`: Embedded Sub-document

---

### **Lab 2: Working with References and IDs**

MongoDB allows referencing documents from other collections using IDs.

1. **Create a `departments` collection** and insert documents:
   ```javascript
   db.departments.insertMany([
      { _id: ObjectId(), name: "Engineering", budget: 500000 },
      { _id: ObjectId(), name: "Marketing", budget: 300000 }
   ])
   ```

2. **Insert an employee with a department reference**:
   ```javascript
   var departmentId = db.departments.findOne({ name: "Engineering" })._id;
   db.employees.insertOne({
      _id: ObjectId(),
      name: "Alice Smith",
      departmentId: departmentId,
      salary: NumberDecimal("82000.00")
   });
   ```

3. **Reference explanation**: The `departmentId` field links the `employees` collection with the `departments` collection.

---

### **Lab 3: Using Keys in MongoDB**

1. **Insert a document with unique keys**:
   ```javascript
   db.employees.createIndex({ email: 1 }, { unique: true })
   ```

2. **Insert an employee with an email field**:
   ```javascript
   db.employees.insertOne({
      _id: ObjectId(),
      name: "Mark Thomas",
      email: "mark.thomas@example.com",
      salary: NumberDecimal("95000.00")
   });
   ```

3. **Try inserting a duplicate email**:
   ```javascript
   db.employees.insertOne({
      _id: ObjectId(),
      name: "Sarah Connor",
      email: "mark.thomas@example.com",
      salary: NumberDecimal("70000.00")
   });
   ```
   - You should get an error due to the unique key constraint on the email field.

---

### **Lab 4: Embedded Sub-documents and Tree Structures**

MongoDB allows embedding documents inside other documents to represent hierarchical data.

1. **Create a `categories` collection** with embedded sub-documents:
   ```javascript
   db.categories.insertOne({
      _id: ObjectId(),
      name: "Electronics",
      subcategories: [
         { name: "Laptops", subcategories: [{ name: "Gaming Laptops" }, { name: "Ultrabooks" }] },
         { name: "Mobile Phones", subcategories: [{ name: "Smartphones" }, { name: "Feature Phones" }] }
      ]
   })
   ```

2. **Tree structure explanation**: The `subcategories` array in the document creates a hierarchical tree structure representing categories and subcategories.

---

### **Lab 5: Working with Tailable Cursors**

Tailable cursors allow you to process data from capped collections in real-time, like reading from a log file.

1. **Create a capped collection** (required for tailable cursors):
   ```javascript
   db.createCollection("logs", { capped: true, size: 5000 })
   ```

2. **Insert some logs**:
   ```javascript
   db.logs.insertMany([
      { message: "Application started", timestamp: new Date() },
      { message: "User logged in", timestamp: new Date() },
      { message: "Data backup completed", timestamp: new Date() }
   ]);
   ```

3. **Use a tailable cursor** to listen for new logs:
   ```javascript
   var cursor = db.logs.find().addOption(DBQuery.Option.tailable);
   while (cursor.hasNext()) {
      print(cursor.next().message);
   }
   ```

---

### **Lab 6: Two-Phase Commits in MongoDB**

Two-phase commits are useful in scenarios requiring transactions across multiple documents or collections.

1. **Start a MongoDB session** and initiate a transaction:
   ```javascript
   session = db.getMongo().startSession();
   session.startTransaction();

   db.departments.updateOne(
      { name: "Engineering" },
      { $inc: { budget: -50000 } },
      { session: session }
   );

   db.employees.updateOne(
      { name: "Alice Smith" },
      { $inc: { salary: 50000 } },
      { session: session }
   );
   ```

2. **Commit the transaction**:
   ```javascript
   session.commitTransaction();
   session.endSession();
   ```

3. **Transaction explanation**: This ensures that both the budget update and the salary update occur together or not at all.

---

### **Lab 7: Auto-incrementing Sequence Field**

MongoDB doesn’t support auto-increment natively, but you can create a counter collection to simulate this behavior.

1. **Create a counter collection**:
   ```javascript
   db.createCollection("counters")
   db.counters.insertOne({ _id: "employeeId", sequence_value: 0 })
   ```

2. **Create a function to get the next sequence number**:
   ```javascript
   function getNextSequence(name) {
      var ret = db.counters.findOneAndUpdate(
         { _id: name },
         { $inc: { sequence_value: 1 } },
         { returnNewDocument: true }
      );
      return ret.sequence_value;
   }
   ```

3. **Insert a new employee using the auto-increment ID**:
   ```javascript
   db.employees.insertOne({
      _id: getNextSequence("employeeId"),
      name: "Jane Doe",
      department: "Marketing",
      salary: NumberDecimal("78000.00")
   });
   ```

---

### **Conclusion**

In this lab, you explored various advanced MongoDB features such as working with different data types, references, unique keys, embedded sub-documents, tailable cursors, two-phase commits, and an auto-incrementing sequence field. These exercises provided hands-on experience with critical MongoDB functionalities for document manipulation and database structuring.

---

**End of Lab**