# Manejo de Imágenes

## ¿Qué es?

El manejo de imágenes es un conjunto de estándares, convenciones y técnicas para organizar, optimizar y gestionar todos los recursos gráficos en un proyecto de software. Incluye la estructura de carpetas, formatos recomendados, convenciones de nomenclatura, estrategias de optimización y buenas prácticas para garantizar performance, accesibilidad y mantenibilidad.

## ¿Por qué es importante?

- **Performance web**: Las imágenes representan 60-70% del peso total de una página web
- **Experiencia del usuario**: Optimización reduce tiempos de carga y mejora la percepción
- **SEO**: Imágenes bien optimizadas mejoran el ranking en motores de búsqueda
- **Accesibilidad**: Estructura clara facilita navegación para usuarios con discapacidades
- **Mantenibilidad**: Organización estándar reduce tiempo de búsqueda y modificación
- **Escalabilidad**: Permite crecimiento ordenado del proyecto sin desorganización
- **Ancho de banda**: Optimización reduce costos de transferencia de datos
- **Dispositivos móviles**: Mejora experiencia en conexiones lentas y datos limitados

## ¿Qué debe incluir?

### Estructura de Carpetas Organizada

- Categorización por tipo de imagen y uso
- Separación entre assets de desarrollo y producción
- Organización por resoluciones y formatos
- Versionado para diferentes tamaños

### Formatos Optimizados

- Matriz de decisión para selección de formatos
- Formatos modernos (WebP, AVIF) con fallbacks
- Configuración automática de conversión
- Compresión optimizada por contexto

### Convenciones de Nomenclatura

- Patrones consistentes para nombres de archivos
- Prefijos y sufijos estándar
- Descriptores de tamaño y resolución
- Identificadores de estado y variantes

### Optimización Automática

- Pipeline de procesamiento en build time
- Lazy loading y progressive loading
- Responsive images con srcset
- CDN integration para delivery optimizado

## ¿Qué debo hacer?

### 1. Definir estructura de carpetas para imágenes

#### Estructura Recomendada

```
src/assets/images/
├── icons/                     # Iconografía del sistema
│   ├── ui/                   # Iconos de interfaz (16x16, 24x24, 32x32)
│   ├── social/               # Iconos de redes sociales
│   ├── actions/              # Iconos de acciones (edit, delete, save)
│   └── status/               # Iconos de estado (success, error, warning)
│
├── logos/                     # Logotipos y branding
│   ├── brand/                # Logo principal en diferentes formatos
│   ├── partners/             # Logos de socios y partners
│   └── clients/              # Logos de clientes
│
├── backgrounds/               # Imágenes de fondo
│   ├── hero/                 # Imágenes hero/banner principales
│   ├── patterns/             # Patrones y texturas
│   └── gradients/            # Gradientes exportados como imagen
│
├── content/                   # Imágenes de contenido
│   ├── products/             # Fotos de productos
│   ├── team/                 # Fotos del equipo
│   ├── gallery/              # Galería de imágenes
│   └── blog/                 # Imágenes para artículos
│
├── ui/                        # Elementos de interfaz
│   ├── avatars/              # Avatares por defecto
│   ├── placeholders/         # Imágenes placeholder
│   └── illustrations/        # Ilustraciones personalizadas
│
└── optimized/                 # Versiones optimizadas automáticas
    ├── webp/                 # Conversiones WebP
    ├── avif/                 # Conversiones AVIF
    └── responsive/           # Versiones responsive generadas
```

#### Estructura para Diferentes Tipos de Proyecto

**Frontend (React/Vue):**

```
public/images/               # Imágenes públicas accesibles
src/assets/images/          # Imágenes procesadas por bundler
src/components/images/      # Imágenes específicas de componentes
```

**Backend (Laravel):**

```
public/storage/images/      # Imágenes subidas por usuarios
resources/images/           # Imágenes del sistema
storage/app/public/images/  # Storage link para imágenes
```

**Móvil (React Native):**

```
src/assets/images/
├── @1x/                    # Densidad estándar (mdpi)
├── @2x/                    # Alta densidad (xhdpi)
├── @3x/                    # Muy alta densidad (xxhdpi)
└── vector/                 # SVGs y vectores
```

### 2. Implementar matriz de formatos recomendados

#### Matriz de Decisión de Formatos

