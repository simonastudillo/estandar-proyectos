# Testing de Seguridad OWASP

## ¿Qué es?

El testing de seguridad OWASP es el proceso de evaluación de vulnerabilidades de
seguridad en aplicaciones web siguiendo las mejores prácticas y metodologías
establecidas por el Open Web Application Security Project (OWASP). Se enfoca en
identificar y mitigar las amenazas más comunes según el OWASP Top 10 y otros
estándares de seguridad.

## ¿Por qué es importante?

- **Protección de datos**: Salvaguarda información sensible de usuarios y
  empresa
- **Cumplimiento normativo**: Asegura el cumplimiento de regulaciones como GDPR,
  LOPD
- **Reputación**: Protege la imagen de la empresa ante brechas de seguridad
- **Costos**: Previene pérdidas económicas por ataques cibernéticos
- **Confianza**: Mantiene la confianza de usuarios y stakeholders
- **Prevención temprana**: Identifica vulnerabilidades antes del despliegue
- **Responsabilidad legal**: Cumple con obligaciones legales de protección de
  datos

## ¿Qué debe incluir?

### OWASP Top 10 (2021)

#### A01 - Broken Access Control

- Verificación de autenticación y autorización
- Control de acceso a recursos protegidos
- Validación de permisos por roles
- Prevención de escalación de privilegios

#### A02 - Cryptographic Failures

- Uso correcto de algoritmos de cifrado
- Gestión segura de claves
- Protección de datos en tránsito y reposo
- Implementación de HTTPS/TLS

#### A03 - Injection

- Prevención de SQL Injection
- Validación contra NoSQL Injection
- Protección contra LDAP Injection
- Sanitización de entrada de datos

#### A04 - Insecure Design

- Revisión de arquitectura de seguridad
- Implementación de controles de seguridad
- Análisis de amenazas y riesgos
- Diseño de defensas en profundidad

#### A05 - Security Misconfiguration

- Configuración segura de servidores
- Hardening de sistemas operativos
- Configuración de bases de datos
- Gestión de headers de seguridad

#### A06 - Vulnerable Components

- Inventario de componentes
- Análisis de vulnerabilidades conocidas
- Gestión de actualizaciones
- Evaluación de dependencias

#### A07 - Identification and Authentication Failures

- Fortaleza de contraseñas
- Protección contra ataques de fuerza bruta
- Gestión de sesiones
- Implementación de 2FA/MFA

#### A08 - Software and Data Integrity Failures

- Verificación de integridad de código
- Validación de actualizaciones
- Protección contra supply chain attacks
- Implementación de checksums

#### A09 - Security Logging and Monitoring Failures

- Logging completo de eventos de seguridad
- Monitoreo en tiempo real
- Alertas de incidentes
- Análisis forense

#### A10 - Server-Side Request Forgery (SSRF)

- Validación de URLs
- Filtrado de requests internos
- Protección contra SSRF
- Whitelist de dominios permitidos

## ¿Qué debo hacer?

### 1. Configurar herramientas de security testing

#### OWASP ZAP (Zed Attack Proxy)

```bash
# Instalar OWASP ZAP
wget https://github.com/zaproxy/zaproxy/releases/download/v2.12.0/ZAP_2.12.0_Linux.tar.gz
tar -xzf ZAP_2.12.0_Linux.tar.gz
cd ZAP_2.12.0

# Ejecutar ZAP en modo headless
./zap.sh -cmd -quickurl http://localhost:3000 -quickprogress -quickout /tmp/zap-report.html
```

#### Nikto - Escáner de vulnerabilidades web

```bash
# Instalar Nikto
git clone https://github.com/sullo/nikto.git
cd nikto/program

# Ejecutar escaneo
./nikto.pl -h http://localhost:3000 -output nikto-report.txt
```

#### Dependency Check para vulnerabilidades en dependencias

```bash
# Backend - PHP
composer require --dev roave/security-advisories

# Frontend - npm audit
npm audit
npm audit fix

# Audit avanzado con audit-ci
npm install -g audit-ci
audit-ci --config audit-ci.json
```

### 2. Implementar análisis estático de seguridad

#### SonarQube Security Rules

```yaml
# sonar-project.properties
sonar.projectKey=mi-proyecto
sonar.projectName=Mi Proyecto
sonar.projectVersion=1.0

sonar.sources=src
sonar.tests=tests
sonar.exclusions=**/node_modules/**,**/vendor/**

# Reglas de seguridad
sonar.security.hotspots.threshold=5
sonar.security.review.threshold=A
```

#### ESLint Security Plugin

```bash
# Instalar plugin de seguridad
npm install --save-dev eslint-plugin-security

# Configurar .eslintrc.js
cat > .eslintrc.js << 'EOF'
module.exports = {
  extends: [
    'plugin:security/recommended'
  ],
  plugins: ['security'],
  rules: {
    'security/detect-sql-injection': 'error',
    'security/detect-xss': 'error',
    'security/detect-eval-with-expression': 'error',
    'security/detect-non-literal-regexp': 'error',
    'security/detect-unsafe-regex': 'error',
    'security/detect-buffer-noassert': 'error',
    'security/detect-child-process': 'error',
    'security/detect-disable-mustache-escape': 'error',
    'security/detect-new-buffer': 'error',
    'security/detect-no-csrf-before-method-override': 'error',
  }
};
EOF
```

