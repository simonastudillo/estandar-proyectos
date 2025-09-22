# Testing por Entorno

## Conocimientos involucrados
- Configuración de entornos de testing
- Herramientas como Jest, Cypress, PHPUnit
- Estrategias específicas para cada ambiente

## Responsable
- Equipo de QA y DevOps

# Testing por Entorno

## ¿Qué es?

El testing por entorno es la práctica de adaptar las estrategias,
configuraciones y tipos de pruebas según el ambiente donde se ejecutan
(desarrollo, testing, staging, producción). Cada entorno tiene características
específicas, limitaciones y objetivos diferentes que requieren enfoques de
testing particulares para garantizar la calidad y estabilidad del software en
todas las etapas del ciclo de vida.

## ¿Por qué es importante?

- **Validación progresiva**: Cada entorno valida aspectos específicos antes del
  siguiente
- **Detección temprana**: Problemas específicos de cada ambiente se detectan a
  tiempo
- **Optimización de recursos**: Tests apropiados para las características de
  cada entorno
- **Confianza en releases**: Validación completa antes de llegar a producción
- **Debugging efectivo**: Estrategias específicas para cada tipo de problema
- **Performance realista**: Testing con datos y condiciones reales por entorno
- **Compliance**: Cumplimiento de regulaciones específicas por ambiente

## ¿Qué debe incluir?

### Entorno de Desarrollo (Development)

#### Características del Entorno

- Código en constante cambio
- Datos de prueba controlados
- Recursos limitados localmente
- Acceso completo para debugging

#### Estrategia de Testing

```typescript
// jest.config.development.js
module.exports = {
   preset: "ts-jest",
   testEnvironment: "jsdom",
   setupFilesAfterEnv: ["<rootDir>/src/__tests__/setup.development.ts"],

   // Fast feedback - reduced coverage for speed
   collectCoverage: false,

   // Watch mode optimizations
   watchman: true,
   watchPlugins: [
      "jest-watch-typeahead/filename",
      "jest-watch-typeahead/testname",
   ],

   // Mock external services
   moduleNameMapping: {
      "^@/(.*)$": "<rootDir>/src/$1",
      "^@/services/(.*)$": "<rootDir>/src/__tests__/__mocks__/services/$1",
   },

   // Fast execution
   testTimeout: 5000,
   maxWorkers: "50%",
};
```

#### Tests Recomendados

- **Unitarios**: Componentes, hooks, utilidades
- **Integración básica**: Mocks de APIs externas
- **Snapshot testing**: UI components
- **Hot reload validation**: Cambios en vivo

#### Configuración Específica

```typescript
// src/__tests__/setup.development.ts
import "@testing-library/jest-dom";
import { configure } from "@testing-library/react";

// More verbose output for development
configure({
   testIdAttribute: "data-testid",
   computedStyleSupportsPseudoElements: false,
});

// Mock all external APIs
jest.mock("@/services/api", () => ({
   userService: {
      getUsers: jest.fn().mockResolvedValue({ data: [] }),
      createUser: jest.fn().mockResolvedValue({ data: { id: 1 } }),
   },
}));

// Console warnings and errors visible
const originalError = console.error;
const originalWarn = console.warn;

beforeAll(() => {
   console.error = (...args: any[]) => {
      originalError.call(console, "🔴 [TEST ERROR]:", ...args);
   };

   console.warn = (...args: any[]) => {
      originalWarn.call(console, "🟡 [TEST WARNING]:", ...args);
   };
});

afterAll(() => {
   console.error = originalError;
   console.warn = originalWarn;
});
```

### Entorno de Testing (QA)

#### Características del Entorno

- Datos de prueba más realistas
- Integración con servicios externos
- Performance cercana a producción
- Automatización completa

#### Estrategia de Testing

