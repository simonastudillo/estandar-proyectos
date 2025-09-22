# Cómo Documentar Procesos

## Conocimientos Involucrados
- Metodologías de documentación
- Herramientas de gestión de conocimiento
- Estandarización de procesos

## Responsable
- Equipo de gestión de conocimiento

## ¿Qué es?

**Cómo documentar procesos** es una metodología sistemática para capturar,
estructurar y mantener la información sobre workflows, procedimientos y
prácticas operativas del proyecto. En el contexto de Clean Architecture + DDD,
esto incluye documentar procesos de desarrollo, deployment, mantenimiento, y
flujos de negocio críticos que permiten la continuidad operativa y el knowledge
transfer efectivo.

Esta práctica garantiza que los procesos críticos del proyecto estén claramente
definidos, sean reproducibles y puedan ser ejecutados por cualquier miembro del
equipo, independientemente de su experiencia previa con el proyecto específico.

## ¿Por qué es importante?

### 📋 **Consistencia Operativa**

- Asegura que los procesos se ejecuten de manera uniforme
- Reduce errores humanos mediante procedimientos estandarizados
- Facilita la replicación de procesos en diferentes entornos

### 🎯 **Transferencia de Conocimiento**

- Permite que nuevos miembros del equipo sean productivos rápidamente
- Preserva el conocimiento crítico ante rotación de personal
- Facilita el escalamiento del equipo y la distribución de responsabilidades

### 🔍 **Trazabilidad y Auditabilidad**

- Documenta decisiones y cambios en procesos
- Facilita auditorías y cumplimiento de estándares
- Permite identificar puntos de mejora en workflows existentes

### ⚡ **Eficiencia y Automatización**

- Identifica oportunidades de automatización
- Elimina pasos redundantes o innecesarios
- Optimiza tiempos de ejecución mediante claridad en procedimientos

### 🛡️ **Continuidad de Negocio**

- Asegura que los procesos críticos puedan continuar sin interrupciones
- Facilita la recuperación ante incidentes o cambios de personal
- Mantiene la calidad del servicio independientemente del operador

## ¿Qué debe incluir?

### Estructura Base del Proceso

- Objetivo y alcance del proceso
- Prerequisitos y condiciones iniciales
- Pasos detallados con comandos específicos
- Resultados esperados y criterios de éxito
- Manejo de errores y troubleshooting

### Información Contextual

- Roles y responsabilidades involucrados
- Dependencias con otros procesos
- Frecuencia de ejecución
- Impacto y criticidad del proceso
- Historial de cambios y versiones

### Elementos Técnicos

- Comandos exactos y scripts
- Configuraciones específicas
- Variables de entorno requeridas
- Herramientas y accesos necesarios
- Validaciones y checkpoints

### Documentación de Excepciones

- Casos especiales y variaciones
- Procedimientos de rollback
- Escalamiento ante problemas
- Contactos y recursos adicionales

## ¿Qué debo hacer?

### 1. **Identificar Procesos Críticos**

Catalogar todos los procesos que requieren documentación:

```bash
# Lista de procesos críticos a documentar
echo "📋 Procesos identificados para documentación:"
echo "- Setup de entorno de desarrollo"
echo "- Proceso de deployment"
echo "- Manejo de hotfixes"
echo "- Backup y recovery"
echo "- Onboarding de nuevos desarrolladores"
echo "- Release y versionado"
echo "- Configuración de CI/CD"
echo "- Troubleshooting común"
```

### 2. **Aplicar Template Estándar**

Usar formato consistente para todos los procesos documentados.

### 3. **Validar y Mantener**

Verificar regularmente que la documentación esté actualizada y sea funcional.

## Metodología de Documentación

## 📝 **Template Estándar para Procesos**

### Estructura Base (Obligatoria)

````markdown
# [Nombre del Proceso]

## 🎯 Objetivo

[Descripción clara de qué logra este proceso]

## 📋 Prerequisitos

- [ ] [Requisito 1]
- [ ] [Requisito 2]
- [ ] [Accesos necesarios]

## ⚡ Pasos de Ejecución

### Paso 1: [Nombre descriptivo]

```bash
# Comando específico
comando --parametro valor
```
````

**Resultado esperado**: [Descripción] **Validación**: [Cómo verificar que
funcionó]

### Paso 2: [Siguiente paso]

[Continuación...]

## ✅ Criterios de Éxito

- [ ] [Resultado 1 verificable]
- [ ] [Resultado 2 verificable]

