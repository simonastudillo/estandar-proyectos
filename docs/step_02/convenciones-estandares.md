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

#### Convenciones UI Components - Shadcn/ui + Atomic Design

```typescript
// Fase 1: Shadcn/ui components
src/components/ui/
├── button.tsx          // Lowercase, siguiendo convención Shadcn
├── input.tsx           // Componentes copiables, mantener estructura original
├── card.tsx            // No modificar nombres internos de Shadcn
└── modal.tsx

// Fase 2: Atomic Design evolution
src/components/atoms/
├── Button/             // PascalCase para carpetas
│   ├── Button.tsx      // PascalCase para archivos principales
│   ├── Button.stories.tsx  // .stories.tsx para Storybook
│   ├── Button.test.tsx     // .test.tsx para unit tests
│   └── index.ts        // Barrel export
└── Input/

src/components/molecules/
├── SearchBox/          // PascalCase para combinaciones
│   ├── SearchBox.tsx
│   ├── SearchBox.stories.tsx
│   └── index.ts
└── FormField/

src/components/organisms/
├── DataTable/          // PascalCase para componentes complejos
└── Navigation/

// Import patterns
import { Button } from "@/components/ui/button";        // Fase 1
import { Button } from "@/components/atoms";             // Fase 2 - desde barrel
import { DataTable } from "@/components/organisms";     // Organisms específicos
```

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

#### TypeScript/JavaScript + React

```typescript
// 1. Tipado estricto obligatorio
interface User {
   id: string;
   name: string;
   email: string;
   createdAt: Date;
}

// 2. No usar 'any', preferir tipos específicos
const users: User[] = []; // ✅ Correcto
const data: any = []; // ❌ Evitar

// 3. Componentes React - Convenciones específicas
// Fase 1: Uso de Shadcn/ui components
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { cn } from "@/lib/utils"; // Utility para class merging

export function LoginForm() {
  return (
    <form className="space-y-4">
      <Input 
        type="email" 
        placeholder="Email" 
        className={cn("w-full", someCondition && "border-red-500")}
      />
      <Button type="submit" variant="default" size="lg">
        Login
      </Button>
    </form>
  );
}

// Fase 2: Atomic Design patterns
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  loading?: boolean;
}

export const Button: React.FC<ButtonProps> = ({ 
  variant = 'primary', 
  size = 'md',
  loading = false,
  children,
  className,
  ...props 
}) => {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      disabled={loading || props.disabled}
      {...props}
    >
      {loading ? <Spinner /> : children}
    </button>
  );
};

// 4. Estado Global - Redux Toolkit (estándar único)
// Configuración de store y slices
interface AuthState {
  user: User | null;
  loading: boolean;
  error: string | null;
}

const authSlice = createSlice({
  name: 'auth',
  initialState: { user: null, loading: false, error: null } as AuthState,
  reducers: {
    loginStart: (state) => {
      state.loading = true;
      state.error = null;
    },
    loginSuccess: (state, action) => {
      state.loading = false;
      state.user = action.payload;
    },
    loginFailure: (state, action) => {
      state.loading = false;
      state.error = action.payload;
    },
    logout: (state) => {
      state.user = null;
      state.error = null;
    },
  },
});

export const { loginStart, loginSuccess, loginFailure, logout } = authSlice.actions;
      state.loading = true;
      state.error = null;
    },
    loginSuccess: (state, action: PayloadAction<User>) => {
      state.loading = false;
      state.user = action.payload;
    },
    loginFailure: (state, action: PayloadAction<string>) => {
      state.loading = false;
      state.error = action.payload;
    },
  },
});

// 5. Custom Hooks - Convenciones
function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(`Error reading localStorage key "${key}":`, error);
      return initialValue;
    }
  });

  const setValue = useCallback((value: T | ((val: T) => T)) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(`Error setting localStorage key "${key}":`, error);
    }
  }, [key, storedValue]);

  return [storedValue, setValue] as const;
}

// 6. Tailwind CSS - Convenciones
// ✅ Usar cn() utility para conditional classes
<div className={cn(
  "base-classes",
  variant === 'primary' && "bg-blue-500",
  size === 'lg' && "px-6 py-3"
)} />

// ❌ Evitar concatenación manual
<div className={`base-classes ${variant === 'primary' ? 'bg-blue-500' : ''}`} />

// 7. Usar const assertions cuando sea apropiado
const themes = ["light", "dark"] as const;
type Theme = typeof themes[number]; // 'light' | 'dark'

// 8. Preferir arrow functions para callbacks
const userNames = users.map((user) => user.name);

// 9. Destructuring cuando mejore la legibilidad
const { name, email } = user;

// 10. Optional chaining y nullish coalescing
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
// .prettierrc - Configuración para React + TypeScript + Tailwind
{
   "semi": true,
   "trailingComma": "es5",
   "singleQuote": true,
   "printWidth": 80,
   "tabWidth": 2,
   "useTabs": false,
   "plugins": ["prettier-plugin-tailwindcss"],
   "tailwindConfig": "./tailwind.config.js",
   "tailwindFunctions": ["cn", "clsx"]
}
```

