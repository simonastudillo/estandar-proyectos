# Testing de Usabilidad

## ¿Qué es?

El testing de usabilidad es el proceso de evaluación de la experiencia del
usuario (UX) y la facilidad de uso de una aplicación web o móvil. Se enfoca en
identificar problemas de navegación, comprensión y eficiencia de uso para
garantizar que la aplicación sea intuitiva, accesible y satisfactoria para el
usuario final.

## ¿Por qué es importante?

- **Experiencia del usuario**: Asegura una interfaz intuitiva y agradable de
  usar
- **Adopción**: Mejora la tasa de adopción y retención de usuarios
- **Reducción de soporte**: Menos consultas y problemas reportados por usuarios
- **Conversión**: Aumenta las tasas de conversión y cumplimiento de objetivos
- **Accesibilidad**: Garantiza que la aplicación sea usable por personas con
  discapacidades
- **Competitividad**: Diferencia el producto en el mercado
- **ROI**: Reduce costos de rediseño y mejora el retorno de inversión

## ¿Qué debe incluir?

### Tipos de Testing de Usabilidad

#### Testing de Navegación

- Estructura de menús y navegación
- Breadcrumbs y orientación
- Flujos de navegación intuitivos
- Consistencia en patrones de interacción

#### Testing de Accesibilidad

- Cumplimiento de WCAG 2.1
- Navegación por teclado
- Compatibilidad con lectores de pantalla
- Contraste de colores adecuado

#### Testing de Responsive Design

- Adaptabilidad a diferentes tamaños de pantalla
- Usabilidad en dispositivos móviles
- Touch targets apropiados
- Rendimiento en dispositivos de baja gama

#### Testing de Formularios

- Claridad en etiquetas y instrucciones
- Validación de errores clara
- Flujo de completado intuitivo
- Autocompletado y ayuda contextual

#### Testing de Carga Cognitiva

- Simplicidad en interfaces
- Jerarquía visual clara
- Minimización de decisiones del usuario
- Feedback apropiado para acciones

### Métodos de Testing

#### Testing Moderado

- Sesiones con usuarios reales
- Observación directa
- Think-aloud protocol
- Entrevistas post-test

#### Testing No Moderado

- Herramientas de grabación de sesión
- Heatmaps y análisis de clics
- A/B testing
- Encuestas de satisfacción

#### Testing de Guerrilla

- Testing rápido e informal
- Usuarios aleatorios
- Feedback inmediato
- Iteraciones rápidas

## ¿Qué debo hacer?

### 1. Configurar herramientas de usability testing

#### Hotjar para heatmaps y session recordings

```html
<!-- Instalar Hotjar -->
<script>
   (function (h, o, t, j, a, r) {
      h.hj = h.hj ||
         function () {
            (h.hj.q = h.hj.q || []).push(arguments);
         };
      h._hjSettings = { hjid: YOUR_HOTJAR_ID, hjsv: 6 };
      a = o.getElementsByTagName("head")[0];
      r = o.createElement("script");
      r.async = 1;
      r.src = t + h._hjSettings.hjid + j + h._hjSettings.hjsv;
      a.appendChild(r);
   })(window, document, "https://static.hotjar.com/c/hotjar-", ".js?sv=");
</script>
```

#### Google Analytics 4 para análisis de comportamiento

```typescript
// src/utils/analytics.ts
import { gtag } from "ga-gtag";

export class AnalyticsService {
   static trackUserInteraction(
      action: string,
      element: string,
      value?: number,
   ): void {
      gtag("event", action, {
         event_category: "User Interaction",
         event_label: element,
         value: value,
      });
   }

   static trackFormError(
      formName: string,
      fieldName: string,
      errorType: string,
   ): void {
      gtag("event", "form_error", {
         event_category: "Form Validation",
         form_name: formName,
         field_name: fieldName,
         error_type: errorType,
      });
   }

   static trackPagePerformance(pageName: string, loadTime: number): void {
      gtag("event", "page_performance", {
         event_category: "Performance",
         page_name: pageName,
         load_time: loadTime,
      });
   }

   static trackUserFlow(
      stepName: string,
      stepNumber: number,
      completed: boolean,
   ): void {
      gtag("event", "user_flow", {
         event_category: "User Journey",
         step_name: stepName,
         step_number: stepNumber,
         completed: completed,
      });
   }
}
```

