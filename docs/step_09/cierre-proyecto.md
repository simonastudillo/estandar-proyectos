# Cierre de Proyecto

## ¿Qué es?

El cierre de proyecto es la fase final del ciclo de vida del proyecto, donde se
consolida toda la documentación, se realiza la evaluación final, se archivan los
entregables y se formaliza la conclusión del proyecto. Es un proceso
estructurado que asegura que todo el conocimiento y experiencia del proyecto se
preserve para futuros esfuerzos.

## ¿Por qué es importante?

- **Formalización de la entrega**: Confirma oficialmente que el proyecto cumple
  con los objetivos establecidos
- **Preservación del conocimiento**: Asegura que las lecciones y experiencias no
  se pierdan
- **Liberación de recursos**: Permite la reasignación eficiente de recursos del
  equipo
- **Evaluación de éxito**: Proporciona una evaluación objetiva del éxito del
  proyecto
- **Cumplimiento organizacional**: Satisface requerimientos organizacionales y
  de auditoría
- **Base para futuros proyectos**: Crea un repositorio de conocimiento para
  proyectos similares
- **Reconocimiento del equipo**: Reconoce formalmente las contribuciones del
  equipo
- **Mejora continua**: Proporciona datos para mejorar procesos organizacionales

## ¿Qué debe incluir?

### Documentación Final

- **Documento de cierre**: Resumen ejecutivo del proyecto completo
- **Entregables finalizados**: Todos los productos del proyecto documentados
- **Documentación técnica**: Arquitectura, APIs, manuales de usuario
  actualizados
- **Documentación de procesos**: Procesos seguidos y adaptaciones realizadas

### Evaluación Final

- **Cumplimiento de objetivos**: Análisis detallado vs objetivos originales
- **Calidad de entregables**: Evaluación de la calidad de los productos finales
- **Performance del proyecto**: Análisis de tiempo, presupuesto y recursos
- **Satisfacción del cliente**: Evaluación de la satisfacción de stakeholders

### Transferencia de Conocimiento

- **Handover documentation**: Documentación para equipo de mantenimiento
- **Training materials**: Materiales de capacitación para usuarios finales
- **Knowledge base**: Base de conocimiento consolidada
- **Contact information**: Información de contacto para soporte post-proyecto

### Archivado y Almacenamiento

- **Repositorio de código**: Archivado final del código fuente
- **Documentación archivada**: Almacenamiento permanente de documentación
- **Assets del proyecto**: Diseños, mockups, recursos multimedia
- **Comunicaciones importantes**: Emails, decisiones, cambios importantes

## ¿Qué debo hacer?

### 1. Preparar documentación de cierre

```bash
# Crear estructura para cierre de proyecto
mkdir -p project-closure/{documentation,evaluation,handover,archive}

# Generar documento maestro de cierre
cat > project-closure/PROJECT_CLOSURE_DOCUMENT.md << 'EOF'
# Documento de Cierre de Proyecto

## Información del Proyecto
- **Nombre del Proyecto**: [Nombre]
- **Código del Proyecto**: [Código]
- **Fecha de Inicio**: [Fecha]
- **Fecha de Cierre**: [Fecha]
- **Duración Total**: [X meses]
- **Project Manager**: [Nombre]
- **Sponsor**: [Nombre]

## Resumen Ejecutivo

### Objetivos Cumplidos
- ✅ [Objetivo 1]: Descripción del cumplimiento
- ✅ [Objetivo 2]: Descripción del cumplimiento
- ⚠️ [Objetivo 3]: Parcialmente cumplido - explicación
- ❌ [Objetivo 4]: No cumplido - razones

### Entregables Principales
- [Entregable 1]: Estado final y ubicación
- [Entregable 2]: Estado final y ubicación
- [Entregable 3]: Estado final y ubicación

### Métricas del Proyecto
- **Presupuesto Planificado**: $X
- **Presupuesto Utilizado**: $Y (Z% del planificado)
- **Tiempo Planificado**: X meses
- **Tiempo Real**: Y meses
- **Recursos Utilizados**: X desarrolladores

## Evaluación de Cumplimiento

### Alcance
- **Scope creep**: [Descripción de cambios en alcance]
- **Features entregadas**: [X/Y features completadas]
- **Calidad final**: [Evaluación de calidad]

### Tiempo
- **Hitos principales**: [Estado de cumplimiento de hitos]
- **Retrasos significativos**: [Descripción y causas]
- **Optimizaciones realizadas**: [Acciones para recuperar tiempo]

### Presupuesto
- **Distribución de costos**: [Breakdown de gastos]
- **Variaciones**: [Explicación de diferencias vs presupuesto]
- **ROI del proyecto**: [Cálculo de retorno de inversión]

### Calidad
- **Métricas de calidad**: [Coverage, bugs, performance]
- **Satisfacción del usuario**: [Resultados de UAT]
- **Cumplimiento de estándares**: [Adherencia al estándar organizacional]

## Lecciones Aprendidas Principales

### Lo que funcionó bien
1. [Lección 1]: Descripción y por qué fue exitoso
2. [Lección 2]: Descripción y por qué fue exitoso
3. [Lección 3]: Descripción y por qué fue exitoso

### Desafíos enfrentados
1. [Desafío 1]: Descripción y cómo se resolvió
2. [Desafío 2]: Descripción y cómo se resolvió
3. [Desafío 3]: Descripción y impacto

### Recomendaciones para futuros proyectos
1. [Recomendación 1]: Acción específica y beneficio esperado
2. [Recomendación 2]: Acción específica y beneficio esperado
3. [Recomendación 3]: Acción específica y beneficio esperado

## Estado Final de Entregables

### Aplicación Web
- **Estado**: ✅ Completada y en producción
- **URL**: https://app.ejemplo.com
- **Documentación**: [Enlaces a documentación]
- **Responsable de mantenimiento**: [Equipo/Persona]

### API Backend
- **Estado**: ✅ Completada y en producción
- **Documentación**: [Enlace a Swagger/OpenAPI]
- **Monitoreo**: [Enlaces a dashboards]
- **Responsable de mantenimiento**: [Equipo/Persona]

### Base de Datos
- **Estado**: ✅ Implementada y operativa
- **Backups**: [Configuración de respaldos]
- **Monitoreo**: [Sistema de monitoreo]
- **Responsable de mantenimiento**: [DBA/Equipo]

### Documentación
- **Documentación técnica**: ✅ Completa y actualizada
- **Manuales de usuario**: ✅ Entregados y validados
- **Runbooks operacionales**: ✅ Documentados y probados

## Transferencia de Responsabilidades

### Equipo de Mantenimiento
- **Lead**: [Nombre y contacto]
- **Desarrolladores**: [Lista de desarrolladores]
- **DevOps**: [Responsable de infraestructura]
- **QA**: [Responsable de testing]

### Handover Completado
- ✅ Sesiones de transferencia realizadas
- ✅ Documentación entregada y validada
- ✅ Accesos transferidos
- ✅ Procesos de escalamiento establecidos

## Archivado del Proyecto

### Repositorios de Código
- **Frontend**: [URL del repositorio final]
- **Backend**: [URL del repositorio final]
- **Infrastructure**: [URL de configs de infraestructura]

### Documentación Archivada
- **Project Wiki**: [Ubicación final]
- **Decisiones técnicas**: [Ubicación de ADRs]
- **Comunicaciones**: [Archivo de emails y decisiones importantes]

### Assets del Proyecto
- **Diseños**: [Ubicación de archivos Figma/Sketch]
- **Branding**: [Logos, guías de estilo]
- **Multimedia**: [Videos, imágenes, demos]

## Aprobaciones y Firmas

### Stakeholder Sign-off
- [ ] **Sponsor**: [Nombre] - Fecha: _____ - Firma: _____
- [ ] **Product Owner**: [Nombre] - Fecha: _____ - Firma: _____
- [ ] **Tech Lead**: [Nombre] - Fecha: _____ - Firma: _____
- [ ] **Project Manager**: [Nombre] - Fecha: _____ - Firma: _____

### Confirmación de Entregables
- [ ] Todos los entregables están completos y aceptados
- [ ] Documentación técnica está actualizada y accesible
- [ ] Transferencia de conocimiento está completada
- [ ] Equipo de mantenimiento está preparado
- [ ] Archivado del proyecto está completo

## Próximos Pasos

### Inmediatos (1-2 semanas)
- [Acción 1]: Responsable y fecha
- [Acción 2]: Responsable y fecha

### Corto Plazo (1 mes)
- [Acción 1]: Responsable y fecha
- [Acción 2]: Responsable y fecha

### Seguimiento (3-6 meses)
- [Review 1]: Evaluación post-implementación
- [Review 2]: Análisis de adopción de usuarios

---

**Fecha de Cierre Oficial**: [Fecha]
**Preparado por**: [Nombre del PM]
**Revisado por**: [Nombre del Sponsor]
EOF
```

