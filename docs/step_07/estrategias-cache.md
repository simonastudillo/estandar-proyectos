# Estrategias de Caché

## ¿Qué es?

Las estrategias de caché son metodologías y técnicas sistemáticas para
almacenar temporalmente datos frecuentemente accedidos en ubicaciones de
acceso rápido, reduciendo la latencia y mejorando el rendimiento de la
aplicación. Incluye caché de navegador, caché de aplicación, caché de base de
datos, CDN y estrategias de invalidación inteligente.

## ¿Por qué es importante?

- **Performance**: Reduce significativamente los tiempos de respuesta
- **Escalabilidad**: Disminuye la carga en servidores y bases de datos
- **Experiencia del usuario**: Mejora la percepción de velocidad
- **Reducción de costos**: Menor uso de recursos computacionales y ancho de
  banda
- **Disponibilidad**: Proporciona resilencia ante fallos de servicios externos
- **SEO**: Mejora métricas de velocidad que afectan el ranking
- **Eficiencia energética**: Reduce el consumo de recursos y energía

## ¿Qué debe incluir?

### Niveles de Caché

#### Browser Caching

- HTTP Cache Headers
- Service Workers
- Local Storage y Session Storage
- IndexedDB para datos complejos

#### Application-Level Caching

- In-memory caching (Redis, Memcached)
- Query result caching
- Computed values caching
- Session data caching

#### Database Caching

- Query result caching
- Connection pooling
- Prepared statement caching
- Buffer pool optimization

#### CDN Caching

- Static asset caching
- Edge server caching
- Geographic distribution
- Cache-Control headers optimization

### Estrategias de Invalidación

#### Time-Based Invalidation

- TTL (Time To Live)
- Sliding expiration
- Absolute expiration
- Scheduled invalidation

#### Event-Based Invalidation

- Cache tagging
- Dependency tracking
- Cascade invalidation
- Real-time invalidation

## ¿Qué debo hacer?

### 1. Implementar Browser Caching Strategy

#### HTTP Cache Headers Configuration

```nginx
# nginx.conf - Configuración optimizada de cache headers

server {
    listen 80;
    server_name example.com;

    # Cache estático de larga duración
    location ~* \.(css|js|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        add_header Vary "Accept-Encoding";

        # Compression
        gzip on;
        gzip_vary on;
        gzip_types
            text/css
            application/javascript
            application/json
            image/svg+xml;
    }

    # Cache de media duración para HTML
    location ~* \.(html|htm)$ {
        expires 1h;
        add_header Cache-Control "public, must-revalidate";
        add_header Vary "Accept-Encoding";
    }

    # No cache para API endpoints
    location /api/ {
        add_header Cache-Control "no-cache, no-store, must-revalidate";
        add_header Pragma "no-cache";
        add_header Expires "0";
    }

    # Cache especial para manifest y service worker
    location ~* \.(manifest|sw)\.js$ {
        expires 0;
        add_header Cache-Control "no-cache, no-store, must-revalidate";
    }

    # Cache intermedio para assets versionados
    location ~* \.(css|js)\?v= {
        expires 1M;
        add_header Cache-Control "public";
    }
}
```

#### Advanced Service Worker Implementation