#### Microsoft Clarity para análisis de comportamiento

```html
<!-- Microsoft Clarity -->
<script type="text/javascript">
   (function (c, l, a, r, i, t, y) {
      c[a] = c[a] ||
         function () {
            (c[a].q = c[a].q || []).push(arguments);
         };
      t = l.createElement(r);
      t.async = 1;
      t.src = "https://www.clarity.ms/tag/" + i;
      y = l.getElementsByTagName(r)[0];
      y.parentNode.insertBefore(t, y);
   })(window, document, "clarity", "script", "YOUR_CLARITY_ID");
</script>
```

### 2. Implementar testing de accesibilidad automatizado

#### Configuración de axe-core para React

```bash
# Instalar dependencias de accesibilidad
npm install --save-dev @axe-core/react jest-axe
```

```typescript
// src/__tests__/accessibility/accessibility.test.tsx
import React from "react";
import { render } from "@testing-library/react";
import { axe, toHaveNoViolations } from "jest-axe";
import LoginForm from "../../components/LoginForm/LoginForm";
import Dashboard from "../../pages/Dashboard/Dashboard";
import UserProfile from "../../components/UserProfile/UserProfile";

expect.extend(toHaveNoViolations);

describe("Accessibility Tests", () => {
   test("LoginForm should not have accessibility violations", async () => {
      const { container } = render(<LoginForm />);
      const results = await axe(container);
      expect(results).toHaveNoViolations();
   });

   test("Dashboard should not have accessibility violations", async () => {
      const { container } = render(<Dashboard />);
      const results = await axe(container);
      expect(results).toHaveNoViolations();
   });

   test("UserProfile should not have accessibility violations", async () => {
      const mockUser = {
         id: 1,
         name: "Juan Pérez",
         email: "juan@example.com",
      };

      const { container } = render(<UserProfile user={mockUser} />);
      const results = await axe(container);
      expect(results).toHaveNoViolations();
   });

   test("should have proper heading hierarchy", () => {
      const { container } = render(<Dashboard />);

      const h1 = container.querySelectorAll("h1");
      const h2 = container.querySelectorAll("h2");
      const h3 = container.querySelectorAll("h3");

      // Debe haber exactamente un h1
      expect(h1).toHaveLength(1);

      // No debe haber saltos en la jerarquía
      if (h3.length > 0) {
         expect(h2.length).toBeGreaterThan(0);
      }
   });

   test("all images should have alt text", () => {
      const { container } = render(<Dashboard />);

      const images = container.querySelectorAll("img");
      images.forEach((img) => {
         expect(img).toHaveAttribute("alt");
         expect(img.getAttribute("alt")).not.toBe("");
      });
   });

   test("form inputs should have labels", () => {
      const { container } = render(<LoginForm />);

      const inputs = container.querySelectorAll("input, select, textarea");
      inputs.forEach((input) => {
         const id = input.getAttribute("id");
         if (id) {
            const label = container.querySelector(`label[for="${id}"]`);
            expect(label).toBeInTheDocument();
         }
      });
   });
});
```

#### Configuración de Pa11y para testing de línea de comandos

```bash
# Instalar Pa11y
npm install -g pa11y

# Crear configuración
cat > .pa11yrc << 'EOF'
{
  "standard": "WCAG2AA",
  "ignore": [
    "color-contrast"
  ],
  "chromeLaunchConfig": {
    "args": ["--no-sandbox"]
  }
}
EOF

# Script de testing
cat > scripts/accessibility-test.sh << 'EOF'
#!/bin/bash

URLS=(
  "http://localhost:3000"
  "http://localhost:3000/login"
  "http://localhost:3000/dashboard"
  "http://localhost:3000/profile"
)

echo "🔍 Ejecutando tests de accesibilidad..."

for url in "${URLS[@]}"; do
  echo "Testing: $url"
  pa11y "$url" --reporter json > "reports/a11y-$(echo $url | sed 's/[^a-zA-Z0-9]/-/g').json"

  # Mostrar resumen
  violations=$(pa11y "$url" --reporter json | jq '. | length')
  echo "  Violations found: $violations"
done

echo "✅ Tests de accesibilidad completados"
EOF

chmod +x scripts/accessibility-test.sh
```

### 3. Configurar testing de usabilidad con usuarios reales

#### Script de testing moderado