#### ESLint Rules - Específicas para el Stack

```javascript
// .eslintrc.js - Configuración para React + TypeScript + Shadcn/ui
module.exports = {
   extends: [
      "eslint:recommended",
      "@typescript-eslint/recommended",
      "plugin:react/recommended",
      "plugin:react-hooks/recommended",
      "plugin:jsx-a11y/recommended",
   ],
   plugins: ["@typescript-eslint", "react", "react-hooks", "jsx-a11y"],
   rules: {
      // TypeScript específicas
      "prefer-const": "error",
      "no-var": "error",
      "no-unused-vars": "off", // Usar la versión de TypeScript
      "@typescript-eslint/no-unused-vars": "error",
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/explicit-function-return-type": "warn",

      // React específicas
      "react/react-in-jsx-scope": "off", // No necesario en React 17+
      "react/prop-types": "off", // Usamos TypeScript
      "react-hooks/rules-of-hooks": "error",
      "react-hooks/exhaustive-deps": "warn",

      // Shadcn/ui y Tailwind específicas
      "jsx-a11y/click-events-have-key-events": "error",
      "jsx-a11y/no-static-element-interactions": "error",

      // Import/Export
      "import/order": ["error", {
         "groups": [
            "builtin",
            "external",
            "internal",
            "parent",
            "sibling",
            "index",
         ],
         "pathGroups": [
            {
               "pattern": "@/**",
               "group": "internal",
               "position": "after",
            },
         ],
         "pathGroupsExcludedImportTypes": ["builtin"],
      }],
   },
   settings: {
      react: {
         version: "detect",
      },
   },
};
```

#### Tailwind CSS Configuration

