# Doc 06 - Diseño de Base de Datos y Modelo de Dominio

**Proyecto:** SyncroLab  
**Versión:** 1.0  
**Estado:** En planificación  
**Última actualización:** Agosto 2026  

---

### 1. Introducción
Este documento define el diseño conceptual y lógico de la base de datos relacional de SyncroLab.

Su objetivo es estructurar la información que el sistema deberá persistir, identificando las entidades principales del negocio, sus atributos y las relaciones que aseguran la consistencia transaccional. Este diseño actúa como el puente entre los requerimientos funcionales y la implementación física del motor de base de datos.

---

### 2. Objetivos del Diseño
- **Centralización (SSOT):** Evitar la fragmentación de la información utilizando una única fuente de verdad.
- **Integridad Referencial:** Garantizar que las relaciones entre tablas no dejen registros huérfanos.
- **Normalización:** Diseñar las tablas evitando la redundancia innecesaria de datos (Forma Normal 3 - 3NF).
- **Escalabilidad:** Permitir la incorporación de nuevas entidades sin romper la estructura existente.

---

### 3. Principios de Diseño
- **Soft Deletion:** Las tablas transaccionales y de entidades críticas no utilizarán borrado físico (`DELETE`). Se implementará una bandera lógica (`is_active: BOOLEAN`) para preservar el historial.
- **Trazabilidad:** Toda operación de stock, caja o venta deberá registrar marcas de tiempo (`created_at`, `updated_at`) y el usuario responsable (`user_id`).
- **Consistencia Monetaria:** Los montos económicos se almacenarán utilizando tipos de datos numéricos con precisión decimal estricta para evitar errores de redondeo.

---

### 4. Entidades Principales del Sistema
El modelo de datos se compone de los siguientes dominios de información:

1. **Seguridad y Accesos:** `Users`, `Roles`, `Permissions`.
2. **Catálogos Base:** `ProductCategories`, `Products`, `InsumoCategories`, `Insumos`.
3. **Entidades Externas:** `Customers`, `Suppliers`.
4. **Inventario:** `StockMovements` (Control unificado de stock).
5. **Operaciones Financieras:** `CashSessions` (Cajas), `Sales`, `SaleDetails`, `Expenses`, `Purchases`.
6. **Producción:** `Recipes`, `RecipeItems`.
7. **Control Operativo:** `Orders`.

---

### 5. Crecimiento y Seguridad de los Datos
- **Seguridad:** Los accesos a la base de datos estarán restringidos exclusivamente al servidor Backend mediante credenciales de entorno (`.env`). Ningún cliente (Frontend) se conectará directamente a la base de datos.
- **Respaldo:** Se planificarán respaldos automatizados diarios de la estructura y los datos relacionales.
- **Futuras Ampliaciones:** El diseño modular contempla la adición futura de tablas para facturación electrónica (AFIP/ARCA) y operaciones multi-sucursal mediante un identificador de empresa (`tenant_id`).

---

# 10. Desarrollo del Modelo de Datos

## 10.1 Modelo de Dominio

### ¿Qué es el Modelo de Dominio?
Antes de diseñar tablas, atributos o relaciones, es necesario comprender cuáles son los elementos que forman parte del negocio. El Modelo de Dominio representa los conceptos principales con los que trabaja una empresa y cómo se relacionan entre sí. En esta etapa no se piensa en programación ni en bases de datos, sino en comprender el funcionamiento del negocio desde una perspectiva funcional.

### ¿Por qué es importante?
Diseñar un sistema sin comprender el dominio del negocio suele generar estructuras incorrectas, duplicación de información y funcionalidades difíciles de mantener. Por ese motivo, el análisis del dominio constituye la base sobre la cual se construirá toda la arquitectura del sistema.

