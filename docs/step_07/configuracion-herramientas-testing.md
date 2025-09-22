# Configuración de Herramientas de Testing

## Conocimientos involucrados
- Configuración de frameworks de testing
- Integración continua y pipelines de CI/CD
- Herramientas como Jest, Cypress, Selenium

## Responsable
- Equipo de QA y DevOps

# Configuración de Herramientas de Testing

## ¿Qué es?

La configuración de herramientas de testing establece la infraestructura
necesaria para ejecutar pruebas automatizadas en cada stack tecnológico del
proyecto. Incluye la configuración de frameworks de testing, reportes de
cobertura, mocks, fixtures y la integración con el entorno de desarrollo para
garantizar que todas las pruebas se ejecuten de manera consistente y confiable.

## ¿Por qué es importante?

- **Consistencia**: Garantiza que todos los desarrolladores ejecuten tests de la
  misma manera
- **Automatización**: Facilita la ejecución de pruebas en CI/CD
- **Cobertura**: Permite medir y reportar la cobertura de código efectivamente
- **Eficiencia**: Optimiza el tiempo de ejecución de las pruebas
- **Depuración**: Facilita la identificación y corrección de fallos en tests
- **Calidad**: Asegura que las herramientas funcionen correctamente
- **Escalabilidad**: Permite agregar nuevos tipos de pruebas fácilmente

## ¿Qué debe incluir?

### Configuración Frontend (React + TypeScript + Jest)

#### Jest Configuration (`jest.config.js`)

```javascript
module.exports = {
   preset: "ts-jest",
   testEnvironment: "jsdom",
   setupFilesAfterEnv: ["<rootDir>/src/__tests__/setup.ts"],
   moduleNameMapping: {
      "^@/(.*)$": "<rootDir>/src/$1",
      "\\.(css|less|scss|sass)$": "identity-obj-proxy",
      "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$":
         "<rootDir>/src/__tests__/__mocks__/fileMock.js",
   },
   collectCoverageFrom: [
      "src/**/*.{ts,tsx}",
      "!src/**/*.d.ts",
      "!src/index.tsx",
      "!src/reportWebVitals.ts",
      "!src/__tests__/**/*",
      "!src/**/*.stories.{ts,tsx}",
   ],
   coverageThreshold: {
      global: {
         branches: 80,
         functions: 80,
         lines: 80,
         statements: 80,
      },
   },
   testMatch: [
      "<rootDir>/src/**/__tests__/**/*.{ts,tsx}",
      "<rootDir>/src/**/*.{test,spec}.{ts,tsx}",
   ],
   transform: {
      "^.+\\.(ts|tsx)$": "ts-jest",
   },
   moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"],
};
```

#### Testing Setup (`src/__tests__/setup.ts`)

```typescript
import "@testing-library/jest-dom";
import { configure } from "@testing-library/react";
import { server } from "./mocks/server";

// Configure Testing Library
configure({ testIdAttribute: "data-testid" });

// Mock IntersectionObserver
global.IntersectionObserver = class IntersectionObserver {
   constructor() {}
   disconnect() {}
   observe() {}
   unobserve() {}
};

// Mock ResizeObserver
global.ResizeObserver = class ResizeObserver {
   constructor() {}
   disconnect() {}
   observe() {}
   unobserve() {}
};

// Mock window.matchMedia
Object.defineProperty(window, "matchMedia", {
   writable: true,
   value: jest.fn().mockImplementation((query) => ({
      matches: false,
      media: query,
      onchange: null,
      addListener: jest.fn(),
      removeListener: jest.fn(),
      addEventListener: jest.fn(),
      removeEventListener: jest.fn(),
      dispatchEvent: jest.fn(),
   })),
});

// Setup MSW
beforeAll(() => server.listen({ onUnhandledRequest: "error" }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

// Mock console errors for cleaner test output
const originalError = console.error;
beforeAll(() => {
   console.error = (...args: any[]) => {
      if (
         typeof args[0] === "string" &&
         args[0].includes("Warning: ReactDOM.render is deprecated")
      ) {
         return;
      }
      originalError.call(console, ...args);
   };
});

afterAll(() => {
   console.error = originalError;
});
```

#### MSW Setup (`src/__tests__/mocks/server.ts`)

