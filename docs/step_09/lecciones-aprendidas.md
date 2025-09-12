# Lecciones Aprendidas

## ¿Qué es?

Las lecciones aprendidas son un registro estructurado de conocimientos,
experiencias, insights y aprendizajes obtenidos durante el desarrollo del
proyecto. Incluye tanto los éxitos como los fracasos, las decisiones acertadas y
los errores cometidos, con el objetivo de mejorar futuros proyectos y procesos.

## ¿Por qué es importante?

- **Mejora continua**: Evita repetir errores y optimiza procesos exitosos
- **Transferencia de conocimiento**: Comparte experiencias valiosas con futuros
  equipos
- **Toma de decisiones**: Informa mejores decisiones basadas en experiencia real
- **Reducción de riesgos**: Identifica y mitiga riesgos conocidos en futuros
  proyectos
- **Optimización de recursos**: Maximiza la eficiencia basada en experiencias
  pasadas
- **Desarrollo profesional**: Fomenta el crecimiento y aprendizaje del equipo
- **Cultura organizacional**: Crea una cultura de aprendizaje y transparencia
- **ROI del conocimiento**: Capitaliza la inversión en experiencia adquirida

## ¿Qué debe incluir?

### Lecciones Técnicas

- **Arquitectura y Diseño**: Decisiones de arquitectura que funcionaron o
  fallaron
- **Stack Tecnológico**: Efectividad de las tecnologías seleccionadas
- **Patrones y Prácticas**: Patrones de código que resultaron útiles o
  problemáticos
- **Performance**: Optimizaciones que generaron mayor impacto

### Lecciones de Proceso

- **Metodología**: Efectividad de Scrumban y adaptaciones realizadas
- **Comunicación**: Canales y frecuencias de comunicación más efectivos
- **Planificación**: Precisión de estimaciones y factores de error
- **Quality Assurance**: Procesos de testing y control de calidad más efectivos

### Lecciones de Equipo

- **Colaboración**: Dinámicas de equipo que funcionaron mejor
- **Distribución de trabajo**: Estrategias efectivas de asignación de tareas
- **Resolución de conflictos**: Métodos exitosos para resolver desacuerdos
- **Mentoring**: Enfoques efectivos para desarrollo de habilidades

### Lecciones de Negocio

- **Gestión de stakeholders**: Estrategias efectivas de comunicación con cliente
- **Gestión de cambios**: Manejo efectivo de cambios en requerimientos
- **Entrega de valor**: Enfoques que maximizaron el valor entregado
- **Gestión de expectativas**: Métodos para alinear expectativas efectivamente

### Lecciones de Herramientas

- **Efectividad**: Qué herramientas cumplieron mejor su propósito
- **Integración**: Combinaciones de herramientas que funcionaron bien
- **Limitaciones**: Restricciones encontradas y workarounds aplicados
- **Alternativas**: Herramientas que podrían ser mejores opciones

## ¿Qué debo hacer?

### 1. Configurar sistema de captura de lecciones

```bash
# Crear estructura para lecciones aprendidas
mkdir -p lessons-learned/{sessions,individual,templates,analysis}

# Template para captura de lecciones individuales
cat > lessons-learned/templates/individual-lesson-template.md << 'EOF'
# Lección Aprendida Individual

## Información Básica
- **Contribuidor**: [Nombre]
- **Fecha**: [Fecha]
- **Área**: [Técnica/Proceso/Equipo/Negocio/Herramientas]
- **Prioridad**: [Alta/Media/Baja]

## Contexto
**Situación**: Describe la situación o problema que enfrentaste

**Decisión tomada**: Explica qué decisión se tomó o qué acción se realizó

## Resultado
**Qué pasó**: Describe el resultado de la decisión/acción

**Impacto**: Explica el impacto positivo o negativo

## Lección
**Qué aprendiste**: La lección clave que extraes de esta experiencia

**Por qué es importante**: Explica por qué esta lección es valiosa

## Aplicación Futura
**Qué harías diferente**: Si te encontraras en la misma situación otra vez

**Recomendación**: Consejo específico para futuros proyectos

## Evidencia
**Métricas/datos**: Cualquier dato que respalde la lección

**Referencias**: Enlaces a código, documentos, conversaciones, etc.

## Tags
[Agregar tags relevantes: #arquitectura #performance #comunicación #testing etc.]
EOF

# Template para sesiones de lecciones aprendidas
cat > lessons-learned/templates/session-template.md << 'EOF'
# Sesión de Lecciones Aprendidas

## Información de la Sesión
- **Fecha**: [Fecha]
- **Facilitador**: [Nombre]
- **Participantes**: [Lista de participantes]
- **Duración**: [Duración]
- **Tema/Sprint**: [Enfoque específico]

## Agenda
1. **Revisión del periodo** (10 min)
2. **Identificación de lecciones** (40 min)
3. **Priorización y categorización** (20 min)
4. **Definición de acciones** (15 min)
5. **Documentación y próximos pasos** (5 min)

## Lecciones Identificadas

### 🎯 Lo que funcionó bien
- [Lección 1]
- [Lección 2]
- [Lección 3]

### ⚠️ Lo que no funcionó
- [Lección 1]
- [Lección 2]
- [Lección 3]

### 💡 Insights y descubrimientos
- [Insight 1]
- [Insight 2]
- [Insight 3]

### 🔄 Cambios a implementar
- [Cambio 1] - Responsable: [Nombre] - Fecha: [Fecha]
- [Cambio 2] - Responsable: [Nombre] - Fecha: [Fecha]

## Lecciones Priorizadas
1. **[Lección más importante]** - Impacto: Alto, Esfuerzo de aplicación: Bajo
2. **[Segunda lección]** - Impacto: Alto, Esfuerzo de aplicación: Medio
3. **[Tercera lección]** - Impacto: Medio, Esfuerzo de aplicación: Bajo

## Acciones Definidas
| Acción | Responsable | Fecha Límite | Estado |
|--------|-------------|--------------|--------|
| [Acción específica] | [Nombre] | [Fecha] | 🔄 Pendiente |

## Próximos Pasos
- [Próximo paso 1]
- [Próximo paso 2]
- [Próximo paso 3]
EOF
```

