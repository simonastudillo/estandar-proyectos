# Optimización de Recursos (React + TypeScript + Vite)

> **IMPORTANTE**: Esta guía está específicamente diseñada para proyectos que utilizan **Vite como bundler estándar**. Todas las configuraciones y herramientas están alineadas con el ecosistema Vite/Rollup, sin incluir Webpack, Next.js u otros bundlers.

## ¿Qué es?

La optimización de recursos es el proceso sistemático de mejorar la eficiencia
en la carga, transferencia y uso de assets web (imágenes, CSS, JavaScript,
fuentes, etc.) para reducir los tiempos de carga, minimizar el ancho de banda
utilizado y mejorar la experiencia del usuario. Incluye técnicas de compresión,
caching, CDN, lazy loading y optimización de formatos, todo integrado con las
capacidades nativas de Vite para el desarrollo con React y TypeScript.

## ¿Por qué es importante?

- **Performance web**: Reduce significativamente los tiempos de carga de páginas
- **Experiencia del usuario**: Mejora la percepción de velocidad y responsividad
- **SEO**: Los motores de búsqueda favorecen sitios con mejor performance
- **Ancho de banda**: Reduce costos de transferencia de datos
- **Dispositivos móviles**: Optimiza la experiencia en conexiones lentas
- **Conversión**: Mejor performance se traduce en mayor tasa de conversión
- **Sostenibilidad**: Reduce el consumo energético y la huella de carbono

## ¿Qué debe incluir?

### Optimización de Imágenes

#### Formatos Modernos

- WebP y AVIF para navegadores compatibles
- Fallbacks para navegadores antiguos
- Compresión lossy y lossless según el contexto
- Responsive images con srcset

#### Lazy Loading

- Intersection Observer API
- Placeholder images o skeleton screens
- Progressive image loading
- Lazy loading para videos y iframes

### Optimización de CSS

#### Minificación y Compresión

- Eliminación de whitespace y comentarios
- CSS crítico inline
- Preload para CSS no crítico
- CSS modules y code splitting

#### Purging CSS Unused

- Eliminación de CSS no utilizado
- Tree shaking para frameworks CSS
- Critical path CSS extraction
- PostCSS optimizations

### Optimización de JavaScript

#### Bundle Optimization

- Code splitting por rutas y componentes
- Tree shaking para eliminar código muerto
- Dynamic imports para lazy loading
- Rollup plugin visualizer para análisis de bundles

#### Minificación y Compresión

- Terser integrado en Vite para minificación
- Gzip y Brotli compression automáticos
- ESM optimization para mejor tree shaking
- Runtime optimizations con SWC (opcional)

### CDN y Caching

#### Content Delivery Network

- Distribución geográfica de assets
- Edge caching strategies
- Image optimization en CDN
- API caching en edge locations

#### Browser Caching

- Cache headers optimization
- Service Workers para offline caching
- Application shell caching
- Resource versioning strategies

## ¿Qué debo hacer?

### 1. Configurar optimización de imágenes automática

#### Vite Image Optimization Setup

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
   plugins: [
      react(),
   ],
   
   // Configuración de assets
   assetsInclude: ["**/*.webp", "**/*.avif"],
   
   build: {
      rollupOptions: {
         output: {
            assetFileNames: (assetInfo) => {
               if (assetInfo.name?.match(/\.(png|jpg|jpeg|webp|avif|svg)$/)) {
                  return "images/[name]-[hash][extname]";
               }
               return "assets/[name]-[hash][extname]";
            },
         },
      },
   },
   
   // Configuración de servidor de desarrollo
   server: {
      fs: {
         allow: [".."],
      },
   },
});
```

#### Plugin para Optimización de Imágenes

```bash
# Instalar plugin de optimización de imágenes para Vite
npm install --save-dev vite-plugin-imagemin
npm install --save-dev imagemin-webp imagemin-avif
```

```typescript
// vite.config.ts - con optimización de imágenes
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { ViteImageOptimize } from "vite-plugin-imagemin";

