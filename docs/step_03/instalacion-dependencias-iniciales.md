# Instalación de Dependencias Iniciales

## ¿Qué es la Instalación de Dependencias Iniciales?

La **Instalación de Dependencias Iniciales** es el proceso de configurar e
instalar todas las herramientas fundamentales necesarias para el desarrollo del
proyecto antes de comenzar con la configuración específica del entorno. Esta
etapa garantiza que todos los desarrolladores tengan las mismas versiones de
herramientas base y que el sistema esté preparado para ejecutar Docker, Node.js,
PHP, y todas las tecnologías del stack definido.

Este proceso incluye la instalación de **herramientas de sistema**, **gestores
de paquetes**, **runtimes de lenguajes de programación**, y **utilitarios de
desarrollo** que servirán como base para todas las configuraciones posteriores
del entorno de desarrollo.

La instalación sigue un orden específico para evitar conflictos de dependencias
y garantizar que cada herramienta se configure correctamente antes de proceder
con las siguientes.

## ¿Por qué es Importante?

### 🎯 **Base Sólida para el Desarrollo**

- Establece un entorno consistente para todos los miembros del equipo
- Evita problemas de compatibilidad entre diferentes versiones de herramientas
- Garantiza que las configuraciones posteriores funcionen correctamente
- Facilita la resolución de problemas al tener un punto de partida común

### 🚀 **Eficiencia y Productividad**

- Elimina tiempo perdido en instalaciones ad-hoc durante el desarrollo
- Reduce errores relacionados con dependencias faltantes o incorrectas
- Facilita el onboarding de nuevos desarrolladores
- Permite automatizar verificaciones de entorno

### 🛡️ **Seguridad y Estabilidad**

- Asegura el uso de versiones con soporte de seguridad activo
- Evita vulnerabilidades conocidas en versiones obsoletas
- Establece configuraciones seguras desde el inicio
- Facilita actualizaciones coordinadas del stack

### 🔧 **Soporte para Arquitectura del Proyecto**

- Instala herramientas específicas para Clean Architecture + DDD
- Configura entorno para desarrollo con TypeScript y tipado fuerte
- Prepara sistema para contenedorización con Docker
- Establece base para CI/CD y automatización

## ¿Qué debe incluir?

### 🖥️ **Herramientas de Sistema Base**

#### **Control de Versiones**

- **Git**: 2.34.0+ para control de versiones distribuido
- **GitHub CLI**: 2.20.0+ para integración con GitHub (opcional)
- Configuración inicial de usuario y credenciales

#### **Gestores de Paquetes de Sistema**

- **Windows**: Chocolatey o Winget para instalación de herramientas
- **macOS**: Homebrew para gestión de paquetes
- **Linux**: apt/yum/pacman según distribución

### 🐳 **Plataforma de Contenedorización**

#### **Docker y Docker Compose**

- **Docker Desktop**: 4.15.0+ (Windows/macOS)
- **Docker Engine**: 20.10.0+ (Linux)
- **Docker Compose**: 2.12.0+ para orquestación de servicios
- Configuración de recursos (RAM, CPU)

### 📦 **Runtimes y Gestores de Dependencias**

#### **JavaScript/TypeScript**

- **Node.js**: 18.17.0 LTS o 20.x LTS (recomendado)
- **npm**: 9.0.0+ (incluido con Node.js)
- **Yarn**: 3.6.0+ (opcional, como alternativa a npm)

#### **PHP**

- **PHP**: 8.2.0+ o 8.3.x (recomendado)
- **Composer**: 2.4.0+ para gestión de dependencias PHP
- **Extensiones PHP**: curl, dom, fileinfo, mbstring, xml, zip, mysql

### 🛠️ **Herramientas de Desarrollo**

#### **Editores e IDEs (Opcional)**

- **Visual Studio Code**: 1.80.0+ con extensiones recomendadas
- **PHPStorm**: 2023.2+ para desarrollo PHP avanzado
- **WebStorm**: 2023.2+ para desarrollo frontend avanzado

