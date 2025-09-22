# Métricas y Analytics de Uso

## Conocimientos Involucrados
- Análisis de datos y métricas
- Herramientas de analytics
- Optimización de experiencia de usuario

## Responsable
- Equipo de producto y análisis

## ¿Qué es?

Las métricas y analytics de uso son sistemas de medición y análisis que
proporcionan insights sobre cómo los usuarios interactúan con la aplicación, qué
funcionalidades utilizan más, dónde encuentran dificultades y cómo evoluciona el
comportamiento de uso a lo largo del tiempo. Estas métricas son fundamentales
para la toma de decisiones informadas sobre el producto.

## ¿Por qué es importante?

- **Toma de decisiones basada en datos**: Proporciona evidencia objetiva para
  decisiones de producto
- **Optimización de UX**: Identifica puntos de fricción y oportunidades de
  mejora
- **Validación de hipótesis**: Confirma o refuta suposiciones sobre
  comportamiento de usuarios
- **ROI del producto**: Mide el retorno de inversión de nuevas funcionalidades
- **Detección temprana de problemas**: Identifica issues antes de que se vuelvan
  críticos
- **Planificación de roadmap**: Informa la priorización de futuras
  funcionalidades
- **Comprensión del valor**: Entiende qué aspectos del producto aportan más
  valor
- **Optimización de performance**: Identifica cuellos de botella y áreas de
  mejora técnica

## ¿Qué debe incluir?

### Métricas de Adopción

- **Usuarios activos**: DAU, WAU, MAU (Daily, Weekly, Monthly Active Users)
- **Tasa de retención**: Usuarios que regresan después del primer uso
- **Onboarding success**: Porcentaje de usuarios que completan el proceso de
  incorporación
- **Feature adoption**: Adopción de funcionalidades específicas

### Métricas de Engagement

- **Tiempo en aplicación**: Duración promedio de sesiones
- **Frecuencia de uso**: Cuántas veces por día/semana usan la aplicación
- **Profundidad de uso**: Cuántas funcionalidades diferentes utilizan
- **Patrones de navegación**: Rutas más comunes dentro de la aplicación

### Métricas de Performance

- **Velocidad de carga**: Tiempo de respuesta de páginas y APIs
- **Errores**: Tasa de errores 4xx, 5xx, JavaScript errors
- **Uptime**: Disponibilidad del sistema
- **Core Web Vitals**: LCP, FID, CLS

### Métricas de Negocio

- **Conversión**: Tasas de conversión en diferentes funnels
- **Revenue per user**: Ingresos generados por usuario
- **Costo de adquisición**: CAC (Customer Acquisition Cost)
- **Lifetime value**: CLV (Customer Lifetime Value)
- Feature usage patterns
- Session duration y page views
- Drop-off points y friction areas
- A/B testing results

### Reportes y Dashboards

- Executive dashboards con KPIs principales
- Technical dashboards para equipos de desarrollo
- Product analytics para product managers
- Automated alerts para métricas críticas

## ¿Qué debo hacer?

### 1. Implementar Sistema de Métricas

**Backend tracking service:**

