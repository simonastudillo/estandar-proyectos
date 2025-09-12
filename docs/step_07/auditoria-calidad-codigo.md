# Auditoría de Calidad de Código

## ¿Qué es?

La auditoría de calidad de código es un proceso sistemático de evaluación del
código fuente para identificar problemas de calidad, cumplimiento de estándares,
mantenibilidad, seguridad y rendimiento. Es una revisión exhaustiva que va más
allá del code review diario y proporciona una visión integral del estado del
proyecto.

## ¿Por qué es importante?

- **Evaluación objetiva**: Proporciona métricas cuantificables de la calidad del
  código
- **Identificación de riesgos**: Detecta problemas técnicos antes de que se
  conviertan en crisis
- **Planificación estratégica**: Ayuda a priorizar esfuerzos de mejora y
  refactoring
- **Cumplimiento**: Verifica adherencia a estándares y mejores prácticas
- **Reducción de costos**: Previene problemas costosos en producción
- **Toma de decisiones**: Proporciona datos para decisiones técnicas y de
  negocio
- **Baseline**: Establece línea base para mejoras continuas

## ¿Qué debe incluir?

### Dimensiones de Calidad

#### Mantenibilidad

- Legibilidad del código
- Complejidad ciclomática
- Acoplamiento entre módulos
- Cohesión interna
- Documentación del código

#### Confiabilidad

- Cobertura de pruebas
- Calidad de los tests
- Manejo de errores
- Robustez ante fallos
- Cumplimiento de contratos

#### Seguridad

- Vulnerabilidades conocidas
- Prácticas de seguridad
- Gestión de credenciales
- Validación de entradas
- Configuraciones seguras

#### Rendimiento

- Optimización de algoritmos
- Uso eficiente de memoria
- Operaciones de I/O
- Consultas de base de datos
- Carga de recursos

#### Escalabilidad

- Arquitectura modular
- Patrones de diseño
- Separación de responsabilidades
- Capacidad de extensión
- Configurabilidad

### Métricas Técnicas

#### Métricas de Código

- Líneas de código (LOC)
- Complejidad ciclomática
- Profundidad de herencia
- Acoplamiento aferente/eferente
- Índice de mantenibilidad

#### Métricas de Testing

- Cobertura de código
- Cobertura de ramas
- Número de tests por módulo
- Tiempo de ejecución de tests
- Tasa de fallos

#### Métricas de Calidad

- Duplicación de código
- Code smells detectados
- Violaciones de reglas
- Deuda técnica estimada
- Bugs reportados

## ¿Qué debo hacer?

### 1. Configurar herramientas de auditoría

#### SonarQube para análisis integral

```yaml
# docker-compose.yml para SonarQube
version: "3.8"
services:
   sonarqube:
      image: sonarqube:lts
      container_name: sonarqube
      depends_on:
         - sonarqube-db
      environment:
         SONAR_JDBC_URL: jdbc:postgresql://sonarqube-db:5432/sonar
         SONAR_JDBC_USERNAME: sonar
         SONAR_JDBC_PASSWORD: sonar
      volumes:
         - sonarqube_data:/opt/sonarqube/data
         - sonarqube_logs:/opt/sonarqube/logs
         - sonarqube_extensions:/opt/sonarqube/extensions
      ports:
         - "9000:9000"

   sonarqube-db:
      image: postgres:13
      container_name: sonarqube-db
      environment:
         POSTGRES_USER: sonar
         POSTGRES_PASSWORD: sonar
         POSTGRES_DB: sonar
      volumes:
         - postgresql:/var/lib/postgresql
         - postgresql_data:/var/lib/postgresql/data

volumes:
   sonarqube_data:
   sonarqube_logs:
   sonarqube_extensions:
   postgresql:
   postgresql_data:
```

#### Configuración de análisis

```properties
# sonar-project.properties
sonar.projectKey=mi-proyecto-audit
sonar.projectName=Mi Proyecto - Auditoría
sonar.projectVersion=1.0

# Source directories
sonar.sources=src,app
sonar.tests=src/__tests__,tests

# Exclusions
sonar.exclusions=**/node_modules/**,**/vendor/**,**/dist/**,**/build/**

# Language specific settings
sonar.php.coverage.reportPaths=coverage/clover.xml
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.typescript.lcov.reportPaths=coverage/lcov.info

# Quality Gate settings
sonar.qualitygate.wait=true

# Duplications
sonar.cpd.exclusions=**/*test*/**,**/*spec*/**

# Custom rules
sonar.issue.ignore.multicriteria=e1,e2
sonar.issue.ignore.multicriteria.e1.ruleKey=php:S1481
sonar.issue.ignore.multicriteria.e1.resourceKey=**/*Test.php
```

