# Preparación App Stores

## Conocimientos Involucrados
- Generación de builds de producción
- Configuración de App Store Connect y Google Play Console
- Cumplimiento de políticas de distribución

## Responsable
- Equipo de DevOps

## ¿Qué es?

Es el proceso de preparación, configuración y publicación de la aplicación móvil
en las tiendas de aplicaciones (App Store y Google Play Store). Incluye la
configuración de metadatos, generación de builds de producción, compliance con
políticas de las tiendas y estrategias de distribución.

## ¿Por qué es importante?

- **Distribución Global**: Acceso a millones de usuarios
- **Monetización**: Plataforma para generar ingresos
- **Credibilidad**: Presencia oficial en tiendas reconocidas
- **Updates Automáticos**: Sistema de actualizaciones automáticas
- **Analytics**: Métricas detalladas de uso y descargas
- **Security**: Proceso de revisión que garantiza seguridad

## ¿Qué debe incluir?

### Configuración de Build

- Release builds optimizados
- Code signing configurado
- ProGuard/R8 para Android
- App Store Connect setup
- Google Play Console setup

### Assets y Metadatos

- Iconos en todas las resoluciones
- Screenshots para diferentes dispositivos
- Descripción de la app
- Keywords para ASO (App Store Optimization)
- Privacy policy y términos de uso

### Compliance

- Cumplimiento de políticas de las tiendas
- Permisos necesarios documentados
- Edad mínima y ratings
- Funcionalidades de accesibilidad
- Protección de datos (GDPR, CCPA)

### Distribución

- Beta testing con TestFlight/Internal Testing
- Phased rollout strategy
- Release notes
- Version management

## ¿Qué debo hacer?

### 1. Configuración iOS (App Store)

#### Configuración en Xcode

**ios/MyApp/Info.plist**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>CFBundleDisplayName</key>
    <string>MyApp</string>
    <key>CFBundleIdentifier</key>
    <string>com.company.myapp</string>
    <key>CFBundleVersion</key>
    <string>1</string>
    <key>CFBundleShortVersionString</key>
    <string>1.0.0</string>
    <key>NSCameraUsageDescription</key>
    <string>Esta app necesita acceso a la cámara para tomar fotos de perfil</string>
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>Esta app necesita acceso a la ubicación para mostrar contenido relevante</string>
    <key>NSPhotoLibraryUsageDescription</key>
    <string>Esta app necesita acceso a la galería para seleccionar fotos de perfil</string>
    <key>UIRequiredDeviceCapabilities</key>
    <array>
        <string>arm64</string>
    </array>
    <key>LSRequiresIPhoneOS</key>
    <true/>
    <key>UILaunchStoryboardName</key>
    <string>LaunchScreen</string>
    <key>UISupportedInterfaceOrientations</key>
    <array>
        <string>UIInterfaceOrientationPortrait</string>
        <string>UIInterfaceOrientationPortraitUpsideDown</string>
    </array>
    <key>UIViewControllerBasedStatusBarAppearance</key>
    <false/>
</dict>
</plist>
```

#### Script de Build para Release

**scripts/build-ios.sh**:

```bash
#!/bin/bash

# Configuración
SCHEME="MyApp"
WORKSPACE="ios/MyApp.xcworkspace"
CONFIGURATION="Release"
EXPORT_PATH="ios/build"

echo "🚀 Building iOS Release..."

# Limpiar build anterior
rm -rf $EXPORT_PATH
mkdir -p $EXPORT_PATH

# Build del archivo
xcodebuild \
  -workspace $WORKSPACE \
  -scheme $SCHEME \
  -configuration $CONFIGURATION \
  -archivePath "$EXPORT_PATH/MyApp.xcarchive" \
  archive

# Exportar IPA
xcodebuild \
  -exportArchive \
  -archivePath "$EXPORT_PATH/MyApp.xcarchive" \
  -exportPath $EXPORT_PATH \
  -exportOptionsPlist ios/ExportOptions.plist

echo "✅ iOS build completed!"
echo "📱 IPA location: $EXPORT_PATH/MyApp.ipa"
```

**ios/ExportOptions.plist**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>app-store</string>
    <key>teamID</key>
    <string>TEAM_ID_HERE</string>
    <key>uploadBitcode</key>
    <false/>
    <key>compileBitcode</key>
    <true/>
    <key>uploadSymbols</key>
    <true/>
</dict>
</plist>
```

