# Diseño de Base de Datos

## ¿Qué es?

El diseño de base de datos es el proceso de crear una estructura lógica y física
para almacenar, organizar y gestionar los datos de manera eficiente. En el
contexto de MySQL, esto implica definir tablas, relaciones, índices,
restricciones y procedimientos almacenados que soporten las necesidades
funcionales y no funcionales del proyecto.

Es una fase crítica que establece los cimientos para la persistencia de datos,
optimización de consultas y escalabilidad del sistema.

## ¿Por qué es importante?

1. **Integridad de datos**: Garantiza que los datos se mantengan consistentes y
   válidos a través de restricciones y validaciones
2. **Rendimiento**: Un diseño optimizado mejora significativamente la velocidad
   de consultas y operaciones
3. **Escalabilidad**: Permite que el sistema crezca sin degradación del
   rendimiento
4. **Mantenibilidad**: Facilita futuras modificaciones y evolución del esquema
5. **Seguridad**: Establece controles de acceso y auditoría de datos
6. **Reducción de redundancia**: Minimiza la duplicación de datos mediante
   normalización

## ¿Qué debe incluir?

### Diseño Conceptual

- **Diagrama Entidad-Relación (ER)**: Representación visual de entidades y sus
  relaciones
- **Identificación de entidades**: Objetos principales del dominio del negocio
- **Definición de atributos**: Propiedades de cada entidad
- **Establecimiento de relaciones**: Conexiones entre entidades (1:1, 1:N, N:M)

### Diseño Lógico

- **Modelo relacional**: Transformación del modelo ER a tablas
- **Normalización**: Aplicación de formas normales (1FN, 2FN, 3FN, BCNF)
- **Definición de claves**: Primarias, foráneas y candidatas
- **Restricciones de integridad**: Check constraints, unique constraints

### Diseño Físico

- **Definición de tipos de datos**: Selección apropiada para cada columna
- **Índices**: Estrategia de indexación para optimizar consultas
- **Particionamiento**: División de tablas grandes para mejor rendimiento
- **Configuración de almacenamiento**: Tablespaces, file groups

### Documentación Técnica

- **Diccionario de datos**: Descripción detallada de cada tabla y columna
- **Diagramas de base de datos**: Representación visual del esquema
- **Procedimientos almacenados**: Lógica de negocio en la base de datos
- **Triggers**: Automatización de procesos y validaciones

## ¿Qué debo hacer?

### 1. Análisis de Requerimientos

```sql
-- Ejemplo: Análisis para un sistema de gestión de usuarios
-- Entidades identificadas: Usuario, Rol, Permiso, Sesión
-- Relaciones: Usuario-Rol (N:M), Rol-Permiso (N:M), Usuario-Sesión (1:N)
```

### 2. Diseño del Modelo Conceptual

- Crear diagrama ER usando herramientas como MySQL Workbench, Draw.io o
  Lucidchart
- Identificar todas las entidades del dominio
- Definir atributos para cada entidad
- Establecer cardinalidades de relaciones

### 3. Normalización del Modelo

```sql
-- Primera Forma Normal (1FN): Eliminar grupos repetitivos
-- Antes (violación 1FN)
CREATE TABLE usuario_mal (
    id INT PRIMARY KEY,
    nombre VARCHAR(100),
    telefonos VARCHAR(255) -- '123456789,987654321'
);

-- Después (cumple 1FN)
CREATE TABLE usuario (
    id INT PRIMARY KEY,
    nombre VARCHAR(100)
);

CREATE TABLE telefono_usuario (
    id INT PRIMARY KEY,
    usuario_id INT,
    telefono VARCHAR(20),
    FOREIGN KEY (usuario_id) REFERENCES usuario(id)
);
```

### 4. Definición de Esquema Físico

```sql
-- Definición de base de datos
CREATE DATABASE proyecto_estandar
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

-- Tabla de usuarios con optimizaciones
CREATE TABLE users (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    uuid CHAR(32) NOT NULL UNIQUE COMMENT 'Generated with bin2hex(random_bytes(16))',
    email VARCHAR(255) NOT NULL UNIQUE,
    name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    email_verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    -- Índices para optimización
    INDEX idx_users_email (email),
    INDEX idx_users_is_active (is_active),
    INDEX idx_users_created_at (created_at),
    INDEX idx_users_deleted_at (deleted_at)
) ENGINE=InnoDB;
```

