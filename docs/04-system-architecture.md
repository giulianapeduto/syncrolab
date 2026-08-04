# Doc 04 - Arquitectura del Sistema

**Proyecto:** SyncroLab  
**Versión:** 1.0  
**Estado:** En planificación  
**Última actualización:** Agosto 2026  

---

### 1. Introducción
Este documento define la arquitectura lógica y técnica de SyncroLab. 

El objetivo es establecer cómo se estructurarán las aplicaciones, cómo se comunicarán los distintos módulos entre sí y cuáles son los patrones de diseño que garantizarán un código robusto y mantenible a lo largo del tiempo.

La arquitectura de SyncroLab está diseñada para evitar el antipatrón del "Monolito fuertemente acoplado", priorizando un enfoque modular y orientado a servicios.

---

### 2. Objetivos de la Arquitectura
- **Desacoplamiento:** Separación estricta entre la interfaz de usuario (Frontend) y la lógica de negocio (Backend).
- **Escalabilidad:** Capacidad de soportar mayor volumen de transacciones y datos sin degradar el rendimiento.
- **Interoperabilidad:** Diseño **API-First** preparado desde el día cero para conectarse con servicios externos (AFIP, WhatsApp, E-commerce).
- **Mantenibilidad:** Código predecible, basado en estándares de la industria, que facilite la integración de nuevos desarrolladores.
- **Seguridad:** Protección de rutas mediante tokens de autorización y validación estricta de entrada de datos.

---

### 3. Modelo Arquitectónico General
SyncroLab implementará una **Arquitectura Cliente-Servidor** comunicada a través de una **API RESTful**. 

El sistema abandona el modelo tradicional de "vistas renderizadas en el servidor" en favor de aplicaciones independientes:
1. **Frontend (SPA - Single Page Application):** Aplicación cliente que corre en el navegador del usuario.
2. **Backend (API Server):** Servidor centralizado que contiene la lógica de negocio, reglas de autorización y acceso a datos.
3. **Base de Datos:** Almacenamiento persistente, que operará como la única fuente de verdad (SSOT).

---

### 4. Estructura del Backend (API RESTful)
El servidor backend adoptará una arquitectura de capas lógicas internas para separar las responsabilidades:

- **Capa de Controladores (Routing/Controllers):** Recibe las peticiones HTTP (GET, POST, PUT, DELETE), extrae los parámetros y devuelve las respuestas en formato JSON.
- **Capa de Servicios (Lógica de Negocio):** Contiene el "cerebro" de la aplicación. Aquí se ejecutan los cálculos, se validan las reglas de negocio (ej. *¿Hay suficiente stock para vender?*) y se orquestan múltiples modelos.
- **Capa de Acceso a Datos (Repositorios/Modelos):** Es la única capa autorizada para comunicarse e interactuar directamente con la Base de Datos.

---

### 5. Estructura del Frontend (Cliente)
La interfaz de usuario será responsable de:
- Renderizar vistas modulares y componentes reutilizables.
- Manejar el estado global del cliente (estado de la sesión, carritos de venta, caché de catálogos).
- Consumir de manera asíncrona los *endpoints* del Backend.
- Proveer *Feedback* inmediato al usuario (alertas de error, loaders, validaciones en el navegador).

La interfaz adoptará un enfoque *Responsive* y priorizará la velocidad operativa (atajos de teclado para puntos de venta, reducción de clics).

---

### 6. Módulos Internos (El Core)
En sintonía con el Documento de Requerimientos Funcionales, la arquitectura soportará los siguientes dominios como módulos aislados a nivel de lógica:

- Módulo de Autenticación y Usuarios (RBAC).
- Módulo de Entidades (Clientes / Proveedores).
- Módulo de Catálogo (Productos / Insumos).
- Módulo de Gestión Unificada de Stock.
- Módulo Transaccional (Ventas / Caja / Egresos).
- Módulo de Producción (Opcional).

---

### 7. Capa de Integraciones (Módulos Periféricos)
Para evitar que el *Core* se contamine con lógica de terceros, se diseñará una capa de integración mediante **Webhooks** y **Servicios Externos**.
Esto permitirá que el sistema, en el futuro, se comunique bidireccionalmente con:
- ARCA / AFIP (Facturación Electrónica mediante certificados y webservices fiscales).
- APIs de mensajería (WhatsApp Business API, Email transaccional).
- Plataformas E-commerce (Sincronización de stock y recepción de pedidos).
- Pasarelas de pago.

---

### 8. Principios de Diseño e Ingeniería de Software
Todo el código escrito para SyncroLab deberá someterse a los siguientes principios:

- **SSOT (Single Source of Truth):** Los datos críticos (ej. Stock) existirán en un solo lugar. Ningún módulo puede tener "su propia copia" de un dato central.
- **SoC (Separation of Concerns):** Cada función, clase o componente debe tener una única responsabilidad clara.
- **DRY (Don't Repeat Yourself):** Reutilización activa de componentes UI en el Frontend y funciones/servicios en el Backend. Evitar la duplicación de código.
- **KISS (Keep It Simple, Stupid):** Priorizar soluciones simples y legibles por encima de abstracciones complejas o sobre-ingeniería innecesaria.
- **Soft Deletion:** Nunca se ejecutan sentencias `DELETE` físicas sobre entidades transaccionales en la base de datos.

---

### Decisiones Arquitectónicas Tomadas

- Se adopta un modelo Cliente-Servidor (Frontend SPA + Backend API RESTful).
- La comunicación entre capas se realizará estrictamente en formato JSON.
- Toda acción transaccional pasará obligatoriamente por la capa de lógica de negocio del Backend (El Frontend nunca altera la Base de Datos de manera directa).
- Se establecen principios de código limpio (SoC, DRY, KISS) como estándar obligatorio para la revisión de código (Code Reviews).
