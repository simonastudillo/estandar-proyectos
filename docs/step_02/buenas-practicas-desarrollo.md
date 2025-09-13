# Buenas Prácticas de Desarrollo

## ¿Qué es?

Las buenas prácticas de desarrollo son un conjunto de principios, metodologías y
técnicas probadas que garantizan la creación de código limpio, mantenible,
escalable y seguro. Este documento consolida todas las prácticas esenciales que
deben seguirse en cada proyecto, desde la escritura de código hasta el uso
responsable de herramientas y dependencias externas.

En el contexto de Clean Architecture + DDD, estas prácticas aseguran que el
código no solo funcione correctamente, sino que también sea sostenible a largo
plazo, fácil de entender por nuevos desarrolladores y robusto ante cambios
futuros.

## ¿Por qué es importante?

- **Calidad del código**: Garantiza código limpio, legible y mantenible
- **Productividad del equipo**: Acelera el desarrollo al tener estándares claros
- **Reducción de bugs**: Previene errores comunes mediante prácticas probadas
- **Facilita mantenimiento**: Código bien estructurado es más fácil de modificar
- **Onboarding rápido**: Nuevos desarrolladores se integran más fácilmente
- **Seguridad**: Reduce vulnerabilidades mediante prácticas seguras
- **Sostenibilidad**: Evita deuda técnica y facilita la evolución del proyecto
- **Profesionalismo**: Demuestra madurez técnica del equipo

## ¿Qué debe incluir?

### 1. Principios SOLID

#### Single Responsibility Principle (SRP)

Cada clase o función debe tener una sola razón para cambiar.

```typescript
// ❌ Viola SRP - hace demasiadas cosas
class UserManager {
   validateUser(data: any): boolean {/* validación */}
   saveUser(user: User): void {/* persistencia */}
   sendWelcomeEmail(user: User): void {/* notificación */}
   generateReport(users: User[]): string {/* reportes */}
}

// ✅ Sigue SRP - responsabilidades separadas
class UserValidator {
   validate(data: CreateUserRequest): ValidationResult {
      // Solo se encarga de validar
   }
}

class UserRepository {
   save(user: User): Promise<User> {
      // Solo se encarga de persistencia
   }
}

class EmailService {
   sendWelcomeEmail(user: User): Promise<void> {
      // Solo se encarga de notificaciones
   }
}
```

#### Open/Closed Principle (OCP)

Abierto para extensión, cerrado para modificación.

```typescript
// ✅ Extensible sin modificar código existente
interface PaymentProcessor {
   process(amount: number): Promise<PaymentResult>;
}

class CreditCardProcessor implements PaymentProcessor {
   process(amount: number): Promise<PaymentResult> {
      // Implementación específica para tarjeta de crédito
   }
}

class PayPalProcessor implements PaymentProcessor {
   process(amount: number): Promise<PaymentResult> {
      // Implementación específica para PayPal
   }
}

class PaymentService {
   constructor(private processor: PaymentProcessor) {}

   processPayment(amount: number): Promise<PaymentResult> {
      return this.processor.process(amount);
   }
}
```

#### Otros Principios SOLID

- **Liskov Substitution**: Las subclases deben poder reemplazar a sus clases
  base
- **Interface Segregation**: Interfaces específicas mejor que una interfaz
  general
- **Dependency Inversion**: Depender de abstracciones, no de concreciones

### 2. Clean Code (Código Limpio)

#### Nombrado Descriptivo

```typescript
// ❌ Nombres poco descriptivos
const d = new Date();
const u = users.filter((x) => x.a);
function calc(a: number, b: number): number {
   return a * b * 0.1;
}

// ✅ Nombres descriptivos
const currentDate = new Date();
const activeUsers = users.filter((user) => user.isActive);
function calculateDiscount(price: number, quantity: number): number {
   return price * quantity * DISCOUNT_RATE;
}
```

#### Funciones Pequeñas y Enfocadas