#### Code Climate para métricas de mantenibilidad

```yaml
# .codeclimate.yml
version: "2"
checks:
   argument-count:
      config:
         threshold: 4
   complex-logic:
      config:
         threshold: 4
   file-lines:
      config:
         threshold: 250
   method-complexity:
      config:
         threshold: 5
   method-count:
      config:
         threshold: 20
   method-lines:
      config:
         threshold: 25
   nested-control-flow:
      config:
         threshold: 4
   return-statements:
      config:
         threshold: 4
   similar-code:
      config:
         threshold: 70
   identical-code:
      config:
         threshold: 70

plugins:
   eslint:
      enabled: true
      config:
         config: .eslintrc.js
   phpmd:
      enabled: true
      config:
         file_extensions: "php"
   duplication:
      enabled: true
      config:
         languages:
            - typescript
            - php

exclude_patterns:
   - "tests/"
   - "spec/"
   - "**/vendor/"
   - "**/node_modules/"
```

### 2. Implementar sistema de métricas personalizado

#### Colector de métricas de código

```typescript
// scripts/code-audit/MetricsCollector.ts
import * as fs from "fs";
import * as path from "path";

export interface CodeMetrics {
   files: FileMetrics[];
   summary: SummaryMetrics;
   qualityGate: QualityGateResult;
}

export interface FileMetrics {
   path: string;
   lines: number;
   complexity: number;
   maintainabilityIndex: number;
   codeSmells: string[];
   testCoverage: number;
   duplications: number;
}

export interface SummaryMetrics {
   totalFiles: number;
   totalLines: number;
   averageComplexity: number;
   averageMaintainability: number;
   totalCodeSmells: number;
   overallCoverage: number;
   technicalDebt: {
      hours: number;
      cost: number;
   };
}

export interface QualityGateResult {
   passed: boolean;
   score: number;
   failedCriteria: string[];
   recommendations: string[];
}

export class CodeMetricsCollector {
   private readonly sourceDirectories: string[];
   private readonly qualityThresholds: QualityThresholds;

   constructor(sourceDirectories: string[], thresholds: QualityThresholds) {
      this.sourceDirectories = sourceDirectories;
      this.qualityThresholds = thresholds;
   }

   async collectMetrics(): Promise<CodeMetrics> {
      console.log("🔍 Iniciando recolección de métricas...");

      const files = this.getAllSourceFiles();
      const fileMetrics = await Promise.all(
         files.map((file) => this.analyzeFile(file)),
      );

      const summary = this.calculateSummary(fileMetrics);
      const qualityGate = this.evaluateQualityGate(summary);

      return {
         files: fileMetrics,
         summary,
         qualityGate,
      };
   }

   private getAllSourceFiles(): string[] {
      const files: string[] = [];

      for (const dir of this.sourceDirectories) {
         this.traverseDirectory(dir, files);
      }

      return files.filter(
         (file) =>
            file.endsWith(".ts") || file.endsWith(".tsx") ||
            file.endsWith(".php"),
      );
   }

   private traverseDirectory(dir: string, files: string[]): void {
      if (!fs.existsSync(dir)) return;

      const items = fs.readdirSync(dir);

      for (const item of items) {
         const fullPath = path.join(dir, item);
         const stat = fs.statSync(fullPath);

         if (stat.isDirectory() && !this.isExcludedDirectory(item)) {
            this.traverseDirectory(fullPath, files);
         } else if (stat.isFile()) {
            files.push(fullPath);
         }
      }
   }

   private isExcludedDirectory(dir: string): boolean {
      const excluded = ["node_modules", "vendor", "dist", "build", "coverage"];
      return excluded.includes(dir);
   }

   private async analyzeFile(filePath: string): Promise<FileMetrics> {
      const content = fs.readFileSync(filePath, "utf8");
      const lines = content.split("\n").length;

      return {
         path: filePath,
         lines,
         complexity: this.calculateComplexity(content),
         maintainabilityIndex: this.calculateMaintainabilityIndex(content),
         codeSmells: this.detectCodeSmells(content, filePath),
         testCoverage: await this.getTestCoverage(filePath),
         duplications: this.countDuplications(content),
      };
   }

   private calculateComplexity(content: string): number {
      // Implementación simplificada de complejidad ciclomática
      const complexityPatterns = [
         /if\s*\(/g,
         /else\s*if\s*\(/g,
         /while\s*\(/g,
         /for\s*\(/g,
         /forEach\s*\(/g,
         /switch\s*\(/g,
         /case\s+/g,
         /catch\s*\(/g,
         /&&/g,
         /\|\|/g,
         /\?.*:/g, // ternary operator
      ];

      let complexity = 1; // Base complexity

      complexityPatterns.forEach((pattern) => {
         const matches = content.match(pattern);
         if (matches) {
            complexity += matches.length;
         }
      });

      return complexity;
   }

   private calculateMaintainabilityIndex(content: string): number {
      // Simplified Maintainability Index calculation
      const lines = content.split("\n").length;
      const complexity = this.calculateComplexity(content);
      const halsteadVolume = this.calculateHalsteadVolume(content);

      // MI = MAX(0, (171 - 5.2 * ln(V) - 0.23 * G - 16.2 * ln(LOC)) * 100 / 171)
      const mi = Math.max(
         0,
         ((171 -
            5.2 * Math.log(halsteadVolume) -
            0.23 * complexity -
            16.2 * Math.log(lines)) *
            100) /
            171,
      );

      return Math.round(mi);
   }

   private calculateHalsteadVolume(content: string): number {
      // Simplified Halstead Volume calculation
      const operators = content.match(/[+\-*/=<>!&|?:]/g) || [];
      const operands = content.match(/\b[a-zA-Z_$][a-zA-Z0-9_$]*\b/g) || [];

      const uniqueOperators = new Set(operators).size;
      const uniqueOperands = new Set(operands).size;
      const totalOperators = operators.length;
      const totalOperands = operands.length;

      const vocabulary = uniqueOperators + uniqueOperands;
      const length = totalOperators + totalOperands;

      return length * Math.log2(vocabulary);
   }

   private detectCodeSmells(content: string, filePath: string): string[] {
      const smells: string[] = [];

      // Long Method
      const methods = this.extractMethods(content);
      methods.forEach((method, index) => {
         if (method.lines > 30) {
            smells.push(
               `Long Method: Method ${index + 1} has ${method.lines} lines`,
            );
         }
      });

      // Large Class
      if (content.split("\n").length > 500) {
         smells.push("Large Class: File exceeds 500 lines");
      }

      // Too Many Parameters
      const longParamMethods = content.match(/function\s+\w+\s*\([^)]{80,}\)/g);
      if (longParamMethods) {
         smells.push(
            `Long Parameter List: ${longParamMethods.length} methods with many parameters`,
         );
      }

      // Magic Numbers
      const magicNumbers = content.match(/\b(?<!const\s+\w+\s*=\s*)\d{2,}\b/g);
      if (magicNumbers && magicNumbers.length > 5) {
         smells.push(
            `Magic Numbers: ${magicNumbers.length} magic numbers found`,
         );
      }

      // Duplicate Code (simplified)
      const blocks = this.extractCodeBlocks(content);
      const duplicates = this.findDuplicateBlocks(blocks);
      if (duplicates.length > 0) {
         smells.push(`Duplicate Code: ${duplicates.length} duplicate blocks`);
      }

      return smells;
   }

   private extractMethods(content: string): { name: string; lines: number }[] {
      const methodPattern =
         /(function\s+\w+|const\s+\w+\s*=.*=>|class\s+\w+.*{)/g;
      const methods: { name: string; lines: number }[] = [];
      const lines = content.split("\n");

      let match;
      while ((match = methodPattern.exec(content)) !== null) {
         const startLine = content.substring(0, match.index).split("\n").length;
         const endLine = this.findMethodEnd(lines, startLine);

         methods.push({
            name: match[0],
            lines: endLine - startLine,
         });
      }

      return methods;
   }

   private findMethodEnd(lines: string[], startLine: number): number {
      let braceCount = 0;
      let inMethod = false;

      for (let i = startLine - 1; i < lines.length; i++) {
         const line = lines[i];

         if (line.includes("{")) {
            braceCount += (line.match(/{/g) || []).length;
            inMethod = true;
         }

         if (line.includes("}")) {
            braceCount -= (line.match(/}/g) || []).length;
         }

         if (inMethod && braceCount === 0) {
            return i + 1;
         }
      }

      return lines.length;
   }

   private extractCodeBlocks(content: string): string[] {
      // Extract blocks of code for duplication analysis
      const lines = content.split("\n");
      const blocks: string[] = [];

      for (let i = 0; i < lines.length - 5; i++) {
         const block = lines
            .slice(i, i + 5)
            .join("\n")
            .trim();
         if (block.length > 50) {
            blocks.push(block);
         }
      }

      return blocks;
   }

   private findDuplicateBlocks(blocks: string[]): string[] {
      const seen = new Set<string>();
      const duplicates: string[] = [];

      blocks.forEach((block) => {
         if (seen.has(block)) {
            duplicates.push(block);
         } else {
            seen.add(block);
         }
      });

      return duplicates;
   }

   private async getTestCoverage(filePath: string): Promise<number> {
      // This would integrate with actual coverage reports
      // For now, return mock data
      const isTestFile = filePath.includes("test") || filePath.includes("spec");
      return isTestFile ? 100 : Math.random() * 100;
   }

   private countDuplications(content: string): number {
      // Simplified duplication count
      const blocks = this.extractCodeBlocks(content);
      const duplicates = this.findDuplicateBlocks(blocks);
      return duplicates.length;
   }

   private calculateSummary(fileMetrics: FileMetrics[]): SummaryMetrics {
      const totalFiles = fileMetrics.length;
      const totalLines = fileMetrics.reduce((sum, file) => sum + file.lines, 0);
      const averageComplexity =
         fileMetrics.reduce((sum, file) => sum + file.complexity, 0) /
         totalFiles;
      const averageMaintainability =
         fileMetrics.reduce((sum, file) => sum + file.maintainabilityIndex, 0) /
         totalFiles;
      const totalCodeSmells = fileMetrics.reduce(
         (sum, file) => sum + file.codeSmells.length,
         0,
      );
      const overallCoverage =
         fileMetrics.reduce((sum, file) => sum + file.testCoverage, 0) /
         totalFiles;

      // Technical debt calculation (simplified)
      const debtHours = Math.round(
         totalCodeSmells * 0.5 + (100 - overallCoverage) * 0.1,
      );
      const debtCost = debtHours * 50; // $50/hour average

      return {
         totalFiles,
         totalLines,
         averageComplexity: Math.round(averageComplexity * 100) / 100,
         averageMaintainability: Math.round(averageMaintainability),
         totalCodeSmells,
         overallCoverage: Math.round(overallCoverage * 100) / 100,
         technicalDebt: {
            hours: debtHours,
            cost: debtCost,
         },
      };
   }

   private evaluateQualityGate(summary: SummaryMetrics): QualityGateResult {
      const failedCriteria: string[] = [];
      let score = 100;

      // Check coverage threshold
      if (summary.overallCoverage < this.qualityThresholds.minCoverage) {
         failedCriteria.push(
            `Coverage below ${this.qualityThresholds.minCoverage}%`,
         );
         score -= 20;
      }

      // Check complexity threshold
      if (summary.averageComplexity > this.qualityThresholds.maxComplexity) {
         failedCriteria.push(
            `Complexity above ${this.qualityThresholds.maxComplexity}`,
         );
         score -= 15;
      }

      // Check maintainability threshold
      if (
         summary.averageMaintainability <
            this.qualityThresholds.minMaintainability
      ) {
         failedCriteria.push(
            `Maintainability below ${this.qualityThresholds.minMaintainability}`,
         );
         score -= 15;
      }

      // Check code smells threshold
      const smellRatio = summary.totalCodeSmells / summary.totalFiles;
      if (smellRatio > this.qualityThresholds.maxCodeSmellsPerFile) {
         failedCriteria.push(
            `Too many code smells per file (${smellRatio.toFixed(1)})`,
         );
         score -= 10;
      }

      const recommendations = this.generateRecommendations(
         summary,
         failedCriteria,
      );

      return {
         passed: failedCriteria.length === 0,
         score: Math.max(0, score),
         failedCriteria,
         recommendations,
      };
   }

   private generateRecommendations(
      summary: SummaryMetrics,
      failedCriteria: string[],
   ): string[] {
      const recommendations: string[] = [];

      if (summary.overallCoverage < 80) {
         recommendations.push(
            "Incrementar cobertura de tests escribiendo más pruebas unitarias",
         );
      }

      if (summary.averageComplexity > 10) {
         recommendations.push(
            "Refactorizar métodos complejos dividiendo en funciones más pequeñas",
         );
      }

      if (summary.averageMaintainability < 70) {
         recommendations.push(
            "Mejorar legibilidad del código y añadir documentación",
         );
      }

      if (summary.totalCodeSmells > 0) {
         recommendations.push(
            "Abordar code smells identificados siguiendo principios SOLID",
         );
      }

      if (summary.technicalDebt.hours > 40) {
         recommendations.push(
            "Planificar sprints de refactoring para reducir deuda técnica",
         );
      }

      return recommendations;
   }
}

export interface QualityThresholds {
   minCoverage: number;
   maxComplexity: number;
   minMaintainability: number;
   maxCodeSmellsPerFile: number;
}
```

