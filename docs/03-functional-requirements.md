# Doc 03 - Functional Requirements

**Proyecto:** SyncroLab  
**Versión:** 1.0  
**Estado:** En análisis  
**Última actualización:** Agosto 2026  

---

### 1. Introducción
Este documento define los Requerimientos Funcionales (RF) del núcleo de SyncroLab. 

Un requerimiento funcional describe las capacidades técnicas y acciones transaccionales que el sistema debe poder ejecutar para resolver los problemas de negocio identificados en el análisis previo. Estos requerimientos operan como el "plano de construcción" para el diseño de la base de datos, el desarrollo de APIs y la interfaz de usuario.

---

### 2. Objetivo
El objetivo de este documento es delimitar el alcance del **Core (Núcleo Base)** de la plataforma, asegurando que las funcionalidades desarrolladas sean agnósticas al rubro comercial y permitan una gestión unificada y escalable.

---

### 3. Módulos del Núcleo Identificados
En base al modelo de dominio y al análisis del PoC (Cronkie), el Core del sistema se compondrá de los siguientes módulos transaccionales:

- Gestión de Usuarios y Autenticación
- Gestión de Productos (Bienes Finales)
- Gestión de Insumos (Recursos Consumibles)
- Gestión de Stock (Inventario Unificado)
- Gestión de Ventas y Facturación
- Gestión de Caja y Arqueo
- Gestión de Egresos (Gastos y Compras)
- Gestión de Clientes y Proveedores (Entidades)
- Gestión de Producción (Módulo opcional)
- Dashboard y Reportería
- Configuración Global

---

### 4. Requerimientos Funcionales (RF)

#### RF-001: Gestión de Usuarios y Autenticación
El sistema deberá permitir:
- Autenticar usuarios (Login/Logout) de forma segura.
- Crear y modificar perfiles de usuario.
- Gestionar Roles (Dueño, Administrador, Operador, etc.).
- Restringir el acceso a módulos específicos según el Rol (Control de Autorización - RBAC).
- Registrar trazabilidad de acciones (quién hizo qué y cuándo).

#### RF-002: Gestión de Productos (Bienes Finales)
El sistema deberá permitir:
- Crear y editar productos comercializables.
- Clasificar productos mediante Categorías dinámicas.
- Asignar precios de venta y costos teóricos.
- Cambiar el estado del producto (Activo/Inactivo - *Soft Delete* para conservar historial).
- Buscar y filtrar el catálogo completo.

#### RF-003: Gestión de Insumos (Recursos Consumibles)
El sistema deberá permitir:
- Crear y editar insumos (Ej: ingredientes, empaques, artículos de limpieza).
- Crear y asignar "Categorías de Insumos" personalizables.
- Cambiar el estado del insumo (Activo/Inactivo - *Soft Delete*).
- Asociar insumos a proveedores específicos.

#### RF-004: Gestión de Stock Unificado
El sistema deberá permitir:
- Registrar movimientos manuales de inventario (ingreso, egreso, ajuste, merma).
- Sincronizar automáticamente el stock tras una Venta (egreso) o una Compra (ingreso).
- Consultar el inventario actual (tanto de Productos como de Insumos).
- Configurar y visualizar "Alertas de Stock Mínimo".

#### RF-005: Gestión de Ventas y Pedidos
El sistema deberá permitir:
- Registrar órdenes de venta detallando los ítems adquiridos.
- Asociar la venta a un Cliente (opcional).
- Seleccionar método de pago y aplicar descuentos/recargos.
- Gestionar estados de pedidos (Pendiente, En Preparación, Entregado, Cancelado).
- Emitir comprobantes de venta no fiscales (Tickets).

#### RF-006: Gestión de Caja y Arqueo
El sistema deberá permitir:
- Registrar la "Apertura de Caja" con un monto inicial.
- Registrar el "Cierre de Caja" (Arqueo Z) computando ventas, ingresos extra y retiros.
- Detectar diferencias entre el saldo teórico del sistema y el dinero físico declarado.

#### RF-007: Gestión de Egresos y Compras
El sistema deberá permitir:
- Registrar gastos fijos y variables.
- Registrar compras de mercadería asociadas a un Proveedor.
- Impactar automáticamente las compras de mercadería en el módulo de Stock.
- Categorizar los egresos para su posterior análisis financiero.
- Anular o archivar egresos en caso de error (*Soft Delete*).

#### RF-008: Gestión de Entidades (Clientes y Proveedores)
El sistema deberá permitir:
- Crear y editar perfiles de Clientes y Proveedores.
- Visualizar el historial de transacciones asociado a cada entidad.
- Desactivar perfiles en desuso (*Soft Delete*).

#### RF-009: Gestión de Producción (Módulo Opcional)
El sistema deberá permitir:
- Crear y editar "Recetas" o "Fórmulas" que vinculen un Producto con N cantidad de Insumos.
- Registrar "Órdenes de Producción" (Ej: Elaborar 10 Tortas).
- Descontar automáticamente del stock los insumos utilizados en base a la receta al finalizar una orden de producción.

#### RF-010: Dashboard y Métricas
El sistema deberá mostrar visualmente:
- Ingresos, egresos y rentabilidad neta del día/mes en curso.
- Ranking de productos más vendidos.
- Alertas de inventario crítico.

#### RF-011: Configuración Global
El sistema deberá permitir:
- Configurar datos comerciales de la empresa (Nombre, CUIT, Dirección, Logo).
- Gestionar monedas y tasas de impuestos.
- Personalizar los métodos de pago aceptados.

---

### 5. Requerimientos Periféricos y Futuros Integrables
Las siguientes funcionalidades se diseñarán como módulos desacoplados del Core, comunicables vía API:
- Integración con Organismos Fiscales (AFIP/ARCA para facturación electrónica).
- Recepción de pedidos omnicanal (E-commerce web, WhatsApp Bot).
- Reportería avanzada mediante Inteligencia Artificial.
- Exportación automatizada a sistemas contables externos.

---

### 6. Prioridad de Desarrollo (Roadmap Estratégico)
Para garantizar una construcción lógica y estable, el desarrollo seguirá este orden de dependencias:

1. **Configuración Global y Usuarios** (Cimientos del sistema y seguridad).
2. **Productos, Insumos y Entidades** (Catálogos base).
3. **Stock Unificado** (El motor de inventario).
4. **Ventas y Caja** (Flujo de ingresos).
5. **Egresos y Compras** (Flujo de salidas).
6. **Producción** (Módulo opcional de transformación).
7. **Dashboard** (Lectura de datos consolidados).
8. **Módulos Periféricos** (Facturación Electrónica, Web, Bots).

---

### Decisiones Arquitectónicas Tomadas

- **Soft Delete Obligatorio:** Ningún registro transaccional será eliminado físicamente de la base de datos para preservar la integridad histórica y referencial. Se utilizarán banderas de estado (Activo/Inactivo).
- **Caja como Eje de Control:** Se incorpora la gestión de Sesiones de Caja para asegurar que toda transacción económica quede registrada dentro de un turno operativo auditable.
- **Producción Aislada:** El módulo de producción se diseña como una capa independiente. Si el usuario final es una Ferretería, este módulo simplemente no se renderiza, manteniendo el Core intacto.