```typescript
// jest.config.testing.js
module.exports = {
   preset: "ts-jest",
   testEnvironment: "jsdom",
   setupFilesAfterEnv: ["<rootDir>/src/__tests__/setup.testing.ts"],

   // Full coverage required
   collectCoverage: true,
   coverageThreshold: {
      global: {
         branches: 80,
         functions: 85,
         lines: 85,
         statements: 85,
      },
   },

   // Real integrations with test doubles
   moduleNameMapping: {
      "^@/(.*)$": "<rootDir>/src/$1",
   },

   // Longer timeouts for integration tests
   testTimeout: 30000,

   // Parallel execution
   maxWorkers: 4,
};
```

#### Tests Recomendados

- **Integración completa**: APIs reales con datos de test
- **End-to-end críticos**: Flujos principales
- **Performance básico**: Tiempo de carga, memory leaks
- **Cross-browser**: Compatibilidad básica
- **Regression**: Suite completa de regresión

#### Configuración Backend Testing

```php
<?php
// config/testing.php
return [
    'database' => [
        'default' => 'mysql_testing',
        'connections' => [
            'mysql_testing' => [
                'driver' => 'mysql',
                'host' => env('DB_TEST_HOST', '127.0.0.1'),
                'port' => env('DB_TEST_PORT', '3306'),
                'database' => env('DB_TEST_DATABASE', 'app_testing'),
                'username' => env('DB_TEST_USERNAME', 'test_user'),
                'password' => env('DB_TEST_PASSWORD', 'test_password'),
            ],
        ],
    ],
    
    'external_services' => [
        'payment_gateway' => [
            'base_url' => 'https://sandbox.paymentapi.com',
            'api_key' => env('PAYMENT_TEST_KEY'),
            'timeout' => 10,
        ],
        'email_service' => [
            'driver' => 'log', // Log emails instead of sending
        ],
        'file_storage' => [
            'disk' => 'testing', // Separate storage for tests
        ],
    ],
    
    'cache' => [
        'default' => 'redis_testing',
    ],
    
    'logging' => [
        'level' => 'debug',
        'channels' => ['testing'],
    ],
];
```

### Entorno de Staging (Pre-Production)

#### Características del Entorno

- Réplica exacta de producción
- Datos anonimizados de producción
- Integración con servicios reales
- Testing de deployment

#### Estrategia de Testing

```typescript
// jest.config.staging.js
module.exports = {
   preset: "ts-jest",
   testEnvironment: "jsdom",
   setupFilesAfterEnv: ["<rootDir>/src/__tests__/setup.staging.ts"],

   // Production-like coverage
   collectCoverage: true,
   coverageThreshold: {
      global: {
         branches: 85,
         functions: 90,
         lines: 90,
         statements: 90,
      },
   },

   // Real environment variables
   testEnvironment: "node",
   globalSetup: "<rootDir>/src/__tests__/global-setup.staging.js",

   // Production timeouts
   testTimeout: 60000,

   // Limited parallelism to avoid overloading staging
   maxWorkers: 2,
};
```

#### Tests Recomendados

- **Smoke tests**: Funcionalidad crítica post-deployment
- **End-to-end completos**: Todos los flujos de usuario
- **Performance real**: Con volúmenes de datos reales
- **Security testing**: Validación de seguridad
- **Integration testing**: Servicios externos reales
- **Load testing básico**: Capacidad del sistema

#### Smoke Tests Configuration