```typescript
// ❌ Función que hace demasiadas cosas
function processOrder(orderData: any): any {
   // Validar datos (20 líneas)
   // Calcular totales (15 líneas)
   // Aplicar descuentos (25 líneas)
   // Guardar en base de datos (10 líneas)
   // Enviar email (15 líneas)
   // Actualizar inventario (20 líneas)
}

// ✅ Funciones pequeñas y enfocadas
function validateOrderData(data: OrderData): ValidationResult {
   // Solo validación
}

function calculateOrderTotal(items: OrderItem[]): number {
   // Solo cálculo de totales
}

function processOrder(orderData: OrderData): Promise<Order> {
   const validation = validateOrderData(orderData);
   if (!validation.isValid) throw new ValidationError(validation.errors);

   const total = calculateOrderTotal(orderData.items);
   const order = await orderRepository.create({ ...orderData, total });

   await Promise.all([
      emailService.sendOrderConfirmation(order),
      inventoryService.updateStock(order.items),
   ]);

   return order;
}
```

### 3. DRY (Don't Repeat Yourself)

```typescript
// ❌ Código duplicado
function validateEmail(email: string): boolean {
   const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
   return emailRegex.test(email);
}

function validateUserEmail(userEmail: string): boolean {
   const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
   return emailRegex.test(userEmail);
}

// ✅ Centralizado y reutilizable
class ValidationUtils {
   private static readonly EMAIL_REGEX = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

   static isValidEmail(email: string): boolean {
      return this.EMAIL_REGEX.test(email);
   }
}

// Uso en diferentes contextos
const isUserEmailValid = ValidationUtils.isValidEmail(user.email);
const isContactEmailValid = ValidationUtils.isValidEmail(contact.email);
```

### 4. KISS (Keep It Simple, Stupid)

```typescript
// ❌ Complejidad innecesaria
function getUserStatus(user: User): string {
   return user.isActive
      ? (user.isPremium
         ? (user.hasValidSubscription ? "premium-active" : "premium-expired")
         : "basic-active")
      : "inactive";
}

// ✅ Simple y claro
function getUserStatus(user: User): UserStatus {
   if (!user.isActive) return "inactive";

   if (user.isPremium) {
      return user.hasValidSubscription ? "premium-active" : "premium-expired";
   }

   return "basic-active";
}
```

### 5. YAGNI (You Aren't Gonna Need It)

```typescript
// ❌ Sobre-ingeniería prematura
interface UserServiceInterface {
   createUser(data: CreateUserRequest): Promise<User>;
   updateUser(id: string, data: UpdateUserRequest): Promise<User>;
   deleteUser(id: string): Promise<void>;
   getUserById(id: string): Promise<User>;
   getUsersByRole(role: string): Promise<User[]>;
   getUsersByCompany(companyId: string): Promise<User[]>;
   getUsersByDateRange(start: Date, end: Date): Promise<User[]>;
   // ... 20 métodos más que "podrías necesitar"
}

// ✅ Solo lo que necesitas ahora
interface UserServiceInterface {
   createUser(data: CreateUserRequest): Promise<User>;
   getUserById(id: string): Promise<User>;
   updateUser(id: string, data: UpdateUserRequest): Promise<User>;
}

// Agregar métodos cuando realmente los necesites
```

### 6. Organización del Código

#### Estructura de Archivos

```
src/
├── components/          # Componentes reutilizables
│   ├── common/         # Componentes básicos (Button, Input)
│   ├── forms/          # Componentes de formularios
│   └── layout/         # Componentes de layout
├── hooks/              # Custom hooks
├── pages/              # Páginas principales
├── services/           # API calls y servicios
├── store/              # Redux store y slices
├── types/              # Definiciones TypeScript
├── utils/              # Utilidades y helpers
└── constants/          # Constantes de la aplicación
```

#### Agrupación Lógica

```typescript
// ✅ Agrupación por funcionalidad
src/
├── features/
│   ├── authentication/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types/
│   ├── orders/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── services/
│   └── users/
└── shared/
    ├── components/
    ├── utils/
    └── types/
```

### 7. Uso Responsable de Librerías Externas

#### Criterios de Selección

- **Mantenimiento activo**: Última actualización reciente, issues respondidos
- **Popularidad y madurez**: GitHub stars, downloads npm, tiempo en el mercado
- **Tamaño y dependencias**: Evaluar bundle size y dependencias transitivas
- **Licencia compatible**: MIT, Apache 2.0, etc.
- **Documentación**: Documentación clara y ejemplos

```bash
# Evaluar una dependencia antes de instalar
npm info lodash
npm audit
npx bundlephobia lodash
```

#### Principio de Mínimas Dependencias