### 2. Script automatizado de cierre

```bash
#!/bin/bash
# project-closure/scripts/project-closure.sh

echo "📋 Iniciando proceso de cierre de proyecto..."

PROJECT_NAME="$1"
CLOSURE_DATE=$(date +"%Y-%m-%d")
CLOSURE_DIR="project-closure"

if [[ -z "$PROJECT_NAME" ]]; then
    echo "❌ Error: Proporciona el nombre del proyecto"
    echo "Uso: $0 <project-name>"
    exit 1
fi

# Crear estructura de cierre
mkdir -p "$CLOSURE_DIR"/{reports,archive,handover,documentation}

echo "📊 Generando métricas finales del proyecto..."

# 1. Métricas de código
echo "📝 Recopilando métricas de código..."
{
    echo "# Métricas Finales de Código"
    echo ""
    echo "## Estadísticas Generales"
    echo "- **Fecha de análisis**: $(date)"
    echo "- **Total de commits**: $(git rev-list --all --count)"
    echo "- **Número de contribuidores**: $(git log --format='%aN' | sort -u | wc -l)"
    echo "- **Líneas de código**:"
    
    # Contar líneas por tecnología
    if [[ -d "frontend" ]]; then
        echo "  - Frontend (JS/TS): $(find frontend/src -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" | xargs wc -l | tail -1 | awk '{print $1}') líneas"
    fi
    
    if [[ -d "backend" ]]; then
        echo "  - Backend (PHP): $(find backend/app -name "*.php" | xargs wc -l | tail -1 | awk '{print $1}') líneas"
    fi
    
    echo ""
    echo "## Calidad del Código"
    
    # Coverage de tests si existe
    if [[ -f "frontend/coverage/coverage-summary.json" ]]; then
        COVERAGE=$(cat frontend/coverage/coverage-summary.json | jq '.total.lines.pct')
        echo "- **Coverage de tests**: ${COVERAGE}%"
    fi
    
    # Issues de ESLint si existe
    if [[ -f "frontend/.eslintrc.js" ]]; then
        cd frontend
        ESLINT_ISSUES=$(npx eslint src/ --format json 2>/dev/null | jq '[.[].messages] | flatten | length' 2>/dev/null || echo "0")
        echo "- **Issues de ESLint**: $ESLINT_ISSUES"
        cd ..
    fi
    
} > "$CLOSURE_DIR/reports/code-metrics-final.md"

# 2. Análisis de Git
echo "📈 Generando análisis de repositorio..."
{
    echo "# Análisis Final del Repositorio"
    echo ""
    echo "## Cronología del Proyecto"
    echo "- **Primer commit**: $(git log --reverse --format='%ai' | head -1)"
    echo "- **Último commit**: $(git log -1 --format='%ai')"
    echo ""
    echo "## Contribuciones por Desarrollador"
    git log --format='%aN' | sort | uniq -c | sort -nr | while read count name; do
        echo "- **$name**: $count commits"
    done
    echo ""
    echo "## Actividad por Mes"
    git log --format='%ai' | awk '{print $1}' | cut -d'-' -f1,2 | sort | uniq -c | tail -12 | while read count month; do
        echo "- **$month**: $count commits"
    done
    
} > "$CLOSURE_DIR/reports/git-analysis-final.md"

# 3. Documentación de APIs (si existe)
echo "📚 Archivando documentación de APIs..."
if [[ -f "backend/storage/api-docs/api-docs.json" ]]; then
    cp "backend/storage/api-docs/api-docs.json" "$CLOSURE_DIR/archive/api-documentation.json"
    echo "✅ Documentación de API archivada"
fi

# 4. Configuraciones importantes
echo "⚙️ Archivando configuraciones..."
mkdir -p "$CLOSURE_DIR/archive/configurations"

# Docker configs
if [[ -f "docker-compose.yml" ]]; then
    cp docker-compose.yml "$CLOSURE_DIR/archive/configurations/"
fi

# CI/CD configs
if [[ -d ".github/workflows" ]]; then
    cp -r .github/workflows "$CLOSURE_DIR/archive/configurations/"
fi

# Environment samples
find . -name "*.env.example" -exec cp {} "$CLOSURE_DIR/archive/configurations/" \;

# 5. Generar lista de dependencias finales
echo "📦 Documentando dependencias finales..."
{
    echo "# Dependencias Finales del Proyecto"
    echo ""
    if [[ -f "frontend/package.json" ]]; then
        echo "## Frontend Dependencies"
        echo "\`\`\`json"
        cat frontend/package.json | jq '.dependencies'
        echo "\`\`\`"
        echo ""
        echo "## Frontend Dev Dependencies"
        echo "\`\`\`json"
        cat frontend/package.json | jq '.devDependencies'
        echo "\`\`\`"
        echo ""
    fi
    
    if [[ -f "backend/composer.json" ]]; then
        echo "## Backend Dependencies"
        echo "\`\`\`json"
        cat backend/composer.json | jq '.require'
        echo "\`\`\`"
        echo ""
        echo "## Backend Dev Dependencies"
        echo "\`\`\`json"
        cat backend/composer.json | jq '."require-dev"'
        echo "\`\`\`"
    fi
} > "$CLOSURE_DIR/archive/dependencies-final.md"

# 6. Crear README de archivado
echo "📄 Creando README de archivo..."
{
    echo "# Archivo del Proyecto: $PROJECT_NAME"
    echo ""
    echo "**Fecha de cierre**: $CLOSURE_DATE"
    echo "**Estado**: Proyecto completado y archivado"
    echo ""
    echo "## Contenido del Archivo"
    echo ""
    echo "### Reportes"
    echo "- [\`code-metrics-final.md\`](./reports/code-metrics-final.md) - Métricas finales de código"
    echo "- [\`git-analysis-final.md\`](./reports/git-analysis-final.md) - Análisis del repositorio"
    echo ""
    echo "### Configuraciones"
    echo "- [\`configurations/\`](./archive/configurations/) - Configuraciones de CI/CD, Docker, etc."
    echo "- [\`dependencies-final.md\`](./archive/dependencies-final.md) - Dependencias finales"
    echo ""
    echo "### Documentación"
    echo "- [\`api-documentation.json\`](./archive/api-documentation.json) - Documentación de API"
    echo ""
    echo "## Cómo Acceder al Código"
    echo ""
    echo "El código fuente del proyecto está disponible en:"
    echo "- **Repositorio principal**: $(git remote get-url origin)"
    echo "- **Última versión**: $(git describe --tags --always)"
    echo "- **Branch principal**: $(git branch --show-current)"
    echo ""
    echo "## Contactos para Soporte"
    echo ""
    echo "Para preguntas sobre este proyecto archivado:"
    echo "- **Tech Lead**: [Nombre y email]"
    echo "- **Project Manager**: [Nombre y email]"
    echo "- **Equipo de Mantenimiento**: [Información de contacto]"
    echo ""
    echo "## Instrucciones de Restauración"
    echo ""
    echo "Para restaurar este proyecto para desarrollo:"
    echo ""
    echo "\`\`\`bash"
    echo "# 1. Clonar repositorio"
    echo "git clone $(git remote get-url origin) $PROJECT_NAME-restored"
    echo "cd $PROJECT_NAME-restored"
    echo ""
    echo "# 2. Checkout a la versión final"
    echo "git checkout $(git describe --tags --always)"
    echo ""
    echo "# 3. Seguir instrucciones en README.md principal"
    echo "\`\`\`"
    
} > "$CLOSURE_DIR/README.md"

# 7. Generar checklist de cierre
echo "✅ Generando checklist de cierre..."
{
    echo "# Checklist de Cierre de Proyecto"
    echo ""
    echo "**Proyecto**: $PROJECT_NAME"
    echo "**Fecha**: $CLOSURE_DATE"
    echo ""
    echo "## Pre-Cierre"
    echo "- [ ] Todos los features están completados y probados"
    echo "- [ ] Documentación técnica está actualizada"
    echo "- [ ] Tests de aceptación han pasado"
    echo "- [ ] Performance meets requirements"
    echo "- [ ] Security audit completado"
    echo ""
    echo "## Documentación"
    echo "- [ ] README.md actualizado con instrucciones finales"
    echo "- [ ] API documentation generada y archivada"
    echo "- [ ] Architecture documentation actualizada"
    echo "- [ ] User manuals entregados"
    echo "- [ ] Runbooks operacionales documentados"
    echo ""
    echo "## Code y Repositorio"
    echo "- [ ] Código final committeado y pusheado"
    echo "- [ ] Tags de versión creados"
    echo "- [ ] Branches innecesarios eliminados"
    echo "- [ ] Archive de configuraciones creado"
    echo ""
    echo "## Handover"
    echo "- [ ] Sesión de handover con equipo de mantenimiento"
    echo "- [ ] Accesos transferidos (repositories, servers, etc.)"
    echo "- [ ] Contactos de emergencia establecidos"
    echo "- [ ] Escalation procedures documentados"
    echo ""
    echo "## Stakeholder Sign-off"
    echo "- [ ] Product Owner aprueba entregables"
    echo "- [ ] Sponsor confirma cumplimiento de objetivos"
    echo "- [ ] Tech Lead valida calidad técnica"
    echo "- [ ] Usuario final acepta sistema"
    echo ""
    echo "## Administrativo"
    echo "- [ ] Recursos liberados (servidores, licencias)"
    echo "- [ ] Presupuesto final reconciliado"
    echo "- [ ] Contratos con vendors finalizados"
    echo "- [ ] Equipment returned"
    echo ""
    echo "## Post-Cierre"
    echo "- [ ] Retrospectiva final realizada"
    echo "- [ ] Lecciones aprendidas documentadas"
    echo "- [ ] Team celebration organizada"
    echo "- [ ] Next steps para equipo definidos"
    echo ""
    echo "---"
    echo ""
    echo "**Completado por**: [Nombre]"
    echo "**Fecha de completación**: [Fecha]"
    echo "**Aprobado por**: [Sponsor/PM]"
    
} > "$CLOSURE_DIR/closure-checklist.md"

# 8. Crear paquete de entrega final
echo "📦 Creando paquete de entrega final..."
ARCHIVE_NAME="${PROJECT_NAME}-final-delivery-$(date +%Y%m%d).tar.gz"

tar -czf "$CLOSURE_DIR/$ARCHIVE_NAME" \
    --exclude='node_modules' \
    --exclude='vendor' \
    --exclude='.git' \
    --exclude='storage/logs' \
    --exclude='build' \
    --exclude='dist' \
    .

echo "📁 Paquete de entrega creado: $CLOSURE_DIR/$ARCHIVE_NAME"

echo "✅ Proceso de cierre completado exitosamente!"
echo ""
echo "📋 Próximos pasos:"
echo "1. Revisar y completar: $CLOSURE_DIR/closure-checklist.md"
echo "2. Obtener aprobaciones de stakeholders"
echo "3. Finalizar handover con equipo de mantenimiento"
echo "4. Archivar documentación en ubicación permanente"
echo ""
echo "📂 Archivos generados en: $CLOSURE_DIR/"
ls -la "$CLOSURE_DIR/"
```