```typescript
// src/utils/usabilityTesting.ts
export interface UsabilityTask {
   id: string;
   title: string;
   description: string;
   successCriteria: string[];
   expectedTime: number; // en segundos
}

export interface UsabilitySession {
   participantId: string;
   startTime: Date;
   endTime?: Date;
   tasks: UsabilityTaskResult[];
   feedback: string;
   satisfaction: number; // 1-10
}

export interface UsabilityTaskResult {
   taskId: string;
   completed: boolean;
   timeSpent: number;
   errors: number;
   frustrationLevel: number; // 1-5
   notes: string;
}

export class UsabilityTestRunner {
   private session: UsabilitySession;
   private currentTask: number = 0;
   private startTime: Date;

   constructor(participantId: string) {
      this.session = {
         participantId,
         startTime: new Date(),
         tasks: [],
         feedback: "",
         satisfaction: 0,
      };
   }

   startTask(task: UsabilityTask): void {
      this.startTime = new Date();
      console.log(`🎯 Tarea ${this.currentTask + 1}: ${task.title}`);
      console.log(`📝 Descripción: ${task.description}`);
      console.log(`✅ Criterios de éxito: ${task.successCriteria.join(", ")}`);
   }

   completeTask(
      completed: boolean,
      errors: number,
      frustrationLevel: number,
      notes: string,
   ): void {
      const timeSpent = (new Date().getTime() - this.startTime.getTime()) /
         1000;

      const result: UsabilityTaskResult = {
         taskId: `task-${this.currentTask + 1}`,
         completed,
         timeSpent,
         errors,
         frustrationLevel,
         notes,
      };

      this.session.tasks.push(result);
      this.currentTask++;

      console.log(`⏱️ Tiempo: ${timeSpent}s`);
      console.log(
         `${completed ? "✅" : "❌"} ${
            completed ? "Completada" : "No completada"
         }`,
      );
   }

   endSession(feedback: string, satisfaction: number): UsabilitySession {
      this.session.endTime = new Date();
      this.session.feedback = feedback;
      this.session.satisfaction = satisfaction;

      return this.session;
   }

   generateReport(): UsabilityReport {
      const totalTasks = this.session.tasks.length;
      const completedTasks =
         this.session.tasks.filter((t) => t.completed).length;
      const averageTime =
         this.session.tasks.reduce((sum, t) => sum + t.timeSpent, 0) /
         totalTasks;
      const totalErrors = this.session.tasks.reduce(
         (sum, t) => sum + t.errors,
         0,
      );
      const averageFrustration =
         this.session.tasks.reduce((sum, t) => sum + t.frustrationLevel, 0) /
         totalTasks;

      return {
         participantId: this.session.participantId,
         completionRate: (completedTasks / totalTasks) * 100,
         averageTaskTime: averageTime,
         totalErrors,
         averageFrustration,
         satisfaction: this.session.satisfaction,
         recommendations: this.generateRecommendations(),
      };
   }

   private generateRecommendations(): string[] {
      const recommendations: string[] = [];

      const incompleteTasks = this.session.tasks.filter((t) => !t.completed);
      if (incompleteTasks.length > 0) {
         recommendations.push(
            "Revisar tareas no completadas para identificar barreras de usabilidad",
         );
      }

      const highFrustrationTasks = this.session.tasks.filter(
         (t) => t.frustrationLevel >= 4,
      );
      if (highFrustrationTasks.length > 0) {
         recommendations.push(
            "Simplificar tareas que generan alta frustración",
         );
      }

      if (this.session.satisfaction < 7) {
         recommendations.push("Mejorar satisfacción general del usuario");
      }

      return recommendations;
   }
}

export interface UsabilityReport {
   participantId: string;
   completionRate: number;
   averageTaskTime: number;
   totalErrors: number;
   averageFrustration: number;
   satisfaction: number;
   recommendations: string[];
}
```

#### Tareas de usabilidad predefinidas