| Tipo de Imagen      | Uso Recomendado      | Formato Principal | Formato Alternativo | Caso de Uso                           |
| ------------------- | -------------------- | ----------------- | ------------------- | ------------------------------------- |
| **Fotografías**     | Contenido, productos | **WebP**          | JPEG (fallback)     | Imágenes complejas con muchos colores |
| **Ilustraciones**   | Gráficos simples     | **SVG**           | PNG (fallback)      | Vectores escalables, iconos           |
| **Logotipos**       | Branding             | **SVG**           | PNG (fallback)      | Escalabilidad perfecta                |
| **Capturas**        | Documentación        | **PNG**           | WebP (optimizado)   | Texto claro, interfaces               |
| **Iconos pequeños** | UI (≤32px)           | **SVG**           | PNG @2x, @3x        | Interfaz de usuario                   |
| **Iconos grandes**  | UI (>32px)           | **WebP**          | PNG (fallback)      | Elementos decorativos                 |
| **Fondos**          | Decorativos          | **WebP**          | JPEG (fallback)     | Imágenes de fondo                     |
| **Animaciones**     | Interacciones        | **Lottie/JSON**   | GIF (fallback)      | Micro-animaciones                     |
| **Avatares**        | Usuarios             | **WebP**          | JPEG (fallback)     | Fotos de perfil                       |
| **Thumbnails**      | Previsualizaciones   | **WebP**          | JPEG (fallback)     | Miniaturas de contenido               |

#### Configuración de Calidad por Formato

```typescript
// src/config/imageFormats.ts
export const IMAGE_QUALITY_SETTINGS = {
  webp: {
    photo: 85, // Fotografías
    graphics: 90, // Gráficos e ilustraciones
    thumbnail: 75, // Miniaturas
  },
  avif: {
    photo: 70, // Más eficiente que WebP
    graphics: 80,
    thumbnail: 65,
  },
  jpeg: {
    photo: 85, // Fallback para fotografías
    thumbnail: 80, // Fallback para miniaturas
  },
  png: {
    graphics: 9, // Compresión PNG (0-9)
    logos: 9, // Máxima calidad para logos
  },
} as const;

export const IMAGE_SIZES = {
  thumbnail: { width: 150, height: 150 },
  small: { width: 300, height: 200 },
  medium: { width: 600, height: 400 },
  large: { width: 1200, height: 800 },
  hero: { width: 1920, height: 1080 },
} as const;
```

### 3. Establecer convenciones de nomenclatura

#### Patrón de Nomenclatura Estándar

```
[contexto]-[descripcion]-[variante]?-[tamaño]?.[formato]

Ejemplos:
- icon-search-filled-24.svg
- logo-brand-white-large.webp
- bg-hero-gradient-1920x1080.jpg
- avatar-placeholder-default.png
- btn-primary-hover-state.svg
- product-smartphone-thumbnail-150x150.webp
```

#### Convenciones Específicas

**Iconos:**

```
icon-[nombre]-[variante]?-[tamaño].[formato]

Ejemplos:
- icon-search-24.svg
- icon-user-filled-32.svg
- icon-arrow-left-white-16.svg
```

**Logos:**

```
logo-[entidad]-[variante]?-[tamaño]?.[formato]

Ejemplos:
- logo-brand-main.svg
- logo-brand-white-small.png
- logo-partner-company-horizontal.svg
```

**Fondos:**

```
bg-[contexto]-[descripcion]-[resolucion]?.[formato]

Ejemplos:
- bg-hero-gradient-1920x1080.webp
- bg-section-pattern-seamless.svg
- bg-modal-overlay-dark.png
```

**Contenido:**

```
[categoria]-[descripcion]-[variante]?-[tamaño]?.[formato]

Ejemplos:
- product-laptop-main-large.webp
- team-john-avatar-150x150.jpg
- blog-architecture-cover-hero.webp
```

#### Reglas de Nomenclatura

1. **Solo minúsculas y guiones**: `kebab-case` para todos los nombres
2. **Sin espacios ni caracteres especiales**: Solo `a-z`, `0-9`, `-`, `.`
3. **Descriptivo pero conciso**: Máximo 50 caracteres
4. **Consistente**: Mismo patrón para categorías similares
5. **Versionado**: Usar sufijos como `-v2`, `-2024` para versiones
6. **Estados**: Sufijos como `-hover`, `-active`, `-disabled`

### 4. Configurar optimización automática

#### Configuración Vite para Imágenes

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import { imageOptimize } from "vite-plugin-imagemin";