### 3. Generar reportes de auditoría

#### Generador de reportes HTML

```typescript
// scripts/code-audit/ReportGenerator.ts
import * as fs from "fs";
import * as path from "path";
import { CodeMetrics } from "./MetricsCollector";

export class AuditReportGenerator {
   static generateHTMLReport(metrics: CodeMetrics, outputPath: string): void {
      const html = this.createHTMLReport(metrics);
      fs.writeFileSync(outputPath, html);
      console.log(`📊 Reporte generado: ${outputPath}`);
   }

   static generateJSONReport(metrics: CodeMetrics, outputPath: string): void {
      const json = JSON.stringify(metrics, null, 2);
      fs.writeFileSync(outputPath, json);
      console.log(`📋 Datos JSON generados: ${outputPath}`);
   }

   static generateMarkdownReport(
      metrics: CodeMetrics,
      outputPath: string,
   ): void {
      const markdown = this.createMarkdownReport(metrics);
      fs.writeFileSync(outputPath, markdown);
      console.log(`📝 Reporte Markdown generado: ${outputPath}`);
   }

   private static createHTMLReport(metrics: CodeMetrics): string {
      const { summary, qualityGate } = metrics;

      return `
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Auditoría de Calidad de Código</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif; margin: 0; padding: 20px; background: #f5f5f5; }
        .container { max-width: 1200px; margin: 0 auto; background: white; border-radius: 8px; overflow: hidden; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .header { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; padding: 30px; text-align: center; }
        .header h1 { margin: 0; font-size: 2.5em; }
        .header p { margin: 10px 0 0 0; opacity: 0.9; }
        .content { padding: 30px; }
        .metrics-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 20px; margin-bottom: 30px; }
        .metric-card { background: #f8f9fa; padding: 20px; border-radius: 8px; border-left: 4px solid #007bff; }
        .metric-value { font-size: 2em; font-weight: bold; color: #007bff; }
        .metric-label { color: #6c757d; margin-top: 5px; }
        .quality-gate { padding: 20px; border-radius: 8px; margin-bottom: 30px; }
        .quality-gate.passed { background: #d4edda; border: 1px solid #c3e6cb; color: #155724; }
        .quality-gate.failed { background: #f8d7da; border: 1px solid #f5c6cb; color: #721c24; }
        .files-table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        .files-table th, .files-table td { padding: 12px; text-align: left; border-bottom: 1px solid #dee2e6; }
        .files-table th { background: #f8f9fa; font-weight: 600; }
        .chart-container { width: 100%; height: 300px; margin: 20px 0; }
        .recommendations { background: #fff3cd; border: 1px solid #ffeaa7; border-radius: 8px; padding: 20px; margin-top: 20px; }
        .recommendation-item { margin: 10px 0; padding-left: 20px; position: relative; }
        .recommendation-item:before { content: "💡"; position: absolute; left: 0; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Auditoría de Calidad de Código</h1>
            <p>Generado el ${
         new Date().toLocaleDateString("es-ES", {
            year: "numeric",
            month: "long",
            day: "numeric",
            hour: "2-digit",
            minute: "2-digit",
         })
      }</p>
        </div>
        
        <div class="content">
            <div class="quality-gate ${
         qualityGate.passed ? "passed" : "failed"
      }">
                <h2>${
         qualityGate.passed
            ? "✅ Quality Gate: PASSED"
            : "❌ Quality Gate: FAILED"
      }</h2>
                <p><strong>Score:</strong> ${qualityGate.score}/100</p>
                ${
         qualityGate.failedCriteria.length > 0
            ? `
                <h3>Criterios No Cumplidos:</h3>
                <ul>
                    ${
               qualityGate.failedCriteria
                  .map((criteria) => `<li>${criteria}</li>`)
                  .join("")
            }
                </ul>
                `
            : ""
      }
            </div>

            <h2>📊 Métricas Generales</h2>
            <div class="metrics-grid">
                <div class="metric-card">
                    <div class="metric-value">${summary.totalFiles}</div>
                    <div class="metric-label">Archivos Analizados</div>
                </div>
                <div class="metric-card">
                    <div class="metric-value">${summary.totalLines.toLocaleString()}</div>
                    <div class="metric-label">Líneas de Código</div>
                </div>
                <div class="metric-card">
                    <div class="metric-value">${
         summary.overallCoverage.toFixed(
            1,
         )
      }%</div>
                    <div class="metric-label">Cobertura de Tests</div>
                </div>
                <div class="metric-card">
                    <div class="metric-value">${summary.averageComplexity}</div>
                    <div class="metric-label">Complejidad Promedio</div>
                </div>
                <div class="metric-card">
                    <div class="metric-value">${summary.averageMaintainability}</div>
                    <div class="metric-label">Índice de Mantenibilidad</div>
                </div>
                <div class="metric-card">
                    <div class="metric-value">${summary.totalCodeSmells}</div>
                    <div class="metric-label">Code Smells Detectados</div>
                </div>
            </div>

            <h2>💰 Deuda Técnica</h2>
            <div class="metrics-grid">
                <div class="metric-card">
                    <div class="metric-value">${summary.technicalDebt.hours}h</div>
                    <div class="metric-label">Tiempo Estimado</div>
                </div>
                <div class="metric-card">
                    <div class="metric-value">$${summary.technicalDebt.cost.toLocaleString()}</div>
                    <div class="metric-label">Costo Estimado</div>
                </div>
            </div>

            ${
         qualityGate.recommendations.length > 0
            ? `
            <div class="recommendations">
                <h3>💡 Recomendaciones</h3>
                ${
               qualityGate.recommendations
                  .map(
                     (rec) => `
                <div class="recommendation-item">${rec}</div>
                `,
                  )
                  .join("")
            }
            </div>
            `
            : ""
      }

            <h2>📁 Análisis por Archivo</h2>
            <table class="files-table">
                <thead>
                    <tr>
                        <th>Archivo</th>
                        <th>Líneas</th>
                        <th>Complejidad</th>
                        <th>Mantenibilidad</th>
                        <th>Cobertura</th>
                        <th>Code Smells</th>
                    </tr>
                </thead>
                <tbody>
                    ${
         metrics.files
            .slice(0, 20)
            .map(
               (file) => `
                    <tr>
                        <td>${file.path.split("/").pop()}</td>
                        <td>${file.lines}</td>
                        <td>${file.complexity}</td>
                        <td>${file.maintainabilityIndex}</td>
                        <td>${file.testCoverage.toFixed(1)}%</td>
                        <td>${file.codeSmells.length}</td>
                    </tr>
                    `,
            )
            .join("")
      }
                </tbody>
            </table>
            ${
         metrics.files.length > 20
            ? `<p><em>Mostrando solo los primeros 20 archivos de ${metrics.files.length} analizados.</em></p>`
            : ""
      }
        </div>
    </div>
</body>
</html>
    `.trim();
   }

   private static createMarkdownReport(metrics: CodeMetrics): string {
      const { summary, qualityGate } = metrics;

      return `
# Auditoría de Calidad de Código

**Generado el:** ${
         new Date().toLocaleDateString("es-ES", {
            year: "numeric",
            month: "long",
            day: "numeric",
            hour: "2-digit",
            minute: "2-digit",
         })
      }

## Quality Gate

${
         qualityGate.passed
            ? "✅ **PASSED**"
            : "❌ **FAILED**"
      } - Score: ${qualityGate.score}/100

${
         qualityGate.failedCriteria.length > 0
            ? `
### Criterios No Cumplidos
${qualityGate.failedCriteria.map((criteria) => `- ${criteria}`).join("\n")}
`
            : ""
      }

## Métricas Generales

| Métrica | Valor |
|---------|-------|
| Archivos Analizados | ${summary.totalFiles} |
| Líneas de Código | ${summary.totalLines.toLocaleString()} |
| Cobertura de Tests | ${summary.overallCoverage.toFixed(1)}% |
| Complejidad Promedio | ${summary.averageComplexity} |
| Índice de Mantenibilidad | ${summary.averageMaintainability} |
| Code Smells | ${summary.totalCodeSmells} |

## Deuda Técnica

- **Tiempo Estimado:** ${summary.technicalDebt.hours} horas
- **Costo Estimado:** $${summary.technicalDebt.cost.toLocaleString()}

${
         qualityGate.recommendations.length > 0
            ? `
## Recomendaciones

${qualityGate.recommendations.map((rec) => `- ${rec}`).join("\n")}
`
            : ""
      }

## Top 10 Archivos por Complejidad

| Archivo | Líneas | Complejidad | Mantenibilidad | Code Smells |
|---------|--------|-------------|----------------|-------------|
${
         metrics.files
            .sort((a, b) => b.complexity - a.complexity)
            .slice(0, 10)
            .map(
               (file) =>
                  `| ${
                     file.path.split("/").pop()
                  } | ${file.lines} | ${file.complexity} | ${file.maintainabilityIndex} | ${file.codeSmells.length} |`,
            )
            .join("\n")
      }

## Archivos con Más Code Smells

${
         metrics.files
            .filter((file) => file.codeSmells.length > 0)
            .sort((a, b) => b.codeSmells.length - a.codeSmells.length)
            .slice(0, 5)
            .map(
               (file) => `
### ${file.path.split("/").pop()}
${file.codeSmells.map((smell) => `- ${smell}`).join("\n")}
`,
            )
            .join("\n")
      }
    `.trim();
   }
}
```

### 4. Automatizar auditorías periódicas

#### Script principal de auditoría

```typescript
// scripts/code-audit/runAudit.ts
import { CodeMetricsCollector, QualityThresholds } from "./MetricsCollector";
import { AuditReportGenerator } from "./ReportGenerator";

async function runCodeAudit(): Promise<void> {
   console.log("🔍 Iniciando auditoría de calidad de código...");

   const sourceDirectories = ["src", "app"];
   const qualityThresholds: QualityThresholds = {
      minCoverage: 80,
      maxComplexity: 10,
      minMaintainability: 70,
      maxCodeSmellsPerFile: 5,
   };

   const collector = new CodeMetricsCollector(
      sourceDirectories,
      qualityThresholds,
   );

   try {
      const metrics = await collector.collectMetrics();

      const timestamp = new Date().toISOString().split("T")[0];
      const outputDir = `audit-reports/${timestamp}`;

      // Crear directorio de reportes
      await fs.mkdir(outputDir, { recursive: true });

      // Generar reportes en múltiples formatos
      AuditReportGenerator.generateHTMLReport(
         metrics,
         `${outputDir}/audit-report.html`,
      );

      AuditReportGenerator.generateMarkdownReport(
         metrics,
         `${outputDir}/audit-report.md`,
      );

      AuditReportGenerator.generateJSONReport(
         metrics,
         `${outputDir}/audit-data.json`,
      );

      // Mostrar resumen en consola
      console.log("\n📊 Resumen de Auditoría:");
      console.log(
         `Quality Gate: ${
            metrics.qualityGate.passed ? "✅ PASSED" : "❌ FAILED"
         }`,
      );
      console.log(`Score: ${metrics.qualityGate.score}/100`);
      console.log(`Archivos analizados: ${metrics.summary.totalFiles}`);
      console.log(`Cobertura: ${metrics.summary.overallCoverage.toFixed(1)}%`);
      console.log(
         `Deuda técnica: ${metrics.summary.technicalDebt.hours}h ($${metrics.summary.technicalDebt.cost})`,
      );

      if (!metrics.qualityGate.passed) {
         console.log("\n❌ Criterios no cumplidos:");
         metrics.qualityGate.failedCriteria.forEach((criteria) => {
            console.log(`  - ${criteria}`);
         });

         process.exit(1); // Fallar CI/CD si no pasa quality gate
      }

      console.log(`\n📁 Reportes generados en: ${outputDir}/`);
   } catch (error) {
      console.error("❌ Error durante la auditoría:", error);
      process.exit(1);
   }
}

// Ejecutar si es llamado directamente
if (require.main === module) {
   runCodeAudit();
}
```

### 5. Integración con CI/CD

```yaml
# .github/workflows/code-audit.yml
name: Code Quality Audit

on:
   schedule:
      - cron: "0 2 * * 1" # Lunes a las 2 AM
   workflow_dispatch: # Permite ejecución manual
   push:
      branches: [main]
      paths: ["src/**", "app/**"]

jobs:
   code-audit:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
           with:
              fetch-depth: 0 # Necesario para análisis histórico

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"

         - name: Install dependencies
           run: |
              npm ci
              composer install

         - name: Run tests with coverage
           run: |
              npm run test:coverage
              ./vendor/bin/phpunit --coverage-clover coverage.xml

         - name: Run SonarQube analysis
           uses: sonarqube-quality-gate-action@master
           env:
              GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
              SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

         - name: Run custom code audit
           run: |
              npm run audit:code

         - name: Upload audit reports
           uses: actions/upload-artifact@v3
           with:
              name: audit-reports
              path: audit-reports/

         - name: Comment on PR with audit results
           if: github.event_name == 'pull_request'
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const auditReport = fs.readFileSync('audit-reports/*/audit-report.md', 'utf8');

                 github.rest.issues.createComment({
                   issue_number: context.issue.number,
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   body: `## 🔍 Code Quality Audit Results\n\n${auditReport}`
                 });

         - name: Create audit tracking issue
           if: failure()
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const auditData = JSON.parse(fs.readFileSync('audit-reports/*/audit-data.json', 'utf8'));

                 const issueBody = `
                 # Code Quality Audit Failed

                 **Date:** ${new Date().toISOString()}
                 **Quality Gate Score:** ${auditData.qualityGate.score}/100

                 ## Failed Criteria
                 ${auditData.qualityGate.failedCriteria.map(c => `- ${c}`).join('\n')}

                 ## Recommendations
                 ${auditData.qualityGate.recommendations.map(r => `- ${r}`).join('\n')}

                 ## Technical Debt
                 - **Hours:** ${auditData.summary.technicalDebt.hours}
                 - **Cost:** $${auditData.summary.technicalDebt.cost}
                 `;

                 github.rest.issues.create({
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   title: `Code Quality Audit Failed - ${new Date().toISOString().split('T')[0]}`,
                   body: issueBody,
                   labels: ['technical-debt', 'quality', 'audit']
                 });
```

### 6. Dashboard de métricas históricas

```typescript
// scripts/code-audit/HistoricalAnalysis.ts
import * as fs from "fs";
import * as path from "path";

export interface HistoricalMetrics {
   date: string;
   score: number;
   coverage: number;
   complexity: number;
   maintainability: number;
   technicalDebt: number;
   codeSmells: number;
}

export class HistoricalAnalysis {
   static generateTrendReport(auditReportsDir: string): void {
      const historical = this.loadHistoricalData(auditReportsDir);
      const trends = this.calculateTrends(historical);

      const reportHtml = this.generateTrendHTML(historical, trends);
      fs.writeFileSync(path.join(auditReportsDir, "trends.html"), reportHtml);

      console.log("📈 Reporte de tendencias generado: trends.html");
   }

   private static loadHistoricalData(reportsDir: string): HistoricalMetrics[] {
      const historical: HistoricalMetrics[] = [];

      const reportDirs = fs
         .readdirSync(reportsDir)
         .filter((dir) => dir.match(/\d{4}-\d{2}-\d{2}/))
         .sort();

      for (const dir of reportDirs) {
         const jsonPath = path.join(reportsDir, dir, "audit-data.json");
         if (fs.existsSync(jsonPath)) {
            const data = JSON.parse(fs.readFileSync(jsonPath, "utf8"));

            historical.push({
               date: dir,
               score: data.qualityGate.score,
               coverage: data.summary.overallCoverage,
               complexity: data.summary.averageComplexity,
               maintainability: data.summary.averageMaintainability,
               technicalDebt: data.summary.technicalDebt.hours,
               codeSmells: data.summary.totalCodeSmells,
            });
         }
      }

      return historical;
   }

   private static calculateTrends(data: HistoricalMetrics[]): any {
      if (data.length < 2) return null;

      const latest = data[data.length - 1];
      const previous = data[data.length - 2];

      return {
         score: latest.score - previous.score,
         coverage: latest.coverage - previous.coverage,
         complexity: latest.complexity - previous.complexity,
         maintainability: latest.maintainability - previous.maintainability,
         technicalDebt: latest.technicalDebt - previous.technicalDebt,
         codeSmells: latest.codeSmells - previous.codeSmells,
      };
   }

   private static generateTrendHTML(
      historical: HistoricalMetrics[],
      trends: any,
   ): string {
      return `
<!DOCTYPE html>
<html>
<head>
    <title>Code Quality Trends</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .chart-container { width: 100%; height: 400px; margin: 20px 0; }
        .trend-indicator { display: inline-block; padding: 4px 8px; border-radius: 4px; color: white; margin-left: 10px; }
        .positive { background-color: #28a745; }
        .negative { background-color: #dc3545; }
        .neutral { background-color: #6c757d; }
    </style>
</head>
<body>
    <h1>Code Quality Trends</h1>
    
    <div class="chart-container">
        <canvas id="qualityChart"></canvas>
    </div>
    
    <script>
        const ctx = document.getElementById('qualityChart').getContext('2d');
        const chart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: ${JSON.stringify(historical.map((d) => d.date))},
                datasets: [
                    {
                        label: 'Quality Score',
                        data: ${JSON.stringify(historical.map((d) => d.score))},
                        borderColor: 'rgb(75, 192, 192)',
                        tension: 0.1
                    },
                    {
                        label: 'Coverage %',
                        data: ${
         JSON.stringify(
            historical.map((d) => d.coverage),
         )
      },
                        borderColor: 'rgb(54, 162, 235)',
                        tension: 0.1
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    y: {
                        beginAtZero: true,
                        max: 100
                    }
                }
            }
        });
    </script>
</body>
</html>
    `;
   }
}
```

## Tips

- **Automatización**: Ejecuta auditorías de forma automática y regular
- **Baseline**: Establece métricas base antes de comenzar mejoras
- **Incremental**: Enfócate en mejoras graduales, no cambios drásticos
- **Priorización**: Usa los resultados para priorizar refactoring
- **Contexto**: Considera el contexto del negocio al interpretar métricas
- **Evolución**: Las métricas deben evolucionar con el proyecto
- **Acción**: Los reportes deben llevar a acciones concretas
- **Comunicación**: Comparte resultados con todo el equipo

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
- ✅ [Testing de Usabilidad](./testing-usabilidad.md)
- ✅ [Code Review y Refactoring](./code-review-refactoring.md)
- ✅ **Auditoría de Calidad de Código** ← Estás aquí
- ⏭️ [Checklist Específico de Performance](./checklist-performance.md)
- ⏭️ [Revisión General del Código](./revision-general-codigo.md)
- ⏭️ [Optimización de Recursos](./optimizacion-recursos.md)

---

### Siguiente Paso

Continúa con
[**Checklist Específico de Performance**](./checklist-performance.md)

[⬅️ Code Review y Refactoring](./code-review-refactoring.md) |
[🏠 README Principal](../../README.md) |
[➡️ Checklist Específico de Performance](./checklist-performance.md)