```typescript
// src/data/usabilityTasks.ts
export const USABILITY_TASKS: UsabilityTask[] = [
   {
      id: "task-1",
      title: "Registro de usuario",
      description: "Crear una nueva cuenta de usuario en la aplicación",
      successCriteria: [
         "Encontrar el enlace de registro",
         "Completar el formulario correctamente",
         "Confirmar la cuenta por email",
      ],
      expectedTime: 180, // 3 minutos
   },
   {
      id: "task-2",
      title: "Iniciar sesión",
      description: "Acceder a la aplicación con las credenciales creadas",
      successCriteria: [
         "Encontrar el formulario de login",
         "Ingresar credenciales correctas",
         "Acceder al dashboard",
      ],
      expectedTime: 60, // 1 minuto
   },
   {
      id: "task-3",
      title: "Actualizar perfil",
      description: "Cambiar la información personal en el perfil de usuario",
      successCriteria: [
         "Navegar a la sección de perfil",
         "Editar información personal",
         "Guardar cambios exitosamente",
      ],
      expectedTime: 120, // 2 minutos
   },
   {
      id: "task-4",
      title: "Buscar contenido",
      description:
         "Encontrar información específica usando la función de búsqueda",
      successCriteria: [
         "Localizar la barra de búsqueda",
         "Realizar búsqueda efectiva",
         "Interpretar resultados correctamente",
      ],
      expectedTime: 90, // 1.5 minutos
   },
   {
      id: "task-5",
      title: "Cerrar sesión",
      description: "Salir de la aplicación de manera segura",
      successCriteria: [
         "Encontrar opción de cerrar sesión",
         "Confirmar cierre de sesión",
         "Verificar que la sesión terminó",
      ],
      expectedTime: 30, // 30 segundos
   },
];
```

### 4. Implementar A/B testing para optimización

#### Configuración de A/B testing con React

```typescript
// src/hooks/useABTest.ts
import { useEffect, useState } from "react";

interface ABTestConfig {
   testName: string;
   variants: string[];
   weights?: number[];
}

interface ABTestResult {
   variant: string;
   isLoading: boolean;
}

export function useABTest({
   testName,
   variants,
   weights,
}: ABTestConfig): ABTestResult {
   const [variant, setVariant] = useState<string>("");
   const [isLoading, setIsLoading] = useState(true);

   useEffect(() => {
      // Verificar si el usuario ya tiene una variante asignada
      const savedVariant = localStorage.getItem(`ab-test-${testName}`);

      if (savedVariant && variants.includes(savedVariant)) {
         setVariant(savedVariant);
         setIsLoading(false);
         return;
      }

      // Asignar nueva variante
      const assignedVariant = assignVariant(variants, weights);
      setVariant(assignedVariant);
      localStorage.setItem(`ab-test-${testName}`, assignedVariant);
      setIsLoading(false);

      // Tracking del evento
      trackABTestAssignment(testName, assignedVariant);
   }, [testName, variants, weights]);

   return { variant, isLoading };
}

function assignVariant(variants: string[], weights?: number[]): string {
   if (!weights || weights.length !== variants.length) {
      // Distribución uniforme
      const randomIndex = Math.floor(Math.random() * variants.length);
      return variants[randomIndex];
   }

   // Distribución ponderada
   const totalWeight = weights.reduce((sum, weight) => sum + weight, 0);
   const random = Math.random() * totalWeight;

   let cumulativeWeight = 0;
   for (let i = 0; i < variants.length; i++) {
      cumulativeWeight += weights[i];
      if (random <= cumulativeWeight) {
         return variants[i];
      }
   }

   return variants[0]; // Fallback
}

function trackABTestAssignment(testName: string, variant: string): void {
   // Integración con Google Analytics
   if (typeof gtag !== "undefined") {
      gtag("event", "ab_test_assignment", {
         test_name: testName,
         variant: variant,
         event_category: "A/B Testing",
      });
   }

   // También enviar a tu analytics interno
   fetch("/api/v1/analytics/ab-test", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
         testName,
         variant,
         userId: getUserId(),
         timestamp: new Date().toISOString(),
      }),
   }).catch(console.error);
}

function getUserId(): string {
   // Obtener ID de usuario o generar ID anónimo
   return localStorage.getItem("user-id") || "anonymous";
}
```

#### Ejemplo de uso de A/B testing