export default defineConfig({
  plugins: [
    // Optimización automática de imágenes
    imageOptimize({
      gifsicle: { optimizationLevel: 7 },
      mozjpeg: { quality: 85 },
      optipng: { optimizationLevel: 7 },
      pngquant: { quality: [0.65, 0.8] },
      svgo: {
        plugins: [
          { name: "removeViewBox", active: false },
          { name: "removeDimensions", active: true },
        ],
      },
      webp: { quality: 85 },
      avif: { quality: 70 },
    }),
  ],

  // Configuración de assets
  assetsInclude: ["**/*.webp", "**/*.avif"],

  build: {
    assetsInlineLimit: 4096, // 4KB - inline pequeños assets
    rollupOptions: {
      output: {
        assetFileNames: (assetInfo) => {
          const info = assetInfo.name.split(".");
          const ext = info[info.length - 1];

          if (/\.(png|jpe?g|webp|avif|svg|gif)$/i.test(assetInfo.name)) {
            return `assets/images/[name]-[hash].${ext}`;
          }

          return `assets/[name]-[hash].${ext}`;
        },
      },
    },
  },
});
```

#### Plugin Personalizado de Optimización

```typescript
// scripts/optimizeImages.ts
import sharp from "sharp";
import { glob } from "glob";
import path from "path";
import fs from "fs/promises";

interface OptimizationConfig {
  inputDir: string;
  outputDir: string;
  formats: ("webp" | "avif" | "jpeg" | "png")[];
  sizes: { width: number; height?: number; suffix: string }[];
  quality: Record<string, number>;
}

class ImageOptimizer {
  private config: OptimizationConfig;

  constructor(config: OptimizationConfig) {
    this.config = config;
  }

  async optimizeAll(): Promise<void> {
    console.log("🖼️  Starting image optimization...");

    const imageFiles = await glob("**/*.{jpg,jpeg,png}", {
      cwd: this.config.inputDir,
    });

    for (const file of imageFiles) {
      await this.optimizeImage(file);
    }

    console.log("✅ Image optimization completed!");
  }

  private async optimizeImage(filePath: string): Promise<void> {
    const inputPath = path.join(this.config.inputDir, filePath);
    const fileName = path.parse(filePath).name;
    const dirName = path.dirname(filePath);

    const outputDir = path.join(this.config.outputDir, dirName);
    await fs.mkdir(outputDir, { recursive: true });

    const image = sharp(inputPath);
    const metadata = await image.metadata();

    // Generate different sizes
    for (const size of this.config.sizes) {
      const resized = image.clone().resize(size.width, size.height, {
        fit: "cover",
        withoutEnlargement: true,
      });

      // Generate different formats
      for (const format of this.config.formats) {
        const quality = this.config.quality[format] || 85;
        const outputName = `${fileName}${size.suffix}.${format}`;
        const outputPath = path.join(outputDir, outputName);

        await resized.clone()[format]({ quality }).toFile(outputPath);

        console.log(`   ✓ Generated: ${outputName}`);
      }
    }
  }
}

// Configuración por defecto
const config: OptimizationConfig = {
  inputDir: "src/assets/images",
  outputDir: "src/assets/images/optimized",
  formats: ["webp", "avif", "jpeg"],
  sizes: [
    { width: 150, suffix: "-thumbnail" },
    { width: 300, suffix: "-small" },
    { width: 600, suffix: "-medium" },
    { width: 1200, suffix: "-large" },
  ],
  quality: {
    webp: 85,
    avif: 70,
    jpeg: 85,
    png: 90,
  },
};

// Ejecutar optimización
const optimizer = new ImageOptimizer(config);
optimizer.optimizeAll().catch(console.error);
```

#### Package.json Scripts

```json
{
  "scripts": {
    "images:optimize": "tsx scripts/optimizeImages.ts",
    "images:convert": "tsx scripts/convertToWebP.ts",
    "images:compress": "imagemin 'src/assets/images/**/*.{jpg,png}' --out-dir=src/assets/images/compressed",
    "images:analyze": "tsx scripts/analyzeImageSizes.ts"
  }
}
```

### 5. Implementar componente de imagen optimizada

#### Componente React Optimizado

```tsx
// src/components/OptimizedImage.tsx
import React, { useState, useRef, useEffect } from "react";

interface OptimizedImageProps {
  src: string;
  alt: string;
  width?: number;
  height?: number;
  className?: string;
  sizes?: string;
  priority?: boolean;
  placeholder?: "blur" | "empty";
  blurDataURL?: string;
  onLoad?: () => void;
  onError?: () => void;
}

