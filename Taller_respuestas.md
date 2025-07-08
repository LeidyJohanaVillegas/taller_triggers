🚀 **Taller de Triggers en MySQL**

## 📌 **Objetivo**

En este taller, aprenderás a utilizar **Triggers** en MySQL a través de casos prácticos. Implementarás triggers para validaciones, auditoría de cambios y registros automáticos.

* * *

## **🔹 Caso 1: Control de Stock de Productos**

### **Escenario:**

Una tienda en línea necesita asegurarse de que los clientes no puedan comprar más unidades de un producto del stock disponible. Si intentan hacerlo, la compra debe **bloquearse**.

**TAREA:**

1. Crear las tablas productos y ventas.
RTA:
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

2. Implementar un trigger BEFORE INSERT para evitar ventas con cantidad mayor al stock disponible.
RTA:
DELIMITER //
CREATE TRIGGER before_stock_productos 
BEFORE INSERT ON ventas
FOR EACH ROW
BEGIN
DECLARE product_stock INT;
SELECT stock INTO product_stock FROM productos WHERE id = NEW.id_producto;
IF NEW.cantidad > product_stock THEN
SIGNAL SQLSTATE '45000' 
SET MESSAGE_TEXT = 'Sin cantidad solicitada';
END IF;
END //
DELIMITER ;

INSERT INTO productos (nombre, stock) VALUES ('Televisor 40"', 4);
INSERT INTO ventas (id_producto, cantidad) VALUES (1, 10);

3. Probar el trigger.
RTA:
mysql> INSERT INTO ventas (id_producto, cantidad) VALUES (1, 10);
ERROR 1644 (45000): Sin cantidad solicitada

**🔹 Caso 2: Registro Automático de Cambios en Salarios**

### **Escenario:**

La empresa **TechCorp** desea mantener un registro histórico de todos los cambios de salario de sus empleados.

### **Tarea:**

1. Crear las tablas `empleados` y `historial_salarios`.
RTA: 
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

2. Implementar un trigger `BEFORE UPDATE` que registre cualquier cambio en el salario.
RTA:
DELIMITER //
CREATE TRIGGER before_salarios_update 
BEFORE UPDATE ON empleados
FOR EACH ROW
BEGIN
INSERT INTO historial_salarios (id_empleado, salario_anterior, salario_nuevo) VALUES (OLD.id, OLD.salario, NEW.salario);
END //
DELIMITER ;

INSERT INTO empleados (nombre, salario) VALUES ('Johana Niño', 3000.00);
UPDATE empleados SET salario = 4000.00 WHERE id = 1;
SELECT id_empleado, salario_anterior, salario_nuevo FROM historial_salarios;

3. Probar el trigger.
RTA:
mysql> SELECT id_empleado, salario_anterior, salario_nuevo FROM historial_salarios;
+-------------+------------------+---------------+
| id_empleado | salario_anterior | salario_nuevo |
+-------------+------------------+---------------+
|           1 |          3000.00 |       4000.00 |
+-------------+------------------+---------------+
1 row in set (0.00 sec)

🔹 Caso 3: Registro de Eliminaciones en Auditoría**

### **Escenario:**

La empresa **DataSecure** quiere registrar toda eliminación de clientes en una tabla de auditoría para evitar pérdidas accidentales de datos.

### **Tarea:**

1. Crear las tablas `clientes` y `clientes_auditoria`.
RTA:
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

2. Implementar un trigger `AFTER DELETE` para registrar los clientes eliminados.
RTA:
DELIMITER //
CREATE TRIGGER before_clientes_delete 
AFTER DELETE ON clientes
FOR EACH ROW
BEGIN
INSERT INTO clientes_auditoria (id_cliente, nombre, email) VALUES (OLD.id, OLD.nombre, OLD.email);
END //
DELIMITER ;

INSERT INTO clientes (nombre, email) VALUES ('Juan Pérez', 'juan@example.com'), ('Ana Gómez', 'ana@example.com');
SELECT id, nombre, email FROM clientes;
+----+-------------+------------------+
| id | nombre      | email            |
+----+-------------+------------------+
|  1 | Juan Pérez  | juan@example.com |
|  2 | Ana Gómez   | ana@example.com  |
+----+-------------+------------------+
DELETE FROM clientes WHERE nombre = 'Juan Pérez';
SELECT id, nombre, email FROM clientes;
+----+------------+-----------------+
| id | nombre     | email           |
+----+------------+-----------------+
|  2 | Ana Gómez  | ana@example.com |
+----+------------+-----------------+
SELECT id_cliente, nombre, email FROM clientes_auditoria;

3. Probar el trigger.
RTA:
+------------+-------------+------------------+
| id_cliente | nombre      | email            |
+------------+-------------+------------------+
|          1 | Juan Pérez  | juan@example.com |
+------------+-------------+------------------+

**🔹 Caso 4: Restricción de Eliminación de Pedidos Pendientes**

### **Escenario:**

En un sistema de ventas, no se debe permitir eliminar pedidos que aún están **pendientes**.

### **Tarea:**

1. Crear las tablas `pedidos`.
RTA:
CREATE TABLE pedidos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente VARCHAR(100),
    estado ENUM('pendiente', 'completado')
);

2. Implementar un trigger `BEFORE DELETE` para evitar la eliminación de pedidos pendientes.
RTA:
DELIMITER //
CREATE TRIGGER before_pedidos_delete
BEFORE DELETE ON pedidos
FOR EACH ROW
BEGIN
    IF OLD.estado = 'pendiente' THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'No se puede eliminar un pedido pendiente.';
    END IF;
END;
//
DELIMITER ;

INSERT INTO pedidos (cliente, estado) VALUES ('Carlos', 'pendiente'), ('Maria', 'completado');
DELETE FROM pedidos WHERE cliente = 'Maria';
DELETE FROM pedidos WHERE cliente = 'Carlos';

3. Probar el trigger.
RTA:
mysql> DELETE FROM pedidos WHERE cliente = 'Maria';
Query OK, 1 row affected (0.01 sec)
mysql> DELETE FROM pedidos WHERE cliente = 'Carlos';
ERROR 1644 (45000): No se puede eliminar un pedido pendiente.