```typescript
// src/components/CTAButton/CTAButton.tsx
import React from "react";
import { useABTest } from "../../hooks/useABTest";
import { AnalyticsService } from "../../utils/analytics";

interface CTAButtonProps {
   onClick: () => void;
   children: React.ReactNode;
}

export default function CTAButton({
   onClick,
   children,
}: CTAButtonProps): JSX.Element {
   const { variant, isLoading } = useABTest({
      testName: "cta-button-color",
      variants: ["blue", "green", "red"],
      weights: [0.4, 0.4, 0.2], // 40% azul, 40% verde, 20% rojo
   });

   const handleClick = () => {
      // Tracking de conversión
      AnalyticsService.trackUserInteraction("cta_click", `button-${variant}`);
      onClick();
   };

   if (isLoading) {
      return <button disabled>Loading...</button>;
   }

   const getButtonStyle = () => {
      switch (variant) {
         case "blue":
            return "bg-blue-600 hover:bg-blue-700";
         case "green":
            return "bg-green-600 hover:bg-green-700";
         case "red":
            return "bg-red-600 hover:bg-red-700";
         default:
            return "bg-blue-600 hover:bg-blue-700";
      }
   };

   return (
      <button
         onClick={handleClick}
         className={`px-4 py-2 text-white font-medium rounded ${getButtonStyle()}`}
         data-testid={`cta-button-${variant}`}
      >
         {children}
      </button>
   );
}
```

### 5. Configurar testing de carga cognitiva

#### Métricas de complejidad visual

```typescript
// src/utils/cognitiveLoad.ts
export interface CognitiveLoadMetrics {
   elementCount: number;
   interactiveElements: number;
   textDensity: number;
   colorVariety: number;
   navigationDepth: number;
   formComplexity: number;
}

export class CognitiveLoadAnalyzer {
   static analyze(container: HTMLElement): CognitiveLoadMetrics {
      return {
         elementCount: this.countElements(container),
         interactiveElements: this.countInteractiveElements(container),
         textDensity: this.calculateTextDensity(container),
         colorVariety: this.calculateColorVariety(container),
         navigationDepth: this.calculateNavigationDepth(container),
         formComplexity: this.calculateFormComplexity(container),
      };
   }

   private static countElements(container: HTMLElement): number {
      return container.querySelectorAll("*").length;
   }

   private static countInteractiveElements(container: HTMLElement): number {
      const selectors =
         "button, a, input, select, textarea, [onclick], [tabindex]";
      return container.querySelectorAll(selectors).length;
   }

   private static calculateTextDensity(container: HTMLElement): number {
      const textContent = container.textContent || "";
      const containerArea = container.offsetWidth * container.offsetHeight;
      return textContent.length / containerArea;
   }

   private static calculateColorVariety(container: HTMLElement): number {
      const elements = container.querySelectorAll("*");
      const colors = new Set<string>();

      elements.forEach((element) => {
         const computedStyle = getComputedStyle(element);
         colors.add(computedStyle.color);
         colors.add(computedStyle.backgroundColor);
      });

      return colors.size;
   }

   private static calculateNavigationDepth(container: HTMLElement): number {
      const navElements = container.querySelectorAll("nav, .nav, .menu");
      let maxDepth = 0;

      navElements.forEach((nav) => {
         const depth = this.getMaxNestingDepth(nav);
         maxDepth = Math.max(maxDepth, depth);
      });

      return maxDepth;
   }

   private static calculateFormComplexity(container: HTMLElement): number {
      const forms = container.querySelectorAll("form");
      let totalComplexity = 0;

      forms.forEach((form) => {
         const fields = form.querySelectorAll("input, select, textarea").length;
         const groups = form.querySelectorAll("fieldset, .form-group").length;
         totalComplexity += fields + groups * 0.5;
      });

      return totalComplexity;
   }

   private static getMaxNestingDepth(element: Element): number {
      let maxDepth = 0;

      function traverse(el: Element, depth: number) {
         maxDepth = Math.max(maxDepth, depth);
         Array.from(el.children).forEach((child) => {
            traverse(child, depth + 1);
         });
      }

      traverse(element, 0);
      return maxDepth;
   }

   static assessCognitiveLoad(
      metrics: CognitiveLoadMetrics,
   ): CognitiveLoadAssessment {
      const score = this.calculateScore(metrics);
      const level = this.getLoadLevel(score);
      const recommendations = this.generateRecommendations(metrics, level);

      return { score, level, recommendations, metrics };
   }

   private static calculateScore(metrics: CognitiveLoadMetrics): number {
      // Algoritmo de puntuación basado en investigación UX
      let score = 0;

      // Penalizar muchos elementos
      if (metrics.elementCount > 100) score += 2;
      else if (metrics.elementCount > 50) score += 1;

      // Penalizar muchos elementos interactivos
      if (metrics.interactiveElements > 20) score += 2;
      else if (metrics.interactiveElements > 10) score += 1;

      // Penalizar alta densidad de texto
      if (metrics.textDensity > 0.01) score += 2;
      else if (metrics.textDensity > 0.005) score += 1;

      // Penalizar muchos colores
      if (metrics.colorVariety > 15) score += 2;
      else if (metrics.colorVariety > 10) score += 1;

      // Penalizar navegación profunda
      if (metrics.navigationDepth > 4) score += 2;
      else if (metrics.navigationDepth > 3) score += 1;

      // Penalizar formularios complejos
      if (metrics.formComplexity > 15) score += 2;
      else if (metrics.formComplexity > 10) score += 1;

      return Math.min(score, 10); // Máximo 10
   }

   private static getLoadLevel(score: number): "low" | "medium" | "high" {
      if (score <= 3) return "low";
      if (score <= 6) return "medium";
      return "high";
   }

   private static generateRecommendations(
      metrics: CognitiveLoadMetrics,
      level: "low" | "medium" | "high",
   ): string[] {
      const recommendations: string[] = [];

      if (level === "high") {
         recommendations.push(
            "Simplificar la interfaz eliminando elementos innecesarios",
         );
         recommendations.push("Agrupar elementos relacionados visualmente");
         recommendations.push("Reducir la cantidad de opciones simultáneas");
      }

      if (metrics.interactiveElements > 15) {
         recommendations.push(
            "Reducir el número de elementos interactivos visibles",
         );
         recommendations.push("Considerar navegación progresiva o wizards");
      }

      if (metrics.colorVariety > 12) {
         recommendations.push(
            "Establecer una paleta de colores más consistente",
         );
         recommendations.push(
            "Usar color de manera estratégica para jerarquía",
         );
      }

      if (metrics.formComplexity > 12) {
         recommendations.push("Dividir formularios largos en pasos múltiples");
         recommendations.push("Agrupar campos relacionados");
         recommendations.push("Proporcionar ayuda contextual");
      }

      return recommendations;
   }
}

export interface CognitiveLoadAssessment {
   score: number;
   level: "low" | "medium" | "high";
   recommendations: string[];
   metrics: CognitiveLoadMetrics;
}
```