### 3. Template de retrospectiva final

```typescript
// project-closure/retrospective/FinalRetrospective.ts

interface FinalRetrospectiveData {
   projectInfo: {
      name: string;
      duration: number; // months
      teamSize: number;
      budget: number;
      finalDeliveryDate: Date;
   };
   objectivesAnalysis: {
      originalObjectives: string[];
      achievedObjectives: string[];
      partiallyAchieved: Array<{
         objective: string;
         percentageComplete: number;
         reason: string;
      }>;
      notAchieved: Array<{
         objective: string;
         reason: string;
         impact: string;
      }>;
   };
   metricsAnalysis: {
      schedule: {
         planned: number; // days
         actual: number; // days
         variance: number; // percentage
      };
      budget: {
         planned: number;
         actual: number;
         variance: number; // percentage
      };
      quality: {
         defectDensity: number; // bugs per KLOC
         testCoverage: number; // percentage
         customerSatisfaction: number; // 1-10 scale
      };
   };
   teamRetrospective: {
      whatWorkedWell: Array<{
         item: string;
         impact: "high" | "medium" | "low";
         category: "process" | "technology" | "team" | "communication";
      }>;
      whatDidntWork: Array<{
         item: string;
         impact: "high" | "medium" | "low";
         category: "process" | "technology" | "team" | "communication";
         rootCause: string;
      }>;
      surprises: Array<{
         description: string;
         type: "positive" | "negative";
         howWeHandled: string;
      }>;
   };
   stakeholderFeedback: {
      sponsor: {
         satisfaction: number; // 1-10
         feedback: string;
         futureRecommendations: string;
      };
      endUsers: {
         satisfaction: number; // 1-10
         adoptionRate: number; // percentage
         mainComplaints: string[];
         mainPraises: string[];
      };
      developmentTeam: {
         satisfaction: number; // 1-10
         wouldWorkTogetherAgain: number; // percentage
         skillsDeveloped: string[];
         burnoutLevel: number; // 1-10
      };
   };
   lessonsLearned: {
      technical: Array<{
         lesson: string;
         applicability: string;
         priority: "high" | "medium" | "low";
      }>;
      process: Array<{
         lesson: string;
         applicability: string;
         priority: "high" | "medium" | "low";
      }>;
      organizational: Array<{
         lesson: string;
         applicability: string;
         priority: "high" | "medium" | "low";
      }>;
   };
   recommendations: {
      forFutureProjects: string[];
      forOrganization: string[];
      forTeamDevelopment: string[];
      forProcessImprovement: string[];
   };
}

export class FinalRetrospectiveGenerator {
   public generateRetrospectiveReport(data: FinalRetrospectiveData): string {
      let report = "# Retrospectiva Final de Proyecto\n\n";

      // Project Overview
      report += "## Información del Proyecto\n\n";
      report += `- **Nombre**: ${data.projectInfo.name}\n`;
      report += `- **Duración**: ${data.projectInfo.duration} meses\n`;
      report +=
         `- **Tamaño del equipo**: ${data.projectInfo.teamSize} personas\n`;
      report +=
         `- **Presupuesto**: $${data.projectInfo.budget.toLocaleString()}\n`;
      report +=
         `- **Fecha de entrega**: ${data.projectInfo.finalDeliveryDate.toLocaleDateString()}\n\n`;

      // Objectives Analysis
      report += "## Análisis de Objetivos\n\n";

      const totalObjectives = data.objectivesAnalysis.originalObjectives.length;
      const achieved = data.objectivesAnalysis.achievedObjectives.length;
      const partial = data.objectivesAnalysis.partiallyAchieved.length;
      const notAchieved = data.objectivesAnalysis.notAchieved.length;

      report += `### Resumen de Cumplimiento\n`;
      report +=
         `- ✅ **Completamente logrados**: ${achieved}/${totalObjectives} (${
            Math.round(achieved / totalObjectives * 100)
         }%)\n`;
      report +=
         `- ⚠️ **Parcialmente logrados**: ${partial}/${totalObjectives} (${
            Math.round(partial / totalObjectives * 100)
         }%)\n`;
      report += `- ❌ **No logrados**: ${notAchieved}/${totalObjectives} (${
         Math.round(notAchieved / totalObjectives * 100)
      }%)\n\n`;

      if (data.objectivesAnalysis.achievedObjectives.length > 0) {
         report += "### ✅ Objetivos Completamente Logrados\n";
         data.objectivesAnalysis.achievedObjectives.forEach((obj) => {
            report += `- ${obj}\n`;
         });
         report += "\n";
      }

      if (data.objectivesAnalysis.partiallyAchieved.length > 0) {
         report += "### ⚠️ Objetivos Parcialmente Logrados\n";
         data.objectivesAnalysis.partiallyAchieved.forEach((obj) => {
            report +=
               `- **${obj.objective}** (${obj.percentageComplete}% completo)\n`;
            report += `  - *Razón*: ${obj.reason}\n`;
         });
         report += "\n";
      }

      if (data.objectivesAnalysis.notAchieved.length > 0) {
         report += "### ❌ Objetivos No Logrados\n";
         data.objectivesAnalysis.notAchieved.forEach((obj) => {
            report += `- **${obj.objective}**\n`;
            report += `  - *Razón*: ${obj.reason}\n`;
            report += `  - *Impacto*: ${obj.impact}\n`;
         });
         report += "\n";
      }

      // Metrics Analysis
      report += "## Análisis de Métricas\n\n";

      report += "### 📅 Cronograma\n";
      report +=
         `- **Planificado**: ${data.metricsAnalysis.schedule.planned} días\n`;
      report += `- **Real**: ${data.metricsAnalysis.schedule.actual} días\n`;
      report += `- **Variación**: ${
         data.metricsAnalysis.schedule.variance > 0 ? "+" : ""
      }${data.metricsAnalysis.schedule.variance}%\n\n`;

      report += "### 💰 Presupuesto\n";
      report +=
         `- **Planificado**: $${data.metricsAnalysis.budget.planned.toLocaleString()}\n`;
      report +=
         `- **Real**: $${data.metricsAnalysis.budget.actual.toLocaleString()}\n`;
      report += `- **Variación**: ${
         data.metricsAnalysis.budget.variance > 0 ? "+" : ""
      }${data.metricsAnalysis.budget.variance}%\n\n`;

      report += "### 🎯 Calidad\n";
      report +=
         `- **Densidad de defectos**: ${data.metricsAnalysis.quality.defectDensity} bugs/KLOC\n`;
      report +=
         `- **Cobertura de tests**: ${data.metricsAnalysis.quality.testCoverage}%\n`;
      report +=
         `- **Satisfacción del cliente**: ${data.metricsAnalysis.quality.customerSatisfaction}/10\n\n`;

      // Team Retrospective
      report += "## Retrospectiva del Equipo\n\n";

      report += "### 🌟 Lo que funcionó bien\n";
      data.teamRetrospective.whatWorkedWell.forEach((item) => {
         const emoji = item.impact === "high"
            ? "🔥"
            : item.impact === "medium"
            ? "👍"
            : "✅";
         report += `${emoji} **${item.item}** (${item.category})\n`;
      });
      report += "\n";

      report += "### ⚠️ Lo que no funcionó bien\n";
      data.teamRetrospective.whatDidntWork.forEach((item) => {
         const emoji = item.impact === "high"
            ? "🔴"
            : item.impact === "medium"
            ? "🟡"
            : "🟠";
         report += `${emoji} **${item.item}** (${item.category})\n`;
         report += `   - *Causa raíz*: ${item.rootCause}\n`;
      });
      report += "\n";

      if (data.teamRetrospective.surprises.length > 0) {
         report += "### 😲 Sorpresas del Proyecto\n";
         data.teamRetrospective.surprises.forEach((surprise) => {
            const emoji = surprise.type === "positive" ? "🎉" : "😰";
            report += `${emoji} **${surprise.description}**\n`;
            report += `   - *Cómo lo manejamos*: ${surprise.howWeHandled}\n`;
         });
         report += "\n";
      }

      // Stakeholder Feedback
      report += "## Feedback de Stakeholders\n\n";

      report += "### 💼 Sponsor\n";
      report +=
         `- **Satisfacción**: ${data.stakeholderFeedback.sponsor.satisfaction}/10\n`;
      report +=
         `- **Feedback**: ${data.stakeholderFeedback.sponsor.feedback}\n`;
      report +=
         `- **Recomendaciones futuras**: ${data.stakeholderFeedback.sponsor.futureRecommendations}\n\n`;

      report += "### 👥 Usuarios Finales\n";
      report +=
         `- **Satisfacción**: ${data.stakeholderFeedback.endUsers.satisfaction}/10\n`;
      report +=
         `- **Tasa de adopción**: ${data.stakeholderFeedback.endUsers.adoptionRate}%\n`;
      if (data.stakeholderFeedback.endUsers.mainPraises.length > 0) {
         report += "- **Principales elogios**:\n";
         data.stakeholderFeedback.endUsers.mainPraises.forEach((praise) => {
            report += `  - ${praise}\n`;
         });
      }
      if (data.stakeholderFeedback.endUsers.mainComplaints.length > 0) {
         report += "- **Principales quejas**:\n";
         data.stakeholderFeedback.endUsers.mainComplaints.forEach(
            (complaint) => {
               report += `  - ${complaint}\n`;
            },
         );
      }
      report += "\n";

      report += "### 👨‍💻 Equipo de Desarrollo\n";
      report +=
         `- **Satisfacción del equipo**: ${data.stakeholderFeedback.developmentTeam.satisfaction}/10\n`;
      report +=
         `- **Trabajaría junto otra vez**: ${data.stakeholderFeedback.developmentTeam.wouldWorkTogetherAgain}%\n`;
      report +=
         `- **Nivel de burnout**: ${data.stakeholderFeedback.developmentTeam.burnoutLevel}/10\n`;
      if (data.stakeholderFeedback.developmentTeam.skillsDeveloped.length > 0) {
         report += "- **Habilidades desarrolladas**:\n";
         data.stakeholderFeedback.developmentTeam.skillsDeveloped.forEach(
            (skill) => {
               report += `  - ${skill}\n`;
            },
         );
      }
      report += "\n";

      // Lessons Learned
      report += "## Lecciones Aprendidas\n\n";

      report += "### 🔧 Técnicas\n";
      data.lessonsLearned.technical.forEach((lesson) => {
         const emoji = lesson.priority === "high"
            ? "🔥"
            : lesson.priority === "medium"
            ? "⭐"
            : "💡";
         report += `${emoji} **${lesson.lesson}**\n`;
         report += `   - *Aplicabilidad*: ${lesson.applicability}\n`;
      });
      report += "\n";

      report += "### 📋 Procesos\n";
      data.lessonsLearned.process.forEach((lesson) => {
         const emoji = lesson.priority === "high"
            ? "🔥"
            : lesson.priority === "medium"
            ? "⭐"
            : "💡";
         report += `${emoji} **${lesson.lesson}**\n`;
         report += `   - *Aplicabilidad*: ${lesson.applicability}\n`;
      });
      report += "\n";

      report += "### 🏢 Organizacionales\n";
      data.lessonsLearned.organizational.forEach((lesson) => {
         const emoji = lesson.priority === "high"
            ? "🔥"
            : lesson.priority === "medium"
            ? "⭐"
            : "💡";
         report += `${emoji} **${lesson.lesson}**\n`;
         report += `   - *Aplicabilidad*: ${lesson.applicability}\n`;
      });
      report += "\n";

      // Recommendations
      report += "## Recomendaciones\n\n";

      report += "### 🚀 Para Futuros Proyectos\n";
      data.recommendations.forFutureProjects.forEach((rec) => {
         report += `- ${rec}\n`;
      });
      report += "\n";

      report += "### 🏢 Para la Organización\n";
      data.recommendations.forOrganization.forEach((rec) => {
         report += `- ${rec}\n`;
      });
      report += "\n";

      report += "### 👥 Para Desarrollo del Equipo\n";
      data.recommendations.forTeamDevelopment.forEach((rec) => {
         report += `- ${rec}\n`;
      });
      report += "\n";

      report += "### ⚙️ Para Mejora de Procesos\n";
      data.recommendations.forProcessImprovement.forEach((rec) => {
         report += `- ${rec}\n`;
      });
      report += "\n";

      // Conclusion
      report += "## Conclusión\n\n";
      const successRate = (achieved + partial * 0.5) / totalObjectives * 100;

      if (successRate >= 80) {
         report +=
            "🎉 **Proyecto Altamente Exitoso**: Se lograron la mayoría de los objetivos con alta calidad y satisfacción de stakeholders.\n\n";
      } else if (successRate >= 60) {
         report +=
            "👍 **Proyecto Exitoso**: Se cumplieron los objetivos principales, aunque hay áreas de mejora identificadas.\n\n";
      } else {
         report +=
            "⚠️ **Proyecto con Desafíos**: Aunque se completó el proyecto, hay lecciones importantes que aplicar en futuros esfuerzos.\n\n";
      }

      report += `**Tasa de éxito general**: ${Math.round(successRate)}%\n\n`;

      report +=
         "Este proyecto ha sido una valiosa experiencia de aprendizaje para todo el equipo y la organización. ";
      report +=
         "Las lecciones aprendidas y recomendaciones documentadas servirán como base para futuros proyectos similares.\n\n";

      report += "---\n\n";
      report += `**Retrospectiva completada el**: ${
         new Date().toLocaleDateString()
      }\n`;
      report += `**Facilitada por**: [Nombre del facilitador]\n`;
      report += `**Participantes**: [Lista de participantes]\n`;

      return report;
   }

   public generateActionPlan(data: FinalRetrospectiveData): string {
      let actionPlan = "# Plan de Acción Post-Retrospectiva\n\n";

      actionPlan +=
         "Basado en los resultados de la retrospectiva final, se han identificado las siguientes acciones prioritarias:\n\n";

      // High priority lessons as actions
      actionPlan += "## Acciones Inmediatas (1-2 semanas)\n\n";
      const highPriorityLessons = [
         ...data.lessonsLearned.technical.filter((l) => l.priority === "high"),
         ...data.lessonsLearned.process.filter((l) => l.priority === "high"),
         ...data.lessonsLearned.organizational.filter((l) =>
            l.priority === "high"
         ),
      ];

      highPriorityLessons.forEach((lesson, index) => {
         actionPlan += `### ${index + 1}. ${lesson.lesson}\n`;
         actionPlan += `- **Responsable**: [Asignar]\n`;
         actionPlan += `- **Fecha límite**: [Definir]\n`;
         actionPlan += `- **Aplicabilidad**: ${lesson.applicability}\n\n`;
      });

      // Process improvements
      actionPlan += "## Mejoras de Proceso (1-3 meses)\n\n";
      data.recommendations.forProcessImprovement.forEach((rec, index) => {
         actionPlan += `### ${index + 1}. ${rec}\n`;
         actionPlan += `- **Responsable**: [Asignar]\n`;
         actionPlan += `- **Fecha límite**: [Definir]\n`;
         actionPlan += `- **Impacto esperado**: [Definir]\n\n`;
      });

      // Organizational changes
      actionPlan += "## Cambios Organizacionales (3-6 meses)\n\n";
      data.recommendations.forOrganization.forEach((rec, index) => {
         actionPlan += `### ${index + 1}. ${rec}\n`;
         actionPlan += `- **Responsable**: [Asignar]\n`;
         actionPlan += `- **Fecha límite**: [Definir]\n`;
         actionPlan += `- **Recursos necesarios**: [Definir]\n\n`;
      });

      actionPlan += "## Seguimiento\n\n";
      actionPlan += "- **Revisión mensual**: [Fecha de primera revisión]\n";
      actionPlan += "- **Responsable del seguimiento**: [Nombre]\n";
      actionPlan +=
         "- **Métricas de éxito**: [Definir métricas para medir progreso]\n";

      return actionPlan;
   }
}
```

### 4. Automatización de entrega final

```yaml
# .github/workflows/project-closure.yml
name: Project Closure Automation

