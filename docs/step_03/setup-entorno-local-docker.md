# Setup de Entorno Local con Docker

## Conocimientos involucrados
- Uso de Docker y Docker Compose
- Configuración de contenedores
- Orquestación de servicios
- Resolución de problemas de entorno

## Responsable de la etapa
- Equipo de DevOps

## ¿Qué es el Setup de Entorno Local con Docker?

El **Setup de Entorno Local con Docker** es la configuración de un entorno de
desarrollo **completamente containerizado** que permite a todos los miembros del
equipo trabajar con **exactamente la misma configuración** de servicios,
dependencias y versiones. Utiliza **Docker** y **Docker Compose** para orquestar
múltiples contenedores que replican el stack tecnológico completo del proyecto.

Este enfoque elimina los problemas del clásico _"en mi máquina funciona"_ al
garantizar que **todos los desarrolladores** trabajen en un entorno **idéntico**
que además es **similar al de producción**. Incluye la configuración de
contenedores para el **backend Laravel**, **frontend React**, **base de datos
MySQL**, **Redis**, **servidor web** y todas las herramientas de desarrollo
necesarias.

La configuración está optimizada para soportar la arquitectura **Clean
Architecture + DDD** y facilita el desarrollo con **hot-reloading**,
**debugging**, **testing** y **múltiples ambientes** (development, testing,
staging) de manera simple y consistente.

## ¿Por qué es Importante?

### 🎯 **Consistencia Absoluta del Entorno**

- Elimina diferencias entre entornos de desarrollo de diferentes miembros del
  equipo
- Garantiza que todos trabajen con las mismas versiones de PHP, Node.js, MySQL,
  etc.
- Reduce significativamente los bugs relacionados con diferencias de entorno
- Facilita la incorporación inmediata de nuevos desarrolladores al proyecto

### ⚡ **Productividad y Eficiencia**

- Setup inicial completo en minutos con un solo comando
- Hot-reloading automático para desarrollo ágil
- Reinicio rápido de servicios sin afectar el sistema host
- Isolation completa que evita conflictos con otros proyectos

### 🔧 **Flexibilidad y Escalabilidad**

- Fácil agregado de nuevos servicios (Elasticsearch, Memcached, etc.)
- Configuración rápida de múltiples ambientes (dev, test, staging)
- Switching fácil entre versiones de servicios para testing
- Replicación exacta del entorno de producción en local

### 🛡️ **Aislamiento y Seguridad**

- Completa isolation entre proyectos
- No contamina el sistema host con dependencias
- Fácil cleanup y reset completo del entorno
- Control granular de networking y permisos

## ¿Qué debe incluir?

### 🐳 **Configuración Docker Principal**

#### **1. Dockerfile para Backend Laravel**

```dockerfile
# Dockerfile.laravel
FROM php:8.2-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    default-mysql-client

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www/html

# Copy application code
COPY ./backend /var/www/html

# Install dependencies
RUN composer install --no-dev --optimize-autoloader

# Set permissions
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 755 /var/www/html/storage \
    && chmod -R 755 /var/www/html/bootstrap/cache

EXPOSE 9000

CMD ["php-fpm"]
```

#### **2. Dockerfile para Frontend React**

```dockerfile
# Dockerfile.react
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY ./frontend/package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY ./frontend .

# Build application
RUN npm run build

# Install serve for production
RUN npm install -g serve

EXPOSE 3000

# Development command
CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]
```

#### **3. Docker Compose Principal**

