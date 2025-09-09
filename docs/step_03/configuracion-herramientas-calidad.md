# Configuración de Herramientas de Calidad de Código

## ¿Qué es?

La configuración de herramientas de calidad de código es el proceso de implementar y configurar herramientas automatizadas que analizan, validan y mejoran la calidad del código fuente. Estas herramientas incluyen linters, formateadores, analizadores estáticos, herramientas de testing y validadores de tipos que garantizan que el código siga estándares, buenas prácticas y principios de Clean Code.

## ¿Por qué es importante?

- **Consistencia**: Mantiene un estilo de código uniforme en todo el equipo de desarrollo
- **Calidad**: Detecta errores potenciales, vulnerabilidades y code smells antes de la producción
- **Mantenibilidad**: Facilita la lectura, comprensión y modificación del código a largo plazo
- **Productividad**: Automatiza tareas repetitivas de revisión de código y reduce el tiempo en code reviews
- **Confiabilidad**: Reduce bugs y problemas en producción mediante análisis temprano
- **Adherencia a estándares**: Garantiza que el código siga los principios SOLID, DRY y Clean Architecture

## ¿Qué debe incluir?

### Frontend (React + TypeScript + Vite)

1. **Linting y Formateo**

   - ESLint con configuraciones específicas para React y TypeScript
   - Prettier para formateo automático de código
   - Integración con el editor (VS Code)

2. **Análisis de Tipos**

   - TypeScript compiler con configuración estricta
   - Validación de tipos en tiempo de desarrollo

3. **Testing**

   - Jest para pruebas unitarias
   - React Testing Library para pruebas de componentes
   - Coverage reports automatizados

4. **Análisis de Calidad**
   - SonarJS para análisis estático
   - Herramientas de detección de dependencias circulares
   - Análisis de bundle size

### Backend (Laravel + Clean Architecture)

1. **Análisis Estático PHP**

   - PHPStan para análisis estático
   - PHP CS Fixer para formateo automático
   - PHPMD (PHP Mess Detector) para detección de code smells

2. **Testing**

   - PHPUnit para pruebas unitarias y de integración
   - Pest (opcional) para sintaxis más moderna
   - Coverage reports con Xdebug

3. **Validación de Arquitectura**

   - Deptrac para validar dependencias entre capas
   - PHP Architecture Tester para reglas de arquitectura

4. **Calidad de Código**
   - Herramientas de detección de duplicación
   - Análisis de complejidad ciclomática
   - Validación de PSR standards

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

#### Jest Configuration

```bash
npm install --save-dev jest @types/jest ts-jest @testing-library/react @testing-library/jest-dom
```

**Crear `jest.config.js`:**

```javascript
export default {
  preset: "ts-jest",
  testEnvironment: "jsdom",
  setupFilesAfterEnv: ["<rootDir>/src/setupTests.ts"],
  moduleNameMapping: {
    "^@/(.*)$": "<rootDir>/src/$1",
  },
  collectCoverageFrom: [
    "src/**/*.{ts,tsx}",
    "!src/**/*.d.ts",
    "!src/main.tsx",
    "!src/vite-env.d.ts",
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};
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
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
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

1. **Configuración Incremental**: Implementa las herramientas gradualmente, comenzando por las más críticas (linting y formateo)

2. **Integración con CI/CD**: Asegúrate de que todas las validaciones se ejecuten en el pipeline de GitHub Actions

3. **Educación del Equipo**: Capacita al equipo sobre el uso y beneficios de estas herramientas

4. **Configuración Compartida**: Mantén las configuraciones en el repositorio para que todos usen las mismas reglas

5. **Performance**: Configura las herramientas para que no impacten significativamente el tiempo de desarrollo

6. **Excepciones Controladas**: Usa comentarios especiales para deshabilitar reglas solo cuando sea absolutamente necesario

7. **Revisión Regular**: Revisa y actualiza las configuraciones periódicamente según evolucione el proyecto

## Ejemplos

### Ejemplo de Test Unitario (Frontend)

```typescript
// src/components/Button/Button.test.tsx
import { render, screen, fireEvent } from "@testing-library/react";
import "@testing-library/jest-dom";
import { Button } from "./Button";

describe("Button Component", () => {
  it("should render correctly", () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole("button")).toBeInTheDocument();
  });

  it("should call onClick when clicked", () => {
    const handleClick = jest.fn();
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

## Navegación

**Progreso en Configuración del Entorno de Desarrollo:**

- ✅
  [Configuración del Entorno - Introducción](./configuracion-entorno-desarrollo.md)
- ✅
  [Configuración de repositorios Git y GitHub](./configuracion-repositorios-git.md)
- ✅
  [Setup de entornos de desarrollo local con Docker](./setup-entorno-local-docker.md)
- ✅
  [Configuración de herramientas de CI/CD con GitHub Actions](./configuracion-cicd-github-actions.md)
- ✅ [Setup de ambientes (desarrollo, testing, staging, producción)](./setup-ambientes-multiples.md)
- ⏭️ **Configuración de herramientas de calidad de código** ← Estás aquí
- ⏭️
  [Setup de Laravel con Clean Architecture](./setup-laravel-clean-architecture.md)
- ⏭️
  [Configuración de React + TypeScript + Vite](./configuracion-react-typescript-vite.md)

---

### Siguiente Paso

Continúa con
[**Setup de Laravel con Clean Architecture**](./setup-laravel-clean-architecture.md)

[⬅️ Setup de ambientes (desarrollo, testing, staging, producción)](./setup-ambientes-multiples.md)
| [🏠 README Principal](../../README.md) |
[➡️ Setup de Laravel con Clean Architecture](./setup-laravel-clean-architecture.md)