```php
<?php
// app/Application/Services/AnalyticsService.php
namespace App\Application\Services;

use App\Domain\Analytics\Entities\Event;
use App\Domain\Analytics\ValueObjects\EventType;
use App\Domain\Analytics\Repositories\EventRepositoryInterface;

class AnalyticsService
{
    public function __construct(
        private EventRepositoryInterface $eventRepository,
        private MetricsCollector $metricsCollector,
        private BusinessMetricsCalculator $businessMetrics
    ) {}

    public function trackEvent(
        EventType $eventType,
        string $userId,
        array $properties = [],
        array $context = []
    ): void {
        $event = Event::create(
            type: $eventType,
            userId: $userId,
            properties: $properties,
            context: array_merge($context, [
                'timestamp' => new \DateTimeImmutable(),
                'session_id' => $this->getSessionId(),
                'user_agent' => request()->userAgent(),
                'ip_address' => request()->ip(),
                'referrer' => request()->header('referer')
            ])
        );

        $this->eventRepository->save($event);
        
        // Send to real-time analytics
        $this->metricsCollector->collect($event);
        
        // Update business metrics
        $this->updateBusinessMetrics($event);
    }

    public function trackUserAction(
        string $action,
        string $userId,
        ?string $entityId = null,
        array $metadata = []
    ): void {
        $this->trackEvent(
            EventType::USER_ACTION,
            $userId,
            [
                'action' => $action,
                'entity_id' => $entityId,
                'metadata' => $metadata
            ]
        );
    }

    public function trackBusinessEvent(
        string $eventName,
        string $userId,
        float $value = 0.0,
        array $properties = []
    ): void {
        $this->trackEvent(
            EventType::BUSINESS_EVENT,
            $userId,
            array_merge($properties, [
                'event_name' => $eventName,
                'value' => $value,
                'currency' => 'USD'
            ])
        );
    }

    public function generateDashboardData(
        string $dashboardType,
        \DateTimeInterface $startDate,
        \DateTimeInterface $endDate
    ): array {
        return match ($dashboardType) {
            'executive' => $this->generateExecutiveDashboard($startDate, $endDate),
            'technical' => $this->generateTechnicalDashboard($startDate, $endDate),
            'product' => $this->generateProductDashboard($startDate, $endDate),
            default => throw new \InvalidArgumentException("Unknown dashboard type: {$dashboardType}")
        };
    }

    private function generateExecutiveDashboard(
        \DateTimeInterface $startDate,
        \DateTimeInterface $endDate
    ): array {
        return [
            'revenue' => $this->businessMetrics->getRevenue($startDate, $endDate),
            'active_users' => $this->businessMetrics->getActiveUsers($startDate, $endDate),
            'conversion_rate' => $this->businessMetrics->getConversionRate($startDate, $endDate),
            'user_growth' => $this->businessMetrics->getUserGrowth($startDate, $endDate),
            'retention_rate' => $this->businessMetrics->getRetentionRate($startDate, $endDate),
            'customer_satisfaction' => $this->businessMetrics->getCustomerSatisfaction($startDate, $endDate)
        ];
    }
```

### 2. Configurar Google Analytics 4

**Frontend analytics implementation:**

```typescript
// src/services/analytics.service.ts
interface AnalyticsEvent {
   name: string;
   parameters?: Record<string, any>;
   userId?: string;
}

interface BusinessEvent {
   name: string;
   value?: number;
   currency?: string;
   parameters?: Record<string, any>;
}

export class AnalyticsService {
   private static instance: AnalyticsService;
   private gtag: Function;
   private isInitialized = false;

   private constructor() {
      this.initializeGoogleAnalytics();
   }

   static getInstance(): AnalyticsService {
      if (!AnalyticsService.instance) {
         AnalyticsService.instance = new AnalyticsService();
      }
      return AnalyticsService.instance;
   }

   private initializeGoogleAnalytics(): void {
      const GA_MEASUREMENT_ID = process.env.REACT_APP_GA_MEASUREMENT_ID;

      if (!GA_MEASUREMENT_ID) {
         console.warn("Google Analytics measurement ID not configured");
         return;
      }

      // Load gtag script
      const script = document.createElement("script");
      script.async = true;
      script.src =
         `https://www.googletagmanager.com/gtag/js?id=${GA_MEASUREMENT_ID}`;
      document.head.appendChild(script);

      // Initialize gtag
      window.dataLayer = window.dataLayer || [];
      this.gtag = function () {
         window.dataLayer.push(arguments);
      };

      this.gtag("js", new Date());
      this.gtag("config", GA_MEASUREMENT_ID, {
         send_page_view: false, // Manual page view tracking
      });

      this.isInitialized = true;
   }

   trackPageView(pagePath: string, pageTitle?: string): void {
      if (!this.isInitialized) return;

      this.gtag("event", "page_view", {
         page_path: pagePath,
         page_title: pageTitle || document.title,
      });

      // También enviar al backend
      this.sendToBackend("page_view", {
         page_path: pagePath,
         page_title: pageTitle,
      });
   }

   trackEvent(event: AnalyticsEvent): void {
      if (!this.isInitialized) return;

      this.gtag("event", event.name, {
         ...event.parameters,
         user_id: event.userId,
      });

      this.sendToBackend("custom_event", {
         event_name: event.name,
         ...event.parameters,
      });
   }

   trackBusinessEvent(event: BusinessEvent): void {
      if (!this.isInitialized) return;

      // Track as conversion in GA4
      this.gtag("event", "conversion", {
         event_name: event.name,
         value: event.value || 0,
         currency: event.currency || "USD",
         ...event.parameters,
      });

      this.sendToBackend("business_event", event);
   }

   trackUserAction(action: string, category: string, label?: string): void {
      this.trackEvent({
         name: "user_action",
         parameters: {
            action,
            category,
            label,
         },
      });
   }

   trackFeatureUsage(featureName: string, userId: string): void {
      this.trackEvent({
         name: "feature_usage",
         parameters: {
            feature_name: featureName,
         },
         userId,
      });
   }

   trackError(error: Error, context?: Record<string, any>): void {
      this.trackEvent({
         name: "exception",
         parameters: {
            description: error.message,
            fatal: false,
            ...context,
         },
      });
   }

   private async sendToBackend(eventType: string, data: any): Promise<void> {
      try {
         await fetch("/api/analytics/events", {
            method: "POST",
            headers: {
               "Content-Type": "application/json",
            },
            body: JSON.stringify({
               type: eventType,
               data,
               timestamp: new Date().toISOString(),
            }),
         });
      } catch (error) {
         console.error("Failed to send analytics to backend:", error);
      }
   }
}