#### PHPStan Security Rules

```bash
# Instalar PHPStan con reglas de seguridad
composer require --dev phpstan/phpstan
composer require --dev roave/security-advisories

# Configurar phpstan.neon
cat > phpstan.neon << 'EOF'
parameters:
    level: 8
    paths:
        - app
        - tests
    excludePaths:
        - vendor

    # Reglas de seguridad
    checkMissingIterableValueType: false
    checkGenericClassInNonGenericObjectType: false

    # Validaciones de seguridad personalizadas
    customRulesetUsed: true
EOF
```

### 3. Configurar testing automatizado de seguridad

#### Tests de autenticación y autorización

```php
<?php
// tests/Feature/Security/AuthSecurityTest.php

namespace Tests\Feature\Security;

use Tests\TestCase;
use App\Domain\Entities\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

class AuthSecurityTest extends TestCase
{
    use RefreshDatabase;

    public function test_prevents_brute_force_attacks()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('correct-password')
        ]);

        // Intentar login fallido múltiples veces
        for ($i = 0; $i < 10; $i++) {
            $response = $this->postJson('/api/v1/auth/login', [
                'email' => 'test@example.com',
                'password' => 'wrong-password'
            ]);
        }

        // El siguiente intento debe ser bloqueado
        $response = $this->postJson('/api/v1/auth/login', [
            'email' => 'test@example.com',
            'password' => 'correct-password'
        ]);

        $response->assertStatus(429); // Too Many Requests
        $response->assertJson(['message' => 'Too many login attempts']);
    }

    public function test_enforces_strong_password_policy()
    {
        $weakPasswords = [
            '123456',
            'password',
            'qwerty',
            'abc123',
            '12345678'
        ];

        foreach ($weakPasswords as $password) {
            $response = $this->postJson('/api/v1/auth/register', [
                'name' => 'Test User',
                'email' => 'test@example.com',
                'password' => $password,
                'password_confirmation' => $password
            ]);

            $response->assertStatus(422);
            $response->assertJsonValidationErrors(['password']);
        }
    }

    public function test_prevents_session_fixation()
    {
        // Obtener ID de sesión antes del login
        $response = $this->get('/');
        $sessionBefore = $response->headers->get('Set-Cookie');

        // Hacer login
        $user = User::factory()->create();
        $this->actingAs($user);

        // Verificar que el ID de sesión cambió
        $response = $this->get('/dashboard');
        $sessionAfter = $response->headers->get('Set-Cookie');

        $this->assertNotEquals($sessionBefore, $sessionAfter);
    }

    public function test_protects_against_csrf()
    {
        $user = User::factory()->create();

        // Intentar hacer request sin token CSRF
        $response = $this->post('/api/v1/users', [
            'name' => 'Test User',
            'email' => 'test@example.com'
        ]);

        $response->assertStatus(419); // CSRF token mismatch
    }

    public function test_validates_jwt_token_integrity()
    {
        $user = User::factory()->create();

        // Generar token válido
        $validToken = $user->createToken('test')->plainTextToken;

        // Modificar token (simular tampering)
        $tamperedToken = $validToken . 'tampered';

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $tamperedToken
        ])->getJson('/api/v1/users/profile');

        $response->assertStatus(401);
    }
}
```

#### Tests de inyección SQL

```php
<?php
// tests/Feature/Security/SqlInjectionTest.php

namespace Tests\Feature\Security;

use Tests\TestCase;
use App\Domain\Entities\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

class SqlInjectionTest extends TestCase
{
    use RefreshDatabase;

    public function test_prevents_sql_injection_in_login()
    {
        $maliciousInputs = [
            "admin'; DROP TABLE users; --",
            "' OR '1'='1",
            "' OR 1=1 --",
            "'; INSERT INTO users (email) VALUES ('hacked@evil.com'); --",
            "' UNION SELECT * FROM users WHERE '1'='1"
        ];

        foreach ($maliciousInputs as $input) {
            $response = $this->postJson('/api/v1/auth/login', [
                'email' => $input,
                'password' => $input
            ]);

            // Debe fallar la autenticación, no causar error SQL
            $response->assertStatus(422);

            // Verificar que la tabla users sigue existiendo
            $this->assertDatabaseCount('users', 0);
        }
    }

    public function test_prevents_sql_injection_in_search()
    {
        User::factory()->count(5)->create();

        $maliciousInputs = [
            "'; DROP TABLE users; --",
            "' OR 1=1 --",
            "' UNION SELECT password FROM users --"
        ];

        foreach ($maliciousInputs as $input) {
            $response = $this->getJson('/api/v1/users?search=' . urlencode($input));

            // Debe retornar respuesta válida, no error SQL
            $response->assertStatus(200);
            $response->assertJsonStructure(['data']);
        }
    }

    public function test_parameterized_queries_prevent_injection()
    {
        $user = User::factory()->create(['name' => 'John Doe']);

        // Test con input malicioso en parámetro
        $response = $this->getJson("/api/v1/users/{$user->id}'; DROP TABLE users; --");

        // Debe retornar 404, no ejecutar SQL malicioso
        $response->assertStatus(404);

        // Verificar que el usuario original sigue existiendo
        $this->assertDatabaseHas('users', ['id' => $user->id]);
    }
}
```