```javascript
// tailwind.config.js - Configuración específica del design system
/** @type {import('tailwindcss').Config} */
module.exports = {
   darkMode: ["class"],
   content: [
      "./pages/**/*.{ts,tsx}",
      "./components/**/*.{ts,tsx}",
      "./app/**/*.{ts,tsx}",
      "./src/**/*.{ts,tsx}",
   ],
   theme: {
      container: {
         center: true,
         padding: "2rem",
         screens: {
            "2xl": "1400px",
         },
      },
      extend: {
         colors: {
            border: "hsl(var(--border))",
            input: "hsl(var(--input))",
            ring: "hsl(var(--ring))",
            background: "hsl(var(--background))",
            foreground: "hsl(var(--foreground))",
            primary: {
               DEFAULT: "hsl(var(--primary))",
               foreground: "hsl(var(--primary-foreground))",
            },
            secondary: {
               DEFAULT: "hsl(var(--secondary))",
               foreground: "hsl(var(--secondary-foreground))",
            },
            // ... Design tokens específicos del proyecto
         },
         borderRadius: {
            lg: "var(--radius)",
            md: "calc(var(--radius) - 2px)",
            sm: "calc(var(--radius) - 4px)",
         },
         keyframes: {
            "accordion-down": {
               from: { height: 0 },
               to: { height: "var(--radix-accordion-content-height)" },
            },
            "accordion-up": {
               from: { height: "var(--radix-accordion-content-height)" },
               to: { height: 0 },
            },
         },
         animation: {
            "accordion-down": "accordion-down 0.2s ease-out",
            "accordion-up": "accordion-up 0.2s ease-out",
         },
      },
   },
   plugins: [
      require("tailwindcss-animate"),
      require("@tailwindcss/typography"),
      require("@tailwindcss/forms"),
   ],
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

#### Para TypeScript/React + Shadcn/ui

- **ESLint**: Análisis estático de código + reglas React/TypeScript
- **Prettier**: Formateo automático + prettier-plugin-tailwindcss
- **TypeScript**: Tipado estático strict mode
- **Husky**: Git hooks para calidad de código
- **lint-staged**: Linting incremental en archivos modificados
- **Tailwind CSS IntelliSense**: Autocomplete para clases
- **Shadcn CLI**: `npx shadcn-ui@latest add [component]` para agregar components
- **Storybook**: Documentación visual de componentes (Fase 2)

#### Para PHP/Laravel + Clean Architecture

- **PHP CS Fixer**: Formateo automático siguiendo PSR-12
- **PHPStan**: Análisis estático con reglas strict
- **Larastan**: PHPStan específico para Laravel
- **PHPMD**: Detector de code smells
- **PHP Intelephense**: IntelliSense para PHP en VS Code

#### Para Base de Datos

- **Adminer**: Administración ligera
- **MySQL Workbench**: Diseño y modelado
- **Laravel Telescope**: Debugging de queries
- **Laravel Debugbar**: Performance profiling en desarrollo

#### Herramientas de Estado Global

**Redux Toolkit (estándar único):**

- **Redux DevTools**: Debugging avanzado del estado
- **RTK Query**: Data fetching y caching automático
- **Redux Persist**: Persistencia de estado en localStorage/AsyncStorage
- **Reselect**: Selectores memoizados para optimización

### Automatización

```json
// package.json scripts - Específicos para el stack
{
   "scripts": {
      // Linting y formato
      "lint": "eslint src/ --ext .ts,.tsx",
      "lint:fix": "eslint src/ --ext .ts,.tsx --fix",
      "format": "prettier --write 'src/**/*.{ts,tsx,json,css,md}'",
      "format:check": "prettier --check 'src/**/*.{ts,tsx,json,css,md}'",

      // TypeScript
      "type-check": "tsc --noEmit",
      "type-check:watch": "tsc --noEmit --watch",

      // Testing
      "test": "jest",
      "test:watch": "jest --watch",
      "test:coverage": "jest --coverage",
      "test:ci": "jest --ci --coverage --watchAll=false",

      // Shadcn/ui
      "ui:add": "npx shadcn-ui@latest add",
      "ui:list": "npx shadcn-ui@latest list",

      // Storybook (Fase 2)
      "storybook": "start-storybook -p 6006",
      "build-storybook": "build-storybook",
      "chromatic": "npx chromatic --project-token=<project-token>",

      // Build y dev
      "dev": "vite",
      "build": "tsc && vite build",
      "preview": "vite preview",

      // Validación completa
      "validate": "npm run type-check && npm run lint && npm run format:check && npm run test:ci",
      "prepare": "husky install"
   }
}
```

```bash
# .husky/pre-commit - Git hooks específicos
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Ejecutar lint-staged para archivos modificados
npx lint-staged

# Verificar tipos TypeScript
npm run type-check

# .husky/pre-push - Validación antes de push
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Ejecutar tests completos
npm run test:ci