// Hook para tracking automático
export const useAnalytics = () => {
   const analytics = AnalyticsService.getInstance();

   const trackPageView = useCallback((path?: string) => {
      const pagePath = path || window.location.pathname;
      analytics.trackPageView(pagePath);
   }, [analytics]);

   const trackAction = useCallback(
      (action: string, category: string, label?: string) => {
         analytics.trackUserAction(action, category, label);
      },
      [analytics],
   );

   const trackFeature = useCallback((featureName: string) => {
      const userId = getCurrentUserId(); // Implementar según tu auth
      analytics.trackFeatureUsage(featureName, userId);
   }, [analytics]);

   return {
      trackPageView,
      trackAction,
      trackFeature,
      trackEvent: analytics.trackEvent.bind(analytics),
      trackBusinessEvent: analytics.trackBusinessEvent.bind(analytics),
   };
};
```

### 3. Dashboard de Métricas en Tiempo Real

**Real-time metrics dashboard:**

```typescript
// src/components/MetricsDashboard.tsx
import React, { useEffect, useState } from "react";
import { Card, Chart, Col, Row, Statistic } from "antd";

interface DashboardMetrics {
   activeUsers: number;
   responseTime: number;
   errorRate: number;
   conversionRate: number;
   revenue: number;
   newSignups: number;
}

interface MetricsTrend {
   timestamp: string;
   value: number;
}