export const OptimizedImage: React.FC<OptimizedImageProps> = ({
  src,
  alt,
  width,
  height,
  className = "",
  sizes = "100vw",
  priority = false,
  placeholder = "blur",
  blurDataURL,
  onLoad,
  onError,
}) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [hasError, setHasError] = useState(false);
  const [isInView, setIsInView] = useState(priority);
  const imgRef = useRef<HTMLImageElement>(null);

  // Lazy loading con Intersection Observer
  useEffect(() => {
    if (priority) return;

    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { rootMargin: "50px" }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, [priority]);

  // Generar srcset para diferentes formatos
  const generateSrcSet = (baseSrc: string) => {
    const baseName = baseSrc.replace(/\.[^/.]+$/, "");
    const extension = baseSrc.split(".").pop();

    return {
      webp: `${baseName}-thumbnail.webp 150w, ${baseName}-small.webp 300w, ${baseName}-medium.webp 600w, ${baseName}-large.webp 1200w`,
      avif: `${baseName}-thumbnail.avif 150w, ${baseName}-small.avif 300w, ${baseName}-medium.avif 600w, ${baseName}-large.avif 1200w`,
      fallback: `${baseName}-thumbnail.${extension} 150w, ${baseName}-small.${extension} 300w, ${baseName}-medium.${extension} 600w, ${baseName}-large.${extension} 1200w`,
    };
  };

  const handleLoad = () => {
    setIsLoaded(true);
    onLoad?.();
  };

  const handleError = () => {
    setHasError(true);
    onError?.();
  };

  const srcSets = generateSrcSet(src);

  // Fallback para errores
  if (hasError) {
    return (
      <div
        className={`bg-gray-200 flex items-center justify-center ${className}`}
        style={{ width, height }}
      >
        <span className="text-gray-500 text-sm">Image not available</span>
      </div>
    );
  }

  // Placeholder mientras carga
  if (!isInView) {
    return (
      <div
        ref={imgRef}
        className={`bg-gray-100 ${className}`}
        style={{ width, height }}
      >
        {placeholder === "blur" && blurDataURL && (
          <img
            src={blurDataURL}
            alt=""
            className="w-full h-full object-cover filter blur-sm"
          />
        )}
      </div>
    );
  }

  return (
    <picture className={className}>
      {/* AVIF para navegadores compatibles */}
      <source srcSet={srcSets.avif} sizes={sizes} type="image/avif" />

      {/* WebP para navegadores compatibles */}
      <source srcSet={srcSets.webp} sizes={sizes} type="image/webp" />

      {/* Fallback original */}
      <img
        ref={imgRef}
        src={src}
        srcSet={srcSets.fallback}
        sizes={sizes}
        alt={alt}
        width={width}
        height={height}
        loading={priority ? "eager" : "lazy"}
        className={`transition-opacity duration-300 ${
          isLoaded ? "opacity-100" : "opacity-0"
        }`}
        onLoad={handleLoad}
        onError={handleError}
      />
    </picture>
  );
};

// Hook para usar imágenes optimizadas
export const useOptimizedImage = (
  src: string,
  size: "thumbnail" | "small" | "medium" | "large" = "medium"
) => {
  const baseName = src.replace(/\.[^/.]+$/, "");
  const extension = src.split(".").pop();

  return {
    webp: `${baseName}-${size}.webp`,
    avif: `${baseName}-${size}.avif`,
    fallback: `${baseName}-${size}.${extension}`,
    original: src,
  };
};
```

#### Componente para React Native

```tsx
// src/components/OptimizedImageRN.tsx
import React, { useState } from "react";
import { View, StyleSheet, ActivityIndicator, Platform } from "react-native";
import FastImage, { FastImageProps } from "react-native-fast-image";

interface OptimizedImageRNProps extends Omit<FastImageProps, "source"> {
  uri: string;
  placeholder?: string;
  size?: number;
  borderRadius?: number;
  showLoader?: boolean;
}

export const OptimizedImageRN: React.FC<OptimizedImageRNProps> = ({
  uri,
  placeholder,
  size = 100,
  borderRadius = 0,
  showLoader = true,
  style,
  ...props
}) => {
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(false);

  // Seleccionar resolución basada en densidad de pantalla
  const getOptimizedUri = (originalUri: string): string => {
    const pixelRatio = Platform.select({
      ios: "@2x",
      android: "@2x",
      default: "@1x",
    });

    // Si es una URI externa, usar parámetros de query
    if (originalUri.startsWith("http")) {
      const url = new URL(originalUri);
      url.searchParams.set("w", (size * 2).toString());
      url.searchParams.set("h", (size * 2).toString());
      url.searchParams.set("fit", "crop");
      url.searchParams.set("auto", "format");
      return url.toString();
    }

    // Para assets locales, usar convencion de naming
    const baseName = originalUri.replace(/\.[^/.]+$/, "");
    const extension = originalUri.split(".").pop();

    return `${baseName}${pixelRatio}.${extension}`;
  };

  const optimizedUri = getOptimizedUri(uri);

  if (error) {
    return (
      <View
        style={[
          styles.container,
          { width: size, height: size, borderRadius },
          style,
        ]}
      >
        <View style={styles.errorPlaceholder}>
          {/* Icono de error o imagen placeholder */}
        </View>
      </View>
    );
  }

  return (
    <View
      style={[
        styles.container,
        { width: size, height: size, borderRadius },
        style,
      ]}
    >
      <FastImage
        source={{
          uri: optimizedUri,
          priority: FastImage.priority.normal,
          cache: FastImage.cacheControl.immutable,
        }}
        style={[styles.image, { borderRadius }]}
        onLoadStart={() => setLoading(true)}
        onLoad={() => setLoading(false)}
        onError={() => {
          setLoading(false);
          setError(true);
        }}
        {...props}
      />

      {loading && showLoader && (
        <View style={styles.loader}>
          <ActivityIndicator size="small" color="#666" />
        </View>
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    position: "relative",
    backgroundColor: "#f0f0f0",
  },
  image: {
    width: "100%",
    height: "100%",
  },
  loader: {
    position: "absolute",
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "rgba(255, 255, 255, 0.8)",
  },
  errorPlaceholder: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "#e0e0e0",
  },
});
```

### 6. Implementar lazy loading avanzado

#### Hook de Lazy Loading

```typescript
// src/hooks/useLazyImages.ts
import { useEffect, useRef, useState } from "react";

