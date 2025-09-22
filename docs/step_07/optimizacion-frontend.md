# Optimización del Frontend (React + TypeScript + Vite)

## Conocimientos involucrados
- Core Web Vitals y métricas de rendimiento
- Herramientas como Lighthouse, Webpack Bundle Analyzer
- Técnicas de optimización específicas para React

## Responsable
- Equipo de desarrollo

# Optimización del Frontend (React + TypeScript + Vite)

> **IMPORTANTE**: Esta guía está específicamente diseñada para proyectos que
> utilizan **Vite como bundler estándar**. No incluye configuraciones para
> Webpack, Next.js u otros bundlers, manteniendo consistencia con nuestro stack
> tecnológico establecido.

## ¿Qué es?

La optimización del frontend es el conjunto de técnicas y estrategias aplicadas
para mejorar el rendimiento, velocidad de carga y experiencia del usuario en la
interfaz de la aplicación web. Incluye optimización de Core Web Vitals, bundle
splitting, lazy loading, critical rendering path y técnicas específicas para
React con TypeScript usando Vite como herramienta de desarrollo y build.

## ¿Por qué es importante?

- **Core Web Vitals**: Métricas críticas que afectan el ranking en motores de
  búsqueda
- **Experiencia del usuario**: Mejora significativa en la percepción de
  velocidad
- **Conversión**: Cada 100ms de mejora puede aumentar la conversión hasta 1%
- **SEO**: Google considera la velocidad como factor de ranking
- **Engagement**: Usuarios permanecen más tiempo en sitios rápidos
- **Mobile Performance**: Crucial para dispositivos con recursos limitados
- **Accesibilidad**: Mejor experiencia para conexiones lentas o dispositivos
  antiguos

## ¿Qué debe incluir?

### Core Web Vitals Optimization

#### Largest Contentful Paint (LCP)

- Optimización del elemento más grande visible
- Preload de recursos críticos
- Optimización de imágenes hero
- Server-side rendering para contenido crítico

#### First Input Delay (FID)

- Code splitting para reducir JavaScript inicial
- Lazy loading de componentes no críticos
- Web Workers para tareas pesadas
- Optimización de event listeners

#### Cumulative Layout Shift (CLS)

- Dimensiones explícitas para imágenes y videos
- Font loading optimizado
- Placeholder apropiados para contenido dinámico
- Evitar inserción de contenido sobre contenido existente

### Bundle Optimization

#### Code Splitting

- Route-based splitting
- Component-based splitting
- Dynamic imports
- Vendor bundle separation

#### Tree Shaking

- Eliminación de código muerto
- Import específicos vs namespace imports
- Side effects management
- Bundle analyzer para identificar código no usado

### Asset Optimization

#### Image Optimization

- Formatos modernos (WebP, AVIF)
- Responsive images con srcset
- Lazy loading inteligente
- Image compression automática

#### CSS Optimization

- Critical CSS extraction
- CSS minification y compression
- Unused CSS removal
- CSS-in-JS optimization

## ¿Qué debo hacer?

### 1. Configurar herramientas de análisis de performance

#### Lighthouse CI Integration

```bash
# Instalar Lighthouse CI
npm install -g @lhci/cli

# Configurar Lighthouse CI
cat > lighthouserc.js << 'EOF'
module.exports = {
  ci: {
    collect: {
      url: ['http://localhost:3000'],
      startServerCommand: 'npm run build && npm run preview',
      numberOfRuns: 3,
    },
    assert: {
      assertions: {
        'categories:performance': ['error', {minScore: 0.9}],
        'categories:accessibility': ['error', {minScore: 0.9}],
        'categories:best-practices': ['error', {minScore: 0.9}],
        'categories:seo': ['error', {minScore: 0.9}],

        // Core Web Vitals
        'first-contentful-paint': ['error', {maxNumericValue: 1800}],
        'largest-contentful-paint': ['error', {maxNumericValue: 2500}],
        'first-input-delay': ['error', {maxNumericValue: 100}],
        'cumulative-layout-shift': ['error', {maxNumericValue: 0.1}],

        // Other Performance Metrics
        'speed-index': ['error', {maxNumericValue: 3000}],
        'interactive': ['error', {maxNumericValue: 3800}],
        'total-blocking-time': ['error', {maxNumericValue: 300}],
      },
    },
    upload: {
      target: 'temporary-public-storage',
    },
  },
};
EOF

# Ejecutar análisis
lhci autorun
```

#### Bundle Analyzer Setup (Vite)