```yaml
# docker-compose.yml
version: "3.8"

services:
   # Backend Laravel
   laravel:
      build:
         context: .
         dockerfile: Dockerfile.laravel
      container_name: ${APP_NAME}_laravel
      restart: unless-stopped
      working_dir: /var/www/html
      volumes:
         - ./backend:/var/www/html
         - ./docker/php/local.ini:/usr/local/etc/php/conf.d/local.ini
      networks:
         - app-network
      depends_on:
         - mysql
         - redis

   # Frontend React
   react:
      build:
         context: .
         dockerfile: Dockerfile.react
      container_name: ${APP_NAME}_react
      restart: unless-stopped
      ports:
         - "3000:3000"
      volumes:
         - ./frontend:/app
         - /app/node_modules
      environment:
         - CHOKIDAR_USEPOLLING=true
      networks:
         - app-network
      depends_on:
         - laravel

   # Nginx Web Server
   nginx:
      image: nginx:alpine
      container_name: ${APP_NAME}_nginx
      restart: unless-stopped
      ports:
         - "80:80"
         - "443:443"
      volumes:
         - ./backend:/var/www/html
         - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
         - ./docker/nginx/ssl:/etc/nginx/ssl
      networks:
         - app-network
      depends_on:
         - laravel

   # MySQL Database
   mysql:
      image: mysql:8.0
      container_name: ${APP_NAME}_mysql
      restart: unless-stopped
      environment:
         MYSQL_DATABASE: ${DB_DATABASE}
         MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
         MYSQL_PASSWORD: ${DB_PASSWORD}
         MYSQL_USER: ${DB_USERNAME}
      ports:
         - "3306:3306"
      volumes:
         - mysql_data:/var/lib/mysql
         - ./docker/mysql/my.cnf:/etc/mysql/my.cnf
         - ./docker/mysql/init:/docker-entrypoint-initdb.d
      networks:
         - app-network

   # Redis Cache
   redis:
      image: redis:7-alpine
      container_name: ${APP_NAME}_redis
      restart: unless-stopped
      ports:
         - "6379:6379"
      volumes:
         - redis_data:/data
         - ./docker/redis/redis.conf:/usr/local/etc/redis/redis.conf
      networks:
         - app-network

   # PHPMyAdmin (Development only)
   phpmyadmin:
      image: phpmyadmin/phpmyadmin
      container_name: ${APP_NAME}_phpmyadmin
      restart: unless-stopped
      environment:
         PMA_HOST: mysql
         MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
      ports:
         - "8080:80"
      networks:
         - app-network
      depends_on:
         - mysql
      profiles:
         - dev

   # Mailhog (Development only)
   mailhog:
      image: mailhog/mailhog
      container_name: ${APP_NAME}_mailhog
      restart: unless-stopped
      ports:
         - "8025:8025"
         - "1025:1025"
      networks:
         - app-network
      profiles:
         - dev

networks:
   app-network:
      driver: bridge

volumes:
   mysql_data:
      driver: local
   redis_data:
      driver: local
```

### ⚙️ **Configuraciones de Servicios**

#### **1. Configuración de Nginx**

```nginx
# docker/nginx/default.conf
server {
    listen 80;
    listen 443 ssl http2;
    
    server_name localhost;
    root /var/www/html/public;
    index index.php;

    # SSL Configuration
    ssl_certificate /etc/nginx/ssl/localhost.crt;
    ssl_certificate_key /etc/nginx/ssl/localhost.key;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;

    # Laravel routes
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # PHP-FPM
    location ~ \.php$ {
        fastcgi_pass laravel:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }

    # Static files
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security
    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

#### **2. Configuración de PHP**

```ini
# docker/php/local.ini
upload_max_filesize=100M
post_max_size=100M
memory_limit=512M
max_execution_time=300
max_input_vars=3000

# Development settings
display_errors=On
display_startup_errors=On
error_reporting=E_ALL
log_errors=On
error_log=/var/log/php_errors.log

# Opcache settings
opcache.enable=1
opcache.revalidate_freq=0
opcache.validate_timestamps=1
opcache.max_accelerated_files=10000
opcache.memory_consumption=192
opcache.max_wasted_percentage=10
```

#### **3. Configuración de MySQL**

```ini
# docker/mysql/my.cnf
[mysqld]
general_log = 1
general_log_file = /var/lib/mysql/general.log

# Performance
innodb_buffer_pool_size = 256M
innodb_log_file_size = 64M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50

# Character set
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

# Networking
max_connections = 200
max_allowed_packet = 64M

[mysql]
default-character-set = utf8mb4

[client]
default-character-set = utf8mb4
```

#### **4. Configuración de Redis**

```conf
# docker/redis/redis.conf
# Network
bind 0.0.0.0
protected-mode no
port 6379

# Memory
maxmemory 256mb
maxmemory-policy allkeys-lru

# Persistence
save 900 1
save 300 10
save 60 10000

# Logging
loglevel notice
logfile /var/log/redis/redis-server.log

# Performance
tcp-keepalive 300
timeout 0
```

### 🛠️ **Scripts de Automatización**

#### **1. Script de Setup Inicial**

```bash
#!/bin/bash
# scripts/setup.sh

echo "🐳 Configurando entorno Docker..."

# Crear directorios necesarios
mkdir -p docker/{nginx,php,mysql,redis}
mkdir -p docker/nginx/ssl
mkdir -p docker/mysql/init
mkdir -p logs/{nginx,php,mysql,redis}