interface UseLazyImagesOptions {
  rootMargin?: string;
  threshold?: number;
  fallbackDelay?: number;
}

export const useLazyImages = (options: UseLazyImagesOptions = {}) => {
  const {
    rootMargin = "50px",
    threshold = 0.1,
    fallbackDelay = 3000,
  } = options;

  useEffect(() => {
    // Verificar soporte para Intersection Observer
    if (!("IntersectionObserver" in window)) {
      // Fallback: cargar todas las imágenes después de un delay
      setTimeout(() => {
        const lazyImages = document.querySelectorAll("[data-lazy]");
        lazyImages.forEach(loadImage);
      }, fallbackDelay);
      return;
    }

    const imageObserver = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            loadImage(entry.target);
            imageObserver.unobserve(entry.target);
          }
        });
      },
      { rootMargin, threshold }
    );

    // Observar todas las imágenes lazy
    const lazyImages = document.querySelectorAll("[data-lazy]");
    lazyImages.forEach((img) => imageObserver.observe(img));

    return () => imageObserver.disconnect();
  }, [rootMargin, threshold, fallbackDelay]);
};

const loadImage = (img: Element) => {
  const imageElement = img as HTMLImageElement;
  const src = imageElement.dataset.lazy;

  if (src) {
    imageElement.src = src;
    imageElement.classList.add("loaded");
    imageElement.removeAttribute("data-lazy");
  }
};

// Progressive loading hook
export const useProgressiveImage = (src: string, placeholder?: string) => {
  const [imageSrc, setImageSrc] = useState(placeholder);
  const [isLoaded, setIsLoaded] = useState(false);

  useEffect(() => {
    const img = new Image();
    img.onload = () => {
      setImageSrc(src);
      setIsLoaded(true);
    };
    img.src = src;
  }, [src]);

  return { imageSrc, isLoaded };
};
```

#### CSS para Lazy Loading

```css
/* src/styles/lazy-images.css */

/* Estado inicial de imágenes lazy */
[data-lazy] {
  opacity: 0;
  transition: opacity 0.3s ease-in-out;
  background-color: #f0f0f0;
  background-image: linear-gradient(
      45deg,
      transparent 25%,
      rgba(255, 255, 255, 0.5) 25%
    ), linear-gradient(-45deg, transparent 25%, rgba(255, 255, 255, 0.5) 25%),
    linear-gradient(45deg, rgba(255, 255, 255, 0.5) 75%, transparent 75%),
    linear-gradient(-45deg, rgba(255, 255, 255, 0.5) 75%, transparent 75%);
  background-size: 20px 20px;
  background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
  animation: loading 1.5s infinite linear;
}

/* Estado cargado */
[data-lazy].loaded {
  opacity: 1;
  background: none;
  animation: none;
}

/* Animación de loading */
@keyframes loading {
  0% {
    background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
  }
  100% {
    background-position: 20px 20px, 20px 30px, 30px 10px, 10px 20px;
  }
}

/* Blur placeholder para progressive loading */
.image-placeholder {
  filter: blur(5px);
  transform: scale(1.05);
  transition: filter 0.3s ease, transform 0.3s ease;
}

.image-placeholder.loaded {
  filter: blur(0);
  transform: scale(1);
}

/* Responsive image containers */
.image-container {
  position: relative;
  overflow: hidden;
}

.image-container::before {
  content: "";
  display: block;
  padding-top: 56.25%; /* 16:9 aspect ratio */
}

