
#### **Example of Map-Reduce in MongoDB**

Consider a collection of orders where you need to calculate the total sales for each product. Below is a detailed example that shows how to set up and run a Map-Reduce operation in MongoDB.

### **Lab: Calculating Total Sales Per Product Using Map-Reduce**

#### **Step 1: Setup the `orders` Collection**

Insert some sample documents into the `orders` collection that represent customer orders:

```javascript
db.orders.insertMany([
  { _id: 1, product: "Laptop", quantity: 2, price: 1000 },
  { _id: 2, product: "Smartphone", quantity: 5, price: 500 },
  { _id: 3, product: "Laptop", quantity: 1, price: 1000 },
  { _id: 4, product: "Headphones", quantity: 10, price: 100 },
  { _id: 5, product: "Smartphone", quantity: 3, price: 500 }
]);
```

#### **Step 2: Define the Map and Reduce Functions**

1. **Map Function**: Emits the product name as the key and the total sales (quantity multiplied by price) as the value.

    ```javascript
    var mapFunction = function() {
      emit(this.product, this.quantity * this.price);
    };
    ```

2. **Reduce Function**: Sums up the total sales for each product.

    ```javascript
    var reduceFunction = function(key, values) {
      return Array.sum(values);
    };
    ```

#### **Step 3: Execute the Map-Reduce Operation**

Run the Map-Reduce operation using the defined functions:

```javascript
db.orders.mapReduce(
  mapFunction,
  reduceFunction,
  {
    out: "total_sales" // The output collection to store the result
  }
);
```

#### **Step 4: View the Results**

Check the results stored in the `total_sales` collection:

```javascript
db.total_sales.find();
```

**Expected Output:**

```json
[
  { "_id": "Headphones", "value": 1000 },
  { "_id": "Laptop", "value": 3000 },
  { "_id": "Smartphone", "value": 4000 }
]
```

This output shows the total sales for each product, where `_id` is the product name and `value` is the total sales amount.

#### **Step 5: Optional Finalize Function**

You can also define a finalize function to further process the result, such as converting the total sales into a different currency or format.

```javascript
var finalizeFunction = function(key, reducedValue) {
  return { totalSales: reducedValue, currency: "USD" };
};

db.orders.mapReduce(
  mapFunction,
  reduceFunction,
  {
    out: "total_sales",
    finalize: finalizeFunction
  }
);
```

#### **Step 6: Viewing the Finalized Results**

```javascript
db.total_sales.find();
```

**Expected Output:**

```json
[
  { "_id": "Headphones", "value": { "totalSales": 1000, "currency": "USD" } },
  { "_id": "Laptop", "value": { "totalSales": 3000, "currency": "USD" } },
  { "_id": "Smartphone", "value": { "totalSales": 4000, "currency": "USD" } }
]
```

