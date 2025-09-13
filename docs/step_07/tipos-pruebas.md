# Tipos de Pruebas

## ¿Qué es?

La clasificación de tipos de pruebas es fundamental para establecer una
estrategia integral de testing que cubra todos los aspectos del software. Cada
tipo de prueba tiene un propósito específico y se ejecuta en diferentes momentos
del ciclo de desarrollo, garantizando que el sistema funcione correctamente
desde múltiples perspectivas.

## ¿Por qué es importante?

- **Cobertura completa**: Asegura que se prueben todos los aspectos del sistema
- **Detección temprana**: Diferentes tipos de pruebas detectan diferentes tipos
  de problemas
- **Reducción de riesgos**: Minimiza la probabilidad de errores en producción
- **Calidad integral**: Garantiza tanto funcionalidad como características no
  funcionales
- **Planificación efectiva**: Permite organizar el esfuerzo de testing de manera
  eficiente
- **Comunicación clara**: Facilita la coordinación entre equipos de desarrollo y
  QA

## ¿Qué debe incluir?

### Clasificación por Nivel

#### Pruebas Unitarias

- Prueban componentes individuales de manera aislada
- Se ejecutan rápidamente y con frecuencia
- Utilizan mocks y stubs para dependencias
- Son la base de la pirámide de testing

#### Pruebas de Integración

- Verifican la comunicación entre componentes
- Pueden ser entre módulos o con servicios externos
- Incluyen pruebas de API y base de datos
- Validan contratos entre servicios

#### Pruebas End-to-End (E2E)

- Simulan el comportamiento real del usuario
- Prueban flujos completos de la aplicación
- Incluyen interfaz de usuario y backend
- Son más lentas pero más realistas

### Clasificación por Propósito

#### Pruebas Funcionales

- Verifican que el sistema hace lo que debe hacer
- Basadas en requerimientos específicos
- Incluyen casos de uso y user stories
- Validan reglas de negocio

#### Pruebas No Funcionales

- Evalúan características de calidad
- Incluyen performance, seguridad, usabilidad
- Miden atributos como velocidad y escalabilidad
- Verifican cumplimiento de estándares

### Clasificación por Estrategia

#### Pruebas de Humo (Smoke Testing)

- Verifican funcionalidad básica del sistema
- Se ejecutan después de deployments
- Detectan problemas críticos rápidamente
- Son un subconjunto de pruebas de regresión

#### Pruebas de Regresión

- Aseguran que nuevos cambios no rompan funcionalidad existente
- Se ejecutan después de modificaciones al código
- Pueden ser automatizadas o manuales
- Incluyen casos de prueba previamente exitosos

#### Pruebas Exploratorias

- Investigación simultánea de aprendizaje, diseño y testing
- No siguen scripts predefinidos
- Permiten descubrir casos no contemplados
- Aprovechan la creatividad del tester

## ¿Qué debo hacer?

### 1. Definir estrategia de testing por proyecto

```typescript
// src/testing/strategy.ts
export interface TestingStrategy {
   unitTests: {
      coverage: number;
      tools: string[];
      frequency: "continuous" | "daily" | "weekly";
   };
   integrationTests: {
      apiTests: boolean;
      databaseTests: boolean;
      externalServices: boolean;
   };
   e2eTests: {
      criticalPaths: string[];
      browsers: string[];
      devices: string[];
   };
   performanceTests: {
      loadTesting: boolean;
      stressTesting: boolean;
      volumeTesting: boolean;
   };
}

export const webAppStrategy: TestingStrategy = {
   unitTests: {
      coverage: 80,
      tools: ["Jest", "React Testing Library"],
      frequency: "continuous",
   },
   integrationTests: {
      apiTests: true,
      databaseTests: true,
      externalServices: true,
   },
   e2eTests: {
      criticalPaths: ["login", "checkout", "user-registration"],
      browsers: ["Chrome", "Firefox", "Safari"],
      devices: ["desktop", "tablet", "mobile"],
   },
   performanceTests: {
      loadTesting: true,
      stressTesting: true,
      volumeTesting: false,
   },
};
```