#### **Terminales Mejorados (Opcional)**

- **Windows Terminal**: Para mejor experiencia en Windows
- **iTerm2**: Terminal mejorado para macOS
- **Zsh + Oh My Zsh**: Shell mejorado para Linux/macOS

### 🔍 **Herramientas de Verificación**

#### **Verificadores de Instalación**

- Scripts de verificación automática
- Herramientas de diagnóstico de entorno
- Validadores de versiones mínimas

## ¿Qué debo hacer?

### 1. **Preparar el Sistema**

#### **1.1 Verificar Requisitos del Sistema**

Antes de comenzar, asegúrate de que tu sistema cumple con los
[requisitos mínimos](./requisitos-sistema.md):

```bash
# Verificar sistema operativo
# Windows
winver

# macOS
sw_vers

# Linux
lsb_release -a
```

#### **1.2 Actualizar Sistema Operativo**

```bash
# Windows (PowerShell como Administrador)
Get-WindowsUpdate -Install -AcceptAll

# macOS
sudo softwareupdate -ia

# Ubuntu/Debian
sudo apt update && sudo apt upgrade -y

# CentOS/RHEL
sudo yum update -y
```

### 2. **Instalar Gestores de Paquetes**

#### **2.1 Windows - Chocolatey**

```powershell
# Ejecutar PowerShell como Administrador
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Verificar instalación
choco --version
```

#### **2.2 macOS - Homebrew**

```bash
# Instalar Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Agregar al PATH (seguir instrucciones del instalador)
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

# Verificar instalación
brew --version
```

#### **2.3 Linux - Preparar repositorios**

```bash
# Ubuntu/Debian - Actualizar repositorios
sudo apt update

# CentOS/RHEL - Habilitar EPEL
sudo yum install epel-release -y

# Arch Linux - Actualizar repositorios
sudo pacman -Syu
```

### 3. **Instalar Git y Configurar**

#### **3.1 Instalación de Git**

```bash
# Windows (Chocolatey)
choco install git -y

# macOS (Homebrew)
brew install git

# Ubuntu/Debian
sudo apt install git -y

# CentOS/RHEL
sudo yum install git -y

# Arch Linux
sudo pacman -S git
```

#### **3.2 Configuración Inicial de Git**

```bash
# Configurar usuario y email
git config --global user.name "Tu Nombre Completo"
git config --global user.email "tu.email@ejemplo.com"

# Configurar rama por defecto
git config --global init.defaultBranch main

# Configurar estrategia de pull
git config --global pull.rebase false

# Configurar editor (opcional)
git config --global core.editor "code --wait"

# Verificar configuración
git config --list --global
```

#### **3.3 Configurar SSH para GitHub (Recomendado)**

```bash
# Generar clave SSH
ssh-keygen -t ed25519 -C "tu.email@ejemplo.com"

# Iniciar ssh-agent
eval "$(ssh-agent -s)"

# Agregar clave privada
ssh-add ~/.ssh/id_ed25519

# Mostrar clave pública para agregar a GitHub
cat ~/.ssh/id_ed25519.pub

# Verificar conexión (después de agregar la clave a GitHub)
ssh -T git@github.com
```

### 4. **Instalar Docker**

#### **4.1 Windows - Docker Desktop**

```powershell
# Opción 1: Con Chocolatey
choco install docker-desktop -y

# Opción 2: Descarga manual desde https://www.docker.com/products/docker-desktop/
# Ejecutar instalador y reiniciar sistema

# Habilitar WSL 2 (requerido)
wsl --install
# Reiniciar sistema después de la instalación
```

#### **4.2 macOS - Docker Desktop**

```bash
# Opción 1: Con Homebrew
brew install --cask docker

# Opción 2: Descarga manual desde https://www.docker.com/products/docker-desktop/
# Arrastrar Docker.app a /Applications

# Iniciar Docker Desktop desde Aplicaciones
open /Applications/Docker.app
```

#### **4.3 Linux - Docker Engine**