### 2. Implementar proceso de captura estructurado

```typescript
// lessons-learned/LessonsLearnedManager.ts

interface Lesson {
   id: string;
   contributor: string;
   date: Date;
   category: "technical" | "process" | "team" | "business" | "tools";
   priority: "high" | "medium" | "low";
   title: string;
   context: {
      situation: string;
      decision: string;
   };
   outcome: {
      result: string;
      impact: string;
   };
   lesson: {
      learning: string;
      importance: string;
   };
   futureApplication: {
      whatToDoDifferent: string;
      recommendation: string;
   };
   evidence?: {
      metrics?: string;
      references?: string[];
   };
   tags: string[];
   validated: boolean;
   appliedInProjects?: string[];
}

interface LessonsSession {
   id: string;
   date: Date;
   facilitator: string;
   participants: string[];
   focus: string;
   lessonsIdentified: {
      whatWorked: string[];
      whatDidntWork: string[];
      insights: string[];
      changes: Array<{
         change: string;
         responsible: string;
         deadline: Date;
      }>;
   };
   prioritizedLessons: Array<{
      lesson: string;
      impact: "high" | "medium" | "low";
      effort: "high" | "medium" | "low";
      score: number;
   }>;
   actionItems: Array<{
      action: string;
      responsible: string;
      deadline: Date;
      status: "pending" | "in_progress" | "completed";
   }>;
}

export class LessonsLearnedManager {
   private lessons: Lesson[] = [];
   private sessions: LessonsSession[] = [];

   public captureLesson(
      lessonData: Omit<Lesson, "id" | "validated" | "appliedInProjects">,
   ): string {
      const lesson: Lesson = {
         ...lessonData,
         id: this.generateId(),
         validated: false,
         appliedInProjects: [],
      };

      this.lessons.push(lesson);
      this.persistLesson(lesson);

      return lesson.id;
   }

   public conductSession(sessionData: Omit<LessonsSession, "id">): string {
      const session: LessonsSession = {
         ...sessionData,
         id: this.generateId(),
      };

      this.sessions.push(session);
      this.persistSession(session);

      // Convertir insights de la sesión en lecciones individuales
      this.convertSessionToLessons(session);

      return session.id;
   }

   public analyzePatterns(): LessonsAnalysis {
      return {
         frequentCategories: this.getMostFrequentCategories(),
         commonTags: this.getMostCommonTags(),
         highImpactLessons: this.getHighImpactLessons(),
         unvalidatedLessons: this.getUnvalidatedLessons(),
         trendAnalysis: this.analyzeTrends(),
         recommendations: this.generateRecommendations(),
      };
   }

   public generateKnowledgeBase(): KnowledgeBase {
      const categorizedLessons = this.categorizeLessons();

      return {
         technicalLessons: {
            architecture: categorizedLessons.technical.filter((l) =>
               l.tags.includes("architecture") || l.tags.includes("design")
            ),
            performance: categorizedLessons.technical.filter((l) =>
               l.tags.includes("performance") || l.tags.includes("optimization")
            ),
            security: categorizedLessons.technical.filter((l) =>
               l.tags.includes("security")
            ),
            testing: categorizedLessons.technical.filter((l) =>
               l.tags.includes("testing") || l.tags.includes("qa")
            ),
         },
         processLessons: {
            methodology: categorizedLessons.process.filter((l) =>
               l.tags.includes("scrumban") || l.tags.includes("agile")
            ),
            communication: categorizedLessons.process.filter((l) =>
               l.tags.includes("communication")
            ),
            planning: categorizedLessons.process.filter((l) =>
               l.tags.includes("planning") || l.tags.includes("estimation")
            ),
         },
         teamLessons: categorizedLessons.team,
         businessLessons: categorizedLessons.business,
         toolsLessons: categorizedLessons.tools,
         bestPractices: this.extractBestPractices(),
         antiPatterns: this.extractAntiPatterns(),
      };
   }

   private convertSessionToLessons(session: LessonsSession): void {
      // Convertir lo que funcionó bien en lecciones
      session.lessonsIdentified.whatWorked.forEach((item) => {
         this.captureLesson({
            contributor: session.facilitator,
            date: session.date,
            category: this.inferCategory(item),
            priority: "medium",
            title: `Práctica exitosa: ${item}`,
            context: {
               situation: `Durante ${session.focus}`,
               decision: item,
            },
            outcome: {
               result: "Resultado positivo en el proyecto",
               impact: "Contribuyó al éxito del proyecto",
            },
            lesson: {
               learning: `Esta práctica es efectiva: ${item}`,
               importance: "Debe replicarse en futuros proyectos",
            },
            futureApplication: {
               whatToDoDifferent: "Continuar aplicando esta práctica",
               recommendation:
                  `Implementar ${item} desde el inicio del proyecto`,
            },
            tags: this.inferTags(item),
         });
      });

      // Convertir lo que no funcionó en lecciones
      session.lessonsIdentified.whatDidntWork.forEach((item) => {
         this.captureLesson({
            contributor: session.facilitator,
            date: session.date,
            category: this.inferCategory(item),
            priority: "high",
            title: `Problema identificado: ${item}`,
            context: {
               situation: `Durante ${session.focus}`,
               decision: "Se implementó una práctica que no funcionó",
            },
            outcome: {
               result: "Resultado negativo o subóptimo",
               impact: item,
            },
            lesson: {
               learning: `Esta práctica no es efectiva: ${item}`,
               importance: "Debe evitarse en futuros proyectos",
            },
            futureApplication: {
               whatToDoDifferent:
                  "Evitar esta práctica o encontrar alternativas",
               recommendation:
                  `No implementar ${item} o buscar mejores alternativas`,
            },
            tags: this.inferTags(item),
         });
      });
   }

   public exportLessonsToDocumentation(): string {
      const knowledgeBase = this.generateKnowledgeBase();

      let documentation = "# Base de Conocimiento - Lecciones Aprendidas\n\n";

      documentation += "## Resumen Ejecutivo\n\n";
      documentation +=
         `- **Total de lecciones capturadas**: ${this.lessons.length}\n`;
      documentation += `- **Sesiones realizadas**: ${this.sessions.length}\n`;
      documentation += `- **Categorías principales**: ${
         this.getMostFrequentCategories().join(", ")
      }\n\n`;

      documentation += "## Lecciones Técnicas\n\n";
      documentation += "### Arquitectura y Diseño\n\n";
      knowledgeBase.technicalLessons.architecture.forEach((lesson) => {
         documentation += `#### ${lesson.title}\n`;
         documentation += `**Contexto**: ${lesson.context.situation}\n\n`;
         documentation += `**Lección**: ${lesson.lesson.learning}\n\n`;
         documentation +=
            `**Recomendación**: ${lesson.futureApplication.recommendation}\n\n`;
         documentation += `**Tags**: ${lesson.tags.join(", ")}\n\n`;
         documentation += "---\n\n";
      });

      // ... continuar con otras categorías

      return documentation;
   }

   private generateRecommendations(): string[] {
      const recommendations: string[] = [];
      const analysis = this.analyzePatterns();

      // Recomendaciones basadas en patrones frecuentes
      if (analysis.frequentCategories.includes("technical")) {
         recommendations.push(
            "Crear sesiones técnicas específicas para capturar más lecciones de arquitectura",
         );
      }

      if (analysis.unvalidatedLessons.length > 5) {
         recommendations.push(
            "Implementar proceso de validación para lecciones no verificadas",
         );
      }

      return recommendations;
   }
}
```

### 3. Sistema de análisis y patrones

```php
<?php
// lessons-learned/analysis/LessonsAnalyzer.php

