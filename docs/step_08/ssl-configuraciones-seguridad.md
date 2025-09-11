# SSL y Configuraciones de Seguridad

## ¿Qué es?

SSL/TLS (Secure Sockets Layer/Transport Layer Security) y las configuraciones de
seguridad son el conjunto de protocolos, certificados y configuraciones que
aseguran la comunicación entre clientes y servidores. Incluye encriptación de
datos en tránsito, autenticación de servidores, y hardening de la
infraestructura para proteger contra amenazas cibernéticas.

## ¿Por qué es importante?

- **Protección de Datos**: Encripta información sensible en tránsito
- **Confianza del Usuario**: Los navegadores marcan sitios sin SSL como
  inseguros
- **SEO**: Google prioriza sitios con SSL en rankings de búsqueda
- **Compliance**: Requerido por regulaciones como GDPR, PCI-DSS
- **Integridad**: Previene modificación de datos durante transmisión
- **Autenticación**: Verifica la identidad del servidor

## ¿Qué debe incluir?

### Certificados SSL/TLS

- Certificados automáticos con Let's Encrypt
- Renovación automática de certificados
- Wildcard certificates para subdominios
- Configuración de múltiples dominios (SAN)

### Configuración de Seguridad Web

- HTTP Strict Transport Security (HSTS)
- Content Security Policy (CSP)
- Security Headers (X-Frame-Options, X-XSS-Protection, etc.)
- SSL/TLS cipher suites optimization

### Hardening del Servidor

- Firewall configuration (UFW/iptables)
- SSH security hardening
- Fail2ban para prevenir ataques de fuerza bruta
- Regular security updates

### Seguridad de Aplicación

- Input validation y sanitization
- SQL injection prevention
- Cross-Site Scripting (XSS) protection
- Cross-Site Request Forgery (CSRF) protection

## ¿Qué debo hacer?

### 1. Configurar SSL con Let's Encrypt

#### Instalar Certbot

```bash
# Instalar Certbot
sudo apt update
sudo apt install -y certbot python3-certbot-nginx

# Verificar instalación
certbot --version
```

#### Obtener Certificado SSL

```bash
# Obtener certificado para dominio principal
sudo certbot --nginx -d example.com -d www.example.com

# Para múltiples subdominios
sudo certbot --nginx \
    -d example.com \
    -d www.example.com \
    -d api.example.com \
    -d admin.example.com

# Certificado wildcard (requiere DNS challenge)
sudo certbot certonly \
    --manual \
    --preferred-challenges dns \
    -d example.com \
    -d *.example.com
```

#### Configurar Renovación Automática

```bash
# Test renovación automática
sudo certbot renew --dry-run

# Verificar cron job (ya configurado automáticamente)
sudo systemctl status certbot.timer

# Custom script para post-renewal actions
sudo nano /etc/letsencrypt/renewal-hooks/post/reload-services.sh
```

```bash
#!/bin/bash
# /etc/letsencrypt/renewal-hooks/post/reload-services.sh

# Reload Nginx after certificate renewal
systemctl reload nginx

# Restart any services that need the new certificates
systemctl restart your-app-service

# Log renewal
echo "$(date): SSL certificates renewed and services reloaded" >> /var/log/ssl-renewal.log
```

### 2. Configurar Nginx con SSL Optimizado

#### Configuración SSL Avanzada

```nginx
# /etc/nginx/sites-available/app-ssl
server {
    listen 80;
    server_name example.com www.example.com;
    
    # Redirect all HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com www.example.com;
    
    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
    # Modern SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    
    # SSL session settings
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_session_tickets off;
    
    # OCSP stapling
    ssl_stapling on;
    ssl_stapling_verify on;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 5s;
    
    # Security Headers
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always;
    
    # Content Security Policy
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://cdn.jsdelivr.net; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; img-src 'self' data: https:; connect-src 'self' https://api.example.com; frame-src 'none';" always;
    
    # Application configuration
    root /var/www/html/public;
    index index.php index.html;
    
    # Frontend (React)
    location / {
        try_files $uri $uri/ /index.html;
        
        # Cache static assets
        location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
    }
    
    # Backend API (Laravel)
    location /api {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    # PHP processing
    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        
        # Security headers for PHP responses
        fastcgi_hide_header X-Powered-By;
    }
    
    # Deny access to sensitive files
    location ~ /\. {
        deny all;
    }
    
    location ~ /\.env {
        deny all;
    }
    
    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    location /api {
        limit_req zone=api burst=20 nodelay;
        try_files $uri $uri/ /index.php?$query_string;
    }
}
```

