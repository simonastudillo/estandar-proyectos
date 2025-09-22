# Checklist Mínima por Proyecto

## Conocimientos involucrados
- Estándares de calidad de software
- Clean Architecture y DDD
- Validación y auditoría de proyectos

## Responsable
- Equipo de desarrollo

## ¿Qué es?

El **Checklist Mínima por Proyecto** es una lista de verificación esencial que
define los elementos básicos, obligatorios e indispensables que debe cumplir
todo proyecto siguiendo este estándar. Esta lista funciona como una **puerta de
calidad** que asegura que ningún proyecto se considere completo sin haber
implementado los fundamentos críticos de Clean Architecture, DDD y las mejores
prácticas establecidas.

Este checklist actúa como un **filtro de calidad mínima**, garantizando
consistencia entre proyectos y estableciendo una base sólida para el
mantenimiento, escalabilidad y evolución futura del software.

## ¿Por qué es importante?

### ✅ **Garantía de Calidad Mínima**

- Establece estándares no negociables para todos los proyectos
- Previene la entrega de software que no cumple requisitos básicos
- Asegura consistencia en la calidad entre diferentes equipos y proyectos

### 🎯 **Uniformidad y Consistencia**

- Todos los proyectos siguen la misma estructura base
- Facilita el onboarding entre proyectos del mismo estándar
- Reduce la curva de aprendizaje para nuevos desarrolladores

### 🔒 **Cumplimiento Obligatorio**

- Define elementos que NO son opcionales
- Establece criterios objetivos de aceptación
- Facilita auditorías y revisiones de calidad

### 📋 **Control de Proceso**

- Permite tracking del progreso del proyecto
- Identifica gaps antes de la entrega
- Facilita la planificación y estimación

### 🚀 **Base para Escalabilidad**

- Asegura que el proyecto tenga fundamentos sólidos
- Facilita futuras expansiones y mantenimiento
- Previene deuda técnica desde el inicio

## ¿Qué debe incluir?

### Elementos Arquitectónicos

- Implementación de Clean Architecture básica
- Separación clara de capas (Domain, Application, Infrastructure)
- Implementación de principios SOLID básicos
- Patrones de diseño fundamentales

### Documentación Mínima

- README del proyecto con setup básico
- Documentación de APIs principales
- Diagramas de arquitectura básicos
- Guías de instalación y configuración

### Testing Básico

- Cobertura mínima de testing definida
- Tests unitarios para capa de dominio
- Tests de integración para APIs principales
- Configuración de CI/CD básica

### Configuración de Proyecto

- Configuración de entornos (dev, staging, prod)
- Variables de entorno documentadas
- Scripts de automatización básicos
- Configuración de herramientas de calidad

## ¿Qué debo hacer?

### 1. **Definir Criterios Mínimos**

Establecer elementos no negociables por área:

```bash
# Ejemplo de verificación automatizada
#!/bin/bash
echo "🔍 Verificando checklist mínimo del proyecto..."

# Verificar estructura de archivos
echo "📁 Estructura de archivos..."
[ -f "README.md" ] && echo "✅ README.md" || echo "❌ README.md FALTANTE"
[ -f "docker-compose.yml" ] && echo "✅ docker-compose.yml" || echo "❌ docker-compose.yml FALTANTE"
[ -d "docs/" ] && echo "✅ Carpeta docs/" || echo "❌ Carpeta docs/ FALTANTE"
```

### 2. **Implementar Verificaciones**

Crear scripts automáticos que verifiquen cada elemento del checklist.

### 3. **Establecer Gates de Calidad**

Integrar el checklist en el proceso de CI/CD para bloquear deployments que no
cumplan requisitos mínimos.

## Checklist por Categoría

## 🏗️ **Arquitectura y Estructura**

### Clean Architecture (Obligatorio)

- [ ] **Separación de capas**: Domain, Application, Infrastructure claramente
      definidas
- [ ] **Independencia del framework**: Lógica de negocio NO depende de Laravel
- [ ] **Inyección de dependencias**: Implementada correctamente
- [ ] **Interfaces y contratos**: Definidos para todas las dependencias externas

### Domain-Driven Design (Obligatorio)

- [ ] **Entidades del dominio**: Implementadas como POPOs (Plain Old PHP
      Objects)
- [ ] **Value Objects**: Para conceptos como Email, Money, etc.
- [ ] **Repositorios**: Interfaces en Domain, implementación en Infrastructure
- [ ] **Casos de uso**: Implementados en Application layer

### Estructura de Carpetas (Obligatorio)