class LessonsAnalyzer
{
    private array $lessons = [];
    private array $sessions = [];

    public function __construct(array $lessons, array $sessions)
    {
        $this->lessons = $lessons;
        $this->sessions = $sessions;
    }

    public function generateInsightsReport(): array
    {
        return [
            'pattern_analysis' => $this->analyzePatterns(),
            'trend_analysis' => $this->analyzeTrends(),
            'impact_analysis' => $this->analyzeImpact(),
            'application_analysis' => $this->analyzeApplication(),
            'knowledge_gaps' => $this->identifyKnowledgeGaps(),
            'recommendations' => $this->generateStrategicRecommendations()
        ];
    }

    private function analyzePatterns(): array
    {
        // Analizar patrones en las lecciones
        $patterns = [];
        
        // Patrones por categoría
        $categoryPatterns = [];
        foreach ($this->lessons as $lesson) {
            $category = $lesson['category'];
            if (!isset($categoryPatterns[$category])) {
                $categoryPatterns[$category] = [];
            }
            $categoryPatterns[$category][] = $lesson;
        }
        
        foreach ($categoryPatterns as $category => $lessons) {
            $patterns[$category] = [
                'count' => count($lessons),
                'common_tags' => $this->getCommonTags($lessons),
                'frequent_problems' => $this->getFrequentProblems($lessons),
                'success_factors' => $this->getSuccessFactors($lessons)
            ];
        }
        
        // Patrones temporales
        $patterns['temporal'] = $this->analyzeTemporalPatterns();
        
        // Patrones de aplicación
        $patterns['application'] = $this->analyzeApplicationPatterns();
        
        return $patterns;
    }

    private function analyzeTrends(): array
    {
        $trends = [];
        
        // Tendencias por período de tiempo
        $monthlyLessons = $this->groupLessonsByMonth();
        $trends['monthly_volume'] = $monthlyLessons;
        
        // Tendencias por categoría a lo largo del tiempo
        $trends['category_evolution'] = $this->analyzeCategoryEvolution();
        
        // Tendencias de calidad (validación y aplicación)
        $trends['quality_trends'] = $this->analyzeQualityTrends();
        
        return $trends;
    }

    private function analyzeImpact(): array
    {
        $impact = [];
        
        // Lecciones de mayor impacto
        $highImpactLessons = array_filter($this->lessons, function($lesson) {
            return $lesson['priority'] === 'high' && 
                   !empty($lesson['appliedInProjects']);
        });
        
        $impact['high_impact_lessons'] = $highImpactLessons;
        
        // Cálculo de ROI de lecciones aplicadas
        $impact['roi_analysis'] = $this->calculateLessonsROI();
        
        // Impacto por categoría
        $impact['category_impact'] = $this->analyzeCategoryImpact();
        
        return $impact;
    }