```typescript
import { setupServer } from "msw/node";
import { rest } from "msw";

export const handlers = [
   rest.get("/api/v1/users", (req, res, ctx) => {
      return res(
         ctx.status(200),
         ctx.json([
            { id: 1, name: "John Doe", email: "john@example.com" },
            { id: 2, name: "Jane Smith", email: "jane@example.com" },
         ]),
      );
   }),

   rest.post("/api/v1/users", (req, res, ctx) => {
      return res(
         ctx.status(201),
         ctx.json({ id: 3, name: "New User", email: "new@example.com" }),
      );
   }),

   rest.get("/api/v1/users/:id", (req, res, ctx) => {
      const { id } = req.params;
      return res(
         ctx.status(200),
         ctx.json({ id: Number(id), name: "User", email: "user@example.com" }),
      );
   }),
];

export const server = setupServer(...handlers);
```

#### TypeScript Testing Types (`src/__tests__/types.ts`)

```typescript
import { ReactElement } from "react";
import { RenderOptions } from "@testing-library/react";

// Custom render function types
export interface CustomRenderOptions extends Omit<RenderOptions, "wrapper"> {
   preloadedState?: any;
   store?: any;
   route?: string;
}

export type CustomRender = (
   ui: ReactElement,
   options?: CustomRenderOptions,
) => ReturnType<typeof render>;

// Mock data types
export interface MockUser {
   id: number;
   name: string;
   email: string;
   role?: string;
}

export interface MockApiResponse<T = any> {
   data: T;
   message?: string;
   status: number;
}

// Test utilities types
export interface TestWrapper {
   store?: any;
   router?: any;
   theme?: any;
}
```

### Configuración Backend (Laravel + PHPUnit)

#### PHPUnit Configuration (`phpunit.xml`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/10.0/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
         processIsolation="false"
         stopOnFailure="false"
         cacheDirectory=".phpunit.cache"
         backupGlobals="false">
    
    <testsuites>
        <testsuite name="Unit">
            <directory>tests/Unit</directory>
        </testsuite>
        <testsuite name="Integration">
            <directory>tests/Integration</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory>tests/Feature</directory>
        </testsuite>
    </testsuites>
    
    <source>
        <include>
            <directory>app/Domain</directory>
            <directory>app/Application</directory>
            <directory>app/Infrastructure</directory>
        </include>
        <exclude>
            <directory>app/Infrastructure/Console</directory>
            <directory>app/Infrastructure/Http/Middleware</directory>
            <file>app/Infrastructure/Providers/RouteServiceProvider.php</file>
        </exclude>
    </source>
    
    <coverage>
        <report>
            <html outputDirectory="storage/coverage-html"/>
            <text outputFile="storage/coverage.txt"/>
            <clover outputFile="storage/coverage.xml"/>
        </report>
    </coverage>
    
    <php>
        <server name="APP_ENV" value="testing"/>
        <server name="BCRYPT_ROUNDS" value="4"/>
        <server name="CACHE_DRIVER" value="array"/>
        <server name="DB_CONNECTION" value="sqlite"/>
        <server name="DB_DATABASE" value=":memory:"/>
        <server name="MAIL_MAILER" value="array"/>
        <server name="QUEUE_CONNECTION" value="sync"/>
        <server name="SESSION_DRIVER" value="array"/>
        <server name="TELESCOPE_ENABLED" value="false"/>
    </php>
</phpunit>
```

#### Testing Configuration (`config/testing.php`)

```php
<?php

return [
    'database' => [
        'default' => 'sqlite',
        'connections' => [
            'sqlite' => [
                'driver' => 'sqlite',
                'database' => ':memory:',
                'prefix' => '',
                'foreign_key_constraints' => true,
            ],
        ],
    ],
    
    'cache' => [
        'default' => 'array',
    ],
    
    'session' => [
        'driver' => 'array',
    ],
    
    'queue' => [
        'default' => 'sync',
    ],
    
    'mail' => [
        'default' => 'array',
    ],
    
    'filesystem' => [
        'default' => 'local',
        'disks' => [
            'local' => [
                'driver' => 'local',
                'root' => storage_path('app/testing'),
            ],
        ],
    ],
    
    'services' => [
        'external_api' => [
            'base_url' => 'http://localhost:8080',
            'timeout' => 5,
            'mock' => true,
        ],
    ],
];
```

#### Base Test Case (`tests/TestCase.php`)

```php
<?php

