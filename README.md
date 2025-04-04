# gestion-mantenimiento-web
Mantenimientos LGM
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Gestión de Mantenimiento - La Gran Manzana</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Estilos adicionales para el mapa interactivo (opcional) */
        #mapa-container {
            width: 100%;
            height: 500px; /* Ajusta la altura según tus necesidades */
            border: 1px solid #e2e8f0;
            border-radius: 0.5rem;
            margin-bottom: 2rem;
        }
        /* Estilos para los marcadores del mapa (opcional) */
        .marcador {
            width: 10px;
            height: 10px;
            background-color: #f59e0b; /* Color de los marcadores (amarillo) */
            border-radius: 50%;
            cursor: pointer;
            display: inline-block;
            position: absolute;
        }
        .marcador:hover {
            background-color: #ed8936; /* Color al pasar el mouse (naranja más oscuro) */
        }
    </style>
</head>
<body class="bg-gray-100 font-inter antialiased">
    <div class="min-h-screen flex flex-col">
        <header class="bg-indigo-600 text-white py-4">
            <div class="container mx-auto px-4">
                <h1 class="text-2xl font-semibold">Sistema de Gestión de Mantenimiento - La Gran Manzana</h1>
            </div>
        </header>
        <main class="container mx-auto px-4 py-8 flex-grow">
            <h2 class="text-xl font-semibold mb-4 text-gray-800">Registro de Mantenimientos</h2>
            <div class="bg-white shadow-md rounded-lg p-6 mb-6">
                <form id="filtro-mantenimientos" class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-4">
                    <div>
                        <label for="filtro-tipo" class="block text-gray-700 text-sm font-bold mb-2">Tipo de Mantenimiento:</label>
                        <select id="filtro-tipo" class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                            <option value="">Todos</option>
                            <option value="local">Local</option>
                            <option value="vereda">Vereda</option>
                            <option value="puerta">Puerta Lanfor</option>
                            <option value="bomba">Bomba de Agua</option>
                        </select>
                    </div>
                    <div>
                        <label for="filtro-estado" class="block text-gray-700 text-sm font-bold mb-2">Estado:</label>
                        <select id="filtro-estado" class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                            <option value="">Todos</option>
                            <option value="pendiente">Pendiente</option>
                            <option value="en-proceso">En Proceso</option>
                            <option value="terminado">Terminado</option>
                            <option value="verificado">Verificado</option>
                        </select>
                    </div>
                    <div>
                        <label for="filtro-fecha-inicio" class="block text-gray-700 text-sm font-bold mb-2">Fecha Inicio:</label>
                        <input type="date" id="filtro-fecha-inicio" class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                    </div>
                    <div>
                        <label for="filtro-fecha-fin" class="block text-gray-700 text-sm font-bold mb-2">Fecha Fin:</label>
                        <input type="date" id="filtro-fecha-fin" class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                    </div>
                    <div class="md:col-span-4 flex justify-end mt-2">
                        <button id="btn-filtrar" type="button" class="bg-indigo-500 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                            Filtrar
                        </button>
                    </div>
                </form>
                <div class="overflow-x-auto">
                    <table id="tabla-mantenimientos" class="min-w-full leading-normal shadow-md rounded-lg overflow-hidden">
                        <thead class="bg-gray-200 text-gray-700">
                            <tr>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Número de Orden
                                </th>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Fecha Solicitud
                                </th>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Tipo
                                </th>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Local/Área
                                </th>
                                 <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Descripción
                                </th>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Estado
                                </th>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Responsable
                                </th>
                                <th class="px-5 py-3 border-b-2 border-gray-200 text-left text-xs font-semibold uppercase tracking-wider">
                                    Costo
                                </th>
                            </tr>
                        </thead>
                        <tbody class="bg-white">
                            <tr>
                                <td colspan="8" class="px-5 py-5 border-b border-gray-200 text-sm">
                                    <p class="text-gray-900">No hay mantenimientos registrados.</p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <div class="bg-white shadow-md rounded-lg p-6 mb-6">
                <h3 class="text-lg font-semibold mb-4 text-gray-800">Mapa de Mantenimientos</h3>
                <div id="mapa-container">
                    <p class="text-gray-500">Mapa interactivo de los locales y áreas del centro comercial. Los mantenimientos se mostrarán como marcadores.</p>
                </div>
            </div>
             <div class="bg-white shadow-md rounded-lg p-6">
                <h3 class="text-lg font-semibold mb-4 text-gray-800">Agregar Mantenimiento</h3>
                <form id="form-agregar-mantenimiento" class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div>
                        <label for="tipo" class="block text-gray-700 text-sm font-bold mb-2">Tipo de Mantenimiento:</label>
                        <select id="tipo" name="tipo" required class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                            <option value="">Seleccione...</option>
                            <option value="local">Local</option>
                            <option value="vereda">Vereda</option>
                            <option value="puerta">Puerta Lanfor</option>
                            <option value="bomba">Bomba de Agua</option>
                        </select>
                         <div id="tipo-error" class="text-red-500 text-xs italic" style="display: none;"></div>
                    </div>
                    <div>
                        <label for="local_area" class="block text-gray-700 text-sm font-bold mb-2">Local/Área:</label>
                        <input type="text" id="local_area" name="local_area" required class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                        <div id="local_area-error" class="text-red-500 text-xs italic" style="display: none;"></div>
                    </div>
                    <div>
                        <label for="descripcion" class="block text-gray-700 text-sm font-bold mb-2">Descripción del Trabajo:</label>
                        <textarea id="descripcion" name="descripcion" required class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"></textarea>
                        <div id="descripcion-error" class="text-red-500 text-xs italic" style="display: none;"></div>
                    </div>
                     <div>
                        <label for="responsable" class="block text-gray-700 text-sm font-bold mb-2">Responsable:</label>
                        <input type="text" id="responsable" name="responsable" required class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                        <div id="responsable-error" class="text-red-500 text-xs italic" style="display: none;"></div>
                    </div>
                    <div>
                        <label for="costo" class="block text-gray-700 text-sm font-bold mb-2">Costo:</label>
                        <input type="number" id="costo" name="costo" required class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                         <div id="costo-error" class="text-red-500 text-xs italic" style="display: none;"></div>
                    </div>
                    <div>
                        <label for="fecha_solicitud" class="block text-gray-700 text-sm font-bold mb-2">Fecha de Solicitud:</label>
                        <input type="date" id="fecha_solicitud" name="fecha_solicitud" required class="shadow appearance-none border rounded w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                        <div id="fecha_solicitud-error" class="text-red-500 text-xs italic" style="display: none;"></div>
                    </div>
                    <div class="md:col-span-3 flex justify-end mt-6">
                        <button id="btn-agregar-mantenimiento" type="submit" class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                            Agregar Mantenimiento
                        </button>
                    </div>
                </form>
            </div>
        </main>
        <footer class="bg-gray-800 text-white py-4">
            <div class="container mx-auto px-4 text-center">
                <p>© 2025 Sistema de Gestión de Mantenimiento - La Gran Manzana. Todos los derechos reservados.</p>
            </div>
        </footer>
    </div>
    <script>
    let mantenimientos = [
        {
            numero_orden: "M001",
            fecha_solicitud: "2024-01-15",
            tipo: "local",
            local_area: "Local 101",
            descripcion: "Reparación de puerta",
            estado: "pendiente",
            responsable: "Juan Pérez",
            costo: 150.00,
        },
        {
            numero_orden: "M002",
            fecha_solicitud: "2024-01-20",
            tipo: "vereda",
            local_area: "Vereda Norte",
            descripcion: "Pegar piedras sueltas",
            estado: "en-proceso",
            responsable: "Pedro Gómez",
            costo: 200.00,
        },
        {
            numero_orden: "M003",
            fecha_solicitud: "2024-02-01",
            tipo: "puerta",
            local_area: "Puerta Lanfor 5",
            descripcion: "Pintura",
            estado: "terminado",
            responsable: "María Rodríguez",
            costo: 100.00,
        },
         {
            numero_orden: "M004",
            fecha_solicitud: "2024-02-05",
            tipo: "bomba",
            local_area: "Bomba Agua 1",
            descripcion: "Limpieza",
            estado: "terminado",
            responsable: "Carlos López",
            costo: 50.00,
        },
        {
            numero_orden: "M005",
            fecha_solicitud: "2024-02-10",
            tipo: "local",
            local_area: "Local 205",
            descripcion: "Reparación de baño",
            estado: "pendiente",
            responsable: "Ana Martínez",
            costo: 300.00,
        },
    ];
