# Convenciones y Estándares

## ¿Qué es?

Las convenciones y estándares definen un conjunto unificado de reglas para
nomenclatura, estilo de código, comentarios y buenas prácticas que garantizan
consistencia, legibilidad y mantenibilidad en todo el proyecto. Estas reglas son
fundamentales para el trabajo en equipo y la escalabilidad del código.

## ¿Por qué es importante?

- **Consistencia**: Garantiza que todo el código del proyecto siga las mismas
  reglas independientemente del desarrollador
- **Legibilidad**: Facilita la comprensión del código para cualquier miembro del
  equipo
- **Mantenibilidad**: Reduce el tiempo necesario para entender y modificar
  código existente
- **Onboarding**: Acelera la integración de nuevos desarrolladores al equipo
- **Calidad**: Previene errores comunes y promueve mejores prácticas de
  desarrollo
- **Colaboración**: Elimina discusiones sobre estilo y permite enfocarse en la
  lógica del negocio
- **Automatización**: Permite implementar herramientas de linting y formateo
  automático

## ¿Qué debe incluir?

### Nomenclaturas y Convenciones Generales

...

### Reglas por Lenguaje

...

### Estilo de Código

...

### Comentarios y Anotaciones

...

### Buenas Prácticas

...

### Reglas del Sentido Común

...

### Control de Versiones Semántico (SemVer)

...

### Convención de Mensajes de Commit

...

### Nomenclaturas y Convenciones Generales

#### Archivos y Carpetas

- **Carpetas**: kebab-case para estructura de proyecto
- **Componentes React**: PascalCase (ej: `UserProfile.tsx`)
- **Archivos utilitarios**: camelCase (ej: `apiHelpers.ts`)
- **Archivos de configuración**: kebab-case (ej: `eslint.config.js`)
- **Assets**: kebab-case (ej: `user-avatar.png`)

#### Variables y Funciones

```typescript
// TypeScript/JavaScript
const userName = "john"; // camelCase para variables
const MAX_RETRY_ATTEMPTS = 3; // UPPER_SNAKE_CASE para constantes
const API_BASE_URL = process.env.REACT_APP_API_URL; // constantes de entorno

function getUserById(id: string): User {} // camelCase para funciones
class UserService {} // PascalCase para clases
interface UserData {} // PascalCase para interfaces
type ApiResponse<T> = {}; // PascalCase para tipos
```

```php
// PHP
$userName = 'john'; // camelCase para variables
const MAX_RETRY_ATTEMPTS = 3; // UPPER_SNAKE_CASE para constantes

public function getUserById(string $id): User { } // camelCase para métodos
class UserService { } // PascalCase para clases
interface UserRepositoryInterface { } // PascalCase + Interface suffix
```

#### Base de Datos

```sql
-- Tablas: plural, snake_case, inglés
CREATE TABLE users (
    id BIGINT UNSIGNED PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL, -- snake_case
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Claves foráneas: tabla_singular + _id
ALTER TABLE orders ADD FOREIGN KEY (user_id) REFERENCES users(id);

-- Índices: idx_tabla_columna(s)
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- Triggers: accion_tabla_evento
CREATE TRIGGER audit_users_update BEFORE UPDATE ON users;
```

#### APIs RESTful

```bash
# URLs: kebab-case, plural para recursos
GET /api/v1/users
GET /api/v1/user-profiles
POST /api/v1/order-items

# Parámetros: snake_case para consistencia con BD
{
  "first_name": "John",
  "last_name": "Doe",
  "created_at": "2025-09-11T10:00:00Z"
}
```

### Reglas por Lenguaje

#### TypeScript/JavaScript

```typescript
// 1. Tipado estricto
interface User {
   id: string;
   name: string;
   email: string;
   createdAt: Date;
}

// 2. No usar 'any', preferir tipos específicos
const users: User[] = []; // ✅ Correcto
const data: any = []; // ❌ Evitar

// 3. Usar const assertions cuando sea apropiado
const themes = ["light", "dark"] as const;
type Theme = typeof themes[number]; // 'light' | 'dark'

// 4. Preferir arrow functions para callbacks
const userNames = users.map((user) => user.name);

// 5. Destructuring cuando mejore la legibilidad
const { name, email } = user;

// 6. Optional chaining y nullish coalescing
const userName = user?.profile?.name ?? "Anonymous";
```