export const MetricsDashboard: React.FC = () => {
   const [metrics, setMetrics] = useState<DashboardMetrics>();
   const [trends, setTrends] = useState<Record<string, MetricsTrend[]>>({});
   const [loading, setLoading] = useState(true);
   const [timeRange, setTimeRange] = useState("24h");

   useEffect(() => {
      const fetchMetrics = async () => {
         try {
            const response = await fetch(
               `/api/analytics/dashboard?range=${timeRange}`,
            );
            const data = await response.json();

            setMetrics(data.current);
            setTrends(data.trends);
         } catch (error) {
            console.error("Failed to fetch metrics:", error);
         } finally {
            setLoading(false);
         }
      };

      fetchMetrics();

      // Actualizar cada 30 segundos
      const interval = setInterval(fetchMetrics, 30000);
      return () => clearInterval(interval);
   }, [timeRange]);

   const getGrowthRate = (metric: string): number => {
      const trend = trends[metric];
      if (!trend || trend.length < 2) return 0;

      const current = trend[trend.length - 1].value;
      const previous = trend[trend.length - 2].value;

      return ((current - previous) / previous) * 100;
   };

   return (
      <div className="metrics-dashboard">
         <Row gutter={[16, 16]}>
            <Col span={4}>
               <Card>
                  <Statistic
                     title="Usuarios Activos"
                     value={metrics?.activeUsers}
                     precision={0}
                     valueStyle={{
                        color: getGrowthRate("activeUsers") > 0
                           ? "#3f8600"
                           : "#cf1322",
                     }}
                     suffix={`${getGrowthRate("activeUsers") > 0 ? "↗" : "↘"} ${
                        Math.abs(getGrowthRate("activeUsers")).toFixed(1)
                     }%`}
                     loading={loading}
                  />
               </Card>
            </Col>

            <Col span={4}>
               <Card>
                  <Statistic
                     title="Tiempo de Respuesta"
                     value={metrics?.responseTime}
                     precision={0}
                     suffix="ms"
                     valueStyle={{
                        color: (metrics?.responseTime || 0) < 500
                           ? "#3f8600"
                           : "#cf1322",
                     }}
                     loading={loading}
                  />
               </Card>
            </Col>

            <Col span={4}>
               <Card>
                  <Statistic
                     title="Tasa de Error"
                     value={metrics?.errorRate}
                     precision={2}
                     suffix="%"
                     valueStyle={{
                        color: (metrics?.errorRate || 0) < 1
                           ? "#3f8600"
                           : "#cf1322",
                     }}
                     loading={loading}
                  />
               </Card>
            </Col>

            <Col span={4}>
               <Card>
                  <Statistic
                     title="Conversión"
                     value={metrics?.conversionRate}
                     precision={2}
                     suffix="%"
                     valueStyle={{ color: "#3f8600" }}
                     loading={loading}
                  />
               </Card>
            </Col>

            <Col span={4}>
               <Card>
                  <Statistic
                     title="Revenue (24h)"
                     value={metrics?.revenue}
                     precision={2}
                     prefix="$"
                     valueStyle={{ color: "#3f8600" }}
                     loading={loading}
                  />
               </Card>
            </Col>

            <Col span={4}>
               <Card>
                  <Statistic
                     title="Nuevos Usuarios"
                     value={metrics?.newSignups}
                     precision={0}
                     valueStyle={{ color: "#3f8600" }}
                     loading={loading}
                  />
               </Card>
            </Col>
         </Row>

         <Row gutter={[16, 16]} style={{ marginTop: 16 }}>
            <Col span={12}>
               <Card title="Usuarios Activos (Tiempo Real)">
                  <Chart
                     data={trends.activeUsers || []}
                     config={{
                        xField: "timestamp",
                        yField: "value",
                        smooth: true,
                        color: "#1890ff",
                     }}
                  />
               </Card>
            </Col>

            <Col span={12}>
               <Card title="Tiempo de Respuesta">
                  <Chart
                     data={trends.responseTime || []}
                     config={{
                        xField: "timestamp",
                        yField: "value",
                        smooth: true,
                        color: "#52c41a",
                     }}
                  />
               </Card>
            </Col>
         </Row>
      </div>
   );
};
```

### 4. Sistema de Alertas Basado en Métricas

**Metrics alerting service:**

```php
<?php
// app/Application/Services/MetricsAlertingService.php
namespace App\Application\Services;

use App\Domain\Metrics\Entities\Alert;
use App\Domain\Metrics\ValueObjects\AlertSeverity;
use App\Domain\Metrics\ValueObjects\AlertCondition;

class MetricsAlertingService
{
    public function __construct(
        private MetricsRepository $metricsRepository,
        private AlertRepository $alertRepository,
        private NotificationService $notificationService
    ) {}

    public function checkAlerts(): void
    {
        $alertRules = $this->alertRepository->findActiveRules();
        
        foreach ($alertRules as $rule) {
            $currentValue = $this->metricsRepository->getCurrentValue($rule->getMetric());
            
            if ($this->shouldTriggerAlert($rule, $currentValue)) {
                $this->triggerAlert($rule, $currentValue);
            }
        }
    }