```typescript
// vite.config.ts - Configuración de análisis de bundles
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { visualizer } from "rollup-plugin-visualizer";

export default defineConfig({
   plugins: [
      react(),
      // Analizador de bundles para Vite
      visualizer({
         filename: "dist/bundle-analysis.html",
         open: true,
         gzipSize: true,
         brotliSize: true,
         template: "treemap", // treemap, sunburst, network
      }),
   ],
   build: {
      // Configuración adicional para análisis
      rollupOptions: {
         output: {
            manualChunks: {
               vendor: ["react", "react-dom"],
               router: ["react-router-dom"],
               ui: ["@radix-ui/react-dialog", "@radix-ui/react-select"],
            },
         },
      },
   },
});
```

#### Performance Monitoring Hook

```typescript
// src/hooks/usePerformanceMonitoring.ts
import { useEffect } from "react";

interface PerformanceMetrics {
   lcp: number;
   fid: number;
   cls: number;
   fcp: number;
   ttfb: number;
}

export const usePerformanceMonitoring = () => {
   useEffect(() => {
      // Observar LCP
      const lcpObserver = new PerformanceObserver((entryList) => {
         const entries = entryList.getEntries();
         const lastEntry = entries[entries.length - 1];

         console.log("LCP:", lastEntry.startTime);

         // Enviar a analytics
         if (typeof gtag !== "undefined") {
            gtag("event", "web_vitals", {
               event_category: "Performance",
               event_label: "LCP",
               value: Math.round(lastEntry.startTime),
            });
         }
      });

      lcpObserver.observe({ entryTypes: ["largest-contentful-paint"] });

      // Observar CLS
      const clsObserver = new PerformanceObserver((entryList) => {
         let clsValue = 0;

         for (const entry of entryList.getEntries()) {
            if (!entry.hadRecentInput) {
               clsValue += entry.value;
            }
         }

         console.log("CLS:", clsValue);

         if (typeof gtag !== "undefined") {
            gtag("event", "web_vitals", {
               event_category: "Performance",
               event_label: "CLS",
               value: Math.round(clsValue * 1000),
            });
         }
      });

      clsObserver.observe({ entryTypes: ["layout-shift"] });

      // Observar FID
      const fidObserver = new PerformanceObserver((entryList) => {
         for (const entry of entryList.getEntries()) {
            console.log("FID:", entry.processingStart - entry.startTime);

            if (typeof gtag !== "undefined") {
               gtag("event", "web_vitals", {
                  event_category: "Performance",
                  event_label: "FID",
                  value: Math.round(entry.processingStart - entry.startTime),
               });
            }
         }
      });

      fidObserver.observe({ entryTypes: ["first-input"] });

      return () => {
         lcpObserver.disconnect();
         clsObserver.disconnect();
         fidObserver.disconnect();
      };
   }, []);

   const getPerformanceMetrics = (): Promise<PerformanceMetrics> => {
      return new Promise((resolve) => {
         const metrics: Partial<PerformanceMetrics> = {};

         // Obtener TTFB
         const navigation = performance.getEntriesByType(
            "navigation",
         )[0] as PerformanceNavigationTiming;
         metrics.ttfb = navigation.responseStart - navigation.requestStart;

         // Obtener FCP
         const paintEntries = performance.getEntriesByType("paint");
         const fcpEntry = paintEntries.find(
            (entry) => entry.name === "first-contentful-paint",
         );
         if (fcpEntry) {
            metrics.fcp = fcpEntry.startTime;
         }

         // Los otros valores se obtienen de los observers
         resolve(metrics as PerformanceMetrics);
      });
   };

   return { getPerformanceMetrics };
};
```

### 2. Implementar optimizaciones de React con Vite

#### Component Optimization