namespace Tests;

use Illuminate\Foundation\Testing\TestCase as BaseTestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;

abstract class TestCase extends BaseTestCase
{
    use CreatesApplication, RefreshDatabase, WithFaker;

    protected function setUp(): void
    {
        parent::setUp();
        
        // Disable exception handling for better error visibility
        $this->withoutExceptionHandling();
        
        // Set up common test configuration
        $this->setUpDatabase();
        $this->setUpAuthentication();
        $this->setUpMocks();
    }

    protected function setUpDatabase(): void
    {
        // Run specific migrations for testing
        $this->artisan('migrate', ['--database' => 'sqlite']);
        
        // Seed essential data
        $this->seed(['RoleSeeder', 'PermissionSeeder']);
    }

    protected function setUpAuthentication(): void
    {
        // Configure Sanctum for testing
        \Laravel\Sanctum\Sanctum::actingAs(
            \App\Infrastructure\Persistence\Eloquent\User::factory()->create(),
            ['*']
        );
    }

    protected function setUpMocks(): void
    {
        // Mock external services
        $this->mock(\App\Infrastructure\Services\ExternalApiService::class)
             ->shouldReceive('get')
             ->andReturn(['status' => 'success']);
    }

    /**
     * Create authenticated user for testing
     */
    protected function createAuthenticatedUser(array $attributes = []): \App\Domain\Entities\User
    {
        $user = \App\Infrastructure\Persistence\Eloquent\User::factory()
                    ->create($attributes);
        
        $this->actingAs($user);
        
        return $user->toDomainEntity();
    }

    /**
     * Assert API response structure
     */
    protected function assertApiResponse(
        \Illuminate\Testing\TestResponse $response,
        int $status = 200,
        array $structure = []
    ): void {
        $response->assertStatus($status);
        
        if (!empty($structure)) {
            $response->assertJsonStructure($structure);
        }
        
        // Assert common API response format
        $response->assertJsonStructure([
            'data',
            'message',
            'status'
        ]);
    }
}
```

### Configuración Mobile (React Native + Jest + Detox)

#### Jest Configuration (`jest.config.js`)

```javascript
module.exports = {
   preset: "react-native",
   setupFilesAfterEnv: ["<rootDir>/src/__tests__/setup.ts"],
   moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"],
   transform: {
      "^.+\\.(js|jsx|ts|tsx)$": "babel-jest",
   },
   transformIgnorePatterns: [
      "node_modules/(?!(react-native|@react-native|@react-navigation)/)",
   ],
   testMatch: [
      "<rootDir>/src/**/__tests__/**/*.{ts,tsx}",
      "<rootDir>/src/**/*.{test,spec}.{ts,tsx}",
   ],
   collectCoverageFrom: [
      "src/**/*.{ts,tsx}",
      "!src/**/*.d.ts",
      "!src/__tests__/**/*",
      "!src/**/*.stories.{ts,tsx}",
   ],
   coverageThreshold: {
      global: {
         branches: 75,
         functions: 75,
         lines: 75,
         statements: 75,
      },
   },
   moduleNameMapping: {
      "^@/(.*)$": "<rootDir>/src/$1",
   },
};
```

#### Detox Configuration (`.detoxrc.js`)

```javascript
module.exports = {
   testRunner: {
      args: {
         "$0": "jest",
         config: "e2e/jest.config.js",
      },
      jest: {
         setupTimeout: 120000,
      },
   },
   apps: {
      "ios.debug": {
         type: "ios.app",
         binaryPath:
            "ios/build/Build/Products/Debug-iphonesimulator/YourApp.app",
         build:
            "xcodebuild -workspace ios/YourApp.xcworkspace -scheme YourApp -configuration Debug -sdk iphonesimulator -derivedDataPath ios/build",
      },
      "android.debug": {
         type: "android.apk",
         binaryPath: "android/app/build/outputs/apk/debug/app-debug.apk",
         build:
            "cd android && ./gradlew assembleDebug assembleAndroidTest -DtestBuildType=debug",
      },
   },
   devices: {
      simulator: {
         type: "ios.simulator",
         device: {
            type: "iPhone 14",
         },
      },
      emulator: {
         type: "android.emulator",
         device: {
            avdName: "Pixel_3a_API_30_x86",
         },
      },
   },
   configurations: {
      "ios.sim.debug": {
         device: "simulator",
         app: "ios.debug",
      },
      "android.emu.debug": {
         device: "emulator",
         app: "android.debug",
      },
   },
};
```

## ¿Qué debo hacer?

### 1. Instalar dependencias de testing

#### Frontend

```bash
# Core testing dependencies
npm install --save-dev jest @types/jest ts-jest
npm install --save-dev @testing-library/react @testing-library/jest-dom
npm install --save-dev @testing-library/user-event