### 6. Testing automatizado de usabilidad

```typescript
// src/__tests__/usability/usability.test.tsx
import React from "react";
import { fireEvent, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { CognitiveLoadAnalyzer } from "../../utils/cognitiveLoad";
import Dashboard from "../../pages/Dashboard/Dashboard";
import LoginForm from "../../components/LoginForm/LoginForm";

describe("Usability Tests", () => {
   test("should have low cognitive load on login form", () => {
      const { container } = render(<LoginForm />);
      const assessment = CognitiveLoadAnalyzer.assessCognitiveLoad(
         CognitiveLoadAnalyzer.analyze(container),
      );

      expect(assessment.level).toBe("low");
      expect(assessment.score).toBeLessThan(4);
   });

   test("should have accessible touch targets on mobile", () => {
      const { container } = render(<Dashboard />);

      // Simular viewport móvil
      Object.defineProperty(window, "innerWidth", { value: 375 });
      Object.defineProperty(window, "innerHeight", { value: 667 });

      const buttons = container.querySelectorAll(
         'button, a, input[type="button"]',
      );

      buttons.forEach((button) => {
         const rect = button.getBoundingClientRect();
         const size = Math.min(rect.width, rect.height);

         // Los touch targets deben ser al menos 44px según Apple HIG
         expect(size).toBeGreaterThanOrEqual(44);
      });
   });

   test("should provide clear feedback for user actions", async () => {
      const user = userEvent.setup();
      render(<LoginForm />);

      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });

      // Enviar formulario vacío
      await user.click(submitButton);

      // Debe mostrar mensajes de error claros
      expect(screen.getByText(/email es requerido/i)).toBeInTheDocument();
      expect(screen.getByText(/contraseña es requerida/i)).toBeInTheDocument();
   });

   test("should have logical tab order", () => {
      render(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      const passwordInput = screen.getByLabelText(/contraseña/i);
      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });

      // Verificar orden de tab
      expect(emailInput.tabIndex).toBeLessThan(passwordInput.tabIndex);
      expect(passwordInput.tabIndex).toBeLessThan(submitButton.tabIndex);
   });

   test("should provide help text for complex fields", () => {
      render(<LoginForm />);

      const passwordInput = screen.getByLabelText(/contraseña/i);
      const helpText = screen.queryByText(/mínimo 8 caracteres/i);

      expect(helpText).toBeInTheDocument();
      expect(passwordInput).toHaveAttribute("aria-describedby");
   });

   test("should handle errors gracefully", async () => {
      const user = userEvent.setup();

      // Mock de error de red
      global.fetch = jest.fn().mockRejectedValue(new Error("Network error"));

      render(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      const passwordInput = screen.getByLabelText(/contraseña/i);
      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });

      await user.type(emailInput, "test@example.com");
      await user.type(passwordInput, "password123");
      await user.click(submitButton);

      // Debe mostrar mensaje de error amigable
      expect(await screen.findByText(/error de conexión/i)).toBeInTheDocument();
   });

   test("should have consistent visual hierarchy", () => {
      const { container } = render(<Dashboard />);

      const h1Elements = container.querySelectorAll("h1");
      const h2Elements = container.querySelectorAll("h2");

      // Solo debe haber un H1 principal
      expect(h1Elements).toHaveLength(1);

      // Los H2 deben tener tamaño menor que H1
      if (h1Elements.length > 0 && h2Elements.length > 0) {
         const h1Style = getComputedStyle(h1Elements[0]);
         const h2Style = getComputedStyle(h2Elements[0]);

         const h1Size = parseFloat(h1Style.fontSize);
         const h2Size = parseFloat(h2Style.fontSize);

         expect(h1Size).toBeGreaterThan(h2Size);
      }
   });

   test("should have sufficient color contrast", () => {
      const { container } = render(<Dashboard />);

      const textElements = container.querySelectorAll("p, span, label, button");

      textElements.forEach((element) => {
         const style = getComputedStyle(element);
         const backgroundColor = style.backgroundColor;
         const textColor = style.color;

         // Esta es una verificación básica - en producción usarías una librería
         // específica para calcular el ratio de contraste
         expect(backgroundColor).not.toBe(textColor);
      });
   });
});
```