    private function identifyKnowledgeGaps(): array
    {
        $gaps = [];
        
        // Áreas con pocas lecciones pero alta importancia
        $categoryDistribution = $this->getCategoryDistribution();
        $expectedDistribution = [
            'technical' => 0.4,
            'process' => 0.25,
            'team' => 0.15,
            'business' => 0.1,
            'tools' => 0.1
        ];
        
        foreach ($expectedDistribution as $category => $expectedRatio) {
            $actualRatio = ($categoryDistribution[$category] ?? 0) / count($this->lessons);
            if ($actualRatio < $expectedRatio * 0.7) { // 30% menos de lo esperado
                $gaps[] = [
                    'category' => $category,
                    'gap_severity' => ($expectedRatio - $actualRatio) / $expectedRatio,
                    'recommendation' => "Enfocar más esfuerzo en capturar lecciones de $category"
                ];
            }
        }
        
        // Lecciones sin validar por mucho tiempo
        $unvalidatedLessons = array_filter($this->lessons, function($lesson) {
            return !$lesson['validated'] && 
                   (time() - strtotime($lesson['date'])) > (30 * 24 * 60 * 60); // 30 días
        });
        
        if (count($unvalidatedLessons) > 0) {
            $gaps[] = [
                'type' => 'validation_gap',
                'count' => count($unvalidatedLessons),
                'recommendation' => 'Implementar proceso de validación más efectivo'
            ];
        }
        
        return $gaps;
    }

    private function generateStrategicRecommendations(): array
    {
        $recommendations = [];
        
        $patterns = $this->analyzePatterns();
        $trends = $this->analyzeTrends();
        $gaps = $this->identifyKnowledgeGaps();
        
        // Recomendaciones basadas en patrones
        if ($patterns['technical']['count'] > $patterns['process']['count'] * 2) {
            $recommendations[] = [
                'type' => 'focus_shift',
                'priority' => 'medium',
                'description' => 'Balancear captura de lecciones técnicas con lecciones de proceso',
                'action' => 'Programar sesiones específicas para lecciones de proceso'
            ];
        }
        
        // Recomendaciones basadas en tendencias
        if ($trends['quality_trends']['validation_rate'] < 0.7) {
            $recommendations[] = [
                'type' => 'process_improvement',
                'priority' => 'high',
                'description' => 'Mejorar proceso de validación de lecciones',
                'action' => 'Implementar reviews regulares de lecciones no validadas'
            ];
        }
        
        // Recomendaciones basadas en gaps
        foreach ($gaps as $gap) {
            if ($gap['gap_severity'] ?? 0 > 0.5) {
                $recommendations[] = [
                    'type' => 'knowledge_gap',
                    'priority' => 'high',
                    'description' => $gap['recommendation'],
                    'action' => "Organizar sesiones específicas para {$gap['category']}"
                ];
            }
        }
        
        return $recommendations;
    }

    public function exportAnalysisReport(): string
    {
        $analysis = $this->generateInsightsReport();
        
        $report = "# Análisis de Lecciones Aprendidas\n\n";
        
        $report .= "## Resumen Ejecutivo\n\n";
        $report .= "- **Total de lecciones**: " . count($this->lessons) . "\n";
        $report .= "- **Sesiones realizadas**: " . count($this->sessions) . "\n";
        $report .= "- **Lecciones validadas**: " . $this->getValidatedCount() . "\n";
        $report .= "- **Lecciones aplicadas**: " . $this->getAppliedCount() . "\n\n";
        
        $report .= "## Análisis de Patrones\n\n";
        foreach ($analysis['pattern_analysis'] as $category => $pattern) {
            if ($category !== 'temporal' && $category !== 'application') {
                $report .= "### " . ucfirst($category) . "\n";
                $report .= "- **Cantidad**: {$pattern['count']}\n";
                $report .= "- **Tags comunes**: " . implode(', ', $pattern['common_tags']) . "\n";
                $report .= "- **Problemas frecuentes**: " . implode(', ', $pattern['frequent_problems']) . "\n\n";
            }
        }
        
        $report .= "## Recomendaciones Estratégicas\n\n";
        foreach ($analysis['recommendations'] as $rec) {
            $priority_emoji = $rec['priority'] === 'high' ? '🔴' : ($rec['priority'] === 'medium' ? '🟡' : '🟢');
            $report .= "{$priority_emoji} **{$rec['type']}**: {$rec['description']}\n";
            $report .= "   - *Acción*: {$rec['action']}\n\n";
        }
        
        return $report;
    }
}

// Script de ejecución
$lessonsData = json_decode(file_get_contents('lessons-learned/data/lessons.json'), true);
$sessionsData = json_decode(file_get_contents('lessons-learned/data/sessions.json'), true);

$analyzer = new LessonsAnalyzer($lessonsData, $sessionsData);
$analysisReport = $analyzer->exportAnalysisReport();

file_put_contents('lessons-learned/reports/analysis-report.md', $analysisReport);
echo "📊 Reporte de análisis de lecciones generado\n";
?>
```

### 4. Dashboard de lecciones aprendidas

```typescript
// lessons-learned/dashboard/LessonsDashboard.tsx
import React, { useEffect, useState } from "react";
import {
   Bar,
   BarChart,
   CartesianGrid,
   Cell,
   Line,
   LineChart,
   Pie,
   PieChart,
   ResponsiveContainer,
   Tooltip,
   Treemap,
   XAxis,
   YAxis,
} from "recharts";

interface LessonsDashboardProps {
   lessons: Lesson[];
   sessions: LessonsSession[];
   analysis: LessonsAnalysis;
}

