# day-34-task
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=password
DB_NAME=zen_class
// db.js
const { Sequelize } = require('sequelize');
require('dotenv').config();

const sequelize = new Sequelize(process.env.DB_NAME, process.env.DB_USER, process.env.DB_PASSWORD, {
  host: process.env.DB_HOST,
  dialect: 'mysql',
});

sequelize.authenticate()
  .then(() => console.log('Database connected...'))
  .catch(err => console.log('Error: ' + err));

module.exports = sequelize;
// models/User.js
const { DataTypes } = require('sequelize');
const sequelize = require('../db');

const User = sequelize.define('User', {
  User_ID: {
    type: DataTypes.INTEGER,
    primaryKey: true,
    autoIncrement: true,
  },
  Name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  Email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
  Password: {
    type: DataTypes.STRING,
    allowNull: false,
  },
  Role: {
    type: DataTypes.ENUM('Student', 'Instructor', 'Admin'),
    allowNull: false,
  },
});

module.exports = User;
// routes/user.js
const express = require('express');
const router = express.Router();
const User = require('../models/User');

// Get all users
router.get('/', async (req, res) => {
  const users = await User.findAll();
  res.json(users);
});

// Get a specific user
router.get('/:id', async (req, res) => {
  const user = await User.findByPk(req.params.id);
  res.json(user);
});

// Create a new user
router.post('/', async (req, res) => {
  const newUser = await User.create(req.body);
  res.json(newUser);
});

// Update a user
router.put('/:id', async (req, res) => {
  const user = await User.update(req.body, {
    where: { User_ID: req.params.id }
  });
  res.json(user);
});

// Delete a user
router.delete('/:id', async (req, res) => {
  await User.destroy({
    where: { User_ID: req.params.id }
  });
  res.json({ message: 'User deleted' });
});

module.exports = router;
// server.js
const express = require('express');
const app = express();
const userRoutes = require('./routes/user');

app.use(express.json());

app.use('/api/users', userRoutes);

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