```bash
# Ubuntu/Debian
# Actualizar paquetes
sudo apt update

# Instalar dependencias
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release -y

# Agregar clave GPG oficial de Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Agregar repositorio Docker
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Agregar usuario al grupo docker
sudo usermod -aG docker $USER

# Activar cambios de grupo
newgrp docker

# Habilitar Docker para iniciar automáticamente
sudo systemctl enable docker
sudo systemctl start docker
```

#### **4.4 Verificar Instalación de Docker**

```bash
# Verificar versión de Docker
docker --version

# Verificar Docker Compose
docker compose version

# Probar Docker con hello-world
docker run hello-world

# Verificar información del sistema Docker
docker system info
```

### 5. **Instalar Node.js y npm**

#### **5.1 Instalación de Node.js**

```bash
# Windows (Chocolatey)
choco install nodejs -y

# macOS (Homebrew)
brew install node

# Ubuntu/Debian (NodeSource repository)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

# CentOS/RHEL (NodeSource repository)
curl -fsSL https://rpm.nodesource.com/setup_lts.x | sudo bash -
sudo yum install -y nodejs

# Arch Linux
sudo pacman -S nodejs npm
```

#### **5.2 Verificar y Configurar npm**

```bash
# Verificar versiones
node --version
npm --version

# Configurar npm (opcional)
npm config set init.author.name "Tu Nombre"
npm config set init.author.email "tu.email@ejemplo.com"
npm config set init.license "MIT"

# Actualizar npm a la última versión
npm install -g npm@latest

# Verificar configuración global
npm config list
```

#### **5.3 Instalar Yarn (Opcional)**

```bash
# Instalar Yarn globalmente
npm install -g yarn

# Verificar versión
yarn --version

# Configurar Yarn
yarn config set init-author-name "Tu Nombre"
yarn config set init-author-email "tu.email@ejemplo.com"
```

### 6. **Instalar PHP y Composer**

#### **6.1 Instalación de PHP**

```bash
# Windows (Chocolatey)
choco install php -y

# macOS (Homebrew)
brew install php

# Ubuntu/Debian
sudo apt update
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-curl php8.2-dom php8.2-fileinfo php8.2-mbstring php8.2-mysql php8.2-xml php8.2-zip -y

# CentOS/RHEL (con Remi repository)
sudo yum install epel-release -y
sudo yum install https://rpms.remirepo.net/enterprise/remi-release-8.rpm -y
sudo yum module enable php:remi-8.2 -y
sudo yum install php php-cli php-common php-curl php-dom php-fileinfo php-mbstring php-mysqlnd php-xml php-zip -y

# Arch Linux
sudo pacman -S php php-composer
```

#### **6.2 Verificar Extensiones PHP**

```bash
# Verificar versión de PHP
php --version

# Verificar extensiones instaladas
php -m

# Verificar extensiones requeridas específicamente
php -m | grep -E "(curl|dom|fileinfo|mbstring|mysql|xml|zip)"
```

#### **6.3 Instalar Composer**

```bash
# Instalación global con script oficial
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer

# En Windows (alternativo con Chocolatey)
choco install composer -y

# En macOS (alternativo con Homebrew)
brew install composer

# Verificar instalación
composer --version

# Configurar Composer
composer config --global repo.packagist composer https://packagist.org
```

### 7. **Instalar Herramientas Adicionales**

#### **7.1 GitHub CLI (Opcional pero Recomendado)**

```bash
# Windows (Chocolatey)
choco install gh -y

# macOS (Homebrew)
brew install gh

# Ubuntu/Debian
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh -y

# Autenticación con GitHub
gh auth login
```

#### **7.2 Visual Studio Code (Opcional)**

```bash
# Windows (Chocolatey)
choco install vscode -y

# macOS (Homebrew)
brew install --cask visual-studio-code

# Ubuntu/Debian
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code -y
```

### 8. **Verificación Final de la Instalación**

#### **8.1 Script de Verificación Completa**

Crear y ejecutar script `scripts/verify-installation.sh`:

```bash
#!/bin/bash
echo "🔍 Verificando instalación completa de dependencias..."
echo ""

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

# Verificar Node.js y npm
if command -v node &> /dev/null; then
    echo "✅ Node.js: $(node --version)"
else
    echo "❌ Node.js no está instalado"
fi

if command -v npm &> /dev/null; then
    echo "✅ npm: $(npm --version)"
else
    echo "❌ npm no está instalado"
fi

# Verificar PHP
if command -v php &> /dev/null; then
    echo "✅ PHP: $(php --version | head -n 1)"
    
    # Verificar extensiones críticas
    REQUIRED_EXTENSIONS=("curl" "dom" "fileinfo" "mbstring" "mysql" "xml" "zip")
    MISSING_EXTENSIONS=()
    
    for ext in "${REQUIRED_EXTENSIONS[@]}"; do
        if ! php -m | grep -q "$ext"; then
            MISSING_EXTENSIONS+=("$ext")
        fi
    done
    
    if [ ${#MISSING_EXTENSIONS[@]} -eq 0 ]; then
        echo "✅ Extensiones PHP: Todas las extensiones requeridas están instaladas"
    else
        echo "❌ Extensiones PHP faltantes: ${MISSING_EXTENSIONS[*]}"
    fi
else
    echo "❌ PHP no está instalado"
fi

# Verificar Composer
if command -v composer &> /dev/null; then
    echo "✅ Composer: $(composer --version)"
else
    echo "❌ Composer no está instalado"
fi

# Verificar GitHub CLI (opcional)
if command -v gh &> /dev/null; then
    echo "✅ GitHub CLI: $(gh --version | head -n 1)"
else
    echo "⚠️  GitHub CLI no está instalado (opcional)"
fi

# Verificar Visual Studio Code (opcional)
if command -v code &> /dev/null; then
    echo "✅ Visual Studio Code: $(code --version | head -n 1)"
else
    echo "⚠️  Visual Studio Code no está instalado (opcional)"
fi

echo ""
echo "💻 Información del sistema:"

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
echo "🎉 Verificación de instalación completada!"
```

#### **8.2 Ejecutar Verificación**

```bash
# Hacer script ejecutable
chmod +x scripts/verify-installation.sh

# Ejecutar verificación
./scripts/verify-installation.sh
```

#### **8.3 Prueba de Funcionalidad**

```bash
# Probar Docker con un container simple
docker run --rm hello-world

# Probar Node.js
node -e "console.log('Node.js funcionando correctamente!')"

# Probar npm
npm init -y
rm package.json

# Probar PHP
php -r "echo 'PHP funcionando correctamente!' . PHP_EOL;"

# Probar Composer
composer about

# Probar Git
git --version
```

## Tips

### 💡 **Optimización de Instalación**

- **Instalación en lotes**: Agrupa instalaciones por gestor de paquetes para
  mayor eficiencia
- **Cache de paquetes**: Configura cache local para npm y composer para
  instalaciones más rápidas
- **Versiones específicas**: Instala versiones LTS para mayor estabilidad
- **Verificación continua**: Ejecuta verificaciones después de cada instalación

### 🔧 **Configuración Post-Instalación**

- **Variables de entorno**: Configura PATH correctamente para todas las
  herramientas
- **Alias útiles**: Crea alias para comandos frecuentemente usados
- **Configuración de shells**: Configura bash/zsh con completado automático
- **Integración de IDEs**: Configura extensiones recomendadas automáticamente

### 🛡️ **Seguridad**

- **Verificación de integridad**: Verifica checksums de instaladores cuando sea
  posible
- **Fuentes oficiales**: Instala siempre desde repositorios oficiales
- **Actualizaciones regulares**: Mantén herramientas actualizadas para seguridad
- **Configuración segura**: Usa configuraciones seguras por defecto

### 🚀 **Performance**

- **SSD recomendado**: Docker y Node.js se benefician enormemente de
  almacenamiento SSD