```
✅ Estructura requerida:
backend/
├── app/
│   ├── Domain/          # ✅ Obligatorio
│   ├── Application/     # ✅ Obligatorio
│   └── Infrastructure/  # ✅ Obligatorio
frontend/
├── src/
│   ├── components/      # ✅ Obligatorio
│   ├── pages/          # ✅ Obligatorio
│   ├── services/       # ✅ Obligatorio
│   └── types/          # ✅ Obligatorio
```

### Principios SOLID (Verificación mínima)

- [ ] **Single Responsibility**: Clases con una sola responsabilidad
- [ ] **Dependency Inversion**: Dependencias a través de interfaces
- [ ] **Interface Segregation**: Interfaces específicas, no genéricas

## 📱 **Frontend (React + TypeScript)**

### Configuración Base (Obligatorio)

- [ ] **TypeScript configurado**: tsconfig.json con strict mode
- [ ] **ESLint + Prettier**: Configurados y funcionando
- [ ] **Vite configurado**: Con configuración de producción
- [ ] **Redux Toolkit**: Store configurado con slices básicos

### Componentes y Tipado (Obligatorio)

- [ ] **Tipado fuerte**: Props y estados con tipos explícitos
- [ ] **Componentes reutilizables**: Al menos 3 componentes base
- [ ] **Custom hooks**: Para lógica común reutilizable
- [ ] **Error boundaries**: Implementados para manejo de errores

### Configuración mínima requerida:

```typescript
// ✅ tsconfig.json obligatorio
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}

// ✅ Componente tipado ejemplo
interface Props {
  title: string;
  onClick: () => void;
}

const Button: React.FC<Props> = ({ title, onClick }) => {
  return <button onClick={onClick}>{title}</button>;
};
```

## 🚀 **Backend (Laravel + Clean Architecture)**

### Configuración PHP (Obligatorio)

- [ ] **PHP 8.2+**: Versión mínima requerida
- [ ] **Composer configurado**: Con autoload PSR-4
- [ ] **Laravel 10+**: Framework base configurado
- [ ] **Variables de entorno**: .env.example documentado

### APIs y Rutas (Obligatorio)

- [ ] **Versionado de APIs**: `/api/v1/` implementado
- [ ] **Autenticación básica**: Laravel Sanctum configurado
- [ ] **Middleware**: Básico para CORS y autenticación
- [ ] **Form Requests**: Para validación de datos

### Base de Datos (Obligatorio)

- [ ] **Migraciones**: Para todas las tablas
- [ ] **Seeders básicos**: Para datos de prueba
- [ ] **Models con relaciones**: Eloquent configurado
- [ ] **Soft deletes**: Implementado donde corresponde

```php
// ✅ Estructura mínima requerida
// UseCase obligatorio
class CreateUserUseCase
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(UserDTO $userData): User
    {
        // Lógica de negocio aquí
    }
}

// ✅ Repository interface obligatorio
interface UserRepositoryInterface
{
    public function save(User $user): User;
    public function findById(string $id): ?User;
}
```

## 🗄️ **Base de Datos (MySQL)**

### Configuración y Estructura (Obligatorio)

- [ ] **MySQL 8.0+**: Versión mínima
- [ ] **Charset UTF8MB4**: Configurado correctamente
- [ ] **Índices básicos**: En campos de búsqueda frecuente
- [ ] **Foreign keys**: Para todas las relaciones

### Convenciones (Obligatorio)

- [ ] **Nombres en inglés**: Tablas y columnas
- [ ] **Snake_case**: Para nombres de tablas y columnas
- [ ] **Plural para tablas**: `users`, `posts`, etc.
- [ ] **Timestamps**: `created_at`, `updated_at`, `deleted_at`

```sql
-- ✅ Estructura mínima requerida
CREATE TABLE users (
    id CHAR(36) PRIMARY KEY,           -- ✅ UUID obligatorio
    email VARCHAR(255) UNIQUE NOT NULL, -- ✅ Índice único
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL          -- ✅ Soft delete
);
```

## 🧪 **Testing (Mínimo Obligatorio)**

### Cobertura Mínima (Obligatorio)

- [ ] **Cobertura global**: Mínimo 70%
- [ ] **Tests unitarios**: Para todos los casos de uso
- [ ] **Tests de integración**: Para APIs principales
- [ ] **Tests de componentes**: Para componentes React clave

### Configuración Testing (Obligatorio)

- [ ] **PHPUnit configurado**: Para backend
- [ ] **Jest configurado**: Para frontend
- [ ] **CI/CD tests**: Tests ejecutándose automáticamente
- [ ] **Base de datos de testing**: Configurada y aislada

