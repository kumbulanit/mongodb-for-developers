### **Lab Extension: Update Operations in MongoDB**

In this extension of the lab, we will explore two fundamental update operations in MongoDB: `updateOne()` for updating a single document and `updateMany()` for updating multiple documents at once.

---

### **Lab Setup:**

Ensure your MongoDB environment is running, and you have the `companyDB` database created as mentioned in the previous labs.

---

### **Lab 8: Update Operations**

#### **Step 1: Update a Single Document (updateOne)**

We will use the `updateOne()` method to update a specific employee's salary.

1. **Check the current state of the `employees` collection:**
   ```javascript
   db.employees.find({ name: "John Doe" }).pretty();
   ```

2. **Use `updateOne()` to change John Doe’s salary:**
   ```javascript
   db.employees.updateOne(
      { name: "John Doe" },  // Filter: Find the employee by name
      { $set: { salary: NumberDecimal("80000.00") } }  // Update: Set the new salary
   );
   ```

3. **Verify the update:**
   ```javascript
   db.employees.find({ name: "John Doe" }).pretty();
   ```
   - You should see the updated salary reflected in the document.

#### **Step 2: Update Multiple Documents (updateMany)**

Now, we will use `updateMany()` to update multiple documents based on a common condition.

1. **Insert multiple employees to demonstrate `updateMany()`:**
   ```javascript
   db.employees.insertMany([
      { name: "Jake Peralta", department: "Detective", salary: NumberDecimal("55000.00") },
      { name: "Terry Jeffords", department: "Detective", salary: NumberDecimal("62000.00") },
      { name: "Rosa Diaz", department: "Detective", salary: NumberDecimal("58000.00") }
   ]);
   ```

2. **Use `updateMany()` to increase the salary of all employees in the "Detective" department:**
   ```javascript
   db.employees.updateMany(
      { department: "Detective" },  // Filter: Employees in "Detective" department
      { $inc: { salary: 5000 } }  // Update: Increase the salary by 5000
   );
   ```

3. **Verify the update:**
   ```javascript
   db.employees.find({ department: "Detective" }).pretty();
   ```
   - All employees in the "Detective" department should now have their salaries increased by 5000.

---

### **Explanation:**

1. **`updateOne()`**:
   - This method updates the first document that matches the given filter.
   - It is useful when you need to modify a specific document based on unique identifiers (e.g., name or _id).

2. **`updateMany()`**:
   - This method updates all documents that match the given filter.
   - It is ideal when multiple documents share the same attribute or condition that needs to be updated.

---

### **Conclusion**

In this extension, you practiced using `updateOne()` to update a single document and `updateMany()` to update multiple documents in MongoDB. These operations are crucial for efficiently modifying data based on certain conditions.