```typescript
// public/sw.js - Service Worker avanzado con múltiples estrategias

const CACHE_VERSION = "v1.2.0";
const STATIC_CACHE = `static-${CACHE_VERSION}`;
const DYNAMIC_CACHE = `dynamic-${CACHE_VERSION}`;
const API_CACHE = `api-${CACHE_VERSION}`;
const IMAGE_CACHE = `images-${CACHE_VERSION}`;

const STATIC_ASSETS = [
  "/",
  "/offline.html",
  "/manifest.json",
  "/css/critical.css",
  "/js/app.min.js",
  "/fonts/inter-var.woff2",
];

// Configuración de estrategias por tipo de recurso
const CACHE_STRATEGIES = {
  // Documentos HTML - Network First con fallback
  documents: {
    strategy: "networkFirst",
    cache: DYNAMIC_CACHE,
    fallback: "/offline.html",
    maxAge: 300, // 5 minutos
  },

  // APIs - Network First con cache temporal
  api: {
    strategy: "networkFirst",
    cache: API_CACHE,
    maxAge: 300, // 5 minutos
    maxEntries: 100,
  },

  // Imágenes - Cache First con compresión
  images: {
    strategy: "cacheFirst",
    cache: IMAGE_CACHE,
    maxAge: 2592000, // 30 días
    maxEntries: 200,
  },

  // Assets estáticos - Cache First de larga duración
  static: {
    strategy: "cacheFirst",
    cache: STATIC_CACHE,
    maxAge: 31536000, // 1 año
  },

  // Scripts y CSS - Stale While Revalidate
  scripts: {
    strategy: "staleWhileRevalidate",
    cache: DYNAMIC_CACHE,
    maxAge: 86400, // 1 día
  },
};

self.addEventListener("install", (event) => {
  console.log("SW: Installing...");

  event.waitUntil(
    caches
      .open(STATIC_CACHE)
      .then((cache) => {
        console.log("SW: Caching static assets");
        return cache.addAll(STATIC_ASSETS);
      })
      .then(() => {
        console.log("SW: Static assets cached");
        return self.skipWaiting();
      })
  );
});

self.addEventListener("activate", (event) => {
  console.log("SW: Activating...");

  event.waitUntil(
    Promise.all([
      // Limpiar caches antiguos
      caches.keys().then((cacheNames) => {
        return Promise.all(
          cacheNames.map((cacheName) => {
            if (!cacheName.includes(CACHE_VERSION)) {
              console.log("SW: Deleting old cache:", cacheName);
              return caches.delete(cacheName);
            }
          })
        );
      }),
      // Tomar control de todos los clientes
      self.clients.claim(),
    ])
  );
});

self.addEventListener("fetch", (event) => {
  const { request } = event;
  const url = new URL(request.url);

  // Solo manejar requests GET
  if (request.method !== "GET") return;

  // Determinar tipo de recurso y estrategia
  const resourceType = getResourceType(url, request);
  const strategy = CACHE_STRATEGIES[resourceType];

  if (strategy) {
    event.respondWith(handleRequest(request, strategy));
  }
});

function getResourceType(url, request) {
  // API requests
  if (url.pathname.startsWith("/api/")) {
    return "api";
  }

  // Documentos HTML
  if (request.destination === "document") {
    return "documents";
  }

  // Imágenes
  if (request.destination === "image") {
    return "images";
  }

  // Scripts y CSS
  if (request.destination === "script" || request.destination === "style") {
    return "scripts";
  }

  // Assets estáticos
  if (
    /\.(css|js|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$/.test(url.pathname)
  ) {
    return "static";
  }

  return "documents"; // Default
}

async function handleRequest(request, strategy) {
  switch (strategy.strategy) {
    case "cacheFirst":
      return cacheFirst(request, strategy);
    case "networkFirst":
      return networkFirst(request, strategy);
    case "staleWhileRevalidate":
      return staleWhileRevalidate(request, strategy);
    default:
      return fetch(request);
  }
}

async function cacheFirst(request, strategy) {
  const cache = await caches.open(strategy.cache);
  const cached = await cache.match(request);

  if (cached && !isExpired(cached, strategy.maxAge)) {
    return cached;
  }

  try {
    const response = await fetch(request);

    if (response.ok) {
      // Clonar respuesta antes de cachear
      const responseClone = response.clone();

      // Añadir timestamp para control de expiración
      const responseWithTimestamp = new Response(responseClone.body, {
        status: responseClone.status,
        statusText: responseClone.statusText,
        headers: {
          ...responseClone.headers,
          "sw-cached-at": Date.now().toString(),
        },
      });

      cache.put(request, responseWithTimestamp);

      // Limpieza de cache si supera el límite
      if (strategy.maxEntries) {
        await cleanupCache(strategy.cache, strategy.maxEntries);
      }
    }

    return response;
  } catch (error) {
    // Retornar cache aunque esté expirado si hay error de red
    if (cached) {
      return cached;
    }
    throw error;
  }
}

async function networkFirst(request, strategy) {
  const cache = await caches.open(strategy.cache);

  try {
    const response = await fetch(request);

    if (response.ok) {
      const responseClone = response.clone();

      const responseWithTimestamp = new Response(responseClone.body, {
        status: responseClone.status,
        statusText: responseClone.statusText,
        headers: {
          ...responseClone.headers,
          "sw-cached-at": Date.now().toString(),
        },
      });

      cache.put(request, responseWithTimestamp);

      if (strategy.maxEntries) {
        await cleanupCache(strategy.cache, strategy.maxEntries);
      }
    }

    return response;
  } catch (error) {
    const cached = await cache.match(request);

    if (cached) {
      return cached;
    }

    // Fallback para documentos
    if (strategy.fallback) {
      return caches.match(strategy.fallback);
    }

    throw error;
  }
}

async function staleWhileRevalidate(request, strategy) {
  const cache = await caches.open(strategy.cache);
  const cached = await cache.match(request);

  // Fetch en background para actualizar cache
  const fetchPromise = fetch(request).then((response) => {
    if (response.ok) {
      const responseClone = response.clone();

      const responseWithTimestamp = new Response(responseClone.body, {
        status: responseClone.status,
        statusText: responseClone.statusText,
        headers: {
          ...responseClone.headers,
          "sw-cached-at": Date.now().toString(),
        },
      });

      cache.put(request, responseWithTimestamp);
    }
    return response;
  });

  // Retornar cache inmediatamente si existe, sino esperar network
  return cached || fetchPromise;
}

function isExpired(response, maxAge) {
  const cachedAt = response.headers.get("sw-cached-at");
  if (!cachedAt) return false;

  const age = Date.now() - parseInt(cachedAt);
  return age > maxAge * 1000;
}

async function cleanupCache(cacheName, maxEntries) {
  const cache = await caches.open(cacheName);
  const requests = await cache.keys();

  if (requests.length > maxEntries) {
    const requestsToDelete = requests.slice(0, requests.length - maxEntries);
    await Promise.all(requestsToDelete.map((request) => cache.delete(request)));
  }
}

// Eventos de background sync para actualización de cache
self.addEventListener("sync", (event) => {
  if (event.tag === "background-sync") {
    event.waitUntil(backgroundSync());
  }
});

async function backgroundSync() {
  // Actualizar caches críticos en background
  const criticalUrls = ["/api/user", "/api/config"];

  for (const url of criticalUrls) {
    try {
      await fetch(url);
    } catch (error) {
      console.log("Background sync failed for:", url);
    }
  }
}
```

