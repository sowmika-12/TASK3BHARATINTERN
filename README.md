# TASK3BHARATINTERN
/project-root
|-- public
|   |-- styles.css
|-- views
|   |-- index.html
|-- server.js
|-- package.json

npm init -y
npm install express mongoose body-parser

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="/styles.css">
  <title>Money Tracker</title>
</head>
<body>
  <div class="container">
    <h2>Money Tracker</h2>
    <form id="transactionForm" action="/add-transaction" method="post">
      <label for="description">Description:</label>
      <input type="text" id="description" name="description" required>

      <label for="amount">Amount:</label>
      <input type="number" id="amount" name="amount" required>

      <label for="type">Type:</label>
      <select id="type" name="type" required>
        <option value="income">Income</option>
        <option value="expense">Expense</option>
      </select>

      <button type="submit">Add Transaction</button>
    </form>

    <h3>Transaction History</h3>
    <ul id="transactionList"></ul>
  </div>

  <script src="/app.js"></script>
</body>
</html>


body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.container {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

form {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  margin-bottom: 8px;
}

input, select {
  padding: 8px;
  margin-bottom: 16px;
}

button {
  padding: 10px;
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  border-bottom: 1px solid #ddd;
  padding: 10px;
  display: flex;
  justify-content: space-between;
}

const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

const app = express();
const PORT = process.env.PORT || 3000;

// Connect to MongoDB (replace 'your-mongodb-uri' with your actual MongoDB connection string)
mongoose.connect('mongodb://localhost:27017/your-database-name', { useNewUrlParser: true, useUnifiedTopology: true });

// Create a mongoose schema and model for transactions
const transactionSchema = new mongoose.Schema({
  description: String,
  amount: Number,
  type: String,
  timestamp: { type: Date, default: Date.now }
});

const Transaction = mongoose.model('Transaction', transactionSchema);

// Middleware to parse the request body
app.use(bodyParser.urlencoded({ extended: true }));

// Serve static files from the 'public' directory
app.use(express.static('public'));

// Serve the money tracker app
app.get('/', async (req, res) => {
  const transactions = await Transaction.find().sort({ timestamp: -1 });
  res.render(__dirname + '/views/index.html', { transactions });
});

// Handle adding a new transaction
app.post('/add-transaction', async (req, res) => {
  const { description, amount, type } = req.body;

  try {
    // Save the transaction to the database
    const newTransaction = new Transaction({ description, amount, type });
    await newTransaction.save();

    // Redirect to the home page after adding a transaction
    res.redirect('/');
  } catch (error) {
    res.status(500).send('Error adding transaction.');
  }
});

// Start the server
app.listen(PORT, () => {
  console.log(Server is running on http://localhost:${PORT});
});