### 5. Implementación de Relaciones

```sql
-- Tabla de roles
CREATE TABLE roles (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,
    description TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL
) ENGINE=InnoDB;

-- Tabla pivote user-role (relación N:M)
CREATE TABLE user_roles (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    role_id INT UNSIGNED NOT NULL,
    assigned_by BIGINT UNSIGNED NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (role_id) REFERENCES roles(id) ON DELETE CASCADE,
    FOREIGN KEY (assigned_by) REFERENCES users(id) ON DELETE SET NULL,

    UNIQUE KEY unique_user_role (user_id, role_id),
    INDEX idx_user_roles_user_id (user_id),
    INDEX idx_user_roles_role_id (role_id),
    INDEX idx_user_roles_assigned_by (assigned_by)
) ENGINE=InnoDB;
```

### 6. Optimización y Índices

```sql
-- Índices compuestos para consultas frecuentes
CREATE INDEX idx_users_is_active_email ON users(is_active, email);
CREATE INDEX idx_user_roles_lookup ON user_roles(user_id, role_id);

-- Índice para soft deletes
CREATE INDEX idx_users_soft_delete ON users(deleted_at, is_active);
```

### 7. Procedimientos y Triggers

```sql
-- Procedimiento para asignar rol a usuario
DELIMITER //
CREATE PROCEDURE assign_role_to_user(
    IN p_user_id BIGINT UNSIGNED,
    IN p_role_id INT UNSIGNED,
    IN p_assigned_by BIGINT UNSIGNED
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        RESIGNAL;
    END;

    START TRANSACTION;

    INSERT INTO user_roles (user_id, role_id, assigned_by)
    VALUES (p_user_id, p_role_id, p_assigned_by)
    ON DUPLICATE KEY UPDATE
        assigned_by = p_assigned_by,
        updated_at = CURRENT_TIMESTAMP,
        deleted_at = NULL;

    COMMIT;
END //
DELIMITER ;

-- Trigger para auditoría
CREATE TABLE user_audits (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED,
    action ENUM('INSERT', 'UPDATE', 'DELETE'),
    old_data JSON,
    new_data JSON,
    modified_by BIGINT UNSIGNED,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

DELIMITER //
CREATE TRIGGER audit_users_update
    AFTER UPDATE ON users
    FOR EACH ROW
BEGIN
    INSERT INTO user_audits (
        user_id,
        action,
        old_data,
        new_data,
        modified_by
    ) VALUES (
        NEW.id,
        'UPDATE',
        JSON_OBJECT(
            'name', OLD.name,
            'email', OLD.email,
            'is_active', OLD.is_active
        ),
        JSON_OBJECT(
            'name', NEW.name,
            'email', NEW.email,
            'is_active', NEW.is_active
        ),
        @current_user_id
    );
END //
DELIMITER ;
```

## Tips

### Mejores Prácticas de Diseño

1. **Convenciones de nomenclatura**:

   - Tablas: Nombres en inglés, plural y snake_case: `users`, `order_products`
   - Columnas: Nombres en inglés, singular y snake_case: `name`, `created_at`
   - Claves foráneas: Nombre de tabla en singular + `_id`: `user_id`,
     `category_id`
   - Índices: Inglés, snake*case, prefijo `idx*`: `idx_users_email`,
     `idx_products_category`
   - Triggers: Inglés, snake_case: `audit_users_update`, `validate_order_total`

2. **Tipos de datos optimizados**:

   - Usar `BIGINT UNSIGNED` para IDs con alto volumen
   - `VARCHAR` en lugar de `CHAR` para texto variable
   - `TIMESTAMP` para fechas con zona horaria
   - `DECIMAL` para valores monetarios

3. **Estrategias de indexación**:
   - Índices en claves foráneas
   - Índices compuestos para consultas frecuentes
   - Evitar sobre-indexación
   - Nomenclatura: `idx_tabla_columna`

### Patrones de Diseño Recomendados

1. **UUID como identificador público**: Generados desde PHP con
   `bin2hex(random_bytes(16))` para mayor seguridad
