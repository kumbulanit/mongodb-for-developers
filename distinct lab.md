

### **Lab: Using Distinct in MongoDB**

#### **Objective:**
To practice using the `distinct` operation in MongoDB to retrieve unique values from a specified field, and to understand how filtering affects the results.

#### **Lab Setup:**

1. **Create a Sample Collection:**
   First, create a collection called `inventory` with sample data:

   ```javascript
   db.inventory.insertMany([
     { "_id": 1, "item": "Notebook", "type": "Stationery", "price": 3.5, "supplier": "Alpha Corp" },
     { "_id": 2, "item": "Pen", "type": "Stationery", "price": 1.0, "supplier": "Beta Ltd" },
     { "_id": 3, "item": "Notebook", "type": "Stationery", "price": 3.5, "supplier": "Alpha Corp" },
     { "_id": 4, "item": "Chair", "type": "Furniture", "price": 45.0, "supplier": "Gamma Co" },
     { "_id": 5, "item": "Desk", "type": "Furniture", "price": 150.0, "supplier": "Gamma Co" },
     { "_id": 6, "item": "Lamp", "type": "Electronics", "price": 20.0, "supplier": "Alpha Corp" },
     { "_id": 7, "item": "Monitor", "type": "Electronics", "price": 120.0, "supplier": "Delta Inc" }
   ])
   ```

2. **Task 1: Find Distinct Types**
   Use the `distinct` command to list all unique product types in the inventory.

   ```javascript
   db.inventory.distinct("type")
   // Expected Output: ["Stationery", "Furniture", "Electronics"]
   ```

3. **Task 2: Find Distinct Suppliers of Furniture Items**
   Use `distinct` with a filter to find all unique suppliers for products of type "Furniture".

   ```javascript
   db.inventory.distinct("supplier", { type: "Furniture" })
   // Expected Output: ["Gamma Co"]
   ```

4. **Task 3: Find Distinct Prices of Items Supplied by "Alpha Corp"**
   Retrieve distinct prices of items supplied by "Alpha Corp".

   ```javascript
   db.inventory.distinct("price", { supplier: "Alpha Corp" })
   // Expected Output: [3.5, 20.0]
   ```

5. **Task 4: Count the Distinct Items**
   Use JavaScript to count how many distinct items are in the inventory:

   ```javascript
   let distinctItems = db.inventory.distinct("item").length;
   print("Total distinct items: ", distinctItems);
   // Expected Output: Total distinct items:  6
   ```

#### **Lab Summary:**

- The `distinct` command helps retrieve unique values, which can be filtered further using query criteria.
- It's useful for data analysis, quick insights, and refining queries when dealing with complex datasets.

By completing this lab, you gain hands-on experience with the `distinct` function, understanding its use cases and limitations in real-world scenarios.