#### PHP (Laravel)

```php
<?php
// 1. Strict types siempre
declare(strict_types=1);

// 2. Type hints obligatorios
public function createUser(string $name, string $email): User
{
    // implementación
}

// 3. Return types explícitos
public function getUsers(): Collection
{
    return $this->userRepository->findAll();
}

// 4. Usar final para clases que no deben extenderse
final class UserService
{
    // ...
}

// 5. Propiedades readonly cuando sea apropiado
public function __construct(
    private readonly UserRepositoryInterface $userRepository
) {}
```

#### SQL

```sql
-- 1. Palabras clave en MAYÚSCULAS
SELECT u.id, u.name
FROM users u
WHERE u.status = 'active'
ORDER BY u.created_at DESC;

-- 2. Aliases cortos y descriptivos
SELECT 
    u.name AS user_name,
    p.title AS post_title
FROM users u
INNER JOIN posts p ON u.id = p.user_id;

-- 3. Indentación para consultas complejas
SELECT 
    COUNT(*) AS total_users,
    DATE(created_at) AS registration_date
FROM users
WHERE status IN ('active', 'pending')
    AND created_at >= '2025-01-01'
GROUP BY DATE(created_at)
HAVING COUNT(*) > 10
ORDER BY registration_date DESC;
```

### Estilo de Código

#### Formateo Automático

```json
// .prettierrc
{
   "semi": true,
   "trailingComma": "es5",
   "singleQuote": true,
   "printWidth": 80,
   "tabWidth": 2,
   "useTabs": false
}
```

#### ESLint Rules

```javascript
// .eslintrc.js
module.exports = {
   rules: {
      "prefer-const": "error",
      "no-var": "error",
      "no-unused-vars": "error",
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/explicit-function-return-type": "warn",
   },
};
```

#### PHP CS Fixer

```php
// .php-cs-fixer.php
return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => true,
        'no_unused_imports' => true,
    ]);
```

### Comentarios y Anotaciones

#### Principios Generales

1. **El código debe ser auto-explicativo**: Los comentarios explican el "por
   qué", no el "qué"
2. **Mantener sincronizados**: Actualizar comentarios cuando se modifica el
   código
3. **Ser concisos**: Evitar comentarios obvios o redundantes

#### TypeScript/JavaScript

````typescript
/**
 * Calcula el descuento aplicable basado en reglas de negocio complejas.
 *
 * @param user - Usuario al que se aplicará el descuento
 * @param orderTotal - Total de la orden antes de descuentos
 * @returns Porcentaje de descuento (0-1)
 *
 * @example
 * ```typescript
 * const discount = calculateDiscount(user, 100);
 * console.log(discount); // 0.15 (15%)
 * ```
 */
function calculateDiscount(user: User, orderTotal: number): number {
   // Aplicar descuento por fidelidad solo si el usuario tiene más de 5 órdenes
   // Regla de negocio definida en JIRA-1234
   if (user.orderCount > 5) {
      return 0.15;
   }

   return 0;
}

// TODO: Implementar descuentos por temporada (Sprint 3)
// FIXME: Este cálculo no considera usuarios VIP - Bug #567
// NOTE: Esta función será refactorizada cuando se implemente el nuevo sistema de pricing
````

#### PHP (Laravel)

```php
<?php

/**
 * Servicio para gestionar la lógica de negocio de usuarios.
 * 
 * Encapsula todas las operaciones complejas relacionadas con usuarios
 * siguiendo los principios de Domain-Driven Design.
 */
final class UserService
{
    /**
     * Crea un nuevo usuario aplicando validaciones de negocio.
     * 
     * @param CreateUserDTO $data Datos validados del usuario
     * @return User Usuario creado
     * 
     * @throws UserAlreadyExistsException Si el email ya está registrado
     * @throws InvalidUserDataException Si los datos no cumplen reglas de negocio
     */
    public function createUser(CreateUserDTO $data): User
    {
        // Verificar unicidad del email en el dominio
        // Esta validación debe estar aquí y no solo en BD por reglas de negocio
        if ($this->userRepository->existsByEmail($data->email)) {
            throw new UserAlreadyExistsException($data->email);
        }
        
        return $this->userRepository->create($data);
    }
}
```

#### Comentarios en SQL

