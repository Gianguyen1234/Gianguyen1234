---
title: "🚀 Build a Simple Food API with JavaScript (Node.js & Express) 🍕🍣🍔"
datePublished: Sat Feb 08 2025 04:39:14 GMT+0000 (Coordinated Universal Time)
cuid: cm6vphbwl000b09kwd1ch7gqq
slug: build-a-simple-food-api-with-javascript-nodejs-express
tags: js, apis

---

Youtube video: [https://youtu.be/F77VG0kTgak](https://youtu.be/F77VG0kTgak)

To create a simple API for food using JavaScript, you can use Node.js along with the Express.js framework. Here's a step-by-step guide to creating your API:

### 1\. Initialize Your Project

First, make sure Node.js is installed. Then, initialize your project by running:

```bash
mkdir food-api
cd food-api
npm init -y
```

### 2\. Install Dependencies

Install the necessary packages for the API:

```bash
npm install express
```

### 3\. Create `server.js`

In your project directory, create a `server.js` file, which will contain the API logic.

```javascript
const express = require('express');
const app = express();
const port = 3000;

// Sample food data
const foods = [
  { id: 1, name: 'Pizza', type: 'Italian' },
  { id: 2, name: 'Sushi', type: 'Japanese' },
  { id: 3, name: 'Burger', type: 'American' },
];

// Middleware to parse JSON
app.use(express.json());

// Routes
// Get all foods
app.get('/api/foods', (req, res) => {
  res.json(foods);
});

// Get a food by ID
app.get('/api/foods/:id', (req, res) => {
  const food = foods.find((f) => f.id === parseInt(req.params.id));
  if (!food) return res.status(404).send('Food not found');
  res.json(food);
});

// Create a new food
app.post('/api/foods', (req, res) => {
  const food = {
    id: foods.length + 1,
    name: req.body.name,
    type: req.body.type,
  };
  foods.push(food);
  res.status(201).json(food);
});

// Update a food by ID
app.put('/api/foods/:id', (req, res) => {
  const food = foods.find((f) => f.id === parseInt(req.params.id));
  if (!food) return res.status(404).send('Food not found');

  food.name = req.body.name;
  food.type = req.body.type;
  res.json(food);
});

// Delete a food by ID
app.delete('/api/foods/:id', (req, res) => {
  const foodIndex = foods.findIndex((f) => f.id === parseInt(req.params.id));
  if (foodIndex === -1) return res.status(404).send('Food not found');

  const deletedFood = foods.splice(foodIndex, 1);
  res.json(deletedFood);
});

// Start the server
app.listen(port, () => {
  console.log(`Food API running at http://localhost:${port}`);
});
```

### 4\. Run the API Server

To run the server, use the following command:

```bash
node server.js
```

### 5\. Test Your API

You can test your API using a tool like [Postman](https://www.postman.com/) or by making HTTP requests from your browser:

* `GET http://localhost:3000/api/foods` – Get all foods
    
* `GET http://localhost:3000/api/foods/1` – Get a food by ID
    
* `POST http://localhost:3000/api/foods` – Create a new food (use JSON body with `name` and `type`)
    
* `PUT http://localhost:3000/api/foods/1` – Update food by ID
    
* `DELETE http://localhost:3000/api/foods/1` – Delete food by ID
    

This is a basic implementation of a food API using JavaScript (Node.js) and Express. You can enhance it further by connecting it to a database, adding authentication, or implementing more advanced features.