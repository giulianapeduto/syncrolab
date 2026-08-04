# Doc 02 - Business Analysis

**Proyecto:** SyncroLab  
**Caso de estudio (PoC):** Cronkie  
**Versión:** 1.0  
**Estado:** En análisis  
**Última actualización:** Agosto 2026  

---

### 1. Introducción
Este documento tiene como objetivo comprender la lógica operativa de nuestro caso de estudio (Cronkie) antes de iniciar el diseño del modelo de datos.

El propósito del análisis funcional es identificar el flujo de trabajo diario, qué entidades de información interactúan entre sí y cuáles son los cuellos de botella actuales. Este relevamiento físico es el paso previo indispensable para definir los requerimientos funcionales y la arquitectura de los módulos de SyncroLab.

---

### 2. Descripción del Negocio
Cronkie es una empresa del rubro gastronómico (cafetería y pastelería artesanal) dedicada a la elaboración y comercialización de productos finales.

Además de ofrecer productos para consumo inmediato, el negocio cuenta con procesos de manufactura interna, elaborando subproductos utilizados posteriormente en la cadena de producción. 

Operativamente, el negocio requiere orquestar múltiples dominios de información en tiempo real: flujo de caja, control de inventario (stock), gestión de proveedores, trazabilidad de pedidos y omnicanalidad con los clientes.

---

### 3. Entidades Comerciales Identificadas
Para trasladar la lógica del negocio al sistema, categorizamos los elementos físicos en dos grandes entidades abstractas:

**A. Productos (Bienes para comercialización final)**
- *Ejemplos de reventa/elaboración:* Cookies, Brownies, Alfajores, Tartas, Medialunas, Tortas.

**B. Insumos (Recursos consumibles de la empresa)**
El sistema no diferenciará estructuralmente entre "materias primas" y "herramientas", todo será tratado como un Insumo categorizado, permitiendo un control de stock unificado.
- *Categoría Ingredientes/Subproductos:* Mantequilla de maní, Caramelo, Leches vegetales (elaboración propia), Harina, Azúcar.
- *Categoría Empaque (Packaging):* Cajas, bolsas, stickers.
- *Categoría Operativa:* Utensilios, artículos de limpieza.

---

### 4. Actores del Negocio
Se identifican los siguientes roles que interactuarán directa o indirectamente con la plataforma:

- **Dueño / Administrador:** Posee acceso total. Necesita visualizar métricas, rentabilidad, reportes y control de mermas.
- **Operador / Empleado:** Interactúa con el sistema en la capa transaccional (punto de venta, registro de elaboración, apertura y cierre de caja).
- **Cliente:** Actor pasivo en el Core, pero activo en las integraciones externas (tienda digital, WhatsApp).
- **Proveedor:** Entidad externa a la que se le asocian órdenes de compra, cuentas por pagar y reposición de insumos.

---

### 5. Mapeo de Procesos (Físico a Lógico)
Las operaciones diarias del local se traducirán en eventos del sistema:

**A. Apertura de Operaciones**
- *Físico:* Abrir local, encender equipos, contar dinero inicial.
- *Lógico:* Apertura de Sesión de Caja (Módulo de Facturación), sincronización de stock inicial.

**B. Operación Transaccional**
- *Físico:* Recepción de mercadería, elaboración, atención, cobro.
- *Lógico:* Movimientos de Stock (ingreso/egreso), generación de Tickets/Facturas, actualización del catálogo en tiempo real, cambio de estados en el módulo de Pedidos.

**C. Cierre y Arqueo**
- *Físico:* Recuento de dinero, revisión de stock, limpieza.
- *Lógico:* Cierre de Sesión de Caja (Arqueo Z), generación de reportes diarios, alertas de reposición (Stock mínimo).

---

### 6. Dominios de Información a Controlar
El análisis detecta que el sistema debe gestionar, como mínimo, los siguientes dominios:

- **Inventario (Stock):** Trazabilidad de unidades, rotación, alertas de quiebre de stock, gestión de mermas.
- **Transacciones (Ventas/Gastos):** Flujo de caja, métodos de pago, categorización de egresos (servicios, pago a proveedores, insumos).
- **Entidades Externas:** Fichas de proveedores (historial de precios y compras) y bases de datos de clientes (fidelización).
- **Operaciones en curso:** Gestión de estados de pedidos (Pendiente, En preparación, Listo, Entregado).

---

### 7. Presencia Digital y Omnicanalidad
El modelo de negocio actual exige presencia en múltiples canales (WhatsApp, Redes Sociales, Tienda Online). 

Para SyncroLab, esto representa un requerimiento de **Arquitectura Abierta**. Si bien para el negocio la presencia digital es un todo integrado, a nivel de software se diseñarán integraciones mediante APIs (Módulos periféricos) que se conecten al *Core* del sistema de manera desacoplada. Esto permitirá recibir un pedido desde un menú QR o desde un e-commerce y que impacte en el mismo módulo de Stock centralizado.

---

### 8. Problemas Operativos Identificados (Puntos de Dolor)
- Alta fragmentación de datos (múltiples plataformas desconectadas).
- Carga manual de inventario, propensa a errores humanos y falta de sincronización.
- Imposibilidad de obtener reportes financieros o históricos en tiempo real.
- Alta carga cognitiva para los operadores al repetir tareas administrativas.

---

### 9. Oportunidades de Mejora con SyncroLab
- Establecer una **Única Fuente de Verdad (Single Source of Truth)** para los datos operativos.
- Automatizar la deducción de insumos al realizar una venta.
- Proveer un Dashboard gerencial en tiempo real para la toma de decisiones.
- Minimizar el error humano centralizando los flujos de trabajo en interfaces intuitivas.

---

### Decisiones Arquitectónicas Derivadas del Análisis

- Se consolida el concepto universal de **Insumo** (categorizado dinámicamente) para manejar inventarios, descartando conceptos limitantes como "Materia Prima".
- El modelo de datos debe soportar **Elaboración Propia**, es decir, un "Producto" puede actuar como "Insumo" en un flujo y viceversa.
- Las interacciones con plataformas externas (e-commerce, WhatsApp) serán módulos periféricos y **desacoplados** del Core transaccional para garantizar la estabilidad del sistema.