### 2. Application-Level Caching con Redis

#### Redis Cache Service

```php
<?php
// app/Services/CacheService.php

namespace App\Services;

use Illuminate\Support\Facades\Redis;
use Illuminate\Support\Facades\Log;
use Carbon\Carbon;

class CacheService
{
    private $defaultTtl = 3600; // 1 hora
    private $tags = [];

    /**
     * Cache inteligente con múltiples estrategias
     */
    public function remember(string $key, callable $callback, ?int $ttl = null, array $tags = []): mixed
    {
        $ttl = $ttl ?? $this->defaultTtl;
        $this->tags = $tags;

        // Verificar si existe en cache
        $cached = $this->get($key);
        if ($cached !== null) {
            $this->recordHit($key);
            return $cached;
        }

        // Ejecutar callback y cachear resultado
        $value = $callback();
        $this->put($key, $value, $ttl, $tags);
        $this->recordMiss($key);

        return $value;
    }

    /**
     * Cache con refresh automático (Stale-While-Revalidate)
     */
    public function rememberWithRefresh(
        string $key,
        callable $callback,
        int $ttl = 3600,
        int $refreshThreshold = 300
    ): mixed {
        $cacheData = Redis::hgetall("cache:{$key}");

        if (!empty($cacheData)) {
            $cachedAt = (int) $cacheData['cached_at'];
            $data = unserialize($cacheData['data']);

            // Si está dentro del threshold, retornar inmediatamente
            if ((time() - $cachedAt) < ($ttl - $refreshThreshold)) {
                $this->recordHit($key);
                return $data;
            }

            // Si está en zona de refresh, retornar cache y actualizar en background
            if ((time() - $cachedAt) < $ttl) {
                $this->recordHit($key);

                // Actualizar en background
                dispatch(function() use ($key, $callback, $ttl) {
                    $newValue = $callback();
                    $this->put($key, $newValue, $ttl);
                });

                return $data;
            }
        }

        // Cache expirado o no existe, generar nuevo valor
        $value = $callback();
        $this->put($key, $value, $ttl);
        $this->recordMiss($key);

        return $value;
    }

    /**
     * Cache jerárquico con invalidación en cascada
     */
    public function rememberHierarchical(
        string $key,
        callable $callback,
        array $dependencies = [],
        int $ttl = 3600
    ): mixed {
        // Verificar dependencies
        foreach ($dependencies as $dependency) {
            if (!$this->exists($dependency)) {
                // Si una dependencia no existe, invalidar este cache
                $this->forget($key);
                break;
            }
        }

        return $this->remember($key, $callback, $ttl, $dependencies);
    }

    /**
     * Cache por lotes (Bulk Caching)
     */
    public function rememberMany(array $keyCallbackPairs, int $ttl = 3600): array
    {
        $results = [];
        $missing = [];

        // Verificar qué keys ya están en cache
        foreach ($keyCallbackPairs as $key => $callback) {
            $cached = $this->get($key);
            if ($cached !== null) {
                $results[$key] = $cached;
                $this->recordHit($key);
            } else {
                $missing[$key] = $callback;
            }
        }

        // Procesar los missing en paralelo si es posible
        if (!empty($missing)) {
            foreach ($missing as $key => $callback) {
                $value = $callback();
                $results[$key] = $value;
                $this->put($key, $value, $ttl);
                $this->recordMiss($key);
            }
        }

        return $results;
    }

    /**
     * Cache con versioning automático
     */
    public function rememberVersioned(
        string $key,
        callable $callback,
        int $ttl = 3600,
        string $version = null
    ): mixed {
        $version = $version ?? $this->getContentVersion($key);
        $versionedKey = "{$key}:v{$version}";

        return $this->remember($versionedKey, $callback, $ttl);
    }

    /**
     * Warm-up de cache
     */
    public function warmUp(array $cacheDefinitions): void
    {
        Log::info('Cache warm-up started', ['definitions_count' => count($cacheDefinitions)]);

        foreach ($cacheDefinitions as $definition) {
            try {
                $this->remember(
                    $definition['key'],
                    $definition['callback'],
                    $definition['ttl'] ?? $this->defaultTtl,
                    $definition['tags'] ?? []
                );

                Log::debug('Cache warmed up', ['key' => $definition['key']]);
            } catch (\Exception $e) {
                Log::error('Cache warm-up failed', [
                    'key' => $definition['key'],
                    'error' => $e->getMessage()
                ]);
            }
        }

        Log::info('Cache warm-up completed');
    }

    /**
     * Invalidación inteligente por tags
     */
    public function invalidateByTag(string $tag): int
    {
        $pattern = "tags:{$tag}:*";
        $keys = Redis::keys($pattern);

        if (empty($keys)) {
            return 0;
        }

        // Obtener las keys reales de cache
        $cacheKeys = [];
        foreach ($keys as $tagKey) {
            $cacheKey = Redis::get($tagKey);
            if ($cacheKey) {
                $cacheKeys[] = "cache:{$cacheKey}";
            }
        }

        // Eliminar cache keys y tag keys
        $pipeline = Redis::pipeline();
        foreach ($cacheKeys as $cacheKey) {
            $pipeline->del($cacheKey);
        }
        foreach ($keys as $tagKey) {
            $pipeline->del($tagKey);
        }
        $pipeline->execute();

        Log::info('Cache invalidated by tag', [
            'tag' => $tag,
            'keys_invalidated' => count($cacheKeys)
        ]);

        return count($cacheKeys);
    }

    /**
     * Operaciones básicas de cache
     */
    public function get(string $key): mixed
    {
        $cacheData = Redis::hgetall("cache:{$key}");

        if (empty($cacheData)) {
            return null;
        }

        $ttl = (int) $cacheData['ttl'];
        $cachedAt = (int) $cacheData['cached_at'];

        // Verificar expiración
        if ($ttl > 0 && (time() - $cachedAt) > $ttl) {
            $this->forget($key);
            return null;
        }

        return unserialize($cacheData['data']);
    }

    public function put(string $key, mixed $value, int $ttl = null, array $tags = []): void
    {
        $ttl = $ttl ?? $this->defaultTtl;

        $cacheData = [
            'data' => serialize($value),
            'cached_at' => time(),
            'ttl' => $ttl,
            'size' => strlen(serialize($value)),
        ];

        // Almacenar en Redis
        Redis::hmset("cache:{$key}", $cacheData);

        // Configurar expiración absoluta en Redis
        if ($ttl > 0) {
            Redis::expire("cache:{$key}", $ttl + 60); // +60s buffer
        }

        // Manejar tags
        foreach ($tags as $tag) {
            Redis::setex("tags:{$tag}:{$key}", $ttl + 60, $key);
        }

        // Estadísticas
        $this->updateStats($key, strlen(serialize($value)));
    }

    public function forget(string $key): bool
    {
        // Eliminar cache
        $deleted = Redis::del("cache:{$key}") > 0;

        // Limpiar tags asociadas
        $tagKeys = Redis::keys("tags:*:{$key}");
        if (!empty($tagKeys)) {
            Redis::del($tagKeys);
        }

        return $deleted;
    }

    public function exists(string $key): bool
    {
        return Redis::exists("cache:{$key}") > 0;
    }

    /**
     * Gestión de estadísticas
     */
    private function recordHit(string $key): void
    {
        Redis::hincrby('cache:stats', 'hits', 1);
        Redis::hincrby("cache:stats:keys:{$key}", 'hits', 1);
    }

    private function recordMiss(string $key): void
    {
        Redis::hincrby('cache:stats', 'misses', 1);
        Redis::hincrby("cache:stats:keys:{$key}", 'misses', 1);
    }

    private function updateStats(string $key, int $size): void
    {
        Redis::hset("cache:stats:keys:{$key}", 'size', $size);
        Redis::hset("cache:stats:keys:{$key}", 'updated_at', time());
    }

    public function getStats(): array
    {
        $stats = Redis::hgetall('cache:stats');

        $hitRate = 0;
        if (isset($stats['hits']) && isset($stats['misses'])) {
            $total = $stats['hits'] + $stats['misses'];
            $hitRate = $total > 0 ? ($stats['hits'] / $total) * 100 : 0;
        }

        return [
            'hits' => (int) ($stats['hits'] ?? 0),
            'misses' => (int) ($stats['misses'] ?? 0),
            'hit_rate' => round($hitRate, 2),
            'memory_usage' => $this->getMemoryUsage(),
            'key_count' => $this->getKeyCount(),
        ];
    }

    private function getMemoryUsage(): array
    {
        $info = Redis::info('memory');

        return [
            'used_memory' => $info['used_memory_human'] ?? 'N/A',
            'used_memory_peak' => $info['used_memory_peak_human'] ?? 'N/A',
            'memory_fragmentation_ratio' => $info['mem_fragmentation_ratio'] ?? 0,
        ];
    }

    private function getKeyCount(): int
    {
        return count(Redis::keys('cache:*'));
    }

    private function getContentVersion(string $key): string
    {
        // Generar versión basada en timestamp de archivos relevantes
        $relevantFiles = [
            app_path(),
            config_path(),
            database_path('migrations'),
        ];

        $timestamps = [];
        foreach ($relevantFiles as $path) {
            if (is_dir($path)) {
                $timestamps[] = $this->getDirectoryModificationTime($path);
            } elseif (file_exists($path)) {
                $timestamps[] = filemtime($path);
            }
        }

        return md5(implode('', $timestamps));
    }

    private function getDirectoryModificationTime(string $directory): int
    {
        $latestTime = 0;
        $iterator = new \RecursiveIteratorIterator(
            new \RecursiveDirectoryIterator($directory)
        );

        foreach ($iterator as $file) {
            if ($file->isFile()) {
                $latestTime = max($latestTime, $file->getMTime());
            }
        }

        return $latestTime;
    }
}
```