2. **Soft deletes**: Obligatorio en todas las tablas usando `deleted_at`
3. **Timestamps automáticos**: Obligatorio `created_at`, `updated_at`,
   `deleted_at` en todas las tablas
4. **Claves foráneas**: Crear para todas las relaciones entre tablas
5. **Versionado de esquema**: Usar migraciones para cambios estructurales

### Consideraciones de Rendimiento

1. **Particionamiento horizontal**: Para tablas con millones de registros
2. **Desnormalización controlada**: En casos específicos de rendimiento
3. **Caché de consultas**: Configurar query cache apropiadamente
4. **Monitoreo de queries lentas**: Activar slow query log

## Ejemplos

### Ejemplo 1: Sistema de E-commerce

```sql
-- Esquema simplificado para e-commerce
CREATE DATABASE ecommerce
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE ecommerce;

-- Tabla de categorías
CREATE TABLE categories (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    uuid CHAR(32) NOT NULL UNIQUE COMMENT 'Generated with bin2hex(random_bytes(16))',
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    parent_category_id INT UNSIGNED NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    FOREIGN KEY (parent_category_id) REFERENCES categories(id),
    INDEX idx_categories_slug (slug),
    INDEX idx_categories_is_active (is_active),
    INDEX idx_categories_parent_category_id (parent_category_id),
    INDEX idx_categories_deleted_at (deleted_at)
) ENGINE=InnoDB;

-- Tabla de productos
CREATE TABLE products (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    uuid CHAR(32) NOT NULL UNIQUE COMMENT 'Generated with bin2hex(random_bytes(16))',
    sku VARCHAR(50) NOT NULL UNIQUE,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    sale_price DECIMAL(10,2) NULL,
    stock INT UNSIGNED DEFAULT 0,
    category_id INT UNSIGNED NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    is_featured BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    FOREIGN KEY (category_id) REFERENCES categories(id),
    INDEX idx_products_sku (sku),
    INDEX idx_products_category_id (category_id),
    INDEX idx_products_is_active_is_featured (is_active, is_featured),
    INDEX idx_products_price (price),
    INDEX idx_products_stock (stock),
    INDEX idx_products_deleted_at (deleted_at),
    FULLTEXT idx_products_search (name, description)
) ENGINE=InnoDB;

-- Tabla de órdenes
CREATE TABLE orders (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    uuid CHAR(32) NOT NULL UNIQUE COMMENT 'Generated with bin2hex(random_bytes(16))',
    order_number VARCHAR(20) NOT NULL UNIQUE,
    user_id BIGINT UNSIGNED NOT NULL,
    status ENUM('pending', 'processing', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
    subtotal DECIMAL(10,2) NOT NULL,
    taxes DECIMAL(10,2) DEFAULT 0,
    discount DECIMAL(10,2) DEFAULT 0,
    total DECIMAL(10,2) NOT NULL,
    currency CHAR(3) DEFAULT 'USD',
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    FOREIGN KEY (user_id) REFERENCES users(id),
    INDEX idx_orders_user_id (user_id),
    INDEX idx_orders_status (status),
    INDEX idx_orders_created_at (created_at),
    INDEX idx_orders_order_number (order_number),
    INDEX idx_orders_deleted_at (deleted_at)
) ENGINE=InnoDB;

-- Tabla de detalle de órdenes
CREATE TABLE order_products (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    order_id BIGINT UNSIGNED NOT NULL,
    product_id BIGINT UNSIGNED NOT NULL,
    quantity INT UNSIGNED NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    subtotal DECIMAL(10,2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id),
    INDEX idx_order_products_order_id (order_id),
    INDEX idx_order_products_product_id (product_id),
    INDEX idx_order_products_deleted_at (deleted_at)
) ENGINE=InnoDB;
```

### Ejemplo 2: Sistema de Gestión de Contenido (CMS)