#### Tests de XSS (Cross-Site Scripting)

```typescript
// src/__tests__/security/xss.test.tsx
import React from "react";
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import UserProfile from "../../components/UserProfile/UserProfile";
import CommentSection from "../../components/CommentSection/CommentSection";

describe("XSS Protection", () => {
  const maliciousInputs = [
    '<script>alert("XSS")</script>',
    '<img src="x" onerror="alert(\'XSS\')" />',
    "<svg onload=\"alert('XSS')\" />",
    'javascript:alert("XSS")',
    "<iframe src=\"javascript:alert('XSS')\"></iframe>",
    "<body onload=\"alert('XSS')\">",
    "<div onclick=\"alert('XSS')\">Click me</div>",
    '"><script>alert("XSS")</script>',
    '\';alert("XSS");//',
  ];

  test("should sanitize user input in profile display", () => {
    const user = {
      id: 1,
      name: '<script>alert("XSS")</script>John Doe',
      bio: '<img src="x" onerror="alert(\'XSS\')" />Software Developer',
    };

    render(<UserProfile user={user} />);

    // El script no debe estar presente en el DOM
    expect(screen.queryByText(/script/)).not.toBeInTheDocument();
    expect(screen.getByText(/John Doe/)).toBeInTheDocument();
    expect(screen.getByText(/Software Developer/)).toBeInTheDocument();
  });

  test("should prevent XSS in comment submission", async () => {
    const user = userEvent.setup();
    const onSubmit = jest.fn();

    render(<CommentSection onSubmit={onSubmit} />);

    const textarea = screen.getByRole("textbox");
    const submitButton = screen.getByRole("button", { name: /enviar/i });

    for (const maliciousInput of maliciousInputs) {
      await user.clear(textarea);
      await user.type(textarea, maliciousInput);
      await user.click(submitButton);

      // Verificar que el input fue sanitizado
      const submittedData =
        onSubmit.mock.calls[onSubmit.mock.calls.length - 1][0];
      expect(submittedData.comment).not.toContain("<script>");
      expect(submittedData.comment).not.toContain("javascript:");
      expect(submittedData.comment).not.toContain("onerror");
      expect(submittedData.comment).not.toContain("onload");
    }
  });

  test("should safely render HTML content with DOMPurify", () => {
    const dangerousHTML = `
      <p>Contenido válido</p>
      <script>alert('XSS')</script>
      <img src="x" onerror="alert('XSS')" />
      <a href="javascript:alert('XSS')">Link malicioso</a>
    `;

    const { container } = render(
      <div
        dangerouslySetInnerHTML={{
          __html: require("dompurify").sanitize(dangerousHTML),
        }}
      />
    );

    // Solo el contenido válido debe estar presente
    expect(container.innerHTML).toContain("<p>Contenido válido</p>");
    expect(container.innerHTML).not.toContain("<script>");
    expect(container.innerHTML).not.toContain("onerror");
    expect(container.innerHTML).not.toContain("javascript:");
  });

  test("should escape content in URLs", () => {
    const maliciousUrl = 'javascript:alert("XSS")';

    render(<a href={maliciousUrl}>Link</a>);

    const link = screen.getByRole("link");
    // React automáticamente sanitiza URLs peligrosas
    expect(link.getAttribute("href")).not.toBe(maliciousUrl);
  });
});
```

### 4. Configurar headers de seguridad

#### Middleware de seguridad para Laravel

```php
<?php
// app/Infrastructure/Http/Middleware/SecurityHeaders.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class SecurityHeaders
{
    public function handle(Request $request, Closure $next): Response
    {
        $response = $next($request);

        // Content Security Policy
        $response->headers->set('Content-Security-Policy',
            "default-src 'self'; " .
            "script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com; " .
            "style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; " .
            "font-src 'self' https://fonts.gstatic.com; " .
            "img-src 'self' data: https:; " .
            "connect-src 'self' https://api.example.com"
        );

        // Prevenir clickjacking
        $response->headers->set('X-Frame-Options', 'DENY');

        // Prevenir MIME type sniffing
        $response->headers->set('X-Content-Type-Options', 'nosniff');

        // XSS Protection
        $response->headers->set('X-XSS-Protection', '1; mode=block');

        // Referrer Policy
        $response->headers->set('Referrer-Policy', 'strict-origin-when-cross-origin');

        // Permissions Policy
        $response->headers->set('Permissions-Policy',
            'camera=(), microphone=(), geolocation=(), payment=()'
        );

        // HSTS (solo en HTTPS)
        if ($request->isSecure()) {
            $response->headers->set('Strict-Transport-Security',
                'max-age=31536000; includeSubDomains; preload'
            );
        }

        return $response;
    }
}
```