```tsx
// src/components/OptimizedComponent.tsx
import React, { lazy, memo, Suspense, useCallback, useMemo } from "react";
import { ErrorBoundary } from "react-error-boundary";

// Lazy loading de componentes pesados
const HeavyChart = lazy(() =>
   import("./HeavyChart").then((module) => ({
      default: module.HeavyChart,
   }))
);

const ExpensiveChildComponent = lazy(() => import("./ExpensiveChildComponent"));

interface OptimizedComponentProps {
   data: any[];
   onItemClick: (id: string) => void;
   filterValue: string;
}

export const OptimizedComponent = memo<OptimizedComponentProps>(
   ({ data, onItemClick, filterValue }) => {
      // Memoizar cálculos costosos
      const filteredData = useMemo(() => {
         return data.filter((item) =>
            item.name.toLowerCase().includes(filterValue.toLowerCase())
         );
      }, [data, filterValue]);

      const expensiveCalculation = useMemo(() => {
         return filteredData.reduce((sum, item) => sum + item.value, 0);
      }, [filteredData]);

      // Memoizar callbacks para evitar re-renders innecesarios
      const handleItemClick = useCallback(
         (id: string) => {
            onItemClick(id);
         },
         [onItemClick],
      );

      // Renderizado condicional para componentes pesados
      const shouldRenderChart = filteredData.length > 0 &&
         filteredData.length < 1000;

      return (
         <div className="optimized-component">
            <div className="summary">
               <p>Total items: {filteredData.length}</p>
               <p>Total value: {expensiveCalculation}</p>
            </div>

            {/* Lista virtualizada para grandes datasets */}
            <VirtualizedList
               items={filteredData}
               onItemClick={handleItemClick}
            />

            {/* Lazy loading con error boundary */}
            {shouldRenderChart && (
               <ErrorBoundary fallback={<div>Error loading chart</div>}>
                  <Suspense fallback={<ChartSkeleton />}>
                     <HeavyChart data={filteredData} />
                  </Suspense>
               </ErrorBoundary>
            )}

            {/* Componente condicional pesado */}
            {filteredData.length > 100 && (
               <Suspense fallback={<div>Loading analytics...</div>}>
                  <ExpensiveChildComponent data={filteredData} />
               </Suspense>
            )}
         </div>
      );
   },
);

OptimizedComponent.displayName = "OptimizedComponent";
```

#### Virtual Scrolling Implementation

```tsx
// src/components/VirtualizedList.tsx
import React, { useEffect, useMemo, useRef, useState } from "react";

interface VirtualizedListProps {
   items: any[];
   itemHeight: number;
   containerHeight: number;
   onItemClick: (id: string) => void;
   renderItem: (item: any, index: number) => React.ReactNode;
}

export const VirtualizedList: React.FC<VirtualizedListProps> = ({
   items,
   itemHeight = 50,
   containerHeight = 400,
   onItemClick,
   renderItem,
}) => {
   const [scrollTop, setScrollTop] = useState(0);
   const containerRef = useRef<HTMLDivElement>(null);

   // Calcular qué elementos son visibles
   const visibleRange = useMemo(() => {
      const visibleStart = Math.floor(scrollTop / itemHeight);
      const visibleEnd = Math.min(
         visibleStart + Math.ceil(containerHeight / itemHeight) + 1,
         items.length,
      );

      return { start: visibleStart, end: visibleEnd };
   }, [scrollTop, itemHeight, containerHeight, items.length]);

   // Elementos visibles
   const visibleItems = useMemo(() => {
      return items.slice(visibleRange.start, visibleRange.end);
   }, [items, visibleRange]);

   const totalHeight = items.length * itemHeight;
   const offsetY = visibleRange.start * itemHeight;

   const handleScroll = (e: React.UIEvent<HTMLDivElement>) => {
      setScrollTop(e.currentTarget.scrollTop);
   };

   return (
      <div
         ref={containerRef}
         className="virtualized-list"
         style={{ height: containerHeight, overflow: "auto" }}
         onScroll={handleScroll}
      >
         <div style={{ height: totalHeight, position: "relative" }}>
            <div
               style={{
                  transform: `translateY(${offsetY}px)`,
                  position: "absolute",
                  top: 0,
                  left: 0,
                  right: 0,
               }}
            >
               {visibleItems.map((item, index) => (
                  <div
                     key={item.id || visibleRange.start + index}
                     style={{
                        height: itemHeight,
                        display: "flex",
                        alignItems: "center",
                        padding: "0 16px",
                        borderBottom: "1px solid #eee",
                        cursor: "pointer",
                     }}
                     onClick={() => onItemClick(item.id)}
                  >
                     {renderItem
                        ? (
                           renderItem(item, visibleRange.start + index)
                        )
                        : (
                           <span>
                              {item.name ||
                                 `Item ${visibleRange.start + index}`}
                           </span>
                        )}
                  </div>
               ))}
            </div>
         </div>
      </div>
   );
};
```

### 3. Optimización de imágenes con Vite

#### Componente de Imagen Optimizada (Vite + React)