# Verificar build
npm run build
```

```json
// lint-staged.config.js - Configuración para archivos específicos
module.exports = {
  // TypeScript/JavaScript files
  '*.{ts,tsx,js,jsx}': [
    'eslint --fix',
    'prettier --write',
    'jest --bail --findRelatedTests --passWithNoTests',
  ],
  
  // CSS/SCSS files
  '*.{css,scss}': [
    'prettier --write',
  ],
  
  // JSON/Markdown files
  '*.{json,md}': [
    'prettier --write',
  ],
  
  // Package.json
  'package.json': [
    'sort-package-json',
  ],
};
```

### Integración con IDE

#### VS Code Settings

```json
// .vscode/settings.json - Configuración específica para el stack
{
   // Formateo automático
   "editor.formatOnSave": true,
   "editor.defaultFormatter": "esbenp.prettier-vscode",
   "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true,
      "source.organizeImports": true
   },

   // TypeScript
   "typescript.preferences.importModuleSpecifier": "relative",
   "typescript.suggest.autoImports": true,
   "typescript.updateImportsOnFileMove.enabled": "always",

   // Tailwind CSS
   "tailwindCSS.includeLanguages": {
      "typescript": "typescript",
      "typescriptreact": "typescriptreact"
   },
   "tailwindCSS.experimental.classRegex": [
      ["cn\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"],
      ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"]
   ],

   // File associations
   "files.associations": {
      "*.css": "tailwindcss"
   },

   // Auto-imports
   "typescript.suggest.includeCompletionsForModuleExports": true,
   "typescript.preferences.includePackageJsonAutoImports": "auto",

   // Git
   "git.enableSmartCommit": true,
   "git.confirmSync": false,

   // Emmet
   "emmet.includeLanguages": {
      "typescript": "typescriptreact",
      "typescriptreact": "typescriptreact"
   }
}
```

```json
// .vscode/extensions.json - Extensiones recomendadas
{
   "recommendations": [
      // React + TypeScript
      "bradlc.vscode-tailwindcss",
      "esbenp.prettier-vscode",
      "dbaeumer.vscode-eslint",
      "ms-vscode.vscode-typescript-next",

      // React específicas
      "burkeholland.simple-react-snippets",
      "dsznajder.es7-react-js-snippets",

      // Git y colaboración
      "eamodio.gitlens",
      "github.vscode-pull-request-github",

      // PHP/Laravel
      "bmewburn.vscode-intelephense-client",
      "onecentlin.laravel-blade",
      "ryannaddy.laravel-artisan",

      // Utilidades
      "christian-kohler.path-intellisense",
      "formulahendry.auto-rename-tag",
      "bradgashler.htmltagwrap"
   ]
}
```

## Ejemplos

### Proyecto Frontend Completo - Shadcn/ui + Atomic Design

```
src/
├── components/
│   ├── ui/                 # Fase 1: Shadcn components
│   │   ├── button.tsx      # Lowercase siguiendo convención Shadcn
│   │   ├── input.tsx       
│   │   ├── card.tsx        
│   │   └── index.ts        # Barrel export
│   │
│   ├── atoms/              # Fase 2: Atomic Design  
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.stories.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   ├── Input/
│   │   └── index.ts        # Barrel export para todos los atoms
│   │
│   ├── molecules/          # Combinaciones de atoms
│   │   ├── SearchBox/
│   │   │   ├── SearchBox.tsx
│   │   │   ├── SearchBox.stories.tsx
│   │   │   └── index.ts
│   │   ├── FormField/
│   │   └── index.ts
│   │
│   ├── organisms/          # Componentes complejos
│   │   ├── DataTable/
│   │   ├── Navigation/
│   │   └── index.ts
│   │
│   └── templates/          # Page layouts
│       ├── PageLayout/
│       ├── DashboardLayout/
│       └── index.ts
│
├── lib/                    # Utilidades específicas
│   ├── utils.ts           # cn(), clsx helpers
│   ├── validations.ts     # Zod schemas
│   ├── api.ts             # Axios configuration
│   └── constants.ts       # App constants
│
├── hooks/                  # Custom hooks
│   ├── useAuth.ts
│   ├── useLocalStorage.ts
│   └── index.ts
│
├── services/               # API services
│   ├── api/
│   │   ├── authApi.ts
│   │   ├── userApi.ts
│   │   └── index.ts
│   └── index.ts
│
├── store/                  # Estado global (Redux Toolkit)
│   ├── slices/            # Redux slices
│   │   ├── authSlice.ts
│   │   ├── userSlice.ts
│   │   └── uiSlice.ts
│   ├── middleware/        # Middleware personalizado
│   │   └── authMiddleware.ts
│   ├── hooks.ts          # Hooks tipados
│   └── index.ts          # Configuración del store
│
├── types/                  # TypeScript definitions
│   ├── auth.ts
│   ├── api.ts
│   ├── components.ts
│   └── index.ts
│
├── pages/                  # Páginas principales
│   ├── LoginPage/
│   ├── DashboardPage/
│   └── index.ts
│
├── stories/                # Storybook stories (Fase 2)
│   ├── atoms/
│   ├── molecules/
│   └── organisms/
│
└── utils/                  # Utilidades generales
    ├── dateHelpers.ts
    ├── formatters.ts
    └── index.ts

# Archivos de configuración
├── .eslintrc.js           # ESLint + React + TypeScript + Tailwind
├── .prettierrc            # Prettier + Tailwind plugin
├── tailwind.config.js     # Tailwind + design tokens
├── components.json        # Shadcn/ui configuration
├── vite.config.ts         # Vite + path aliases
├── tsconfig.json          # TypeScript strict config
└── .storybook/            # Storybook config (Fase 2)
    ├── main.ts
    └── preview.ts
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