```typescript
// src/__tests__/smoke/staging-smoke.test.ts
import { expect, test } from "@playwright/test";

const STAGING_BASE_URL = process.env.STAGING_URL || "https://staging.myapp.com";

test.describe("Staging Smoke Tests", () => {
   test("Homepage loads successfully", async ({ page }) => {
      await page.goto(STAGING_BASE_URL);
      await expect(page).toHaveTitle(/MyApp/);
      await expect(page.locator('[data-testid="header"]')).toBeVisible();
   });

   test("User can login", async ({ page }) => {
      await page.goto(`${STAGING_BASE_URL}/login`);

      await page.fill('[data-testid="email"]', "test@staging.com");
      await page.fill('[data-testid="password"]', "staging123");
      await page.click('[data-testid="login-button"]');

      await expect(page).toHaveURL(/\/dashboard/);
      await expect(page.locator('[data-testid="user-menu"]')).toBeVisible();
   });

   test("API health check", async ({ request }) => {
      const response = await request.get(`${STAGING_BASE_URL}/api/health`);
      expect(response.status()).toBe(200);

      const data = await response.json();
      expect(data.status).toBe("healthy");
      expect(data.database).toBe("connected");
   });

   test("Critical user flow works", async ({ page }) => {
      // Login
      await page.goto(`${STAGING_BASE_URL}/login`);
      await page.fill('[data-testid="email"]', "test@staging.com");
      await page.fill('[data-testid="password"]', "staging123");
      await page.click('[data-testid="login-button"]');

      // Navigate to main feature
      await page.click('[data-testid="create-item"]');
      await page.fill('[data-testid="item-name"]', "Staging Test Item");
      await page.click('[data-testid="save-item"]');

      // Verify creation
      await expect(page.locator("text=Staging Test Item")).toBeVisible();
   });
});
```

### Entorno de Producción (Production)

#### Características del Entorno

- Datos reales de usuarios
- Alto volumen y concurrencia
- Monitoring continuo
- Zero downtime requirements

#### Estrategia de Testing

```typescript
// Configuración de monitoreo en producción
// src/monitoring/production-tests.ts
class ProductionMonitoring {
   private metricsCollector: MetricsCollector;

   constructor() {
      this.metricsCollector = new MetricsCollector({
         endpoint: process.env.METRICS_ENDPOINT,
         apiKey: process.env.METRICS_API_KEY,
      });
   }

   async healthCheck(): Promise<HealthStatus> {
      const checks = await Promise.allSettled([
         this.checkDatabaseConnection(),
         this.checkExternalAPIs(),
         this.checkCacheStatus(),
         this.checkFileStorage(),
         this.checkMemoryUsage(),
         this.checkResponseTimes(),
      ]);

      const results = checks.map((check, index) => ({
         name: this.getCheckName(index),
         status: check.status === "fulfilled" ? "pass" : "fail",
         message: check.status === "fulfilled" ? check.value : check.reason,
         timestamp: new Date().toISOString(),
      }));

      return {
         overall: results.every((r) => r.status === "pass")
            ? "healthy"
            : "unhealthy",
         checks: results,
      };
   }

   async performSyntheticTests(): Promise<SyntheticTestResults> {
      const tests = [
         this.testUserLogin(),
         this.testCriticalAPIs(),
         this.testPaymentFlow(),
         this.testDataConsistency(),
      ];

      const results = await Promise.allSettled(tests);

      // Send metrics to monitoring system
      this.metricsCollector.record("synthetic_tests", {
         passed: results.filter((r) => r.status === "fulfilled").length,
         failed: results.filter((r) => r.status === "rejected").length,
         timestamp: Date.now(),
      });

      return this.formatResults(results);
   }
}
```

#### Tests Recomendados

- **Synthetic monitoring**: Tests automáticos continúos
- **Health checks**: Validación de servicios críticos
- **Performance monitoring**: APM y métricas en tiempo real
- **Canary testing**: Validación de nuevos releases
- **A/B testing validation**: Correcta segmentación de usuarios
- **Disaster recovery**: Tests de recuperación

#### Canary Testing Implementation