```tsx
// src/components/OptimizedImage.tsx
import { useEffect, useRef, useState } from "react";

interface OptimizedImageProps {
   src: string;
   alt: string;
   width?: number;
   height?: number;
   priority?: boolean;
   placeholder?: string;
   className?: string;
   onLoad?: () => void;
   onError?: () => void;
   lazy?: boolean;
}

export const OptimizedImage: React.FC<OptimizedImageProps> = ({
   src,
   alt,
   width,
   height,
   priority = false,
   placeholder,
   className,
   onLoad,
   onError,
   lazy = true,
}) => {
   const [isLoading, setIsLoading] = useState(true);
   const [error, setError] = useState(false);
   const [isInView, setIsInView] = useState(false);
   const imgRef = useRef<HTMLDivElement>(null);

   // Intersection Observer para lazy loading
   useEffect(() => {
      if (!imgRef.current || priority || !lazy) {
         setIsInView(true);
         return;
      }

      const observer = new IntersectionObserver(
         ([entry]) => {
            if (entry.isIntersecting) {
               setIsInView(true);
               observer.disconnect();
            }
         },
         { rootMargin: "50px" },
      );

      observer.observe(imgRef.current);

      return () => observer.disconnect();
   }, [priority, lazy]);

   // Procesar diferentes formatos de imagen con Vite
   const getOptimizedSrc = (src: string, width?: number) => {
      // Vite puede procesar imágenes con query params para optimización
      if (src.startsWith("/") && width) {
         return `${src}?w=${width}&format=webp`;
      }
      return src;
   };

   const handleLoad = () => {
      setIsLoading(false);
      onLoad?.();
   };

   const handleError = () => {
      setError(true);
      setIsLoading(false);
      onError?.();
   };

   if (error) {
      return (
         <div
            className={`flex items-center justify-center bg-gray-200 ${className}`}
            style={{ width, height }}
         >
            <span className="text-gray-500 text-sm">Failed to load image</span>
         </div>
      );
   }

   return (
      <div ref={imgRef} className={`relative overflow-hidden ${className}`}>
         {isInView && (
            <>
               <img
                  src={getOptimizedSrc(src, width)}
                  alt={alt}
                  width={width}
                  height={height}
                  loading={lazy && !priority ? "lazy" : "eager"}
                  className={`transition-opacity duration-300 object-cover ${
                     isLoading ? "opacity-0" : "opacity-100"
                  }`}
                  onLoad={handleLoad}
                  onError={handleError}
                  style={{
                     width: width ? `${width}px` : "100%",
                     height: height ? `${height}px` : "auto",
                  }}
               />
               {isLoading && (
                  <div
                     className="absolute inset-0 bg-gray-200 animate-pulse"
                     style={{
                        backgroundImage: placeholder
                           ? `url(${placeholder})`
                           : undefined,
                        backgroundSize: "cover",
                        backgroundPosition: "center",
                     }}
                  />
               )}
               )}
            </>
         )}

         {!isInView && (
            <div
               className="bg-gray-200 animate-pulse"
               style={{ width, height }}
            />
         )}
      </div>
   );
};
```

#### Configuración de Vite para Optimización de Imágenes

```typescript
// vite.config.ts - Configuración de imágenes
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
   plugins: [react()],

   // Configuración de assets
   assetsInclude: ["**/*.webp", "**/*.avif"],

   build: {
      rollupOptions: {
         output: {
            assetFileNames: (assetInfo) => {
               if (
                  assetInfo.name?.endsWith(".png") ||
                  assetInfo.name?.endsWith(".jpg") ||
                  assetInfo.name?.endsWith(".jpeg") ||
                  assetInfo.name?.endsWith(".webp")
               ) {
                  return "images/[name]-[hash][extname]";
               }
               return "assets/[name]-[hash][extname]";
            },
         },
      },
   },

   // Optimización de imports estáticos
   define: {
      __IMAGE_OPTIMIZATION__: true,
   },
});
```

#### Hook para Lazy Loading de Imágenes

```tsx
// src/hooks/useImageLazyLoading.ts
import { useEffect, useRef, useState } from "react";

interface UseImageLazyLoadingOptions {
   rootMargin?: string;
   threshold?: number;
}

export const useImageLazyLoading = (
   options: UseImageLazyLoadingOptions = {},
) => {
   const { rootMargin = "50px", threshold = 0.1 } = options;
   const [isInView, setIsInView] = useState(false);
   const imgRef = useRef<HTMLElement>(null);

   useEffect(() => {
      const observer = new IntersectionObserver(
         ([entry]) => {
            if (entry.isIntersecting) {
               setIsInView(true);
               observer.disconnect();
            }
         },
         { rootMargin, threshold },
      );

      if (imgRef.current) {
         observer.observe(imgRef.current);
      }

      return () => observer.disconnect();
   }, [rootMargin, threshold]);

   return { imgRef, isInView };
};
```

       setCurrentSrc(highQualitySrc);
       setIsHighQualityLoaded(true);
    };
    highQualityImg.src = highQualitySrc;

}, [highQualitySrc]);

return (
<div className="relative overflow-hidden"> <img ref={imgRef} src={currentSrc}
alt={alt}
className={`transition-all duration-500 ${
               isHighQualityLoaded ? "filter-none" : "filter blur-sm scale-105"
            } ${className}`}
loading="lazy" />

       {!isHighQualityLoaded && (
          <div className="absolute inset-0 bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 animate-pulse" />
       )}
    </div>

); };

