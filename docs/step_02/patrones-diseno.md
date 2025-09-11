# Patrones de Diseño

## ¿Qué es?

Los patrones de diseño son soluciones reutilizables y probadas para problemas
comunes que ocurren en el diseño de software. Son plantillas que describen cómo
resolver un problema de manera que pueda ser usado en muchas situaciones
diferentes. Los patrones de diseño representan las mejores prácticas
desarrolladas por programadores experimentados a lo largo del tiempo.

Se clasifican en tres categorías principales:

- **Patrones Creacionales**: Se enfocan en la creación de objetos
- **Patrones Estructurales**: Se ocupan de la composición de clases y objetos
- **Patrones de Comportamiento**: Se centran en la comunicación entre objetos

## ¿Por qué es importante?

Los patrones de diseño son fundamentales porque:

1. **Reutilización**: Proporcionan soluciones probadas que pueden aplicarse en
   múltiples contextos
2. **Comunicación**: Crean un vocabulario común entre desarrolladores
3. **Mantenibilidad**: Facilitan el mantenimiento y evolución del código
4. **Escalabilidad**: Ayudan a diseñar sistemas que pueden crecer de manera
   controlada
5. **Calidad**: Mejoran la estructura y organización del código
6. **Flexibilidad**: Permiten que el código sea más adaptable a cambios futuros
7. **Documentación**: Actúan como documentación viva del diseño del sistema

## ¿Qué debe incluir?

La implementación de patrones de diseño debe incluir:

### Patrones Creacionales

- **Singleton**: Una sola instancia de una clase
- **Factory Method**: Creación de objetos sin especificar clases exactas
- **Abstract Factory**: Familias de objetos relacionados
- **Builder**: Construcción paso a paso de objetos complejos
- **Prototype**: Clonación de objetos existentes

### Patrones Estructurales

- **Adapter**: Compatibilidad entre interfaces incompatibles
- **Decorator**: Agregar funcionalidad sin modificar estructura
- **Facade**: Interfaz simplificada para subsistemas complejos
- **Composite**: Estructura de árbol para objetos individuales y compuestos
- **Proxy**: Sustituto o placeholder para otro objeto

### Patrones de Comportamiento

- **Observer**: Notificación automática de cambios
- **Strategy**: Algoritmos intercambiables
- **Command**: Encapsulación de solicitudes como objetos
- **State**: Cambio de comportamiento según estado interno
- **Template Method**: Esqueleto de algoritmo con pasos personalizables

## ¿Qué debo hacer?

### 1. Análisis de Requisitos (1 Story Point)

- Identificar problemas recurrentes en el diseño
- Analizar la complejidad del sistema
- Determinar qué patrones son más relevantes

### 2. Selección de Patrones (2 Story Points)

- Elegir patrones apropiados para cada problema
- Considerar el contexto y las restricciones
- Evaluar el impacto en la arquitectura general

### 3. Diseño de la Implementación (3 Story Points)

- Crear diagramas UML de los patrones seleccionados
- Definir interfaces y contratos
- Planificar la integración con el código existente

### 4. Implementación (5 Story Points)

- Implementar cada patrón siguiendo las mejores prácticas
- Asegurar el cumplimiento de principios SOLID
- Escribir código limpio y bien documentado

### 5. Testing y Validación (3 Story Points)

- Crear pruebas unitarias para cada patrón
- Validar el comportamiento esperado
- Verificar la integración con otros componentes

### 6. Documentación (2 Story Points)

- Documentar cada patrón implementado
- Crear guías de uso y ejemplos
- Actualizar la documentación de arquitectura

## Tips

### Mejores Prácticas

- **No sobreuses patrones**: Solo úsalos cuando realmente resuelvan un problema
- **Comprende antes de implementar**: Asegúrate de entender completamente el
  patrón
- **Adapta según el contexto**: Los patrones son guías, no reglas rígidas
- **Mantén la simplicidad**: No compliques innecesariamente el diseño

### Para React/TypeScript

- Usa **Factory Method** para crear componentes dinámicamente
- Implementa **Observer** con Context API para manejo de estado
- Aplica **Strategy** para diferentes algoritmos de validación
- Utiliza **Decorator** con Higher-Order Components (HOCs)

### Para Laravel

- Usa **Repository Pattern** para acceso a datos
- Implementa **Service Pattern** para lógica de negocio
- Aplica **Observer** con Eloquent Events
- Utiliza **Factory** para creación de modelos en testing

### Herramientas Útiles

- Diagramas UML para visualizar patrones
- IDEs con soporte para refactoring
- Linters para mantener consistencia
- Herramientas de análisis estático