```typescript
// ❌ Agregar librería completa para una función simple
import _ from "lodash";
const uniqueItems = _.uniq(items);

// ✅ Implementación interna simple
function getUniqueItems<T>(items: T[]): T[] {
   return Array.from(new Set(items));
}
const uniqueItems = getUniqueItems(items);

// ❌ Librería pesada para formato de fecha
import moment from "moment"; // 67KB
const formatted = moment().format("YYYY-MM-DD");

// ✅ API nativa o librería ligera
const formatted = new Date().toISOString().split("T")[0];
// O usar date-fns (solo funciones necesarias)
import { format } from "date-fns";
const formatted = format(new Date(), "yyyy-MM-dd");
```

#### Gestión de Dependencias

```json
// package.json - Versiones fijas para estabilidad
{
   "dependencies": {
      "react": "18.2.0", // Versión exacta
      "axios": "^1.5.0", // Solo actualizaciones de patch
      "lodash": "~4.17.21" // Solo actualizaciones de bug fixes
   }
}
```

#### Evaluación Continua

```typescript
// Documentar razón de cada dependencia
/*
DEPENDENCIAS Y JUSTIFICACIÓN:
- axios: Cliente HTTP con interceptors y mejor manejo de errores que fetch
- react-router-dom: Estándar de facto para routing en React
- @reduxjs/toolkit: Simplifica Redux, recomendado oficialmente
- zod: Validación con inferencia de tipos TypeScript

CANDIDATOS A REEMPLAZAR:
- moment (67KB) → date-fns (solo funciones necesarias)
- lodash (24KB) → implementaciones nativas cuando sea posible
*/
```

### 8. Uso Correcto de la IA (Copilot, ChatGPT, etc.)

#### ✅ Usos Recomendados

**Generación de Boilerplate**

```typescript
// Prompt: "Crear interface TypeScript para usuario con validación Zod"
interface User {
   id: string;
   email: string;
   name: string;
   createdAt: Date;
}

const UserSchema = z.object({
   id: z.string().uuid(),
   email: z.string().email(),
   name: z.string().min(2).max(50),
   createdAt: z.date(),
});
```

**Alternativas de Implementación**

```typescript
// Prompt: "Mostrar 3 formas diferentes de manejar async state en React"
// 1. useState + useEffect
// 2. Custom hook con reducer
// 3. React Query/SWR
```

**Explicación de Código Complejo**

```typescript
// Prompt: "Explicar qué hace esta función de reduce"
const groupBy = <T, K extends keyof T>(array: T[], key: K) =>
   array.reduce((groups, item) => {
      const group = item[key];
      groups[group] = groups[group] || [];
      groups[group].push(item);
      return groups;
   }, {} as Record<string, T[]>);
```

**Sugerencias de Nombres**

```typescript
// Prompt: "Nombres mejores para esta función que valida y procesa datos de usuario"
// validateAndProcessUserData → processUserRegistration
```

#### ❌ Usos NO Recomendados

**Copiar y Pegar sin Entender**

```typescript
// ❌ NO hacer esto
// Copié esto de ChatGPT pero no sé cómo funciona
const mysteriousFunction = (data: any) => {
   return data.reduce((acc: any, curr: any) => /* código complejo sin entender */, {});
};

// ✅ Entender antes de usar
const groupUsersByRole = (users: User[]): Record<string, User[]> => {
   return users.reduce((groups, user) => {
      const role = user.role;
      if (!groups[role]) groups[role] = [];
      groups[role].push(user);
      return groups;
   }, {} as Record<string, User[]>);
};
```

**Dependencias Innecesarias**

```typescript
// ❌ La IA sugiere usar una librería pesada
// Prompt: "Función para validar email"
// Respuesta IA: "Instala validator.js"
import validator from "validator"; // 24KB para validar un email

// ✅ Implementación simple y controlada
const isValidEmail = (email: string): boolean => {
   return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};
```

**IA como Excusa para No Aprender**

```typescript
// ❌ Actitud incorrecta
// "No necesito entender promesas, la IA me escribe el async/await"

// ✅ Actitud correcta
// "Uso la IA para generar ejemplos, pero entiendo cómo funcionan las promesas"
async function fetchUserData(id: string): Promise<User> {
   // Entiendo que esto es asíncrono y puede fallar
   try {
      const response = await api.get(`/users/${id}`);
      return response.data;
   } catch (error) {
      // Entiendo el manejo de errores
      throw new UserNotFoundError(`User ${id} not found`);
   }
}
```