export const LessonsDashboard: React.FC<LessonsDashboardProps> = ({
   lessons,
   sessions,
   analysis,
}) => {
   const [activeTab, setActiveTab] = useState("overview");
   const [selectedCategory, setSelectedCategory] = useState<string | null>(
      null,
   );

   const categoryData = Object.entries(analysis.frequentCategories).map((
      [category, count],
   ) => ({
      category: category.charAt(0).toUpperCase() + category.slice(1),
      count,
      percentage: Math.round((count / lessons.length) * 100),
   }));

   const priorityData = lessons.reduce((acc, lesson) => {
      acc[lesson.priority] = (acc[lesson.priority] || 0) + 1;
      return acc;
   }, {} as Record<string, number>);

   const validationData = [
      {
         name: "Validadas",
         value: lessons.filter((l) => l.validated).length,
         color: "#10B981",
      },
      {
         name: "No Validadas",
         value: lessons.filter((l) => !l.validated).length,
         color: "#F59E0B",
      },
   ];

   return (
      <div className="lessons-dashboard">
         <header className="dashboard-header">
            <h1>Dashboard de Lecciones Aprendidas</h1>
            <div className="stats-summary">
               <div className="stat-card">
                  <h3>Total Lecciones</h3>
                  <div className="stat-value">{lessons.length}</div>
               </div>
               <div className="stat-card">
                  <h3>Sesiones Realizadas</h3>
                  <div className="stat-value">{sessions.length}</div>
               </div>
               <div className="stat-card">
                  <h3>Lecciones Aplicadas</h3>
                  <div className="stat-value">
                     {lessons.filter((l) =>
                        l.appliedInProjects && l.appliedInProjects.length > 0
                     ).length}
                  </div>
               </div>
               <div className="stat-card">
                  <h3>Tasa de Validación</h3>
                  <div className="stat-value">
                     {Math.round(
                        (lessons.filter((l) => l.validated).length /
                           lessons.length) * 100,
                     )}%
                  </div>
               </div>
            </div>
         </header>

         <nav className="dashboard-tabs">
            <button
               className={activeTab === "overview" ? "active" : ""}
               onClick={() => setActiveTab("overview")}
            >
               Resumen
            </button>
            <button
               className={activeTab === "categories" ? "active" : ""}
               onClick={() => setActiveTab("categories")}
            >
               Por Categoría
            </button>
            <button
               className={activeTab === "trends" ? "active" : ""}
               onClick={() => setActiveTab("trends")}
            >
               Tendencias
            </button>
            <button
               className={activeTab === "knowledge" ? "active" : ""}
               onClick={() => setActiveTab("knowledge")}
            >
               Base de Conocimiento
            </button>
            <button
               className={activeTab === "recommendations" ? "active" : ""}
               onClick={() => setActiveTab("recommendations")}
            >
               Recomendaciones
            </button>
         </nav>

         <main className="dashboard-content">
            {activeTab === "overview" && (
               <OverviewSection
                  categoryData={categoryData}
                  priorityData={priorityData}
                  validationData={validationData}
                  lessons={lessons}
               />
            )}
            {activeTab === "categories" && (
               <CategoriesSection
                  lessons={lessons}
                  selectedCategory={selectedCategory}
                  onCategorySelect={setSelectedCategory}
               />
            )}
            {activeTab === "trends" && (
               <TrendsSection
                  lessons={lessons}
                  sessions={sessions}
                  analysis={analysis}
               />
            )}
            {activeTab === "knowledge" && (
               <KnowledgeBaseSection lessons={lessons} />
            )}
            {activeTab === "recommendations" && (
               <RecommendationsSection analysis={analysis} />
            )}
         </main>
      </div>
   );
};

const OverviewSection: React.FC<{
   categoryData: any[];
   priorityData: Record<string, number>;
   validationData: any[];
   lessons: Lesson[];
}> = ({ categoryData, priorityData, validationData, lessons }) => {
   const recentLessons = lessons
      .sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
      .slice(0, 5);

   const highImpactLessons = lessons
      .filter((l) => l.priority === "high")
      .slice(0, 3);

   return (
      <div className="overview-section">
         <div className="charts-grid">
            <div className="chart-container">
               <h3>Distribución por Categoría</h3>
               <ResponsiveContainer width="100%" height={300}>
                  <BarChart data={categoryData}>
                     <CartesianGrid strokeDasharray="3 3" />
                     <XAxis dataKey="category" />
                     <YAxis />
                     <Tooltip />
                     <Bar dataKey="count" fill="#8884d8" />
                  </BarChart>
               </ResponsiveContainer>
            </div>

            <div className="chart-container">
               <h3>Estado de Validación</h3>
               <ResponsiveContainer width="100%" height={300}>
                  <PieChart>
                     <Pie
                        data={validationData}
                        cx="50%"
                        cy="50%"
                        labelLine={false}
                        label={({ name, percent }) =>
                           `${name} ${(percent * 100).toFixed(0)}%`}
                        outerRadius={80}
                        fill="#8884d8"
                        dataKey="value"
                     >
                        {validationData.map((entry, index) => (
                           <Cell key={`cell-${index}`} fill={entry.color} />
                        ))}
                     </Pie>
                     <Tooltip />
                  </PieChart>
               </ResponsiveContainer>
            </div>
         </div>

         <div className="lessons-lists">
            <div className="recent-lessons">
               <h3>Lecciones Recientes</h3>
               <div className="lessons-list">
                  {recentLessons.map((lesson) => (
                     <div key={lesson.id} className="lesson-item">
                        <div className="lesson-title">{lesson.title}</div>
                        <div className="lesson-meta">
                           <span className={`category ${lesson.category}`}>
                              {lesson.category}
                           </span>
                           <span className={`priority ${lesson.priority}`}>
                              {lesson.priority}
                           </span>
                           <span className="date">
                              {new Date(lesson.date).toLocaleDateString()}
                           </span>
                        </div>
                        <div className="lesson-summary">
                           {lesson.lesson.learning.substring(0, 120)}...
                        </div>
                     </div>
                  ))}
               </div>
            </div>

            <div className="high-impact-lessons">
               <h3>Lecciones de Alto Impacto</h3>
               <div className="lessons-list">
                  {highImpactLessons.map((lesson) => (
                     <div key={lesson.id} className="lesson-item high-impact">
                        <div className="lesson-title">{lesson.title}</div>
                        <div className="lesson-application">
                           Aplicada en: {lesson.appliedInProjects?.length || 0}
                           {" "}
                           proyectos
                        </div>
                        <div className="lesson-summary">
                           {lesson.futureApplication.recommendation}
                        </div>
                     </div>
                  ))}
               </div>
            </div>
         </div>
      </div>
   );
};