# Generar certificados SSL para desarrollo
if [ ! -f docker/nginx/ssl/localhost.crt ]; then
    echo "🔐 Generando certificados SSL..."
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout docker/nginx/ssl/localhost.key \
        -out docker/nginx/ssl/localhost.crt \
        -subj "/C=CL/ST=Santiago/L=Santiago/O=Development/CN=localhost"
fi

# Copiar archivo .env si no existe
if [ ! -f backend/.env ]; then
    echo "📝 Configurando archivo .env..."
    cp backend/.env.example backend/.env
    
    # Configurar variables Docker
    sed -i 's/DB_HOST=127.0.0.1/DB_HOST=mysql/' backend/.env
    sed -i 's/REDIS_HOST=127.0.0.1/REDIS_HOST=redis/' backend/.env
    sed -i 's/MAIL_HOST=mailhog/MAIL_HOST=mailhog/' backend/.env
    sed -i 's/MAIL_PORT=1025/MAIL_PORT=1025/' backend/.env
fi

echo "✅ Setup completado. Ejecuta 'docker-compose up -d' para iniciar."
```

#### **2. Script de Desarrollo**

```bash
#!/bin/bash
# scripts/dev.sh

# Función para mostrar ayuda
show_help() {
    echo "🚀 Script de desarrollo Docker"
    echo ""
    echo "Uso: ./scripts/dev.sh [comando]"
    echo ""
    echo "Comandos disponibles:"
    echo "  up              Iniciar todos los servicios"
    echo "  down            Detener todos los servicios"
    echo "  restart         Reiniciar todos los servicios"
    echo "  logs [service]  Ver logs de un servicio"
    echo "  shell [service] Acceder al shell de un servicio"
    echo "  install         Instalar dependencias"
    echo "  migrate         Ejecutar migraciones"
    echo "  seed            Ejecutar seeders"
    echo "  test            Ejecutar tests"
    echo "  artisan [cmd]   Ejecutar comando artisan"
    echo "  npm [cmd]       Ejecutar comando npm en frontend"
    echo "  clean           Limpiar contenedores y volúmenes"
}

# Verificar si Docker está corriendo
check_docker() {
    if ! docker info >/dev/null 2>&1; then
        echo "❌ Docker no está corriendo. Por favor inicia Docker Desktop."
        exit 1
    fi
}

# Comando principal
case $1 in
    "up")
        check_docker
        echo "🚀 Iniciando servicios..."
        docker-compose --profile dev up -d
        echo "✅ Servicios iniciados."
        echo "📱 Frontend: http://localhost:3000"
        echo "🌐 Backend: http://localhost"
        echo "🗄️ phpMyAdmin: http://localhost:8080"
        echo "📧 Mailhog: http://localhost:8025"
        ;;
    
    "down")
        echo "🛑 Deteniendo servicios..."
        docker-compose down
        ;;
    
    "restart")
        echo "🔄 Reiniciando servicios..."
        docker-compose restart
        ;;
    
    "logs")
        if [ -z "$2" ]; then
            docker-compose logs -f
        else
            docker-compose logs -f $2
        fi
        ;;
    
    "shell")
        if [ -z "$2" ]; then
            echo "❌ Especifica el servicio: laravel, react, mysql, redis"
            exit 1
        fi
        docker-compose exec $2 /bin/bash
        ;;
    
    "install")
        echo "📦 Instalando dependencias..."
        docker-compose exec laravel composer install
        docker-compose exec react npm install
        ;;
    
    "migrate")
        echo "🗄️ Ejecutando migraciones..."
        docker-compose exec laravel php artisan migrate
        ;;
    
    "seed")
        echo "🌱 Ejecutando seeders..."
        docker-compose exec laravel php artisan db:seed
        ;;
    
    "test")
        echo "🧪 Ejecutando tests..."
        docker-compose exec laravel php artisan test
        docker-compose exec react npm run test
        ;;
    
    "artisan")
        shift
        docker-compose exec laravel php artisan $@
        ;;
    
    "npm")
        shift
        docker-compose exec react npm $@
        ;;
    
    "clean")
        echo "🧹 Limpiando contenedores y volúmenes..."
        docker-compose down -v
        docker system prune -f
        ;;
    
    *)
        show_help
        ;;
esac
```

### 📝 **Archivos de Configuración del Proyecto**

#### **1. Archivo .env.docker**

```env
# .env.docker - Variables específicas para Docker
APP_NAME=mi_proyecto
APP_ENV=local
APP_KEY=base64:generated_key_here
APP_DEBUG=true
APP_URL=http://localhost