export default defineConfig({
   plugins: [
      react(),
      ViteImageOptimize({
         gifsicle: { optimizationLevel: 7 },
         mozjpeg: { quality: 80 },
         optipng: { optimizationLevel: 7 },
         pngquant: { quality: [0.65, 0.8] },
         svgo: {
            plugins: [
               { name: "removeViewBox" },
               { name: "removeEmptyAttrs", active: false },
            ],
         },
         webp: { quality: 80 },
         avif: { quality: 80 },
      }),
   ],
});
```

#### Custom Image Loader

```typescript
// src/utils/imageLoader.ts
interface ImageLoaderProps {
   src: string;
   width: number;
   quality?: number;
}

export default function customImageLoader(
   { src, width, quality }: ImageLoaderProps,
): string {
   const params = new URLSearchParams();

   params.set("w", width.toString());
   params.set("q", (quality || 75).toString());
   params.set("auto", "format");
   params.set("fit", "crop");

   // Use CDN transformation service
   if (src.startsWith("/")) {
      return `https://cdn.example.com${src}?${params.toString()}`;
   }

   return `${src}?${params.toString()}`;
}
```

#### Responsive Image Component

```tsx
// src/components/OptimizedImage.tsx
import Image from "next/image";
import { useState } from "react";

interface OptimizedImageProps {
   src: string;
   alt: string;
   width?: number;
   height?: number;
   priority?: boolean;
   className?: string;
   placeholder?: "blur" | "empty";
   blurDataURL?: string;
}

export const OptimizedImage: React.FC<OptimizedImageProps> = ({
   src,
   alt,
   width,
   height,
   priority = false,
   className,
   placeholder = "blur",
   blurDataURL,
}) => {
   const [isLoading, setIsLoading] = useState(true);
   const [error, setError] = useState(false);

   // Generate blur placeholder if not provided
   const generateBlurDataURL = (w: number, h: number) => {
      if (blurDataURL) return blurDataURL;

      return `data:image/svg+xml;base64,${
         Buffer.from(
            `<svg width="${w}" height="${h}" xmlns="http://www.w3.org/2000/svg">
        <rect width="100%" height="100%" fill="#f0f0f0"/>
        <circle cx="50%" cy="50%" r="10" fill="#e0e0e0"/>
      </svg>`,
         ).toString("base64")
      }`;
   };

   if (error) {
      return (
         <div
            className={`bg-gray-200 flex items-center justify-center ${className}`}
            style={{ width, height }}
         >
            <span className="text-gray-500 text-sm">Failed to load image</span>
         </div>
      );
   }

   return (
      <div className={`relative overflow-hidden ${className}`}>
         <Image
            src={src}
            alt={alt}
            width={width}
            height={height}
            priority={priority}
            placeholder={placeholder}
            blurDataURL={generateBlurDataURL(width || 400, height || 300)}
            className={`transition-opacity duration-300 ${
               isLoading ? "opacity-0" : "opacity-100"
            }`}
            onLoadingComplete={() => setIsLoading(false)}
            onError={() => setError(true)}
            sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
         />
         {isLoading && (
            <div className="absolute inset-0 bg-gray-200 animate-pulse" />
         )}
      </div>
   );
};
```

### 2. Optimización de CSS automática

#### PostCSS Configuration

```javascript
// postcss.config.js
module.exports = {
   plugins: {
      "tailwindcss": {},
      "autoprefixer": {},
      "cssnano": {
         preset: ["default", {
            discardComments: { removeAll: true },
            normalizeWhitespace: true,
            mergeLonghand: true,
            mergeRules: true,
            minifyFontValues: true,
            minifyParams: true,
            minifySelectors: true,
         }],
      },
      "@fullhuman/postcss-purgecss": {
         content: [
            "./src/**/*.{js,jsx,ts,tsx}",
            "./pages/**/*.{js,jsx,ts,tsx}",
            "./components/**/*.{js,jsx,ts,tsx}",
         ],
         defaultExtractor: (content) => content.match(/[\w-/:]+(?<!:)/g) || [],
         safelist: {
            standard: ["html", "body"],
            deep: [/^hljs-/, /^katex/],
            greedy: [/^tooltip-/],
         },
      },
   },
};
```

#### Critical CSS Extraction

```typescript
// scripts/extract-critical-css.ts
import { PurgeCSS } from "purgecss";
import * as fs from "fs";
import * as path from "path";