````
### 4. Critical CSS y Font Loading

#### Critical CSS Extraction

```javascript
// scripts/extract-critical-css.js
const critical = require("critical");
const path = require("path");
const fs = require("fs");

async function extractCriticalCSS() {
   const pages = [
      { name: "home", url: "http://localhost:3000" },
      { name: "about", url: "http://localhost:3000/about" },
      { name: "products", url: "http://localhost:3000/products" },
      { name: "contact", url: "http://localhost:3000/contact" },
   ];

   for (const page of pages) {
      try {
         console.log(`Extracting critical CSS for ${page.name}...`);

         const result = await critical.generate({
            base: "dist/",
            src: page.url,
            target: {
               css: `critical-${page.name}.css`,
               html: `${page.name}.html`,
               uncritical: `uncritical-${page.name}.css`,
            },
            width: 1300,
            height: 900,
            dimensions: [
               { width: 320, height: 480 },
               { width: 768, height: 1024 },
               { width: 1300, height: 900 },
            ],
            inline: true,
            minify: true,
            extract: true,
            ignore: {
               atrule: ["@font-face"],
               rule: [/some-unused-class/],
               decl: (node, value) => /url\(/.test(value),
            },
         });

         console.log(`✅ Critical CSS extracted for ${page.name}`);

         // Guardar estadísticas
         const stats = {
            page: page.name,
            originalSize:
               fs.statSync(path.join("dist", "css", "main.css")).size,
            criticalSize: result.css.length,
            reduction: (
               (1 -
                  result.css.length /
                     fs.statSync(path.join("dist", "css", "main.css")).size) *
               100
            ).toFixed(2) + "%",
         };

         console.log(
            `   Original: ${(stats.originalSize / 1024).toFixed(2)}KB`,
         );
         console.log(
            `   Critical: ${(stats.criticalSize / 1024).toFixed(2)}KB`,
         );
         console.log(`   Reduction: ${stats.reduction}`);
      } catch (error) {
         console.error(
            `Error extracting critical CSS for ${page.name}:`,
            error,
         );
      }
   }
}

extractCriticalCSS();
````

#### Font Loading Optimization

```typescript
// src/utils/fontLoading.ts
export class FontLoadingOptimizer {
   private static fontFaceObserver?: any;

   static async loadFonts(): Promise<void> {
      // Importar dinámicamente FontFaceObserver
      if (!this.fontFaceObserver) {
         const { default: FontFaceObserver } = await import("fontfaceobserver");
         this.fontFaceObserver = FontFaceObserver;
      }

      const fonts = [
         { family: "Inter", weight: "400" },
         { family: "Inter", weight: "500" },
         { family: "Inter", weight: "600" },
         { family: "Inter", weight: "700" },
      ];

      const fontPromises = fonts.map((font) => {
         const observer = new this.fontFaceObserver(font.family, {
            weight: font.weight,
         });
         return observer.load(null, 5000); // timeout de 5 segundos
      });

      try {
         await Promise.all(fontPromises);
         document.documentElement.classList.add("fonts-loaded");
         console.log("✅ All fonts loaded successfully");
      } catch (error) {
         console.warn("⚠️ Some fonts failed to load:", error);
         document.documentElement.classList.add("fonts-failed");
      }
   }

   static preloadFonts(): void {
      const fontUrls = [
         "/fonts/inter-var.woff2",
         "/fonts/inter-italic-var.woff2",
      ];

      fontUrls.forEach((url) => {
         const link = document.createElement("link");
         link.rel = "preload";
         link.as = "font";
         link.type = "font/woff2";
         link.crossOrigin = "anonymous";
         link.href = url;
         document.head.appendChild(link);
      });
   }

   static inlineSystemFontStack(): void {
      const style = document.createElement("style");
      style.textContent = `
      :root {
        --font-sans: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        --font-mono: 'SF Mono', Monaco, 'Inconsolata', 'Roboto Mono', monospace;
      }
      
      .font-sans { font-family: var(--font-sans); }
      .font-mono { font-family: var(--font-mono); }
      
      /* Font loading states */
      .fonts-loading .font-sans { font-family: var(--font-sans); }
      .fonts-loaded .font-sans { font-family: 'Inter', var(--font-sans); }
      .fonts-failed .font-sans { font-family: var(--font-sans); }
    `;
      document.head.appendChild(style);
   }
}