```typescript
// src/canary/canary-tests.ts
interface CanaryConfig {
   percentage: number;
   duration: number;
   successThreshold: number;
   rollbackThreshold: number;
}

class CanaryTesting {
   private config: CanaryConfig;
   private metrics: MetricsService;

   constructor(config: CanaryConfig) {
      this.config = config;
      this.metrics = new MetricsService();
   }

   async runCanaryValidation(version: string): Promise<CanaryResult> {
      const startTime = Date.now();
      const endTime = startTime + (this.config.duration * 1000);

      console.log(`🚀 Starting canary test for version ${version}`);
      console.log(
         `📊 ${this.config.percentage}% traffic for ${this.config.duration}s`,
      );

      const results = {
         version,
         startTime,
         endTime,
         tests: [] as CanaryTestResult[],
         metrics: {} as CanaryMetrics,
      };

      while (Date.now() < endTime) {
         // Run critical path tests
         const testResults = await this.runCriticalTests();
         results.tests.push(testResults);

         // Collect metrics
         const currentMetrics = await this.collectMetrics();
         this.updateMetrics(results.metrics, currentMetrics);

         // Check for immediate failures
         if (this.shouldRollback(results)) {
            console.log("❌ Canary test failed - initiating rollback");
            return { ...results, status: "failed", action: "rollback" };
         }

         await this.sleep(10000); // Check every 10 seconds
      }

      const finalStatus = this.evaluateResults(results);
      console.log(`✅ Canary test completed with status: ${finalStatus}`);

      return { ...results, status: finalStatus, action: "proceed" };
   }

   private async runCriticalTests(): Promise<CanaryTestResult> {
      const tests = [
         () => this.testLogin(),
         () => this.testAPIEndpoints(),
         () => this.testDatabaseOperations(),
         () => this.testPaymentProcessing(),
      ];

      const results = await Promise.allSettled(tests.map((test) => test()));

      return {
         timestamp: Date.now(),
         passed: results.filter((r) => r.status === "fulfilled").length,
         failed: results.filter((r) => r.status === "rejected").length,
         errors: results
            .filter((r) => r.status === "rejected")
            .map((r) => (r as PromiseRejectedResult).reason),
      };
   }
}
```

## ¿Qué debo hacer?

### 1. Configurar pipelines específicos por entorno

```yaml
# .github/workflows/environment-testing.yml
name: Environment-Specific Testing

on:
   push:
      branches: [develop, main]
   pull_request:
      branches: [main]

jobs:
   development-tests:
      if: github.ref == 'refs/heads/develop'
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v4
         - name: Development Tests
           run: |
              npm ci
              npm run test:dev
              npm run test:unit

   testing-environment:
      if: github.ref == 'refs/heads/main'
      runs-on: ubuntu-latest
      environment: testing
      steps:
         - uses: actions/checkout@v4
         - name: Deploy to Testing
           run: |
              # Deploy application
              npm run deploy:testing
         - name: Integration Tests
           run: |
              npm run test:integration
              npm run test:e2e
         - name: Performance Tests
           run: |
              npm run test:performance

   staging-validation:
      needs: testing-environment
      runs-on: ubuntu-latest
      environment: staging
      steps:
         - name: Deploy to Staging
           run: npm run deploy:staging
         - name: Smoke Tests
           run: npm run test:smoke
         - name: Security Tests
           run: npm run test:security
         - name: Load Tests
           run: npm run test:load

   production-monitoring:
      if: github.ref == 'refs/heads/main' && github.event_name == 'push'
      runs-on: ubuntu-latest
      environment: production
      steps:
         - name: Canary Deployment
           run: npm run deploy:canary
         - name: Canary Validation
           run: npm run test:canary
         - name: Full Production Deployment
           if: success()
           run: npm run deploy:production
```

### 2. Establecer métricas por entorno

