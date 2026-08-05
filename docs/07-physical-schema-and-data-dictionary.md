# Doc 07 - Modelo Físico y Diccionario de Datos

**Proyecto:** SyncroLab  
**Versión:** 1.0  
**Estado:** En planificación  
**Última actualización:** Agosto 2026 

---

## 1. Decisiones Técnicas de Arquitectura

1. **Motor de Base de Datos:** PostgreSQL (v15+)
2. **Claves Primarias (PK):** Universally Unique Identifier (`UUID`), generado mediante `gen_random_uuid()`.
3. **Convención de Nombres:** `snake_case` tanto para nombres de tablas (en plural) como para columnas.
4. **Tipos Monetarios:** `NUMERIC(12, 2)` para evitar errores de redondeo de punto flotante en importes de ventas, costos y gastos.
5. **Tipos de Cantidades:** `NUMERIC(10, 3)` en insumos para permitir unidades fraccionadas (ej: 0.250 kg) e `INTEGER` en productos unitarios.
6. **Marcas de Tiempo (Auditoría):** `TIMESTAMPTZ` (Timestamp con zona horaria) en `created_at` y `updated_at`.
7. **Estrategia de Borrado:** Soft Delete mediante la columna `is_active BOOLEAN DEFAULT TRUE`.

---

## 2. Mapa de Relaciones y Cardinalidades

- **users** `1:N` **cash_sessions** (Un usuario abre/cierra muchas cajas).
- **users** `1:N` **sales** (Un usuario registra muchas ventas).
- **users** `1:N` **stock_movements** (Un usuario es responsable de muchos movimientos de stock).
- **roles** `N:M` **permissions** (Mediante tabla intermedia `role_permissions`).
- **users** `N:M` **roles** (Mediante tabla intermedia `user_roles`).
- **product_categories** `1:N` **products** (Una categoría agrupa muchos productos).
- **insumo_categories** `1:N` **insumos** (Una categoría agrupa muchos insumos).
- **products** `1:1` **recipes** (Un producto elaborado tiene una receta).
- **recipes** `1:N` **recipe_items** (Una receta tiene muchos insumos asociados).
- **insumos** `1:N` **recipe_items** (Un insumo aparece en muchas recetas).
- **customers** `1:N` **sales** (Un cliente realiza muchas compras; relación opcional).
- **suppliers** `1:N` **purchases** (Un proveedor provee muchas compras).
- **cash_sessions** `1:N` **sales** (Una sesión de caja registra muchas ventas).
- **cash_sessions** `1:N` **expenses** (Una sesión de caja registra muchos egresos).
- **sales** `1:N` **sale_details** (Una venta desglosa muchos ítems).
- **products** `1:N` **sale_details** (Un producto se vende en muchas transacciones).

---

## 3. Diccionario de Datos

### 3.1 Módulo: Seguridad y Accesos