# Database
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=mi_proyecto_db
DB_USERNAME=mi_proyecto_user
DB_PASSWORD=mi_proyecto_password

# Redis
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

# Mail
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

# Queue
QUEUE_CONNECTION=redis

# Session
SESSION_DRIVER=redis
SESSION_LIFETIME=120

# Cache
CACHE_DRIVER=redis
```

#### **2. Configuración de VSCode para Docker**

```json
// .vscode/settings.json
{
   "php.executablePath": "docker-compose exec laravel php",
   "php.validate.executablePath": "docker-compose exec laravel php",
   "phpcs.executablePath": "docker-compose exec laravel ./vendor/bin/phpcs",
   "phpcbf.executablePath": "docker-compose exec laravel ./vendor/bin/phpcbf",
   "phpmd.executablePath": "docker-compose exec laravel ./vendor/bin/phpmd",

   "eslint.workingDirectories": ["./frontend"],
   "typescript.preferences.includePackageJsonAutoImports": "auto",

   "docker.compose.files": ["docker-compose.yml"],
   "docker.compose.projectName": "mi_proyecto",

   "remote.containers.defaultExtensions": [
      "ms-vscode.vscode-typescript-next",
      "bradlc.vscode-tailwindcss",
      "bmewburn.vscode-intelephense-client"
   ]
}
```

## ¿Qué debo hacer?

### 🎯 **Fase 1: Preparación del Entorno (1-2 días)**

#### **Paso 1: Instalación de Dependencias**

1. **Instalar Docker Desktop**
   ```bash
   # Windows/Mac: Descargar desde docker.com
   # Linux (Ubuntu):
   curl -fsSL https://get.docker.com -o get-docker.sh
   sh get-docker.sh
   sudo usermod -aG docker $USER
   ```

2. **Verificar instalación**
   ```bash
   docker --version
   docker-compose --version
   ```

3. **Configurar recursos de Docker**
   - CPU: Mínimo 4 cores
   - RAM: Mínimo 8GB (recomendado 16GB)
   - Almacenamiento: Mínimo 50GB disponibles

#### **Paso 2: Estructura de Archivos**

1. **Crear estructura Docker**
   ```bash
   mkdir -p docker/{nginx,php,mysql,redis}
   mkdir -p docker/nginx/ssl
   mkdir -p scripts
   ```

2. **Configurar permisos en Linux/Mac**
   ```bash
   chmod +x scripts/*.sh
   ```

### 🚀 **Fase 2: Configuración Base (2-3 días)**

#### **Paso 3: Archivos Docker Principales**

1. **Crear Dockerfiles**
   - Configurar `Dockerfile.laravel` con PHP 8.2
   - Configurar `Dockerfile.react` con Node.js 18
   - Optimizar layers para mejor caching

2. **Configurar docker-compose.yml**
   - Definir todos los servicios necesarios
   - Configurar networks y volumes
   - Establecer dependencias entre servicios

3. **Configurar servicios auxiliares**
   - Nginx como reverse proxy
   - Redis para cache y sesiones
   - phpMyAdmin para desarrollo
   - Mailhog para testing de emails

#### **Paso 4: Scripts de Automatización**

1. **Script de setup inicial**
   ```bash
   ./scripts/setup.sh
   ```

2. **Script de desarrollo**
   ```bash
   ./scripts/dev.sh up
   ```

### ⚙️ **Fase 3: Configuración Avanzada (3-4 días)**

#### **Paso 5: Optimización y Performance**

1. **Configurar PHP optimizado**
   - OPcache habilitado
   - Memory limits apropiados
   - Configuración de desarrollo vs producción

2. **Optimizar MySQL**
   - InnoDB buffer pool
   - Query cache configurado
   - Logging para development

3. **Configurar Redis**
   - Políticas de memoria
   - Persistencia configurada
   - Conexiones optimizadas

#### **Paso 6: Herramientas de Desarrollo**

1. **Configurar debugging**
   - Xdebug en PHP
   - Source maps en React
   - Breakpoints remotos

2. **Hot reloading**
   - Laravel con file watching
   - React con Vite HMR
   - Auto-restart en cambios

### 🧪 **Fase 4: Testing y Validación (2-3 días)**

#### **Paso 7: Validación del Entorno**

1. **Tests de conectividad**
   ```bash
   ./scripts/dev.sh test
   ```

2. **Verificar servicios**
   - Base de datos accesible
   - Cache funcionando
   - Mail catching trabajando

3. **Performance testing**
   - Tiempo de startup < 60 segundos
   - Hot reload < 3 segundos
   - Queries optimizadas

#### **Paso 8: Documentación del Entorno**

1. **README específico**
   - Instrucciones de setup
   - Comandos comunes
   - Troubleshooting

2. **Scripts de troubleshooting**
   - Diagnóstico automático
   - Reset completo
   - Backup/restore

## Tips

### 💡 **Optimización y Performance**

- **Layer Caching**: Ordena los comandos Dockerfile para maximizar cache hits
- **Multi-stage builds**: Usa builds separados para development y production
- **Volume optimization**: Monta solo los directorios necesarios para mejor
  performance
- **Resource limits**: Configura limits apropiados para evitar consumo excesivo

### 🔧 **Development Experience**

- **File watching**: Configura polling para sistemas de archivos que no soportan
  inotify
- **Port mapping**: Usa puertos estándar para fácil acceso (3000, 8080, etc.)
- **Environment switching**: Crea profiles para diferentes modos (dev, test,
  prod)

### 🛡️ **Seguridad y Mejores Prácticas**

- **Non-root users**: Ejecuta contenedores con usuarios no privilegiados
- **Secret management**: Usa Docker secrets para datos sensibles
- **Network isolation**: Configura networks separadas para diferentes servicios
- **Regular updates**: Mantén imágenes base actualizadas

### 🚨 **Troubleshooting Común**

- **Puertos ocupados**: Verifica que los puertos no estén siendo usados por
  otros servicios
- **Permisos de archivos**: Configura ownership correcto en volúmenes
- **Memory issues**: Monitora uso de memoria y ajusta limits según necesidad
- **Database connections**: Verifica que las variables de entorno sean correctas

## Ejemplos

### 📋 **Ejemplo 1: Setup Completo desde Cero**

```bash
# 1. Clonar el repositorio
git clone https://github.com/usuario/mi-proyecto.git
cd mi-proyecto

# 2. Ejecutar setup inicial
./scripts/setup.sh

# 3. Iniciar servicios
./scripts/dev.sh up

# 4. Instalar dependencias
./scripts/dev.sh install

# 5. Configurar base de datos
./scripts/dev.sh migrate
./scripts/dev.sh seed

# 6. Verificar que todo funciona
curl http://localhost/api/health
curl http://localhost:3000
```

### 📋 **Ejemplo 2: Workflow de Desarrollo Diario**

```bash
# Iniciar día de trabajo
./scripts/dev.sh up

# Ver logs en tiempo real
./scripts/dev.sh logs

# Ejecutar tests
./scripts/dev.sh test

# Acceder al contenedor de Laravel
./scripts/dev.sh shell laravel

# Ejecutar comando artisan
./scripts/dev.sh artisan make:controller UserController

# Instalar nueva dependencia npm
./scripts/dev.sh npm install axios

# Finalizar día de trabajo
./scripts/dev.sh down
```

### 📋 **Ejemplo 3: Configuración de Nuevo Servicio**

```yaml
# Agregar Elasticsearch al docker-compose.yml
services:
   elasticsearch:
      image: docker.elastic.co/elasticsearch/elasticsearch:8.8.0
      container_name: ${APP_NAME}_elasticsearch
      environment:
         - discovery.type=single-node
         - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
         - xpack.security.enabled=false
      ports:
         - "9200:9200"
      volumes:
         - elasticsearch_data:/usr/share/elasticsearch/data
      networks:
         - app-network

volumes:
   elasticsearch_data:
      driver: local
```

### 📋 **Ejemplo 4: Debugging con Xdebug**

```ini
# docker/php/xdebug.ini
[xdebug]
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
xdebug.log=/var/log/xdebug.log
```

```json
// .vscode/launch.json
{
   "version": "0.2.0",
   "configurations": [
      {
         "name": "Listen for Xdebug",
         "type": "php",
         "request": "launch",
         "port": 9003,
         "pathMappings": {
            "/var/www/html": "${workspaceFolder}/backend"
         }
      }
   ]
}
```

---

## Navegación

[⬅️ Configuración de repositorios Git y GitHub](./configuracion-repositorios-git.md)
| [🏠 README Principal](../../README.md) |
[Configuración de herramientas de CI/CD con GitHub Actions ➡️](./configuracion-cicd-github-actions.md)