// Auto-inicializar
if (typeof window !== "undefined") {
   FontLoadingOptimizer.inlineSystemFontStack();
   FontLoadingOptimizer.preloadFonts();

   document.addEventListener("DOMContentLoaded", () => {
      FontLoadingOptimizer.loadFonts();
   });
}
```

### 5. Service Worker para caching avanzado

```typescript
// public/sw.js
const CACHE_NAME = "app-cache-v1";
const STATIC_CACHE = "static-cache-v1";
const DYNAMIC_CACHE = "dynamic-cache-v1";
const API_CACHE = "api-cache-v1";

const STATIC_ASSETS = [
   "/",
   "/manifest.json",
   "/offline.html",
   "/css/critical.css",
   "/fonts/inter-var.woff2",
];

// Estrategias de cache personalizadas
const CACHE_STRATEGIES = {
   "/api/": "network-first",
   ".js": "stale-while-revalidate",
   ".css": "stale-while-revalidate",
   ".woff2": "cache-first",
   ".jpg": "cache-first",
   ".png": "cache-first",
   ".webp": "cache-first",
   ".avif": "cache-first",
};

self.addEventListener("install", (event) => {
   event.waitUntil(
      caches
         .open(STATIC_CACHE)
         .then((cache) => cache.addAll(STATIC_ASSETS))
         .then(() => self.skipWaiting()),
   );
});

self.addEventListener("activate", (event) => {
   event.waitUntil(
      caches
         .keys()
         .then((cacheNames) => {
            return Promise.all(
               cacheNames.map((cacheName) => {
                  if (
                     ![CACHE_NAME, STATIC_CACHE, DYNAMIC_CACHE, API_CACHE]
                        .includes(
                           cacheName,
                        )
                  ) {
                     return caches.delete(cacheName);
                  }
               }),
            );
         })
         .then(() => self.clients.claim()),
   );
});

self.addEventListener("fetch", (event) => {
   const { request } = event;
   const url = new URL(request.url);

   // Determinar estrategia de cache
   const strategy = getStrategy(url.pathname, request);

   switch (strategy) {
      case "cache-first":
         event.respondWith(cacheFirst(request));
         break;
      case "network-first":
         event.respondWith(networkFirst(request));
         break;
      case "stale-while-revalidate":
         event.respondWith(staleWhileRevalidate(request));
         break;
      default:
         event.respondWith(fetch(request));
   }
});

function getStrategy(pathname, request) {
   // API requests
   if (pathname.startsWith("/api/")) {
      return "network-first";
   }

   // Static assets
   for (const [pattern, strategy] of Object.entries(CACHE_STRATEGIES)) {
      if (
         pathname.includes(pattern) ||
         request.destination === getDestinationType(pattern)
      ) {
         return strategy;
      }
   }

   return "network-first";
}

function getDestinationType(pattern) {
   const typeMap = {
      ".js": "script",
      ".css": "style",
      ".woff2": "font",
      ".jpg": "image",
      ".png": "image",
      ".webp": "image",
      ".avif": "image",
   };
   return typeMap[pattern];
}

async function cacheFirst(request) {
   const cache = await caches.open(DYNAMIC_CACHE);
   const cached = await cache.match(request);

   if (cached) {
      return cached;
   }

   try {
      const response = await fetch(request);
      if (response.ok) {
         cache.put(request, response.clone());
      }
      return response;
   } catch (error) {
      if (request.destination === "document") {
         return caches.match("/offline.html");
      }
      throw error;
   }
}

async function networkFirst(request) {
   const cache = await caches.open(API_CACHE);

   try {
      const response = await fetch(request);
      if (response.ok) {
         cache.put(request, response.clone());
      }
      return response;
   } catch (error) {
      const cached = await cache.match(request);
      if (cached) {
         return cached;
      }
      throw error;
   }
}

async function staleWhileRevalidate(request) {
   const cache = await caches.open(DYNAMIC_CACHE);
   const cached = await cache.match(request);

   const fetchPromise = fetch(request).then((response) => {
      if (response.ok) {
         cache.put(request, response.clone());
      }
      return response;
   });

   return cached || fetchPromise;
}
```

### 6. Automated Performance Testing

```typescript
// tests/performance/lighthouse-ci.test.ts
import { execSync } from "child_process";
import { readFileSync } from "fs";