### Dominio de SyncroLab
A partir del análisis realizado sobre pequeñas y medianas empresas, y utilizando a Dolce Capriccio como caso de estudio, se identifican los siguientes dominios principales:
- Gestión de Productos
- Gestión de Categorías
- Gestión de Insumos y Categorías de Insumos
- Gestión de Recetas
- Gestión de Stock
- Gestión de Clientes
- Gestión de Proveedores
- Gestión de Compras
- Gestión de Ventas y Detalle de Venta
- Gestión de Caja
- Gestión de Gastos
- Gestión de Usuarios, Roles y Permisos
- Gestión de Reportes y Producción

### Objetivo del Modelo de Dominio
- Identificar las entidades principales del sistema.
- Comprender las responsabilidades de cada una.
- Definir los límites de cada módulo.
- Evitar duplicidad de información.
- Facilitar el diseño de la base de datos.
- Servir como referencia durante todo el desarrollo del proyecto.

### Alcance
En esta etapa únicamente se identifican los conceptos del negocio. La definición de atributos, relaciones, reglas de negocio y restricciones será desarrollada en las siguientes secciones del documento.

### Decisiones tomadas
- El diseño de la base de datos estará basado en el dominio del negocio y no en decisiones técnicas.
- Cada entidad deberá representar un concepto real dentro de una empresa.
- Ninguna entidad será incorporada sin una necesidad funcional claramente identificada.

---

## 10.2 Entidad: Producto

### Descripción
La entidad **Producto** representa cada uno de los bienes que la empresa ofrece para la venta o utiliza como parte de su operación. En SyncroLab, un producto constituye uno de los elementos centrales del sistema, ya que interviene en múltiples procesos del negocio, como las ventas, el control de stock, la producción, las compras y la generación de reportes.

Un producto podrá representar tanto un artículo elaborado por la empresa como un artículo adquirido a terceros, diferenciándose mediante su tipo de producto.

### Objetivo
Centralizar toda la información relacionada con los productos administrados por la empresa, permitiendo gestionar su ciclo de vida y sirviendo como punto de conexión entre los distintos módulos.

### Responsabilidades
- Identificar de forma única cada producto.
- Almacenar su información principal y su categoría.
- Determinar si el producto es elaborado o adquirido.
- Relacionarse con el stock disponible, ventas, recetas y compras.

### Usuarios que interactúan con esta entidad
- Administrador, Dueño del negocio, Personal de ventas, Personal de producción, Personal de compras y Responsable de stock.

### Atributos
- **ID:** Identificador único del producto.
- **Código:** Código interno del producto.
- **Nombre:** Nombre comercial del producto.
- **Descripción:** Información adicional sobre el producto.
- **Categoría:** Clasificación principal (FK -> ProductCategories).
- **Tipo de producto:** Elaborado o Adquirido.
- **Precio de venta:** Precio base utilizado para la comercialización.
- **Costo:** Costo de elaboración o adquisición.
- **Estado:** Activo o inactivo (Soft Delete).
- **Fecha de creación:** Fecha en la que fue registrado.
- **Fecha de última actualización:** Registro de última modificación.

### Relaciones
- Categorías, Stock, Movimientos de Stock, Ventas, Detalle de Venta, Pedidos, Compras, Proveedores, Producción y Recetas.

### Reglas de Negocio
- Todo producto deberá tener un nombre y pertenecer a una categoría.
- Todo producto deberá indicar su tipo (Elaborado o Comprado).
- No podrán existir productos duplicados con el mismo código.
- El precio de venta y el costo no podrán ser negativos.
- Todo producto elaborado deberá tener una receta asociada.
- Un producto con historial de ventas no podrá eliminarse físicamente del sistema.
- Los productos inactivos no podrán utilizarse en nuevas ventas, salvo autorización del administrador.

---

## 10.3 Entidad: Categoría

### Descripción
La entidad **Categoría** representa la clasificación principal de los productos dentro del sistema. Su finalidad es organizar el catálogo de productos de forma lógica, facilitando la administración, las búsquedas, los filtros y la generación de reportes.

