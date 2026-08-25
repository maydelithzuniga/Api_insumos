const express = require("express");
const db = require("./db");

const app = express();
app.use(express.json());

// GET todos los productos / POST un nuevo producto
app.route("/productos")
	.get((req, res) => {
		db.all("SELECT * FROM productos", [], (err, rows) => {
			if (err) return res.status(500).json({ error: err.message });
			res.json(rows);
		});
	})
	.post((req, res) => {
		const { nombre, categoria, precio, stock } = req.body;
		const sql = `INSERT INTO productos (nombre, categoria, precio, stock) VALUES (?, ?, ?, ?)`;

		db.run(sql, [nombre, categoria, precio, stock], function (err) {
			if (err) return res.status(500).json({ error: err.message });
			res.status(201).json({ message: `Producto creado con id: ${this.lastID}` });
		});
	});

// GET / PUT / DELETE un solo producto por id
app.route("/productos/:id")
	.get((req, res) => {
		db.get("SELECT * FROM productos WHERE id = ?", [req.params.id], (err, row) => {
			if (err) return res.status(500).json({ error: err.message });
			if (!row) return res.status(404).json({ message: "Producto no encontrado" });
			res.json(row);
		});
	})
	.put((req, res) => {
		const { nombre, categoria, precio, stock } = req.body;
		const sql = `UPDATE productos SET nombre = ?, categoria = ?, precio = ?, stock = ? WHERE id = ?`;

		db.run(sql, [nombre, categoria, precio, stock, req.params.id], function (err) {
			if (err) return res.status(500).json({ error: err.message });
			if (this.changes === 0) return res.status(404).json({ message: "Producto no encontrado" });
			res.json({ id: req.params.id, nombre, categoria, precio, stock });
		});
	})
	.delete((req, res) => {
		db.run("DELETE FROM productos WHERE id = ?", [req.params.id], function (err) {
			if (err) return res.status(500).json({ error: err.message });
			if (this.changes === 0) return res.status(404).json({ message: "Producto no encontrado" });
			res.json({ message: `Producto con id: ${req.params.id} eliminado.` });
		});
	});

app.get("/productos/stock/bajo/:limite", (req, res) => {
	db.all("SELECT * FROM productos WHERE stock <= ?", [req.params.limite], (err, rows) => {
		if (err) return res.status(500).json({ error: err.message });
		res.json(rows);
	});
});

app.listen(8000, () => console.log("Servidor corriendo en http://localhost:8000"));