describe("Lighthouse Performance Tests", () => {
   const urls = [
      "http://localhost:3000",
      "http://localhost:3000/about",
      "http://localhost:3000/products",
   ];

   urls.forEach((url) => {
      test(`Performance audit for ${url}`, async () => {
         // Ejecutar Lighthouse
         const command =
            `lighthouse ${url} --output=json --chrome-flags="--headless --no-sandbox"`;

         try {
            execSync(command);
            const report = JSON.parse(
               readFileSync("./lighthouse-report.json", "utf8"),
            );

            // Verificar Core Web Vitals
            expect(report.categories.performance.score).toBeGreaterThanOrEqual(
               0.9,
            );

            const lcp = report.audits["largest-contentful-paint"].numericValue;
            const fid = report.audits["max-potential-fid"].numericValue;
            const cls = report.audits["cumulative-layout-shift"].numericValue;
            const fcp = report.audits["first-contentful-paint"].numericValue;

            expect(lcp).toBeLessThanOrEqual(2500);
            expect(fid).toBeLessThanOrEqual(100);
            expect(cls).toBeLessThanOrEqual(0.1);
            expect(fcp).toBeLessThanOrEqual(1800);

            // Verificar otras métricas
            const speedIndex = report.audits["speed-index"].numericValue;
            const interactive = report.audits["interactive"].numericValue;

            expect(speedIndex).toBeLessThanOrEqual(3000);
            expect(interactive).toBeLessThanOrEqual(3800);
         } catch (error) {
            throw new Error(
               `Lighthouse audit failed for ${url}: ${error.message}`,
            );
         }
      }, 60000); // timeout de 60 segundos
   });
});
```

## Tips

- **Medir antes de optimizar**: Siempre establece un baseline antes de aplicar
  optimizaciones
- **Core Web Vitals primero**: Prioriza LCP, FID y CLS por su impacto en SEO
- **Progressive enhancement**: Implementa funcionalidades básicas primero, luego
  mejoras
- **Bundle analysis regular**: Revisa el tamaño de bundles en cada deploy
- **Lazy loading estratégico**: No todos los componentes se benefician del lazy
  loading
- **Image optimization**: Usa formatos modernos con fallbacks apropiados
- **Critical CSS**: Extrae y inline CSS crítico para above-the-fold content
- **Font loading**: Implementa font-display: swap y preload de fuentes críticas
- **Service Workers**: Implementa caching inteligente para offline-first
  experience
- **Performance budgets**: Establece límites claros para métricas clave

## Ejemplos

### Performance Budget Configuration

```json
{
   "performance": {
      "budgets": {
         "lighthouse": {
            "performance": 90,
            "accessibility": 90,
            "best-practices": 90,
            "seo": 90
         },
         "webVitals": {
            "lcp": 2500,
            "fid": 100,
            "cls": 0.1,
            "fcp": 1800,
            "ttfb": 600
         },
         "resources": {
            "html": "5KB",
            "css": "50KB",
            "js": "150KB",
            "images": "500KB",
            "fonts": "100KB",
            "total": "1MB"
         },
         "network": {
            "requests": 50,
            "domains": 5
         }
      },
      "monitoring": {
         "alerts": {
            "performance_score": 85,
            "lcp_threshold": 3000,
            "bundle_size": "200KB"
         }
      }
   }
}
```

### Vite Optimization Configuration

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { visualizer } from "rollup-plugin-visualizer";
import { VitePWA } from "vite-plugin-pwa";

export default defineConfig({
   plugins: [
      react({
         // Fast Refresh optimizations
         fastRefresh: true,
         // Automatic JSX runtime
         jsxRuntime: "automatic",
      }),

      // Bundle analyzer
      visualizer({
         filename: "dist/bundle-analysis.html",
         open: true,
         gzipSize: true,
         brotliSize: true,
      }),

      // PWA with Service Worker
      VitePWA({
         registerType: "autoUpdate",
         workbox: {
            globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
            runtimeCaching: [
               {
                  urlPattern: /^https:\/\/api\./,
                  handler: "NetworkFirst",
                  options: {
                     cacheName: "api-cache",
                     expiration: {
                        maxEntries: 100,
                        maxAgeSeconds: 300,
                     },
                  },
               },
               {
                  urlPattern: /\.(?:png|jpg|jpeg|svg|gif|webp|avif)$/,
                  handler: "CacheFirst",
                  options: {
                     cacheName: "images-cache",
                     expiration: {
                        maxEntries: 200,
                        maxAgeSeconds: 86400,
                     },
                  },
               },
            ],
         },
      }),
   ],

   build: {
      // Target modern browsers
      target: "esnext",

      // Enable minification
      minify: "terser",
      terserOptions: {
         compress: {
            drop_console: true,
            drop_debugger: true,
            pure_funcs: ["console.log"],
         },
      },

      // Optimize chunk splitting
      rollupOptions: {
         output: {
            manualChunks: {
               // Vendor chunk
               vendor: ["react", "react-dom"],

               // Router chunk
               router: ["react-router-dom"],

               // UI library chunk
               ui: ["@headlessui/react", "@heroicons/react"],

               // Utility libraries
               utils: ["lodash-es", "date-fns", "classnames"],

               // Charts/visualization (if used)
               charts: ["chart.js", "recharts"],
            },
         },
      },

      // Source maps for production debugging
      sourcemap: true,

      // Asset optimization
      assetsInlineLimit: 4096, // 4KB
      cssCodeSplit: true,

      // Chunk size warning threshold
      chunkSizeWarningLimit: 500,
   },

   // Optimize dependencies
   optimizeDeps: {
      include: ["react", "react-dom", "react-router-dom"],
      exclude: ["@vite/client", "@vite/env"],
   },

   // Development server optimization
   server: {
      hmr: {
         overlay: false,
      },
   },
});
```