### 3. Hardening del Servidor

#### Configurar Firewall (UFW)

```bash
# Reset UFW to defaults
sudo ufw --force reset

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (cambiar puerto si es necesario)
sudo ufw allow 22/tcp

# Allow HTTP and HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow MySQL only from localhost (si es necesario)
sudo ufw allow from 127.0.0.1 to any port 3306

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status verbose
```

#### Configurar Fail2ban

```bash
# Instalar Fail2ban
sudo apt install -y fail2ban

# Crear configuración personalizada
sudo nano /etc/fail2ban/jail.local
```

```ini
# /etc/fail2ban/jail.local
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 5
backend = systemd

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3

[nginx-http-auth]
enabled = true
filter = nginx-http-auth
port = http,https
logpath = /var/log/nginx/error.log

[nginx-limit-req]
enabled = true
filter = nginx-limit-req
port = http,https
logpath = /var/log/nginx/error.log
maxretry = 10

[nginx-botsearch]
enabled = true
filter = nginx-botsearch
port = http,https
logpath = /var/log/nginx/access.log
maxretry = 2

# Custom jail for Laravel login attempts
[laravel-login]
enabled = true
filter = laravel-login
port = http,https
logpath = /var/www/html/storage/logs/laravel.log
maxretry = 5
findtime = 300
bantime = 1800
```

#### Crear Filtros Personalizados

```bash
# Filtro para intentos de login en Laravel
sudo nano /etc/fail2ban/filter.d/laravel-login.conf
```

```ini
# /etc/fail2ban/filter.d/laravel-login.conf
[Definition]
failregex = .*authentication.failed.*"ip":"<HOST>".*
ignoreregex =
```

#### Hardening SSH

```bash
# Editar configuración SSH
sudo nano /etc/ssh/sshd_config
```

```bash
# /etc/ssh/sshd_config - Configuraciones de seguridad

# Cambiar puerto por defecto (opcional)
Port 2222

# Deshabilitar login como root
PermitRootLogin no

# Solo autenticación por clave
PasswordAuthentication no
PubkeyAuthentication yes
AuthenticationMethods publickey

# Limitar usuarios
AllowUsers deployuser adminuser

# Configuraciones de seguridad
Protocol 2
IgnoreRhosts yes
HostbasedAuthentication no
PermitEmptyPasswords no
X11Forwarding no
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2

# Logging
SyslogFacility AUTHPRIV
LogLevel INFO
```

### 4. Configurar HTTPS en Laravel

#### Configuración de Laravel para HTTPS

```php
<?php
// config/app.php

'url' => env('APP_URL', 'https://example.com'),

// Forzar HTTPS en producción
'force_https' => env('FORCE_HTTPS', false),
```

```php
<?php
// app/Providers/AppServiceProvider.php

use Illuminate\Support\Facades\Schema;
use Illuminate\Support\Facades\URL;

public function boot()
{
    Schema::defaultStringLength(191);
    
    // Force HTTPS in production
    if (config('app.force_https')) {
        URL::forceScheme('https');
    }
    
    // Trust proxy headers
    if (config('app.env') === 'production') {
        $this->app['request']->setTrustedProxies(
            ['127.0.0.1', '10.0.0.0/8', '172.16.0.0/12', '192.168.0.0/16'],
            \Illuminate\Http\Request::HEADER_X_FORWARDED_FOR |
            \Illuminate\Http\Request::HEADER_X_FORWARDED_HOST |
            \Illuminate\Http\Request::HEADER_X_FORWARDED_PORT |
            \Illuminate\Http\Request::HEADER_X_FORWARDED_PROTO
        );
    }
}
```