# Mocking and utilities
npm install --save-dev msw identity-obj-proxy
npm install --save-dev jest-environment-jsdom

# Coverage reporting
npm install --save-dev @istanbuljs/nyc-config-typescript
```

#### Backend

```bash
# PHPUnit and testing utilities
composer require --dev phpunit/phpunit
composer require --dev mockery/mockery
composer require --dev fakerphp/faker

# Laravel testing helpers
composer require --dev laravel/sanctum
composer require --dev orchestra/testbench
```

#### Mobile

```bash
# React Native testing
npm install --save-dev jest react-test-renderer
npm install --save-dev @testing-library/react-native

# E2E testing
npm install --save-dev detox
```

### 2. Configurar archivos de configuración

- Crear los archivos de configuración mostrados arriba
- Configurar scripts en `package.json` / `composer.json`
- Establecer variables de entorno para testing

### 3. Configurar scripts de testing

#### Frontend (`package.json`)

```json
{
   "scripts": {
      "test": "jest",
      "test:watch": "jest --watch",
      "test:coverage": "jest --coverage",
      "test:ci": "jest --ci --coverage --watchAll=false"
   }
}
```

#### Backend (`composer.json`)

```json
{
   "scripts": {
      "test": "phpunit",
      "test:unit": "phpunit --testsuite=Unit",
      "test:integration": "phpunit --testsuite=Integration",
      "test:feature": "phpunit --testsuite=Feature",
      "test:coverage": "phpunit --coverage-html storage/coverage"
   }
}
```

### 4. Configurar IDE integration

#### VS Code (`.vscode/settings.json`)

```json
{
   "jest.autoRun": "watch",
   "jest.showCoverageOnLoad": true,
   "php.validate.executablePath": "/usr/bin/php",
   "phpunit.command": "vendor/bin/phpunit",
   "phpunit.args": ["--configuration", "phpunit.xml"]
}
```

## Tips

- **Configuración por entorno**: Mantén configuraciones separadas para
  desarrollo, CI y local
- **Mocks centralizados**: Organiza todos los mocks en carpetas dedicadas
- **Coverage realista**: No busques 100% de cobertura, enfócate en código
  crítico
- **Performance**: Usa `--maxWorkers=50%` en Jest para optimizar recursos
- **Debugging**: Configura sourcemaps para debugging de tests
- **Parallelización**: Configura testing paralelo para reducir tiempos
- **Cache**: Usa cache de Jest y PHPUnit para ejecuciones más rápidas

## Ejemplos

### Test Runner personalizado (Frontend)

```typescript
// src/__tests__/utils/render.tsx
import React, { ReactElement } from "react";
import { render } from "@testing-library/react";
import { Provider } from "react-redux";
import { BrowserRouter } from "react-router-dom";
import { store } from "@/store";

export const renderWithProviders = (
   ui: ReactElement,
   options = {},
) => {
   function Wrapper({ children }: { children: React.ReactNode }) {
      return (
         <Provider store={store}>
            <BrowserRouter>
               {children}
            </BrowserRouter>
         </Provider>
      );
   }

   return render(ui, { wrapper: Wrapper, ...options });
};
```

### Test Base personalizado (Backend)

```php
<?php
// tests/Unit/UnitTestCase.php
namespace Tests\Unit;

use Tests\TestCase;
use Mockery;

abstract class UnitTestCase extends TestCase
{
    protected function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }

    protected function mockRepository(string $repository): Mockery\MockInterface
    {
        $mock = Mockery::mock($repository);
        $this->app->instance($repository, $mock);
        return $mock;
    }
}
```

## Navegación

[⬅️ Tipos de Pruebas](./tipos-pruebas.md) |
[🏠 README Principal](../../README.md) |
[Ejemplos de Testing por Lenguaje ➡️](./ejemplos-testing-por-lenguaje.md)
