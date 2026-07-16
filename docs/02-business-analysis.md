# Business Analysis

**Proyecto:** SyncroLab

**Caso de estudio:** Dolce Capriccio

**Versión:** 1.0

**Estado:** En análisis

**Última actualización:** Julio 2026

---

# 1. Introducción

Este documento tiene como objetivo comprender el funcionamiento actual de Dolce Capriccio antes del desarrollo del software.

El propósito del análisis es identificar cómo opera el negocio, cuáles son sus procesos diarios, qué información necesita administrar y cuáles son las oportunidades de mejora que permitirán definir los requerimientos funcionales de SyncroLab.

---

# 2. Descripción del negocio

Dolce Capriccio es una cafetería y pastelería artesanal dedicada a la elaboración y comercialización de productos propios.

Además de ofrecer productos para consumo inmediato, el negocio produce diversos insumos de elaboración propia utilizados durante el proceso productivo.

El funcionamiento diario requiere controlar ventas, producción, stock, proveedores, pedidos, gastos y la comunicación con los clientes.

---

# 3. Productos comercializados

Actualmente el negocio ofrece productos como:

- Cookies
- Brownies
- Alfajores
- Tartas
- Medialunas
- Rolls de canela
- Tortas
- Budines
- Pepas
- Chipitas
- Scones de queso

Además, elabora internamente distintos insumos para la producción:

- Mantequilla de maní
- Caramelo
- Mermeladas
- Leche de avena
- Leche de almendra
- Leche de coco
- Yogurt

---

# 4. Actores del negocio

Durante el análisis se identifican los siguientes actores:

## Dueño

Responsable de la administración general del negocio.

## Empleados

Atienden clientes, preparan pedidos, registran ventas y colaboran con la producción.

## Clientes

Realizan compras presenciales o pedidos.

## Proveedores

Abastecen materias primas, insumos y utensilios.

En futuras versiones podrían incorporarse nuevos actores como repartidores o administradores externos.

---

# 5. Procesos diarios

## Apertura

- Abrir el local.
- Encender cafeteras.
- Encender computadora.
- Encender iluminación.
- Abrir caja.

## Operación

- Recepción de mercadería.
- Registro de ingreso de productos.
- Elaboración de productos.
- Atención al cliente.
- Registro de ventas.
- Cobro.
- Gestión de pedidos.

## Cierre

- Cierre de caja.
- Revisión de ventas.
- Revisión de gastos.
- Organización del local.
- Preparación del día siguiente.

---

# 6. Información que necesita controlar el negocio

Durante el análisis se detectaron las siguientes necesidades.

## Stock

- Cantidad disponible de cada producto.
- Productos con mayor demanda.
- Productos con baja rotación.
- Productos faltantes.

## Ventas

- Ventas diarias.
- Ventas semanales.
- Ventas mensuales.
- Productos más vendidos.

## Gastos

- Materias primas.
- Insumos.
- Utensilios.
- Servicios.
- Gastos generales.

## Proveedores

- Información de contacto.
- Productos suministrados.
- Comparación de precios.
- Historial de compras.

## Pedidos

- Pedidos pendientes.
- Fecha de entrega.
- Estado del pedido.

---

# 7. Presencia digital

Actualmente resulta indispensable mantener una presencia activa en medios digitales.

El sistema deberá contemplar futuras integraciones con:

- Página web institucional.
- Catálogo digital.
- Formulario de pedidos.
- WhatsApp.
- Redes sociales.
- Google Maps.
- Canales de contacto.

La presencia digital no solo permite aumentar el alcance de la marca, sino también facilitar la comunicación con los clientes y optimizar el proceso de ventas.

---

# 8. Problemas identificados

Durante el relevamiento inicial se detectan oportunidades de mejora.

- Información distribuida en diferentes herramientas.
- Procesos manuales.
- Dificultad para consultar información histórica.
- Escaso control centralizado.
- Pérdida de tiempo en tareas repetitivas.
- Falta de indicadores para la toma de decisiones.

---

# 9. Oportunidades de mejora

La implementación de SyncroLab permitirá:

- Centralizar toda la información.
- Automatizar procesos repetitivos.
- Mejorar el control operativo.
- Facilitar la toma de decisiones.
- Reducir errores manuales.
- Optimizar la comunicación con clientes.
- Mejorar la organización general del negocio.

---

# 10. Conclusión

El análisis realizado demuestra que Dolce Capriccio presenta procesos comunes a muchas pequeñas y medianas empresas.

La existencia de operaciones distribuidas entre diferentes herramientas y procedimientos manuales representa una oportunidad para desarrollar una plataforma que centralice la información y simplifique la gestión diaria.

Este documento constituye la base para la definición de los requerimientos funcionales que serán desarrollados en la siguiente etapa del proyecto.

---

## Decisiones tomadas

- Se utilizará una cafetería/pastelería como caso de estudio inicial.
- El software será diseñado para poder adaptarse posteriormente a otros rubros.
- La presencia digital se considera parte integral de la gestión del negocio y no un módulo aislado.
- La arquitectura será modular para permitir incorporar nuevas funcionalidades sin rediseñar el sistema.