function mostrarMantenimientos(mantenimientos) {
    const tablaMantenimientos = document.querySelector("#tabla-mantenimientos tbody");
    tablaMantenimientos.innerHTML = "";
    if (mantenimientos.length === 0) {
        tablaMantenimientos.innerHTML = `
            <tr>
                <td colspan="8" class="px-5 py-5 border-b border-gray-200 text-sm">
                    <p class="text-gray-900">No hay mantenimientos registrados.</p>
                </td>
            </tr>
        `;
        return;
    }
    mantenimientos.forEach((mantenimiento) => {
        const row = document.createElement("tr");
        row.innerHTML = `
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">${mantenimiento.numero_orden}</p></td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">${mantenimiento.fecha_solicitud}</p></td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">${mantenimiento.tipo === 'local' ? 'Local' : mantenimiento.tipo === 'vereda' ? 'Vereda' : mantenimiento.tipo === 'puerta' ? 'Puerta Lanfor' : 'Bomba de Agua'}</p></td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">${mantenimiento.local_area}</p></td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">${mantenimiento.descripcion}</p></td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm">
                <span class="${mantenimiento.estado === 'pendiente' ? 'bg-yellow-100 text-yellow-800' : mantenimiento.estado === 'en-proceso' ? 'bg-blue-100 text-blue-800' : mantenimiento.estado === 'terminado' ? 'bg-green-100 text-green-800' : 'bg-gray-100 text-gray-800'} text-xs font-semibold px-2 py-1 rounded-full">${mantenimiento.estado}</span>
            </td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">${mantenimiento.responsable}</p></td>
            <td class="px-5 py-5 border-b border-gray-200 text-sm"><p class="text-gray-900">$${mantenimiento.costo.toFixed(2)}</p></td>
        `;
        tablaMantenimientos.appendChild(row);
    });
}
function filtrarMantenimientos() {
    const tipo = document.getElementById("filtro-tipo").value;
    const estado = document.getElementById("filtro-estado").value;
    const fechaInicio = document.getElementById("filtro-fecha-inicio").value;
    const fechaFin = document.getElementById("filtro-fecha-fin").value;
    let mantenimientosFiltrados = [...mantenimientos];
    if (tipo) {
        mantenimientosFiltrados = mantenimientosFiltrados.filter((mantenimiento) => mantenimiento.tipo === tipo);
    }
    if (estado) {
        mantenimientosFiltrados = mantenimientosFiltrados.filter((mantenimiento) => mantenimiento.estado === estado);
    }
    if (fechaInicio) {
        mantenimientosFiltrados = mantenimientosFiltrados.filter((mantenimiento) => new Date(mantenimiento.fecha_solicitud) >= new Date(fechaInicio));
    }
    if (fechaFin) {
        mantenimientosFiltrados = mantenimientosFiltrados.filter((mantenimiento) => new Date(mantenimiento.fecha_solicitud) <= new Date(fechaFin));
    }
    mostrarMantenimientos(mantenimientosFiltrados);
}
document.getElementById("btn-filtrar").addEventListener("click", filtrarMantenimientos);
mostrarMantenimientos(mantenimientos);