#### Tabla: `roles`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador único del rol. |
| `name` | `VARCHAR(50)` | - | `UNIQUE, NOT NULL` | Nombre del rol (ej: Admin, Vendedor). |
| `description` | `TEXT` | - | `NULL` | Descripción de las responsabilidades del rol. |
| `created_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Fecha de creación del registro. |

#### Tabla: `permissions`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador único del permiso. |
| `code` | `VARCHAR(100)`| - | `UNIQUE, NOT NULL` | Código técnico (ej: `sales:create`). |
| `description` | `TEXT` | - | `NULL` | Detalle funcional del permiso. |

#### Tabla: `users`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador único del usuario. |
| `email` | `VARCHAR(150)`| - | `UNIQUE, NOT NULL` | Correo electrónico / Credencial. |
| `password_hash`| `VARCHAR(255)`| - | `NOT NULL` | Hash seguro de la contraseña. |
| `first_name` | `VARCHAR(100)`| - | `NOT NULL` | Nombre del usuario. |
| `last_name` | `VARCHAR(100)`| - | `NOT NULL` | Apellido del usuario. |
| `is_active` | `BOOLEAN` | - | `DEFAULT TRUE` | Estado lógico para Soft Delete. |
| `created_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Fecha de creación. |
| `updated_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Fecha de última actualización. |

---

### 3.2 Módulo: Catálogos Base

#### Tabla: `product_categories`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador único de la categoría. |
| `name` | `VARCHAR(100)`| - | `UNIQUE, NOT NULL` | Nombre de la categoría de productos. |
| `description` | `TEXT` | - | `NULL` | Información complementaria. |
| `is_active` | `BOOLEAN` | - | `DEFAULT TRUE` | Estado de disponibilidad. |

#### Tabla: `products`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador único del producto. |
| `category_id` | `UUID` | FK | `REFERENCES product_categories(id)` | Categoría a la que pertenece. |
| `sku` | `VARCHAR(50)` | - | `UNIQUE, NOT NULL` | Código interno de producto. |
| `name` | `VARCHAR(150)`| - | `NOT NULL` | Nombre comercial del producto. |
| `product_type` | `VARCHAR(20)` | - | `CHECK (type IN ('MANUFACTURED', 'PURCHASED'))` | Tipo: Elaborado o Comprado. |
| `sale_price` | `NUMERIC(12,2)`| - | `NOT NULL, CHECK (sale_price >= 0)` | Precio base de venta. |
| `cost_price` | `NUMERIC(12,2)`| - | `DEFAULT 0.00` | Costo estimado o de elaboración. |
| `is_active` | `BOOLEAN` | - | `DEFAULT TRUE` | Estado lógico del producto. |
| `created_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Marca de tiempo de alta. |

---

### 3.3 Módulo: Insumos y Producción

#### Tabla: `insumos`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador único del insumo. |
| `code` | `VARCHAR(50)` | - | `UNIQUE, NOT NULL` | Código técnico del insumo. |
| `name` | `VARCHAR(150)`| - | `NOT NULL` | Nombre del insumo/materia prima. |
| `unit_measure` | `VARCHAR(20)` | - | `NOT NULL` | Unidad (KG, GR, LT, ML, UNIDAD). |
| `cost_price` | `NUMERIC(12,2)`| - | `NOT NULL, CHECK (cost_price >= 0)` | Costo actual de compra unitario. |
| `is_active` | `BOOLEAN` | - | `DEFAULT TRUE` | Estado lógico del insumo. |

#### Tabla: `recipes`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador de la receta. |
| `product_id` | `UUID` | FK | `UNIQUE, REFERENCES products(id)` | Producto asociado (1 a 1). |
| `yield_quantity`| `INTEGER` | - | `NOT NULL, DEFAULT 1` | Cantidad de unidades que rinde la receta. |
| `is_active` | `BOOLEAN` | - | `DEFAULT TRUE` | Estado de la receta. |

#### Tabla: `recipe_items`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador del detalle. |
| `recipe_id` | `UUID` | FK | `REFERENCES recipes(id) ON DELETE CASCADE` | Receta contenedora. |
| `insumo_id` | `UUID` | FK | `REFERENCES insumos(id)` | Insumo consumido. |
| `quantity` | `NUMERIC(10,3)`| - | `NOT NULL, CHECK (quantity > 0)` | Cantidad de insumo requerida. |

---

### 3.4 Módulo: Stock Unificado

#### Tabla: `stock_movements`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador del movimiento. |
| `item_type` | `VARCHAR(20)` | - | `CHECK (item_type IN ('PRODUCT', 'INSUMO'))` | Discrimina entre producto e insumo. |
| `item_id` | `UUID` | - | `NOT NULL` | ID referencial del producto o insumo. |
| `movement_type`| `VARCHAR(20)` | - | `CHECK (type IN ('SALE', 'PURCHASE', 'PRODUCTION', 'ADJUSTMENT', 'WASTE'))` | Motivo de la alteración. |
| `quantity` | `NUMERIC(10,3)`| - | `NOT NULL` | Cantidad (+ entrada, - salida). |
| `user_id` | `UUID` | FK | `REFERENCES users(id)` | Responsable de la transacción. |
| `created_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Momento exacto del registro. |

---

### 3.5 Módulo: Operaciones y Finanzas

#### Tabla: `cash_sessions`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador de la sesión de caja. |
| `user_id` | `UUID` | FK | `REFERENCES users(id)` | Usuario operador de la caja. |
| `opening_amount`| `NUMERIC(12,2)`| - | `NOT NULL, CHECK (opening_amount >= 0)` | Monto de apertura en efectivo. |
| `closing_amount`| `NUMERIC(12,2)`| - | `NULL` | Monto físico declarado al cierre. |
| `system_amount` | `NUMERIC(12,2)`| - | `NULL` | Monto calculado automáticamente. |
| `status` | `VARCHAR(20)` | - | `DEFAULT 'OPEN' CHECK (status IN ('OPEN', 'CLOSED'))` | Estado de la caja. |
| `opened_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Apertura de turno. |
| `closed_at` | `TIMESTAMPTZ`| - | `NULL` | Cierre de turno. |