### Objetivo
Proporcionar una estructura organizada para clasificar los productos comercializados por la empresa, mejorando la administración del catálogo y el análisis estadístico.

### Responsabilidades
- Organizar los productos del sistema.
- Facilitar búsquedas y filtros.
- Agrupar productos similares.
- Servir como criterio para reportes y estadísticas.

### Atributos
- **ID:** Identificador único de la categoría.
- **Nombre:** Nombre de la categoría (Ej: Cookies, Brownies, Tartas).
- **Descripción:** Información adicional sobre la categoría.
- **Estado:** Indica si la categoría se encuentra disponible.
- **Fecha de creación / actualización:** Marcas de tiempo de auditoría.

### Reglas de Negocio
- Toda categoría deberá tener un nombre.
- No podrán existir dos categorías con el mismo nombre.
- Una categoría no podrá eliminarse si existen productos asociados activos.
- Las categorías inactivas no podrán asignarse a nuevos productos.

---

## 10.4 Módulo: Producción

### Descripción
El módulo de Producción es el encargado de administrar todo el proceso de elaboración de productos dentro de aquellas empresas que fabrican bienes propios. Permite planificar, registrar y controlar la producción, gestionar recetas y mantener actualizado el stock de forma automática. Su implementación es opcional.

### Alcance
- Crear recetas y administrar materias primas/insumos.
- Registrar órdenes de producción.
- Consumir automáticamente los insumos utilizados y dar de alta el stock final elaborado.
- Calcular costos reales de producción.

---

## 10.5 Entidad: Insumo

### Descripción
La entidad **Insumo** representa todos los elementos consumibles necesarios para el funcionamiento operativo y/o productivo de una empresa (ingredientes, empaques, elementos de branding, productos de limpieza, artículos de oficina).

### Objetivo
Centralizar toda la información relacionada con los insumos utilizados por la empresa, permitiendo su correcta administración, clasificación, relación con proveedores y cálculo de costos.

### Responsabilidades
- Identificar cada insumo y asociarlo a una categoría de insumo.
- Registrar la unidad de medida correspondiente.
- Participar en recetas e integrarse con el módulo de Stock.

### Atributos
- **ID:** Identificador único del insumo.
- **Código:** Código interno del insumo.
- **Nombre:** Nombre del insumo.
- **Descripción:** Información complementaria o especificaciones técnicas.
- **Categoría de Insumo:** Clasificación propia de los insumos (FK).
- **Unidad de Medida:** Kilogramos, gramos, litros, mililitros, unidades, etc.
- **Costo de Adquisición:** Costo actual de compra.
- **Estado:** Activo, Inactivo, Discontinuado.
- **Fecha de creación / última actualización:** Marcas de auditoría.

### Reglas de Negocio
- Todo insumo deberá tener un nombre, pertenecer a una categoría y definir una unidad de medida.
- No podrán existir dos insumos con el mismo código.
- El costo de adquisición no podrá ser negativo.
- No podrá eliminarse un insumo utilizado en procesos históricos.

---

## 10.6 Entidad: Receta

### Descripción
La entidad **Receta** define la estructura compositiva de un producto elaborado, especificando qué insumos y en qué cantidades exactas se consumen para fabricar una unidad o lote de dicho producto.

### Objetivo
Permitir el cálculo automatizado de requerimientos de insumos y la deducción precisa del stock al momento de registrar una producción o venta.

### Atributos
- **ID:** Identificador único de la receta.
- **Product ID:** Producto elaborado al que pertenece la receta (FK).
- **Nombre:** Denominación de la receta.
- **Rendimiento:** Cantidad de unidades que produce la receta base.
- **Estado:** Activa / Inactiva.

### Reglas de Negocio
- Todo producto de tipo "Elaborado" debe contar obligatoriamente con una receta asociada.
- Las cantidades de insumos dentro de una receta no pueden ser negativas.