const KnowledgeBaseSection: React.FC<{ lessons: Lesson[] }> = ({ lessons }) => {
   const [searchTerm, setSearchTerm] = useState("");
   const [selectedTag, setSelectedTag] = useState<string | null>(null);

   const allTags = Array.from(
      new Set(lessons.flatMap((lesson) => lesson.tags)),
   ).sort();

   const filteredLessons = lessons.filter((lesson) => {
      const matchesSearch = searchTerm === "" ||
         lesson.title.toLowerCase().includes(searchTerm.toLowerCase()) ||
         lesson.lesson.learning.toLowerCase().includes(
            searchTerm.toLowerCase(),
         );

      const matchesTag = selectedTag === null ||
         lesson.tags.includes(selectedTag);

      return matchesSearch && matchesTag;
   });

   const bestPractices = lessons.filter((l) =>
      l.tags.includes("best-practice") ||
      l.outcome.impact.includes("positiv")
   );

   const antiPatterns = lessons.filter((l) =>
      l.tags.includes("anti-pattern") ||
      l.outcome.impact.includes("negativ")
   );

   return (
      <div className="knowledge-base-section">
         <div className="search-controls">
            <input
               type="text"
               placeholder="Buscar lecciones..."
               value={searchTerm}
               onChange={(e) => setSearchTerm(e.target.value)}
               className="search-input"
            />

            <select
               value={selectedTag || ""}
               onChange={(e) => setSelectedTag(e.target.value || null)}
               className="tag-filter"
            >
               <option value="">Todos los tags</option>
               {allTags.map((tag) => (
                  <option key={tag} value={tag}>{tag}</option>
               ))}
            </select>
         </div>

         <div className="knowledge-sections">
            <div className="best-practices">
               <h3>🌟 Mejores Prácticas</h3>
               <div className="practices-grid">
                  {bestPractices.slice(0, 6).map((lesson) => (
                     <div key={lesson.id} className="practice-card">
                        <h4>{lesson.title}</h4>
                        <p>{lesson.lesson.learning}</p>
                        <div className="recommendation">
                           <strong>Recomendación:</strong>{" "}
                           {lesson.futureApplication.recommendation}
                        </div>
                        <div className="tags">
                           {lesson.tags.map((tag) => (
                              <span key={tag} className="tag">{tag}</span>
                           ))}
                        </div>
                     </div>
                  ))}
               </div>
            </div>

            <div className="anti-patterns">
               <h3>⚠️ Anti-Patrones</h3>
               <div className="anti-patterns-grid">
                  {antiPatterns.slice(0, 6).map((lesson) => (
                     <div key={lesson.id} className="anti-pattern-card">
                        <h4>{lesson.title}</h4>
                        <p>{lesson.lesson.learning}</p>
                        <div className="what-to-avoid">
                           <strong>Qué evitar:</strong>{" "}
                           {lesson.context.decision}
                        </div>
                        <div className="alternative">
                           <strong>Alternativa:</strong>{" "}
                           {lesson.futureApplication.whatToDoDifferent}
                        </div>
                     </div>
                  ))}
               </div>
            </div>

            <div className="filtered-lessons">
               <h3>Todas las Lecciones ({filteredLessons.length})</h3>
               <div className="lessons-grid">
                  {filteredLessons.map((lesson) => (
                     <div key={lesson.id} className="lesson-card">
                        <div className="lesson-header">
                           <h4>{lesson.title}</h4>
                           <span className={`priority ${lesson.priority}`}>
                              {lesson.priority}
                           </span>
                        </div>
                        <div className="lesson-content">
                           <p>
                              <strong>Contexto:</strong>{" "}
                              {lesson.context.situation}
                           </p>
                           <p>
                              <strong>Lección:</strong> {lesson.lesson.learning}
                           </p>
                           <p>
                              <strong>Aplicación:</strong>{" "}
                              {lesson.futureApplication.recommendation}
                           </p>
                        </div>
                        <div className="lesson-footer">
                           <div className="tags">
                              {lesson.tags.map((tag) => (
                                 <span key={tag} className="tag">{tag}</span>
                              ))}
                           </div>
                           <div className="meta">
                              <span className="contributor">
                                 {lesson.contributor}
                              </span>
                              <span className="date">
                                 {new Date(lesson.date).toLocaleDateString()}
                              </span>
                           </div>
                        </div>
                     </div>
                  ))}
               </div>
            </div>
         </div>
      </div>
   );
};
```

### 5. Integración con futuros proyectos

```bash
# scripts/apply-lessons.sh
#!/bin/bash

echo "🎓 Aplicando lecciones aprendidas a nuevo proyecto..."