#### Tabla: `sales`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador de la venta. |
| `cash_session_id`| `UUID` | FK | `REFERENCES cash_sessions(id)` | Caja abierta asociada. |
| `user_id` | `UUID` | FK | `REFERENCES users(id)` | Usuario vendedor. |
| `customer_id` | `UUID` | FK | `NULL, REFERENCES customers(id)` | Cliente (opcional). |
| `total_amount` | `NUMERIC(12,2)`| - | `NOT NULL, CHECK (total_amount >= 0)` | Importe total pagado. |
| `payment_method`| `VARCHAR(20)` | - | `NOT NULL CHECK (payment_method IN ('CASH', 'DEBIT', 'CREDIT', 'TRANSFER'))` | Medio de pago. |
| `created_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Marca temporal de la venta. |

#### Tabla: `sale_details`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador de línea de detalle. |
| `sale_id` | `UUID` | FK | `REFERENCES sales(id) ON DELETE CASCADE` | Venta a la que pertenece. |
| `product_id` | `UUID` | FK | `REFERENCES products(id)` | Producto vendido. |
| `quantity` | `INTEGER` | - | `NOT NULL, CHECK (quantity > 0)` | Cantidad vendida. |
| `unit_price` | `NUMERIC(12,2)`| - | `NOT NULL` | Precio histórico al momento de venta. |
| `subtotal` | `NUMERIC(12,2)`| - | `NOT NULL` | Subtotal (`quantity * unit_price`). |

#### Tabla: `expenses`
| Columna | Tipo de Dato | PK/FK | Constraints | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | `DEFAULT gen_random_uuid()` | Identificador del egreso. |
| `cash_session_id`| `UUID` | FK | `REFERENCES cash_sessions(id)` | Caja de la cual sale el dinero. |
| `user_id` | `UUID` | FK | `REFERENCES users(id)` | Usuario que registra el egreso. |
| `category` | `VARCHAR(50)` | - | `NOT NULL` | Categoría (Servicios, Caja chica, etc.). |
| `amount` | `NUMERIC(12,2)`| - | `NOT NULL, CHECK (amount > 0)` | Monto del gasto. |
| `description` | `TEXT` | - | `NOT NULL` | Justificación del gasto. |
| `created_at` | `TIMESTAMPTZ`| - | `DEFAULT CURRENT_TIMESTAMP` | Marca temporal. |

---

## 4. Estrategia de Índices y Optimización

Para garantizar búsquedas de alto rendimiento a medida que las tablas transaccionales crezcan, se deben definir los siguientes índices:

```sql
-- Optimización de cálculo de Stock
CREATE INDEX idx_stock_movements_item ON stock_movements(item_type, item_id);

-- Optimización de búsquedas de productos por categoría y SKU
CREATE INDEX idx_products_sku ON products(sku);
CREATE INDEX idx_products_category ON products(category_id);

-- Optimización de reporte de ventas por caja y rango de fechas
CREATE INDEX idx_sales_cash_session ON sales(cash_session_id);
CREATE INDEX idx_sales_created_at ON sales(created_at);

-- Optimización de búsqueda de sesiones de caja activas
CREATE INDEX idx_cash_sessions_user_status ON cash_sessions(user_id, status);