class CriticalCSSExtractor {
   private async extractCriticalCSS(
      htmlContent: string,
      cssFiles: string[],
   ): Promise<string> {
      const purgeCSSResult = await new PurgeCSS().purge({
         content: [{
            raw: htmlContent,
            extension: "html",
         }],
         css: cssFiles,
         extractors: [{
            extractor: (content: string) => {
               return content.match(/[\w-/:]+(?<!:)/g) || [];
            },
            extensions: ["html"],
         }],
      });

      return purgeCSSResult.map((result) => result.css).join("\n");
   }

   public async generateCriticalCSS(): Promise<void> {
      console.log("🎨 Extracting Critical CSS...");

      const pages = [
         { name: "home", path: "/index.html" },
         { name: "login", path: "/login.html" },
         { name: "dashboard", path: "/dashboard.html" },
      ];

      const cssFiles = [
         "dist/css/main.css",
         "dist/css/components.css",
      ];

      for (const page of pages) {
         const htmlPath = `dist${page.path}`;

         if (fs.existsSync(htmlPath)) {
            const htmlContent = fs.readFileSync(htmlPath, "utf8");
            const criticalCSS = await this.extractCriticalCSS(
               htmlContent,
               cssFiles,
            );

            const outputPath = `dist/css/critical-${page.name}.css`;
            fs.writeFileSync(outputPath, criticalCSS);

            console.log(
               `   ✅ Generated critical CSS for ${page.name}: ${outputPath}`,
            );
         }
      }
   }
}

// Execute
const extractor = new CriticalCSSExtractor();
extractor.generateCriticalCSS();
```

### 3. Bundle optimization para JavaScript con Vite

#### Rollup Bundle Analyzer Setup

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { visualizer } from "rollup-plugin-visualizer";
import { compression } from "vite-plugin-compression";

export default defineConfig({
   plugins: [
      react(),
      
      // Bundle analyzer nativo de Vite/Rollup
      visualizer({
         filename: "dist/bundle-analysis.html",
         open: true,
         gzipSize: true,
         brotliSize: true,
         template: "treemap", // treemap, sunburst, network
      }),

      // Compresión Gzip
      compression({
         algorithm: "gzip",
         ext: ".gz",
      }),

      // Compresión Brotli
      compression({
         algorithm: "brotliCompress",
         ext: ".br",
      }),
   ],

   build: {
      rollupOptions: {
         output: {
           // Manual chunks optimization para Vite
           manualChunks: {
             // Vendor chunks
             vendor: ["react", "react-dom"],
             router: ["react-router-dom"],
             ui: ["@radix-ui/react-dialog", "@radix-ui/react-select"],
             utils: ["lodash-es", "date-fns"],
           },
         },
       },
       // Configuración de optimización
       target: "es2015",
       minify: "terser",
       terserOptions: {
         compress: {
           drop_console: true,
           drop_debugger: true,
         },
       },
     },

   // Configuración de dependencias optimizadas
   optimizeDeps: {
     include: ["react", "react-dom", "react-router-dom"],
     exclude: ["@vite/client", "@vite/env"],
   },
});
```
```

#### Dynamic Import Optimization

```typescript
// src/utils/dynamicImports.ts
import { ComponentType, lazy } from "react";

// Higher-order component for lazy loading with error boundaries
export function withLazyLoading<T extends ComponentType<any>>(
   importFn: () => Promise<{ default: T }>,
   fallback: React.ComponentType = () => <div>Loading...</div>,
) {
   const LazyComponent = lazy(importFn);

   return (props: React.ComponentProps<T>) => (
      <React.Suspense fallback={<fallback />}>
         <LazyComponent {...props} />
      </React.Suspense>
   );
}

// Route-based code splitting
export const HomePage = withLazyLoading(() => import("../pages/HomePage"));
export const Dashboard = withLazyLoading(() => import("../pages/Dashboard"));
export const Profile = withLazyLoading(() => import("../pages/Profile"));

// Component-based code splitting
export const DataTable = withLazyLoading(() =>
   import("../components/DataTable")
);
export const Chart = withLazyLoading(() => import("../components/Chart"));

// Utility-based code splitting
export const loadDateUtils = () => import("date-fns");
export const loadChartLibrary = () => import("chart.js");

