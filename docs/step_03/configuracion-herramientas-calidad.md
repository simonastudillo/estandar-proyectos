# Configuración de Herramientas de Calidad de Código (Estándar Único)

## Conocimientos involucrados
- Uso de linters y formateadores
- Análisis estático de código
- Testing automatizado
- Principios de Clean Code

## Responsable de la etapa
- Equipo de Desarrollo

> **IMPORTANTE**: Esta configuración representa el **estándar único NO NEGOCIABLE** para herramientas de calidad. Las herramientas aquí especificadas son obligatorias y no admiten alternativas, garantizando consistencia total entre proyectos.

## ¿Qué es?

La configuración de herramientas de calidad de código es el **proceso estándar obligatorio**
para implementar herramientas automatizadas específicas que analizan, validan y
mejoran la calidad del código fuente. Este documento establece las herramientas
**únicas permitidas** incluyendo linters, formateadores, analizadores estáticos,
frameworks de testing y validadores de tipos que garantizan adherencia estricta
a estándares, buenas prácticas y principios de Clean Code.

## ¿Por qué es importante?

- **Eliminación de ambigüedad**: Herramientas únicas estándar evitan decisiones de configuración
- **Consistencia total**: Estilo de código idéntico en todos los proyectos del equipo
- **Calidad garantizada**: Detecta errores, vulnerabilidades y code smells con estándares unificados
- **Mantenibilidad**: Facilita lectura y modificación con patrones de código predecibles
- **Productividad máxima**: Automatización total elimina debates sobre configuraciones
- **Confiabilidad**: Reduce bugs mediante análisis temprano con herramientas probadas
- **Adherencia estricta**: Garantiza cumplimiento de SOLID, DRY y Clean Architecture
- **Integración Vite**: Herramientas específicamente optimizadas para nuestro stack Vite + React

## ¿Qué debe incluir?

### Frontend (React + TypeScript + Vite - Configuración Obligatoria)

1. **Linting y Formateo (Herramientas Únicas)**

   - **ESLint** con configuraciones específicas para React y TypeScript (único linter permitido)
   - **Prettier** para formateo automático (único formateador permitido)
   - Integración obligatoria con VS Code

2. **Análisis de Tipos (Estándar Único)**

   - **TypeScript compiler** con configuración estricta obligatoria
   - Validación de tipos en tiempo de desarrollo (no negociable)

3. **Testing (Framework Único para Vite)**

   - **Vitest** para pruebas unitarias (único framework permitido con Vite)
   - **React Testing Library** para pruebas de componentes (única biblioteca permitida)
   - **@testing-library/jest-dom** para matchers DOM
   - Coverage reports automatizados con Vitest

4. **Análisis de Calidad (Herramientas Estándar)**
   - **SonarJS** para análisis estático
   - **madge** para detección de dependencias circulares
   - **rollup-plugin-visualizer** para análisis de bundle size (específico Vite)

### Backend (Laravel + Clean Architecture - Configuración Obligatoria)

1. **Análisis Estático PHP (Herramientas Únicas)**

   - **PHPStan** para análisis estático (único analizador permitido)
   - **PHP CS Fixer** para formateo automático (único formateador PHP permitido)
   - **PHPMD** (PHP Mess Detector) para detección de code smells

2. **Testing (Framework Único)**

   - **PHPUnit** para pruebas unitarias y de integración (único framework permitido)
   - Coverage reports con Xdebug (única herramienta de coverage)

3. **Validación de Arquitectura (Herramientas Estándar)**

   - **Deptrac** para validar dependencias entre capas
   - **PHP Architecture Tester** para reglas de arquitectura

4. **Calidad de Código (Estándares Únicos)**
   - **PHPCPD** para detección de duplicación
   - **PhpMetrics** para análisis de complejidad ciclomática
   - **PHP_CodeSniffer** para validación de PSR standards

## ¿Qué debo hacer?

### 1. Configuración Frontend (React + TypeScript)

#### ESLint Configuration

```bash
# Instalar dependencias
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install --save-dev eslint-plugin-react eslint-plugin-react-hooks
npm install --save-dev eslint-plugin-jsx-a11y eslint-plugin-import
```

**Crear `.eslintrc.json`:**