.image-container img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* Aspect ratio utilities */
.aspect-square::before {
  padding-top: 100%;
}
.aspect-video::before {
  padding-top: 56.25%;
}
.aspect-photo::before {
  padding-top: 66.67%;
}
.aspect-wide::before {
  padding-top: 41.67%;
}
```

### 7. Configurar herramientas de monitoreo

#### Script de Análisis de Imágenes

```typescript
// scripts/analyzeImages.ts
import { glob } from "glob";
import { promises as fs } from "fs";
import path from "path";
import sharp from "sharp";

interface ImageAnalysis {
  path: string;
  size: number;
  dimensions: { width: number; height: number };
  format: string;
  hasOptimized: boolean;
  recommendations: string[];
}

class ImageAnalyzer {
  private thresholds = {
    maxSize: 500 * 1024, // 500KB
    maxWidth: 2000,
    maxHeight: 2000,
    minQuality: 70,
  };

  async analyzeProject(directory: string): Promise<void> {
    console.log("🔍 Analyzing images in project...\n");

    const imageFiles = await glob("**/*.{jpg,jpeg,png,webp,avif,gif,svg}", {
      cwd: directory,
      ignore: ["node_modules/**", "dist/**", "build/**"],
    });

    const analysis: ImageAnalysis[] = [];

    for (const file of imageFiles) {
      try {
        const analysis_result = await this.analyzeImage(
          path.join(directory, file)
        );
        analysis.push(analysis_result);
      } catch (error) {
        console.error(`Error analyzing ${file}:`, error);
      }
    }

    this.generateReport(analysis);
  }

  private async analyzeImage(filePath: string): Promise<ImageAnalysis> {
    const stats = await fs.stat(filePath);
    const metadata = await sharp(filePath).metadata();

    const analysis: ImageAnalysis = {
      path: filePath,
      size: stats.size,
      dimensions: {
        width: metadata.width || 0,
        height: metadata.height || 0,
      },
      format: metadata.format || "unknown",
      hasOptimized: this.checkOptimizedVersions(filePath),
      recommendations: [],
    };

    // Generar recomendaciones
    this.generateRecommendations(analysis);

    return analysis;
  }

  private checkOptimizedVersions(filePath: string): boolean {
    const baseName = filePath.replace(/\.[^/.]+$/, "");
    const optimizedDir = path.join(path.dirname(filePath), "optimized");

    try {
      const webpExists = require("fs").existsSync(
        `${optimizedDir}/${path.basename(baseName)}.webp`
      );
      const avifExists = require("fs").existsSync(
        `${optimizedDir}/${path.basename(baseName)}.avif`
      );
      return webpExists || avifExists;
    } catch {
      return false;
    }
  }

  private generateRecommendations(analysis: ImageAnalysis): void {
    const { size, dimensions, format, hasOptimized } = analysis;

    // Tamaño excesivo
    if (size > this.thresholds.maxSize) {
      analysis.recommendations.push(
        `📦 Size too large (${(size / 1024).toFixed(
          1
        )}KB). Consider compression or resizing.`
      );
    }

    // Dimensiones excesivas
    if (
      dimensions.width > this.thresholds.maxWidth ||
      dimensions.height > this.thresholds.maxHeight
    ) {
      analysis.recommendations.push(
        `📏 Dimensions too large (${dimensions.width}x${dimensions.height}). Consider resizing.`
      );
    }

    // Formato no optimizado
    if (format === "png" && size > 100 * 1024) {
      analysis.recommendations.push(
        "🎨 Large PNG detected. Consider converting to WebP or JPEG if photo."
      );
    }

    if (format === "jpeg" && !hasOptimized) {
      analysis.recommendations.push(
        "⚡ No optimized versions found. Generate WebP/AVIF variants."
      );
    }

    // Falta de versiones optimizadas
    if (!hasOptimized && !["svg", "gif"].includes(format)) {
      analysis.recommendations.push(
        "🚀 Missing optimized formats. Generate WebP and AVIF versions."
      );
    }
  }

