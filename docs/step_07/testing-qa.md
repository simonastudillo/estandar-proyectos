# Testing y Quality Assurance

## Conocimientos involucrados
- Estrategias de QA y testing
- Herramientas como Jest, Selenium, SonarQube
- Normas y estándares de calidad

## Responsable
- Equipo de QA

# Testing y Quality Assurance

## ¿Qué es?

Testing y Quality Assurance (QA) es el proceso sistemático de verificación y
validación que garantiza que el software cumple con los requerimientos
funcionales y no funcionales establecidos. Incluye diferentes tipos de pruebas
automatizadas y manuales, revisiones de código y auditorías de calidad para
asegurar la entrega de un producto robusto y confiable.

## ¿Por qué es importante?

- **Prevención de errores**: Detecta bugs antes de que lleguen a producción
- **Calidad del código**: Mantiene estándares altos de programación
- **Confiabilidad**: Asegura que el sistema funcione correctamente bajo
  diferentes condiciones
- **Experiencia del usuario**: Garantiza una interfaz intuitiva y funcional
- **Costos reducidos**: Es más económico corregir errores en etapas tempranas
- **Cumplimiento**: Verifica que se cumplan todos los requerimientos
- **Mantenibilidad**: Facilita futuras modificaciones y mejoras

## ¿Qué debe incluir?

### Testing Funcional

- Pruebas unitarias para todos los casos de uso
- Pruebas de integración entre componentes
- Pruebas end-to-end del flujo completo
- Pruebas de API con diferentes escenarios
- Validación de reglas de negocio

### Testing No Funcional

- Pruebas de rendimiento y carga
- Pruebas de seguridad siguiendo OWASP
- Pruebas de usabilidad y UX
- Pruebas de compatibilidad entre navegadores
- Pruebas de responsividad móvil

### Quality Assurance

- Revisiones de código (code reviews)
- Auditorías de calidad de código
- Análisis estático de código
- Documentación de procesos de QA
- Métricas de cobertura de pruebas

## ¿Qué debo hacer?

### 1. Configurar el entorno de testing

```bash
# Backend - PHPUnit
composer require --dev phpunit/phpunit
composer require --dev mockery/mockery

# Frontend - Jest + Testing Library
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
npm install --save-dev @testing-library/user-event
```

### 2. Implementar testing funcional automatizado

- Crear pruebas unitarias para cada caso de uso
- Desarrollar pruebas de integración para APIs
- Configurar pruebas end-to-end con herramientas como Cypress

### 3. Ejecutar testing de performance y seguridad

- Realizar pruebas de carga con herramientas especializadas
- Implementar análisis de seguridad OWASP
- Validar tiempos de respuesta y recursos

### 4. Conducir testing de usabilidad

- Realizar pruebas con usuarios reales
- Validar la experiencia de usuario
- Verificar accesibilidad web

### 5. Establecer procesos de code review

- Definir criterios de revisión de código
- Implementar flujos de aprobación
- Configurar herramientas de análisis estático

### 6. Realizar auditorías de calidad

- Analizar métricas de código
- Verificar cumplimiento de estándares
- Documentar hallazgos y mejoras

## Tips

- **Automatización primero**: Prioriza pruebas automatizadas sobre manuales
- **Testing temprano**: Implementa pruebas desde el desarrollo, no al final
- **Cobertura balanceada**: Apunta a 80-90% de cobertura, no 100%
- **Pruebas significativas**: Enfócate en casos de uso reales, no en métricas
- **Documentación clara**: Describe qué prueba cada test y por qué
- **Ambiente aislado**: Usa datos de prueba y ambientes dedicados
- **CI/CD integrado**: Ejecuta pruebas automáticamente en cada commit
- **Feedback rápido**: Configura notificaciones inmediatas de fallos

## Ejemplos

### Estructura de Testing Backend (Laravel)

```
tests/
├── Feature/           # Pruebas de integración
│   ├── Auth/
│   ├── User/
│   └── Api/V1/
├── Unit/              # Pruebas unitarias
│   ├── Domain/
│   │   ├── Entities/
│   │   ├── ValueObjects/
│   │   └── Services/
│   └── Application/
│       └── UseCases/
└── TestCase.php       # Clase base para tests
```

### Ejemplo de Test Unitario (UseCase)

```php
<?php

namespace Tests\Unit\Application\UseCases;

use Tests\TestCase;
use App\Domain\Entities\User;
use App\Application\UseCases\CreateUserUseCase;
use App\Domain\Repositories\UserRepository;

class CreateUserUseCaseTest extends TestCase
{
    public function test_can_create_user_successfully()
    {
        // Arrange
        $mockRepository = $this->mock(UserRepository::class);
        $useCase = new CreateUserUseCase($mockRepository);

        $userData = [
            'name' => 'Juan Pérez',
            'email' => 'juan@example.com',
            'password' => 'password123'
        ];

        $mockRepository->shouldReceive('save')
                      ->once()
                      ->andReturn(new User($userData));

        // Act
        $result = $useCase->execute($userData);

        // Assert
        $this->assertInstanceOf(User::class, $result);
        $this->assertEquals('Juan Pérez', $result->getName());
    }
}
```

### Estructura de Testing Frontend (React)

```
src/
├── __tests__/         # Tests principales
├── components/
│   └── __tests__/     # Tests de componentes
├── hooks/
│   └── __tests__/     # Tests de custom hooks
├── services/
│   └── __tests__/     # Tests de servicios
└── utils/
    └── __tests__/     # Tests de utilidades
```

### Ejemplo de Test de Componente React

```typescript
import { fireEvent, render, screen } from "@testing-library/react";
import { Provider } from "react-redux";
import { store } from "../store/store";
import LoginForm from "./LoginForm";

describe("LoginForm", () => {
   const renderWithProvider = (component: React.ReactElement) => {
      return render(<Provider store={store}>{component}</Provider>);
   };

   it("should render login form with email and password fields", () => {
      renderWithProvider(<LoginForm />);

      expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
      expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
      expect(screen.getByRole("button", { name: /login/i }))
         .toBeInTheDocument();
   });

   it("should show validation error for invalid email", async () => {
      renderWithProvider(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      const submitButton = screen.getByRole("button", { name: /login/i });

      fireEvent.change(emailInput, { target: { value: "invalid-email" } });
      fireEvent.click(submitButton);

      expect(
         await screen.findByText(/email debe ser válido/i),
      ).toBeInTheDocument();
   });
});
```

### Pipeline de CI/CD con Testing

```yaml
# .github/workflows/testing.yml
name: Testing Pipeline

on: [push, pull_request]

jobs:
   backend-tests:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
         - name: Install dependencies
           run: composer install
         - name: Run tests
           run: ./vendor/bin/phpunit --coverage-clover coverage.xml
         - name: Upload coverage
           uses: codecov/codecov-action@v3

   frontend-tests:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"
         - name: Install dependencies
           run: npm ci
         - name: Run tests
           run: npm run test:coverage
         - name: Upload coverage
           uses: codecov/codecov-action@v3
```

## Navegación

[⬅️ Etapa 6: Preparación App Stores](../step_06/preparacion-app-stores.md) |
[🏠 README Principal](../../README.md) |
[Tipos de Pruebas ➡️](./tipos-pruebas.md)