```php
// ✅ Test unitario obligatorio (ejemplo)
class CreateUserUseCaseTest extends TestCase
{
    public function test_creates_user_successfully(): void
    {
        // Arrange
        $userData = new UserDTO('test@example.com', 'John Doe');

        // Act
        $result = $this->useCase->execute($userData);

        // Assert
        $this->assertInstanceOf(User::class, $result);
    }
}
```

## 🐳 **DevOps y Configuración**

### Docker (Obligatorio)

- [ ] **docker-compose.yml**: Para desarrollo local
- [ ] **Dockerfile**: Para backend y frontend
- [ ] **Variables de entorno**: Configuradas para todos los servicios
- [ ] **Servicios básicos**: App, DB, Redis mínimo

### CI/CD Básico (Obligatorio)

- [ ] **GitHub Actions**: Configurado básico
- [ ] **Tests automáticos**: En cada push/PR
- [ ] **Build verification**: Para todos los servicios
- [ ] **Environment separation**: Dev, staging, prod

```yaml
# ✅ docker-compose.yml mínimo obligatorio
version: "3.8"
services:
  app:
    build: .
    volumes:
      - .:/var/www
  db:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: laravel
  redis:
    image: redis:alpine
```

## 📚 **Documentación Mínima**

### README (Obligatorio)

- [ ] **Descripción del proyecto**: Clara y concisa
- [ ] **Requisitos del sistema**: Versiones específicas
- [ ] **Instalación**: Pasos paso a paso
- [ ] **Configuración**: Variables de entorno explicadas

### Documentación Técnica (Obligatorio)

- [ ] **API Documentation**: OpenAPI/Swagger básico
- [ ] **Diagrama de arquitectura**: Básico de capas
- [ ] **Guía de contribución**: Básica
- [ ] **Changelog**: Para tracking de cambios

```markdown
# ✅ README mínimo obligatorio

## Requisitos

- PHP 8.2+
- Node.js 18+
- MySQL 8.0+
- Docker

## Instalación

1. `git clone ...`
2. `docker-compose up`
3. `composer install`
4. `npm install`

## Configuración

- Copiar `.env.example` a `.env`
- Configurar variables de base de datos
```

## 🔧 **Herramientas de Calidad**

### Linting y Formateo (Obligatorio)

- [ ] **PHP CS Fixer**: Configurado y funcionando
- [ ] **ESLint**: Para TypeScript/JavaScript
- [ ] **Prettier**: Para formateo automático
- [ ] **Pre-commit hooks**: Para verificaciones automáticas

### Análisis Estático (Obligatorio)

- [ ] **PHPStan**: Nivel 6 mínimo para PHP
- [ ] **TypeScript**: Modo strict activado
- [ ] **Code quality gates**: En CI/CD
- [ ] **Security scanning**: Dependencias verificadas

## 🔒 **Seguridad Básica**

### Configuraciones de Seguridad (Obligatorio)

- [ ] **Variables sensibles**: En .env, NO en código
- [ ] **CORS configurado**: Para APIs
- [ ] **Rate limiting**: Básico implementado
- [ ] **Input validation**: En todos los endpoints
- [ ] **Headers de seguridad**: CSP, HSTS, X-Frame-Options configurados
- [ ] **HTTPS**: Configurado para todos los ambientes
- [ ] **Logs de seguridad**: Configurados y monitoreados

### Validaciones Generales (Obligatorio)

- [ ] **Validación de entrada**: Todos los inputs validados y sanitizados
- [ ] **Validación de tipos**: Verificación de tipos de datos
- [ ] **Validación de rangos**: Límites mínimos y máximos definidos
- [ ] **Whitelist approach**: Usar listas permitidas en lugar de prohibidas
- [ ] **Escape de salida**: HTML/SQL/JS escapado correctamente
- [ ] **Validación de email**: Formato y existencia verificados
- [ ] **Validación de URLs**: Protocolo y dominio verificados

### Manejo Seguro de Archivos (Obligatorio)

- [ ] **Validación de tipo MIME**: Verificación real del contenido
- [ ] **Límites de tamaño**: Restricciones implementadas
- [ ] **Extensiones permitidas**: Lista blanca de extensiones
- [ ] **Almacenamiento seguro**: Fuera del directorio público
- [ ] **Nombres de archivo**: Generados aleatoriamente
- [ ] **Escaneo de virus**: Implementado para uploads
- [ ] **Control de acceso**: Verificación de permisos para descarga

### Autenticación y Autorización (Obligatorio)

