# Configuración de Servidores de Producción

## ¿Qué es?

La configuración de servidores de producción es el proceso de preparar y
optimizar la infraestructura donde se ejecutará la aplicación en el entorno
productivo. Incluye la instalación, configuración y hardening de servicios como
web servers, bases de datos, y servicios de aplicación para garantizar
rendimiento, seguridad y estabilidad.

## ¿Por qué es importante?

- **Performance**: Configuraciones optimizadas mejoran significativamente el
  rendimiento
- **Seguridad**: Servidores bien configurados reducen vulnerabilidades y ataques
- **Estabilidad**: Configuraciones probadas minimizan caídas y errores en
  producción
- **Escalabilidad**: Infraestructura preparada para crecer según demanda
- **Mantenibilidad**: Configuraciones documentadas facilitan el mantenimiento
- **Compliance**: Cumplimiento de estándares de seguridad e industria

## ¿Qué debe incluir?

### Infraestructura Base

- Selección de proveedor cloud (DigitalOcean, AWS, Azure, Google Cloud)
- Dimensionamiento de recursos (CPU, RAM, almacenamiento)
- Configuración de red y firewall
- Configuración de dominios y DNS

### Stack de Aplicación

- **Web Server**: Nginx configurado para servir React + APIs Laravel
- **Runtime**: PHP 8.2+ con extensiones necesarias
- **Base de Datos**: MySQL 8.0+ optimizado para producción
- **Cache**: Redis para sesiones y caché de aplicación
- **Node.js**: Para build tools y posibles servicios

### Servicios Adicionales

- **SSL/TLS**: Certificados automáticos con Let's Encrypt
- **Monitoreo**: Agentes de monitoreo instalados
- **Logging**: Configuración centralizada de logs
- **Backup**: Automatización de respaldos

### Seguridad

- Hardening del sistema operativo
- Configuración de usuarios y permisos
- Fail2ban para protección contra ataques
- Updates automáticos de seguridad

## ¿Qué debo hacer?

### 1. Preparar la Infraestructura

#### Seleccionar Proveedor y Recursos

```bash
# Ejemplo para DigitalOcean Droplet
# Mínimo recomendado:
# - 2 vCPU, 4GB RAM para aplicaciones pequeñas
# - 4 vCPU, 8GB RAM para aplicaciones medianas
# - 8+ vCPU, 16+ GB RAM para aplicaciones grandes

# Ubuntu 22.04 LTS (recomendado)
```

#### Configurar DNS

```bash
# Registros DNS necesarios:
# A record: example.com -> IP_SERVIDOR
# A record: www.example.com -> IP_SERVIDOR
# A record: api.example.com -> IP_SERVIDOR (opcional)
```

### 2. Instalar Stack Base

#### Actualizar Sistema

```bash
# Conectarse al servidor
ssh root@your-server-ip

# Actualizar packages
apt update && apt upgrade -y

# Instalar herramientas básicas
apt install -y curl wget git unzip software-properties-common
```

#### Instalar Nginx

```bash
# Instalar Nginx
apt install -y nginx

# Habilitar y iniciar
systemctl enable nginx
systemctl start nginx

# Verificar instalación
nginx -v
```

#### Instalar PHP 8.2+

```bash
# Agregar repositorio PHP
add-apt-repository ppa:ondrej/php
apt update

# Instalar PHP y extensiones
apt install -y php8.2-fpm php8.2-cli php8.2-mysql php8.2-xml \
php8.2-mbstring php8.2-curl php8.2-zip php8.2-intl \
php8.2-gd php8.2-bcmath php8.2-redis php8.2-imagick

# Verificar instalación
php -v
```

#### Instalar MySQL

```bash
# Instalar MySQL Server
apt install -y mysql-server

# Configurar seguridad
mysql_secure_installation

# Crear usuario y base de datos
mysql -u root -p
CREATE DATABASE app_production;
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON app_production.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

#### Instalar Redis

```bash
# Instalar Redis
apt install -y redis-server

# Configurar para iniciar automáticamente
systemctl enable redis-server
systemctl start redis-server

# Verificar
redis-cli ping
```

#### Instalar Node.js

```bash
# Instalar Node.js 18 LTS
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
apt install -y nodejs

# Verificar instalación
node -v
npm -v
```

### 3. Configurar Servicios

#### Configurar PHP-FPM

```bash
# Editar configuración PHP-FPM
nano /etc/php/8.2/fpm/pool.d/www.conf

# Configuraciones importantes:
# user = www-data
# group = www-data
# listen = /run/php/php8.2-fpm.sock
# pm = dynamic
# pm.max_children = 50
# pm.start_servers = 5
# pm.min_spare_servers = 5
# pm.max_spare_servers = 35

# Reiniciar PHP-FPM
systemctl restart php8.2-fpm
```

#### Configurar MySQL para Producción

```bash
# Editar configuración MySQL
nano /etc/mysql/mysql.conf.d/mysqld.cnf

# Configuraciones de producción:
# max_connections = 200
# innodb_buffer_pool_size = 2G  # 70% de RAM disponible
# innodb_log_file_size = 256M
# query_cache_size = 64M
# tmp_table_size = 64M
# max_heap_table_size = 64M

# Reiniciar MySQL
systemctl restart mysql
```

### 4. Configurar Usuario de Aplicación

```bash
# Crear usuario para la aplicación
adduser --system --group --home /var/www app