#### Proceso de Validación IA

```typescript
// CHECKLIST antes de aceptar sugerencia de IA:
// □ ¿Entiendo qué hace este código?
// □ ¿Está alineado con nuestras convenciones?
// □ ¿Introduce dependencias innecesarias?
// □ ¿Es la solución más simple que funciona?
// □ ¿Tiene manejo de errores apropiado?
// □ ¿Está tipado correctamente?
// □ ¿Puedo explicárselo a otro desarrollador?
```

### 9. Comentarios y Documentación

#### Cuándo Comentar

```typescript
// ✅ Comentarios útiles
/**
 * Calcula el precio final aplicando descuentos por volumen
 * @param basePrice - Precio base del producto
 * @param quantity - Cantidad de productos
 * @returns Precio final con descuentos aplicados
 */
function calculateFinalPrice(basePrice: number, quantity: number): number {
   // Descuento por volumen: 5% si cantidad > 10, 10% si cantidad > 50
   const volumeDiscount = quantity > 50 ? 0.1 : quantity > 10 ? 0.05 : 0;

   // Algoritmo de descuento específico del negocio
   // TODO: Mover a configuración cuando tengamos múltiples tipos de descuento
   const discountedPrice = basePrice * (1 - volumeDiscount);

   return discountedPrice * quantity;
}

// ❌ Comentarios inútiles
// Incrementa el contador en 1
counter++;

// Obtiene el usuario
const user = getUser();
```

#### Documentación de APIs

````typescript
/**
 * Servicio para gestión de usuarios
 *
 * @example
 * ```typescript
 * const userService = new UserService();
 * const user = await userService.createUser({
 *   email: 'user@example.com',
 *   name: 'John Doe'
 * });
 * ```
 */
class UserService {
   /**
    * Crea un nuevo usuario en el sistema
    *
    * @param userData - Datos del usuario a crear
    * @returns Promise que resuelve al usuario creado
    * @throws {ValidationError} Cuando los datos son inválidos
    * @throws {DuplicateEmailError} Cuando el email ya existe
    */
   async createUser(userData: CreateUserRequest): Promise<User> {
      // Implementación
   }
}
````

### 10. Testing en el Desarrollo

#### Pirámide de Testing

```typescript
// 70% - Unit Tests (rápidos, muchos)
describe("UserValidator", () => {
   test("should validate correct email format", () => {
      const result = UserValidator.validateEmail("user@example.com");
      expect(result.isValid).toBe(true);
   });
});

// 20% - Integration Tests (medios, algunos)
describe("UserService Integration", () => {
   test("should create user and save to database", async () => {
      const userData = { email: "test@example.com", name: "Test User" };
      const user = await userService.createUser(userData);

      const savedUser = await userRepository.findById(user.id);
      expect(savedUser).toBeDefined();
   });
});

// 10% - E2E Tests (lentos, pocos)
describe("User Registration Flow", () => {
   test("user can register and login", async () => {
      await page.goto("/register");
      await page.fill('[data-testid="email"]', "user@example.com");
      await page.click('[data-testid="submit"]');
      // ...
   });
});
```

#### TDD (Test-Driven Development)

```typescript
// 1. Red: Escribir test que falle
test("should calculate discount correctly", () => {
   const result = calculateDiscount(100, 2);
   expect(result).toBe(10); // 10% de descuento
});

// 2. Green: Implementar mínimo para que pase
function calculateDiscount(price: number, quantity: number): number {
   if (quantity >= 2) return price * 0.1;
   return 0;
}

// 3. Refactor: Mejorar sin romper tests
function calculateDiscount(price: number, quantity: number): number {
   const BULK_DISCOUNT_THRESHOLD = 2;
   const BULK_DISCOUNT_RATE = 0.1;

   if (quantity >= BULK_DISCOUNT_THRESHOLD) {
      return price * BULK_DISCOUNT_RATE;
   }

   return 0;
}
```

### 11. Refactor Continuo

#### Señales para Refactoring

- Funciones con más de 20-30 líneas
- Clases con más de 10 métodos públicos
- Código duplicado en 3 o más lugares
- Complejidad ciclomática alta
- Comentarios que explican código complejo
- Tests difíciles de escribir