- [ ] **Laravel Sanctum**: Configurado correctamente
- [ ] **Password hashing**: Bcrypt/Argon2 implementado
- [ ] **Password policies**: Longitud mínima, complejidad, expiración
- [ ] **Session security**: Regeneración de ID, timeouts
- [ ] **CSRF protection**: Activada en formularios
- [ ] **JWT security**: Tokens con expiración, algoritmos seguros
- [ ] **Two-Factor Auth**: Implementado para usuarios críticos
- [ ] **Account lockout**: Protección contra fuerza bruta
- [ ] **Audit logging**: Registro de accesos y cambios

### Configuración de Base de Datos (Obligatorio)

- [ ] **Usuario específico**: No usar root/admin para la aplicación
- [ ] **Permisos mínimos**: Solo los necesarios para la aplicación
- [ ] **Conexión cifrada**: SSL/TLS habilitado
- [ ] **Prepared statements**: Usar en todas las queries
- [ ] **Backup cifrado**: Copias de seguridad protegidas
- [ ] **Auditoría**: Logs de acceso y modificaciones
- [ ] **Data masking**: Ofuscar datos sensibles en desarrollo

### Prevención de Vulnerabilidades OWASP (Obligatorio)

- [ ] **SQL Injection**: Prepared statements, validación de entrada
- [ ] **XSS Prevention**: Sanitización, CSP, escape de salida
- [ ] **CSRF Protection**: Tokens CSRF en formularios
- [ ] **Clickjacking**: X-Frame-Options, CSP frame-ancestors
- [ ] **XXE Prevention**: Deshabilitar entidades externas en XML
- [ ] **Path Traversal**: Validación de rutas de archivos
- [ ] **Command Injection**: Evitar exec/system con input del usuario
- [ ] **LDAP Injection**: Escapar caracteres especiales LDAP

### Testing de Seguridad (Obligatorio)

- [ ] **Security tests**: Tests automatizados de vulnerabilidades
- [ ] **Input fuzzing**: Tests con datos maliciosos
- [ ] **Authentication tests**: Verificación de controles de acceso
- [ ] **Session tests**: Validación de manejo de sesiones
- [ ] **OWASP ZAP**: Escaneo automatizado configurado
- [ ] **Dependency check**: Verificación de vulnerabilidades en librerías
- [ ] **Static analysis**: Análisis estático de código habilitado

### Monitoreo y Logging (Obligatorio)

- [ ] **Security logs**: Eventos de seguridad registrados
- [ ] **Failed login tracking**: Monitoreo de intentos fallidos
- [ ] **Privileged actions**: Logs de acciones administrativas
- [ ] **Error handling**: Manejo seguro de errores
- [ ] **Log rotation**: Rotación y archivo de logs
- [ ] **SIEM integration**: Integración con herramientas de monitoreo
- [ ] **Alertas**: Notificaciones de eventos críticos

### Configuración de Servidor (Obligatorio)

- [ ] **Firewall**: Configurado y activo
- [ ] **OS updates**: Sistema operativo actualizado
- [ ] **Service hardening**: Servicios innecesarios deshabilitados
- [ ] **SSL/TLS**: Configuración segura (TLS 1.2+)
- [ ] **Security headers**: Implementados en servidor web
- [ ] **Directory listing**: Deshabilitado
- [ ] **Server tokens**: Información del servidor oculta

### Backup y Recuperación (Obligatorio)

- [ ] **Backup strategy**: Estrategia de respaldo definida
- [ ] **Backup encryption**: Respaldos cifrados
- [ ] **Recovery testing**: Pruebas de recuperación regulares
- [ ] **Offsite storage**: Almacenamiento fuera del sitio
- [ ] **Retention policy**: Política de retención definida
- [ ] **Access control**: Control de acceso a respaldos
- [ ] **Incident response**: Plan de respuesta a incidentes

### Compliance y Documentación (Obligatorio)

- [ ] **Privacy policy**: Política de privacidad definida
- [ ] **Data retention**: Política de retención de datos
- [ ] **GDPR compliance**: Cumplimiento de GDPR si aplica
- [ ] **Security documentation**: Documentación de seguridad actualizada
- [ ] **Security training**: Capacitación del equipo en seguridad
- [ ] **Penetration testing**: Pruebas de penetración programadas
- [ ] **Security review**: Revisiones de seguridad regulares

## 📊 **Scripts de Verificación**

### Script de Verificación Completa