## 🚨 Troubleshooting

### Error común 1

**Síntoma**: [Descripción] **Solución**: [Pasos específicos]

## 📞 Contactos

- **Responsable**: [Persona]
- **Escalamiento**: [Siguiente nivel]

````
## 🔄 **Tipos de Procesos por Categoría**

### 1. Procesos de Desarrollo

**Setup de Entorno Local**
```markdown
# Setup de Entorno de Desarrollo Local

## 🎯 Objetivo
Configurar un entorno de desarrollo funcional para el proyecto desde cero.

## 📋 Prerequisitos
- [ ] Docker Desktop instalado
- [ ] Git configurado con SSH keys
- [ ] Node.js 18+ instalado
- [ ] PHP 8.2+ instalado
- [ ] Composer instalado globalmente

## ⚡ Pasos de Ejecución

### Paso 1: Clonar Repositorio
```bash
git clone git@github.com:organization/project.git
cd project
````

**Resultado esperado**: Código fuente descargado **Validación**: `ls -la`
muestra archivos del proyecto

### Paso 2: Configurar Variables de Entorno

```bash
cp .env.example .env
# Editar .env con configuraciones locales
vim .env
```

**Resultado esperado**: Archivo .env configurado **Validación**:
`grep -v '^#' .env | grep -v '^$'` muestra variables configuradas

### Paso 3: Levantar Servicios con Docker

```bash
docker-compose up -d
```

**Resultado esperado**: Todos los servicios ejecutándose **Validación**:
`docker-compose ps` muestra todos los servicios "Up"

### Paso 4: Instalar Dependencias Backend

```bash
docker-compose exec app composer install
docker-compose exec app php artisan key:generate
docker-compose exec app php artisan migrate:fresh --seed
```

**Resultado esperado**: Dependencias instaladas y DB configurada **Validación**:
`docker-compose exec app php artisan route:list` muestra rutas

### Paso 5: Instalar Dependencias Frontend

```bash
docker-compose exec frontend npm install
docker-compose exec frontend npm run dev
```

**Resultado esperado**: Frontend ejecutándose en desarrollo **Validación**:
Navegador en `http://localhost:3000` muestra aplicación

## ✅ Criterios de Éxito

- [ ] Todos los servicios Docker ejecutándose sin errores
- [ ] Backend responde en `http://localhost:8000/api/health`
- [ ] Frontend carga correctamente en `http://localhost:3000`
- [ ] Base de datos contiene datos de seed
- [ ] Tests básicos pasan: `npm run test` y `php artisan test`

## 🚨 Troubleshooting

### Error: "Port already in use"

**Síntoma**: Docker compose falla por puertos ocupados **Solución**:

```bash
# Verificar puertos ocupados
netstat -tulpn | grep :3000
# Cambiar puertos en docker-compose.yml o matar procesos
```

### Error: "Permission denied" en volumes

**Síntoma**: Docker no puede escribir en volumes **Solución**:

```bash
# Linux/Mac: Corregir permisos
sudo chown -R $USER:$USER .
# Windows: Verificar compartir drives en Docker Desktop
```

## 📞 Contactos

- **Responsable**: Tech Lead del proyecto
- **Escalamiento**: Arquitecto de Sistemas
- **Documentación**: [Link a docs adicionales]

````
### 2. Procesos de Deployment

**Deployment a Producción**
```markdown
# Deployment a Producción

## 🎯 Objetivo
Desplegar una nueva versión del sistema a producción de forma segura y con rollback disponible.

## 📋 Prerequisitos
- [ ] Branch `main` con última versión estable
- [ ] Todos los tests CI/CD pasando
- [ ] Approval del Tech Lead y Product Owner
- [ ] Backup de base de datos actual
- [ ] Ventana de mantenimiento comunicada (si aplica)

## ⚡ Pasos de Ejecución

### Paso 1: Preparación Pre-Deploy
```bash
# Verificar estado del branch main
git checkout main
git pull origin main
git log --oneline -5

# Crear tag de versión
git tag -a v1.2.3 -m "Release v1.2.3"
git push origin v1.2.3
````

**Resultado esperado**: Tag creado y pusheado **Validación**:
`git tag -l | tail -5` muestra el nuevo tag

### Paso 2: Backup de Base de Datos

```bash
# Conectar al servidor de producción
ssh deploy@production-server

# Crear backup
mysqldump -u root -p laravel_prod > backup_$(date +%Y%m%d_%H%M%S).sql
ls -la backup_*.sql | tail -1
```