#### Configuración de Helmet.js para Express

```typescript
// src/middleware/security.ts
import helmet from "helmet";
import { Application } from "express";

export function configureSecurityHeaders(app: Application): void {
  app.use(
    helmet({
      contentSecurityPolicy: {
        directives: {
          defaultSrc: ["'self'"],
          scriptSrc: [
            "'self'",
            "'unsafe-inline'",
            "https://cdnjs.cloudflare.com",
          ],
          styleSrc: [
            "'self'",
            "'unsafe-inline'",
            "https://fonts.googleapis.com",
          ],
          fontSrc: ["'self'", "https://fonts.gstatic.com"],
          imgSrc: ["'self'", "data:", "https:"],
          connectSrc: ["'self'", "https://api.example.com"],
          objectSrc: ["'none'"],
          upgradeInsecureRequests: [],
        },
      },
      hsts: {
        maxAge: 31536000,
        includeSubDomains: true,
        preload: true,
      },
      noSniff: true,
      xssFilter: true,
      referrerPolicy: { policy: "strict-origin-when-cross-origin" },
    })
  );
}
```

### 5. Implementar rate limiting y protección DDoS

#### Rate Limiting en Laravel

```php
<?php
// app/Infrastructure/Http/Middleware/CustomRateLimit.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Cache\RateLimiter;
use Illuminate\Http\Exceptions\ThrottleRequestsException;

class CustomRateLimit
{
    private RateLimiter $limiter;

    public function __construct(RateLimiter $limiter)
    {
        $this->limiter = $limiter;
    }

    public function handle(Request $request, Closure $next, string $maxAttempts = '60', string $decayMinutes = '1')
    {
        $key = $this->resolveRequestSignature($request);

        if ($this->limiter->tooManyAttempts($key, $maxAttempts)) {
            throw new ThrottleRequestsException(
                'Too many attempts. Try again in ' . $this->limiter->availableIn($key) . ' seconds.'
            );
        }

        $this->limiter->hit($key, $decayMinutes * 60);

        return $next($request);
    }

    private function resolveRequestSignature(Request $request): string
    {
        return sha1(
            $request->method() .
            '|' . $request->getHost() .
            '|' . $request->ip() .
            '|' . $request->path()
        );
    }
}
```

#### Rate Limiting para APIs específicas

```php
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;

Route::prefix('v1')->group(function () {
    // Rutas públicas con rate limiting estricto
    Route::middleware(['throttle:10,1'])->group(function () {
        Route::post('/auth/login', [AuthController::class, 'login']);
        Route::post('/auth/register', [AuthController::class, 'register']);
        Route::post('/auth/forgot-password', [AuthController::class, 'forgotPassword']);
    });

    // Rutas autenticadas con rate limiting moderado
    Route::middleware(['auth:sanctum', 'throttle:100,1'])->group(function () {
        Route::apiResource('users', UserController::class);
        Route::apiResource('products', ProductController::class);
    });

    // Rutas de búsqueda con rate limiting alto
    Route::middleware(['throttle:200,1'])->group(function () {
        Route::get('/search', [SearchController::class, 'search']);
        Route::get('/autocomplete', [SearchController::class, 'autocomplete']);
    });
});
```

### 6. Configurar logging y monitoreo de seguridad

#### Security Event Logger

```php
<?php
// app/Infrastructure/Security/SecurityLogger.php

namespace App\Infrastructure\Security;

use Illuminate\Support\Facades\Log;
use Illuminate\Http\Request;

class SecurityLogger
{
    public static function logAuthenticationAttempt(Request $request, bool $successful, ?string $email = null): void
    {
        Log::channel('security')->info('Authentication attempt', [
            'ip' => $request->ip(),
            'user_agent' => $request->userAgent(),
            'email' => $email,
            'successful' => $successful,
            'timestamp' => now(),
            'session_id' => $request->session()->getId()
        ]);
    }

    public static function logSuspiciousActivity(Request $request, string $reason, array $context = []): void
    {
        Log::channel('security')->warning('Suspicious activity detected', [
            'ip' => $request->ip(),
            'user_agent' => $request->userAgent(),
            'url' => $request->fullUrl(),
            'method' => $request->method(),
            'reason' => $reason,
            'context' => $context,
            'timestamp' => now()
        ]);
    }

    public static function logSecurityEvent(string $event, array $context = []): void
    {
        Log::channel('security')->error('Security event', [
            'event' => $event,
            'context' => $context,
            'timestamp' => now(),
            'server' => gethostname()
        ]);
    }

    public static function logDataAccess(Request $request, string $resource, array $context = []): void
    {
        Log::channel('security')->info('Data access', [
            'ip' => $request->ip(),
            'user_id' => auth()->id(),
            'resource' => $resource,
            'context' => $context,
            'timestamp' => now()
        ]);
    }
}
```

#### Configuración de canales de logging