```bash
#!/bin/bash
# scripts/verify-minimum-checklist.sh

echo "🔍 Verificando Checklist Mínimo del Proyecto..."

score=0
total=0

# Función para verificar archivo
check_file() {
    local file=$1
    local description=$2
    total=$((total + 1))

    if [ -f "$file" ]; then
        echo "✅ $description"
        score=$((score + 1))
    else
        echo "❌ $description - FALTANTE: $file"
    fi
}

# Función para verificar directorio
check_dir() {
    local dir=$1
    local description=$2
    total=$((total + 1))

    if [ -d "$dir" ]; then
        echo "✅ $description"
        score=$((score + 1))
    else
        echo "❌ $description - FALTANTE: $dir"
    fi
}

# Verificaciones de estructura
echo "📁 Verificando estructura de archivos..."
check_file "README.md" "README del proyecto"
check_file "docker-compose.yml" "Docker Compose"
check_file ".env.example" "Archivo de entorno ejemplo"
check_file "composer.json" "Composer configuration"
check_file "package.json" "Package.json frontend"

# Verificaciones de directorios
echo "📂 Verificando estructura de directorios..."
check_dir "backend/app/Domain" "Domain Layer"
check_dir "backend/app/Application" "Application Layer"
check_dir "backend/app/Infrastructure" "Infrastructure Layer"
check_dir "frontend/src/components" "Componentes React"
check_dir "frontend/src/types" "Tipos TypeScript"
check_dir "docs/" "Documentación"

# Verificaciones de configuración
echo "⚙️ Verificando configuraciones..."
check_file "backend/phpunit.xml" "PHPUnit config"
check_file "frontend/tsconfig.json" "TypeScript config"
check_file "frontend/.eslintrc.json" "ESLint config"
check_file ".github/workflows/ci.yml" "GitHub Actions CI"

# Verificar tests
echo "🧪 Verificando tests..."
if [ -d "backend/tests/Unit" ] && [ -d "backend/tests/Feature" ]; then
    echo "✅ Estructura de tests backend"
    score=$((score + 1))
else
    echo "❌ Estructura de tests backend - FALTANTE"
fi
total=$((total + 1))

# Verificaciones de seguridad
echo "🔒 Verificando configuraciones de seguridad..."

# Verificar archivos de configuración de seguridad
check_file "backend/config/sanctum.php" "Configuración Laravel Sanctum"
check_file "backend/config/cors.php" "Configuración CORS"

# Verificar middleware de seguridad
if [ -f "backend/app/Infrastructure/Http/Middleware/SecurityHeaders.php" ]; then
    echo "✅ Middleware de security headers"
    score=$((score + 1))
else
    echo "❌ Middleware de security headers - FALTANTE"
fi
total=$((total + 1))

# Verificar configuraciones de validación
if grep -r "validate\|FormRequest" backend/app/Infrastructure/Http/Controllers/ > /dev/null 2>&1; then
    echo "✅ Validaciones en controladores"
    score=$((score + 1))
else
    echo "❌ Validaciones en controladores - FALTANTE"
fi
total=$((total + 1))

# Verificar autenticación
if [ -d "backend/app/Application/UseCases/Auth" ]; then
    echo "✅ Casos de uso de autenticación"
    score=$((score + 1))
else
    echo "❌ Casos de uso de autenticación - FALTANTE"
fi
total=$((total + 1))

# Verificar tests de seguridad
if [ -f "backend/tests/Feature/Security/AuthSecurityTest.php" ] || [ -d "backend/tests/Feature/Security" ]; then
    echo "✅ Tests de seguridad"
    score=$((score + 1))
else
    echo "❌ Tests de seguridad - FALTANTE"
fi
total=$((total + 1))

# Verificar configuración de rate limiting
if grep -r "throttle\|RateLimiter" backend/routes/ > /dev/null 2>&1; then
    echo "✅ Rate limiting configurado"
    score=$((score + 1))
else
    echo "❌ Rate limiting - FALTANTE"
fi
total=$((total + 1))

# Verificar configuración HTTPS
if grep -r "FORCE_HTTPS\|APP_URL.*https" backend/.env.example > /dev/null 2>&1; then
    echo "✅ Configuración HTTPS"
    score=$((score + 1))
else
    echo "❌ Configuración HTTPS - FALTANTE"
fi
total=$((total + 1))

# Verificar manejo de archivos
if [ -f "backend/app/Infrastructure/Http/Controllers/Api/V1/FileUploadController.php" ]; then
    echo "✅ Controlador de subida de archivos seguro"
    score=$((score + 1))
else
    echo "❌ Controlador de subida de archivos - FALTANTE"
fi
total=$((total + 1))

# Verificar sanitización frontend
if grep -r "DOMPurify\|sanitize" frontend/src/ > /dev/null 2>&1; then
    echo "✅ Sanitización en frontend"
    score=$((score + 1))
else
    echo "❌ Sanitización en frontend - FALTANTE"
fi
total=$((total + 1))

# Verificar CSP
if grep -r "Content-Security-Policy\|helmet" frontend/src/ backend/app/ > /dev/null 2>&1; then
    echo "✅ Content Security Policy configurado"
    score=$((score + 1))
else
    echo "❌ Content Security Policy - FALTANTE"
fi
total=$((total + 1))

if [ -d "frontend/src/__tests__" ] || [ -d "frontend/tests/" ]; then
    echo "✅ Tests frontend"
    score=$((score + 1))
else
    echo "❌ Tests frontend - FALTANTE"
fi
total=$((total + 1))

# Calcular puntuación
percentage=$((score * 100 / total))

echo ""
echo "📊 RESULTADO FINAL:"
echo "   Completado: $score/$total elementos ($percentage%)"

if [ $percentage -ge 90 ]; then
    echo "🎉 EXCELENTE: Proyecto cumple con estándares mínimos"
    exit 0
elif [ $percentage -ge 70 ]; then
    echo "⚠️  ACEPTABLE: Proyecto necesita mejoras menores"
    exit 1
else
    echo "❌ CRÍTICO: Proyecto NO cumple estándares mínimos"
    exit 2
fi
```