    public function createAlert(
        string $metricName,
        AlertCondition $condition,
        float $threshold,
        AlertSeverity $severity,
        string $description
    ): Alert {
        $alert = Alert::create(
            metricName: $metricName,
            condition: $condition,
            threshold: $threshold,
            severity: $severity,
            description: $description
        );

        $this->alertRepository->save($alert);
        return $alert;
    }

    public function setupDefaultAlerts(): void
    {
        $defaultAlerts = [
            [
                'metric' => 'response_time_p95',
                'condition' => AlertCondition::GREATER_THAN,
                'threshold' => 2000, // 2 seconds
                'severity' => AlertSeverity::WARNING,
                'description' => '95th percentile response time is too high'
            ],
            [
                'metric' => 'error_rate',
                'condition' => AlertCondition::GREATER_THAN,
                'threshold' => 5.0, // 5%
                'severity' => AlertSeverity::CRITICAL,
                'description' => 'Error rate is above acceptable threshold'
            ],
            [
                'metric' => 'active_users',
                'condition' => AlertCondition::LESS_THAN,
                'threshold' => 100,
                'severity' => AlertSeverity::WARNING,
                'description' => 'Active users dropped below normal levels'
            ],
            [
                'metric' => 'conversion_rate',
                'condition' => AlertCondition::LESS_THAN,
                'threshold' => 2.0, // 2%
                'severity' => AlertSeverity::WARNING,
                'description' => 'Conversion rate dropped significantly'
            ]
        ];

        foreach ($defaultAlerts as $alertConfig) {
            $this->createAlert(
                $alertConfig['metric'],
                $alertConfig['condition'],
                $alertConfig['threshold'],
                $alertConfig['severity'],
                $alertConfig['description']
            );
        }
    }

    private function shouldTriggerAlert(Alert $rule, float $currentValue): bool
    {
        return match ($rule->getCondition()) {
            AlertCondition::GREATER_THAN => $currentValue > $rule->getThreshold(),
            AlertCondition::LESS_THAN => $currentValue < $rule->getThreshold(),
            AlertCondition::EQUALS => abs($currentValue - $rule->getThreshold()) < 0.01,
            AlertCondition::NOT_EQUALS => abs($currentValue - $rule->getThreshold()) >= 0.01,
        };
    }

    private function triggerAlert(Alert $rule, float $currentValue): void
    {
        // Check if already alerted recently to avoid spam
        if ($this->wasAlertedRecently($rule)) {
            return;
        }

        $alert = AlertTrigger::create(
            rule: $rule,
            triggeredValue: $currentValue,
            triggeredAt: new \DateTimeImmutable()
        );

        $this->alertRepository->saveAlertTrigger($alert);

        // Send notification based on severity
        match ($rule->getSeverity()) {
            AlertSeverity::CRITICAL => $this->notificationService->sendCriticalAlert($alert),
            AlertSeverity::WARNING => $this->notificationService->sendWarningAlert($alert),
            AlertSeverity::INFO => $this->notificationService->sendInfoAlert($alert),
        };
    }
}
```

## Tips

### Métricas Efectivas

- **Actionable metrics**: Enfócate en métricas que puedas actuar
- **Leading indicators**: Incluye métricas predictivas, no solo reactivas
- **Business alignment**: Alinea métricas técnicas con objetivos de negocio
- **Baseline establishment**: Establece baselines antes de hacer cambios

### Analytics Implementation

- **Privacy compliance**: Asegúrate de cumplir con GDPR y otras regulaciones
- **Data quality**: Implementa validación y limpieza de datos
- **Real-time vs batch**: Elige el approach correcto según el use case
- **Sampling**: Usa sampling para reducir volumen en alto tráfico

### Dashboard Design

- **Audience-specific**: Diseña dashboards para audiencias específicas
- **Visual hierarchy**: Prioriza métricas más importantes visualmente
- **Context provision**: Proporciona contexto para interpretar métricas
- **Mobile-friendly**: Asegura que dashboards funcionen en móviles

### Alert Management

- **Threshold tuning**: Ajusta umbrales para evitar false positives
- **Alert fatigue**: Evita spam de alertas agrupando y priorizando
- **Escalation rules**: Define reglas claras de escalamiento
- **Recovery notifications**: Notifica cuando los problemas se resuelven

## Ejemplos

### 1. Cohort Analysis Implementation

```typescript
// src/services/cohort-analysis.service.ts
interface CohortData {
   cohortMonth: string;
   userCount: number;
   retentionRates: Record<string, number>; // week1, week2, etc.
}