```json
{
   "env": {
      "browser": true,
      "es2021": true,
      "node": true
   },
   "extends": [
      "eslint:recommended",
      "@typescript-eslint/recommended",
      "plugin:react/recommended",
      "plugin:react-hooks/recommended",
      "plugin:jsx-a11y/recommended",
      "plugin:import/recommended",
      "plugin:import/typescript"
   ],
   "parser": "@typescript-eslint/parser",
   "parserOptions": {
      "ecmaFeatures": {
         "jsx": true
      },
      "ecmaVersion": "latest",
      "sourceType": "module",
      "project": "./tsconfig.json"
   },
   "plugins": [
      "react",
      "react-hooks",
      "@typescript-eslint",
      "jsx-a11y",
      "import"
   ],
   "rules": {
      "@typescript-eslint/no-unused-vars": "error",
      "@typescript-eslint/explicit-function-return-type": "warn",
      "@typescript-eslint/no-explicit-any": "error",
      "react/react-in-jsx-scope": "off",
      "react/prop-types": "off",
      "import/order": [
         "error",
         {
            "groups": [
               "builtin",
               "external",
               "internal",
               "parent",
               "sibling",
               "index"
            ],
            "newlines-between": "always"
         }
      ]
   },
   "settings": {
      "react": {
         "version": "detect"
      },
      "import/resolver": {
         "typescript": true
      }
   }
}
```

#### Prettier Configuration

```bash
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
```

**Crear `.prettierrc`:**

```json
{
   "semi": true,
   "trailingComma": "es5",
   "singleQuote": true,
   "printWidth": 80,
   "tabWidth": 2,
   "useTabs": false,
   "bracketSpacing": true,
   "arrowParens": "avoid"
}
```

#### TypeScript Configuration

**Actualizar `tsconfig.json`:**

```json
{
   "compilerOptions": {
      "target": "ES2020",
      "useDefineForClassFields": true,
      "lib": ["ES2020", "DOM", "DOM.Iterable"],
      "module": "ESNext",
      "skipLibCheck": true,
      "moduleResolution": "bundler",
      "allowImportingTsExtensions": true,
      "resolveJsonModule": true,
      "isolatedModules": true,
      "noEmit": true,
      "jsx": "react-jsx",
      "strict": true,
      "noUnusedLocals": true,
      "noUnusedParameters": true,
      "noFallthroughCasesInSwitch": true,
      "exactOptionalPropertyTypes": true,
      "noImplicitReturns": true,
      "noImplicitOverride": true
   },
   "include": ["src"],
   "references": [{ "path": "./tsconfig.node.json" }]
}
```

#### Vitest Configuration (Estándar Único para Vite)

```bash
# Vitest ya está incluido si usaste la configuración estándar de Vite
npm install --save-dev vitest @testing-library/react @testing-library/jest-dom jsdom
```

**Configuración en `vite.config.ts` (Método Estándar):**

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
   plugins: [react()],
   test: {
      globals: true,
      environment: "jsdom",
      setupFiles: ["./src/test/setup.ts"],
      css: true,
      coverage: {
         provider: "v8",
         reporter: ["text", "json", "html"],
         exclude: [
            "node_modules/",
            "src/test/",
            "**/*.d.ts",
            "src/main.tsx",
            "src/vite-env.d.ts",
         ],
         thresholds: {
            branches: 80,
            functions: 80,
            lines: 80,
            statements: 80,
         },
      },
   },
   resolve: {
      alias: {
         "@": path.resolve(__dirname, "./src"),
      },
   },
});
```

**Archivo de setup para tests (`src/test/setup.ts`):**

```typescript
// src/test/setup.ts
import "@testing-library/jest-dom";

// Global test utilities
global.ResizeObserver = vi.fn().mockImplementation(() => ({
   observe: vi.fn(),
   unobserve: vi.fn(),
   disconnect: vi.fn(),
}));
```

### 2. Configuración Backend (Laravel + PHP)

#### PHPStan Configuration

```bash
composer require --dev phpstan/phpstan
composer require --dev phpstan/phpstan-laravel
```

**Crear `phpstan.neon`:**

```neon
includes:
    - vendor/phpstan/phpstan-laravel/extension.neon