#### Proceso de Refactoring

```typescript
// Antes: Función compleja que hace demasiadas cosas
function processPayment(paymentData: any): any {
   // Validación (15 líneas)
   // Cálculo de fees (20 líneas)
   // Procesamiento (25 líneas)
   // Logging (10 líneas)
   // Notificaciones (15 líneas)
}

// Después: Refactorizado en funciones pequeñas
function processPayment(paymentData: PaymentData): Promise<PaymentResult> {
   const validation = validatePaymentData(paymentData);
   if (!validation.isValid) throw new ValidationError(validation.errors);

   const fees = calculateProcessingFees(paymentData.amount);
   const result = await executePayment({ ...paymentData, fees });

   await Promise.all([
      logPaymentAttempt(result),
      sendPaymentNotification(result),
   ]);

   return result;
}
```

### 12. Checklist de Buenas Prácticas

#### Pre-commit Checklist

```typescript
/*
□ CÓDIGO
  □ Nombres descriptivos y consistentes
  □ Funciones pequeñas (< 30 líneas)
  □ Sin código duplicado
  □ Manejo apropiado de errores
  □ Sin números mágicos (usar constantes)

□ TIPOS Y VALIDACIÓN
  □ Tipado fuerte en TypeScript
  □ Interfaces definidas para contratos
  □ Validación de entrada en boundaries
  □ No usar 'any' type

□ TESTING
  □ Tests unitarios para lógica nueva
  □ Tests de integración para flujos
  □ Coverage > 80% en código crítico
  □ Tests pasan en CI/CD

□ DEPENDENCIAS
  □ No agregar dependencias innecesarias
  □ Evaluar alternativas más ligeras
  □ Documentar razón de nueva dependencia
  □ Verificar vulnerabilidades (npm audit)

□ DOCUMENTACIÓN
  □ README actualizado si aplica
  □ Comentarios en código complejo
  □ Documentación de APIs públicas
  □ Changelog actualizado

□ ARQUITECTURA
  □ Respeta Clean Architecture
  □ Separación de responsabilidades
  □ Inversión de dependencias
  □ Principios SOLID aplicados
*/
```

#### Code Review Checklist

```typescript
/*
□ FUNCIONALIDAD
  □ El código hace lo que dice que hace
  □ Maneja casos edge correctamente
  □ Performance aceptable
  □ Sin efectos secundarios inesperados

□ CALIDAD
  □ Legible y mantenible
  □ Sigue convenciones del proyecto
  □ Sin code smells evidentes
  □ Abstracciones apropiadas

□ SEGURIDAD
  □ No expone información sensible
  □ Validación de entrada
  □ Sanitización de datos
  □ Autenticación/autorización apropiada

□ TESTING
  □ Cobertura apropiada
  □ Tests significativos
  □ Mockea dependencias externas
  □ Tests son mantenibles
*/
```

## ¿Qué debo hacer?

### 1. Implementar en el Proyecto

#### Setup Inicial

```bash
# Configurar herramientas de calidad
npm install --save-dev eslint prettier husky lint-staged
npx husky install
npx husky add .husky/pre-commit "npx lint-staged"

# Configurar coverage threshold
# En package.json o jest.config.js
{
  "jest": {
    "coverageThreshold": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
```

#### Configurar Linting

```javascript
// .eslintrc.js
module.exports = {
   extends: [
      "@typescript-eslint/recommended",
      "plugin:react/recommended",
      "plugin:react-hooks/recommended",
   ],
   rules: {
      "max-lines-per-function": ["error", 30],
      "complexity": ["error", 10],
      "no-magic-numbers": ["error", { ignore: [0, 1, -1] }],
      "@typescript-eslint/no-explicit-any": "error",
   },
};
```

### 2. Crear Cultura de Calidad

#### Daily Practices

- **Morning**: Revisar coverage de tests del día anterior
- **Durante desarrollo**: TDD cuando sea apropiado
- **Pre-commit**: Ejecutar checklist de buenas prácticas
- **Code review**: Aplicar checklist de revisión
- **Retrospectiva**: Identificar puntos de mejora en prácticas

#### Team Agreements