#### Middleware para HTTPS

```php
<?php
// app/Http/Middleware/ForceHttps.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class ForceHttps
{
    public function handle(Request $request, Closure $next)
    {
        if (!$request->isSecure() && app()->environment('production')) {
            return redirect()->secure($request->getRequestUri(), 301);
        }

        return $next($request);
    }
}
```

### 5. Configuración de Seguridad en React

#### Environment Variables para HTTPS

```bash
# frontend/.env.production
HTTPS=true
SSL_CRT_FILE=cert.crt
SSL_KEY_FILE=cert.key
REACT_APP_API_URL=https://api.example.com
```

#### Configuración de Axios para HTTPS

```typescript
// src/services/api.ts
import axios from "axios";

const API_BASE_URL = process.env.REACT_APP_API_URL || "https://localhost:8000";

const api = axios.create({
   baseURL: API_BASE_URL,
   timeout: 10000,
   withCredentials: true,
   headers: {
      "Content-Type": "application/json",
      "Accept": "application/json",
   },
});

// Request interceptor para incluir token
api.interceptors.request.use(
   (config) => {
      const token = localStorage.getItem("auth_token");
      if (token) {
         config.headers.Authorization = `Bearer ${token}`;
      }
      return config;
   },
   (error) => Promise.reject(error),
);

// Response interceptor para manejar errores SSL
api.interceptors.response.use(
   (response) => response,
   (error) => {
      if (error.code === "CERT_HAS_EXPIRED") {
         console.error("SSL Certificate has expired");
         // Handle SSL certificate expiration
      }
      return Promise.reject(error);
   },
);

export default api;
```

### 6. Monitoreo de SSL

#### Script de Monitoreo de Certificados

```bash
#!/bin/bash
# /opt/scripts/ssl-monitor.sh

set -e

DOMAINS=(
    "example.com:443"
    "www.example.com:443"
    "api.example.com:443"
)

ALERT_DAYS=7
SLACK_WEBHOOK="$SSL_ALERT_WEBHOOK"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

send_alert() {
    local message="$1"
    local color="$2"
    
    if [ -n "$SLACK_WEBHOOK" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"text\":\"🔒 SSL Certificate Alert\", \"attachments\":[{\"color\":\"$color\", \"text\":\"$message\"}]}" \
            "$SLACK_WEBHOOK"
    fi
    
    echo "$message" | mail -s "SSL Certificate Alert" admin@example.com
}

check_ssl_expiry() {
    local domain_port="$1"
    local domain="${domain_port%:*}"
    local port="${domain_port#*:}"
    
    log "Checking SSL certificate for $domain:$port"
    
    # Get certificate expiry date
    expiry_date=$(echo | openssl s_client -servername "$domain" -connect "$domain_port" 2>/dev/null | \
                 openssl x509 -noout -enddate 2>/dev/null | \
                 cut -d= -f2)
    
    if [ -z "$expiry_date" ]; then
        send_alert "❌ Could not retrieve SSL certificate for $domain" "danger"
        return 1
    fi
    
    # Convert to timestamp
    expiry_timestamp=$(date -d "$expiry_date" +%s)
    current_timestamp=$(date +%s)
    
    # Calculate days until expiry
    days_until_expiry=$(( (expiry_timestamp - current_timestamp) / 86400 ))
    
    log "Certificate for $domain expires in $days_until_expiry days ($expiry_date)"
    
    if [ $days_until_expiry -le 0 ]; then
        send_alert "🚨 SSL certificate for $domain has EXPIRED!" "danger"
    elif [ $days_until_expiry -le $ALERT_DAYS ]; then
        send_alert "⚠️ SSL certificate for $domain expires in $days_until_expiry days" "warning"
    else
        log "✅ SSL certificate for $domain is valid"
    fi
}

# Check all domains
for domain_port in "${DOMAINS[@]}"; do
    check_ssl_expiry "$domain_port"
done

log "SSL certificate check completed"
```

#### Cron Job para Monitoreo