  private generateReport(analysis: ImageAnalysis[]): void {
    const totalSize = analysis.reduce((sum, img) => sum + img.size, 0);
    const largeImages = analysis.filter(
      (img) => img.size > this.thresholds.maxSize
    );
    const unoptimized = analysis.filter((img) => !img.hasOptimized);

    console.log("📊 IMAGE ANALYSIS REPORT");
    console.log("========================\n");

    console.log(`📁 Total images: ${analysis.length}`);
    console.log(`💾 Total size: ${(totalSize / 1024 / 1024).toFixed(2)}MB`);
    console.log(`⚠️  Large images (>500KB): ${largeImages.length}`);
    console.log(`🚀 Unoptimized images: ${unoptimized.length}\n`);

    // Top problematic images
    const problematic = analysis
      .filter((img) => img.recommendations.length > 0)
      .sort((a, b) => b.size - a.size)
      .slice(0, 10);

    if (problematic.length > 0) {
      console.log("🔴 TOP ISSUES TO FIX:\n");

      problematic.forEach((img, index) => {
        console.log(`${index + 1}. ${path.basename(img.path)}`);
        console.log(
          `   Size: ${(img.size / 1024).toFixed(1)}KB | Dimensions: ${
            img.dimensions.width
          }x${img.dimensions.height}`
        );
        img.recommendations.forEach((rec) => console.log(`   ${rec}`));
        console.log("");
      });
    }

    // Format distribution
    const formatStats = analysis.reduce((stats, img) => {
      stats[img.format] = (stats[img.format] || 0) + 1;
      return stats;
    }, {} as Record<string, number>);

    console.log("📈 FORMAT DISTRIBUTION:\n");
    Object.entries(formatStats)
      .sort(([, a], [, b]) => b - a)
      .forEach(([format, count]) => {
        console.log(`   ${format.toUpperCase()}: ${count} files`);
      });

    console.log("\n✅ Analysis complete!");
  }
}

// Ejecutar análisis
const analyzer = new ImageAnalyzer();
analyzer.analyzeProject("src/assets/images").catch(console.error);
```

## Tips

### 🎯 **Mejores Prácticas de Organización**

1. **Estructura Escalable**

   - Agrupa por función antes que por tipo técnico
   - Mantén máximo 10-15 archivos por carpeta
   - Usa subcarpetas para categorías específicas

2. **Nomenclatura Consistente**

   - Establece convenciones desde el inicio del proyecto
   - Documenta patrones de naming en el README
   - Usa herramientas de linting para validar nombres

3. **Versionado de Assets**
   - Usa hash o timestamps en nombres para cache busting
   - Mantén versiones anteriores hasta confirmar deploy
   - Documenta cambios significativos en assets principales

### ⚡ **Optimización de Performance**

1. **Formatos Modernos**

   - Prioriza WebP sobre JPEG/PNG (30-50% menor tamaño)
   - Usa AVIF cuando sea posible (20-30% menor que WebP)
   - Mantén fallbacks para compatibilidad

2. **Lazy Loading Estratégico**

   - Aplica solo a imágenes below-the-fold
   - Usa intersection observer con margin para preload
   - Implementa placeholders para mejor UX

3. **Responsive Images**
   - Genera múltiples tamaños automáticamente
   - Usa srcset y sizes apropiadamente
   - Considera device pixel ratio para displays de alta densidad

### 🔧 **Herramientas y Automatización**

1. **Build Pipeline**

   - Integra optimización en el proceso de build
   - Automatiza conversión de formatos
   - Genera reportes de tamaño en CI/CD

2. **Monitoreo Continuo**

   - Establece límites de tamaño por tipo de imagen
   - Alerta en PR si excede thresholds
   - Analiza impact en performance metrics

3. **CDN Integration**
   - Configura transformación automática en CDN
   - Usa parámetros de query para redimensionado
   - Implementa cache headers apropiados

### 🎨 **Accesibilidad y UX**

1. **Alt Text Descriptivo**

   - Describe contenido, no apariencia
   - Omite "imagen de" o "foto de"
   - Usa alt="" para imágenes decorativas

2. **Estados de Carga**

   - Implementa skeleton screens
   - Muestra progressive loading
   - Maneja estados de error graciosamente

3. **Dark Mode Support**
   - Prepara variantes para modo oscuro
   - Usa CSS custom properties para adaptación
   - Considera inversión automática para iconos

## Ejemplos

### Estructura Completa de Proyecto

```
src/assets/images/
├── icons/
│   ├── ui/
│   │   ├── icon-search-24.svg
│   │   ├── icon-user-filled-32.svg
│   │   └── icon-settings-outline-24.svg
│   ├── social/
│   │   ├── icon-facebook-brand.svg
│   │   ├── icon-twitter-brand.svg
│   │   └── icon-linkedin-brand.svg
│   └── status/
│       ├── icon-success-circle-16.svg
│       ├── icon-error-triangle-16.svg
│       └── icon-warning-diamond-16.svg
│
├── logos/
│   ├── brand/
│   │   ├── logo-main.svg
│   │   ├── logo-main-white.svg
│   │   └── logo-icon-only.svg
│   └── partners/
│       ├── logo-partner-acme.svg
│       └── logo-partner-techcorp.png
│
├── backgrounds/
│   ├── hero/
│   │   ├── bg-hero-gradient-1920x1080.webp
│   │   ├── bg-hero-gradient-1920x1080.avif
│   │   └── bg-hero-gradient-1920x1080.jpg
│   └── patterns/
│       ├── bg-dots-pattern.svg
│       └── bg-grid-subtle.svg
│
├── content/
│   ├── products/
│   │   ├── product-laptop-main-large.webp
│   │   ├── product-laptop-main-medium.webp
│   │   ├── product-laptop-main-small.webp
│   │   └── product-laptop-main-thumbnail.webp
│   └── team/
│       ├── team-john-avatar-150x150.webp
│       ├── team-sarah-avatar-150x150.webp
│       └── team-mike-avatar-150x150.webp
│
└── optimized/
    ├── webp/
    ├── avif/
    └── responsive/