```php
<?php
// config/logging.php

return [
    'channels' => [
        'security' => [
            'driver' => 'daily',
            'path' => storage_path('logs/security.log'),
            'level' => 'debug',
            'days' => 30,
            'formatter' => \Monolog\Formatter\JsonFormatter::class,
        ],

        'audit' => [
            'driver' => 'daily',
            'path' => storage_path('logs/audit.log'),
            'level' => 'info',
            'days' => 90,
            'formatter' => \Monolog\Formatter\JsonFormatter::class,
        ],

        'intrusion' => [
            'driver' => 'single',
            'path' => storage_path('logs/intrusion.log'),
            'level' => 'warning',
            'formatter' => \Monolog\Formatter\JsonFormatter::class,
        ],
    ],
];
```

### 7. Pipeline de CI/CD para security testing

```yaml
# .github/workflows/security-tests.yml
name: Security Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  schedule:
    - cron: "0 2 * * 0" # Semanal, domingos a las 2 AM

jobs:
  dependency-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: "8.2"

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"

      - name: Install PHP dependencies
        run: composer install --no-dev

      - name: Install Node dependencies
        run: npm ci

      - name: Run PHP security audit
        run: composer audit

      - name: Run npm audit
        run: npm audit --audit-level=moderate

      - name: Run OWASP Dependency Check
        uses: dependency-check/Dependency-Check_Action@main
        with:
          project: "mi-proyecto"
          path: "."
          format: "JSON"

      - name: Upload dependency check results
        uses: actions/upload-artifact@v3
        with:
          name: dependency-check-report
          path: reports/dependency-check-report.json

  static-analysis:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: "8.2"

      - name: Install dependencies
        run: composer install

      - name: Run PHPStan security analysis
        run: ./vendor/bin/phpstan analyse --error-format=github

      - name: Run ESLint security rules
        run: npx eslint src/ --ext .ts,.tsx --format=json --output-file=eslint-security-report.json

      - name: Upload static analysis results
        uses: actions/upload-artifact@v3
        with:
          name: static-analysis-report
          path: eslint-security-report.json

  penetration-testing:
    runs-on: ubuntu-latest
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: test_db
        ports:
          - 3306:3306

    steps:
      - uses: actions/checkout@v3

      - name: Setup application
        run: |
          composer install
          php artisan migrate --seed
          php artisan serve &
          sleep 10

      - name: Install OWASP ZAP
        run: |
          wget -q https://github.com/zaproxy/zaproxy/releases/download/v2.12.0/ZAP_2.12.0_Linux.tar.gz
          tar -xzf ZAP_2.12.0_Linux.tar.gz

      - name: Run ZAP baseline scan
        run: |
          ./ZAP_2.12.0/zap.sh -cmd -quickurl http://localhost:8000 \
            -quickprogress -quickout zap-baseline-report.html

      - name: Run ZAP full scan
        run: |
          ./ZAP_2.12.0/zap.sh -cmd -quickurl http://localhost:8000 \
            -quickattack -quickprogress -quickout zap-full-report.html

      - name: Upload ZAP reports
        uses: actions/upload-artifact@v3
        with:
          name: zap-reports
          path: zap-*.html

  security-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: "8.2"

      - name: Install dependencies
        run: composer install

      - name: Run security tests
        run: ./vendor/bin/phpunit --testsuite=Security

      - name: Generate security test report
        run: |
          ./vendor/bin/phpunit --testsuite=Security \
            --coverage-html=coverage-security \
            --log-junit=security-tests.xml

      - name: Upload test results
        uses: actions/upload-artifact@v3
        with:
          name: security-test-results
          path: |
            coverage-security/
            security-tests.xml
```

### 8. Prácticas Inseguras Comunes

#### Errores Críticos de Seguridad a Evitar

##### Backend - Laravel

```php
<?php
// ❌ NUNCA HAGAS ESTO - Query directo sin sanitizar
$userId = $_GET['user_id']; // Sin validación
$user = DB::select("SELECT * FROM users WHERE id = $userId"); // SQL Injection

// ❌ NUNCA HAGAS ESTO - Passwords en texto plano
$user->password = request('password'); // Sin hash

// ❌ NUNCA HAGAS ESTO - Datos sensibles en logs
Log::info('User login', ['password' => request('password')]);

// ❌ NUNCA HAGAS ESTO - Exposición de errores en producción
if (!$user) {
    throw new Exception('Database connection failed: ' . $dbError);
}

// ❌ NUNCA HAGAS ESTO - Mass assignment sin protección
$user = User::create(request()->all()); // Permite asignar cualquier campo

// ✅ FORMA CORRECTA
$validatedData = request()->validate([
    'name' => 'required|string|max:255',
    'email' => 'required|email|unique:users',
    'password' => 'required|string|min:8|confirmed'
]);

$user = User::create([
    'name' => $validatedData['name'],
    'email' => $validatedData['email'],
    'password' => Hash::make($validatedData['password'])
]);
```

##### Frontend - React/TypeScript