```sql
-- Consulta optimizada para el dashboard principal
-- Utiliza índice compuesto idx_orders_user_status para mejor performance
SELECT 
    u.name,
    COUNT(o.id) AS total_orders,
    SUM(o.total) AS total_spent
FROM users u
    -- LEFT JOIN para incluir usuarios sin órdenes
    LEFT JOIN orders o ON u.id = o.user_id 
        AND o.status = 'completed' -- Solo órdenes completadas
        AND o.created_at >= DATE_SUB(NOW(), INTERVAL 30 DAY) -- Últimos 30 días
WHERE u.status = 'active'
GROUP BY u.id, u.name
ORDER BY total_spent DESC;
```

### Buenas Prácticas

#### Principios SOLID

```typescript
// Single Responsibility Principle
class UserValidator {
   validate(user: CreateUserRequest): ValidationResult {
      // Solo se encarga de validar usuarios
   }
}

class UserService {
   createUser(data: CreateUserRequest): User {
      // Solo se encarga de la lógica de negocio
   }
}

// Open/Closed Principle
interface NotificationSender {
   send(message: string, recipient: string): void;
}

class EmailSender implements NotificationSender {
   send(message: string, recipient: string): void {
      // Implementación específica para email
   }
}

// Dependency Inversion Principle
class UserController {
   constructor(
      private userService: UserServiceInterface, // Depende de abstracción
      private notificationSender: NotificationSender,
   ) {}
}
```

#### Clean Code

```typescript
// ❌ Evitar
function calc(x: number, y: number, t: string): number {
   if (t === "a") {
      return x + y;
   } else if (t === "b") {
      return x - y;
   }
   return 0;
}

// ✅ Preferir
enum OperationType {
   ADD = "add",
   SUBTRACT = "subtract",
}

function calculateResult(
   firstNumber: number,
   secondNumber: number,
   operation: OperationType,
): number {
   switch (operation) {
      case OperationType.ADD:
         return firstNumber + secondNumber;
      case OperationType.SUBTRACT:
         return firstNumber - secondNumber;
      default:
         throw new Error(`Unsupported operation: ${operation}`);
   }
}
```

#### Manejo de Errores

```typescript
// TypeScript - Usar Result pattern
type Result<T, E = Error> = {
   success: true;
   data: T;
} | {
   success: false;
   error: E;
};

async function getUser(id: string): Promise<Result<User, UserNotFoundError>> {
   try {
      const user = await this.userRepository.findById(id);
      return { success: true, data: user };
   } catch (error) {
      return { success: false, error: new UserNotFoundError(id) };
   }
}
```

```php
// PHP - Usar excepciones específicas del dominio
final class UserService
{
    public function createUser(CreateUserDTO $data): User
    {
        try {
            return $this->userRepository->create($data);
        } catch (DatabaseException $e) {
            throw new UserCreationFailedException(
                'Failed to create user: ' . $e->getMessage(),
                previous: $e
            );
        }
    }
}
```

### Reglas del Sentido Común

#### Principios Fundamentales

1. **Claridad sobre Cleverness**
   - Código claro es mejor que código "inteligente"
   - Si necesitas explicar cómo funciona, probablemente necesita refactoring
   - Optimiza para el desarrollador que leerá el código en 6 meses

```typescript
// ❌ Evitar: Cleverness innecesario
const f = (x: any[]) => x.reduce((a, b) => a + (b.v || 0), 0);

// ✅ Preferir: Código claro y expresivo
function calculateTotalValue(items: { value?: number }[]): number {
   return items.reduce((total, item) => total + (item.value || 0), 0);
}
```

2. **Principio DRY Aplicado con Sentido**
   - No repetir código, pero tampoco sobre-abstraer prematuramente
   - Tres o más repeticiones justifican abstracción
   - La abstracción debe tener un propósito claro del dominio

```typescript
// ❌ Repetición innecesaria
function validateUserEmail(email: string): boolean {
   return email.includes("@") && email.includes(".") && email.length > 5;
}

function validateAdminEmail(email: string): boolean {
   return email.includes("@") && email.includes(".") && email.length > 5;
}

// ✅ Abstracción apropiada
function isValidEmailFormat(email: string): boolean {
   return email.includes("@") && email.includes(".") && email.length > 5;
}

function validateUserEmail(email: string): boolean {
   return isValidEmailFormat(email);
}

function validateAdminEmail(email: string): boolean {
   return isValidEmailFormat(email) && email.endsWith("@company.com");
}
```