### Script para Nuevos Proyectos

```bash
#!/bin/bash
# scripts/setup-minimum-project.sh

echo "🚀 Configurando proyecto con estándares mínimos..."

# Crear estructura de directorios
mkdir -p backend/app/{Domain,Application,Infrastructure}
mkdir -p frontend/src/{components,pages,services,types,hooks}
mkdir -p docs/{api,architecture,guides}
mkdir -p tests/{backend,frontend}

# Crear archivos base
touch README.md
touch docker-compose.yml
touch .env.example
touch .gitignore

# Configurar archivos de configuración básicos
cat > frontend/tsconfig.json << 'EOF'
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  }
}
EOF

echo "✅ Estructura mínima creada"
echo "📋 Ejecuta 'scripts/verify-minimum-checklist.sh' para verificar completitud"
```

## Tips

### 💡 **Mejores Prácticas**

1. **Automatización del Checklist**

   - Integra verificaciones en CI/CD
   - Usa scripts para verificación rápida
   - Bloquea deploys que no cumplan requisitos mínimos

2. **Evolutivo pero Estricto**

   - Permite agregar elementos al checklist
   - NO permite remover elementos obligatorios
   - Versiona el checklist junto con el proyecto

3. **Documentación Clara**

   - Cada elemento debe ser verificable objetivamente
   - Incluye ejemplos de implementación correcta
   - Documenta excepciones si las hay

4. **Feedback Temprano**
   - Verifica checklist en las primeras fases
   - No esperes al final del desarrollo
   - Facilita correcciones tempranas

### ⚠️ **Elementos NO Negociables**

- Separación de capas Clean Architecture
- Tipado fuerte en TypeScript
- Cobertura mínima de testing (70%)
- Documentación básica (README + API)
- Configuración de entornos (dev, prod)
- Variables de entorno documentadas
- CI/CD básico configurado

## Ejemplos

### Template de README Mínimo

````markdown
# [Nombre del Proyecto]

## Descripción

[Descripción clara del proyecto en 2-3 líneas]

## Stack Tecnológico

- Backend: Laravel 10 + PHP 8.2
- Frontend: React 18 + TypeScript
- Base de datos: MySQL 8.0
- Contenedores: Docker

## Requisitos del Sistema

- Docker & Docker Compose
- PHP 8.2+
- Node.js 18+
- Composer

## Instalación Rápida

```bash
# 1. Clonar repositorio
git clone [url]

# 2. Configurar entorno
cp .env.example .env

# 3. Levantar servicios
docker-compose up -d

# 4. Instalar dependencias
docker-compose exec app composer install
docker-compose exec frontend npm install

# 5. Configurar base de datos
docker-compose exec app php artisan migrate:fresh --seed
```
````

## Configuración

### Variables de Entorno Principales

- `DB_HOST`: Host de base de datos
- `DB_DATABASE`: Nombre de la base de datos
- `APP_KEY`: Clave de aplicación Laravel
- `VITE_API_URL`: URL del backend para frontend

## Testing

```bash
# Backend
docker-compose exec app php artisan test

# Frontend
docker-compose exec frontend npm run test
```

## API Documentation

[Swagger/OpenAPI URL o enlace a documentación]

## Contribución

Ver [CONTRIBUTING.md](./CONTRIBUTING.md)