// Preload utilities for better UX
export function preloadComponent(importFn: () => Promise<any>) {
   const componentImport = importFn();
   return componentImport;
}

// Usage in router or component
export function preloadRoutes() {
   // Preload likely next routes
   preloadComponent(() => import("../pages/Dashboard"));
   preloadComponent(() => import("../pages/Profile"));
}
```

### 4. CDN y caching strategy

#### Service Worker for Asset Caching

```typescript
// public/sw.js
const CACHE_NAME = "app-cache-v1";
const STATIC_CACHE = "static-cache-v1";
const DYNAMIC_CACHE = "dynamic-cache-v1";

const STATIC_ASSETS = [
   "/",
   "/manifest.json",
   "/icons/icon-192x192.png",
   "/icons/icon-512x512.png",
   "/css/critical.css",
];

const CACHE_STRATEGIES = {
   images: "cache-first",
   api: "network-first",
   static: "cache-first",
   documents: "network-first",
};

// Install event
self.addEventListener("install", (event) => {
   event.waitUntil(
      caches.open(STATIC_CACHE)
         .then((cache) => cache.addAll(STATIC_ASSETS))
         .then(() => self.skipWaiting()),
   );
});

// Activate event
self.addEventListener("activate", (event) => {
   event.waitUntil(
      caches.keys().then((cacheNames) => {
         return Promise.all(
            cacheNames.map((cacheName) => {
               if (
                  cacheName !== CACHE_NAME && cacheName !== STATIC_CACHE &&
                  cacheName !== DYNAMIC_CACHE
               ) {
                  return caches.delete(cacheName);
               }
            }),
         );
      }).then(() => self.clients.claim()),
   );
});

// Fetch event with different strategies
self.addEventListener("fetch", (event) => {
   const { request } = event;
   const url = new URL(request.url);

   // Handle different types of requests
   if (request.destination === "image") {
      event.respondWith(handleImageRequest(request));
   } else if (url.pathname.startsWith("/api/")) {
      event.respondWith(handleApiRequest(request));
   } else if (request.destination === "document") {
      event.respondWith(handleDocumentRequest(request));
   } else {
      event.respondWith(handleStaticRequest(request));
   }
});

// Cache-first strategy for images
async function handleImageRequest(request) {
   const cache = await caches.open(DYNAMIC_CACHE);
   const cachedResponse = await cache.match(request);

   if (cachedResponse) {
      return cachedResponse;
   }

   try {
      const networkResponse = await fetch(request);
      if (networkResponse.ok) {
         cache.put(request, networkResponse.clone());
      }
      return networkResponse;
   } catch (error) {
      // Return fallback image
      return caches.match("/images/fallback.jpg");
   }
}

// Network-first strategy for API requests
async function handleApiRequest(request) {
   const cache = await caches.open(DYNAMIC_CACHE);

   try {
      const networkResponse = await fetch(request);
      if (networkResponse.ok) {
         cache.put(request, networkResponse.clone());
      }
      return networkResponse;
   } catch (error) {
      const cachedResponse = await cache.match(request);
      if (cachedResponse) {
         return cachedResponse;
      }
      throw error;
   }
}

// Network-first for documents with fallback
async function handleDocumentRequest(request) {
   try {
      return await fetch(request);
   } catch (error) {
      const cache = await caches.open(STATIC_CACHE);
      return cache.match("/offline.html");
   }
}

// Cache-first for static assets
async function handleStaticRequest(request) {
   const cache = await caches.open(STATIC_CACHE);
   const cachedResponse = await cache.match(request);

   return cachedResponse || fetch(request);
}
```

#### CDN Configuration (Cloudflare Workers)

```typescript
// cloudflare-worker.ts
addEventListener("fetch", (event) => {
   event.respondWith(handleRequest(event.request));
});

async function handleRequest(request: Request): Promise<Response> {
   const url = new URL(request.url);
   const cache = caches.default;

   // Image optimization
   if (url.pathname.match(/\.(jpg|jpeg|png|gif|webp)$/i)) {
      return handleImageRequest(request, cache);
   }

   // CSS/JS optimization
   if (url.pathname.match(/\.(css|js)$/i)) {
      return handleAssetRequest(request, cache);
   }

   // API caching
   if (url.pathname.startsWith("/api/")) {
      return handleApiRequest(request, cache);
   }

   return fetch(request);
}

