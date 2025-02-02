# GamesDB Documentation

## Table of Contents
1. [Overview](#overview)
2. [Installation](#installation)
3. [Database Schema](#database-schema)
4. [Class Methods](#class-methods)
5. [Error Handling](#error-handling)
6. [Examples](#examples)
7. [Best Practices](#best-practices)
8. [Performance Considerations](#performance-considerations)

## Overview

GamesDB is a class that provides a robust interface for managing a video game database. It handles games, genres, platforms, and their relationships.

### Key Features
- Complete CRUD operations for games and related entities.
- Advanced search with multiple filters.
- Automatic slug generation for SEO-friendly URLs.
- Transaction support.
- Connection pooling.
- Comprehensive error handling.

## Installation

```javascript
import { GamesDB } from './GamesDB.js';
import { DBManager } from './DBManager.js';

const dbManager = new DBManager({ path: 'games.db' });
const gamesDB = new GamesDB(dbManager);

// Initialize database tables
await gamesDB.init();
```

## Database Schema

### Tables Structure

#### games
```sql
CREATE TABLE IF NOT EXISTS games (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  description TEXT,
  release_date DATE,
  developer TEXT,
  publisher TEXT,
  rating REAL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

#### genres
```sql
CREATE TABLE IF NOT EXISTS genres (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT UNIQUE NOT NULL
);
```

#### game_genres
```sql
CREATE TABLE IF NOT EXISTS game_genres (
  game_id INTEGER NOT NULL,
  genre_id INTEGER NOT NULL,
  PRIMARY KEY (game_id, genre_id),
  FOREIGN KEY (game_id) REFERENCES games (id) ON DELETE CASCADE,
  FOREIGN KEY (genre_id) REFERENCES genres (id) ON DELETE CASCADE
);
```

#### platforms
```sql
CREATE TABLE IF NOT EXISTS platforms (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT UNIQUE NOT NULL
);
```

#### game_platforms
```sql
CREATE TABLE IF NOT EXISTS game_platforms (
  game_id INTEGER NOT NULL,
  platform_id INTEGER NOT NULL,
  PRIMARY KEY (game_id, platform_id),
  FOREIGN KEY (game_id) REFERENCES games (id) ON DELETE CASCADE,
  FOREIGN KEY (platform_id) REFERENCES platforms (id) ON DELETE CASCADE
);
```

## Class Methods

### Constructor
```javascript
constructor(dbManager)
```
Creates a new instance of GamesDB.
- `dbManager`: An instance of DBManager for database operations.

### Database Initialization
```javascript
async init()
```
Initializes the database by creating all necessary tables.
- Returns: A promise that resolves when initialization is complete.
- Throws: An error if initialization fails.

### Game Management

#### addGame
```javascript
async addGame(game, genres, platforms)
```
Adds a new game with its relationships.
- Parameters:
  - `game`: An object with properties like `title`, `description`, `release_date`, `developer`, `publisher`, and `rating`.
  - `genres`: An array of genre names.
  - `platforms`: An array of platform names.
- Returns: A promise that resolves with the inserted game ID.
- Throws: An error if the operation fails.

#### getGameById
```javascript
async getGameById(id)
```
Retrieves a game by its ID along with its related genres and platforms.
- Parameters:
  - `id`: The game ID.
- Returns: A promise that resolves with the game data or `null` if not found.
- Throws: An error if the query fails.

#### getAllGames
```javascript
async getAllGames()
```
Retrieves all games with their associated genres and platforms.
- Returns: A promise that resolves with an array of game objects.
- Throws: An error if the query fails.

#### deleteGame
```javascript
async deleteGame(id)
```
Deletes a game and all its related entries.
- Parameters:
  - `id`: The game ID.
- Returns: A promise that resolves to `true` if the game was deleted, or `false` if not.
- Throws: An error if deletion fails.

## Error Handling

All methods include error handling using try/catch blocks. For example:

```javascript
try {
  await gamesDB.addGame(gameData, genres, platforms);
} catch (error) {
  // error.message contains detailed error information
  console.error('Failed to add game:', error.message);
}
```

Common errors include:
- Database connection issues.
- Data validation errors.
- Foreign key and unique constraint violations.

## Examples

### Adding a New Game
```javascript
const gameId = await gamesDB.addGame(
  {
    title: 'The Legend of Zelda: Breath of the Wild',
    description: 'An action-adventure game in an open world.',
    release_date: '2017-03-03',
    developer: 'Nintendo',
    publisher: 'Nintendo',
    rating: 9.5
  },
  ['Action', 'Adventure'],
  ['Switch']
);
console.log('Game inserted with ID:', gameId);
```

### Retrieving All Games
```javascript
const games = await gamesDB.getAllGames();
console.log('Games:', games);
```

### Retrieving a Game by ID
```javascript
const game = await gamesDB.getGameById(gameId);
console.log('Game:', game);
```

### Deleting a Game
```javascript
const success = await gamesDB.deleteGame(gameId);
console.log('Game deleted:', success);
```

## Best Practices

1. Always use transactions for operations that modify multiple tables.
2. Close database connections when they are no longer needed.
3. Validate input data before performing database operations.
4. Handle errors appropriately.
5. Use pagination for queries that return large datasets.

## Performance Considerations

1. Utilize connection pooling.
2. Create indexes on frequently queried fields.
3. Optimize JOIN queries.
4. Use pagination to manage large result sets.
5. Use transactions to maintain data consistency.