```typescript
// src/config/testing-metrics.ts
export const ENVIRONMENT_METRICS = {
   development: {
      testTimeout: 5000,
      coverageThreshold: 0, // No coverage requirement
      parallelWorkers: "50%",
      retries: 0,
      verbose: true,
   },

   testing: {
      testTimeout: 30000,
      coverageThreshold: 80,
      parallelWorkers: 4,
      retries: 2,
      verbose: false,
   },

   staging: {
      testTimeout: 60000,
      coverageThreshold: 85,
      parallelWorkers: 2,
      retries: 3,
      verbose: false,
      includePerformance: true,
   },

   production: {
      testTimeout: 120000,
      syntheticInterval: 60000, // 1 minute
      healthCheckInterval: 30000, // 30 seconds
      alertThresholds: {
         errorRate: 0.01, // 1%
         responseTime: 2000, // 2 seconds
         availability: 0.999, // 99.9%
      },
   },
} as const;
```

### 3. Configurar data management por entorno

```typescript
// src/testing/data-management.ts
export class TestDataManager {
   private environment: string;

   constructor(environment: string) {
      this.environment = environment;
   }

   async setupTestData(): Promise<void> {
      switch (this.environment) {
         case "development":
            await this.setupDevelopmentData();
            break;
         case "testing":
            await this.setupTestingData();
            break;
         case "staging":
            await this.setupStagingData();
            break;
         case "production":
            // No test data setup in production
            break;
      }
   }

   private async setupDevelopmentData(): Promise<void> {
      // Minimal, fast test data
      const users = [
         { id: 1, email: "dev@test.com", name: "Dev User" },
         { id: 2, email: "admin@test.com", name: "Admin User" },
      ];

      await this.insertTestUsers(users);
   }

   private async setupTestingData(): Promise<void> {
      // Comprehensive test scenarios
      const users = await this.generateTestUsers(100);
      const orders = await this.generateTestOrders(500);
      const products = await this.generateTestProducts(50);

      await Promise.all([
         this.insertTestUsers(users),
         this.insertTestOrders(orders),
         this.insertTestProducts(products),
      ]);
   }

   private async setupStagingData(): Promise<void> {
      // Production-like volume with anonymized data
      const anonymizedData = await this.getAnonymizedProductionData();
      await this.loadAnonymizedData(anonymizedData);
   }
}
```

### 4. Implementar monitoring diferenciado

```typescript
// src/monitoring/environment-monitoring.ts
export class EnvironmentMonitoring {
   private environment: string;
   private alerting: AlertingService;

   constructor(environment: string) {
      this.environment = environment;
      this.alerting = new AlertingService(environment);
   }

   async startMonitoring(): Promise<void> {
      const config = this.getMonitoringConfig();

      // Health checks
      setInterval(() => {
         this.performHealthChecks();
      }, config.healthCheckInterval);

      // Performance monitoring
      if (config.performanceMonitoring) {
         setInterval(() => {
            this.collectPerformanceMetrics();
         }, config.performanceInterval);
      }

      // Error rate monitoring
      setInterval(() => {
         this.checkErrorRates();
      }, config.errorCheckInterval);
   }

   private getMonitoringConfig() {
      const configs = {
         development: {
            healthCheckInterval: 300000, // 5 minutes
            performanceMonitoring: false,
            errorCheckInterval: 600000, // 10 minutes
            alerting: false,
         },
         testing: {
            healthCheckInterval: 120000, // 2 minutes
            performanceMonitoring: true,
            performanceInterval: 60000, // 1 minute
            errorCheckInterval: 180000, // 3 minutes
            alerting: false,
         },
         staging: {
            healthCheckInterval: 60000, // 1 minute
            performanceMonitoring: true,
            performanceInterval: 30000, // 30 seconds
            errorCheckInterval: 120000, // 2 minutes
            alerting: true,
         },
         production: {
            healthCheckInterval: 30000, // 30 seconds
            performanceMonitoring: true,
            performanceInterval: 15000, // 15 seconds
            errorCheckInterval: 60000, // 1 minute
            alerting: true,
            detailedMetrics: true,
         },
      };

      return configs[this.environment] || configs.development;
   }
}
```

## Tips

- **Progresión gradual**: Cada entorno debe validar aspectos específicos antes
  del siguiente