### 2. Implementar pirámide de testing

```bash
# Distribución recomendada de pruebas
# 70% - Pruebas Unitarias
# 20% - Pruebas de Integración
# 10% - Pruebas E2E

# Estructura de carpetas por tipo
tests/
├── unit/                    # 70% de las pruebas
│   ├── domain/
│   ├── application/
│   └── components/
├── integration/            # 20% de las pruebas
│   ├── api/
│   ├── database/
│   └── services/
└── e2e/                   # 10% de las pruebas
    ├── critical-paths/
    ├── user-flows/
    └── regression/
```

### 3. Configurar herramientas por tipo de prueba

#### Frontend (React + TypeScript)

```json
{
   "scripts": {
      "test:unit": "jest --testPathPattern=unit",
      "test:integration": "jest --testPathPattern=integration",
      "test:e2e": "cypress run",
      "test:watch": "jest --watch",
      "test:coverage": "jest --coverage",
      "test:ci": "jest --ci --coverage --watchAll=false"
   }
}
```

#### Backend (Laravel + PHP)

```php
<?php
// phpunit.xml
// Configuración para diferentes suites de pruebas

'<testsuites>
    <testsuite name="Unit">
        <directory suffix="Test.php">./tests/Unit</directory>
    </testsuite>
    <testsuite name="Feature">
        <directory suffix="Test.php">./tests/Feature</directory>
    </testsuite>
    <testsuite name="Integration">
        <directory suffix="Test.php">./tests/Integration</directory>
    </testsuite>
</testsuites>'
```

### 4. Establecer criterios de entrada y salida

```yaml
# .github/workflows/test-gates.yml
test_gates:
   unit_tests:
      min_coverage: 80%
      max_execution_time: 5min
      required_for: all_commits

   integration_tests:
      min_coverage: 60%
      max_execution_time: 15min
      required_for: pull_requests

   e2e_tests:
      critical_paths: required
      max_execution_time: 30min
      required_for: releases
```

### 5. Implementar testing continuo

```bash
#!/bin/bash
# scripts/continuous-testing.sh

echo "🚀 Iniciando pipeline de testing continuo..."

# 1. Pruebas unitarias (rápidas)
echo "⚡ Ejecutando pruebas unitarias..."
npm run test:unit || exit 1

# 2. Pruebas de integración (medianas)
echo "🔗 Ejecutando pruebas de integración..."
npm run test:integration || exit 1

# 3. Análisis de cobertura
echo "📊 Generando reporte de cobertura..."
npm run test:coverage

# 4. Pruebas E2E (solo en CI/CD)
if [ "$CI" = "true" ]; then
    echo "🌐 Ejecutando pruebas E2E..."
    npm run test:e2e
fi

echo "✅ Pipeline de testing completado"
```

## Tips

- **Pirámide de testing**: Más pruebas unitarias, menos E2E
- **Nombres descriptivos**: Usa nombres que expliquen qué se está probando
- **Aislamiento**: Cada prueba debe ser independiente
- **Datos de prueba**: Usa fixtures y factories para datos consistentes
- **Feedback rápido**: Las pruebas deben ejecutarse rápidamente
- **Mantenibilidad**: Refactoriza pruebas junto con el código
- **Documentación**: Las pruebas sirven como documentación viva
- **Automatización**: Integra todas las pruebas en CI/CD

## Ejemplos

### Ejemplo de Test Unitario (Frontend)

```typescript
// src/components/__tests__/UserCard.test.tsx
import { render, screen } from "@testing-library/react";
import { UserCard } from "../UserCard";

describe("UserCard", () => {
   const mockUser = {
      id: 1,
      name: "Juan Pérez",
      email: "juan@example.com",
      avatar: "https://example.com/avatar.jpg",
   };

   it("should render user information correctly", () => {
      render(<UserCard user={mockUser} />);

      expect(screen.getByText("Juan Pérez")).toBeInTheDocument();
      expect(screen.getByText("juan@example.com")).toBeInTheDocument();
      expect(screen.getByRole("img")).toHaveAttribute("src", mockUser.avatar);
   });

   it("should handle missing avatar gracefully", () => {
      const userWithoutAvatar = { ...mockUser, avatar: null };
      render(<UserCard user={userWithoutAvatar} />);

      expect(screen.getByRole("img")).toHaveAttribute(
         "src",
         "/default-avatar.png",
      );
   });
});
```

