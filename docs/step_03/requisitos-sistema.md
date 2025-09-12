# Requisitos Mínimos del Sistema

## ¿Qué son los Requisitos Mínimos del Sistema?

Los **Requisitos Mínimos del Sistema** son las especificaciones técnicas y de
hardware/software necesarias para ejecutar el entorno de desarrollo completo del
proyecto. Estos requisitos garantizan que todos los miembros del equipo puedan
trabajar eficientemente con el stack tecnológico definido (**React +
TypeScript + Vite** para frontend, **Laravel + Clean Architecture** para
backend) sin limitaciones de rendimiento.

Esta documentación establece las **versiones mínimas** de herramientas,
**especificaciones de hardware** recomendadas, y **dependencias del sistema**
requeridas para ejecutar Docker, Node.js, PHP, y todas las herramientas de
desarrollo y testing del proyecto.

## ¿Por qué es Importante?

### 🎯 **Consistencia del Entorno**

- Garantiza que todos los desarrolladores trabajen con versiones compatibles
- Evita problemas de compatibilidad entre diferentes sistemas operativos
- Asegura rendimiento óptimo para todas las herramientas del stack
- Facilita la resolución de problemas técnicos

### 🚀 **Productividad del Equipo**

- Reduce tiempo perdido en configuraciones incompatibles
- Elimina errores relacionados con versiones obsoletas
- Facilita onboarding rápido de nuevos desarrolladores
- Garantiza que CI/CD funcione correctamente en todos los entornos

### 🛡️ **Estabilidad y Seguridad**

- Asegura que se usen versiones con soporte de seguridad activo
- Evita vulnerabilidades conocidas en versiones obsoletas
- Garantiza compatibilidad con las últimas características de seguridad
- Facilita actualizaciones coordinadas del stack

### 📈 **Escalabilidad y Performance**

- Asegura recursos suficientes para desarrollo local con Docker
- Garantiza capacidad para ejecutar tests completos
- Permite trabajar con bases de datos de desarrollo de tamaño considerable
- Soporta múltiples servicios ejecutándose simultáneamente

## ¿Qué debe incluir?

### 💻 **Especificaciones de Hardware**

#### **Requisitos Mínimos**

- **CPU**: Procesador de 64 bits con 4 núcleos (Intel i5 8va gen / AMD Ryzen 5
  3600 o equivalente)
- **RAM**: 8 GB mínimo, **16 GB recomendado** para desarrollo con Docker
- **Almacenamiento**: 50 GB de espacio libre en SSD
- **Red**: Conexión estable a internet para instalación de dependencias

#### **Requisitos Recomendados**

- **CPU**: Procesador de 64 bits con 8 núcleos (Intel i7 10ma gen / AMD Ryzen 7
  5700X o superior)
- **RAM**: 32 GB para desarrollo avanzado con múltiples servicios
- **Almacenamiento**: 100 GB en SSD NVMe para máximo rendimiento
- **Red**: Conexión de banda ancha (100+ Mbps) para containers y npm/composer

### 🖥️ **Sistemas Operativos Soportados**

#### **Windows**

- **Windows 10** versión 21H2 (build 19044) o superior
- **Windows 11** (recomendado)
- **WSL 2** habilitado para mejor compatibilidad con Docker

#### **macOS**

- **macOS Monterey** (12.0) o superior
- **macOS Ventura** (13.0) o **Sonoma** (14.0) recomendado
- Procesadores Intel x64 o Apple Silicon (M1/M2) soportados

#### **Linux**

- **Ubuntu 20.04 LTS** o **22.04 LTS** (recomendado)
- **Debian 11** (Bullseye) o superior
- **CentOS 8** / **RHEL 8** o superior
- **Arch Linux** (rolling release)

### 🛠️ **Herramientas de Sistema Requeridas**

#### **Control de Versiones**

- **Git**: 2.34.0 o superior
- **GitHub CLI** (opcional pero recomendado): 2.20.0 o superior

#### **Docker y Containerización**

- **Docker Desktop**: 4.15.0 o superior (Windows/macOS)
- **Docker Engine**: 20.10.0 o superior (Linux)
- **Docker Compose**: 2.12.0 o superior (incluido en Docker Desktop)

#### **Runtime de JavaScript**

- **Node.js**: 18.17.0 LTS o superior (**20.x LTS recomendado**)
- **npm**: 9.0.0 o superior (incluido con Node.js)
- **Yarn** (opcional): 3.6.0 o superior

#### **Runtime de PHP**