### GitHub Actions Performance CI

```yaml
# .github/workflows/performance-ci.yml
name: Performance CI

on:
  pull_request:
    branches: [main]
    paths: ['src/**', 'public/**', 'package.json']

jobs:
  lighthouse-ci:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Build application
      run: npm run build

    - name: Start preview server
      run: |
        npm run preview &
        sleep 10

    - name: Run Lighthouse CI
      run: |
        npm install -g @lhci/cli
        lhci autorun
      env:
        LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}

    - name: Bundle size analysis
      run: |
        npm run analyze
        BUNDLE_SIZE=$(du -sh dist/ | cut -f1)
        echo "Bundle size: $BUNDLE_SIZE" >> $GITHUB_STEP_SUMMARY

    - name: Upload Lighthouse reports
      uses: actions/upload-artifact@v3
      with:
        name: lighthouse-reports
        path: .lighthouseci

    - name: Comment PR with results
      uses: actions/github-script@v6
      with:
        script: |
          const fs = require('fs');

          try {
            const lhciResults = JSON.parse(fs.readFileSync('.lighthouseci/lhr-*.json'));
            const scores = lhciResults.categories;

            const comment = `## 🚀 Performance Report

**Lighthouse Scores:**
- Performance: ${Math.round(scores.performance.score * 100)}%
- Accessibility: ${Math.round(scores.accessibility.score * 100)}%
- Best Practices: ${Math.round(scores['best-practices'].score * 100)}%
- SEO: ${Math.round(scores.seo.score * 100)}%

**Core Web Vitals:**
- LCP: ${lhciResults.audits['largest-contentful-paint'].displayValue}
- FID: ${lhciResults.audits['max-potential-fid'].displayValue}
- CLS: ${lhciResults.audits['cumulative-layout-shift'].displayValue}

[View detailed report](${process.env.GITHUB_SERVER_URL}/${context.repo.owner}/${context.repo.repo}/actions/runs/${context.runId})
            `;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
          } catch (error) {
            console.log('Could not parse Lighthouse results:', error);
          }
```

## ⚠️ Herramientas NO Permitidas

Para mantener consistencia con nuestro stack tecnológico establecido, **NO
utilizar**:

### ❌ Bundlers Alternativos

- **Webpack**: Reemplazado por Vite para mejor DX y performance
- **Rollup directo**: Vite ya usa Rollup internamente con optimizaciones
- **Parcel**: No alineado con nuestro stack estándar
- **ESBuild directo**: Vite ya lo integra para transformaciones

### ❌ Frameworks SSR/SSG

- **Next.js**: Para aplicaciones SPA usar React + Vite
- **Gatsby**: No alineado con nuestra arquitectura backend-frontend separada
- **Remix**: Incompatible con nuestro backend Laravel existente

### ❌ Herramientas de Análisis Webpack

- **webpack-bundle-analyzer**: Usar `rollup-plugin-visualizer`
- **webpack-dashboard**: Vite tiene su propio dashboard
- **webpack específico plugins**: Buscar equivalentes para Vite/Rollup

### ✅ Herramientas Recomendadas (Vite Ecosystem)

#### Análisis y Optimización

```bash
# Bundle analyzer para Vite
npm install --save-dev rollup-plugin-visualizer

# Optimizaciones específicas de Vite
npm install --save-dev vite-plugin-pwa
npm install --save-dev @vitejs/plugin-react-swc
```

#### Performance Monitoring

```bash
# Lighthouse CI
npm install --save-dev @lhci/cli

# Web Vitals específicas
npm install web-vitals
```

#### Build Optimization

```typescript
// vite.config.ts - Configuración de producción optimizada
export default defineConfig({
   plugins: [
      react(),
      visualizer({
         filename: "dist/bundle-analysis.html",
         gzipSize: true,
         brotliSize: true,
      }),
   ],
   build: {
      target: "es2015",
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

## Navegación

[⬅️ Checklist de File System](./checklist-file-system.md) |
[🏠 README Principal](../../README.md) |
[Estrategias de Caché ➡️](./estrategias-cache.md)