```

### Configuración GitHub Actions para Optimización

```yaml
# .github/workflows/optimize-images.yml
name: Optimize Images

on:
  pull_request:
    paths:
      - "src/assets/images/**"
  push:
    branches: [main]
    paths:
      - "src/assets/images/**"

jobs:
  optimize-images:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"
          cache: "npm"

      - name: Install dependencies
        run: |
          npm ci
          npm install -g @squoosh/cli sharp-cli

      - name: Analyze current images
        run: |
          npm run images:analyze > image-analysis.txt
          cat image-analysis.txt

      - name: Optimize new/changed images
        run: |
          # Find changed images
          git diff --name-only HEAD~1 HEAD | grep -E '\.(jpg|jpeg|png)$' > changed-images.txt || true

          if [ -s changed-images.txt ]; then
            echo "Optimizing changed images..."
            while read image; do
              if [ -f "$image" ]; then
                echo "Processing: $image"
                # Generate WebP
                squoosh-cli --webp '{"quality":85}' "$image"
                # Generate AVIF
                squoosh-cli --avif '{"quality":70}' "$image"
              fi
            done < changed-images.txt
          else
            echo "No image changes detected"
          fi

      - name: Generate size report
        run: |
          echo "## 📊 Image Optimization Report" > size-report.md
          echo "" >> size-report.md

          # Before/after comparison
          ORIGINAL_SIZE=$(find src/assets/images -name "*.jpg" -o -name "*.png" | xargs ls -la | awk '{sum += $5} END {print sum}')
          OPTIMIZED_SIZE=$(find src/assets/images -name "*.webp" -o -name "*.avif" | xargs ls -la | awk '{sum += $5} END {print sum}')

          echo "**Original images:** $(($ORIGINAL_SIZE / 1024))KB" >> size-report.md
          echo "**Optimized images:** $(($OPTIMIZED_SIZE / 1024))KB" >> size-report.md

          if [ $OPTIMIZED_SIZE -lt $ORIGINAL_SIZE ]; then
            SAVINGS=$((($ORIGINAL_SIZE - $OPTIMIZED_SIZE) * 100 / $ORIGINAL_SIZE))
            echo "**Savings:** ${SAVINGS}%" >> size-report.md
          fi

      - name: Comment PR with results
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');

            if (fs.existsSync('size-report.md')) {
              const report = fs.readFileSync('size-report.md', 'utf8');
              
              github.rest.issues.createComment({
                issue_number: context.issue.number,
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: report
              });
            }

      - name: Commit optimized images
        if: github.event_name == 'push'
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"

          git add src/assets/images/

          if ! git diff --staged --quiet; then
            git commit -m "🖼️ Auto-optimize images [skip ci]"
            git push
          fi
```

### Configuración Webpack para Proyectos Legacy

```javascript
// webpack.config.js
const path = require("path");

module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif|svg)$/i,
        type: "asset",
        parser: {
          dataUrlCondition: {
            maxSize: 4 * 1024, // 4KB
          },
        },
        generator: {
          filename: "assets/images/[name]-[hash][ext]",
        },
        use: [
          {
            loader: "image-webpack-loader",
            options: {
              mozjpeg: {
                progressive: true,
                quality: 85,
              },
              optipng: {
                enabled: false,
              },
              pngquant: {
                quality: [0.65, 0.9],
                speed: 4,
              },
              gifsicle: {
                interlaced: false,
              },
              webp: {
                quality: 85,
              },
            },
          },
        ],
      },
    ],
  },

  plugins: [
    // Generate different sizes
    new (require("responsive-loader/sharp"))({
      adapter: require("responsive-loader/sharp"),
      sizes: [150, 300, 600, 1200],
      placeholder: true,
      placeholderSize: 40,
    }),
  ],
};
```

## Navegación

[⬅️ Estructura de Carpetas](./estructura-carpetas.md) |
[🏠 README Principal](../../README.md) |
[Modelado de datos ➡️](./modelado-datos.md)