async function handleImageRequest(
   request: Request,
   cache: Cache,
): Promise<Response> {
   const url = new URL(request.url);
   const acceptHeader = request.headers.get("Accept") || "";

   // Check for WebP support
   const supportsWebP = acceptHeader.includes("image/webp");
   const supportsAVIF = acceptHeader.includes("image/avif");

   // Modify URL for format conversion
   if (supportsAVIF && !url.pathname.includes(".avif")) {
      url.searchParams.set("format", "avif");
   } else if (supportsWebP && !url.pathname.includes(".webp")) {
      url.searchParams.set("format", "webp");
   }

   // Add optimization parameters
   url.searchParams.set("quality", "85");
   url.searchParams.set("progressive", "true");

   const cacheKey = new Request(url.toString());
   let response = await cache.match(cacheKey);

   if (!response) {
      response = await fetch(cacheKey);
      if (response.ok) {
         // Cache for 30 days
         const modifiedResponse = new Response(response.body, {
            status: response.status,
            statusText: response.statusText,
            headers: {
               ...response.headers,
               "Cache-Control": "public, max-age=2592000",
               "X-Optimized": "true",
            },
         });

         event.waitUntil(cache.put(cacheKey, modifiedResponse.clone()));
         return modifiedResponse;
      }
   }

   return response;
}

async function handleAssetRequest(
   request: Request,
   cache: Cache,
): Promise<Response> {
   let response = await cache.match(request);

   if (!response) {
      response = await fetch(request);
      if (response.ok) {
         const modifiedResponse = new Response(response.body, {
            status: response.status,
            statusText: response.statusText,
            headers: {
               ...response.headers,
               "Cache-Control": "public, max-age=31536000", // 1 year
               "X-Edge-Cache": "HIT",
            },
         });

         event.waitUntil(cache.put(request, modifiedResponse.clone()));
         return modifiedResponse;
      }
   }

   return response;
}
```

### 5. Automated resource optimization script

```bash
#!/bin/bash
# scripts/optimize-resources.sh

echo "🚀 Resource Optimization Pipeline"
echo "================================="

# Create optimization directory
OPTIMIZATION_DIR="optimized-assets-$(date +%Y%m%d-%H%M%S)"
mkdir -p $OPTIMIZATION_DIR

# 1. Image Optimization
echo "1. 📷 Optimizing Images..."

# WebP conversion
find src/assets/images -name "*.jpg" -o -name "*.png" | while read img; do
  filename=$(basename "$img")
  name="${filename%.*}"
  
  # Convert to WebP
  cwebp -q 85 "$img" -o "$OPTIMIZATION_DIR/${name}.webp"
  
  # Convert to AVIF (if supported)
  if command -v avifenc &> /dev/null; then
    avifenc -q 50 "$img" "$OPTIMIZATION_DIR/${name}.avif"
  fi
  
  echo "   ✅ Optimized: $filename"
done

# 2. CSS Optimization
echo "2. 🎨 Optimizing CSS..."