PROJECT_NAME="$1"
PROJECT_TYPE="$2" # web, mobile, fullstack

if [[ -z "$PROJECT_NAME" || -z "$PROJECT_TYPE" ]]; then
    echo "❌ Uso: $0 <project-name> <project-type>"
    exit 1
fi

# Crear directorio de proyecto con lecciones aplicadas
mkdir -p "projects/$PROJECT_NAME"
cd "projects/$PROJECT_NAME"

# Aplicar lecciones relevantes
echo "📋 Aplicando lecciones relevantes para proyecto tipo: $PROJECT_TYPE"

# Lecciones de arquitectura
if [[ -f "../../lessons-learned/knowledge-base/architecture-lessons.md" ]]; then
    echo "🏗️ Aplicando lecciones de arquitectura..."
    
    # Crear estructura basada en lecciones aprendidas
    mkdir -p {frontend/src/{components,pages,hooks,services,store,types,utils},backend/app/{Domain,Application,Infrastructure}}
    
    # Copiar templates mejorados basados en lecciones
    cp ../../lessons-learned/templates/improved-architecture/* ./
fi

# Lecciones de herramientas
echo "🔧 Configurando herramientas basadas en lecciones aprendidas..."

# ESLint config mejorado
cat > frontend/.eslintrc.js << 'EOF'
// Configuración basada en lecciones aprendidas
module.exports = {
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended',
    // Reglas adicionales basadas en problemas encontrados
    'plugin:react-hooks/recommended'
  ],
  rules: {
    // Reglas específicas que evitan problemas identificados en lecciones
    'no-console': 'warn',
    '@typescript-eslint/explicit-function-return-type': 'error',
    'react-hooks/exhaustive-deps': 'error'
  }
};
EOF

# Configuración de testing mejorada
cat > frontend/jest.config.js << 'EOF'
// Configuración de testing basada en lecciones aprendidas
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts'],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  // Configuraciones adicionales basadas en problemas encontrados
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1'
  }
};
EOF

# Crear checklist del proyecto basado en lecciones
cat > PROJECT_CHECKLIST.md << 'EOF'
# Checklist del Proyecto - Basado en Lecciones Aprendidas

## Pre-desarrollo
- [ ] Revisar lecciones aprendidas relevantes
- [ ] Configurar herramientas según mejores prácticas identificadas
- [ ] Establecer arquitectura basada en patrones exitosos
- [ ] Definir proceso de comunicación efectivo

## Durante el desarrollo
- [ ] Aplicar prácticas de código identificadas como exitosas
- [ ] Evitar anti-patrones documentados
- [ ] Realizar reviews regulares basadas en lecciones de calidad
- [ ] Documentar nuevas lecciones conforme aparezcan

## Pre-deploy
- [ ] Verificar cumplimiento con estándares aprendidos
- [ ] Validar performance según benchmarks establecidos
- [ ] Confirmar que documentación está actualizada
- [ ] Revisar que tests cumplen con cobertura aprendida

## Post-deploy
- [ ] Monitorear métricas basadas en lecciones de production
- [ ] Capturar nuevas lecciones del deployment
- [ ] Actualizar base de conocimiento con experiencias
- [ ] Planificar retrospectiva para capturar lecciones
EOF

echo "✅ Proyecto $PROJECT_NAME configurado con lecciones aprendidas aplicadas"
echo "📄 Revisar PROJECT_CHECKLIST.md para seguir mejores prácticas identificadas"
```

## Tips

### Captura Efectiva

- **Regularidad**: Captura lecciones de forma regular, no solo al final del
  proyecto
- **Diversidad**: Busca lecciones en todas las áreas: técnica, proceso, equipo,
  negocio
- **Especificidad**: Sé específico sobre contexto, decisiones y resultados
- **Evidencia**: Incluye datos y evidencia que respalde las lecciones

### Análisis y Síntesis

- **Patrones**: Busca patrones recurrentes en múltiples lecciones
- **Categorización**: Organiza lecciones de manera que sean fáciles de encontrar
- **Priorización**: Enfócate en lecciones de alto impacto y aplicabilidad
- **Validación**: Valida lecciones aplicándolas en situaciones reales

### Aplicación

- **Integración**: Integra lecciones en procesos y herramientas estándar
- **Capacitación**: Usa lecciones para capacitar a nuevos miembros del equipo
- **Templates**: Crea templates y checklists basados en lecciones aprendidas
- **Cultura**: Fomenta una cultura donde compartir lecciones sea valorado

### Evolución

- **Actualización**: Actualiza y refina lecciones basándose en nueva experiencia
- **Descarte**: Elimina lecciones que ya no son relevantes o han sido superadas
- **Expansión**: Expande lecciones exitosas con más detalle y contexto
- **Conexión**: Conecta lecciones relacionadas para crear conocimiento más rico

## Ejemplos

### Ejemplo de Lección Técnica

```markdown
# Lección: Implementación de Clean Architecture en Backend

## Información Básica

- **Contribuidor**: Juan Pérez (Tech Lead)
- **Fecha**: 2024-03-10
- **Área**: Técnica
- **Prioridad**: Alta

## Contexto

**Situación**: Al inicio del proyecto decidimos implementar Clean Architecture
para separar las reglas de negocio del framework Laravel.

**Decisión tomada**: Creamos las capas Domain, Application e Infrastructure como
se especifica en el estándar.

## Resultado

**Qué pasó**: La separación de capas funcionó extremadamente bien. El código de
negocio quedó completamente independiente de Laravel, lo que facilitó testing y
mantenimiento.

**Impacto**:

- Testing: Pudimos testear lógica de negocio sin depender de base de datos
- Mantenimiento: Cambios en requirements no afectaron infraestructura
- Flexibilidad: Pudimos cambiar de MySQL a PostgreSQL sin tocar Domain layer

## Lección

**Qué aprendiste**: Clean Architecture realmente funciona cuando se implementa
correctamente. La inversión inicial en setup se paga rápidamente.

**Por qué es importante**: Permite evolucionar el sistema de manera más segura y
rápida.

## Aplicación Futura

**Qué harías diferente**: Invertiría aún más tiempo inicial en definir bien las
interfaces entre capas.

**Recomendación**: Usar Clean Architecture desde el día 1 en todos los proyectos
backend. Crear generators/templates para acelerar setup inicial.

## Evidencia

**Métricas/datos**:

- Tiempo de testing: 50% reducción vs proyecto anterior
- Bugs en producción: 30% menos que promedio histórico
- Velocidad de desarrollo: Después del mes 2, 40% más rápido

**Referencias**:

- Código: backend/app/Domain/
- Tests: tests/Unit/Domain/
- Documentación: docs/architecture.md

## Tags

#arquitectura #clean-architecture #backend #testing #mantenibilidad
#best-practice
```

### Ejemplo de Lección de Proceso

```markdown
# Lección: Code Review con Template Estructurado

## Información Básica

- **Contribuidor**: María González (Senior Developer)
- **Fecha**: 2024-03-05
- **Área**: Proceso
- **Prioridad**: Media

## Contexto

**Situación**: Los code reviews eran inconsistentes y a veces se omitían
aspectos importantes de calidad y arquitectura.

**Decisión tomada**: Implementamos un template de PR con checklist obligatorio
para el reviewer.

## Resultado

**Qué pasó**: La calidad de los code reviews mejoró significativamente. Los
reviews se volvieron más sistemáticos y completos.

**Impacto**:

- Calidad: Detección 60% más bugs antes de merge
- Consistencia: Todos los reviews siguen el mismo estándar
- Aprendizaje: Developers junior aprendieron más rápido

## Lección

**Qué aprendiste**: La estructura y checklists mejoran drásticamente la calidad
de los procesos. Sin estructura, la calidad depende del humor y disponibilidad
de tiempo de las personas.

**Por qué es importante**: Code review es una de las mejores prácticas para
mantener calidad, pero solo si se hace bien.

## Aplicación Futura

**Qué harías diferente**: Implementaría el template desde el primer día, no
después de 2 meses.

**Recomendación**: Crear templates de PR específicos por tipo de cambio
(feature, bugfix, refactor). Hacer el checklist parte del Definition of Done.

## Evidencia

**Métricas/datos**:

- Bugs encontrados en review: +60% vs período anterior
- Tiempo promedio de review: Mismo (el template no añadió overhead)
- Satisfacción del equipo con code review: 8.5/10 vs 6/10 anterior

**Referencias**:

- Template: .github/pull_request_template.md
- Métricas: GitHub insights de PRs marzo vs enero

## Tags

#code-review #proceso #calidad #template #best-practice #team-process
```

### Ejemplo de Base de Conocimiento

````markdown
# Base de Conocimiento - Mejores Prácticas de Testing

## Resumen

Recopilación de lecciones aprendidas sobre testing basadas en 3 proyectos y 15
lecciones capturadas.

## Lecciones Clave

### ✅ Qué Funciona Bien

1. **Test Coverage como Quality Gate**
   - Implementar coverage mínimo de 80% en CI/CD
   - Bloquear merge si coverage baja
   - _Evidencia_: Proyectos con coverage gates tuvieron 40% menos bugs en
     producción

2. **Testing Pyramid Bien Implementado**
   - 70% unit tests, 20% integration tests, 10% E2E tests
   - _Evidencia_: Balance óptimo entre velocidad y confianza

3. **Mocks para Dependencias Externas**
   - Siempre mockear APIs externas en unit tests
   - _Evidencia_: Tests 10x más rápidos y confiables

### ❌ Anti-Patrones Identificados

1. **Testing Implementation Details**
   - No testear cómo funciona internamente, sino qué resultado produce
   - _Problema_: Tests frágiles que se rompen con refactoring

2. **E2E Tests para Todo**
   - E2E tests solo para happy path críticos
   - _Problema_: Tests lentos e inestables

3. **No Testear Edge Cases**
   - Siempre incluir tests para null, empty, boundary values
   - _Problema_: Bugs frecuentes en casos extremos

## Aplicación en Nuevos Proyectos

### Setup Inicial

```bash
# Configurar Jest con coverage threshold
npm install --save-dev jest @testing-library/react
# Configurar coverage gates en package.json
```

### Checklist de Testing

- [ ] Coverage configurado con threshold 80%
- [ ] Tests divididos según testing pyramid
- [ ] Mocks configurados para APIs externas
- [ ] Tests para edge cases incluidos
- [ ] E2E tests solo para flows críticos

## Herramientas Recomendadas

- **Unit**: Jest + Testing Library (exitoso en 3/3 proyectos)
- **E2E**: Playwright (mejor que Cypress según experiencia)
- **Coverage**: Built-in Jest coverage (suficiente y simple)

## Métricas de Referencia

- **Coverage target**: 80% mínimo
- **Test execution time**: <30 segundos para suite completa unit tests
- **E2E suite time**: <10 minutos máximo
````

## Navegación

[⬅️ Evaluación del Cumplimiento del Estándar](./evaluacion-cumplimiento-estandar.md)
| [🏠 README Principal](../../README.md) |
[Evaluación de Herramientas ➡️](./evaluacion-herramientas.md)