## Ejemplos

### Ejemplo 1: Singleton en TypeScript

```typescript
class ConfigManager {
   private static instance: ConfigManager;
   private config: Record<string, any> = {};

   private constructor() {}

   public static getInstance(): ConfigManager {
      if (!ConfigManager.instance) {
         ConfigManager.instance = new ConfigManager();
      }
      return ConfigManager.instance;
   }

   public setConfig(key: string, value: any): void {
      this.config[key] = value;
   }

   public getConfig(key: string): any {
      return this.config[key];
   }
}

// Uso
const config = ConfigManager.getInstance();
config.setConfig("apiUrl", "https://api.example.com");
```

### Ejemplo 2: Factory Method en React

```typescript
interface ButtonProps {
   onClick: () => void;
   children: React.ReactNode;
}

abstract class ButtonFactory {
   abstract createButton(props: ButtonProps): JSX.Element;
}

class PrimaryButtonFactory extends ButtonFactory {
   createButton(props: ButtonProps): JSX.Element {
      return (
         <button className="btn btn-primary" onClick={props.onClick}>
            {props.children}
         </button>
      );
   }
}

class SecondaryButtonFactory extends ButtonFactory {
   createButton(props: ButtonProps): JSX.Element {
      return (
         <button className="btn btn-secondary" onClick={props.onClick}>
            {props.children}
         </button>
      );
   }
}
```

### Ejemplo 3: Observer en Laravel

```php
// Event
class UserRegistered
{
    public function __construct(public User $user) {}
}

// Listener
class SendWelcomeEmail
{
    public function handle(UserRegistered $event): void
    {
        Mail::to($event->user->email)
            ->send(new WelcomeMail($event->user));
    }
}

// En el EventServiceProvider
protected $listen = [
    UserRegistered::class => [
        SendWelcomeEmail::class,
    ],
];

// Uso en el controlador
public function register(Request $request)
{
    $user = User::create($request->validated());

    event(new UserRegistered($user));

    return response()->json(['message' => 'User registered successfully']);
}
```

### Ejemplo 4: Strategy Pattern en TypeScript

```typescript
interface PaymentStrategy {
   pay(amount: number): void;
}

class CreditCardPayment implements PaymentStrategy {
   constructor(private cardNumber: string) {}

   pay(amount: number): void {
      console.log(
         `Paid $${amount} using Credit Card ending in ${
            this.cardNumber.slice(-4)
         }`,
      );
   }
}

class PayPalPayment implements PaymentStrategy {
   constructor(private email: string) {}

   pay(amount: number): void {
      console.log(`Paid $${amount} using PayPal account ${this.email}`);
   }
}

class PaymentProcessor {
   constructor(private strategy: PaymentStrategy) {}

   setStrategy(strategy: PaymentStrategy): void {
      this.strategy = strategy;
   }

   processPayment(amount: number): void {
      this.strategy.pay(amount);
   }
}

// Uso
const processor = new PaymentProcessor(
   new CreditCardPayment("1234-5678-9012-3456"),
);
processor.processPayment(100);

processor.setStrategy(new PayPalPayment("user@example.com"));
processor.processPayment(50);
```

## Navegación

**Progreso en Diseño y Arquitectura:**

- ✅ [Diseño y Arquitectura - Introducción](./diseno-arquitectura.md)
- ✅ [Stack Tecnológico](./stack-tecnologico.md)
- ✅ [Elección de Herramientas](./eleccion-herramientas.md)
- ✅ **Patrones de Diseño** ← Estás aquí
- ⏭️ [Arquitectura del Sistema](./arquitectura-sistema.md)
- ⏭️ [Diagrama de Arquitectura General](./diagrama-arquitectura-general.md)
- ⏭️ [Estructura de Carpetas](./estructura-carpetas.md)
- ⏭️ [Diseño de Base de Datos](./diseno-base-datos.md)
- ⏭️ [Diseño de APIs](./diseno-apis.md)
- ⏭️ [Diagramas de Flujo](./diagramas-flujo.md)
- ⏭️ [Casos de Uso y User Stories](./casos-uso-user-stories.md)
- ⏭️ [Prototipos y Wireframes](./prototipos-wireframes.md)
- ⏭️ [Especificaciones Técnicas](./especificaciones-tecnicas.md)

---

### Siguiente Paso

Continúa con la [**Arquitectura del Sistema**](./arquitectura-sistema.md).

[⬅️ Elección de Herramientas](./eleccion-herramientas.md) |
[🏠 README Principal](../../README.md) |
[➡️ Arquitectura del Sistema](./arquitectura-sistema.md)