### Ejemplo de Test de Integración (Backend)

```php
<?php
// tests/Integration/UserRegistrationTest.php

namespace Tests\Integration;

use Tests\TestCase;
use App\Domain\Entities\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserRegistrationTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_register_successfully()
    {
        // Arrange
        $userData = [
            'name' => 'Juan Pérez',
            'email' => 'juan@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        // Act
        $response = $this->postJson('/api/v1/register', $userData);

        // Assert
        $response->assertStatus(201)
                ->assertJsonStructure([
                    'data' => ['id', 'name', 'email', 'created_at'],
                    'token'
                ]);

        $this->assertDatabaseHas('users', [
            'email' => 'juan@example.com',
            'name' => 'Juan Pérez'
        ]);
    }

    public function test_user_cannot_register_with_duplicate_email()
    {
        // Arrange
        User::factory()->create(['email' => 'juan@example.com']);

        $userData = [
            'name' => 'Otro Juan',
            'email' => 'juan@example.com', // Email duplicado
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        // Act
        $response = $this->postJson('/api/v1/register', $userData);

        // Assert
        $response->assertStatus(422)
                ->assertJsonValidationErrors(['email']);
    }
}
```

### Ejemplo de Test E2E (Cypress)

```typescript
// cypress/e2e/user-registration.cy.ts
describe("User Registration Flow", () => {
   beforeEach(() => {
      cy.visit("/register");
   });

   it("should allow user to register successfully", () => {
      // Arrange
      const userData = {
         name: "Juan Pérez",
         email: `juan${Date.now()}@example.com`,
         password: "password123",
      };

      // Act
      cy.get('[data-testid="name-input"]').type(userData.name);
      cy.get('[data-testid="email-input"]').type(userData.email);
      cy.get('[data-testid="password-input"]').type(userData.password);
      cy.get('[data-testid="password-confirmation-input"]').type(
         userData.password,
      );
      cy.get('[data-testid="register-button"]').click();

      // Assert
      cy.url().should("include", "/dashboard");
      cy.get('[data-testid="welcome-message"]').should(
         "contain",
         `Bienvenido, ${userData.name}`,
      );
   });

   it("should show validation errors for invalid data", () => {
      // Act
      cy.get('[data-testid="register-button"]').click();

      // Assert
      cy.get('[data-testid="name-error"]').should(
         "contain",
         "El nombre es requerido",
      );
      cy.get('[data-testid="email-error"]').should(
         "contain",
         "El email es requerido",
      );
   });
});
```

### Matriz de Tipos de Pruebas

| Tipo        | Velocidad | Cobertura | Confiabilidad | Mantenimiento | Uso Recomendado            |
| ----------- | --------- | --------- | ------------- | ------------- | -------------------------- |
| Unitarias   | ⚡⚡⚡    | 🎯🎯      | ⭐⭐⭐        | ✅✅✅        | Base de la pirámide        |
| Integración | ⚡⚡      | 🎯🎯🎯    | ⭐⭐          | ✅✅          | Comunicación entre módulos |
| E2E         | ⚡        | 🎯🎯🎯🎯  | ⭐            | ✅            | Flujos críticos            |
| API         | ⚡⚡      | 🎯🎯🎯    | ⭐⭐⭐        | ✅✅          | Contratos de servicios     |
| Performance | ⚡        | 🎯🎯      | ⭐⭐          | ✅            | Carga y rendimiento        |

## Navegación

[⬅️ Testing y QA](./testing-qa.md) | [🏠 README Principal](../../README.md) |
[Configuración de Herramientas de Testing ➡️](./configuracion-herramientas-testing.md)
