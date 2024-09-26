

#### **Example Lab: Aggregation Pipeline**

This lab will guide you through using different stages of the aggregation pipeline to analyze and transform data.

**Scenario**: You are working with a bookstore database and need to generate reports on book sales, including the total sales per genre and the average rating of books sold.

##### **Step 1: Set Up the Environment**

Create a sample collection called `books` with the following documents:

```javascript
db.books.insertMany([
  {
    _id: 1,
    title: "The Great Gatsby",
    author: "F. Scott Fitzgerald",
    genre: "Fiction",
    sales: 300,
    ratings: [{ user: "user1", rating: 4.5 }, { user: "user2", rating: 4.0 }],
  },
  {
    _id: 2,
    title: "1984",
    author: "George Orwell",
    genre: "Dystopian",
    sales: 450,
    ratings: [{ user: "user3", rating: 4.7 }, { user: "user4", rating: 4.9 }],
  },
  {
    _id: 3,
    title: "To Kill a Mockingbird",
    author: "Harper Lee",
    genre: "Fiction",
    sales: 200,
    ratings: [{ user: "user5", rating: 4.8 }, { user: "user6", rating: 4.6 }],
  },
  {
    _id: 4,
    title: "Brave New World",
    author: "Aldous Huxley",
    genre: "Dystopian",
    sales: 320,
    ratings: [{ user: "user7", rating: 4.2 }, { user: "user8", rating: 4.4 }],
  },
]);
```

##### **Step 2: Aggregate Total Sales per Genre**

Use the `$group` and `$sum` stages to calculate total sales for each genre:

```javascript
db.books.aggregate([
  {
    $group: {
      _id: "$genre",
      totalSales: { $sum: "$sales" },
    },
  },
]);
```

**Expected Output:**
```json
[
  { "_id": "Fiction", "totalSales": 500 },
  { "_id": "Dystopian", "totalSales": 770 }
]
```

##### **Step 3: Calculate the Average Rating per Book**

Use the `$unwind`, `$group`, and `$avg` stages to calculate the average rating for each book:

```javascript
db.books.aggregate([
  { $unwind: "$ratings" },
  {
    $group: {
      _id: "$title",
      averageRating: { $avg: "$ratings.rating" },
    },
  },
]);
```

**Expected Output:**
```json
[
  { "_id": "The Great Gatsby", "averageRating": 4.25 },
  { "_id": "1984", "averageRating": 4.8 },
  { "_id": "To Kill a Mockingbird", "averageRating": 4.7 },
  { "_id": "Brave New World", "averageRating": 4.3 }
]
```

##### **Step 4: Filter Books with High Ratings**

Use the `$match` stage to filter books that have an average rating above 4.5:

```javascript
db.books.aggregate([
  { $unwind: "$ratings" },
  {
    $group: {
      _id: "$title",
      averageRating: { $avg: "$ratings.rating" },
    },
  },
  { $match: { averageRating: { $gt: 4.5 } } },
]);
```

**Expected Output:**
```json
[
  { "_id": "1984", "averageRating": 4.8 },
  { "_id": "To Kill a Mockingbird", "averageRating": 4.7 }
]
```

##### **Step 5: Project Specific Fields**

Use the `$project` stage to display only the title and genre of each book:

```javascript
db.books.aggregate([
  {
    $project: {
      _id: 0,
      title: 1,
      genre: 1,
    },
  },
]);
```

**Expected Output:**
```json
[
  { "title": "The Great Gatsby", "genre": "Fiction" },
  { "title": "1984", "genre": "Dystopian" },
  { "title": "To Kill a Mockingbird", "genre": "Fiction" },
  { "title": "Brave New World", "genre": "Dystopian" }
]
```

#### **Conclusion**

Aggregation pipelines are versatile and efficient for complex data manipulation and analysis. The above lab demonstrates how different stages can be combined to achieve meaningful insights from data, making aggregation a powerful tool in MongoDB.