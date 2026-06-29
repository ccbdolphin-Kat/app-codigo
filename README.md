# app-codigo
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Código de Ética INE</title>
    <link rel="manifest" href="manifest.json">
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 15px; background: #f4f4f9; color: #333; }
        .header { background: #d81b60; color: white; padding: 15px; text-align: center; border-radius: 8px; margin-bottom: 15px; }
        .search-box { width: 100%; padding: 12px; font-size: 16px; border: 1px solid #ccc; border-radius: 25px; box-sizing: border-box; margin-bottom: 20px; outline: none; }
        .card { background: white; padding: 15px; border-radius: 8px; margin-bottom: 12px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .card h3 { color: #d81b60; margin-top: 0; font-size: 18px; }
        .card p { font-size: 14px; line-height: 1.5; color: #555; }
        .badge { background: #eee; padding: 3px 8px; border-radius: 4px; font-size: 11px; font-weight: bold; }
    </style>
</head>
<body>

    <div class="header">
        <h2>Código de Ética INE</h2>
        <p>Consulta Offline OIC-INE/04/2026</p>
    </div>

    <input type="text" id="buscador" class="search-box" placeholder="🔍 Buscar artículo, principio o regla...">

    <div id="contenido"></div>

    <script src="app.js"></script>
</body>
</html>
// Base de datos local integrada directamente (sin requerir internet)
const documentoIne = [
    { titulo: "Artículo 1. Ámbito de aplicación", categoria: "Disposiciones Generales", texto: "Las disposiciones del presente Código son aplicables a todas las personas servidoras públicas que desempeñen un empleo, cargo o comisión en el Instituto..." },
    { titulo: "Artículo 3. Glosario - Inteligencia Artificial", categoria: "Glosario", texto: "Disciplina tecnológica que desarrolla modelos, métodos y sistemas informáticos capaces de imitar, ampliar o automatizar funciones de la inteligencia humana..." },
    { titulo: "Artículo 3. Glosario - Conflicto de Interés", categoria: "Glosario", texto: "La posible afectación del desempeño imparcial y objetivo de las funciones de las personas servidoras públicas en razón de intereses personales, familiares o de negocios." },
    { titulo: "Artículo 4. Principio de Certeza", categoria: "Principios", texto: "Los actos y acciones de las personas servidoras públicas deberán ser previsibles y sustentarse en información veraz y comprobable, a fin de que los resultados de sus actividades sean verificables..." },
    { titulo: "Artículo 4. Principio de Legalidad", categoria: "Principios", texto: "Las personas servidoras públicas deberán actuar con estricto apego al marco constitucional y legal, ejercer sus atribuciones únicamente en los términos que la ley les confiere..." },
    { titulo: "Artículo 6. Fracción XIX. Uso ético de la IA", categoria: "Reglas de Integridad", texto: "Asegurar supervisión humana en las decisiones relevantes. Verificar la calidad, pertinencia y licitud de los datos utilizados... No incorporar información reservada en herramientas externas." }
];

const buscador = document.getElementById('buscador');
const contenedor = document.getElementById('contenido');

// Función para renderizar tarjetas en pantalla
function mostrarDatos(datos) {
    contenedor.innerHTML = '';
    if(datos.length === 0) {
        contenedor.innerHTML = '<p style="text-align:center; color:#999;">No se encontraron resultados.</p>';
        return;
    }
    datos.forEach(item => {
        contenedor.innerHTML += `
            <div class="card">
                <span class="badge">${item.categoria}</span>
                <h3>${item.titulo}</h3>
                <p>${item.texto}</p>
            </div>
        `;
    });
}

// Escuchar lo que escribe el usuario para filtrar en tiempo real
buscador.addEventListener('input', (e) => {
    const termino = e.target.value.toLowerCase();
    const filtrados = documentoIne.filter(item => 
        item.titulo.toLowerCase().includes(termino) || 
        item.texto.toLowerCase().includes(termino) ||
        item.categoria.toLowerCase().includes(termino)
    );
    mostrarDatos(filtrados);
});

// Carga inicial de la app
mostrarDatos(documentoIne);

// Registro Obligatorio del Service Worker para funcionamiento Sin Internet
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('sw.js')
            .then(() => console.log('App lista para usarse sin internet.'))
            .catch(err => console.log('Error de registro offline:', err));
    });
}
const CACHE_NAME = 'ine-ethics-v1';
const ASSETS = [
    './',
    './index.html',
    './app.js',
    './manifest.json'
];

// Guardar archivos esenciales en el celular al instalar por primera vez
self.addEventListener('install', e => {
    e.waitUntil(
        caches.open(CACHE_NAME).then(cache => {
            return cache.addAll(ASSETS);
        })
    );
});

// Interceptar peticiones y responder desde la memoria interna sin usar internet
self.addEventListener('fetch', e => {
    e.respondWith(
        caches.match(e.request).then(response => {
            return response || fetch(e.request);
        })
    );
});
{
  "name": "Código de Ética INE Offline",
  "short_name": "INE Ética",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#f4f4f9",
  "theme_color": "#d81b60",
  "orientation": "portrait"
}