```typescript
// ❌ NUNCA HAGAS ESTO - Almacenar tokens en localStorage sin cifrar
localStorage.setItem("auth_token", token); // Vulnerable a XSS

// ❌ NUNCA HAGAS ESTO - Inyección directa de HTML
function UserProfile({ userName }: { userName: string }) {
  return <div dangerouslySetInnerHTML={{ __html: userName }} />;
}

// ❌ NUNCA HAGAS ESTO - Exposición de datos sensibles en cliente
const API_SECRET = "secret123"; // Visible en bundle
const config = {
  apiUrl: "https://api.example.com",
  secretKey: API_SECRET, // Nunca en frontend
};

// ❌ NUNCA HAGAS ESTO - Validación solo en frontend
function submitForm(data: FormData) {
  if (data.email.includes("@")) {
    // Validación insuficiente
    api.post("/users", data); // Sin validación backend
  }
}

// ✅ FORMA CORRECTA
import DOMPurify from "dompurify";

function UserProfile({ userName }: { userName: string }) {
  const sanitizedName = DOMPurify.sanitize(userName);
  return <div>{sanitizedName}</div>; // Usar como texto, no HTML
}

// Token en httpOnly cookie (manejado por backend)
// Validación tanto en frontend como backend
function submitForm(data: FormData) {
  const validatedData = validateFormData(data); // Frontend validation
  api.post("/users", validatedData); // Backend también valida
}
```

#### Configuraciones Inseguras Típicas

##### Variables de Entorno Expuestas

```bash
# ❌ NUNCA EN .env.example o commits
APP_KEY=base64:real-secret-key
DB_PASSWORD=real-database-password
JWT_SECRET=actual-jwt-secret

# ✅ CORRECTO en .env.example
APP_KEY=
DB_PASSWORD=
JWT_SECRET=
```

##### Headers de Seguridad Faltantes

```nginx
# ❌ Configuración insegura de Nginx
server {
    listen 80;
    server_name example.com;

    location / {
        # Sin headers de seguridad
        proxy_pass http://localhost:8000;
    }
}

# ✅ Configuración segura de Nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    # SSL/TLS Configuration
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    ssl_protocols TLSv1.2 TLSv1.3;

    # Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline';" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### Code Review Checklist de Seguridad

##### Backend Security Checklist

```php
<?php
/**
 * Checklist de Revisión de Código - Backend
 */

// ✅ Validación de Entrada
class SecurityChecklist
{
    // 1. ¿Se validan TODOS los inputs del usuario?
    public function validateInput($request) {
        return $request->validate([
            'email' => 'required|email|max:255',
            'name' => 'required|string|max:100|regex:/^[a-zA-Z\s]+$/',
            'age' => 'required|integer|min:18|max:120'
        ]);
    }

    // 2. ¿Se usa prepared statements para queries?
    public function getUserById($id) {
        // ✅ Correcto
        return DB::table('users')->where('id', $id)->first();

        // ❌ Incorrecto
        // return DB::select("SELECT * FROM users WHERE id = $id");
    }

    // 3. ¿Se hasean las contraseñas?
    public function createUser($data) {
        return User::create([
            'email' => $data['email'],
            'password' => Hash::make($data['password']) // ✅ Hasheada
        ]);
    }

    // 4. ¿Se implementa autorización granular?
    public function updateUser(Request $request, $userId) {
        $this->authorize('update', User::find($userId)); // ✅ Autorización

        // Lógica de actualización
    }

    // 5. ¿Se protegen rutas sensibles?
    // En routes/api.php
    // Route::middleware(['auth:sanctum', 'throttle:60,1'])->group(function () {
    //     Route::apiResource('users', UserController::class);
    // });
}
```

##### Frontend Security Checklist

```typescript
/**
 * Checklist de Revisión de Código - Frontend
 */

class FrontendSecurityChecklist {
  // 1. ¿Se sanitiza contenido HTML?
  static renderUserContent(content: string): string {
    return DOMPurify.sanitize(content, {
      ALLOWED_TAGS: ["p", "br", "strong", "em"],
      ALLOWED_ATTR: [],
    });
  }

  // 2. ¿Se validan URLs antes de usar?
  static isValidUrl(url: string): boolean {
    try {
      const parsed = new URL(url);
      return ["http:", "https:"].includes(parsed.protocol);
    } catch {
      return false;
    }
  }

  // 3. ¿Se manejan errores sin exponer información sensible?
  static handleApiError(error: any): string {
    // ❌ Incorrecto - expone información técnica
    // return error.response?.data?.message || error.message;

    // ✅ Correcto - mensaje genérico para el usuario
    if (error.response?.status === 401) {
      return "Sesión expirada. Por favor, inicia sesión nuevamente.";
    }
    if (error.response?.status >= 500) {
      return "Error del servidor. Intenta nuevamente más tarde.";
    }
    return "Ha ocurrido un error. Verifica los datos e intenta nuevamente.";
  }

  // 4. ¿Se implementa CSP correctamente?
  static generateNonce(): string {
    const array = new Uint8Array(16);
    crypto.getRandomValues(array);
    return Array.from(array, (byte) => byte.toString(16).padStart(2, "0")).join(
      ""
    );
  }