---

## 10.7 Módulo: Stock

### Descripción
El **Módulo de Stock** centraliza el control de existencias tanto de productos como de insumos mediante un registro inmutable de movimientos (`StockMovements`), evitando tablas estáticas propensas a desincronización.

### Objetivo
Garantizar la trazabilidad completa de cada entrada, salida, ajuste o pérdida de inventario en tiempo real.

### Atributos de Movimiento de Stock (`StockMovements`)
- **ID:** Identificador único del movimiento.
- **Item Type:** Tipo de elemento ('PRODUCT' o 'INSUMO').
- **Item ID:** ID correspondiente al producto o insumo.
- **Movement Type:** Tipo de operación ('SALE', 'PURCHASE', 'PRODUCTION', 'ADJUSTMENT', 'WASTE').
- **Quantity:** Cantidad afectada (positiva para ingresos, negativa para egresos).
- **User ID:** Usuario responsable de registrar el movimiento (FK).
- **Created At:** Marca de tiempo inmutable de la operación.

### Reglas de Negocio
- El stock actual de cualquier ítem se calcula de forma dinámica mediante la sumatoria de sus movimientos registrados.
- Ningún movimiento de stock puede quedar sin un usuario responsable asociado.

---

## 10.8 Entidad: Proveedor

### Descripción
La entidad **Proveedor** almacena la información comercial y de contacto de las empresas o personas que abastecen de insumos y mercadería al negocio.

### Objetivo
Mantener organizada la cadena de suministro y facilitar la asociación de costos de reposición.

### Atributos
- **ID:** Identificador único del proveedor.
- **Name / Business Name:** Razón social o nombre comercial.
- **CUIT / Identifier:** Clave de identificación fiscal.
- **Contact Info:** Teléfono, correo electrónico, dirección.
- **Estado:** Activo / Inactivo.

### Reglas de Negocio
- Todo proveedor debe contar con un nombre o razón social válido.
- No se pueden eliminar proveedores que posean compras asociadas en el historial.

---

## 10.9 Entidad: Compras

### Descripción
La entidad **Compras** registra formalmente la adquisición de insumos o productos a los proveedores, incrementando el inventario disponible en el stock.

### Objetivo
Controlar la entrada de mercadería externa y registrar los costos de reposición de insumos.

### Atributos
- **ID:** Identificador único de la compra.
- **Supplier ID:** Proveedor que suministra la mercadería (FK).
- **User ID:** Usuario que registra la operación (FK).
- **Total Amount:** Monto total abonado en la transacción.
- **Created At:** Fecha y hora de la compra.

### Reglas de Negocio
- Toda compra debe estar asociada a un proveedor registrado.
- El monto total de la compra no puede ser negativo.

---

## 10.10 Entidad: Cliente

### Descripción
La entidad **Cliente** almacena los datos de los compradores del negocio, permitiendo asociarlos de forma opcional a las transacciones comerciales.

### Objetivo
Facilitar la gestión de ventas frecuentes, cuentas corrientes o historial de compras de los consumidores.

### Atributos
- **ID:** Identificador único del cliente.
- **Name:** Nombre y apellido o razón social.
- **Identifier:** DNI o CUIT (opcional para consumidor final).
- **Contact:** Teléfono, correo electrónico, dirección.
- **Estado:** Activo / Inactivo.

### Reglas de Negocio
- El sistema debe permitir operar de manera predeterminada con un cliente genérico ("Consumidor Final") cuando no se requieran datos específicos.

---

## 10.11 Módulo: Caja (Sesiones de Caja)

### Descripción
El módulo **Sesiones de Caja** permite auditar y controlar el flujo de dinero físico y electrónico operado durante un turno de trabajo o jornada laboral.

### Objetivo
Evitar diferencias de caja, registrar aperturas/cierres y vincular cada movimiento monetario a un operador responsable.

