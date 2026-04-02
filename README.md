# 🛍️ Asistente Virtual de Ventas — BoutiqueLuna

Bot de ventas conversacional integrado con HubSpot, construido en n8n, con memoria persistente en Airtable, modelo de lenguaje Llama 3.3 vía Groq, e inventario en Google Sheets.

---

## 📋 Tabla de Contenidos

- [Descripción General](#descripción-general)
- [Tecnologías Utilizadas](#tecnologías-utilizadas)
- [Arquitectura del Flujo](#arquitectura-del-flujo)
- [Estructura de Airtable](#estructura-de-airtable)
- [Configuración Paso a Paso](#configuración-paso-a-paso)
- [Nodos del Flujo](#nodos-del-flujo)
- [Pantallazos](#pantallazos)

---

## 📌 Descripción General

**Luna** es una asistente virtual de ventas para BoutiqueLuna, una tienda de ropa y accesorios para dama. Atiende a las clientas a través del chat de HubSpot, recuerda el contexto de la conversación, ofrece productos del inventario en tiempo real, detecta el nombre del cliente y cierra ventas derivando al WhatsApp del negocio.

### ¿Qué hace Luna?

- Saluda y pregunta el nombre de la clienta en el primer mensaje
- Recuerda el nombre y el contexto durante toda la conversación
- Consulta el inventario en tiempo real desde Google Sheets
- Solo ofrece productos disponibles
- Cierra ventas dando el número de WhatsApp para coordinar el pedido
- Registra métricas de productos consultados en Airtable

---

## 🛠️ Tecnologías Utilizadas

| Herramienta | Uso |
|-------------|-----|
| **n8n** | Orquestador del flujo de automatización |
| **HubSpot** | Canal de chat con el cliente (Conversations) |
| **Groq + Llama 3.3 70B** | Modelo de lenguaje para generar respuestas |
| **Airtable** | Base de datos para hilos, mensajes y estadísticas |
| **Google Sheets** | Inventario de productos |

---

## 🗺️ Flujo Completo en n8n

> 📸 **Pantallazo — Vista completa del flujo en n8n:**
>
> `[INSERTAR IMAGEN DEL FLUJO COMPLETO AQUÍ]`

---

## 🏗️ Arquitectura del Flujo

```
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
```

---

## 🗃️ Estructura de Airtable

### Tabla: Hilos
Registra cada conversación iniciada.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| HubSpot Thread ID | Texto | ID del hilo en HubSpot |
| Fecha Creación | Fecha | Fecha de inicio de la conversación |
| Nombre Cliente | Texto | Nombre detectado de la clienta |

---

### Tabla: Mensajes *(oculta)*
Almacena el historial completo de la conversación para mantener el contexto.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Thread ID | Texto | Referencia al hilo de Airtable |
| Rol | Texto | `user` o `assistant` |
| Nombre Cliente | Texto | Nombre de quien envió el mensaje |
| Fecha | Fecha | Timestamp del mensaje |
| Contenido | Texto | Texto del mensaje |

---

### Tabla: Estadísticas
Registra qué productos fueron consultados por los clientes (sin duplicados por hilo).

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Thread ID | Texto | Referencia al hilo |
| Producto Consultado | Texto | Nombre del producto mencionado |
| Fecha | Fecha | Fecha de la consulta |

---


---

## 📊 Gráfico de Estadísticas

Visualización de los productos más consultados por los clientes durante las conversaciones. Permite identificar qué productos generan más interés y optimizar el inventario y las estrategias de venta.

> 📸 **Gráfico — Productos más consultados:**
>
> `[INSERTAR GRÁFICO DE BARRAS / PIE CHART DE ESTADÍSTICAS AQUÍ]`

> 📸 **Pantallazo — Tabla Estadísticas en Airtable:**
>
> `[INSERTAR IMAGEN DE LA TABLA ESTADÍSTICAS AQUÍ]`

**Métricas que puedes extraer:**
- Producto más consultado del mes
- Productos con mayor intención de compra
- Horarios de mayor actividad
- Tasa de conversión por producto

## ⚙️ Configuración Paso a Paso

### 1. HubSpot
- Crear una cuenta de desarrollador en HubSpot
- Configurar un canal de chat en **Conversations**
- Crear una suscripción webhook para el evento `conversation.newMessage`
- Apuntar el webhook a la URL del trigger de n8n
- Generar una API Key con permisos de Conversations

### 2. Groq
- Crear cuenta en [console.groq.com](https://console.groq.com)
- Generar una API Key
- En n8n, crear credencial de tipo **HTTP Header Auth**:
  - **Name:** `Authorization`
  - **Value:** `Bearer TU_API_KEY`

### 3. Airtable
- Crear una base con las tres tablas descritas arriba
- Generar un Personal Access Token con permisos de lectura y escritura
- Configurar la credencial en n8n

### 4. Google Sheets
- Crear una hoja con las columnas: `PRODUCTO`, `PRECIO`, `DESCRIPCION`, `DISPONIBLE`
- Llenar con el inventario de la tienda
- Conectar con OAuth2 en n8n

### 5. n8n
- Importar el archivo `Asistente_Virtual_Ventas.json`
- Configurar todas las credenciales en los nodos correspondientes
- Activar el flujo

---

## 📸 Pantallazos del Proyecto

### 1. Flujo Completo en n8n
> ![Flujo Completo](images/01_flujo_completo.png)

---

### 2. Conversación en HubSpot — Parte 1
> ![Conversación HubSpot 1](images/02_conversacion_hubspot_1.png)

---

### 3. Conversación en HubSpot — Parte 2
> ![Conversación HubSpot 2](images/03_conversacion_hubspot_2.png)

---

### 4. Conversación en HubSpot — Parte 3
> ![Conversación HubSpot 3](images/04_conversacion_hubspot_3.png)

---

### 5. Tabla Hilos — Airtable
> ![Tabla Hilos](images/05_tabla_hilos.png)

---

### 6. Tabla Mensajes — Airtable
> ![Tabla Mensajes](images/06_tabla_mensajes.png)

---

### 7. Tabla Estadísticas — Airtable
> ![Tabla Estadísticas](images/07_tabla_estadisticas.png)

---

### 8. Gráfico de Productos Consultados
> ![Gráfico Estadísticas](images/08_grafico_estadisticas.png)

---

### 9. Inventario en Google Sheets
> ![Inventario Google Sheets](images/09_inventario_google_sheets.png)

---

## 📁 Archivos del Proyecto

```
📦 Asistente Virtual Ventas
 ┣ 📄 Asistente_Virtual_Ventas.json   ← Flujo de n8n listo para importar
 ┗ 📄 README.md                        ← Este archivo
```

---

## 👩‍💻 Autor

Proyecto desarrollado como parte de portafolio personal de automatizaciones con IA.
