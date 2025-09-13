# Checklist Mínima por Proyecto

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

### Autenticación (Obligatorio)

- [ ] **Laravel Sanctum**: Configurado
- [ ] **Password hashing**: Implementado correctamente
- [ ] **Session security**: Configurada
- [ ] **CSRF protection**: Activada

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

---

## Navegación

[⬅️ Herramientas por lenguaje](./herramientas-por-lenguaje.md) |
[🏠 README Principal](../../README.md) |
[Estilo y Formato de Documentación ➡️](./estilo-formato-documentacion.md)