### 3. Database Query Caching

#### Eloquent Query Cache Macro

```php
<?php
// app/Providers/QueryCacheServiceProvider.php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Database\Eloquent\Builder;
use App\Services\CacheService;

class QueryCacheServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Macro para cache automático de queries
        Builder::macro('cached', function (int $ttl = 3600, array $tags = []) {
            $query = $this;
            $cacheService = app(CacheService::class);

            // Generar key única para la query
            $sql = $query->toSql();
            $bindings = $query->getBindings();
            $cacheKey = 'query:' . md5($sql . serialize($bindings));

            // Tags automáticos basados en tablas
            $tables = $this->extractTablesFromQuery($sql);
            $autoTags = array_map(fn($table) => "table:{$table}", $tables);
            $allTags = array_merge($tags, $autoTags);

            return $cacheService->remember(
                $cacheKey,
                fn() => $query->get(),
                $ttl,
                $allTags
            );
        });

        // Macro para cache con refresh automático
        Builder::macro('cachedWithRefresh', function (int $ttl = 3600, int $refreshThreshold = 300) {
            $query = $this;
            $cacheService = app(CacheService::class);

            $sql = $query->toSql();
            $bindings = $query->getBindings();
            $cacheKey = 'query:' . md5($sql . serialize($bindings));

            return $cacheService->rememberWithRefresh(
                $cacheKey,
                fn() => $query->get(),
                $ttl,
                $refreshThreshold
            );
        });

        // Macro para invalidación automática
        Builder::macro('invalidateCache', function (string $table = null) {
            $cacheService = app(CacheService::class);
            $table = $table ?? $this->getModel()->getTable();

            return $cacheService->invalidateByTag("table:{$table}");
        });
    }

    private function extractTablesFromQuery(string $sql): array
    {
        // Extraer nombres de tablas de la query SQL
        preg_match_all('/(?:from|join|into|update)\s+`?(\w+)`?/i', $sql, $matches);
        return array_unique($matches[1] ?? []);
    }
}
```