- **RAM suficiente**: Asigna al menos 4GB de RAM para Docker Desktop
- **CPU moderno**: Procesadores con múltiples núcleos aceleran builds
  significativamente
- **Configuración de Docker**: Ajusta límites de memoria y CPU según hardware
  disponible

## Ejemplos

### 🖥️ **Script de Instalación Automática por Plataforma**

#### **Windows - install-windows.ps1**

```powershell
# Ejecutar como Administrador
Write-Host "🚀 Instalando dependencias para Windows..." -ForegroundColor Green

# Instalar Chocolatey si no existe
if (!(Get-Command choco -ErrorAction SilentlyContinue)) {
    Write-Host "📦 Instalando Chocolatey..." -ForegroundColor Yellow
    Set-ExecutionPolicy Bypass -Scope Process -Force
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
    iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
}

# Instalar herramientas principales
Write-Host "🛠️ Instalando herramientas principales..." -ForegroundColor Yellow
choco install git nodejs php composer docker-desktop vscode gh -y

# Habilitar WSL 2
Write-Host "🐧 Habilitando WSL 2..." -ForegroundColor Yellow
wsl --install

Write-Host "✅ Instalación completada! Reinicia el sistema para finalizar." -ForegroundColor Green
```

#### **macOS - install-macos.sh**

```bash
#!/bin/bash
echo "🚀 Instalando dependencias para macOS..."

# Instalar Homebrew si no existe
if ! command -v brew &> /dev/null; then
    echo "📦 Instalando Homebrew..."
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
fi

# Actualizar Homebrew
brew update

# Instalar herramientas principales
echo "🛠️ Instalando herramientas principales..."
brew install git node php composer gh
brew install --cask docker visual-studio-code

echo "✅ Instalación completada!"
```

#### **Ubuntu - install-ubuntu.sh**

```bash
#!/bin/bash
echo "🚀 Instalando dependencias para Ubuntu..."

# Actualizar sistema
sudo apt update && sudo apt upgrade -y

# Instalar Git
echo "📦 Instalando Git..."
sudo apt install git -y

# Instalar Node.js
echo "📦 Instalando Node.js..."
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

# Instalar PHP
echo "📦 Instalando PHP..."
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-curl php8.2-dom php8.2-fileinfo php8.2-mbstring php8.2-mysql php8.2-xml php8.2-zip -y

# Instalar Composer
echo "📦 Instalando Composer..."
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer

# Instalar Docker
echo "📦 Instalando Docker..."
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER

# Instalar GitHub CLI
echo "📦 Instalando GitHub CLI..."
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh -y

# Instalar VS Code
echo "📦 Instalando Visual Studio Code..."
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code -y

echo "✅ Instalación completada! Reinicia la sesión para aplicar cambios de grupo Docker."
```

### 🔍 **Configuración Post-Instalación**

#### **Configuración de Git**

```bash
# Script de configuración de Git
configure_git() {
    echo "🔧 Configurando Git..."
    
    read -p "Ingresa tu nombre completo: " name
    read -p "Ingresa tu email: " email
    
    git config --global user.name "$name"
    git config --global user.email "$email"
    git config --global init.defaultBranch main
    git config --global pull.rebase false
    
    echo "✅ Git configurado correctamente"
}
```

#### **Configuración de Docker**

```bash
# Script de configuración de Docker
configure_docker() {
    echo "🐳 Configurando Docker..."
    
    # Verificar que Docker esté ejecutándose
    if ! docker system info &> /dev/null; then
        echo "❌ Docker no está ejecutándose. Inicia Docker Desktop."
        return 1
    fi
    
    # Configurar recursos (ejemplo para Linux)
    echo "Configurando recursos de Docker..."
    
    # Verificar instalación con hello-world
    docker run --rm hello-world
    
    echo "✅ Docker configurado correctamente"
}
```

## Navegación

[⬅️ Requisitos mínimos del sistema](./requisitos-sistema.md) |
[🏠 README Principal](../../README.md) |
[Configuración de repositorios Git y GitHub ➡️](./configuracion-repositorios-git.md)