- **PHP**: 8.2.0 o superior (**8.3.x recomendado**)
- **Composer**: 2.4.0 o superior
- **Extensiones PHP requeridas**:
  - `php-curl`, `php-dom`, `php-fileinfo`, `php-filter`
  - `php-hash`, `php-mbstring`, `php-openssl`, `php-pcre`
  - `php-pdo`, `php-tokenizer`, `php-xml`, `php-zip`
  - `php-mysql`, `php-redis` (para desarrollo local)

### 🔧 **Herramientas de Desarrollo (Opcionales)**

#### **IDEs y Editores Recomendados**

- **Visual Studio Code**: 1.80.0 o superior
  - Extensiones recomendadas: ESLint, Prettier, PHP Intelephense, Docker
- **PHPStorm**: 2023.2 o superior (para desarrollo PHP avanzado)
- **WebStorm**: 2023.2 o superior (para desarrollo frontend avanzado)

#### **Terminales Mejorados**

- **Windows Terminal** (Windows)
- **iTerm2** (macOS)
- **Terminator** o **Alacritty** (Linux)

#### **Herramientas de Base de Datos**

- **MySQL Workbench**: 8.0.30 o superior
- **phpMyAdmin** (incluido en Docker Compose)
- **TablePlus** o **Sequel Pro** (macOS)
- **DBeaver** (multiplataforma)

## ¿Qué debo hacer?

### 1. **Verificar Hardware y Sistema Operativo**

#### **1.1 Comprobar Especificaciones**

```bash
# Windows (PowerShell)
Get-ComputerInfo | Select-Object TotalPhysicalMemory, CsProcessors
wmic cpu get name
wmic diskdrive get size,model

# macOS
system_profiler SPHardwareDataType
sysctl hw.memsize hw.ncpu
df -h

# Linux
lscpu
free -h
df -h
lsblk
```

#### **1.2 Verificar Versión del SO**

```bash
# Windows
winver

# macOS
sw_vers

# Linux
lsb_release -a
# o
cat /etc/os-release
```

### 2. **Instalar y Configurar Herramientas Base**

#### **2.1 Instalar Git**

```bash
# Windows (con Chocolatey)
choco install git

# macOS (con Homebrew)
brew install git

# Ubuntu/Debian
sudo apt update && sudo apt install git

# CentOS/RHEL
sudo yum install git
```

#### **2.2 Configurar Git**

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu.email@ejemplo.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
```

#### **2.3 Instalar Docker Desktop**

**Windows/macOS**:

- Descargar desde [docker.com](https://www.docker.com/products/docker-desktop)
- Ejecutar instalador y reiniciar sistema
- Habilitar WSL 2 integration (Windows)

**Linux (Ubuntu)**:

```bash
# Actualizar paquetes
sudo apt update

# Instalar dependencias
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release

# Agregar clave GPG de Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Agregar repositorio
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Agregar usuario al grupo docker
sudo usermod -aG docker $USER
newgrp docker
```

#### **2.4 Instalar Node.js y npm**

```bash
# Windows (con Chocolatey)
choco install nodejs

# macOS (con Homebrew)
brew install node

# Linux (con NodeSource)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verificar instalación
node --version
npm --version
```

#### **2.5 Instalar PHP y Composer**

```bash
# Windows (con Chocolatey)
choco install php composer

# macOS (con Homebrew)
brew install php composer

# Ubuntu/Debian
sudo apt update
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-curl php8.2-dom php8.2-mbstring php8.2-mysql php8.2-xml php8.2-zip
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer

# Verificar instalación
php --version
composer --version
```

### 3. **Verificar Instalación Completa**

#### **3.1 Script de Verificación**

```bash
#!/bin/bash
# scripts/verify-system.sh

echo "🔍 Verificando requisitos del sistema..."

# Verificar Git
if command -v git &> /dev/null; then
    echo "✅ Git: $(git --version)"
else
    echo "❌ Git no está instalado"
fi

# Verificar Docker
if command -v docker &> /dev/null; then
    echo "✅ Docker: $(docker --version)"
    if docker compose version &> /dev/null; then
        echo "✅ Docker Compose: $(docker compose version)"
    else
        echo "❌ Docker Compose no está disponible"
    fi
else
    echo "❌ Docker no está instalado"
fi

# Verificar Node.js
if command -v node &> /dev/null; then
    echo "✅ Node.js: $(node --version)"
else
    echo "❌ Node.js no está instalado"
fi

# Verificar npm
if command -v npm &> /dev/null; then
    echo "✅ npm: $(npm --version)"
else
    echo "❌ npm no está instalado"
fi

# Verificar PHP
if command -v php &> /dev/null; then
    echo "✅ PHP: $(php --version | head -n 1)"