# Concatenate and minify CSS
cat src/styles/*.css | npx cssnano > $OPTIMIZATION_DIR/styles.min.css

# Extract critical CSS
node scripts/extract-critical-css.js > $OPTIMIZATION_DIR/critical.css

echo "   ✅ CSS optimized and critical path extracted"

# 3. JavaScript Optimization
echo "3. ⚡ Optimizing JavaScript..."

# Bundle analysis
npm run build:analyze > $OPTIMIZATION_DIR/bundle-analysis.txt

# Generate service worker
npx workbox generateSW workbox-config.js

echo "   ✅ JavaScript optimized and service worker generated"

# 4. Font Optimization
echo "4. 🔤 Optimizing Fonts..."

# Subset fonts (if pyftsubset is available)
if command -v pyftsubset &> /dev/null; then
  find src/assets/fonts -name "*.ttf" | while read font; do
    filename=$(basename "$font")
    name="${filename%.*}"
    
    pyftsubset "$font" \
      --output-file="$OPTIMIZATION_DIR/${name}-subset.woff2" \
      --flavor=woff2 \
      --layout-features="kern,liga" \
      --unicodes="U+0020-007F,U+00A0-00FF,U+0100-017F"
    
    echo "   ✅ Font subset: $filename"
  done
fi

# 5. Generate optimization report
echo "5. 📊 Generating Optimization Report..."

cat > $OPTIMIZATION_DIR/optimization-report.md << EOF
# Resource Optimization Report

**Generated:** $(date)
**Original Size:** $(du -sh src/assets/ | cut -f1)
**Optimized Size:** $(du -sh $OPTIMIZATION_DIR/ | cut -f1)

## Optimizations Applied

### Images
- ✅ WebP conversion (85% quality)
- ✅ AVIF conversion (50% quality)
- ✅ Lossless compression

### CSS
- ✅ Minification and concatenation
- ✅ Critical CSS extraction
- ✅ Unused CSS removal

### JavaScript
- ✅ Tree shaking
- ✅ Code splitting
- ✅ Compression (Gzip + Brotli)

### Fonts
- ✅ Font subsetting
- ✅ WOFF2 conversion
- ✅ Preload optimization

## Recommendations

- Use \`<link rel="preload">\` for critical fonts
- Implement lazy loading for below-the-fold images
- Consider using a CDN for static assets
- Enable HTTP/2 server push for critical resources

EOF

echo "✅ Resource optimization complete!"
echo "📁 Optimized assets saved to: $OPTIMIZATION_DIR"
echo "📊 Report available: $OPTIMIZATION_DIR/optimization-report.md"

# Calculate savings
ORIGINAL_SIZE=$(du -sb src/assets/ | cut -f1)
OPTIMIZED_SIZE=$(du -sb $OPTIMIZATION_DIR/ | cut -f1)
SAVINGS=$((ORIGINAL_SIZE - OPTIMIZED_SIZE))
PERCENTAGE=$((SAVINGS * 100 / ORIGINAL_SIZE))

echo "💾 Size reduction: $PERCENTAGE% ($SAVINGS bytes saved)"
```

### 6. Performance monitoring for resources

```typescript
// src/utils/resourceMonitoring.ts
interface ResourceTiming {
   name: string;
   size: number;
   duration: number;
   type: string;
}

class ResourcePerformanceMonitor {
   private observer: PerformanceObserver;
   private resources: ResourceTiming[] = [];

   constructor() {
      this.initializeObserver();
   }

   private initializeObserver(): void {
      this.observer = new PerformanceObserver((list) => {
         const entries = list.getEntries() as PerformanceResourceTiming[];

         entries.forEach((entry) => {
            this.analyzeResource(entry);
         });
      });

      this.observer.observe({ entryTypes: ["resource"] });
   }

   private analyzeResource(entry: PerformanceResourceTiming): void {
      const resource: ResourceTiming = {
         name: entry.name,
         size: entry.transferSize || 0,
         duration: entry.duration,
         type: this.getResourceType(entry.name),
      };

      this.resources.push(resource);
      this.checkPerformanceThresholds(resource);
   }

   private getResourceType(url: string): string {
      if (url.match(/\.(jpg|jpeg|png|gif|webp|avif|svg)$/i)) return "image";
      if (url.match(/\.(css)$/i)) return "stylesheet";
      if (url.match(/\.(js)$/i)) return "script";
      if (url.match(/\.(woff|woff2|ttf|otf)$/i)) return "font";
      return "other";
   }

   private checkPerformanceThresholds(resource: ResourceTiming): void {
      const thresholds = {
         image: { size: 500000, duration: 3000 }, // 500KB, 3s
         stylesheet: { size: 100000, duration: 2000 }, // 100KB, 2s
         script: { size: 200000, duration: 2000 }, // 200KB, 2s
         font: { size: 50000, duration: 1000 }, // 50KB, 1s
      };

      const threshold = thresholds[resource.type as keyof typeof thresholds];

      if (threshold) {
         if (resource.size > threshold.size) {
            console.warn(`Large ${resource.type} detected:`, {
               name: resource.name,
               size: `${(resource.size / 1024).toFixed(2)}KB`,
               recommendation: "Consider optimization",
            });
         }

         if (resource.duration > threshold.duration) {
            console.warn(`Slow ${resource.type} loading:`, {
               name: resource.name,
               duration: `${resource.duration.toFixed(2)}ms`,
               recommendation: "Check network or server performance",
            });
         }
      }
   }

   public getResourceSummary(): object {
      const summary = this.resources.reduce((acc, resource) => {
         const type = resource.type;
         if (!acc[type]) {
            acc[type] = {
               count: 0,
               totalSize: 0,
               totalDuration: 0,
               averageSize: 0,
               averageDuration: 0,
            };
         }

         acc[type].count++;
         acc[type].totalSize += resource.size;
         acc[type].totalDuration += resource.duration;

         return acc;
      }, {} as any);

      // Calculate averages
      Object.keys(summary).forEach((type) => {
         summary[type].averageSize = summary[type].totalSize /
            summary[type].count;
         summary[type].averageDuration = summary[type].totalDuration /
            summary[type].count;
      });

      return summary;
   }

   public generateReport(): void {
      const summary = this.getResourceSummary();

      console.group("📊 Resource Performance Report");
      console.table(summary);
      console.groupEnd();

      // Send to analytics
      if (typeof gtag !== "undefined") {
         Object.entries(summary).forEach(([type, stats]: [string, any]) => {
            gtag("event", "resource_performance", {
               event_category: "Performance",
               event_label: type,
               value: Math.round(stats.averageDuration),
            });
         });
      }
   }
}

// Initialize monitoring
export const resourceMonitor = new ResourcePerformanceMonitor();

// Report after page load
window.addEventListener("load", () => {
   setTimeout(() => {
      resourceMonitor.generateReport();
   }, 1000);
});
```

## Tips

- **Automatización**: Integra optimización de recursos en el proceso de build
- **Métricas continuas**: Monitorea el tamaño de bundles y performance en cada
  deploy
- **Progressive enhancement**: Implementa formatos modernos con fallbacks
  apropiados
- **Lazy loading estratégico**: Aplica lazy loading solo donde sea beneficioso
- **CDN edge locations**: Aprovecha la distribución geográfica para usuarios
  globales
- **Cache invalidation**: Implementa estrategias de versionado para cache
  busting
- **Budget de performance**: Establece límites de tamaño para cada tipo de
  recurso
- **Testing regular**: Prueba la optimización en dispositivos y conexiones
  reales

## Ejemplos

### Configuración completa de optimización para Vite

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { visualizer } from "rollup-plugin-visualizer";
import { compressionPlugin } from "vite-plugin-compression";

export default defineConfig({
   plugins: [
      react(),

      // Bundle analyzer
      visualizer({
         filename: "dist/stats.html",
         open: true,
         gzipSize: true,
         brotliSize: true,
      }),

      // Gzip compression
      compressionPlugin({
         algorithm: "gzip",
         ext: ".gz",
      }),

      // Brotli compression
      compressionPlugin({
         algorithm: "brotliCompress",
         ext: ".br",
      }),
   ],

   build: {
      target: "esnext",
      minify: "terser",

      rollupOptions: {
         output: {
            manualChunks: {
               vendor: ["react", "react-dom"],
               router: ["react-router-dom"],
               ui: ["@headlessui/react", "@heroicons/react"],
               utils: ["lodash-es", "date-fns"],
            },
         },
      },

      terserOptions: {
         compress: {
            drop_console: true,
            drop_debugger: true,
            pure_funcs: ["console.log", "console.info"],
            passes: 2,
         },
         mangle: {
            safari10: true,
         },
         format: {
            comments: false,
         },
      },

      // Asset optimization
      assetsInlineLimit: 4096, // 4KB
      cssCodeSplit: true,
      sourcemap: false,

      // Chunk size warnings
      chunkSizeWarningLimit: 500, // 500KB
   },

   // Asset optimization
   optimizeDeps: {
      include: ["react", "react-dom"],
      exclude: ["@vite/client", "@vite/env"],
   },
});
```

### GitHub Actions para optimización automática

```yaml
# .github/workflows/optimize-resources.yml
name: Optimize Resources

on:
  pull_request:
    paths:
      - 'src/assets/**'
      - 'public/**'
  push:
    branches: [main]

jobs:
  optimize-resources:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'

    - name: Install dependencies
      run: |
        npm ci
        npm install -g @squoosh/cli

    - name: Optimize images
      run: |
        find src/assets -name "*.jpg" -o -name "*.png" | while read img; do
          squoosh-cli --webp '{"quality":85}' --avif '{"quality":50}' "$img"
        done

    - name: Build and analyze bundle
      run: |
        npm run build
        npm run analyze

    - name: Generate optimization report
      run: |
        bash scripts/optimize-resources.sh
        
    - name: Upload optimization artifacts
      uses: actions/upload-artifact@v3
      with:
        name: optimization-report
        path: |
          dist/stats.html
          optimized-assets-*/
          
    - name: Comment PR with results
      if: github.event_name == 'pull_request'
      uses: actions/github-script@v6
      with:
        script: |
          const fs = require('fs');
          const path = require('path');
          
          // Read bundle stats
          const statsPath = 'dist/stats.json';
          if (fs.existsSync(statsPath)) {
            const stats = JSON.parse(fs.readFileSync(statsPath, 'utf8'));
            const bundleSize = Math.round(stats.assets.reduce((total, asset) => total + asset.size, 0) / 1024);
            
            const comment = `## 📦 Bundle Analysis
            
**Total Bundle Size:** ${bundleSize}KB

**Recommendations:**
- Consider lazy loading for components > 50KB
- Optimize images using WebP/AVIF formats
- Review large dependencies in vendor bundle

[View detailed analysis](${process.env.GITHUB_SERVER_URL}/${context.repo.owner}/${context.repo.repo}/actions/runs/${context.runId})
            `;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
          }