```sql
-- Esquema para CMS
CREATE DATABASE cms_project
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE cms_project;

-- Tabla de contenido
CREATE TABLE contents (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    uuid CHAR(32) NOT NULL UNIQUE COMMENT 'Generated with bin2hex(random_bytes(16))',
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) NOT NULL,
    content LONGTEXT,
    summary TEXT,
    author_id BIGINT UNSIGNED NOT NULL,
    type ENUM('article', 'page', 'event', 'news') NOT NULL,
    status ENUM('draft', 'review', 'published', 'archived') DEFAULT 'draft',
    featured_image VARCHAR(500),
    meta_title VARCHAR(255),
    meta_description TEXT,
    published_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    FOREIGN KEY (author_id) REFERENCES users(id),
    UNIQUE KEY unique_slug_type (slug, type),
    INDEX idx_contents_type_status (type, status),
    INDEX idx_contents_author_id (author_id),
    INDEX idx_contents_published_at (published_at),
    INDEX idx_contents_deleted_at (deleted_at),
    FULLTEXT idx_contents_search (title, content, summary)
) ENGINE=InnoDB;

-- Tabla de etiquetas
CREATE TABLE tags (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE,
    slug VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    color VARCHAR(7) DEFAULT '#000000',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,

    INDEX idx_tags_slug (slug),
    INDEX idx_tags_deleted_at (deleted_at)
) ENGINE=InnoDB;

-- Tabla pivote content-tags
CREATE TABLE content_tags (
    content_id BIGINT UNSIGNED NOT NULL,
    tag_id INT UNSIGNED NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY (content_id, tag_id),
    FOREIGN KEY (content_id) REFERENCES contents(id) ON DELETE CASCADE,
    FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- Vista para contenido publicado
CREATE VIEW published_contents AS
SELECT
    c.*,
    u.name as author_name,
    u.email as author_email,
    GROUP_CONCAT(t.name) as tags
FROM contents c
INNER JOIN users u ON c.author_id = u.id
LEFT JOIN content_tags ct ON c.id = ct.content_id
LEFT JOIN tags t ON ct.tag_id = t.id
WHERE c.status = 'published'
    AND c.published_at <= NOW()
    AND c.deleted_at IS NULL
GROUP BY c.id;
```

### Ejemplo 3: Configuraciones y Migraciones

```sql
-- Script de configuración inicial
-- Configuraciones de MySQL para el proyecto
SET GLOBAL innodb_file_per_table = ON;
SET GLOBAL innodb_buffer_pool_size = 1073741824; -- 1GB
SET GLOBAL query_cache_type = ON;
SET GLOBAL query_cache_size = 268435456; -- 256MB

-- Crear usuario específico para la aplicación
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON proyecto_estandar.* TO 'app_user'@'localhost';
GRANT EXECUTE ON PROCEDURE proyecto_estandar.* TO 'app_user'@'localhost';

-- Usuario para backups (solo lectura)
CREATE USER 'backup_user'@'localhost' IDENTIFIED BY 'backup_password';
GRANT SELECT, LOCK TABLES ON proyecto_estandar.* TO 'backup_user'@'localhost';

FLUSH PRIVILEGES;
```

## Navegación

**Progreso en Diseño y Arquitectura:**

- ✅ [Diseño y Arquitectura - Introducción](./diseno-arquitectura.md)
- ✅ [Stack Tecnológico](./stack-tecnologico.md)
- ✅ [Elección de Herramientas](./eleccion-herramientas.md)
- ✅ [Patrones de Diseño](./patrones-diseno.md)
- ✅ [Arquitectura del Sistema](./arquitectura-sistema.md)
- ✅ [Estructura de Carpetas](./estructura-carpetas.md)
- ✅ **Diseño de Base de Datos** ← Estás aquí
- ⏭️ [Diseño de APIs](./diseno-apis.md)
- ⏭️ [Diagramas de Flujo](./diagramas-flujo.md)
- ⏭️ [Casos de Uso y User Stories](./casos-uso-user-stories.md)
- ⏭️ [Prototipos y Wireframes](./prototipos-wireframes.md)
- ⏭️ [Especificaciones Técnicas](./especificaciones-tecnicas.md)

---

### Siguiente Paso

Continúa con el [**Diseño de APIs**](./diseno-apis.md).

[⬅️ Estructura de Carpetas](./estructura-carpetas.md) |
[🏠 README Principal](../../README.md) | [➡️ Diseño de APIs](./diseno-apis.md)

### 🏠 Inicio

[README - Estándar de Proyectos](../../README.md)

---

**Story Points estimados**: 8 puntos **Tiempo estimado**: 2-3 sprints
(dependiendo de la complejidad del dominio)