else
    echo "❌ PHP no está instalado"
fi

# Verificar Composer
if command -v composer &> /dev/null; then
    echo "✅ Composer: $(composer --version)"
else
    echo "❌ Composer no está instalado"
fi

# Verificar recursos del sistema
echo ""
echo "💻 Recursos del sistema:"

# RAM disponible
if [[ "$OSTYPE" == "linux-gnu"* ]]; then
    RAM=$(free -h | awk '/^Mem:/ {print $2}')
    echo "🧠 RAM total: $RAM"
elif [[ "$OSTYPE" == "darwin"* ]]; then
    RAM=$(sysctl hw.memsize | awk '{print $2/1024/1024/1024 " GB"}')
    echo "🧠 RAM total: $RAM"
fi

# Espacio en disco
echo "💾 Espacio en disco:"
df -h . | tail -1 | awk '{print "   Disponible: " $4 " de " $2}'

echo ""
echo "🎉 Verificación completada!"
```

#### **3.2 Ejecutar Verificación**

```bash
# Hacer script ejecutable
chmod +x scripts/verify-system.sh

# Ejecutar verificación
./scripts/verify-system.sh
```

## Tips

### 💡 **Optimización de Performance**

- **SSD recomendado**: Docker y Node.js se benefician enormemente de
  almacenamiento SSD
- **RAM suficiente**: 16GB+ permite ejecutar múltiples containers
  simultáneamente
- **CPU moderno**: Procesadores con 8+ núcleos aceleran builds y tests
  significativamente

### 🐳 **Configuración de Docker**

- **Asignar memoria**: En Docker Desktop, asignar al menos 4GB de RAM para
  containers
- **WSL 2**: En Windows, usar WSL 2 backend para mejor rendimiento
- **File sharing**: Configurar carpetas compartidas para mejor performance

### 🔧 **Herramientas de Desarrollo**

- **VS Code**: Instalar extensiones recomendadas desde el workspace
- **Terminal mejorado**: Usar terminales modernos para mejor experiencia de
  desarrollo
- **Git hooks**: Configurar pre-commit hooks para validaciones automáticas

### 📦 **Gestión de Dependencias**

- **Node.js**: Usar Node Version Manager (nvm) para alternar entre versiones
- **PHP**: Usar phpenv o similar para gestionar múltiples versiones
- **Composer**: Mantener Composer actualizado para mejor performance

## Ejemplos

### 🖥️ **Configuración Típica de Desarrollo**

#### **Setup de Windows 11 + WSL 2**

```bash
# 1. Habilitar WSL 2
wsl --install

# 2. Instalar Ubuntu 22.04
wsl --install -d Ubuntu-22.04

# 3. Configurar Docker Desktop para usar WSL 2
# Settings > General > Use WSL 2 based engine

# 4. Instalar herramientas en WSL
sudo apt update
sudo apt install git nodejs npm php8.2 composer

# 5. Clonar proyecto en WSL
cd /home/username
git clone https://github.com/usuario/proyecto.git
```

#### **Setup de macOS con Homebrew**

```bash
# 1. Instalar Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Instalar herramientas
brew install git node php composer docker

# 3. Instalar Docker Desktop manualmente
# Descargar desde docker.com

# 4. Configurar Git
git config --global user.name "Nombre"
git config --global user.email "email@ejemplo.com"
```

#### **Setup de Ubuntu 22.04**

```bash
# 1. Actualizar sistema
sudo apt update && sudo apt upgrade

# 2. Instalar herramientas
sudo apt install git curl wget

# 3. Instalar Node.js
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

# 4. Instalar PHP
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-curl php8.2-dom php8.2-mbstring php8.2-mysql php8.2-xml php8.2-zip

# 5. Instalar Composer
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer

# 6. Instalar Docker
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

### 🧪 **Verificación de Compatibilidad**

#### **Test de Performance Básico**

```bash
# Test de Docker
time docker run hello-world

# Test de Node.js
time npm --version

# Test de PHP
time php -v

# Test de Composer
time composer --version

# Test de Git
time git --version
```

#### **Test de Recursos**

```bash
# Monitorear uso de recursos durante desarrollo
# Linux/macOS
htop

# Windows
taskmgr

# Verificar que Docker tenga recursos suficientes
docker system df
docker system info
```

## Navegación

[⬅️ Configuración del Entorno - Introducción](./configuracion-entorno-desarrollo.md)
| [🏠 README Principal](../../README.md) |
[Instalación de dependencias iniciales ➡️](./instalacion-dependencias-iniciales.md)