- **Datos apropiados**: Usa datos realistas pero seguros en cada ambiente
- **Automatización inteligente**: No todo debe ser automático en todos los
  entornos
- **Monitoring continuo**: Producción requiere monitoreo 24/7, desarrollo no
- **Rollback strategy**: Siempre ten plan de rollback, especialmente en staging
  y producción
- **Performance testing**: Realízalo en entornos con recursos similares a
  producción
- **Security testing**: Incrementa la intensidad según se acerca a producción
- **Documentation**: Documenta las diferencias y configuraciones por entorno

## Ejemplos de Configuración por Stack

### Frontend Environment Config

```typescript
// src/config/environment.ts
export interface EnvironmentConfig {
   apiBaseUrl: string;
   enableMocking: boolean;
   logLevel: "debug" | "info" | "warn" | "error";
   enableAnalytics: boolean;
   enableServiceWorker: boolean;
   cacheStrategy: "aggressive" | "normal" | "minimal";
}

export const environments: Record<string, EnvironmentConfig> = {
   development: {
      apiBaseUrl: "http://localhost:8000/api",
      enableMocking: true,
      logLevel: "debug",
      enableAnalytics: false,
      enableServiceWorker: false,
      cacheStrategy: "minimal",
   },

   testing: {
      apiBaseUrl: "https://api.testing.myapp.com",
      enableMocking: false,
      logLevel: "info",
      enableAnalytics: false,
      enableServiceWorker: true,
      cacheStrategy: "normal",
   },

   staging: {
      apiBaseUrl: "https://api.staging.myapp.com",
      enableMocking: false,
      logLevel: "warn",
      enableAnalytics: true,
      enableServiceWorker: true,
      cacheStrategy: "aggressive",
   },

   production: {
      apiBaseUrl: "https://api.myapp.com",
      enableMocking: false,
      logLevel: "error",
      enableAnalytics: true,
      enableServiceWorker: true,
      cacheStrategy: "aggressive",
   },
};
```

### Backend Environment Config

```php
<?php
// config/environments.php
return [
    'development' => [
        'database' => [
            'connections' => ['sqlite'],
            'migrations' => 'fresh_seed',
            'foreign_key_checks' => false,
        ],
        'cache' => ['driver' => 'array'],
        'mail' => ['driver' => 'log'],
        'logging' => ['level' => 'debug'],
        'external_apis' => ['mock' => true],
    ],
    
    'testing' => [
        'database' => [
            'connections' => ['mysql'],
            'migrations' => 'migrate_fresh',
            'foreign_key_checks' => true,
        ],
        'cache' => ['driver' => 'redis'],
        'mail' => ['driver' => 'array'],
        'logging' => ['level' => 'info'],
        'external_apis' => ['mock' => false, 'sandbox' => true],
    ],
    
    'staging' => [
        'database' => [
            'connections' => ['mysql'],
            'migrations' => 'migrate',
            'foreign_key_checks' => true,
        ],
        'cache' => ['driver' => 'redis'],
        'mail' => ['driver' => 'smtp'],
        'logging' => ['level' => 'warning'],
        'external_apis' => ['mock' => false, 'sandbox' => true],
        'monitoring' => ['enabled' => true],
    ],
    
    'production' => [
        'database' => [
            'connections' => ['mysql'],
            'migrations' => 'none',
            'foreign_key_checks' => true,
        ],
        'cache' => ['driver' => 'redis'],
        'mail' => ['driver' => 'smtp'],
        'logging' => ['level' => 'error'],
        'external_apis' => ['mock' => false, 'sandbox' => false],
        'monitoring' => ['enabled' => true, 'detailed' => true],
        'security' => ['strict_mode' => true],
    ],
];
```

## Navegación

[⬅️ Cobertura de Pruebas (Coverage)](./cobertura-pruebas.md) |
[Inicio](../../README.md) |
[Checklist Testing Proyectos ➡️](./checklist-testing-proyectos.md)