on:
   workflow_dispatch:
      inputs:
         closure_type:
            description: "Type of closure"
            required: true
            default: "project_completion"
            type: choice
            options:
               - project_completion
               - milestone_closure
               - emergency_closure

         generate_reports:
            description: "Generate comprehensive reports"
            required: true
            default: true
            type: boolean

         notify_stakeholders:
            description: "Notify stakeholders"
            required: true
            default: true
            type: boolean

jobs:
   project-closure:
      runs-on: ubuntu-latest

      steps:
         - name: Checkout repository
           uses: actions/checkout@v3
           with:
              fetch-depth: 0 # Full history for analysis

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"
              cache: "npm"
              cache-dependency-path: frontend/package-lock.json

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
              extensions: mbstring, xml, ctype, iconv, intl

         - name: Create closure directory structure
           run: |
              mkdir -p project-closure/{documentation,evaluation,handover,archive,reports}

         - name: Generate final code metrics
           if: inputs.generate_reports == true
           run: |
              echo "📊 Generating final code metrics..."

              # Frontend metrics
              if [ -d "frontend" ]; then
                cd frontend
                npm ci

                # Generate test coverage
                npm run test:coverage || echo "Warning: Could not generate coverage"

                # ESLint report
                npx eslint src/ --format json --output-file ../project-closure/reports/eslint-final.json || true

                # Bundle size analysis
                npm run build || echo "Warning: Could not build for size analysis"

                cd ..
              fi

              # Backend metrics
              if [ -d "backend" ]; then
                cd backend
                composer install --no-dev --optimize-autoloader

                # Generate API documentation
                php artisan l5-swagger:generate || echo "Warning: Could not generate API docs"

                cd ..
              fi

         - name: Generate Git analysis
           run: |
              echo "📈 Generating Git analysis..."

              # Contributors analysis
              git log --format='%aN <%aE>' | sort -u > project-closure/reports/contributors.txt

              # Commit timeline
              git log --format='%ai %s' --reverse > project-closure/reports/commit-timeline.txt

              # Code changes over time
              git log --format='%ai' --numstat | head -1000 > project-closure/reports/code-changes.txt

              # Tag information
              git tag -l --sort=-version:refname > project-closure/reports/versions.txt

         - name: Package final deliverables
           run: |
              echo "📦 Creating final delivery package..."

              # Create source code archive
              tar -czf project-closure/archive/source-code-final.tar.gz \
                --exclude='node_modules' \
                --exclude='vendor' \
                --exclude='.git' \
                --exclude='storage/logs' \
                --exclude='storage/framework/cache' \
                --exclude='build' \
                --exclude='dist' \
                .

              # Archive configuration files
              mkdir -p project-closure/archive/configurations
              cp -r .github project-closure/archive/configurations/ || true
              cp docker-compose*.yml project-closure/archive/configurations/ || true
              cp *.env.example project-closure/archive/configurations/ || true

              # Archive documentation
              cp -r docs project-closure/archive/ || true
              cp README.md project-closure/archive/ || true

         - name: Generate closure documentation
           run: |
              echo "📝 Generating closure documentation..."

              # Project statistics
              cat > project-closure/documentation/project-statistics.md << 'EOF'
              # Estadísticas Finales del Proyecto

              ## Repositorio
              - **Total de commits**: $(git rev-list --all --count)
              - **Contribuidores**: $(git log --format='%aN' | sort -u | wc -l)
              - **Branches creados**: $(git branch -a | wc -l)
              - **Tags de versión**: $(git tag | wc -l)

              ## Código
              EOF

              # Add code statistics if directories exist
              if [ -d "frontend/src" ]; then
                echo "- **Frontend (TS/JS)**: $(find frontend/src -name '*.ts' -o -name '*.tsx' -o -name '*.js' -o -name '*.jsx' | xargs wc -l | tail -1 | awk '{print $1}') líneas" >> project-closure/documentation/project-statistics.md
              fi

              if [ -d "backend/app" ]; then
                echo "- **Backend (PHP)**: $(find backend/app -name '*.php' | xargs wc -l | tail -1 | awk '{print $1}') líneas" >> project-closure/documentation/project-statistics.md
              fi

              # Timeline
              echo "" >> project-closure/documentation/project-statistics.md
              echo "## Timeline" >> project-closure/documentation/project-statistics.md
              echo "- **Primer commit**: $(git log --reverse --format='%ai' | head -1)" >> project-closure/documentation/project-statistics.md
              echo "- **Último commit**: $(git log -1 --format='%ai')" >> project-closure/documentation/project-statistics.md
              echo "- **Duración total**: $(( ($(date +%s) - $(git log --reverse --format='%at' | head -1)) / 86400 )) días" >> project-closure/documentation/project-statistics.md

         - name: Create handover package
           run: |
              echo "🤝 Creating handover package..."

              # Handover checklist
              cat > project-closure/handover/handover-checklist.md << 'EOF'
              # Checklist de Handover

              ## Accesos y Permisos
              - [ ] Repositorio de código accesible
              - [ ] Servidores de producción accesibles
              - [ ] Bases de datos accesibles
              - [ ] Servicios externos (APIs, CDNs)
              - [ ] Monitoreo y alertas configuradas

              ## Documentación Entregada
              - [ ] README actualizado
              - [ ] Documentación de API
              - [ ] Guías de deployment
              - [ ] Runbooks operacionales
              - [ ] Documentación de arquitectura

              ## Knowledge Transfer
              - [ ] Sesión de handover realizada
              - [ ] Contactos de emergencia proporcionados
              - [ ] Procedimientos de escalamiento documentados
              - [ ] Known issues documentados

              ## Testing y Validación
              - [ ] Suite de tests funcionando
              - [ ] Environments de testing configurados
              - [ ] Procedimientos de deployment verificados
              - [ ] Rollback procedures validados
              EOF

              # Emergency contacts
              cat > project-closure/handover/emergency-contacts.md << 'EOF'
              # Contactos de Emergencia

              ## Equipo Principal
              - **Tech Lead**: [Nombre] - [Email] - [Teléfono]
              - **DevOps**: [Nombre] - [Email] - [Teléfono]
              - **Product Owner**: [Nombre] - [Email] - [Teléfono]

              ## Escalamiento
              1. **Nivel 1**: Equipo de mantenimiento
              2. **Nivel 2**: Tech Lead original
              3. **Nivel 3**: Engineering Manager

              ## Servicios Críticos
              - **Hosting**: [Proveedor] - [Contacto de soporte]
              - **Base de datos**: [Proveedor] - [Contacto de soporte]
              - **CDN**: [Proveedor] - [Contacto de soporte]
              - **Monitoreo**: [Proveedor] - [Contacto de soporte]
              EOF

         - name: Generate final reports
           if: inputs.generate_reports == true
           run: |
              echo "📋 Generating comprehensive final reports..."

              # Create executive summary
              cat > project-closure/reports/executive-summary.md << 'EOF'
              # Resumen Ejecutivo - Cierre de Proyecto

              ## Estado del Proyecto
              **Estado**: ✅ Completado exitosamente
              **Fecha de cierre**: $(date +"%Y-%m-%d")

              ## Entregables Principales
              - ✅ Aplicación web completada y en producción
              - ✅ API backend documentada y operativa
              - ✅ Base de datos implementada y optimizada
              - ✅ CI/CD pipeline configurado y funcionando
              - ✅ Documentación técnica completa

              ## Métricas Clave
              - **Duración**: $(( ($(date +%s) - $(git log --reverse --format='%at' | head -1)) / 86400 )) días
              - **Commits totales**: $(git rev-list --all --count)
              - **Líneas de código**: [Calculado automáticamente]
              - **Contribuidores**: $(git log --format='%aN' | sort -u | wc -l)

              ## Próximos Pasos
              1. Handover al equipo de mantenimiento
              2. Monitoreo de adopción por usuarios
              3. Retrospectiva final del equipo
              4. Archivado de documentación
              EOF

         - name: Upload closure artifacts
           uses: actions/upload-artifact@v3
           with:
              name: project-closure-package
              path: project-closure/
              retention-days: 90

         - name: Create release with closure documentation
           if: inputs.closure_type == 'project_completion'
           uses: actions/create-release@v1
           env:
              GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
           with:
              tag_name: project-closure-v${{ github.run_number }}
              release_name: Project Closure - Final Release
              body: |
                 ## 🎉 Project Closure - Final Release

                 This release marks the official closure of the project.

                 ### Included in this release:
                 - Final source code
                 - Complete documentation
                 - Handover materials
                 - Project statistics and reports

                 ### Next Steps:
                 1. Handover to maintenance team
                 2. Final retrospective
                 3. Archive project materials

                 **Closure Date**: ${{ github.event.head_commit.timestamp }}
                 **Final Commit**: ${{ github.sha }}
              draft: false
              prerelease: false

         - name: Notify stakeholders
           if: inputs.notify_stakeholders == true
           uses: actions/github-script@v6
           with:
              script: |
                 // Create issue for stakeholder notification
                 const { data: issue } = await github.rest.issues.create({
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   title: '🎯 Proyecto Oficialmente Cerrado - Acción Requerida',
                   body: `## 🎉 Cierre Oficial del Proyecto

                 El proyecto ha sido oficialmente cerrado y está listo para handover.

                 ### 📋 Acciones Requeridas

                 **Para el Sponsor (@sponsor):**
                 - [ ] Revisar resumen ejecutivo
                 - [ ] Aprobar cierre formal del proyecto
                 - [ ] Firmar documentación de acceptance

                 **Para el Product Owner (@product-owner):**
                 - [ ] Validar que todos los entregables están completos
                 - [ ] Confirmar satisfacción con la calidad
                 - [ ] Aprobar handover al equipo de mantenimiento

                 **Para el Tech Lead (@tech-lead):**
                 - [ ] Completar sesión de handover técnico
                 - [ ] Verificar que documentación está actualizada
                 - [ ] Transferir accesos al equipo de mantenimiento

                 **Para el Equipo de Mantenimiento (@maintenance-team):**
                 - [ ] Revisar documentación de handover
                 - [ ] Confirmar acceso a todos los sistemas
                 - [ ] Agendar sesión de Q&A con equipo original

                 ### 📦 Entregables Disponibles

                 Los siguientes materiales están disponibles en los artifacts de este workflow:
                 - Código fuente final archivado
                 - Documentación técnica completa
                 - Materiales de handover
                 - Reportes finales y estadísticas

                 ### 📅 Timeline de Cierre

                 - **Hoy**: Documentación disponible para revisión
                 - **+3 días**: Deadline para aprobaciones de stakeholders
                 - **+7 días**: Handover técnico completado
                 - **+14 días**: Archivo final y liberación de recursos

                 **Closure Date**: ${new Date().toISOString().split('T')[0]}
                 **Artifacts**: Disponibles en [GitHub Actions](${context.payload.repository.html_url}/actions/runs/${context.runId})`,
                   labels: ['project-closure', 'action-required', 'stakeholders'],
                   assignees: ['tech-lead', 'product-owner'] // Adjust based on your team
                 });

                 // También podríamos enviar notificaciones a Slack, email, etc.
                 console.log(`Stakeholder notification issue created: ${issue.html_url}`);

         - name: Summary
           run: |
              echo "✅ Project closure automation completed successfully!"
              echo ""
              echo "📋 Generated artifacts:"
              echo "- Project statistics and metrics"
              echo "- Final code archive"
              echo "- Handover documentation"
              echo "- Emergency contacts and procedures"
              echo "- Executive summary"
              echo ""
              echo "🔗 All materials are available in the workflow artifacts."
              echo "📧 Stakeholders have been notified via GitHub issue."
              echo ""
              echo "🎉 Project is now officially ready for handover and closure!"