```markdown
# Acuerdos del Equipo - Buenas Prácticas

## Definición de Done

- [ ] Funcionalidad implementada y tested
- [ ] Code review aprobado
- [ ] Tests pasan en CI/CD
- [ ] Coverage > 80% en funcionalidad nueva
- [ ] No vulnerabilidades críticas
- [ ] Documentación actualizada

## Criterios de Calidad

- Máximo 30 líneas por función
- Máximo 10 métodos públicos por clase
- Complejidad ciclomática < 10
- Zero warnings en linting
- TypeScript strict mode habilitado
```

### 3. Monitoreo Continuo

#### Métricas de Calidad

```typescript
// Scripts para métricas automatizadas
{
  "scripts": {
    "quality:coverage": "jest --coverage",
    "quality:complexity": "npx complexity-report",
    "quality:deps": "npm audit && npx depcheck",
    "quality:bundle": "npx bundlephobia",
    "quality:full": "npm run quality:coverage && npm run quality:deps"
  }
}
```

#### Dashboard de Calidad

```markdown
# Quality Dashboard (actualizar semanalmente)

## Métricas Técnicas

- **Test Coverage**: 85% (Target: >80%)
- **Vulnerabilidades**: 0 críticas, 2 moderadas
- **Bundle Size**: 1.2MB (Target: <1.5MB)
- **Dependencies**: 45 (Target: <50)

## Code Quality

- **Complejidad Promedio**: 6.2 (Target: <10)
- **Funciones >30 líneas**: 3 (Target: 0)
- **Uso de 'any'**: 0 (Target: 0)
- **Test Files Ratio**: 1:1.2 (Target: 1:1)
```

## Tips

### Adopción Gradual

- Implementa una práctica a la vez, no todas juntas
- Comienza con las más impactantes: linting y testing
- Usa herramientas automáticas para enforcing
- Celebra mejoras incrementales

### Herramientas Recomendadas

- **TypeScript**: Tipado estático
- **ESLint + Prettier**: Calidad y formato de código
- **Jest**: Testing framework
- **Husky**: Git hooks
- **Dependency Cruiser**: Análisis de dependencias
- **SonarJS**: Análisis de calidad de código

### Balance Pragmático

- No sobre-optimices prematuramente
- Prioriza legibilidad sobre cleverness
- Usa el principio 80/20: enfócate en el 20% que da 80% del valor
- Adapta las prácticas al contexto del proyecto

### Cultura de Equipo

- Code reviews constructivos, no destructivos
- Compartir conocimiento en pair programming
- Retrospectivas regulares sobre prácticas
- Documentar decisiones arquitectónicas importantes

## Ejemplos

### Ejemplo de Refactoring Guiado por Principios

#### Antes (Viola múltiples principios)

```typescript
class OrderProcessor {
   processOrder(orderData: any): any {
      // Violación: función muy larga (60+ líneas)
      // Violación: usa 'any' types
      // Violación: hace demasiadas cosas

      // Validación
      if (!orderData.email || !orderData.email.includes("@")) {
         throw new Error("Invalid email");
      }

      // Cálculos
      let total = 0;
      for (let item of orderData.items) {
         total += item.price * item.quantity;
         if (item.quantity > 10) total *= 0.9; // Descuento 10%
      }

      // Procesamiento de pago
      if (orderData.paymentMethod === "credit_card") {
         // Lógica específica de tarjeta de crédito (20 líneas)
      } else if (orderData.paymentMethod === "paypal") {
         // Lógica específica de PayPal (15 líneas)
      }

      // Persistencia
      const orderId = Math.random().toString(); // Número mágico
      // Guardar en base de datos (10 líneas)

      // Notificaciones
      // Enviar email (15 líneas)

      return { success: true, orderId };
   }
}
```

#### Después (Aplica buenas prácticas)