3. **Nombrado Descriptivo y Consistente**
   - Los nombres deben explicar la intención sin necesidad de comentarios
   - Usar el mismo vocabulario del dominio del negocio
   - Evitar abreviaciones y nombres genéricos

```typescript
// ❌ Nombres poco descriptivos
const d = new Date();
const u = users.filter((x) => x.active);
function proc(data: any): any {}

// ✅ Nombres descriptivos
const currentDate = new Date();
const activeUsers = users.filter((user) => user.isActive);
function processPaymentTransaction(transaction: PaymentData): PaymentResult {}
```

4. **Funciones Pequeñas y Enfocadas**
   - Una función debe hacer una sola cosa y hacerla bien
   - Máximo 20-30 líneas por función
   - Si necesitas scrollear para ver toda la función, es muy larga

```typescript
// ❌ Función que hace demasiadas cosas
function processUser(userData: any): any {
   // Validar datos
   if (!userData.email || !userData.name) return null;

   // Crear usuario
   const user = { ...userData, id: generateId() };

   // Enviar email
   sendWelcomeEmail(user.email);

   // Guardar en BD
   database.save(user);

   // Actualizar cache
   cache.invalidate("users");

   return user;
}

// ✅ Funciones enfocadas
function validateUserData(userData: UserInput): boolean {
   return Boolean(userData.email && userData.name);
}

function createUserEntity(userData: UserInput): User {
   return { ...userData, id: generateId() };
}

function processNewUser(userData: UserInput): User | null {
   if (!validateUserData(userData)) {
      return null;
   }

   const user = createUserEntity(userData);

   // Coordinar las operaciones relacionadas
   sendWelcomeEmail(user.email);
   database.save(user);
   cache.invalidate("users");

   return user;
}
```

5. **Comentarios Cuando Son Necesarios**
   - Explicar el "por qué", no el "qué"
   - Los comentarios deben agregar valor, no repetir el código
   - Actualizar comentarios cuando cambies el código

```typescript
// ❌ Comentarios obvios
let i = 0; // inicializar contador en 0
i++; // incrementar contador

// ✅ Comentarios que agregan valor
// Usar algoritmo de Luhn para validar números de tarjeta de crédito
// según estándar ISO/IEC 7812-1
function validateCreditCard(cardNumber: string): boolean {
   // Implementación del algoritmo
}

// Timeout de 30 segundos porque el servicio externo es lento
// y preferimos fallar gracefully que bloquear la UI
const API_TIMEOUT = 30000;
```

6. **Manejo de Errores Consistente y Predecible**
   - Fallar rápido y con mensajes claros
   - Ser consistente en cómo manejas errores similares
   - No silenciar errores sin una buena razón

```typescript
// ❌ Manejo inconsistente de errores
function getUser(id: string): User | null | undefined {
   try {
      return userService.find(id);
   } catch {
      return null; // A veces null
   }
}

function getOrder(id: string): Order | undefined {
   try {
      return orderService.find(id);
   } catch {
      return undefined; // A veces undefined
   }
}

// ✅ Manejo consistente
type Result<T> = { success: true; data: T } | { success: false; error: string };

function getUser(id: string): Result<User> {
   try {
      const user = userService.find(id);
      return { success: true, data: user };
   } catch (error) {
      return {
         success: false,
         error: `Failed to fetch user: ${error.message}`,
      };
   }
}

function getOrder(id: string): Result<Order> {
   try {
      const order = orderService.find(id);
      return { success: true, data: order };
   } catch (error) {
      return {
         success: false,
         error: `Failed to fetch order: ${error.message}`,
      };
   }
}
```

7. **Performance vs. Legibilidad**
   - Optimizar cuando sea necesario, no prematuramente
   - Medir antes de optimizar
   - La legibilidad es más importante que micro-optimizaciones

```typescript
// ❌ Optimización prematura que reduce legibilidad
const u = new Map(users.map((u) => [u.id, u]));
const result = orders.map((o) => ({ ...o, user: u.get(o.uid) }));

// ✅ Código claro, optimizar solo si es necesario
const userMap = new Map(users.map((user) => [user.id, user]));
const ordersWithUsers = orders.map((order) => ({
   ...order,
   user: userMap.get(order.userId),
}));
```