# Crear directorios
mkdir -p /var/www/html
mkdir -p /var/www/logs
mkdir -p /var/www/storage

# Asignar permisos
chown -R app:app /var/www
chmod -R 755 /var/www
```

### 5. Configurar Firewall

```bash
# Configurar UFW (Ubuntu Firewall)
ufw enable
ufw default deny incoming
ufw default allow outgoing

# Permitir conexiones necesarias
ufw allow ssh
ufw allow 'Nginx Full'
ufw allow 3306  # MySQL (solo si es necesario externamente)

# Verificar reglas
ufw status verbose
```

## Tips

### Optimización de Performance

- **Configurar OPcache**: Acelera ejecución de PHP
- **Ajustar worker_processes en Nginx**: Igual al número de CPU cores
- **Optimizar MySQL**: Usar mysqltuner para recomendaciones
- **Implementar HTTP/2**: Para mejor rendimiento web

### Seguridad en Servidores

- **Deshabilitar root login**: Usar usuarios con sudo
- **Cambiar puerto SSH**: Usar puerto diferente al 22
- **Fail2ban**: Instalar para proteger contra ataques de fuerza bruta
- **Updates automáticos**: Para parches de seguridad críticos

### Monitoreo del Sistema

- **htop**: Para monitoreo de procesos en tiempo real
- **iotop**: Para monitoreo de I/O del disco
- **netstat**: Para conexiones de red activas
- **df -h**: Para espacio en disco disponible

### Backup Automático

- **Programar mysqldump**: Para backups de base de datos
- **rsync**: Para sincronización de archivos
- **Almacenamiento externo**: No mantener backups solo en el servidor
- **Testing regular**: Verificar que los backups funcionen

## Ejemplos

### Configuración Nginx para Laravel + React

```nginx
# /etc/nginx/sites-available/app
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com www.example.com;
    root /var/www/html/public;
    index index.php index.html;

    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;

    # Frontend (React)
    location / {
        try_files $uri $uri/ /index.html;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Backend API (Laravel)
    location /api {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # PHP processing
    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Security: deny access to hidden files
    location ~ /\. {
        deny all;
    }
}
```

### Script de Configuración Inicial

```bash
#!/bin/bash
# setup-server.sh

set -e

echo "🚀 Configurando servidor de producción..."

# Update system
apt update && apt upgrade -y

# Install base packages
apt install -y curl wget git unzip software-properties-common

# Install Nginx
apt install -y nginx
systemctl enable nginx

# Install PHP 8.2
add-apt-repository ppa:ondrej/php -y
apt update
apt install -y php8.2-fpm php8.2-cli php8.2-mysql php8.2-xml \
php8.2-mbstring php8.2-curl php8.2-zip php8.2-intl \
php8.2-gd php8.2-bcmath php8.2-redis

# Install MySQL
debconf-set-selections <<< 'mysql-server mysql-server/root_password password temporarypass'
debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password temporarypass'
apt install -y mysql-server

# Install Redis
apt install -y redis-server
systemctl enable redis-server

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
apt install -y nodejs

# Configure firewall
ufw --force enable
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow 'Nginx Full'

echo "✅ Servidor configurado correctamente"
echo "🔧 Continúa con la configuración específica de la aplicación"
```

### PHP Opcache Configuration

```ini
; /etc/php/8.2/fpm/conf.d/10-opcache.ini
zend_extension=opcache.so
opcache.enable=1
opcache.enable_cli=1
opcache.memory_consumption=256
opcache.interned_strings_buffer=16
opcache.max_accelerated_files=10000
opcache.validate_timestamps=0
opcache.revalidate_freq=0
opcache.save_comments=1
```

### MySQL Production Configuration

```ini
# /etc/mysql/mysql.conf.d/production.cnf
[mysqld]
# Basic settings
max_connections = 200
thread_cache_size = 8
table_open_cache = 4000

# InnoDB settings
innodb_buffer_pool_size = 2G
innodb_log_file_size = 256M
innodb_flush_log_at_trx_commit = 2
innodb_flush_method = O_DIRECT

# Query cache
query_cache_type = 1
query_cache_size = 64M
query_cache_limit = 2M

# Temp tables
tmp_table_size = 64M
max_heap_table_size = 64M

# Logging
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 2
```

## Navegación

**Progreso en Despliegue y DevOps:**

- ✅ [Despliegue y DevOps](./despliegue-devops.md)
- ✅ **Configuración de Servidores de Producción** ← Estás aquí
- ⏭️ [CI/CD Pipelines con GitHub Actions](./ci-cd-pipelines-github-actions.md)
- ⏭️ [Docker y Contenedores](./docker-contenedores.md)
- ⏭️ [Monitoreo y Logging](./monitoreo-logging.md)
- ⏭️ [Backup y Recovery](./backup-recovery-strategies.md)
- ⏭️ [SSL y Configuraciones de Seguridad](./ssl-configuraciones-seguridad.md)

---

### Siguiente Paso

Continúa con
[**CI/CD Pipelines con GitHub Actions**](./ci-cd-pipelines-github-actions.md)

[⬅️ Despliegue y DevOps](./despliegue-devops.md) |
[🏠 README Principal](../../README.md) |
[➡️ CI/CD Pipelines con GitHub Actions](./ci-cd-pipelines-github-actions.md)
