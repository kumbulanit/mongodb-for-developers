Here are two lab exercises designed to help you practice document manipulation methods in MongoDB, including insertion, updating, upserting, removing documents, fields, collections, and databases.

---

### Lab 1: Employee Database Management

#### Objective:
Create an employee database and perform various document manipulation methods, including insertion, updating, and removing documents.

#### Instructions:

1. **Set Up MongoDB:**
   - Make sure you have MongoDB installed and running on your local machine or use a cloud-based MongoDB service.

2. **Create a Database and Collection:**
   - Create a database named `Company`.
   - Within the `Company` database, create a collection named `Employees`.

   ```javascript
   use Company;
   db.createCollection("Employees");
   ```

3. **Insert Documents:**
   - Insert at least five employee documents into the `Employees` collection with the following fields: `name`, `age`, `department`, and `salary`.

   ```javascript
   db.Employees.insertMany([
       { name: "Alice Smith", age: 30, department: "Engineering", salary: 80000 },
       { name: "Bob Johnson", age: 45, department: "Sales", salary: 60000 },
       { name: "Charlie Brown", age: 28, department: "Marketing", salary: 55000 },
       { name: "Diana Prince", age: 35, department: "Engineering", salary: 95000 },
       { name: "Evan Williams", age: 50, department: "Sales", salary: 70000 }
   ]);
   ```

4. **Update a Document:**
   - Update the salary of "Alice Smith" to $85,000.

   ```javascript
   db.Employees.updateOne({ name: "Alice Smith" }, { $set: { salary: 85000 } });
   ```

5. **Upsert a Document:**
   - Upsert a document for an employee named "Fiona Green" with the following details: `age: 32`, `department: "HR"`, and `salary: 60000`. If the document already exists, update the information.

   ```javascript
   db.Employees.updateOne(
       { name: "Fiona Green" },
       { $set: { age: 32, department: "HR", salary: 60000 } },
       { upsert: true }
   );
   ```

6. **Remove a Document:**
   - Remove the document for "Charlie Brown".

   ```javascript
   db.Employees.deleteOne({ name: "Charlie Brown" });
   ```

7. **Remove a Field from Documents:**
   - Remove the `department` field from all employees aged over 40.

   ```javascript
   db.Employees.updateMany(
       { age: { $gt: 40 } },
       { $unset: { department: "" } }
   );
   ```

8. **Clean Up:**
   - Drop the `Employees` collection and the `Company` database.

   ```javascript
   db.Employees.drop();
   db.dropDatabase();
   ```

---

### Lab 2: Library Management System

#### Objective:
Create a library management system and practice document manipulation methods, including insertion, updating, removing documents, fields, and the entire database.

#### Instructions:

1. **Set Up MongoDB:**
   - Ensure that MongoDB is installed and running.

2. **Create a Database and Collection:**
   - Create a database named `Library`.
   - Within the `Library` database, create a collection named `Books`.

   ```javascript
   use Library;
   db.createCollection("Books");
   ```

3. **Insert Documents:**
   - Insert at least four book documents into the `Books` collection with the following fields: `title`, `author`, `year`, and `genre`.

   ```javascript
   db.Books.insertMany([
       { title: "1984", author: "George Orwell", year: 1949, genre: "Dystopian" },
       { title: "To Kill a Mockingbird", author: "Harper Lee", year: 1960, genre: "Fiction" },
       { title: "The Great Gatsby", author: "F. Scott Fitzgerald", year: 1925, genre: "Classic" },
       { title: "Moby Dick", author: "Herman Melville", year: 1851, genre: "Adventure" }
   ]);
   ```

4. **Update a Document:**
   - Update the `year` of "Moby Dick" to 1852.

   ```javascript
   db.Books.updateOne({ title: "Moby Dick" }, { $set: { year: 1852 } });
   ```

5. **Remove a Document:**
   - Remove the document for "The Great Gatsby".

   ```javascript
   db.Books.deleteOne({ title: "The Great Gatsby" });
   ```

6. **Remove a Field from Documents:**
   - Remove the `genre` field from all books published before 1950.

   ```javascript
   db.Books.updateMany(
       { year: { $lt: 1950 } },
       { $unset: { genre: "" } }
   );
   ```

7. **Drop the Collection:**
   - Drop the `Books` collection.

   ```javascript
   db.Books.drop();
   ```

8. **Clean Up:**
   - Drop the `Library` database.

   ```javascript
   db.dropDatabase();
   ```

---

### Conclusion

These labs provide hands-on experience with essential document manipulation methods in MongoDB, allowing you to understand how to manage data effectively. Make sure to explore additional features and methods as you become more comfortable with MongoDB!