export class CohortAnalysisService {
   async generateCohortAnalysis(
      startDate: Date,
      endDate: Date,
   ): Promise<CohortData[]> {
      const response = await fetch("/api/analytics/cohort-analysis", {
         method: "POST",
         headers: { "Content-Type": "application/json" },
         body: JSON.stringify({ startDate, endDate }),
      });

      return await response.json();
   }

   async generateRetentionChart(cohortData: CohortData[]): Promise<any> {
      const chartData = cohortData.map((cohort) => ({
         cohort: cohort.cohortMonth,
         week1: cohort.retentionRates.week1 || 0,
         week2: cohort.retentionRates.week2 || 0,
         week4: cohort.retentionRates.week4 || 0,
         week8: cohort.retentionRates.week8 || 0,
         week12: cohort.retentionRates.week12 || 0,
      }));

      return {
         data: chartData,
         config: {
            xField: "cohort",
            yField: "value",
            seriesField: "week",
            smooth: true,
            legend: { position: "top" },
         },
      };
   }
}
```

### 2. A/B Testing Analytics

```php
<?php
// app/Application/Services/ABTestingService.php
namespace App\Application\Services;

class ABTestingService
{
    public function createExperiment(
        string $name,
        string $description,
        array $variants,
        float $trafficAllocation = 1.0
    ): Experiment {
        $experiment = Experiment::create(
            name: $name,
            description: $description,
            variants: $variants,
            trafficAllocation: $trafficAllocation,
            status: ExperimentStatus::DRAFT
        );

        $this->experimentRepository->save($experiment);
        return $experiment;
    }

    public function assignUserToVariant(string $experimentId, string $userId): string
    {
        $experiment = $this->experimentRepository->findById($experimentId);
        
        if (!$experiment->isActive()) {
            return 'control';
        }

        // Use consistent hashing for assignment
        $hash = crc32($userId . $experimentId);
        $bucket = $hash % 100;

        $cumulativePercentage = 0;
        foreach ($experiment->getVariants() as $variant) {
            $cumulativePercentage += $variant['percentage'];
            if ($bucket < $cumulativePercentage) {
                // Record assignment
                $this->trackAssignment($experimentId, $userId, $variant['name']);
                return $variant['name'];
            }
        }

        return 'control';
    }

    public function trackConversion(
        string $experimentId,
        string $userId,
        string $conversionEvent,
        float $value = 0.0
    ): void {
        $assignment = $this->getAssignment($experimentId, $userId);
        
        if ($assignment) {
            $conversion = Conversion::create(
                experimentId: $experimentId,
                userId: $userId,
                variant: $assignment->getVariant(),
                event: $conversionEvent,
                value: $value,
                timestamp: new \DateTimeImmutable()
            );

            $this->conversionRepository->save($conversion);
        }
    }

    public function generateExperimentReport(string $experimentId): array
    {
        $experiment = $this->experimentRepository->findById($experimentId);
        $results = [];

        foreach ($experiment->getVariants() as $variant) {
            $assignments = $this->getAssignmentCount($experimentId, $variant['name']);
            $conversions = $this->getConversionCount($experimentId, $variant['name']);
            
            $conversionRate = $assignments > 0 ? ($conversions / $assignments) * 100 : 0;
            
            $results[$variant['name']] = [
                'assignments' => $assignments,
                'conversions' => $conversions,
                'conversion_rate' => $conversionRate,
                'statistical_significance' => $this->calculateSignificance(
                    $experimentId, 
                    'control', 
                    $variant['name']
                )
            ];
        }

        return [
            'experiment' => $experiment->toArray(),
            'results' => $results,
            'recommendation' => $this->generateRecommendation($results)
        ];
    }
}
```

## Navegación

[⬅️ Documentación y Knowledge Transfer](./documentacion-knowledge-transfer.md) |
[🏠 README Principal](../../README.md) |