```

## Tips

### Documentación Completa

- **Exhaustividad**: Documenta todo aspecto relevante del proyecto
- **Accesibilidad**: Asegúrate de que la documentación sea fácil de encontrar y
  entender
- **Actualización**: Mantén toda la documentación actualizada hasta el final
- **Formato consistente**: Usa formatos estándar que el equipo de mantenimiento
  pueda manejar

### Proceso Estructurado

- **Checklist detallado**: Usa checklists para asegurar que no se omita nada
  importante
- **Aprobaciones formales**: Obtén sign-off formal de todos los stakeholders
  clave
- **Timeline claro**: Establece fechas específicas para cada fase del cierre
- **Handover gradual**: No hagas la transferencia de una sola vez, hazla
  gradualmente

### Preservación del Conocimiento

- **Múltiples formatos**: Documenta en varios formatos (texto, video, diagramas)
- **Contexto incluido**: No solo documentes qué, sino por qué se tomaron ciertas
  decisiones
- **Casos extremos**: Documenta casos edge y soluciones a problemas específicos
- **Contactos futuros**: Deja información de contacto para resolver dudas
  futuras

### Cierre Positivo

- **Reconocimiento**: Reconoce las contribuciones de todo el equipo
- **Celebración**: Organiza alguna forma de celebración del éxito
- **Reflexión**: Dedica tiempo a reflexionar sobre logros y aprendizajes
- **Closure emocional**: Permite que el equipo tenga un cierre emocional
  apropiado

## Navegación

[⬅️ Evaluación de Herramientas](./evaluacion-herramientas.md) |
[🏠 README Principal](../../README.md) |
[Métricas y Analytics de Uso ➡️](./metricas-analytics-uso.md)