### 2. Configuración Android (Google Play)

#### Configuración de Build

**android/app/build.gradle**:

```gradle
android {
    compileSdkVersion rootProject.ext.compileSdkVersion

    defaultConfig {
        applicationId "com.company.myapp"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0.0"
        multiDexEnabled true
    }

    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }

    buildTypes {
        debug {
            signingConfig signingConfigs.debug
        }
        release {
            signingConfig signingConfigs.release
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        }
    }

    splits {
        abi {
            reset()
            enable enableSeparateBuildPerCPUArchitecture
            universalApk false
            include "arm64-v8a", "armeabi-v7a", "x86", "x86_64"
        }
    }
}
```

**android/gradle.properties**:

```properties
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=****
MYAPP_UPLOAD_KEY_PASSWORD=****

# Optimizaciones
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
org.gradle.parallel=true
org.gradle.configureondemand=true
org.gradle.daemon=true

android.useAndroidX=true
android.enableJetifier=true
```

#### Script de Build para Release

**scripts/build-android.sh**:

```bash
#!/bin/bash

echo "🚀 Building Android Release..."

# Limpiar build anterior
cd android
./gradlew clean

# Generar AAB (Android App Bundle)
./gradlew bundleRelease

# Generar APK
./gradlew assembleRelease

echo "✅ Android build completed!"
echo "📱 AAB location: android/app/build/outputs/bundle/release/app-release.aab"
echo "📱 APK location: android/app/build/outputs/apk/release/app-release.apk"

cd ..
```

### 3. Generación de Assets

#### Iconos de App

**scripts/generate-icons.js**:

```javascript
const sharp = require("sharp");
const fs = require("fs");

const generateIcons = async () => {
   const originalIcon = "assets/icon-original.png"; // 1024x1024 PNG

   // iOS Icons
   const iosIcons = [
      { size: 20, scale: 2, name: "Icon-20@2x.png" },
      { size: 20, scale: 3, name: "Icon-20@3x.png" },
      { size: 29, scale: 2, name: "Icon-29@2x.png" },
      { size: 29, scale: 3, name: "Icon-29@3x.png" },
      { size: 40, scale: 2, name: "Icon-40@2x.png" },
      { size: 40, scale: 3, name: "Icon-40@3x.png" },
      { size: 60, scale: 2, name: "Icon-60@2x.png" },
      { size: 60, scale: 3, name: "Icon-60@3x.png" },
      { size: 1024, scale: 1, name: "Icon-1024.png" },
   ];

   // Android Icons
   const androidIcons = [
      { density: "mdpi", size: 48 },
      { density: "hdpi", size: 72 },
      { density: "xhdpi", size: 96 },
      { density: "xxhdpi", size: 144 },
      { density: "xxxhdpi", size: 192 },
   ];

   // Generar iconos iOS
   for (const icon of iosIcons) {
      const size = icon.size * icon.scale;
      await sharp(originalIcon)
         .resize(size, size)
         .png()
         .toFile(`ios/MyApp/Images.xcassets/AppIcon.appiconset/${icon.name}`);
   }

   // Generar iconos Android
   for (const icon of androidIcons) {
      await sharp(originalIcon)
         .resize(icon.size, icon.size)
         .png()
         .toFile(
            `android/app/src/main/res/mipmap-${icon.density}/ic_launcher.png`,
         );
   }

   console.log("✅ Icons generated successfully!");
};

generateIcons().catch(console.error);
```

### 4. Configuración App Store Connect

#### App Information

```json
{
   "name": "MyApp",
   "subtitle": "Descripción corta de la app",
   "description": "Descripción detallada de la aplicación, sus funcionalidades principales y beneficios para el usuario. Debe ser clara, concisa y atractiva.",
   "keywords": "productivity, mobile, app, ios",
   "support_url": "https://myapp.com/support",
   "marketing_url": "https://myapp.com",
   "privacy_policy_url": "https://myapp.com/privacy",
   "category": "Productivity",
   "age_rating": "4+",
   "copyright": "© 2025 MyCompany"
}
```

