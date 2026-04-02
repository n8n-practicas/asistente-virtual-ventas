🛍️ Asistente Virtual de Ventas — BoutiqueLuna
Bot de ventas conversacional integrado con HubSpot, construido en n8n, con memoria persistente en Airtable, modelo de lenguaje Llama 3.3 vía Groq, e inventario en Google Sheets.

📋 Tabla de Contenidos

Descripción General
Tecnologías Utilizadas
Arquitectura del Flujo
Estructura de Airtable
Configuración Paso a Paso
Nodos del Flujo
Pantallazos


📌 Descripción General
Luna es una asistente virtual de ventas para BoutiqueLuna, una tienda de ropa y accesorios para dama. Atiende a las clientas a través del chat de HubSpot, recuerda el contexto de la conversación, ofrece productos del inventario en tiempo real, detecta el nombre del cliente y cierra ventas derivando al WhatsApp del negocio.
¿Qué hace Luna?

Saluda y pregunta el nombre de la clienta en el primer mensaje
Recuerda el nombre y el contexto durante toda la conversación
Consulta el inventario en tiempo real desde Google Sheets
Solo ofrece productos disponibles
Cierra ventas dando el número de WhatsApp para coordinar el pedido
Registra métricas de productos consultados en Airtable


🛠️ Tecnologías Utilizadas
HerramientaUson8nOrquestador del flujo de automatizaciónHubSpotCanal de chat con el cliente (Conversations)Groq + Llama 3.3 70BModelo de lenguaje para generar respuestasAirtableBase de datos para hilos, mensajes y estadísticasGoogle SheetsInventario de productos

🗺️ Flujo Completo en n8n

📸 Pantallazo — Vista completa del flujo en n8n:
[INSERTAR IMAGEN DEL FLUJO COMPLETO AQUÍ]


🏗️ Arquitectura del Flujo
HubSpot Webhook
      ↓
Filtrar Mensaje de Usuario
      ↓
Obtener Mensaje de HubSpot (API)
      ↓
¿Existe el Hilo? (filtro por senderActorId y direction)
      ↓
Airtable - Buscar Hilo
      ↓
¿Hilo Encontrado?
    ↓ SÍ                    ↓ NO
    |                  Airtable - Crear Hilo
    |                       ↓
Airtable - Guardar Mensaje de Usuario
      ↓
Airtable - Obtener Historial
      ↓
Airtable - Obtener Datos del Hilo
      ↓
Google Sheets - Obtener Inventario
      ↓
Código - Construir Mensajes (system prompt + historial)
      ↓
Groq - Chat con Llama 3.3
      ↓
Código - Extraer Respuesta
      ↓
¿Tiene Nombre?
    ↓ SÍ                    ↓ NO
Airtable - Actualizar    Airtable - Guardar
Nombre Cliente           Respuesta del Asistente
      ↓                       ↓
Airtable - Guardar Respuesta del Asistente
      ↓
Responder Mensaje en HubSpot
      ↓
Detectar Productos Consultados
      ↓
¿Hay Productos?
      ↓ SÍ
Airtable - Buscar Estadística Existente
      ↓
¿Estadística Ya Existe?
      ↓ NO
Airtable - Guardar Estadística

🗃️ Estructura de Airtable
Tabla: Hilos
Registra cada conversación iniciada.
CampoTipoDescripciónHubSpot Thread IDTextoID del hilo en HubSpotFecha CreaciónFechaFecha de inicio de la conversaciónNombre ClienteTextoNombre detectado de la clienta

Tabla: Mensajes (oculta)
Almacena el historial completo de la conversación para mantener el contexto.
CampoTipoDescripciónThread IDTextoReferencia al hilo de AirtableRolTextouser o assistantNombre ClienteTextoNombre de quien envió el mensajeFechaFechaTimestamp del mensajeContenidoTextoTexto del mensaje

Tabla: Estadísticas
Registra qué productos fueron consultados por los clientes (sin duplicados por hilo).
CampoTipoDescripciónThread IDTextoReferencia al hiloProducto ConsultadoTextoNombre del producto mencionadoFechaFechaFecha de la consulta


📊 Gráfico de Estadísticas
Visualización de los productos más consultados por los clientes durante las conversaciones. Permite identificar qué productos generan más interés y optimizar el inventario y las estrategias de venta.

📸 Gráfico — Productos más consultados:
[INSERTAR GRÁFICO DE BARRAS / PIE CHART DE ESTADÍSTICAS AQUÍ]


📸 Pantallazo — Tabla Estadísticas en Airtable:
[INSERTAR IMAGEN DE LA TABLA ESTADÍSTICAS AQUÍ]

Métricas que puedes extraer:

Producto más consultado del mes
Productos con mayor intención de compra
Horarios de mayor actividad
Tasa de conversión por producto

⚙️ Configuración Paso a Paso
1. HubSpot

Crear una cuenta de desarrollador en HubSpot
Configurar un canal de chat en Conversations
Crear una suscripción webhook para el evento conversation.newMessage
Apuntar el webhook a la URL del trigger de n8n
Generar una API Key con permisos de Conversations

2. Groq

Crear cuenta en console.groq.com
Generar una API Key
En n8n, crear credencial de tipo HTTP Header Auth:

Name: Authorization
Value: Bearer TU_API_KEY



3. Airtable

Crear una base con las tres tablas descritas arriba
Generar un Personal Access Token con permisos de lectura y escritura
Configurar la credencial en n8n

4. Google Sheets

Crear una hoja con las columnas: PRODUCTO, PRECIO, DESCRIPCION, DISPONIBLE
Llenar con el inventario de la tienda
Conectar con OAuth2 en n8n

5. n8n

Importar el archivo Asistente_Virtual_Ventas.json
Configurar todas las credenciales en los nodos correspondientes
Activar el flujo


📸 Pantallazos del Proyecto
1. Flujo Completo en n8n

[INSERTAR IMAGEN — 01_flujo_completo]


2. Conversación en HubSpot — Parte 1

[INSERTAR IMAGEN — 02_conversacion_hubspot_1]


3. Conversación en HubSpot — Parte 2

[INSERTAR IMAGEN — 03_conversacion_hubspot_2]


4. Conversación en HubSpot — Parte 3

[INSERTAR IMAGEN — 04_conversacion_hubspot_3]


5. Tabla Hilos — Airtable

[INSERTAR IMAGEN — 05_tabla_hilos]


6. Tabla Mensajes — Airtable

[INSERTAR IMAGEN — 06_tabla_mensajes]


7. Tabla Estadísticas — Airtable

[INSERTAR IMAGEN — 07_tabla_estadisticas]


8. Gráfico de Productos Consultados

[INSERTAR IMAGEN — 08_grafico_estadisticas]


9. Inventario en Google Sheets

[INSERTAR IMAGEN — 09_inventario_google_sheets]


📁 Archivos del Proyecto
📦 Asistente Virtual Ventas
 ┣ 📄 Asistente_Virtual_Ventas.json   ← Flujo de n8n listo para importar
 ┗ 📄 README.md                        ← Este archivo

👩‍💻 Autor
Proyecto desarrollado como parte de portafolio personal de automatizaciones con IA.
