<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gestión de Inventario y Elaboraciones</title>
    <style>
        :root {
            --primary-color: #1e3d59;
            --secondary-color: #17a2b8;
            --accent-color: #00b4d8;
            --light-color: #e8f4f8;
            --dark-color: #2c3e50;
            --success-color: #28a745;
            --danger-color: #dc3545;
            --warning-color: #ffc107;
        }
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            background-color: var(--light-color);
        }
        .sidebar {
            width: 200px;
            background-color: var(--primary-color);
            color: #fff;
            padding: 15px;
            height: 100vh;
            position: fixed;
        }
        .sidebar button {
            width: 100%;
            padding: 12px;
            margin: 5px 0;
            background-color: var(--secondary-color);
            color: #fff;
            border: none;
            cursor: pointer;
            border-radius: 4px;
            transition: background-color 0.3s;
        }
        .sidebar button:hover {
            background-color: var(--accent-color);
        }
        .content {
            flex-grow: 1;
            padding: 20px;
            margin-left: 200px;
        }
        .section {
            display: none;
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: var(--primary-color);
        }
        select, input {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        .unidad-medida {
            color: var(--secondary-color);
            font-weight: bold;
            margin-top: 5px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            background-color: white;
        }
        table, th, td {
            border: 1px solid #ddd;
        }
        th, td {
            padding: 12px;
            text-align: left;
        }
        th {
            background-color: var(--light-color);
            color: var(--primary-color);
        }
        tr:nth-child(even) {
            background-color: var(--light-color);
        }
        .btn {
            padding: 10px 15px;
            background-color: var(--success-color);
            color: #fff;
            border: none;
            cursor: pointer;
            border-radius: 4px;
            transition: background-color 0.3s;
        }
        .btn:hover {
            background-color: #218838;
        }
        .btn-danger {
            background-color: var(--danger-color);
        }
        .btn-danger:hover {
            background-color: #c82333;
        }
        .alert {
            padding: 10px;
            margin-bottom: 15px;
            border-radius: 4px;
            display: none;
        }
        .alert-success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        .alert-error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        .alert-warning {
            background-color: #fff3cd;
            color: #856404;
            border: 1px solid #ffeeba;
        }
        .consumo-estimado {
            background-color: var(--light-color);
            padding: 10px;
            border-radius: 4px;
            margin-top: 5px;
        }
        .consumo-estimado ul {
            margin: 0;
            padding-left: 20px;
        }
        .consumo-estimado li {
            margin: 5px 0;
        }
        .stock-bajo {
            color: var(--danger-color);
            font-weight: bold;
        }
        .stock-medio {
            color: var(--warning-color);
            font-weight: bold;
        }
        .stock-alto {
            color: var(--success-color);
        }
    </style>
</head>
<body>
    <div class="sidebar">
        <button onclick="showSection('gestionInventario')">Gestión de Inventario</button>
        <button onclick="showSection('gestionElaboraciones')">Gestión de Elaboraciones</button>
    </div>
    <div class="content">
        <div id="alert" class="alert"></div>
        
        <!-- Sección de Gestión de Inventario -->
        <div id="gestionInventario" class="section">
            <h2>Gestión de Inventario</h2>
            <div class="form-group">
                <label for="producto">Producto:</label>
                <select id="producto" onchange="actualizarUnidadMedida()">
                    <option value="">Seleccione un producto</option>
                    <option value="SAL">SAL</option>
                    <option value="HIDRAL-70">HIDRAL-70</option>
                    <option value="HYDROMAR-4">HYDROMAR-4</option>
                    <option value="ANTIESPUMANTE">ANTIESPUMANTE</option>
                </select>
                <div id="unidadMedida" class="unidad-medida"></div>
            </div>
            <div class="form-group">
                <label for="lote">Lote:</label>
                <input type="text" id="lote" placeholder="Ingrese el número de lote">
            </div>
            <div class="form-group">
                <label for="cantidad">Cantidad:</label>
                <input type="number" id="cantidad" min="0" step="0.01" placeholder="Ingrese la cantidad">
            </div>
            <button class="btn" onclick="agregarProducto()">Agregar Producto</button>
            <h3>Historial de Inventario</h3>
            <table id="historialInventario">
                <thead>
                    <tr>
                        <th>Producto</th>
                        <th>Lote</th>
                        <th>Cantidad</th>
                        <th>Unidad</th>
                        <th>Estado</th>
                        <th>Fecha y Hora</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                </tbody>
            </table>
        </div>

        <!-- Sección de Gestión de Elaboraciones -->
        <div id="gestionElaboraciones" class="section">
            <h2>Gestión de Elaboraciones</h2>
            <div class="form-group">
                <label for="cliente">Cliente:</label>
                <select id="cliente">
                    <option value="">Seleccione un cliente</option>
                    <option value="WOFCO">WOFCO</option>
                    <option value="OVERSEA">OVERSEA</option>
                    <option value="SCANFISK">SCANFISK</option>
                    <option value="IBERCONSA">IBERCONSA</option>
                </select>
            </div>
            <div class="form-group">
                <label for="referencia">Referencia de Elaboración:</label>
                <input type="text" id="referencia" placeholder="Ingrese la referencia">
            </div>
            <div class="form-group">
                <label for="bins">Número de Bins:</label>
                <input type="number" id="bins" min="1" placeholder="Ingrese el número de bins">
            </div>
            <div class="form-group">
                <label>Consumo Estimado por <span id="binsValue">1</span> Bins:</label>
                <div id="consumoEstimado" class="consumo-estimado"></div>
            </div>
            <button class="btn" onclick="registrarElaboracion()">Registrar Elaboración</button>
            <h3>Historial de Elaboraciones</h3>
            <table id="historialElaboraciones">
                <thead>
                    <tr>
                        <th>Cliente</th>
                        <th>Referencia</th>
                        <th>Número de Bins</th>
                        <th>Consumo</th>
                        <th>Fecha y Hora</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                </tbody>
            </table>
        </div>
    </div>

    <script>
        // Configuración de productos y sus unidades de medida
        const productosConfig = {
            'SAL': 'KG',
            'HIDRAL-70': 'KG',
            'HYDROMAR-4': 'L',
            'ANTIESPUMANTE': 'L'
        };

        // Configuración de consumo por bin
        const consumoPorBin = {
            'SAL': 12.5,
            'HIDRAL-70': 6,
            'HYDROMAR-4': 1
        };

        // Umbrales de alerta de stock (en porcentaje)
        const UMBRAL_STOCK_BAJO = 20;
        const UMBRAL_STOCK_MEDIO = 50;

        // Función para actualizar la unidad de medida según el producto seleccionado
        function actualizarUnidadMedida() {
            const producto = document.getElementById('producto').value;
            const unidadMedida = document.getElementById('unidadMedida');
            if (producto && productosConfig[producto]) {
                unidadMedida.textContent = `Unidad de medida: ${productosConfig[producto]}`;
                unidadMedida.style.display = 'block';
            } else {
                unidadMedida.style.display = 'none';
            }
        }

        // Función para mostrar mensajes de alerta
        function showAlert(message, type) {
            const alert = document.getElementById('alert');
            alert.textContent = message;
            alert.className = `alert alert-${type}`;
            alert.style.display = 'block';
            setTimeout(() => {
                alert.style.display = 'none';
            }, 3000);
        }

        // Función para mostrar la sección seleccionada
        function showSection(sectionId) {
            const sections = document.querySelectorAll('.section');
            sections.forEach(section => {
                section.style.display = 'none';
            });
            document.getElementById(sectionId).style.display = 'block';
        }

        // Mostrar la sección de Gestión de Inventario por defecto
        showSection('gestionInventario');

        // Función para calcular el consumo estimado
        function calcularConsumoEstimado() {
            const bins = parseInt(document.getElementById('bins').value) || 1;
            const consumoEstimado = document.getElementById('consumoEstimado');
            document.getElementById('binsValue').textContent = bins;
            
            let html = '<ul>';
            for (const [producto, cantidad] of Object.entries(consumoPorBin)) {
                const total = cantidad * bins;
                html += `<li>${producto}: ${total} ${productosConfig[producto]}</li>`;
            }
            html += '</ul>';
            
            consumoEstimado.innerHTML = html;
        }

        // Agregar evento para actualizar consumo estimado
        document.getElementById('bins').addEventListener('input', calcularConsumoEstimado);

        // Función para verificar stock disponible
        function verificarStockDisponible(bins) {
            const inventario = JSON.parse(localStorage.getItem('inventario')) || [];
            const stockActual = {};
            
            // Calcular stock actual por producto
            inventario.forEach(item => {
                if (!stockActual[item.producto]) {
                    stockActual[item.producto] = 0;
                }
                stockActual[item.producto] += parseFloat(item.cantidad);
            });

            // Verificar si hay suficiente stock
            for (const [producto, cantidadPorBin] of Object.entries(consumoPorBin)) {
                const cantidadNecesaria = cantidadPorBin * bins;
                if (!stockActual[producto] || stockActual[producto] < cantidadNecesaria) {
                    return {
                        disponible: false,
                        producto: producto,
                        stockDisponible: stockActual[producto] || 0,
                        cantidadNecesaria: cantidadNecesaria
                    };
                }
            }
            return { disponible: true };
        }

        // Función para verificar stock bajo
        function verificarStockBajo() {
            const inventario = JSON.parse(localStorage.getItem('inventario')) || [];
            const alertas = [];

            // Agrupar por producto y lote
            const stockPorProducto = {};
            inventario.forEach(item => {
                if (!stockPorProducto[item.producto]) {
                    stockPorProducto[item.producto] = [];
                }
                stockPorProducto[item.producto].push({
                    lote: item.lote,
                    cantidad: parseFloat(item.cantidad),
                    unidad: item.unidad
                });
            });

            // Verificar cada producto
            for (const [producto, lotes] of Object.entries(stockPorProducto)) {
                // Ordenar lotes por fecha (los más antiguos primero)
                lotes.sort((a, b) => {
                    const itemA = inventario.find(i => i.producto === producto && i.lote === a.lote);
                    const itemB = inventario.find(i => i.producto === producto && i.lote === b.lote);
                    return new Date(itemA.fechaHora) - new Date(itemB.fechaHora);
                });

                // Verificar el lote más antiguo
                if (lotes.length > 0) {
                    const loteMasAntiguo = lotes[0];
                    const consumoPromedio = consumoPorBin[producto] * 5; // Estimado para 5 bins
                    const porcentajeRestante = (loteMasAntiguo.cantidad / consumoPromedio) * 100;

                    if (porcentajeRestante <= UMBRAL_STOCK_BAJO) {
                        alertas.push(`Stock bajo en ${producto} - Lote: ${loteMasAntiguo.lote} - Quedan ${loteMasAntiguo.cantidad} ${loteMasAntiguo.unidad}`);
                    }
                }
            }

            return alertas;
        }

        // Función para mostrar alertas de stock bajo
        function mostrarAlertasStockBajo() {
            const alertas = verificarStockBajo();
            if (alertas.length > 0) {
                const alert = document.getElementById('alert');
                alert.innerHTML = '<strong>Alertas de Stock Bajo:</strong><br>' + alertas.join('<br>');
                alert.className = 'alert alert-warning';
                alert.style.display = 'block';
            }
        }

        // Función para obtener el estado del stock
        function obtenerEstadoStock(cantidad, producto) {
            const consumoPromedio = consumoPorBin[producto] * 5;
            const porcentajeRestante = (cantidad / consumoPromedio) * 100;
            
            if (porcentajeRestante <= UMBRAL_STOCK_BAJO) {
                return { texto: 'Stock Bajo', clase: 'stock-bajo' };
            } else if (porcentajeRestante <= UMBRAL_STOCK_MEDIO) {
                return { texto: 'Stock Medio', clase: 'stock-medio' };
            } else {
                return { texto: 'Stock Alto', clase: 'stock-alto' };
            }
        }

        // Función para descontar del inventario
        function descontarDelInventario(bins, elaboracionId) {
            let inventario = JSON.parse(localStorage.getItem('inventario')) || [];
            const descuentos = [];

            // Verificar stock disponible antes de proceder
            const verificacionStock = verificarStockDisponible(bins);
            if (!verificacionStock.disponible) {
                showAlert(`No hay suficiente stock de ${verificacionStock.producto}. Se necesitan ${verificacionStock.cantidadNecesaria} ${productosConfig[verificacionStock.producto]}`, 'error');
                return null;
            }

            // Para cada producto requerido
            for (const [producto, cantidadPorBin] of Object.entries(consumoPorBin)) {
                let cantidadNecesaria = cantidadPorBin * bins;
                let inventarioRestante = [...inventario];

                // Ordenar lotes por fecha (los más antiguos primero)
                const lotesProducto = inventarioRestante
                    .filter(i => i.producto === producto)
                    .sort((a, b) => new Date(a.fechaHora) - new Date(b.fechaHora));

                // Descontar de los lotes más antiguos primero
                for (const lote of lotesProducto) {
                    if (cantidadNecesaria <= 0) break;

                    const itemIndex = inventarioRestante.findIndex(i => i.producto === producto && i.lote === lote.lote);
                    const cantidadDisponible = parseFloat(lote.cantidad);
                    const cantidadDescontar = Math.min(cantidadDisponible, cantidadNecesaria);
                    
                    // Actualizar cantidad en el inventario
                    lote.cantidad = (cantidadDisponible - cantidadDescontar).toString();
                    cantidadNecesaria -= cantidadDescontar;

                    // Registrar el descuento
                    descuentos.push({
                        producto: producto,
                        cantidad: cantidadDescontar,
                        lote: lote.lote,
                        fechaHora: new Date().toLocaleString()
                    });

                    // Si el item se agotó, eliminarlo del inventario
                    if (parseFloat(lote.cantidad) <= 0) {
                        inventarioRestante = inventarioRestante.filter(i => i !== lote);
                    }
                }

                // Actualizar el inventario principal con los cambios
                inventario = inventarioRestante;
            }

            // Actualizar inventario en localStorage
            localStorage.setItem('inventario', JSON.stringify(inventario));

            // Guardar historial de descuentos
            let historialDescuentos = JSON.parse(localStorage.getItem('historialDescuentos')) || {};
            historialDescuentos[elaboracionId] = descuentos;
            localStorage.setItem('historialDescuentos', JSON.stringify(historialDescuentos));

            // Verificar stock bajo después de la operación
            mostrarAlertasStockBajo();

            return descuentos;
        }

        // Función para agregar un producto al inventario
        function agregarProducto() {
            const producto = document.getElementById('producto').value;
            const lote = document.getElementById('lote').value.trim();
            const cantidad = document.getElementById('cantidad').value;

            if (!producto || !lote || !cantidad) {
                showAlert('Por favor, completa todos los campos.', 'error');
                return;
            }

            if (cantidad < 0) {
                showAlert('La cantidad no puede ser negativa.', 'error');
                return;
            }

            const fechaHora = new Date().toLocaleString();
            const item = { 
                producto, 
                lote,
                cantidad, 
                unidad: productosConfig[producto],
                fechaHora 
            };

            let inventario = JSON.parse(localStorage.getItem('inventario')) || [];
            inventario.push(item);
            localStorage.setItem('inventario', JSON.stringify(inventario));

            document.getElementById('producto').value = '';
            document.getElementById('lote').value = '';
            document.getElementById('cantidad').value = '';
            document.getElementById('unidadMedida').style.display = 'none';

            showAlert('Producto agregado exitosamente.', 'success');
            mostrarHistorialInventario();
        }

        // Función para mostrar el historial de inventario
        function mostrarHistorialInventario() {
            const historialInventario = document.getElementById('historialInventario').getElementsByTagName('tbody')[0];
            historialInventario.innerHTML = '';

            const inventario = JSON.parse(localStorage.getItem('inventario')) || [];

            // Ordenar por fecha (los más antiguos primero)
            inventario.sort((a, b) => new Date(a.fechaHora) - new Date(b.fechaHora));

            inventario.forEach((item, index) => {
                const row = historialInventario.insertRow();
                const cantidad = parseFloat(item.cantidad);
                const estado = obtenerEstadoStock(cantidad, item.producto);
                
                row.innerHTML = `
                    <td>${item.producto}</td>
                    <td>${item.lote}</td>
                    <td class="${estado.clase}">${item.cantidad}</td>
                    <td>${item.unidad}</td>
                    <td class="${estado.clase}">${estado.texto}</td>
                    <td>${item.fechaHora}</td>
                    <td>
                        <button class="btn btn-danger" onclick="eliminarItemInventario(${index})">Eliminar</button>
                    </td>
                `;
            });

            // Mostrar alertas de stock bajo
            mostrarAlertasStockBajo();
        }

        // Función para eliminar un item del inventario
        function eliminarItemInventario(index) {
            if (confirm('¿Está seguro de que desea eliminar este item?')) {
                let inventario = JSON.parse(localStorage.getItem('inventario')) || [];
                inventario.splice(index, 1);
                localStorage.setItem('inventario', JSON.stringify(inventario));
                mostrarHistorialInventario();
                showAlert('Item eliminado exitosamente.', 'success');
            }
        }

        // Función para registrar una elaboración
        function registrarElaboracion() {
            const cliente = document.getElementById('cliente').value;
            const referencia = document.getElementById('referencia').value.trim();
            const bins = parseInt(document.getElementById('bins').value);

            if (!cliente || !referencia || !bins) {
                showAlert('Por favor, completa todos los campos.', 'error');
                return;
            }

            if (bins < 1) {
                showAlert('El número de bins debe ser mayor a 0.', 'error');
                return;
            }

            const fechaHora = new Date().toLocaleString();
            const elaboracionId = Date.now().toString();

            // Descontar del inventario
            const descuentos = descontarDelInventario(bins, elaboracionId);
            if (!descuentos) {
                return; // Si hubo error en el descuento, no continuar
            }

            const elaboracion = { 
                id: elaboracionId,
                cliente, 
                referencia, 
                bins, 
                fechaHora,
                consumo: descuentos
            };

            let elaboraciones = JSON.parse(localStorage.getItem('elaboraciones')) || [];
            elaboraciones.push(elaboracion);
            localStorage.setItem('elaboraciones', JSON.stringify(elaboraciones));

            document.getElementById('cliente').value = '';
            document.getElementById('referencia').value = '';
            document.getElementById('bins').value = '';
            document.getElementById('consumoEstimado').innerHTML = '';
            document.getElementById('binsValue').textContent = '1';

            showAlert('Elaboración registrada exitosamente.', 'success');
            mostrarHistorialElaboraciones();
            mostrarHistorialInventario();
        }

        // Función para mostrar el historial de elaboraciones
        function mostrarHistorialElaboraciones() {
            const historialElaboraciones = document.getElementById('historialElaboraciones').getElementsByTagName('tbody')[0];
            historialElaboraciones.innerHTML = '';

            const elaboraciones = JSON.parse(localStorage.getItem('elaboraciones')) || [];
            const historialDescuentos = JSON.parse(localStorage.getItem('historialDescuentos')) || {};

            elaboraciones.forEach((elaboracion, index) => {
                const row = historialElaboraciones.insertRow();
                const consumo = historialDescuentos[elaboracion.id] || [];
                const consumoTexto = consumo.map(c => `${c.producto}: ${c.cantidad} ${productosConfig[c.producto]} (Lote: ${c.lote})`).join('<br>');
                
                row.innerHTML = `
                    <td>${elaboracion.cliente}</td>
                    <td>${elaboracion.referencia}</td>
                    <td>${elaboracion.bins}</td>
                    <td>${consumoTexto}</td>
                    <td>${elaboracion.fechaHora}</td>
                    <td>
                        <button class="btn btn-danger" onclick="eliminarElaboracion(${index})">Eliminar</button>
                    </td>
                `;
            });
        }

        // Función para eliminar una elaboración
        function eliminarElaboracion(index) {
            if (confirm('¿Está seguro de que desea eliminar esta elaboración? Esta acción no se puede deshacer.')) {
                let elaboraciones = JSON.parse(localStorage.getItem('elaboraciones')) || [];
                const elaboracionEliminada = elaboraciones[index];
                
                // Eliminar el historial de descuentos
                let historialDescuentos = JSON.parse(localStorage.getItem('historialDescuentos')) || {};
                delete historialDescuentos[elaboracionEliminada.id];
                localStorage.setItem('historialDescuentos', JSON.stringify(historialDescuentos));
                
                elaboraciones.splice(index, 1);
                localStorage.setItem('elaboraciones', JSON.stringify(elaboraciones));
                
                mostrarHistorialElaboraciones();
                showAlert('Elaboración eliminada exitosamente.', 'success');
            }
        }

        // Cargar historiales al iniciar la página
        window.onload = function() {
            mostrarHistorialInventario();
            mostrarHistorialElaboraciones();
            calcularConsumoEstimado();
            mostrarAlertasStockBajo();
        };
    </script>
</body>
</html>