8. **Consistencia en el Proyecto**
   - Seguir las convenciones establecidas en el proyecto
   - Si cambias un patrón, cambiarlo en todo el proyecto
   - Documentar decisiones arquitectónicas importantes

```typescript
// Si el proyecto usa Result pattern, úsalo consistentemente
// Si usa excepciones, úsalas consistentemente
// No mezclar patrones sin una razón válida

// ✅ Consistente con el patrón del proyecto
class UserService {
   async createUser(data: CreateUserRequest): Promise<Result<User>> {
      // Implementación usando Result pattern
   }

   async updateUser(
      id: string,
      data: UpdateUserRequest,
   ): Promise<Result<User>> {
      // Implementación usando Result pattern
   }
}
```

#### Señales de Alerta (Code Smells)

- **Funciones con más de 3-4 parámetros**: Considera usar un objeto de
  configuración
- **Clases con más de 10 métodos públicos**: Probablemente hace demasiadas cosas
- **Archivos con más de 300-400 líneas**: Considera dividir en módulos más
  pequeños
- **Comentarios que explican código complejo**: El código debería refactorizarse
- **Variables booleanas en nombres de funciones**: `getUserActive()` vs
  `getActiveUsers()`
- **Números mágicos**: Usar constantes con nombres descriptivos

#### Reglas de Oro

1. **Regla del Boy Scout**: Deja el código mejor de como lo encontraste
2. **Principio YAGNI**: You Aren't Gonna Need It - No implementes funcionalidad
   que "podrías necesitar"
3. **Principio KISS**: Keep It Simple, Stupid - La solución más simple que
   funcione
4. **Regla de los dos minutos**: Si no entiendes qué hace una función en dos
   minutos, necesita refactoring
5. **Ley de Demeter**: Un objeto solo debe comunicarse con sus "amigos
   inmediatos"

### Control de Versiones Semántico (SemVer)

#### Formato de Versiones

```bash
# Estructura: MAJOR.MINOR.PATCH
v1.0.0  # Primera versión estable
v1.1.0  # Nueva funcionalidad compatible
v1.1.1  # Corrección de bugs
v2.0.0  # Cambios incompatibles (breaking changes)

# Pre-releases
v1.0.0-alpha.1    # Versión alpha
v1.0.0-beta.2     # Versión beta
v1.0.0-rc.1       # Release candidate
```

#### Criterios para Incrementar Versiones

- **MAJOR**: Cambios incompatibles en la API
  - Eliminación de endpoints
  - Cambios en estructura de respuesta
  - Modificaciones que requieren cambios en el código cliente

- **MINOR**: Nueva funcionalidad compatible hacia atrás
  - Nuevos endpoints
  - Nuevos campos opcionales
  - Funcionalidades que no afectan el código existente

- **PATCH**: Correcciones de bugs compatibles
  - Fixes de seguridad
  - Correcciones de comportamiento
  - Optimizaciones de performance

### Convención de Mensajes de Commit

#### Formato Estándar

```bash
<tipo>(<scope>): <descripción>

[cuerpo opcional]

[footer opcional]
```

#### Tipos de Commit

```bash
feat(auth): implementar login con JWT
fix(api): corregir validación de email en registro
docs(readme): actualizar instrucciones de instalación
style(frontend): aplicar formato con prettier
refactor(backend): reorganizar estructura de controladores
test(auth): agregar tests unitarios para login service
chore(deps): actualizar dependencias de seguridad
perf(db): optimizar consulta de usuarios activos
ci(github): configurar workflow de testing automático
```

#### Scopes Recomendados

- **frontend**: Cambios en React/TypeScript
- **backend**: Cambios en Laravel/PHP
- **api**: Cambios en endpoints o contratos de API
- **db**: Cambios en base de datos o migraciones
- **auth**: Cambios en autenticación/autorización
- **ui**: Cambios en componentes de interfaz
- **config**: Cambios en configuración
- **deps**: Cambios en dependencias

#### Ejemplos Completos

```bash
feat(auth): implementar autenticación con Google OAuth

Permite a los usuarios registrarse e iniciar sesión usando su cuenta de Google.
Incluye validación de tokens y creación automática de perfiles de usuario.

Closes #123
```

