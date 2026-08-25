const sqlite3 = require("sqlite3").verbose();

const db = new sqlite3.Database("./ingredients.sqlite", (err) => {
	if (err) return console.error(err.message);
	console.log("Conectado a la base de datos SQLite.");
});

db.run(`
	CREATE TABLE IF NOT EXISTS ingredients (
		id INTEGER PRIMARY KEY AUTOINCREMENT,
		nombre TEXT NOT NULL,
		categoria TEXT NOT NULL,
		precio REAL NOT NULL,
		stock INTEGER NOT NULL DEFAULT 0
	)
`);

module.exports = db;