#### Model Cache Traits

```php
<?php
// app/Traits/CacheableModel.php

namespace App\Traits;

use App\Services\CacheService;

trait CacheableModel
{
    protected static $cacheService;

    public static function bootCacheableModel()
    {
        static::$cacheService = app(CacheService::class);

        // Invalidar cache automáticamente en eventos del modelo
        static::created(function ($model) {
            $model->invalidateModelCache();
        });

        static::updated(function ($model) {
            $model->invalidateModelCache();
        });

        static::deleted(function ($model) {
            $model->invalidateModelCache();
        });
    }

    /**
     * Cache de modelo individual
     */
    public static function findCached($id, int $ttl = 3600)
    {
        $cacheKey = static::class . ":find:{$id}";

        return static::$cacheService->remember(
            $cacheKey,
            fn() => static::find($id),
            $ttl,
            [static::getCacheTag()]
        );
    }

    /**
     * Cache de colección con parámetros
     */
    public static function getCached(array $params = [], int $ttl = 3600)
    {
        $cacheKey = static::class . ":collection:" . md5(serialize($params));

        return static::$cacheService->remember(
            $cacheKey,
            fn() => static::where($params)->get(),
            $ttl,
            [static::getCacheTag()]
        );
    }

    /**
     * Cache de conteo
     */
    public static function countCached(array $conditions = [], int $ttl = 3600): int
    {
        $cacheKey = static::class . ":count:" . md5(serialize($conditions));

        return static::$cacheService->remember(
            $cacheKey,
            fn() => static::where($conditions)->count(),
            $ttl,
            [static::getCacheTag()]
        );
    }

    /**
     * Cache de relaciones
     */
    public function loadCachedRelation(string $relation, int $ttl = 3600)
    {
        if ($this->relationLoaded($relation)) {
            return $this->getRelation($relation);
        }

        $cacheKey = static::class . ":{$this->getKey()}:relation:{$relation}";

        $relationData = static::$cacheService->remember(
            $cacheKey,
            fn() => $this->getRelationValue($relation),
            $ttl,
            [static::getCacheTag(), $this->getRelatedCacheTag($relation)]
        );

        $this->setRelation($relation, $relationData);
        return $relationData;
    }

    /**
     * Invalidar cache del modelo
     */
    public function invalidateModelCache(): void
    {
        $tags = [
            static::getCacheTag(),
            static::class . ":{$this->getKey()}",
        ];

        foreach ($tags as $tag) {
            static::$cacheService->invalidateByTag($tag);
        }
    }

    /**
     * Generar tag de cache para el modelo
     */
    protected static function getCacheTag(): string
    {
        return 'model:' . static::class;
    }

    /**
     * Generar tag de cache para relación
     */
    protected function getRelatedCacheTag(string $relation): string
    {
        $relatedModel = $this->$relation()->getRelated();
        return 'model:' . get_class($relatedModel);
    }

    /**
     * Warm-up de cache para el modelo
     */
    public static function warmUpCache(array $ids = [], array $relations = []): void
    {
        $cacheService = app(CacheService::class);

        // Warm-up de modelos individuales
        if (!empty($ids)) {
            $definitions = [];
            foreach ($ids as $id) {
                $definitions[] = [
                    'key' => static::class . ":find:{$id}",
                    'callback' => fn() => static::find($id),
                    'ttl' => 3600,
                    'tags' => [static::getCacheTag()],
                ];
            }
            $cacheService->warmUp($definitions);
        }

        // Warm-up de relaciones
        if (!empty($relations)) {
            $models = static::whereIn('id', $ids)->get();
            foreach ($models as $model) {
                foreach ($relations as $relation) {
                    $model->loadCachedRelation($relation);
                }
            }
        }
    }
}
```

### 4. CDN y Edge Caching

#### Cloudflare Workers para Cache Avanzado