const formAgregarMantenimiento = document.getElementById('form-agregar-mantenimiento');
const tipoInput = document.getElementById('tipo');
const localAreaInput = document.getElementById('local_area');
const descripcionInput = document.getElementById('descripcion');
const responsableInput = document.getElementById('responsable');
const costoInput = document.getElementById('costo');
const fechaSolicitudInput = document.getElementById('fecha_solicitud');

formAgregarMantenimiento.addEventListener('submit', (event) => {
    event.preventDefault();

    let hasErrors = false;

    if (!tipoInput.value) {
        document.getElementById('tipo-error').textContent = 'Por favor, seleccione el tipo de mantenimiento.';
        document.getElementById('tipo-error').style.display = 'block';
        hasErrors = true;
    } else {
        document.getElementById('tipo-error').style.display = 'none';
    }
    if (!localAreaInput.value) {
        document.getElementById('local_area-error').textContent = 'Por favor, ingrese el local o área.';
        document.getElementById('local_area-error').style.display = 'block';
        hasErrors = true;
    } else {
        document.getElementById('local_area-error').style.display = 'none';
    }
    if (!descripcionInput.value) {
        document.getElementById('descripcion-error').textContent = 'Por favor, ingrese la descripción del trabajo.';
        document.getElementById('descripcion-error').style.display = 'block';
        hasErrors = true;
    } else {
        document.getElementById('descripcion-error').style.display = 'none';
    }
     if (!responsableInput.value) {
        document.getElementById('responsable-error').textContent = 'Por favor, ingrese el responsable del trabajo.';
        document.getElementById('responsable-error').style.display = 'block';
        hasErrors = true;
    } else {
        document.getElementById('responsable-error').style.display = 'none';
    }

    if (!costoInput.value) {
        document.getElementById('costo-error').textContent = 'Por favor, ingrese el costo del trabajo.';
        document.getElementById('costo-error').style.display = 'block';
        hasErrors = true;
    } else if (isNaN(costoInput.value) || Number(costoInput.value) <= 0) {
        document.getElementById('costo-error').textContent = 'Por favor, ingrese un costo válido (número mayor que cero).';
        document.getElementById('costo-error').style.display = 'block';
        hasErrors = true;
    } else {
        document.getElementById('costo-error').style.display = 'none';
    }

    if (!fechaSolicitudInput.value) {
        document.getElementById('fecha_solicitud-error').textContent = 'Por favor, ingrese la fecha de solicitud.';
        document.getElementById('fecha_solicitud-error').style.display = 'block';
        hasErrors = true;
    } else {
        document.getElementById('fecha_solicitud-error').style.display = 'none';
    }

    if (hasErrors) {
        return;
    }

    const nuevoMantenimiento = {
        numero_orden: `M${String(mantenimientos.length + 1).padStart(3, '0')}`,
        fecha_solicitud: fechaSolicitudInput.value,
        tipo: tipoInput.value,
        local_area: localAreaInput.value,
        descripcion: descripcionInput.value,
        estado: "pendiente",
        responsable: responsableInput.value,
        costo: Number(costoInput.value),
    };

    mantenimientos.push(nuevoMantenimiento);
    mostrarMantenimientos(mantenimientos);
    formAgregarMantenimiento.reset();

    Swal.fire({
        title: 'Mantenimiento Agregado',
        text: 'El mantenimiento se ha agregado correctamente.',
        icon: 'success',
        confirmButtonText: 'OK'
    });
});
    </script>
</body>
</html>
