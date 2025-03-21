<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Gestión de Inventario</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
  <style>
    /* Estilos se mantienen igual */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
    }
    body {
      display: flex;
      height: 100vh;
      background-color: #f5f7fa;
    }
    .sidebar {
      width: 250px;
      background-color: #1e272e;
      color: white;
      padding-top: 30px;
      display: flex;
      flex-direction: column;
      align-items: center;
      box-shadow: 3px 0 10px rgba(0,0,0,0.1);
    }
    .sidebar h2 {
      margin-bottom: 40px;
      font-size: 24px;
      font-weight: 600;
    }
    .sidebar button {
      width: 90%;
      padding: 15px;
      margin: 10px 0;
      background-color: #0fbcf9;
      border: none;
      border-radius: 8px;
      color: white;
      font-size: 16px;
      cursor: pointer;
      transition: background 0.3s;
    }
    .sidebar button:hover {
      background-color: #00a8ff;
    }
    .main {
      flex-grow: 1;
      padding: 40px;
      overflow-y: auto;
    }
    h2 {
      font-size: 28px;
      margin-bottom: 20px;
      color: #2f3640;
    }
    .form-group {
      margin-bottom: 20px;
    }
    label {
      display: block;
      margin-bottom: 8px;
      font-weight: 600;
      font-size: 16px;
      color: #2f3640;
    }
    input, select {
      width: 100%;
      padding: 12px;
      font-size: 16px;
      border: 1px solid #dcdde1;
      border-radius: 8px;
      background-color: #fff;
    }
    button.submit-btn {
      background-color: #44bd32;
      color: white;
      padding: 15px 20px;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      cursor: pointer;
      transition: background 0.3s;
      margin-top: 10px;
    }
    button.submit-btn:hover {
      background-color: #4cd137;
    }
    .inventory-list {
      margin-top: 30px;
    }
    .inventory-item {
      background-color: white;
      padding: 15px;
      border-radius: 8px;
      margin-bottom: 15px;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      font-size: 16px;
      color: #353b48;
      position: relative;
    }
    .inventory-buttons {
      margin-top: 10px;
    }
    .inventory-buttons button {
      margin-right: 10px;
      padding: 8px 12px;
      font-size: 14px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      color: white;
    }
    .edit-btn {
      background-color: #e1b12c;
    }
    .delete-btn {
      background-color: #c23616;
    }
  </style>
</head>
<body>

  <div class="sidebar">
    <h2>Mi Empresa</h2>
    <button onclick="showInventory()">📦 Gestión de Inventario</button>
    <button>⚙️ Gestión de Elaboraciones</button>
    <button>👥 Gestión de Personal</button>
  </div>

  <div class="main" id="main-content">
    <h2>Bienvenido</h2>
    <p style="font-size:18px; color:#57606f;">Selecciona una opción del menú para comenzar.</p>
  </div>

  <script>
    let inventory = JSON.parse(localStorage.getItem('inventory')) || [];

    function saveInventory() {
      localStorage.setItem('inventory', JSON.stringify(inventory));
    }

    function showInventory() {
      const content = `
        <h2>📦 Gestión de Inventario</h2>
        <form id="inventory-form">
          <div class="form-group">
            <label for="product-name">Producto:</label>
            <select id="product-name" required>
              <option value="" disabled selected>Selecciona un producto</option>
              <option value="SAL">SAL</option>
              <option value="HIDRAL-70">HIDRAL-70</option>
              <option value="HYDROMAR-4">HYDROMAR-4</option>
              <option value="ANTIESPUMANTE">ANTIESPUMANTE</option>
            </select>
          </div>
          <div class="form-group">
            <label for="lot">Lote:</label>
            <input type="text" id="lot" required placeholder="Ej: Lote-1234">
          </div>
          <div class="form-group">
            <label for="quantity">Cantidad:</label>
            <input type="number" id="quantity" required placeholder="Ej: 100">
          </div>
          <div class="form-group">
            <label for="unit">Unidad:</label>
            <select id="unit">
              <option value="kg">Kilogramos (kg)</option>
              <option value="litros">Litros</option>
            </select>
          </div>
          <button type="submit" class="submit-btn">➕ Agregar Producto</button>
        </form>
        <div class="inventory-list" id="inventory-list"></div>
      `;
      document.getElementById('main-content').innerHTML = content;

      document.getElementById('inventory-form').addEventListener('submit', function(e) {
        e.preventDefault();
        const name = document.getElementById('product-name').value;
        const lot = document.getElementById('lot').value;
        const quantity = document.getElementById('quantity').value;
        const unit = document.getElementById('unit').value;
        const date = new Date();
        const dateString = `${date.toLocaleDateString()} ${date.toLocaleTimeString()}`;

        inventory.push({ name, lot, quantity, unit, date: dateString });
        saveInventory();
        updateInventoryList();
        this.reset();
      });

      updateInventoryList();
    }

    function updateInventoryList() {
      const list = document.getElementById('inventory-list');
      if (!list) return;
      list.innerHTML = '';
      if (inventory.length === 0) {
        list.innerHTML = `<p style="color:#718093;">No hay productos registrados.</p>`;
      } else {
        inventory.forEach((item, index) => {
          list.innerHTML += `<div class="inventory-item">
            <strong>${item.name}</strong><br>
            Lote: <strong>${item.lot}</strong><br>
            Cantidad: <strong>${item.quantity} ${item.unit}</strong><br>
            Fecha Registro: <strong>${item.date}</strong>
            <div class="inventory-buttons">
              <button class="edit-btn" onclick="editItem(${index})">✏️ Editar</button>
              <button class="delete-btn" onclick="deleteItem(${index})">🗑️ Eliminar</button>
            </div>
          </div>`;
        });
      }
    }

    function deleteItem(index) {
      if (confirm("¿Seguro que quieres eliminar este producto?")) {
        inventory.splice(index, 1);
        saveInventory();
        updateInventoryList();
      }
    }

    function editItem(index) {
      const item = inventory[index];
      const name = prompt("Producto:", item.name);
      const lot = prompt("Lote:", item.lot);
      const quantity = prompt("Cantidad:", item.quantity);
      const unit = prompt("Unidad (kg/litros):", item.unit);

      if (name && lot && quantity && unit) {
        inventory[index] = { ...item, name, lot, quantity, unit };
        saveInventory();
        updateInventoryList();
      }
    }
  </script>

</body>
</html>
