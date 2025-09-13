# Revisión General del Código

## ¿Qué es?

La revisión general del código es un proceso sistemático de análisis y
evaluación del código fuente para identificar oportunidades de optimización de
performance, mejoras de mantenibilidad, detección de code smells y validación
del cumplimiento de estándares de calidad. Se enfoca en el análisis holístico
del codebase para mejorar la eficiencia general del sistema.

## ¿Por qué es importante?

- **Performance optimizada**: Identifica cuellos de botella y código ineficiente
- **Mantenibilidad**: Mejora la legibilidad y estructura del código
- **Escalabilidad**: Detecta patrones que pueden causar problemas de escala
- **Calidad del código**: Garantiza adherencia a best practices y estándares
- **Reducción de deuda técnica**: Identifica y prioriza refactoring necesario
- **Prevención de bugs**: Detecta patrones propensos a errores
- **Consistencia**: Asegura coherencia en todo el codebase

## ¿Qué debe incluir?

### Análisis de Complejidad

#### Complejidad Ciclomática

- Medición de complejidad de funciones y métodos
- Identificación de funciones con alta complejidad
- Refactoring para reducir complejidad

#### Análisis de Dependencias

- Mapeo de dependencias entre módulos
- Detección de dependencias circulares
- Evaluación del acoplamiento

### Code Smells y Anti-patrones

#### Code Smells Comunes

- Métodos y clases demasiado grandes
- Duplicación de código
- Comentarios excesivos o innecesarios
- Nombres poco descriptivos

#### Anti-patrones de Performance

- Queries N+1
- Cargas innecesarias de datos
- Uso ineficiente de memoria
- Algoritmos de complejidad alta

### Estándares de Calidad

#### Convenciones de Código

- Naming conventions
- Estructura de archivos y carpetas
- Documentación y comentarios
- Tipado y validaciones

#### Best Practices