**Resultado esperado**: Backup creado exitosamente **Validación**: Archivo de
backup tiene tamaño > 0 bytes

### Paso 3: Deploy Automático via GitHub Actions

```bash
# Trigger deploy workflow
gh workflow run deploy-production.yml --ref v1.2.3

# Monitorear progreso
gh run list --workflow=deploy-production.yml --limit=1
```

**Resultado esperado**: Workflow ejecutándose **Validación**: Status
"in_progress" en GitHub Actions

### Paso 4: Verificación Post-Deploy

```bash
# Health check automático
curl -f https://api.production.com/health

# Verificar versión desplegada
curl https://api.production.com/version
```

**Resultado esperado**: API responde con nueva versión **Validación**: Versión
devuelta coincide con tag desplegado

### Paso 5: Smoke Tests en Producción

```bash
# Ejecutar tests críticos
./scripts/smoke-tests-production.sh
```

**Resultado esperado**: Todos los smoke tests pasan **Validación**: Script
retorna exit code 0

## ✅ Criterios de Éxito

- [ ] Nueva versión responde correctamente
- [ ] Smoke tests pasan al 100%
- [ ] Métricas de performance dentro de rangos normales
- [ ] No hay errores críticos en logs
- [ ] Funcionalidades principales verificadas manualmente

## 🚨 Rollback (Si es necesario)

### Procedimiento de Rollback

```bash
# Rollback automático a versión anterior
kubectl rollout undo deployment/app-backend
kubectl rollout undo deployment/app-frontend

# O rollback a versión específica
git checkout v1.2.2
./scripts/deploy-production.sh --force
```

### Restaurar Base de Datos (Solo si hay cambios de schema)

```bash
# Restaurar desde backup
mysql -u root -p laravel_prod < backup_20231201_140530.sql
```

## 📞 Contactos

- **Responsable**: DevOps Engineer
- **Escalamiento**: CTO / VP Engineering
- **Guardia 24/7**: [Número de emergencia]

````
### 3. Procesos de Mantenimiento

**Actualización de Dependencias**
```markdown
# Actualización de Dependencias

## 🎯 Objetivo
Mantener las dependencias del proyecto actualizadas de forma segura.

## 📋 Prerequisitos
- [ ] Entorno de desarrollo funcional
- [ ] Branch feature creado desde main
- [ ] Tests funcionando antes de actualizar

## ⚡ Pasos de Ejecución

### Paso 1: Análisis de Dependencias Desactualizadas
```bash
# Backend (PHP/Composer)
composer outdated

# Frontend (Node.js/npm)
npm outdated
````

### Paso 2: Categorizar Actualizaciones

```bash
# Crear reporte de análisis
echo "## Análisis de Dependencias $(date)" > update-report.md
echo "" >> update-report.md
echo "### Actualizaciones Críticas (Seguridad)" >> update-report.md
composer audit >> update-report.md
npm audit >> update-report.md
```

### Paso 3: Actualizar por Categorías

**Actualizaciones de Seguridad (Prioridad Alta)**

```bash
# Backend
composer update --with-dependencies [paquete-vulnerabilidad]

# Frontend  
npm audit fix
```

**Actualizaciones Menores (Prioridad Media)**

```bash
# Actualizar versiones patch
composer update --prefer-lowest
npm update
```

**Actualizaciones Mayores (Prioridad Baja)**

```bash
# Una por una con testing extensivo
composer require "vendor/package:^2.0" --with-all-dependencies
npm install package@^2.0.0
```

### Paso 4: Testing Exhaustivo

```bash
# Ejecutar suite completa de tests
composer test
npm run test

# Tests de integración
./scripts/integration-tests.sh

# Tests E2E críticos
npm run test:e2e:critical
```

## ✅ Criterios de Éxito

- [ ] Todas las dependencias actualizadas según plan
- [ ] Todos los tests pasan
- [ ] No hay vulnerabilidades de seguridad críticas
- [ ] Performance se mantiene o mejora
- [ ] Funcionalidades principales verificadas

## 🚨 Troubleshooting

### Conflictos de Dependencias

**Síntoma**: Composer/npm no puede resolver dependencias **Solución**:

```bash
# Analizar árbol de dependencias
composer why-not package/name version
npm ls package-name

# Resolver manualmente
composer update --with-dependencies specific/package
```