````
### Configuración Docker Mínima
```yaml
# docker-compose.yml mínimo
version: '3.8'

services:
  app:
    build:
      context: ./backend
      dockerfile: Dockerfile
    volumes:
      - ./backend:/var/www
    depends_on:
      - db
      - redis

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    volumes:
      - ./frontend:/app
    ports:
      - "3000:3000"

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
    volumes:
      - mysql_data:/var/lib/mysql
    ports:
      - "3306:3306"

  redis:
    image: redis:alpine
    volumes:
      - redis_data:/data

volumes:
  mysql_data:
  redis_data:
````

### Script de Verificación de Seguridad Específico

```bash
#!/bin/bash
# scripts/verify-security-checklist.sh

echo "🔒 Verificando Checklist de Seguridad Completo..."

security_score=0
security_total=0

# Función para verificar configuración de seguridad
check_security_config() {
    local config=$1
    local file=$2
    local description=$3
    security_total=$((security_total + 1))

    if grep -q "$config" "$file" 2>/dev/null; then
        echo "✅ $description"
        security_score=$((security_score + 1))
    else
        echo "❌ $description - FALTANTE: $config en $file"
    fi
}

echo "🛡️ Verificando configuraciones de seguridad..."

# Headers de seguridad
check_security_config "X-Frame-Options" "backend/app/Infrastructure/Http/Middleware/SecurityHeaders.php" "X-Frame-Options configurado"
check_security_config "Content-Security-Policy" "backend/app/Infrastructure/Http/Middleware/SecurityHeaders.php" "CSP configurado"
check_security_config "X-Content-Type-Options" "backend/app/Infrastructure/Http/Middleware/SecurityHeaders.php" "X-Content-Type-Options configurado"

# Configuración de CORS
check_security_config "supports_credentials" "backend/config/cors.php" "CORS credentials configurado"
check_security_config "allowed_origins" "backend/config/cors.php" "CORS origins configurado"

# Rate limiting
check_security_config "throttle:" "backend/routes/api/v1/auth.php" "Rate limiting en auth"
check_security_config "RateLimiter::for" "backend/app/Providers/RouteServiceProvider.php" "Rate limiting configurado"

# Validaciones
if find backend/app/Infrastructure/Http/Requests -name "*.php" | head -1 > /dev/null 2>&1; then
    echo "✅ Form Requests implementados"
    security_score=$((security_score + 1))
else
    echo "❌ Form Requests - FALTANTE"
fi
security_total=$((security_total + 1))

# Autenticación segura
check_security_config "Hash::make" "backend/app/Application/UseCases/Auth" "Password hashing implementado"
check_security_config "createToken" "backend/app/Application/UseCases/Auth" "Token creation implementado"

# Tests de seguridad
if [ -d "backend/tests/Feature/Security" ] || find backend/tests -name "*Security*" | head -1 > /dev/null 2>&1; then
    echo "✅ Tests de seguridad implementados"
    security_score=$((security_score + 1))
else
    echo "❌ Tests de seguridad - FALTANTE"
fi
security_total=$((security_total + 1))

# Frontend security
if [ -f "frontend/package.json" ]; then
    if grep -q "dompurify" frontend/package.json; then
        echo "✅ DOMPurify instalado para sanitización"
        security_score=$((security_score + 1))
    else
        echo "❌ DOMPurify - FALTANTE"
    fi
    security_total=$((security_total + 1))

    if grep -q "helmet" frontend/package.json; then
        echo "✅ Helmet.js instalado"
        security_score=$((security_score + 1))
    else
        echo "❌ Helmet.js - FALTANTE"
    fi
    security_total=$((security_total + 1))
fi

# Verificar .env.example
if [ -f ".env.example" ]; then
    if ! grep -q "real-password\|secret123\|actual-key" .env.example; then
        echo "✅ .env.example no contiene secretos reales"
        security_score=$((security_score + 1))
    else
        echo "❌ .env.example contiene valores sensibles reales"
    fi
    security_total=$((security_total + 1))

    if grep -q "APP_KEY=\|DB_PASSWORD=\|JWT_SECRET=" .env.example; then
        echo "✅ Variables de seguridad definidas en .env.example"
        security_score=$((security_score + 1))
    else
        echo "❌ Variables de seguridad faltantes en .env.example"
    fi
    security_total=$((security_total + 1))
fi

# Verificar configuración SSL
if grep -q "FORCE_HTTPS\|APP_URL.*https" .env.example 2>/dev/null; then
    echo "✅ Configuración HTTPS definida"
    security_score=$((security_score + 1))
else
    echo "❌ Configuración HTTPS - FALTANTE"
fi
security_total=$((security_total + 1))