#### Release Notes Template

```markdown
# Version 1.0.0

## ✨ Nuevas Funcionalidades

- Funcionalidad principal implementada
- Autenticación de usuarios
- Sincronización de datos offline

## 🐛 Correcciones de Errores

- Mejorado el rendimiento de la aplicación
- Corregidos problemas de conectividad

## 🔧 Mejoras

- Interfaz de usuario optimizada
- Mejor experiencia de navegación
- Optimización del uso de batería

---

¿Tienes sugerencias? Contáctanos en support@myapp.com
```

### 5. Configuración Google Play Console

#### Store Listing

```yaml
Title: "MyApp - Productivity Tool"
Short Description: "Herramienta de productividad para gestionar tareas y proyectos"
Full Description: |
   MyApp es la solución completa para gestionar tu productividad diaria.

   CARACTERÍSTICAS PRINCIPALES:
   ✅ Gestión de tareas intuitiva
   ✅ Sincronización en tiempo real
   ✅ Modo offline
   ✅ Interfaz moderna y fácil de usar

   BENEFICIOS:
   • Aumenta tu productividad
   • Organiza mejor tu tiempo
   • Accede a tus datos desde cualquier lugar

   Descarga MyApp ahora y transforma tu forma de trabajar.

Category: "Productivity"
Content Rating: "Everyone"
Contact Email: "support@myapp.com"
Privacy Policy: "https://myapp.com/privacy"
```

### 6. Automatización con Fastlane

**fastlane/Fastfile**:

```ruby
default_platform(:ios)

platform :ios do
  desc "Submit a new Beta Build to Apple TestFlight"
  lane :beta do
    # Increment build number
    increment_build_number(xcodeproj: "ios/MyApp.xcodeproj")
    
    # Build
    build_app(
      scheme: "MyApp",
      workspace: "ios/MyApp.xcworkspace",
      configuration: "Release",
      clean: true,
      export_method: "app-store"
    )
    
    # Upload to TestFlight
    upload_to_testflight(
      skip_waiting_for_build_processing: true
    )
  end

  desc "Deploy to App Store"
  lane :release do
    # Build
    build_app(
      scheme: "MyApp",
      workspace: "ios/MyApp.xcworkspace",
      configuration: "Release",
      clean: true,
      export_method: "app-store"
    )
    
    # Upload to App Store
    upload_to_app_store(
      force: true,
      reject_if_possible: true,
      skip_metadata: false,
      skip_screenshots: false,
      submit_for_review: true,
      automatic_release: false
    )
  end
end

platform :android do
  desc "Submit a new Beta Build to Google Play Internal Testing"
  lane :beta do
    # Build AAB
    gradle(
      task: "bundle",
      build_type: "Release",
      project_dir: "android/"
    )
    
    # Upload to Internal Testing
    upload_to_play_store(
      track: "internal",
      aab: "android/app/build/outputs/bundle/release/app-release.aab"
    )
  end

  desc "Deploy to Google Play Store"
  lane :release do
    # Build AAB
    gradle(
      task: "bundle",
      build_type: "Release",
      project_dir: "android/"
    )
    
    # Upload to Production
    upload_to_play_store(
      track: "production",
      aab: "android/app/build/outputs/bundle/release/app-release.aab",
      release_status: "draft"
    )
  end
end
```

### 7. CI/CD para Releases

**.github/workflows/release.yml**:

```yaml
name: Release Build

on:
   push:
      tags:
         - "v*"

jobs:
   ios-release:
      runs-on: macos-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"
              cache: "npm"

         - name: Install dependencies
           run: npm ci

         - name: Setup Ruby
           uses: ruby/setup-ruby@v1
           with:
              ruby-version: "3.0"
              bundler-cache: true

         - name: Install Fastlane
           run: gem install fastlane

         - name: Build and Upload to TestFlight
           env:
              FASTLANE_PASSWORD: ${{ secrets.FASTLANE_PASSWORD }}
              APP_STORE_CONNECT_API_KEY: ${{ secrets.APP_STORE_CONNECT_API_KEY }}
           run: fastlane ios beta

   android-release:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"
              cache: "npm"

         - name: Setup JDK
           uses: actions/setup-java@v3
           with:
              java-version: "11"
              distribution: "temurin"

         - name: Install dependencies
           run: npm ci

         - name: Setup Ruby
           uses: ruby/setup-ruby@v1
           with:
              ruby-version: "3.0"
              bundler-cache: true

         - name: Setup Android signing
           run: |
              echo "${{ secrets.ANDROID_KEYSTORE }}" | base64 -d > android/app/my-upload-key.keystore
              echo "MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore" >> android/gradle.properties
              echo "MYAPP_UPLOAD_KEY_ALIAS=${{ secrets.ANDROID_KEY_ALIAS }}" >> android/gradle.properties
              echo "MYAPP_UPLOAD_STORE_PASSWORD=${{ secrets.ANDROID_STORE_PASSWORD }}" >> android/gradle.properties
              echo "MYAPP_UPLOAD_KEY_PASSWORD=${{ secrets.ANDROID_KEY_PASSWORD }}" >> android/gradle.properties

         - name: Build and Upload to Play Store
           env:
              GOOGLE_PLAY_SERVICE_ACCOUNT_JSON: ${{ secrets.GOOGLE_PLAY_SERVICE_ACCOUNT_JSON }}
           run: fastlane android beta
```

## Tips

### Preparation

- **Test Thoroughly**: Realiza testing exhaustivo antes del release
- **Beta Testing**: Usa TestFlight e Internal Testing para validación
- **Performance**: Optimiza performance para dispositivos de gama baja
- **Compliance**: Revisa políticas de las tiendas regularmente

### Metadata Optimization

- **ASO Keywords**: Investiga keywords relevantes para tu app
- **Screenshots**: Crea screenshots atractivos y descriptivos
- **Description**: Escribe descripciones claras y persuasivas
- **Localization**: Considera localización para diferentes mercados

### Release Strategy

- **Phased Rollout**: Usa rollouts graduales para detectar problemas
- **A/B Testing**: Prueba diferentes elementos de la store listing
- **Analytics**: Monitorea métricas de descarga y retención
- **Updates**: Mantén un calendario regular de actualizaciones

### Quality Assurance

- **Code Signing**: Mantén seguros tus certificados de signing
- **Crash Reports**: Configura reportes de crashes (Crashlytics)
- **Performance Monitoring**: Usa herramientas de monitoreo de performance
- **User Feedback**: Responde a reviews y feedback de usuarios

## Ejemplos

### Checklist Pre-Release

```markdown
## ✅ Checklist Pre-Release

### Development

- [ ] Código revisado y aprobado
- [ ] Tests passing (unit, integration, E2E)
- [ ] Performance optimizada
- [ ] Memory leaks corregidos

### Assets

- [ ] Iconos generados para todas las resoluciones
- [ ] Screenshots actualizados
- [ ] Metadata traducida (si aplica)
- [ ] Privacy policy actualizada

### Builds

- [ ] Release build genera correctamente
- [ ] Code signing configurado
- [ ] ProGuard/R8 configurado (Android)
- [ ] Bundle size optimizado

### Store Setup

- [ ] App Store Connect configurado
- [ ] Google Play Console configurado
- [ ] Ratings y categorías correctas
- [ ] Precios configurados

### Legal

- [ ] Privacy policy actualizada
- [ ] Términos de uso actualizados
- [ ] Compliance con GDPR/CCPA
- [ ] Age rating apropiado
```

### Release Notes Template

```typescript
// utils/releaseNotes.ts
export const generateReleaseNotes = (
   version: string,
   features: string[],
   fixes: string[],
): string => {
   return `
# Versión ${version}

## ✨ Nuevas Funcionalidades
${features.map((feature) => `• ${feature}`).join("\n")}

## 🐛 Correcciones
${fixes.map((fix) => `• ${fix}`).join("\n")}

## 📱 Compatibilidad
Esta versión requiere iOS 12.0+ o Android 6.0+

¿Tienes sugerencias? Contáctanos en support@myapp.com
  `.trim();
};
```

## Navegación

[⬅️ Testing Dispositivos y Simuladores](./testing-dispositivos-simuladores.md) |
[🏠 README Principal](../../README.md) |
[Etapa 7: Testing y Quality Assurance ➡️](../step_07/testing-qa.md)