````
## 🛠️ **Herramientas para Documentación de Procesos**

### 1. Generación Automática de Documentación

**Script para Documentar Comandos**
```bash
#!/bin/bash
# scripts/document-process.sh

PROCESS_NAME=$1
OUTPUT_FILE="docs/processes/${PROCESS_NAME}.md"

echo "📝 Documentando proceso: $PROCESS_NAME"

# Crear estructura base
mkdir -p docs/processes

cat > "$OUTPUT_FILE" << EOF
# $PROCESS_NAME

## 🎯 Objetivo
[Completar objetivo del proceso]

## 📋 Prerequisitos
- [ ] [Completar prerequisitos]

## ⚡ Pasos de Ejecución

### Paso 1: [Nombre del paso]
\`\`\`bash
# [Completar comando]
\`\`\`
**Resultado esperado**: [Completar]
**Validación**: [Completar]

## ✅ Criterios de Éxito
- [ ] [Completar criterios]

## 🚨 Troubleshooting
### Error común
**Síntoma**: [Completar]
**Solución**: [Completar]

## 📞 Contactos
- **Responsable**: [Completar]

---
*Documentado el: $(date)*
*Última actualización: $(date)*
EOF

echo "✅ Plantilla creada en: $OUTPUT_FILE"
echo "📝 Por favor completa la información faltante"
````

### 2. Validación de Procesos

**Script de Validación de Documentación**

````bash
#!/bin/bash
# scripts/validate-process-docs.sh

echo "🔍 Validando documentación de procesos..."

DOCS_DIR="docs/processes"
ERRORS=0

# Verificar estructura de archivos
for doc in "$DOCS_DIR"/*.md; do
    if [ -f "$doc" ]; then
        echo "📄 Validando: $(basename "$doc")"
        
        # Verificar secciones obligatorias
        sections=("🎯 Objetivo" "📋 Prerequisitos" "⚡ Pasos de Ejecución" "✅ Criterios de Éxito")
        
        for section in "${sections[@]}"; do
            if ! grep -q "$section" "$doc"; then
                echo "❌ Falta sección: $section en $doc"
                ERRORS=$((ERRORS + 1))
            fi
        done
        
        # Verificar que no haya placeholders
        if grep -q "\[Completar" "$doc"; then
            echo "⚠️  Contiene placeholders sin completar: $doc"
            ERRORS=$((ERRORS + 1))
        fi
        
        # Verificar comandos ejecutables
        if grep -A 5 '```bash' "$doc" | grep -q '[Completar comando]'; then
            echo "⚠️  Contiene comandos sin documentar: $doc"
            ERRORS=$((ERRORS + 1))
        fi
    fi
done

if [ $ERRORS -eq 0 ]; then
    echo "✅ Toda la documentación de procesos está completa"
    exit 0
else
    echo "❌ Se encontraron $ERRORS errores en la documentación"
    exit 1
fi
````

### 3. Automatización de Procesos

**Integración con Makefile**

```makefile
# Makefile para automatizar procesos documentados

.PHONY: setup deploy test update-deps

# Setup completo siguiendo documentación
setup:
	@echo "🚀 Ejecutando setup completo del proyecto..."
	@./scripts/validate-prerequisites.sh
	@cp .env.example .env
	@docker-compose up -d
	@docker-compose exec app composer install
	@docker-compose exec app php artisan migrate:fresh --seed
	@docker-compose exec frontend npm install
	@echo "✅ Setup completado. Verificar en http://localhost:3000"

# Deployment siguiendo proceso documentado
deploy:
	@echo "🚀 Iniciando deployment a producción..."
	@./scripts/pre-deploy-checks.sh
	@./scripts/backup-database.sh
	@gh workflow run deploy-production.yml --ref $(shell git describe --tags --abbrev=0)
	@./scripts/post-deploy-verification.sh

# Testing completo
test:
	@echo "🧪 Ejecutando suite completa de tests..."
	@docker-compose exec app composer test
	@docker-compose exec frontend npm run test
	@./scripts/integration-tests.sh

# Actualización de dependencias
update-deps:
	@echo "📦 Actualizando dependencias..."
	@./scripts/analyze-outdated-deps.sh
	@./scripts/update-security-deps.sh
	@./scripts/test-after-update.sh
```

## 📊 **Métricas y Seguimiento**

### Indicadores de Calidad de Documentación