- SOLID principles
- DRY (Don't Repeat Yourself)
- YAGNI (You Aren't Gonna Need It)
- Clean Code principles

## ¿Qué debo hacer?

### 1. Configurar herramientas de análisis automático

#### ESLint y Prettier para Frontend

```bash
# Instalar dependencias
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
npm install --save-dev eslint-plugin-react eslint-plugin-react-hooks

# Configurar ESLint
cat > .eslintrc.js << 'EOF'
module.exports = {
  parser: '@typescript-eslint/parser',
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier'
  ],
  plugins: ['@typescript-eslint', 'react', 'react-hooks'],
  rules: {
    // Performance rules
    'react-hooks/exhaustive-deps': 'error',
    'react/no-unused-prop-types': 'error',
    'react/no-unused-state': 'error',
    
    // Code quality rules
    'complexity': ['error', 10],
    'max-depth': ['error', 4],
    'max-lines-per-function': ['error', 50],
    'max-params': ['error', 4],
    
    // TypeScript specific
    '@typescript-eslint/no-any': 'error',
    '@typescript-eslint/explicit-function-return-type': 'warn',
    '@typescript-eslint/no-unused-vars': 'error'
  },
  settings: {
    react: {
      version: 'detect'
    }
  }
};
EOF

# Configurar Prettier
cat > .prettierrc << 'EOF'
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false
}
EOF
```

#### PHP CodeSniffer y PHPStan para Backend

```bash
# Instalar dependencias via Composer
composer require --dev squizlabs/php_codesniffer
composer require --dev phpstan/phpstan
composer require --dev larastan/larastan

# Configurar PHP CodeSniffer
cat > phpcs.xml << 'EOF'
<?xml version="1.0"?>
<ruleset name="Laravel Standards">
    <description>Laravel coding standards</description>
    
    <file>app/</file>
    <file>config/</file>
    <file>routes/</file>
    
    <exclude-pattern>*/vendor/*</exclude-pattern>
    <exclude-pattern>*/storage/*</exclude-pattern>
    <exclude-pattern>*/bootstrap/cache/*</exclude-pattern>
    
    <rule ref="PSR12"/>
    
    <!-- Performance rules -->
    <rule ref="Generic.PHP.NoSilencedErrors"/>
    <rule ref="Generic.CodeAnalysis.UnusedFunctionParameter"/>
    <rule ref="Generic.CodeAnalysis.EmptyStatement"/>
    
    <!-- Complexity rules -->
    <rule ref="Generic.Metrics.CyclomaticComplexity">
        <properties>
            <property name="complexity" value="10"/>
            <property name="absoluteComplexity" value="15"/>
        </properties>
    </rule>
    
    <rule ref="Generic.Metrics.NestingLevel">
        <properties>
            <property name="nestingLevel" value="5"/>
            <property name="absoluteNestingLevel" value="10"/>
        </properties>
    </rule>
</ruleset>
EOF

# Configurar PHPStan
cat > phpstan.neon << 'EOF'
parameters:
    level: 8
    paths:
        - app
        - config
        - routes
    excludePaths:
        - app/Console/Kernel.php
        - app/Http/Kernel.php
    checkMissingIterableValueType: false
    checkGenericClassInNonGenericObjectType: false
    
    # Performance checks
    reportUnmatchedIgnoredErrors: true
    checkTooWideReturnTypesInProtectedAndPublicMethods: true
    checkUninitializedProperties: true
    
includes:
    - ./vendor/larastan/larastan/extension.neon
EOF
```

### 2. Script de análisis automatizado

```bash
#!/bin/bash
# scripts/code-review-analysis.sh

echo "🔍 Code Review Analysis"
echo "======================"

REPORT_DIR="reports/code-analysis-$(date +%Y%m%d-%H%M%S)"
mkdir -p $REPORT_DIR

# Frontend Analysis
echo "🎨 Analyzing Frontend Code..."

echo "1. 📊 ESLint Analysis"
npx eslint src/ --ext .ts,.tsx --format json > $REPORT_DIR/eslint-report.json
npx eslint src/ --ext .ts,.tsx > $REPORT_DIR/eslint-report.txt

echo "2. 📈 TypeScript Compilation Check"
npx tsc --noEmit --skipLibCheck > $REPORT_DIR/typescript-errors.txt 2>&1

echo "3. 🔍 Bundle Analysis"
npm run build -- --analyze > $REPORT_DIR/bundle-analysis.txt 2>&1

# Backend Analysis
echo "🔧 Analyzing Backend Code..."

echo "4. 🐘 PHP CodeSniffer"
./vendor/bin/phpcs --report=json > $REPORT_DIR/phpcs-report.json
./vendor/bin/phpcs > $REPORT_DIR/phpcs-report.txt

echo "5. 🔬 PHPStan Analysis"
./vendor/bin/phpstan analyse --error-format=json > $REPORT_DIR/phpstan-report.json
./vendor/bin/phpstan analyse > $REPORT_DIR/phpstan-report.txt

echo "6. 📊 PHP Metrics"
php scripts/php-metrics-analyzer.php > $REPORT_DIR/php-metrics.json

# Generate consolidated report
echo "📋 Generating Consolidated Report..."
php scripts/generate-code-review-report.php $REPORT_DIR

echo "✅ Code review analysis complete!"
echo "📁 Reports saved to: $REPORT_DIR"
echo "📊 Main report: $REPORT_DIR/consolidated-report.html"
```

### 3. Analizador de métricas PHP

```php
<?php
// scripts/php-metrics-analyzer.php

class PHPMetricsAnalyzer
{
    private array $results = [];
    private array $issues = [];

    public function analyze(): array
    {
        echo "🔍 PHP Code Metrics Analysis\n";
        echo "=============================\n\n";

        $this->analyzeComplexity();
        $this->analyzeDuplication();
        $this->analyzeClassStructure();
        $this->analyzePerformancePatterns();
        $this->generateRecommendations();

        return [
            'metrics' => $this->results,
            'issues' => $this->issues,
            'timestamp' => now()->toISOString()
        ];
    }

    private function analyzeComplexity(): void
    {
        echo "1. 📊 Complexity Analysis\n";

        $files = $this->getPhpFiles('app/');
        $complexityIssues = [];

        foreach ($files as $file) {
            $complexity = $this->calculateCyclomaticComplexity($file);
            $methods = $this->extractMethods($file);

            foreach ($methods as $method) {
                $methodComplexity = $this->calculateMethodComplexity($file, $method);
                
                if ($methodComplexity > 10) {
                    $complexityIssues[] = [
                        'file' => $file,
                        'method' => $method,
                        'complexity' => $methodComplexity,
                        'severity' => $methodComplexity > 15 ? 'high' : 'medium'
                    ];
                }
            }

            $this->results['complexity'][$file] = $complexity;
        }

        if (!empty($complexityIssues)) {
            $this->issues['complexity'] = $complexityIssues;
            echo "   ⚠️  Found " . count($complexityIssues) . " high complexity methods\n";
        } else {
            echo "   ✅ All methods within complexity limits\n";
        }
    }

    private function analyzeDuplication(): void
    {
        echo "\n2. 🔄 Code Duplication Analysis\n";

        $duplications = $this->detectDuplication();
        
        if (!empty($duplications)) {
            $this->issues['duplication'] = $duplications;
            echo "   ⚠️  Found " . count($duplications) . " code duplications\n";
            
            foreach (array_slice($duplications, 0, 3) as $dup) {
                echo "   📁 {$dup['file1']} <-> {$dup['file2']} ({$dup['lines']} lines)\n";
            }
        } else {
            echo "   ✅ No significant code duplication detected\n";
        }

        $this->results['duplication_percentage'] = $this->calculateDuplicationPercentage();
    }

    private function analyzeClassStructure(): void
    {
        echo "\n3. 🏗️  Class Structure Analysis\n";

        $classes = $this->getAllClasses();
        $structureIssues = [];

        foreach ($classes as $class) {
            $analysis = $this->analyzeClass($class);

            // Check for god classes
            if ($analysis['methods_count'] > 20) {
                $structureIssues[] = [
                    'type' => 'god_class',
                    'class' => $class,
                    'methods_count' => $analysis['methods_count'],
                    'recommendation' => 'Consider splitting into smaller classes'
                ];
            }

            // Check for data classes
            if ($analysis['methods_count'] < 3 && $analysis['properties_count'] > 5) {
                $structureIssues[] = [
                    'type' => 'data_class',
                    'class' => $class,
                    'recommendation' => 'Consider adding behavior methods or using DTOs'
                ];
            }

            $this->results['classes'][$class] = $analysis;
        }

        if (!empty($structureIssues)) {
            $this->issues['structure'] = $structureIssues;
            echo "   ⚠️  Found " . count($structureIssues) . " structure issues\n";
        } else {
            echo "   ✅ Class structure looks good\n";
        }
    }

    private function analyzePerformancePatterns(): void
    {
        echo "\n4. ⚡ Performance Pattern Analysis\n";

        $performanceIssues = [];

        // Check for N+1 query patterns
        $nPlusOneIssues = $this->detectNPlusOnePatterns();
        if (!empty($nPlusOneIssues)) {
            $performanceIssues = array_merge($performanceIssues, $nPlusOneIssues);
        }

        // Check for inefficient loops
        $loopIssues = $this->detectInefficientLoops();
        if (!empty($loopIssues)) {
            $performanceIssues = array_merge($performanceIssues, $loopIssues);
        }

        // Check for memory-intensive operations
        $memoryIssues = $this->detectMemoryIssues();
        if (!empty($memoryIssues)) {
            $performanceIssues = array_merge($performanceIssues, $memoryIssues);
        }

        if (!empty($performanceIssues)) {
            $this->issues['performance'] = $performanceIssues;
            echo "   ⚠️  Found " . count($performanceIssues) . " performance issues\n";
        } else {
            echo "   ✅ No obvious performance issues detected\n";
        }
    }

    private function generateRecommendations(): void
    {
        echo "\n5. 💡 Generating Recommendations\n";

        $recommendations = [];

        // Complexity recommendations
        if (isset($this->issues['complexity'])) {
            $recommendations[] = [
                'category' => 'complexity',
                'priority' => 'high',
                'message' => 'Refactor high complexity methods using Extract Method pattern'
            ];
        }

        // Duplication recommendations
        if (isset($this->issues['duplication'])) {
            $recommendations[] = [
                'category' => 'duplication',
                'priority' => 'medium',
                'message' => 'Extract common code into shared utilities or traits'
            ];
        }

        // Performance recommendations
        if (isset($this->issues['performance'])) {
            $recommendations[] = [
                'category' => 'performance',
                'priority' => 'high',
                'message' => 'Optimize database queries and implement caching strategies'
            ];
        }

        $this->results['recommendations'] = $recommendations;

        if (!empty($recommendations)) {
            echo "   📋 Generated " . count($recommendations) . " recommendations\n";
        } else {
            echo "   ✅ Code quality looks excellent!\n";
        }
    }

    // Helper methods would be implemented here
    private function getPhpFiles(string $directory): array { /* ... */ }
    private function calculateCyclomaticComplexity(string $file): int { /* ... */ }
    private function extractMethods(string $file): array { /* ... */ }
    private function calculateMethodComplexity(string $file, string $method): int { /* ... */ }
    private function detectDuplication(): array { /* ... */ }
    private function calculateDuplicationPercentage(): float { /* ... */ }
    private function getAllClasses(): array { /* ... */ }
    private function analyzeClass(string $class): array { /* ... */ }
    private function detectNPlusOnePatterns(): array { /* ... */ }
    private function detectInefficientLoops(): array { /* ... */ }
    private function detectMemoryIssues(): array { /* ... */ }
}

// Execute analysis
$analyzer = new PHPMetricsAnalyzer();
$results = $analyzer->analyze();

echo "\n" . json_encode($results, JSON_PRETTY_PRINT);
```

### 4. Analizador TypeScript/React

```typescript
// scripts/frontend-metrics-analyzer.ts
import * as ts from "typescript";
import * as fs from "fs";
import * as path from "path";

interface ComponentAnalysis {
   name: string;
   file: string;
   linesOfCode: number;
   complexity: number;
   propsCount: number;
   hooksCount: number;
   issues: string[];
}

interface AnalysisResults {
   components: ComponentAnalysis[];
   issues: {
      complexity: any[];
      performance: any[];
      accessibility: any[];
   };
   recommendations: string[];
}

class FrontendMetricsAnalyzer {
   private results: AnalysisResults = {
      components: [],
      issues: {
         complexity: [],
         performance: [],
         accessibility: [],
      },
      recommendations: [],
   };

   public analyze(): AnalysisResults {
      console.log("🎨 Frontend Code Metrics Analysis");
      console.log("=================================\n");

      this.analyzeComponents();
      this.analyzePerformancePatterns();
      this.analyzeAccessibilityPatterns();
      this.generateRecommendations();

      return this.results;
   }

   private analyzeComponents(): void {
      console.log("1. 📊 Component Analysis");

      const componentFiles = this.getComponentFiles("src/components");

      for (const file of componentFiles) {
         const analysis = this.analyzeComponent(file);
         this.results.components.push(analysis);

         // Check for complexity issues
         if (analysis.complexity > 10) {
            this.results.issues.complexity.push({
               type: "high_complexity",
               file: analysis.file,
               component: analysis.name,
               complexity: analysis.complexity,
               recommendation: "Consider breaking down into smaller components",
            });
         }

         // Check for too many props
         if (analysis.propsCount > 8) {
            this.results.issues.complexity.push({
               type: "too_many_props",
               file: analysis.file,
               component: analysis.name,
               propsCount: analysis.propsCount,
               recommendation: "Consider using composition or context",
            });
         }
      }

      const highComplexityCount = this.results.issues.complexity.filter(
         (issue) => issue.type === "high_complexity",
      ).length;

      if (highComplexityCount > 0) {
         console.log(
            `   ⚠️  Found ${highComplexityCount} high complexity components`,
         );
      } else {
         console.log("   ✅ All components within complexity limits");
      }
   }

   private analyzePerformancePatterns(): void {
      console.log("\n2. ⚡ Performance Pattern Analysis");

      const performanceIssues = [];

      // Check for missing React.memo
      const unnecessaryRerenders = this.detectUnnecessaryRerenders();
      performanceIssues.push(...unnecessaryRerenders);

      // Check for inefficient hooks usage
      const hookIssues = this.detectIneffientHooks();
      performanceIssues.push(...hookIssues);

      // Check for large bundle imports
      const importIssues = this.detectIneffientImports();
      performanceIssues.push(...importIssues);

      this.results.issues.performance = performanceIssues;

      if (performanceIssues.length > 0) {
         console.log(
            `   ⚠️  Found ${performanceIssues.length} performance issues`,
         );
      } else {
         console.log("   ✅ No obvious performance issues detected");
      }
   }

   private analyzeAccessibilityPatterns(): void {
      console.log("\n3. ♿ Accessibility Pattern Analysis");

      const accessibilityIssues = [];

      // Check for missing alt texts
      const altTextIssues = this.detectMissingAltTexts();
      accessibilityIssues.push(...altTextIssues);

      // Check for missing ARIA labels
      const ariaIssues = this.detectMissingAriaLabels();
      accessibilityIssues.push(...ariaIssues);

      // Check for keyboard navigation
      const keyboardIssues = this.detectKeyboardNavigationIssues();
      accessibilityIssues.push(...keyboardIssues);

      this.results.issues.accessibility = accessibilityIssues;

      if (accessibilityIssues.length > 0) {
         console.log(
            `   ⚠️  Found ${accessibilityIssues.length} accessibility issues`,
         );
      } else {
         console.log("   ✅ Accessibility patterns look good");
      }
   }

   private generateRecommendations(): void {
      console.log("\n4. 💡 Generating Recommendations");

      const recommendations = [];

      // Complexity recommendations
      const complexityIssues = this.results.issues.complexity.length;
      if (complexityIssues > 0) {
         recommendations.push(
            `Refactor ${complexityIssues} components with high complexity using composition patterns`,
         );
      }

      // Performance recommendations
      const performanceIssues = this.results.issues.performance.length;
      if (performanceIssues > 0) {
         recommendations.push(
            `Optimize ${performanceIssues} performance issues using React.memo, useMemo, and useCallback`,
         );
      }

      // Accessibility recommendations
      const accessibilityIssues = this.results.issues.accessibility.length;
      if (accessibilityIssues > 0) {
         recommendations.push(
            `Improve ${accessibilityIssues} accessibility issues with proper ARIA labels and semantic HTML`,
         );
      }

      this.results.recommendations = recommendations;

      if (recommendations.length > 0) {
         console.log(
            `   📋 Generated ${recommendations.length} recommendations`,
         );
      } else {
         console.log("   ✅ Code quality looks excellent!");
      }
   }

   // Helper methods
   private getComponentFiles(directory: string): string[] {
      // Implementation to get all .tsx/.jsx files
      return [];
   }

   private analyzeComponent(file: string): ComponentAnalysis {
      // Implementation to analyze individual component
      return {
         name: "",
         file: "",
         linesOfCode: 0,
         complexity: 0,
         propsCount: 0,
         hooksCount: 0,
         issues: [],
      };
   }

   private detectUnnecessaryRerenders(): any[] {
      return [];
   }
   private detectIneffientHooks(): any[] {
      return [];
   }
   private detectIneffientImports(): any[] {
      return [];
   }
   private detectMissingAltTexts(): any[] {
      return [];
   }
   private detectMissingAriaLabels(): any[] {
      return [];
   }
   private detectKeyboardNavigationIssues(): any[] {
      return [];
   }
}

// Execute analysis
const analyzer = new FrontendMetricsAnalyzer();
const results = analyzer.analyze();

console.log("\n" + JSON.stringify(results, null, 2));
```

### 5. GitHub Actions para análisis automatizado

```yaml
# .github/workflows/code-review.yml
name: Automated Code Review

on:
   pull_request:
      branches: [main, develop]
   push:
      branches: [main, develop]

jobs:
   frontend-analysis:
      name: Frontend Code Analysis
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"
              cache: "npm"

         - name: Install dependencies
           run: npm ci

         - name: Run ESLint
           run: npx eslint src/ --ext .ts,.tsx --format json --output-file eslint-report.json

         - name: Run TypeScript check
           run: npx tsc --noEmit --skipLibCheck

         - name: Bundle size analysis
           run: npm run build -- --analyze

         - name: Upload frontend analysis results
           uses: actions/upload-artifact@v3
           with:
              name: frontend-analysis
              path: |
                 eslint-report.json
                 dist/stats.json

   backend-analysis:
      name: Backend Code Analysis
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v3

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
              extensions: mbstring, xml, ctype, iconv, intl, pdo_sqlite
              coverage: none

         - name: Install dependencies
           run: composer install --prefer-dist --no-progress

         - name: Run PHP CodeSniffer
           run: ./vendor/bin/phpcs --report=json --report-file=phpcs-report.json

         - name: Run PHPStan
           run: ./vendor/bin/phpstan analyse --error-format=json > phpstan-report.json

         - name: Run custom metrics analysis
           run: php scripts/php-metrics-analyzer.php > php-metrics.json

         - name: Upload backend analysis results
           uses: actions/upload-artifact@v3
           with:
              name: backend-analysis
              path: |
                 phpcs-report.json
                 phpstan-report.json
                 php-metrics.json

   generate-report:
      name: Generate Consolidated Report
      runs-on: ubuntu-latest
      needs: [frontend-analysis, backend-analysis]

      steps:
         - uses: actions/checkout@v3

         - name: Download frontend analysis
           uses: actions/download-artifact@v3
           with:
              name: frontend-analysis
              path: reports/frontend/

         - name: Download backend analysis
           uses: actions/download-artifact@v3
           with:
              name: backend-analysis
              path: reports/backend/

         - name: Generate consolidated report
           run: php scripts/generate-code-review-report.php reports/

         - name: Comment PR with results
           if: github.event_name == 'pull_request'
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const report = fs.readFileSync('reports/pr-comment.md', 'utf8');

                 github.rest.issues.createComment({
                   issue_number: context.issue.number,
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   body: report
                 });
```

## Tips

- **Análisis regular**: Ejecuta análisis de código como parte del proceso de
  CI/CD
- **Métricas consistentes**: Establece thresholds claros para todas las métricas
- **Priorización**: Enfócate primero en issues de alta prioridad y impacto
- **Automatización**: Automatiza tanto como sea posible para reducir overhead
  manual
- **Contexto del negocio**: Considera el impacto en el negocio al priorizar
  refactoring
- **Documentación**: Documenta decisiones de arquitectura y debt técnica
- **Evolución**: Actualiza herramientas y estándares conforme evoluciona el
  proyecto
- **Colaboración**: Involucra al equipo completo en la definición de estándares

## Ejemplos

### Script de análisis completo

```bash
#!/bin/bash
# comprehensive-code-review.sh

echo "🔍 Comprehensive Code Review"
echo "============================"

# Create reports directory
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
REPORT_DIR="reports/comprehensive-review-$TIMESTAMP"
mkdir -p $REPORT_DIR

# Frontend analysis
echo "1. 🎨 Frontend Analysis..."
cd frontend/
npm run lint:report > ../$REPORT_DIR/eslint.json
npm run typecheck > ../$REPORT_DIR/typescript.log
npm run test:coverage > ../$REPORT_DIR/frontend-coverage.log
npm run build:analyze > ../$REPORT_DIR/bundle-analysis.log
cd ..

# Backend analysis
echo "2. 🔧 Backend Analysis..."
cd backend/
./vendor/bin/phpcs --report=json > ../$REPORT_DIR/phpcs.json
./vendor/bin/phpstan analyse --error-format=json > ../$REPORT_DIR/phpstan.json
./vendor/bin/phpunit --coverage-html ../$REPORT_DIR/coverage/
php artisan code:analyze > ../$REPORT_DIR/custom-analysis.json
cd ..

# Security analysis
echo "3. 🔒 Security Analysis..."
cd frontend/ && npm audit --json > ../$REPORT_DIR/npm-audit.json && cd ..
cd backend/ && ./vendor/bin/security-checker security:check > ../$REPORT_DIR/security-check.log && cd ..

# Generate final report
echo "4. 📊 Generating Final Report..."
php scripts/generate-comprehensive-report.php $REPORT_DIR

echo "✅ Comprehensive review complete!"
echo "📁 Reports available at: $REPORT_DIR"
echo "🌐 Open: $REPORT_DIR/index.html"
```

### Configuración de Quality Gates

```json
{
   "qualityGates": {
      "frontend": {
         "eslintErrors": 0,
         "eslintWarnings": 10,
         "typescriptErrors": 0,
         "testCoverage": 80,
         "bundleSize": "500KB",
         "performanceScore": 90
      },
      "backend": {
         "phpcsErrors": 0,
         "phpcsWarnings": 5,
         "phpstanErrors": 0,
         "testCoverage": 85,
         "complexityThreshold": 10,
         "duplicationPercentage": 3
      },
      "security": {
         "highVulnerabilities": 0,
         "mediumVulnerabilities": 2,
         "outdatedDependencies": 5
      }
   },
   "actions": {
      "failBuild": [
         "eslintErrors",
         "typescriptErrors",
         "phpcsErrors",
         "phpstanErrors",
         "highVulnerabilities"
      ],
      "warn": ["eslintWarnings", "phpcsWarnings", "mediumVulnerabilities"],
      "monitor": ["testCoverage", "bundleSize", "performanceScore"]
   }
}
```

## Navegación

[⬅️ Checklist de Revisión de Base de Datos](./checklist-revision-base-datos.md)
| [🏠 README Principal](../../README.md) |
[Optimización de Recursos ➡️](./optimizacion-recursos.md)