```javascript
// cloudflare-worker.js - Worker avanzado para edge caching

addEventListener("fetch", (event) => {
  event.respondWith(handleRequest(event.request));
});

async function handleRequest(request) {
  const url = new URL(request.url);
  const cache = caches.default;

  // Configuración de cache por tipo de recurso
  const cacheConfig = getCacheConfig(url, request);

  if (!cacheConfig.cacheable) {
    return fetch(request);
  }

  // Generar cache key personalizada
  const cacheKey = generateCacheKey(request, cacheConfig);

  // Verificar cache
  let response = await cache.match(cacheKey);

  if (response) {
    // Cache hit - añadir headers de debug
    response = new Response(response.body, {
      status: response.status,
      statusText: response.statusText,
      headers: {
        ...response.headers,
        "X-Cache": "HIT",
        "X-Cache-Date": response.headers.get("Date"),
      },
    });

    return response;
  }

  // Cache miss - fetch from origin
  response = await fetch(request);

  if (response.ok) {
    // Aplicar optimizaciones antes de cachear
    response = await optimizeResponse(response, cacheConfig);

    // Configurar headers de cache
    const modifiedResponse = new Response(response.body, {
      status: response.status,
      statusText: response.statusText,
      headers: {
        ...response.headers,
        "Cache-Control": cacheConfig.cacheControl,
        "X-Cache": "MISS",
        "X-Cache-Date": new Date().toISOString(),
        Vary: cacheConfig.vary,
      },
    });

    // Cachear respuesta
    event.waitUntil(cache.put(cacheKey, modifiedResponse.clone()));

    return modifiedResponse;
  }

  return response;
}

function getCacheConfig(url, request) {
  const pathname = url.pathname;
  const fileExtension = pathname.split(".").pop()?.toLowerCase();

  // Configuraciones específicas por tipo
  const configs = {
    // Imágenes
    images: {
      test: () =>
        ["jpg", "jpeg", "png", "gif", "webp", "avif", "svg"].includes(
          fileExtension
        ),
      cacheable: true,
      cacheControl: "public, max-age=31536000, immutable",
      vary: "Accept",
      optimize: true,
    },

    // CSS y JavaScript
    assets: {
      test: () => ["css", "js"].includes(fileExtension),
      cacheable: true,
      cacheControl: "public, max-age=31536000, immutable",
      vary: "Accept-Encoding",
      optimize: true,
    },

    // Fuentes
    fonts: {
      test: () =>
        ["woff", "woff2", "ttf", "otf", "eot"].includes(fileExtension),
      cacheable: true,
      cacheControl: "public, max-age=31536000, immutable",
      vary: "",
      optimize: false,
    },

    // HTML
    html: {
      test: () => request.headers.get("Accept")?.includes("text/html"),
      cacheable: true,
      cacheControl: "public, max-age=300, must-revalidate",
      vary: "Accept-Encoding, Cookie",
      optimize: true,
    },

    // API
    api: {
      test: () => pathname.startsWith("/api/"),
      cacheable: pathname.includes("/public/") || request.method === "GET",
      cacheControl: "public, max-age=60",
      vary: "Accept, Authorization",
      optimize: false,
    },

    // Default
    default: {
      test: () => true,
      cacheable: false,
      cacheControl: "no-cache",
      vary: "",
      optimize: false,
    },
  };

  // Encontrar configuración aplicable
  for (const [type, config] of Object.entries(configs)) {
    if (config.test()) {
      return { ...config, type };
    }
  }

  return configs.default;
}

function generateCacheKey(request, config) {
  const url = new URL(request.url);

  // Base key
  let cacheKey = url.pathname + url.search;

  // Añadir headers relevantes para la variación
  if (config.vary) {
    const varyHeaders = config.vary.split(",").map((h) => h.trim());
    const varyValues = varyHeaders
      .map((header) => {
        const value = request.headers.get(header);
        return value ? `${header}:${value}` : "";
      })
      .filter(Boolean);

    if (varyValues.length > 0) {
      cacheKey += "|" + varyValues.join("|");
    }
  }

  // Normalizar dispositivo para responsive images
  if (config.type === "images") {
    const userAgent = request.headers.get("User-Agent") || "";
    const deviceType = getDeviceType(userAgent);
    cacheKey += `|device:${deviceType}`;
  }

  return new Request(cacheKey);
}

async function optimizeResponse(response, config) {
  if (!config.optimize) {
    return response;
  }

  let body = response.body;
  let headers = { ...response.headers };

  // Optimizaciones específicas por tipo
  switch (config.type) {
    case "images":
      // Optimización de imágenes
      body = await optimizeImage(body, response.headers);
      break;

    case "html":
      // Minificación de HTML
      const html = await response.text();
      body = minifyHTML(html);
      headers["Content-Length"] = body.length.toString();
      break;

    case "assets":
      // Compresión adicional para CSS/JS
      if (!headers["Content-Encoding"]) {
        body = await compressContent(body);
        headers["Content-Encoding"] = "gzip";
      }
      break;
  }

  return new Response(body, {
    status: response.status,
    statusText: response.statusText,
    headers,
  });
}

async function optimizeImage(body, headers) {
  // Implementar optimización de imágenes usando CF Image Resizing
  // Esta es una implementación simplificada
  return body;
}

function minifyHTML(html) {
  return html.replace(/>\s+</g, "><").replace(/\s+/g, " ").trim();
}

async function compressContent(body) {
  // Implementar compresión gzip
  // Esta es una implementación simplificada
  return body;
}

function getDeviceType(userAgent) {
  if (/Mobile|Android|iPhone/i.test(userAgent)) {
    return "mobile";
  }
  if (/Tablet|iPad/i.test(userAgent)) {
    return "tablet";
  }
  return "desktop";
}
```