```bash
#!/bin/bash
# scripts/process-documentation-metrics.sh

echo "📊 Métricas de Documentación de Procesos"

DOCS_DIR="docs/processes"
TOTAL_DOCS=$(find "$DOCS_DIR" -name "*.md" | wc -l)
COMPLETE_DOCS=$(find "$DOCS_DIR" -name "*.md" -exec grep -L "\[Completar" {} \; | wc -l)
OUTDATED_DOCS=$(find "$DOCS_DIR" -name "*.md" -mtime +90 | wc -l)

echo "📁 Total de procesos documentados: $TOTAL_DOCS"
echo "✅ Documentos completos: $COMPLETE_DOCS"
echo "⚠️  Documentos posiblemente obsoletos (>90 días): $OUTDATED_DOCS"

COMPLETION_RATE=$((COMPLETE_DOCS * 100 / TOTAL_DOCS))
echo "📈 Tasa de completitud: $COMPLETION_RATE%"

if [ $COMPLETION_RATE -ge 90 ]; then
    echo "🎉 EXCELENTE: Documentación de procesos está completa"
elif [ $COMPLETION_RATE -ge 70 ]; then
    echo "👍 BUENO: La mayoría de procesos están documentados"
else
    echo "⚠️  MEJORABLE: Muchos procesos necesitan documentación"
fi
```

## Tips

### 💡 **Mejores Prácticas**

1. **Documentación Executable**
   - Los comandos documentados deben ser copy-paste funcionales
   - Incluye validaciones para cada paso
   - Proporciona rollback procedures cuando sea aplicable

2. **Mantenimiento Continuo**
   - Actualiza documentación cuando cambien los procesos
   - Establece revisiones periódicas (trimestral)
   - Incluye fechas de última actualización

3. **Colaboración**
   - Que múltiples personas validen los procesos
   - Incluye feedback de usuarios de la documentación
   - Mantén un canal de comunicación para mejoras

4. **Estandarización**
   - Usa el mismo template para todos los procesos
   - Mantén consistencia en terminología
   - Incluye enlaces a herramientas y recursos

### ⚠️ **Errores Comunes a Evitar**

- Documentar procesos obsoletos o temporales
- Usar comandos sin explicar qué hacen
- No incluir criterios de validación claros
- Omitir información de troubleshooting
- No mantener la documentación actualizada

## Ejemplos

### Proceso Simple: Restart de Servicios

````markdown
# Restart de Servicios en Producción

## 🎯 Objetivo

Reiniciar servicios de aplicación de forma segura sin tiempo de inactividad.

## 📋 Prerequisitos

- [ ] Acceso SSH a servidores de producción
- [ ] Verificar que no hay deployments en curso
- [ ] Notificar al equipo del restart programado

## ⚡ Pasos de Ejecución

### Paso 1: Verificar Estado Actual

```bash
# Conectar al servidor
ssh deploy@prod-server-01

# Verificar servicios activos
systemctl status nginx php8.2-fpm redis mysql
docker ps --format "table {{.Names}}\t{{.Status}}"
```

**Resultado esperado**: Todos los servicios "active (running)" **Validación**:
No hay servicios en estado "failed" o "inactive"

### Paso 2: Restart Rolling (Sin Downtime)

```bash
# Restart PHP-FPM
sudo systemctl reload php8.2-fpm

# Restart aplicación con rolling deployment
docker-compose up -d --force-recreate app

# Verificar que los servicios levantaron correctamente
sleep 30
curl -f https://api.production.com/health
```

**Resultado esperado**: Health check retorna 200 OK **Validación**:
`curl -w "%{http_code}" https://api.production.com/health` retorna 200

## ✅ Criterios de Éxito

- [ ] Todos los servicios en estado "running"
- [ ] Health checks pasan
- [ ] No hay errores en logs recientes
- [ ] Tiempo de respuesta < 500ms

## 🚨 Troubleshooting

### Servicio no levanta después del restart

**Síntoma**: `systemctl status service` muestra "failed" **Solución**:

```bash
# Ver logs detallados
journalctl -u service-name --since "5 minutes ago"
# Verificar configuración
nginx -t
php-fpm8.2 -t
```

## 📞 Contactos

- **Responsable**: DevOps Team
- **Escalamiento**: SRE Lead
````

## Navegación

[⬅️ Documentación y Knowledge Transfer](./documentacion-knowledge-transfer.md) |
[🏠 README Principal](../../README.md) |
[Organización de la Documentación ➡️](./organizacion-documentacion.md)