```

## ⚠️ Herramientas NO Permitidas para Optimización de Recursos

Para mantener consistencia con nuestro stack tecnológico establecido, **NO utilizar**:

### ❌ Bundlers y Build Tools Alternativos
- **Webpack + plugins**: Usar herramientas nativas de Vite/Rollup
- **webpack-bundle-analyzer**: Usar `rollup-plugin-visualizer`
- **webpack-compression**: Usar `vite-plugin-compression`
- **Next.js optimizations**: Para SPA usar React + Vite

### ❌ Herramientas de Imagen Obsoletas
- **imagemin-webpack-plugin**: Usar `vite-plugin-imagemin`
- **next/image**: Crear componente nativo para React + Vite
- **gatsby-image**: No compatible con nuestro stack

### ❌ CSS Optimization Tools Incompatibles
- **webpack css loaders**: Vite maneja CSS nativamente
- **extract-text-webpack-plugin**: Vite extrae CSS automáticamente
- **optimize-css-assets-webpack-plugin**: Usar configuración nativa de Vite

### ✅ Herramientas Recomendadas (Vite Ecosystem)

#### Plugins Esenciales
```bash
# Análisis y optimización
npm install --save-dev rollup-plugin-visualizer
npm install --save-dev vite-plugin-compression

# Optimización de imágenes
npm install --save-dev vite-plugin-imagemin
npm install --save-dev imagemin-webp imagemin-avif

# PWA y Service Workers
npm install --save-dev vite-plugin-pwa
```

#### Configuración de Producción Optimizada
```typescript
// vite.config.ts - Configuración completa
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { visualizer } from "rollup-plugin-visualizer";
import { compression } from "vite-plugin-compression";
import { VitePWA } from "vite-plugin-pwa";

export default defineConfig({
  plugins: [
    react(),
    visualizer({ filename: "dist/bundle-analysis.html" }),
    compression({ algorithm: "gzip" }),
    compression({ algorithm: "brotliCompress", ext: ".br" }),
    VitePWA({
      registerType: "autoUpdate",
      workbox: {
        globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
      },
    }),
  ],
  build: {
    target: "es2015",
    minify: "terser",
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ["react", "react-dom"],
          router: ["react-router-dom"],
        },
      },
    },
  },
});
```
```

## Navegación

[⬅️ Backup y Recovery](./backup-recovery-strategies.md) |
[🏠 README Principal](../../README.md) |
[Etapa 8: Despliegue y DevOps ➡️](../step_08/despliegue-devops.md)