### 5. Cache Monitoring y Analytics

#### Cache Dashboard y Métricas

```php
<?php
// app/Http/Controllers/CacheDashboardController.php

namespace App\Http\Controllers;

use App\Services\CacheService;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Redis;

class CacheDashboardController extends Controller
{
    private $cacheService;

    public function __construct(CacheService $cacheService)
    {
        $this->cacheService = $cacheService;
    }

    public function index()
    {
        $stats = $this->cacheService->getStats();
        $topKeys = $this->getTopKeys();
        $memoryDistribution = $this->getMemoryDistribution();
        $hitRateHistory = $this->getHitRateHistory();

        return view('admin.cache-dashboard', compact(
            'stats',
            'topKeys',
            'memoryDistribution',
            'hitRateHistory'
        ));
    }

    public function apiMetrics()
    {
        return response()->json([
            'timestamp' => now(),
            'stats' => $this->cacheService->getStats(),
            'memory' => $this->getDetailedMemoryStats(),
            'top_keys' => $this->getTopKeys(10),
            'alerts' => $this->getPerformanceAlerts(),
        ]);
    }

    public function flushCache(Request $request)
    {
        $tag = $request->input('tag');

        if ($tag) {
            $cleared = $this->cacheService->invalidateByTag($tag);
            return response()->json([
                'message' => "Cache cleared for tag: {$tag}",
                'keys_cleared' => $cleared,
            ]);
        }

        Redis::flushall();
        return response()->json(['message' => 'All cache cleared']);
    }

    public function warmUpCache()
    {
        $definitions = [
            // Datos críticos del sistema
            [
                'key' => 'system:config',
                'callback' => fn() => config('app'),
                'ttl' => 3600,
                'tags' => ['config'],
            ],

            // Usuarios activos
            [
                'key' => 'users:active',
                'callback' => fn() => \App\Models\User::where('last_login_at', '>', now()->subDays(7))->get(),
                'ttl' => 300,
                'tags' => ['users'],
            ],

            // Productos populares
            [
                'key' => 'products:popular',
                'callback' => fn() => \App\Models\Product::withCount('orders')
                    ->orderBy('orders_count', 'desc')
                    ->limit(20)
                    ->get(),
                'ttl' => 1800,
                'tags' => ['products'],
            ],
        ];

        $this->cacheService->warmUp($definitions);

        return response()->json(['message' => 'Cache warm-up completed']);
    }

    private function getTopKeys(int $limit = 20): array
    {
        $keys = Redis::keys('cache:stats:keys:*');
        $keyStats = [];

        foreach ($keys as $key) {
            $stats = Redis::hgetall($key);
            if (!empty($stats)) {
                $keyName = str_replace('cache:stats:keys:', '', $key);
                $keyStats[] = [
                    'key' => $keyName,
                    'hits' => (int) ($stats['hits'] ?? 0),
                    'misses' => (int) ($stats['misses'] ?? 0),
                    'size' => (int) ($stats['size'] ?? 0),
                    'updated_at' => isset($stats['updated_at']) ?
                        date('Y-m-d H:i:s', $stats['updated_at']) : null,
                ];
            }
        }

        // Ordenar por hits
        usort($keyStats, fn($a, $b) => $b['hits'] - $a['hits']);

        return array_slice($keyStats, 0, $limit);
    }

    private function getMemoryDistribution(): array
    {
        $keys = Redis::keys('cache:*');
        $distribution = [
            'small' => 0,   // < 1KB
            'medium' => 0,  // 1KB - 100KB
            'large' => 0,   // 100KB - 1MB
            'xlarge' => 0,  // > 1MB
        ];

        foreach ($keys as $key) {
            $size = strlen(Redis::get($key) ?? '');

            if ($size < 1024) {
                $distribution['small']++;
            } elseif ($size < 102400) {
                $distribution['medium']++;
            } elseif ($size < 1048576) {
                $distribution['large']++;
            } else {
                $distribution['xlarge']++;
            }
        }

        return $distribution;
    }

    private function getHitRateHistory(): array
    {
        // Obtener historial de hit rate de las últimas 24 horas
        $history = [];

        for ($i = 23; $i >= 0; $i--) {
            $hour = now()->subHours($i);
            $key = 'cache:stats:hourly:' . $hour->format('Y-m-d:H');

            $hourStats = Redis::hgetall($key);

            $hits = (int) ($hourStats['hits'] ?? 0);
            $misses = (int) ($hourStats['misses'] ?? 0);
            $total = $hits + $misses;

            $history[] = [
                'hour' => $hour->format('H:i'),
                'hit_rate' => $total > 0 ? round(($hits / $total) * 100, 2) : 0,
                'requests' => $total,
            ];
        }

        return $history;
    }

    private function getDetailedMemoryStats(): array
    {
        $info = Redis::info();

        return [
            'used_memory' => $info['used_memory'] ?? 0,
            'used_memory_human' => $info['used_memory_human'] ?? 'N/A',
            'used_memory_peak' => $info['used_memory_peak'] ?? 0,
            'used_memory_peak_human' => $info['used_memory_peak_human'] ?? 'N/A',
            'memory_fragmentation_ratio' => $info['mem_fragmentation_ratio'] ?? 0,
            'total_system_memory' => $info['total_system_memory'] ?? 0,
            'total_system_memory_human' => $info['total_system_memory_human'] ?? 'N/A',
        ];
    }

    private function getPerformanceAlerts(): array
    {
        $alerts = [];
        $stats = $this->cacheService->getStats();

        // Alert por hit rate bajo
        if ($stats['hit_rate'] < 70) {
            $alerts[] = [
                'type' => 'warning',
                'message' => "Low cache hit rate: {$stats['hit_rate']}%",
                'recommendation' => 'Review cache TTL settings and usage patterns',
            ];
        }

        // Alert por uso de memoria
        $memInfo = $this->getDetailedMemoryStats();
        if ($memInfo['memory_fragmentation_ratio'] > 1.5) {
            $alerts[] = [
                'type' => 'warning',
                'message' => "High memory fragmentation: {$memInfo['memory_fragmentation_ratio']}",
                'recommendation' => 'Consider Redis restart during low traffic period',
            ];
        }

        // Alert por número de keys
        $keyCount = $stats['key_count'];
        if ($keyCount > 100000) {
            $alerts[] = [
                'type' => 'info',
                'message' => "High number of cache keys: {$keyCount}",
                'recommendation' => 'Review cache cleanup policies',
            ];
        }

        return $alerts;
    }
}
```