  // 5. ¿Se validan archivos antes de subir?
  static validateFile(file: File): { valid: boolean; error?: string } {
    const allowedTypes = ["image/jpeg", "image/png", "image/gif"];
    const maxSize = 5 * 1024 * 1024; // 5MB

    if (!allowedTypes.includes(file.type)) {
      return { valid: false, error: "Tipo de archivo no permitido" };
    }

    if (file.size > maxSize) {
      return { valid: false, error: "Archivo demasiado grande" };
    }

    return { valid: true };
  }
}
```

#### Antipatrones de Seguridad

##### Gestión de Sesiones Insegura

```php
<?php
// ❌ ANTIPATRÓN: Sesiones inseguras
class InsecureSessionManagement
{
    public function login($email, $password) {
        $user = User::where('email', $email)->first();

        if ($user && $user->password === $password) { // Sin hash
            session(['user_id' => $user->id]); // Sin regenerar session ID
            session(['is_admin' => $user->is_admin]); // Datos críticos en sesión
            return redirect('/dashboard');
        }
    }

    public function isAdmin() {
        return session('is_admin') === true; // Confía en datos de sesión
    }
}

// ✅ PATRÓN CORRECTO: Sesiones seguras
class SecureSessionManagement
{
    public function login($email, $password) {
        $user = User::where('email', $email)->first();

        if ($user && Hash::check($password, $user->password)) {
            session()->regenerate(); // Regenerar session ID
            session(['user_id' => $user->id]); // Solo ID en sesión

            // Log de auditoría
            Log::info('User login', [
                'user_id' => $user->id,
                'ip' => request()->ip(),
                'user_agent' => request()->userAgent()
            ]);

            return redirect('/dashboard');
        }
    }

    public function isAdmin() {
        $user = Auth::user();
        return $user && $user->hasRole('admin'); // Verificar en BD
    }
}
```

##### Manejo Inseguro de Archivos

```php
<?php
// ❌ ANTIPATRÓN: Subida de archivos insegura
class InsecureFileUpload
{
    public function upload(Request $request) {
        $file = $request->file('upload');
        $filename = $file->getClientOriginalName(); // Nombre del cliente

        // Sin validación de tipo o tamaño
        $file->move(public_path('uploads'), $filename); // Directorio público

        return response()->json(['url' => asset("uploads/$filename")]);
    }
}

// ✅ PATRÓN CORRECTO: Subida de archivos segura
class SecureFileUpload
{
    private const ALLOWED_TYPES = ['jpg', 'jpeg', 'png', 'gif', 'pdf'];
    private const MAX_SIZE = 5 * 1024 * 1024; // 5MB

    public function upload(Request $request) {
        $request->validate([
            'upload' => [
                'required',
                'file',
                'max:5120', // 5MB
                'mimes:jpg,jpeg,png,gif,pdf'
            ]
        ]);

        $file = $request->file('upload');

        // Validaciones adicionales
        if (!$this->isValidFileType($file)) {
            throw new ValidationException('Tipo de archivo no permitido');
        }

        if (!$this->isValidFileSize($file)) {
            throw new ValidationException('Archivo demasiado grande');
        }

        // Generar nombre seguro
        $extension = $file->getClientOriginalExtension();
        $filename = Str::uuid() . '.' . $extension;

        // Almacenar en directorio privado
        $path = $file->storeAs('private/uploads', $filename);

        // Registrar en BD para control de acceso
        $fileRecord = FileUpload::create([
            'original_name' => $file->getClientOriginalName(),
            'stored_name' => $filename,
            'path' => $path,
            'size' => $file->getSize(),
            'mime_type' => $file->getMimeType(),
            'user_id' => auth()->id()
        ]);

        return response()->json([
            'id' => $fileRecord->id,
            'name' => $fileRecord->original_name
        ]);
    }

    private function isValidFileType($file): bool {
        $extension = strtolower($file->getClientOriginalExtension());
        return in_array($extension, self::ALLOWED_TYPES);
    }

    private function isValidFileSize($file): bool {
        return $file->getSize() <= self::MAX_SIZE;
    }
}
```

## Tips

- **Principio de defensa en profundidad**: Implementa múltiples capas de
  seguridad
- **Validación de entrada**: Nunca confíes en datos del usuario, siempre valida
  y sanitiza
- **Principio de menor privilegio**: Otorga solo los permisos mínimos necesarios
- **Fail securely**: En caso de error, falla de manera segura, no expongas
  información
- **Keep it simple**: La seguridad compleja es propensa a errores
- **Actualizaciones regulares**: Mantén dependencias y sistemas actualizados
- **Testing continuo**: Integra security testing en el pipeline de CI/CD
- **Documentación**: Documenta todas las medidas de seguridad implementadas

## Ejemplos

### Configuración completa de seguridad en React

```typescript
// src/utils/security.ts
import DOMPurify from "dompurify";

export class SecurityUtils {
  // Sanitizar HTML para prevenir XSS
  static sanitizeHtml(dirty: string): string {
    return DOMPurify.sanitize(dirty, {
      ALLOWED_TAGS: ["p", "br", "strong", "em", "u", "a"],
      ALLOWED_ATTR: ["href", "target"],
      ALLOW_DATA_ATTR: false,
    });
  }