```typescript
// Tipos bien definidos
interface OrderData {
   email: string;
   items: OrderItem[];
   paymentMethod: PaymentMethod;
}

interface OrderItem {
   price: number;
   quantity: number;
   productId: string;
}

interface OrderResult {
   success: boolean;
   orderId: string;
}

// Constantes en lugar de números mágicos
const BULK_DISCOUNT_THRESHOLD = 10;
const BULK_DISCOUNT_RATE = 0.1;

// Single Responsibility: Solo validación
class OrderValidator {
   validate(orderData: OrderData): ValidationResult {
      const errors: string[] = [];

      if (!this.isValidEmail(orderData.email)) {
         errors.push("Invalid email format");
      }

      if (!orderData.items.length) {
         errors.push("Order must contain at least one item");
      }

      return {
         isValid: errors.length === 0,
         errors,
      };
   }

   private isValidEmail(email: string): boolean {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
   }
}

// Single Responsibility: Solo cálculos
class OrderCalculator {
   calculateTotal(items: OrderItem[]): number {
      return items.reduce((total, item) => {
         const itemTotal = item.price * item.quantity;
         const discount = item.quantity > BULK_DISCOUNT_THRESHOLD
            ? BULK_DISCOUNT_RATE
            : 0;
         return total + (itemTotal * (1 - discount));
      }, 0);
   }
}

// Open/Closed: Extensible para nuevos métodos de pago
interface PaymentProcessor {
   process(amount: number, data: PaymentData): Promise<PaymentResult>;
}

class CreditCardProcessor implements PaymentProcessor {
   async process(amount: number, data: PaymentData): Promise<PaymentResult> {
      // Implementación específica para tarjeta de crédito
      return { success: true, transactionId: generateId() };
   }
}

// Orquestador que coordina todo
class OrderProcessor {
   constructor(
      private validator: OrderValidator,
      private calculator: OrderCalculator,
      private paymentProcessor: PaymentProcessor,
      private orderRepository: OrderRepository,
      private notificationService: NotificationService,
   ) {}

   async processOrder(orderData: OrderData): Promise<OrderResult> {
      // Validar
      const validation = this.validator.validate(orderData);
      if (!validation.isValid) {
         throw new ValidationError(validation.errors);
      }

      // Calcular
      const total = this.calculator.calculateTotal(orderData.items);

      // Procesar pago
      const paymentResult = await this.paymentProcessor.process(
         total,
         orderData,
      );
      if (!paymentResult.success) {
         throw new PaymentError("Payment processing failed");
      }

      // Persistir
      const order = await this.orderRepository.create({
         ...orderData,
         total,
         transactionId: paymentResult.transactionId,
      });

      // Notificar (no await para no bloquear respuesta)
      this.notificationService.sendOrderConfirmation(order).catch((error) => {
         console.error("Failed to send notification:", error);
      });

      return {
         success: true,
         orderId: order.id,
      };
   }
}
```

### Ejemplo de Evaluación de Dependencia

#### Caso: Necesitamos formatear fechas

```typescript
// Opción 1: Moment.js (❌ NO recomendado)
import moment from "moment"; // 67KB, inmutable, muchas features no usadas
const formatted = moment().format("YYYY-MM-DD");

// Opción 2: Date-fns (✅ RECOMENDADO)
import { format } from "date-fns"; // Solo 2KB, tree-shakeable
const formatted = format(new Date(), "yyyy-MM-dd");

// Opción 3: Implementación propia (✅ ÓPTIMO para casos simples)
function formatDate(date: Date): string {
   const year = date.getFullYear();
   const month = String(date.getMonth() + 1).padStart(2, "0");
   const day = String(date.getDate()).padStart(2, "0");
   return `${year}-${month}-${day}`;
}
```

#### Evaluación de Decisión

```markdown
# Evaluación: Librería para formateo de fechas

## Opciones Consideradas

1. **Moment.js**: Descartado por tamaño (67KB) y mutabilidad
2. **Date-fns**: Seleccionado por modularidad y tamaño
3. **Implementación propia**: Para casos muy simples

## Decisión: Date-fns

- ✅ Tree-shakeable (solo importamos lo que usamos)
- ✅ Inmutable (no modifica objetos originales)
- ✅ TypeScript support nativo
- ✅ Bien mantenido y popular
- ⚠️ Cuidar de no importar funciones innecesarias

## Plan de Migración

- Comenzar con funciones básicas (format, parse)
- Evaluar en 6 meses si necesitamos más funcionalidad
- Considerar implementación propia si solo usamos 2-3 funciones
```

---

**Recuerda**: Las buenas prácticas son una guía, no dogma. Adapta según el
contexto del proyecto, pero siempre con una justificación clara.

## Navegación

- [⬅️ Generación de diagramas](./generacion-diagramas.md)
- [🏠 Volver al README](../../README.md)
- [➡️ Patrones de diseño](./patrones-diseno.md)