### Atributos (`CashSessions`)
- **ID:** Identificador único de la sesión.
- **User ID:** Operador responsable de la caja (FK).
- **Opening Amount:** Dinero inicial con el que abre la caja.
- **Closing Amount:** Dinero físico contado y declarado al cierre.
- **System Calculated Amount:** Dinero calculado automáticamente por el sistema según las ventas y cobros registrados.
- **Status:** Estado de la sesión ('OPEN' o 'CLOSED').
- **Opened At / Closed At:** Marcas temporales de apertura y cierre.

### Reglas de Negocio
- Ninguna venta o gasto operativo puede registrarse si no existe una sesión de caja en estado 'OPEN' operada por el usuario.
- El cierre de caja debe contrastar el monto declarado con el calculado por el sistema para detectar diferencias.

---

## 10.12 Entidad: Ventas y Detalle de Venta

### Descripción
Registra la transacción comercial de salida de productos hacia un cliente y el desglose detallado de los ítems comercializados.

### Atributos (`Sales` & `SaleDetails`)
* **Sales (Cabecera de Venta):**
  - `id` (PK)
  - `customer_id` (FK -> Customers, opcional)
  - `cash_session_id` (FK -> CashSessions)
  - `user_id` (FK -> Users)
  - `total_amount` (DECIMAL)
  - `payment_method` (ENUM: 'CASH', 'DEBIT', 'CREDIT', 'TRANSFER')
  - `created_at` (TIMESTAMP)

* **SaleDetails (Detalle de Venta):**
  - `id` (PK)
  - `sale_id` (FK -> Sales)
  - `product_id` (FK -> Products)
  - `quantity` (INT)
  - `unit_price` (DECIMAL)
  - `subtotal` (DECIMAL)

### Reglas de Negocio
- Toda venta descuenta automáticamente stock del producto vendido mediante un registro en `StockMovements`.
- El importe total de la venta debe coincidir exactamente con la sumatoria de los subtotales de sus detalles.

---

## 10.13 Entidad: Gastos y Egresos

### Descripción
Registra las salidas de dinero operativas del negocio que no corresponden a reposiciones directas de mercadería (ej. servicios, alquileres, insumos generales de caja chica).

### Atributos
- **ID:** Identificador único del gasto.
- **Category:** Categoría o motivo del gasto.
- **Amount:** Monto monetario del egreso.
- **Cash Session ID:** Sesión de caja desde la cual se efectúa el pago (FK).
- **Description:** Observaciones adicionales del gasto.
- **User ID:** Usuario responsable (FK).

### Reglas de Negocio
- Todo gasto reduce el saldo disponible en la sesión de caja activa vinculada.

---

## 10.14 Entidad: Usuarios y Roles

### Descripción
Administra la seguridad, autenticación y control de accesos al sistema bajo un modelo de control basado en roles (RBAC).

### Atributos (`Users`, `Roles` & `Permissions`)
- Define perfiles (Administrador, Vendedor, Producción, etc.), credenciales de acceso cifradas y permisos granulares sobre cada módulo y acción de la plataforma.

### Reglas de Negocio
- Las contraseñas de usuario deben almacenarse obligatoriamente mediante algoritmos de hash seguros (ej. bcrypt).
- Ningún usuario inactivo (`is_active = FALSE`) podrá iniciar sesión en el sistema.

---

### Decisiones Arquitectónicas Tomadas
- Se implementa un modelo de **Movimientos de Stock Unificados** (`StockMovements`) en lugar de actualizar estáticamente una columna de cantidad, garantizando una auditoría completa del inventario.
- Se adopta la estrategia de **Soft Delete** en todas las entidades críticas para proteger la integridad histórica de las ventas y los reportes financieros.
- Se incorpora obligatoriamente el módulo de **CashSessions** para vincular cada venta y gasto a una caja abierta y auditable.