```bash
# Verificar certificados diariamente
0 8 * * * /opt/scripts/ssl-monitor.sh

# Test SSL configuration weekly
0 2 * * 1 /opt/scripts/ssl-test.sh
```

## Tips

### SSL Best Practices

- **Use Strong Ciphers**: Deshabilita protocolos y cifrados obsoletos
- **Perfect Forward Secrecy**: Usa cipher suites que soporten PFS
- **OCSP Stapling**: Mejora performance de validación SSL
- **HTTP/2**: Aprovecha las ventajas de performance de HTTP/2

### Security Headers

- **HSTS**: Fuerza HTTPS en navegadores
- **CSP**: Previene XSS attacks
- **X-Frame-Options**: Previene clickjacking
- **X-Content-Type-Options**: Previene MIME sniffing

### Monitoring y Alertas

- **Certificate Expiry**: Monitor automático de expiración
- **SSL Labs Testing**: Testing regular con herramientas online
- **Security Scanning**: Scans automáticos de vulnerabilidades
- **Log Analysis**: Análisis de logs para detectar ataques

### Performance Considerations

- **Session Reuse**: Configura SSL session caching
- **OCSP Stapling**: Reduce latencia de validación
- **Certificate Chain**: Optimiza la cadena de certificados
- **HTTP/2 Push**: Para recursos críticos

## Ejemplos

### SSL Test Script

```bash
#!/bin/bash
# Test SSL configuration

domain="example.com"

echo "Testing SSL configuration for $domain..."

# Test SSL Labs grade
echo "Checking SSL Labs grade..."
curl -s "https://api.ssllabs.com/api/v3/analyze?host=$domain" | \
jq -r '.endpoints[0].grade'

# Test cipher suites
echo "Testing cipher suites..."
nmap --script ssl-enum-ciphers -p 443 "$domain"

# Test certificate chain
echo "Verifying certificate chain..."
openssl s_client -connect "$domain:443" -verify_return_error < /dev/null
```

### Automated SSL Renewal with Validation

```bash
#!/bin/bash
# Enhanced SSL renewal with validation

domains="example.com,www.example.com,api.example.com"

# Backup current certificates
cp -r /etc/letsencrypt/live /etc/letsencrypt/backup-$(date +%Y%m%d)

# Renew certificates
if certbot renew --quiet --no-self-upgrade; then
    echo "✅ SSL certificates renewed successfully"
    
    # Test nginx configuration
    if nginx -t; then
        # Reload nginx
        systemctl reload nginx
        echo "✅ Nginx reloaded successfully"
        
        # Test SSL configuration
        for domain in ${domains//,/ }; do
            if curl -sSf "https://$domain/health" > /dev/null; then
                echo "✅ SSL test passed for $domain"
            else
                echo "❌ SSL test failed for $domain"
            fi
        done
    else
        echo "❌ Nginx configuration test failed"
        exit 1
    fi
else
    echo "❌ SSL certificate renewal failed"
    exit 1
fi
```

## Navegación

**Progreso en Despliegue y DevOps:**

- ✅ [Despliegue y DevOps](./despliegue-devops.md)
- ✅
  [Configuración de Servidores de Producción](./configuracion-servidores-produccion.md)
- ✅ [CI/CD Pipelines con GitHub Actions](./ci-cd-pipelines-github-actions.md)
- ✅ [Docker y Contenedores](./docker-contenedores.md)
- ✅ [Monitoreo y Logging](./monitoreo-logging.md)
- ✅ [Backup y Recovery](./backup-recovery-strategies.md)
- ✅ **SSL y Configuraciones de Seguridad** ← Estás aquí

---

### 🎉 Etapa Completada

¡Felicitaciones! Has completado la **Etapa 8: Despliegue y DevOps**.

**Próximo paso**: Continúa con la [**Etapa 9: Mantenimiento y Evolución**](../step_09/mantenimiento-evolucion.md).

[⬅️ Backup y Recovery](./backup-recovery-strategies.md) | [🏠 README Principal](../../README.md) | [➡️ Mantenimiento y Evolución](../step_09/mantenimiento-evolucion.md)
