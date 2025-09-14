# Step 8: Despliegue y DevOps

## ¿Qué es?

El despliegue y DevOps es la etapa donde se implementan las prácticas y
herramientas necesarias para llevar la aplicación desde el entorno de desarrollo
hasta producción de manera automatizada, segura y eficiente. Esta fase incluye
la configuración de infraestructura, pipelines de CI/CD, monitoreo, y
estrategias de mantenimiento.

## ¿Por qué es importante?

- **Automatización**: Reduce errores humanos y acelera el proceso de despliegue
- **Confiabilidad**: Garantiza que las aplicaciones funcionen correctamente en
  producción
- **Escalabilidad**: Permite que la infraestructura crezca según las necesidades
- **Seguridad**: Implementa medidas de protección desde la infraestructura hasta
  la aplicación
- **Monitoreo**: Facilita la detección temprana de problemas y optimización de
  rendimiento
- **Recuperación**: Establece estrategias de backup y disaster recovery

## ¿Qué debe incluir?

### Infraestructura y Servidores

- Proveedor cloud estándar: **DigitalOcean** (obligatorio por defecto). Todas
   las instrucciones y ejemplos en este documento asumen DigitalOcean como
   destino. Para backups y object storage usar **DigitalOcean Spaces** (S3-
   compatible).

- Nota de excepciones: El uso de **AWS** solo se permite mediante aprobación
   arquitectónica por requisitos empresariales o de compliance no cubiertos por
   DigitalOcean.

- Gestión de dominios y DNS (DigitalOcean DNS o proveedor externo apuntando a
   recursos de DigitalOcean)
- Configuración de Load Balancers (DigitalOcean Load Balancers o DO-managed)
- Implementación de CDN para assets estáticos (Cloudflare u otros; assets en
   Spaces)

### Automatización CI/CD

- Pipelines de integración continua con GitHub Actions
- Despliegues automatizados por entornos
- Testing automático antes del despliegue
- Rollback automático en caso de errores

### Contenedorización

- Dockerización de aplicaciones frontend y backend
- Orquestación con Docker Compose
- Gestión de imágenes y registros
- Configuración de volúmenes y redes

### Seguridad

- Certificados SSL/TLS
- Configuración de firewalls
- Gestión de secretos y variables de entorno
- Hardening de servidores

### Monitoreo y Logging

- Implementación de herramientas de monitoreo
- Centralización de logs
- Alertas y notificaciones
- Métricas de rendimiento

### Backup y Recovery

- Estrategias de respaldo automático
- Procedimientos de recuperación
- Testing de backups
- Documentación de disaster recovery

## ¿Qué debo hacer?

### 1. Configurar Servidores de Producción

```bash
# Preparar servidor con las dependencias necesarias
# Configurar PHP 8.2+, Nginx, MySQL 8.0+, Node.js 18+
```

### 2. Implementar Pipelines CI/CD

- Configurar GitHub Actions para despliegue automático
- Crear workflows para diferentes entornos
- Implementar testing automático
- Configurar notificaciones de deploy

### 3. Dockerizar Aplicaciones

- Crear Dockerfiles optimizados para frontend y backend
- Configurar Docker Compose para orquestación
- Optimizar imágenes para producción
- Implementar multi-stage builds

### 4. Configurar Seguridad

- Instalar y configurar certificados SSL
- Configurar firewalls y reglas de acceso
- Implementar gestión segura de secretos
- Aplicar hardening de seguridad

### 5. Implementar Monitoreo

- Configurar herramientas de monitoreo de aplicación
- Centralizar logs de aplicación y sistema
- Crear dashboards de métricas
- Configurar alertas críticas

### 6. Establecer Estrategias de Backup

- Automatizar backups de base de datos
- Configurar respaldos de archivos críticos
- Documentar procedimientos de recovery
- Probar regularmente los backups

## Tips

### Mejores Prácticas de DevOps

- **Infraestructura como Código**: Usar herramientas como Terraform o
  CloudFormation
- **Despliegues Blue-Green**: Minimizar downtime durante actualizaciones
- **Feature Flags**: Controlar el rollout de nuevas funcionalidades
- **Monitoring Proactivo**: No esperar a que los usuarios reporten problemas

### Seguridad en Producción

- **Principio de Menor Privilegio**: Solo los permisos necesarios
- **Actualizaciones Regulares**: Mantener sistemas y dependencias actualizadas
- **Auditorías de Seguridad**: Revisiones periódicas de configuraciones
- **Respuesta a Incidentes**: Plan claro para manejar brechas de seguridad

### Optimización de Performance

- **Caché**: Implementar múltiples niveles de caché
- **CDN**: Distribuir contenido estático globalmente
- **Compresión**: Gzip/Brotli para assets web
- **Lazy Loading**: Cargar recursos solo cuando se necesiten

### Gestión de Costos

- **Right-sizing**: Dimensionar recursos según uso real
- **Auto-scaling**: Escalar automáticamente según demanda
- **Reserved Instances**: Planificar capacidad a largo plazo
- **Monitoring de Costos**: Alertas de presupuesto

## Ejemplos

### Pipeline de GitHub Actions

```yaml
name: Deploy to Production
on:
   push:
      branches: [main]

jobs:
   deploy:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
         - name: Deploy to server
           run: |
              # Scripts de despliegue
```

### Dockerfile para Laravel

```dockerfile
FROM php:8.2-fpm-alpine
WORKDIR /var/www
COPY . .
RUN composer install --optimize-autoloader --no-dev
EXPOSE 9000
CMD ["php-fpm"]
```

### Docker Compose para Producción

```yaml
version: "3.8"
services:
   backend:
      build: ./backend
      environment:
         - APP_ENV=production
      volumes:
         - app_storage:/var/www/storage

   frontend:
      build: ./frontend
      ports:
         - "80:80"

   database:
      image: mysql:8.0
      environment:
         - MYSQL_ROOT_PASSWORD=${DB_PASSWORD}
      volumes:
         - db_data:/var/lib/mysql
```

### Configuración de Nginx

```nginx
server {
    listen 443 ssl;
    server_name example.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    
    location / {
        proxy_pass http://frontend:80;
    }
    
    location /api {
        proxy_pass http://backend:9000;
    }
}
```

## Navegación

[⬅️ Etapa 7: Auditoría de Calidad de Código](../step_07/auditoria-calidad-codigo.md)
| [🏠 README Principal](../../README.md) |
[Configuración de Servidores de Producción ➡️](./configuracion-servidores-produccion.md)