## Tips

- **Múltiples niveles**: Implementa cache en browser, aplicación y base de datos
- **Invalidación inteligente**: Usa tags y dependencias para invalidación
  precisa
- **Métricas continuas**: Monitorea hit rates, tamaños y performance
- **TTL estratégico**: Ajusta TTL según patrones de uso de datos
- **Warm-up automático**: Pre-carga cache después de deploys
- **Compression**: Comprime datos grandes antes de cachear
- **Fallback gracioso**: Siempre ten un plan B si el cache falla
- **Testing de cache**: Incluye tests para verificar comportamiento de cache
- **Documentation**: Documenta estrategias y configuraciones de cache

## Ejemplos

### Cache Configuration Environment

```bash
# .env - Configuración de cache por ambiente

# Redis Configuration
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
REDIS_DB=0

# Cache Configuration
CACHE_DRIVER=redis
CACHE_PREFIX=myapp
CACHE_DEFAULT_TTL=3600

# Cache Strategies
CACHE_QUERY_TTL=1800
CACHE_MODEL_TTL=3600
CACHE_API_TTL=300
CACHE_STATIC_TTL=86400

# Performance Settings
CACHE_MAX_MEMORY=512mb
CACHE_EVICTION_POLICY=allkeys-lru
CACHE_COMPRESSION=true
CACHE_SERIALIZATION=igbinary
```

### Automated Cache Management

```bash
#!/bin/bash
# scripts/cache-management.sh

echo "🗂️ Cache Management Script"
echo "========================="

# Función para estadísticas de cache
show_cache_stats() {
    echo "📊 Cache Statistics:"
    redis-cli info memory | grep used_memory
    redis-cli info stats | grep keyspace

    echo -e "\n📈 Hit Rate:"
    php artisan tinker --execute="
        \$stats = app('App\Services\CacheService')->getStats();
        echo 'Hit Rate: ' . \$stats['hit_rate'] . '%' . PHP_EOL;
        echo 'Total Keys: ' . \$stats['key_count'] . PHP_EOL;
    "
}

# Función para limpiar cache expirado
cleanup_expired() {
    echo "🧹 Cleaning expired cache entries..."
    redis-cli --scan --pattern "cache:*" | while read key; do
        ttl=$(redis-cli ttl "$key")
        if [ "$ttl" = "-2" ]; then
            redis-cli del "$key"
            echo "Deleted expired key: $key"
        fi
    done
}

# Función para warm-up
warm_up_cache() {
    echo "🔥 Warming up cache..."
    php artisan cache:warm-up
    echo "Cache warm-up completed"
}

# Función para análisis de memoria
analyze_memory_usage() {
    echo "🧠 Memory Usage Analysis:"
    redis-cli memory usage cache:* | head -20

    echo -e "\n📋 Top memory consuming keys:"
    redis-cli --scan --pattern "cache:*" | while read key; do
        size=$(redis-cli memory usage "$key")
        echo "$size $key"
    done | sort -nr | head -10
}

# Menú principal
case "${1:-help}" in
    "stats")
        show_cache_stats
        ;;
    "cleanup")
        cleanup_expired
        ;;
    "warmup")
        warm_up_cache
        ;;
    "analyze")
        analyze_memory_usage
        ;;
    "all")
        show_cache_stats
        cleanup_expired
        warm_up_cache
        ;;
    *)
        echo "Usage: $0 {stats|cleanup|warmup|analyze|all}"
        echo ""
        echo "Commands:"
        echo "  stats   - Show cache statistics"
        echo "  cleanup - Clean expired cache entries"
        echo "  warmup  - Warm up critical cache"
        echo "  analyze - Analyze memory usage"
        echo "  all     - Run all operations"
        ;;
esac
```

## Navegación

[⬅️ Optimización del Frontend](./optimizacion-frontend.md) |
[🏠 README Principal](../../README.md) |
[Checklist de Revisión de Base de Datos ➡️](./checklist-revision-base-datos.md)