```bash
fix(api): corregir serialización de fechas en respuestas JSON

Las fechas se estaban devolviendo en formato incorrecto causando problemas
en el frontend. Ahora se utiliza formato ISO 8601 consistentemente.

Fixes #456
Breaking change: Las fechas ahora se devuelven en formato ISO 8601
```

## ¿Qué debo hacer?

### 1. Configurar herramientas de formateo

#### Instalar y configurar Prettier

```bash
# Frontend
npm install --save-dev prettier
echo '{"semi": true, "singleQuote": true, "printWidth": 80}' > .prettierrc
```

#### Configurar ESLint con TypeScript

```bash
npm install --save-dev @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

### 2. Establecer reglas de código

#### Crear .editorconfig

```ini
# .editorconfig
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.php]
indent_size = 4

[*.md]
trim_trailing_whitespace = false
```

### 3. Implementar pre-commit hooks

```bash
# Instalar husky para Git hooks
npm install --save-dev husky
npx husky add .husky/pre-commit "npm run lint && npm run test"
```

### 4. Configurar herramientas por proyecto

Ver documentación específica:

- [Configuración de herramientas de calidad](../step_03/configuracion-herramientas-calidad.md)
- [Setup de Laravel con Clean Architecture](../step_03/setup-laravel-clean-architecture.md)
- [Configuración React + TypeScript + Vite](../step_03/configuracion-react-typescript-vite.md)

## Tips

### Herramientas Recomendadas

#### Para TypeScript/React

- **ESLint**: Análisis estático de código
- **Prettier**: Formateo automático
- **TypeScript**: Tipado estático
- **Husky**: Git hooks
- **lint-staged**: Linting incremental

#### Para PHP/Laravel

- **PHP CS Fixer**: Formateo automático
- **PHPStan**: Análisis estático
- **Psalm**: Análisis de tipos
- **PHPMD**: Detector de code smells

#### Para Base de Datos

- **Adminer**: Administración ligera
- **MySQL Workbench**: Diseño y modelado
- **Laravel Telescope**: Debugging de queries

### Automatización

```json
// package.json scripts
{
   "scripts": {
      "lint": "eslint src/ --ext .ts,.tsx",
      "lint:fix": "eslint src/ --ext .ts,.tsx --fix",
      "format": "prettier --write 'src/**/*.{ts,tsx,json,css,md}'",
      "type-check": "tsc --noEmit",
      "validate": "npm run type-check && npm run lint && npm run test"
   }
}
```

### Integración con IDE

#### VS Code Settings

```json
// .vscode/settings.json
{
   "editor.formatOnSave": true,
   "editor.defaultFormatter": "esbenp.prettier-vscode",
   "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true
   },
   "typescript.preferences.importModuleSpecifier": "relative"
}
```

## Ejemplos

### Proyecto Frontend Completo

```
src/
├── components/
│   ├── common/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   └── index.ts
│   └── features/
│       └── auth/
│           ├── LoginForm/
│           └── index.ts
├── hooks/
│   ├── useAuth.ts
│   └── index.ts
├── services/
│   ├── api/
│   │   ├── authApi.ts
│   │   └── index.ts
│   └── index.ts
├── types/
│   ├── auth.ts
│   ├── api.ts
│   └── index.ts
└── utils/
    ├── dateHelpers.ts
    └── index.ts
```

### Estructura Backend Laravel

```
app/
├── Domain/
│   ├── User/
│   │   ├── Entities/
│   │   │   └── User.php
│   │   ├── ValueObjects/
│   │   │   └── Email.php
│   │   ├── Repositories/
│   │   │   └── UserRepositoryInterface.php
│   │   └── Services/
│   │       └── UserDomainService.php
├── Application/
│   ├── UseCases/
│   │   └── User/
│   │       └── CreateUserUseCase.php
│   └── DTOs/
│       └── CreateUserDTO.php
└── Infrastructure/
    ├── Http/
    │   └── Controllers/
    │       └── Api/
    │           └── V1/
    │               └── UserController.php
    └── Persistence/
        └── Eloquent/
            ├── UserModel.php
            └── UserRepository.php
```

## Navegación

[⬅️ Diagrama de Arquitectura General](./diagrama-arquitectura-general.md) |
[🏠 README Principal](../../README.md) |
[Estructura de Carpetas ➡️](./estructura-carpetas.md)