  // Validar y sanitizar URLs
  static sanitizeUrl(url: string): string {
    try {
      const parsedUrl = new URL(url);

      // Solo permitir http y https
      if (!["http:", "https:"].includes(parsedUrl.protocol)) {
        return "#";
      }

      return parsedUrl.href;
    } catch {
      return "#";
    }
  }

  // Escapar caracteres especiales para prevenir injection
  static escapeHtml(unsafe: string): string {
    return unsafe
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");
  }

  // Validar tokens JWT en el frontend
  static isValidJwtFormat(token: string): boolean {
    const parts = token.split(".");
    return parts.length === 3;
  }

  // Generar nonce para CSP
  static generateNonce(): string {
    const array = new Uint8Array(16);
    crypto.getRandomValues(array);
    return Array.from(array, (byte) => byte.toString(16).padStart(2, "0")).join(
      ""
    );
  }

  // Validar contraseña segura
  static isStrongPassword(password: string): boolean {
    const minLength = 8;
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumbers = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);

    return (
      password.length >= minLength &&
      hasUpperCase &&
      hasLowerCase &&
      hasNumbers &&
      hasSpecialChar
    );
  }
}
```

### Test de penetración automatizado

```bash
#!/bin/bash
# automated-pentest.sh

set -e

APP_URL=${1:-"http://localhost:8000"}
REPORT_DIR="security-reports-$(date +%Y%m%d_%H%M%S)"

echo "🔒 Iniciando testing de penetración automatizado..."
echo "Target: $APP_URL"
echo "Report directory: $REPORT_DIR"

mkdir -p $REPORT_DIR

# 1. Reconnaissance
echo "🕵️ Fase 1: Reconnaissance"
nmap -sV -sC -O $APP_URL > $REPORT_DIR/nmap-scan.txt 2>&1 || true

# 2. Vulnerability scanning con Nikto
echo "🔍 Fase 2: Vulnerability Scanning"
nikto -h $APP_URL -output $REPORT_DIR/nikto-scan.txt || true

# 3. SSL/TLS testing
echo "🔐 Fase 3: SSL/TLS Testing"
testssl.sh $APP_URL > $REPORT_DIR/ssl-test.txt 2>&1 || true

# 4. OWASP ZAP automated scan
echo "⚡ Fase 4: OWASP ZAP Scan"
zap-baseline.py -t $APP_URL -J $REPORT_DIR/zap-baseline.json || true
zap-full-scan.py -t $APP_URL -J $REPORT_DIR/zap-full.json || true

# 5. Directory busting
echo "📁 Fase 5: Directory Discovery"
gobuster dir -u $APP_URL -w /usr/share/wordlists/dirb/common.txt \
  -o $REPORT_DIR/gobuster-directories.txt || true

# 6. Subdomain enumeration
echo "🌐 Fase 6: Subdomain Enumeration"
sublist3r -d $(echo $APP_URL | sed 's/https\?:\/\///') \
  -o $REPORT_DIR/subdomains.txt || true

# 7. SQL injection testing con sqlmap
echo "💉 Fase 7: SQL Injection Testing"
sqlmap -u "$APP_URL/api/v1/users?id=1" --batch --banner \
  --output-dir=$REPORT_DIR/sqlmap || true

# 8. Generate consolidated report
echo "📊 Generando reporte consolidado..."
cat > $REPORT_DIR/executive-summary.md << EOF
# Security Penetration Test Report

**Target**: $APP_URL
**Date**: $(date)
**Duration**: Automated scan

## Executive Summary

Este reporte contiene los resultados del testing de penetración automatizado.

## Vulnerabilities Found

### High Risk
$(grep -i "high\|critical" $REPORT_DIR/*.txt | wc -l) vulnerabilities detected

### Medium Risk
$(grep -i "medium" $REPORT_DIR/*.txt | wc -l) vulnerabilities detected

### Low Risk
$(grep -i "low" $REPORT_DIR/*.txt | wc -l) vulnerabilities detected

## Detailed Results

- **Network Scan**: See nmap-scan.txt
- **Web Vulnerabilities**: See nikto-scan.txt
- **SSL/TLS Issues**: See ssl-test.txt
- **OWASP ZAP Results**: See zap-*.json
- **Directory Discovery**: See gobuster-directories.txt
- **SQL Injection**: See sqlmap/ directory

## Recommendations

1. Review and address all high and critical vulnerabilities
2. Implement proper input validation and sanitization
3. Configure security headers correctly
4. Update all dependencies to latest versions
5. Implement proper rate limiting and monitoring

EOF

echo "✅ Testing de penetración completado."
echo "📋 Reporte disponible en: $REPORT_DIR/executive-summary.md"
```

## Navegación

[⬅️ Testing de Performance y Carga](./testing-performance-carga.md) |
[🏠 README Principal](../../README.md) |
[Testing de Usabilidad ➡️](./testing-usabilidad.md)