### 7. Configurar pipeline de usability testing

```yaml
# .github/workflows/usability-tests.yml
name: Usability Tests

on:
   pull_request:
      branches: [main]
      paths: ["src/components/**", "src/pages/**"]
   schedule:
      - cron: "0 1 * * 1" # Lunes a la 1 AM

jobs:
   accessibility-tests:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Install dependencies
           run: npm ci

         - name: Build application
           run: npm run build

         - name: Start application
           run: |
              npm run preview &
              sleep 10

         - name: Run accessibility tests
           run: npm run test:a11y

         - name: Run Pa11y tests
           run: |
              npm install -g pa11y
              pa11y http://localhost:4173 --reporter json > pa11y-report.json

         - name: Upload accessibility reports
           uses: actions/upload-artifact@v3
           with:
              name: accessibility-reports
              path: |
                 pa11y-report.json
                 coverage/a11y/

   usability-metrics:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Install dependencies
           run: npm ci

         - name: Run cognitive load tests
           run: npm run test:cognitive-load

         - name: Generate usability report
           run: |
              node scripts/generate-usability-report.js > usability-report.json

         - name: Check usability thresholds
           run: |
              COGNITIVE_SCORE=$(cat usability-report.json | jq '.cognitiveLoad.score')
              if (( $(echo "$COGNITIVE_SCORE > 6" | bc -l) )); then
                echo "Cognitive load score too high: $COGNITIVE_SCORE"
                exit 1
              fi

         - name: Upload usability reports
           uses: actions/upload-artifact@v3
           with:
              name: usability-reports
              path: usability-report.json

   lighthouse-ux:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Install dependencies
           run: npm ci

         - name: Build and start application
           run: |
              npm run build
              npm run preview &
              sleep 10

         - name: Run Lighthouse UX audit
           run: |
              npm install -g lighthouse
              lighthouse http://localhost:4173 \
                --only-categories=accessibility,best-practices \
                --output=json \
                --output-path=lighthouse-ux.json \
                --chrome-flags="--headless --no-sandbox"

         - name: Check UX scores
           run: |
              ACCESSIBILITY_SCORE=$(cat lighthouse-ux.json | jq '.categories.accessibility.score * 100')
              BEST_PRACTICES_SCORE=$(cat lighthouse-ux.json | jq '.categories["best-practices"].score * 100')

              echo "Accessibility score: $ACCESSIBILITY_SCORE"
              echo "Best practices score: $BEST_PRACTICES_SCORE"

              if (( $(echo "$ACCESSIBILITY_SCORE < 90" | bc -l) )); then
                echo "Accessibility score below threshold"
                exit 1
              fi

         - name: Upload Lighthouse reports
           uses: actions/upload-artifact@v3
           with:
              name: lighthouse-ux-reports
              path: lighthouse-ux.json
```