parameters:
    paths:
        - app
    level: 8
    ignoreErrors:
        - '#Call to an undefined method Illuminate\\Database\\Eloquent\\Builder#'
    excludePaths:
        - app/Infrastructure/Persistence/Eloquent/Migrations/*
        - bootstrap/*
        - storage/*
    checkMissingIterableValueType: false
```

#### PHP CS Fixer Configuration

```bash
composer require --dev friendsofphp/php-cs-fixer
```

**Crear `.php-cs-fixer.php`:**

```php
<?php

$finder = PhpCsFixer\Finder::create()
    ->in([
        __DIR__ . '/app',
        __DIR__ . '/config',
        __DIR__ . '/database',
        __DIR__ . '/routes',
        __DIR__ . '/tests',
    ])
    ->name('*.php')
    ->notName('*.blade.php')
    ->ignoreDotFiles(true)
    ->ignoreVCS(true);

return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        '@PHP81Migration' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => ['sort_algorithm' => 'alpha'],
        'no_unused_imports' => true,
        'not_operator_with_successor_space' => true,
        'trailing_comma_in_multiline' => true,
        'phpdoc_scalar' => true,
        'unary_operator_spaces' => true,
        'binary_operator_spaces' => true,
        'blank_line_before_statement' => [
            'statements' => ['break', 'continue', 'declare', 'return', 'throw', 'try'],
        ],
        'phpdoc_single_line_var_spacing' => true,
        'phpdoc_var_without_name' => true,
        'method_argument_space' => [
            'on_multiline' => 'ensure_fully_multiline',
            'keep_multiple_spaces_after_comma' => true,
        ],
    ])
    ->setFinder($finder);
```

#### Deptrac Configuration

```bash
composer require --dev qossmic/deptrac-shim
```

**Crear `deptrac.yaml`:**

```yaml
deptrac:
   paths:
      - ./app
   exclude_files:
      - "#.*test.*#"
   layers:
      - name: Domain
        collectors:
           - type: directory
             regex: app/Domain/.*
      - name: Application
        collectors:
           - type: directory
             regex: app/Application/.*
      - name: Infrastructure
        collectors:
           - type: directory
             regex: app/Infrastructure/.*
   ruleset:
      Domain: ~
      Application:
         - Domain
      Infrastructure:
         - Application
         - Domain
```

#### PHPUnit Configuration

**Actualizar `phpunit.xml`:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
        <testsuite name="Architecture">
            <directory suffix="Test.php">./tests/Architecture</directory>
        </testsuite>
    </testsuites>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
        <exclude>
            <directory suffix=".php">./app/Infrastructure/Persistence/Eloquent/Migrations</directory>
        </exclude>
        <report>
            <html outputDirectory="coverage"/>
            <text outputFile="coverage.txt"/>
        </report>
    </coverage>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="BCRYPT_ROUNDS" value="4"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="DB_CONNECTION" value="sqlite"/>
        <env name="DB_DATABASE" value=":memory:"/>
        <env name="MAIL_MAILER" value="array"/>
        <env name="QUEUE_CONNECTION" value="sync"/>
        <env name="SESSION_DRIVER" value="array"/>
    </php>
</phpunit>
```

### 3. Scripts de Automatización

#### Package.json (Frontend)

```json
{
   "scripts": {
      "dev": "vite",
      "build": "tsc && vite build",
      "preview": "vite preview",
      "lint": "eslint src --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
      "lint:fix": "eslint src --ext ts,tsx --fix",
      "format": "prettier --write \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
      "format:check": "prettier --check \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
      "test": "vitest",
      "test:ui": "vitest --ui",
      "test:run": "vitest run",
      "test:coverage": "vitest run --coverage",
      "test:watch": "vitest --watch",
      "type-check": "tsc --noEmit",
      "quality": "npm run type-check && npm run lint && npm run format:check && npm run test"
   }
}
```

#### Composer.json (Backend)

```json
{
   "scripts": {
      "analyse": "phpstan analyse",
      "format": "php-cs-fixer fix",
      "format:check": "php-cs-fixer fix --dry-run --diff",
      "test": "phpunit",
      "test:coverage": "phpunit --coverage-html coverage",
      "architecture": "deptrac analyse",
      "quality": ["@analyse", "@format:check", "@test", "@architecture"]
   }
}
```

### 4. Integración con VS Code

**Crear `.vscode/settings.json`:**

```json
{
   "editor.formatOnSave": true,
   "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true,
      "source.organizeImports": true
   },
   "eslint.validate": [
      "javascript",
      "javascriptreact",
      "typescript",
      "typescriptreact"
   ],
   "[php]": {
      "editor.defaultFormatter": "junstyle.php-cs-fixer"
   },
   "php-cs-fixer.onsave": true,
   "phpstan.enabled": true,
   "typescript.preferences.quoteStyle": "single",
   "files.associations": {
      "*.php": "php"
   }
}
```

**Crear `.vscode/extensions.json`:**

```json
{
   "recommendations": [
      "ms-vscode.vscode-typescript-next",
      "esbenp.prettier-vscode",
      "dbaeumer.vscode-eslint",
      "ms-vscode.vscode-json",
      "junstyle.php-cs-fixer",
      "phpstan.phpstan",
      "bmewburn.vscode-intelephense-client"
   ]
}
```

## Tips

1. **Configuración Incremental**: Implementa las herramientas gradualmente,
   comenzando por las más críticas (linting y formateo)

2. **Integración con CI/CD**: Asegúrate de que todas las validaciones se
   ejecuten en el pipeline de GitHub Actions

3. **Educación del Equipo**: Capacita al equipo sobre el uso y beneficios de
   estas herramientas

4. **Configuración Compartida**: Mantén las configuraciones en el repositorio
   para que todos usen las mismas reglas

5. **Performance**: Configura las herramientas para que no impacten
   significativamente el tiempo de desarrollo

6. **Excepciones Controladas**: Usa comentarios especiales para deshabilitar
   reglas solo cuando sea absolutamente necesario

7. **Revisión Regular**: Revisa y actualiza las configuraciones periódicamente
   según evolucione el proyecto

## Ejemplos

### Ejemplo de Test Unitario (Frontend)

```typescript
// src/components/Button/Button.test.tsx
import { fireEvent, render, screen } from "@testing-library/react";
import "@testing-library/jest-dom";
import { Button } from "./Button";

describe("Button Component", () => {
   it("should render correctly", () => {
      render(<Button>Click me</Button>);
      expect(screen.getByRole("button")).toBeInTheDocument();
   });

   it("should call onClick when clicked", () => {
      const handleClick = vi.fn();
      render(<Button onClick={handleClick}>Click me</Button>);

      fireEvent.click(screen.getByRole("button"));
      expect(handleClick).toHaveBeenCalledTimes(1);
   });
});
```

### Ejemplo de Test Unitario (Backend)

```php
<?php
// tests/Unit/Domain/User/CreateUserUseCaseTest.php
namespace Tests\Unit\Domain\User;

use App\Domain\User\User;
use App\Domain\User\UserRepository;
use App\Application\UseCases\User\CreateUserUseCase;
use App\Application\DTOs\UserDTO;
use PHPUnit\Framework\TestCase;
use Mockery;

class CreateUserUseCaseTest extends TestCase
{
    private CreateUserUseCase $useCase;
    private UserRepository $userRepository;

    protected function setUp(): void
    {
        $this->userRepository = Mockery::mock(UserRepository::class);
        $this->useCase = new CreateUserUseCase($this->userRepository);
    }

    public function test_should_create_user_successfully(): void
    {
        // Arrange
        $userData = new UserDTO(
            name: 'John Doe',
            email: 'john@example.com'
        );

        $this->userRepository
            ->shouldReceive('save')
            ->once()
            ->andReturn(new User('John Doe', 'john@example.com'));

        // Act
        $user = $this->useCase->execute($userData);

        // Assert
        $this->assertInstanceOf(User::class, $user);
        $this->assertEquals('John Doe', $user->getName());
    }

    protected function tearDown(): void
    {
        Mockery::close();
    }
}
```

### Ejemplo de Archivo .gitignore

```gitignore
# IDE
.vscode/
.idea/

# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
coverage/

# Environment files
.env
.env.local
.env.production

# Logs
*.log
npm-debug.log*

# Cache
.cache/
.phpunit.cache

# OS
.DS_Store
Thumbs.db

# Quality tools
.php-cs-fixer.cache
.phpstan.cache
```

---

## ⚠️ Herramientas PROHIBIDAS

**Frontend:**
- ❌ **Jest** (usar Vitest con Vite)
- ❌ **Webpack/Create React App** (usar Vite)
- ❌ **Yarn/pnpm** (usar npm)
- ❌ **Standard/JSHint** (usar ESLint)

**Backend:**
- ❌ **Pest** (usar PHPUnit)
- ❌ **PSR-12** (usar PSR-2)
- ❌ **PHPCS** (usar PHP-CS-Fixer)

**Justificación:**
- Mantener consistencia en herramientas
- Evitar configuraciones múltiples
- Reducir complejidad de setup
- Garantizar compatibilidad entre proyectos

---

## Navegación

[⬅️ Setup de ambientes (desarrollo, testing, staging, producción)](./setup-ambientes-multiples.md)
| [🏠 README Principal](../../README.md) |
[Setup de Laravel con Clean Architecture ➡️](./setup-laravel-clean-architecture.md)
