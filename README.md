🚀 **Taller de Triggers en MySQL**

## 📌 **Objetivo**

En este taller, aprenderás a utilizar **Triggers** en MySQL a través de casos prácticos. Implementarás triggers para validaciones, auditoría de cambios y registros automáticos.

* * *

## **🔹 Caso 1: Control de Stock de Productos**

### **Escenario:**

Una tienda en línea necesita asegurarse de que los clientes no puedan comprar más unidades de un producto del stock disponible. Si intentan hacerlo, la compra debe **bloquearse**.
```
CREATE TABLE productos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    stock INT
);
CREATE TABLE ventas (
    id INT PRIMARY KEY AUTO_INCREMENT,
    id_producto INT,
    cantidad INT,
    FOREIGN KEY (id_producto) REFERENCES productos(id)
);
```
**TAREA:**

1. Crear las tablas productos y ventas.
2. Implementar un trigger BEFORE INSERT para evitar ventas con cantidad mayor al stock disponible.
3. Probar el trigger.

**🔹 Caso 2: Registro Automático de Cambios en Salarios**

### **Escenario:**

La empresa **TechCorp** desea mantener un registro histórico de todos los cambios de salario de sus empleados.
```
CREATE TABLE empleados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    salario DECIMAL(10,2)
);
CREATE TABLE historial_salarios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    id_empleado INT,
    salario_anterior DECIMAL(10,2),
    salario_nuevo DECIMAL(10,2),
    fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (id_empleado) REFERENCES empleados(id)
);
```
### **Tarea:**

1. Crear las tablas `empleados` y `historial_salarios`.
2. Implementar un trigger `BEFORE UPDATE` que registre cualquier cambio en el salario.
3. Probar el trigger.


🔹 Caso 3: Registro de Eliminaciones en Auditoría**

### **Escenario:**

La empresa **DataSecure** quiere registrar toda eliminación de clientes en una tabla de auditoría para evitar pérdidas accidentales de datos.
```
CREATE TABLE clientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    email VARCHAR(50)
);
CREATE TABLE clientes_auditoria (
    id INT PRIMARY KEY AUTO_INCREMENT,
    id_cliente INT,
    nombre VARCHAR(50),
    email VARCHAR(50),
    fecha_eliminacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### **Tarea:**

1. Crear las tablas `clientes` y `clientes_auditoria`.
2. Implementar un trigger `AFTER DELETE` para registrar los clientes eliminados.
3. Probar el trigger.

**🔹 Caso 4: Restricción de Eliminación de Pedidos Pendientes**

### **Escenario:**

En un sistema de ventas, no se debe permitir eliminar pedidos que aún están **pendientes**.
```
CREATE TABLE pedidos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente VARCHAR(100),
    estado ENUM('pendiente', 'completado')
);
```
### **Tarea:**

1. Crear las tablas `pedidos`.
2. Implementar un trigger `BEFORE DELETE` para evitar la eliminación de pedidos pendientes.
3. Probar el trigger.

REALIZADO POR: Leidy Johana Niño Villegas