## Tips

- **Testing temprano y frecuente**: Integra usability testing desde las primeras
  fases
- **Usuarios reales**: Siempre que sea posible, testea con usuarios reales del
  target
- **Iteración rápida**: Haz cambios pequeños y vuelve a testear rápidamente
- **Contexto de uso**: Testea en condiciones similares al uso real
- **Métricas cualitativas y cuantitativas**: Combina datos numéricos con
  observaciones
- **Accesibilidad first**: Diseña pensando en accesibilidad desde el inicio
- **Mobile-first**: Prioriza la experiencia móvil en el testing
- **Documentación**: Registra hallazgos y decisiones para referencia futura

## Ejemplos

### Script completo de usability testing

```bash
#!/bin/bash
# complete-usability-test.sh

echo "🎯 Iniciando suite completo de usability testing..."

RESULTS_DIR="usability-results-$(date +%Y%m%d_%H%M%S)"
mkdir -p $RESULTS_DIR

# 1. Tests de accesibilidad automatizados
echo "♿ Ejecutando tests de accesibilidad..."
npm run test:a11y -- --outputFile=$RESULTS_DIR/a11y-results.json

# 2. Análisis de cognitive load
echo "🧠 Analizando carga cognitiva..."
npm run test:cognitive-load -- --output=$RESULTS_DIR/cognitive-load.json

# 3. Lighthouse UX audit
echo "🔍 Ejecutando auditoría Lighthouse..."
lighthouse http://localhost:3000 \
  --only-categories=accessibility,best-practices \
  --output=json \
  --output-path=$RESULTS_DIR/lighthouse-ux.json

# 4. Pa11y accessibility scan
echo "🔎 Escaneando accesibilidad con Pa11y..."
pa11y http://localhost:3000 \
  --reporter json > $RESULTS_DIR/pa11y-report.json

# 5. Generar reporte consolidado
echo "📊 Generando reporte consolidado..."
node scripts/generate-usability-report.js \
  --input=$RESULTS_DIR \
  --output=$RESULTS_DIR/consolidated-report.html

echo "✅ Usability testing completado. Ver: $RESULTS_DIR/consolidated-report.html"
```

---

## Navegación

**Progreso en Testing y Quality Assurance:**

- ✅ [Testing y QA](./testing-qa.md)
- ✅ [Tipos de Pruebas](./tipos-pruebas.md)
- ✅ [Testing Funcional Automatizado](./testing-funcional-automatizado.md)
- ✅ [Testing de Regresión](./testing-regresion.md)
- ✅ [Checklists QA](./checklists-qa.md)
- ✅ [Pruebas de Aceptación del Usuario](./pruebas-aceptacion-usuario.md)
- ✅ [Gestión de Reportes de Errores](./gestion-reportes-errores.md)
- ✅ [Testing de Performance y Carga](./testing-performance-carga.md)
- ✅ [Testing de Seguridad OWASP](./testing-seguridad-owasp.md)
- ✅ **Testing de Usabilidad** ← Estás aquí
- ⏭️ [Code Review y Refactoring](./code-review-refactoring.md)
- ⏭️ [Auditoría de Calidad de Código](./auditoria-calidad-codigo.md)
- ⏭️ [Checklist Específico de Performance](./checklist-performance.md)

---

### Siguiente Paso

Continúa con [**Code Review y Refactoring**](./code-review-refactoring.md)

[⬅️ Testing de Seguridad OWASP](./testing-seguridad-owasp.md) |
[🏠 README Principal](../../README.md) |
[➡️ Code Review y Refactoring](./code-review-refactoring.md)