# Calcular porcentaje de seguridad
security_percentage=$((security_score * 100 / security_total))

echo ""
echo "📊 RESULTADOS DE SEGURIDAD:"
echo "Puntuación: $security_score/$security_total ($security_percentage%)"

if [ $security_percentage -ge 80 ]; then
    echo "🟢 EXCELENTE nivel de seguridad"
elif [ $security_percentage -ge 60 ]; then
    echo "🟡 ACEPTABLE nivel de seguridad - Mejorar elementos faltantes"
else
    echo "🔴 INSUFICIENTE nivel de seguridad - CRÍTICO: Implementar elementos faltantes"
fi

echo ""
echo "💡 Para más detalles, revisar:"
echo "   - docs/step_07/testing-seguridad-owasp.md"
echo "   - docs/step_04/autenticacion-laravel-sanctum.md"
```

### Script de Verificación de Vulnerabilidades

```bash
#!/bin/bash
# scripts/check-vulnerabilities.sh

echo "🔍 Verificando vulnerabilidades conocidas..."

# Backend - PHP dependencies
if [ -f "backend/composer.json" ]; then
    echo "📦 Verificando dependencias PHP..."
    cd backend

    # Verificar composer.lock existe
    if [ ! -f "composer.lock" ]; then
        echo "❌ composer.lock no existe - Ejecutar composer install"
        exit 1
    fi

    # Verificar vulnerabilidades conocidas con composer audit
    if composer audit --no-dev 2>/dev/null; then
        echo "✅ No se encontraron vulnerabilidades en dependencias PHP"
    else
        echo "⚠️ Se encontraron vulnerabilidades en dependencias PHP"
        echo "💡 Ejecutar: composer audit --format=json para más detalles"
    fi

    cd ..
fi

# Frontend - NPM dependencies
if [ -f "frontend/package.json" ]; then
    echo "📦 Verificando dependencias NPM..."
    cd frontend

    # Verificar package-lock.json existe
    if [ ! -f "package-lock.json" ]; then
        echo "❌ package-lock.json no existe - Ejecutar npm install"
        exit 1
    fi

    # Verificar vulnerabilidades con npm audit
    if npm audit --audit-level=high --json > /dev/null 2>&1; then
        echo "✅ No se encontraron vulnerabilidades críticas en dependencias NPM"
    else
        echo "⚠️ Se encontraron vulnerabilidades en dependencias NPM"
        echo "💡 Ejecutar: npm audit para más detalles"
        echo "💡 Intentar: npm audit fix para solucionarlas automáticamente"
    fi

    cd ..
fi

# Verificar archivos sensibles en el repositorio
echo "🔍 Verificando archivos sensibles..."

sensitive_files=(
    ".env"
    "*.key"
    "*.pem"
    "*.p12"
    "*.pfx"
    "*password*"
    "*secret*"
    "*credential*"
)

found_sensitive=false
for pattern in "${sensitive_files[@]}"; do
    if find . -name "$pattern" -not -path "./.git/*" -not -path "./node_modules/*" -not -path "./vendor/*" | grep -q .; then
        echo "⚠️ Archivos sensibles encontrados: $pattern"
        find . -name "$pattern" -not -path "./.git/*" -not -path "./node_modules/*" -not -path "./vendor/*"
        found_sensitive=true
    fi
done

if [ "$found_sensitive" = false ]; then
    echo "✅ No se encontraron archivos sensibles en el repositorio"
fi

# Verificar configuraciones inseguras comunes
echo "🔍 Verificando configuraciones inseguras..."

# Verificar debug activado en producción
if grep -r "APP_DEBUG=true" . --exclude-dir=node_modules --exclude-dir=vendor 2>/dev/null; then
    echo "⚠️ DEBUG activado - Verificar que esté desactivado en producción"
else
    echo "✅ DEBUG no está hardcodeado como true"
fi

# Verificar URLs hardcodeadas
if grep -r "http://localhost\|127.0.0.1" . --exclude-dir=node_modules --exclude-dir=vendor --exclude="*.md" 2>/dev/null; then
    echo "⚠️ URLs de desarrollo hardcodeadas encontradas"
else
    echo "✅ No se encontraron URLs de desarrollo hardcodeadas"
fi

echo ""
echo "✅ Verificación de vulnerabilidades completada"
echo "💡 Ejecutar regularmente para mantener la seguridad"
```

---

## Navegación

[⬅️ Herramientas por lenguaje](./herramientas-por-lenguaje.md) |
[🏠 README Principal](../../README.md) |
[Estilo y Formato de Documentación ➡️](./estilo-formato-documentacion.md)
