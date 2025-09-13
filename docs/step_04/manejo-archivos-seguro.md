# Manejo Seguro de Archivos

## ¿Qué es?

El manejo seguro de archivos es el conjunto de prácticas, técnicas y controles de
seguridad que se implementan para proteger la carga, almacenamiento, procesamiento
y descarga de archivos en aplicaciones web. Incluye validación de tipos, control
de acceso, sanitización de nombres, protección contra malware y prevención de
vulnerabilidades como path traversal y ejecución de código malicioso.

## ¿Por qué es importante?

- **Prevención de malware**: Evita la subida de archivos maliciosos que puedan
  comprometer el servidor
- **Control de acceso**: Garantiza que solo usuarios autorizados puedan acceder
  a archivos específicos
- **Prevención de path traversal**: Impide el acceso a archivos del sistema
  fuera del directorio permitido
- **Integridad del sistema**: Protege contra la ejecución de código no autorizado
- **Cumplimiento normativo**: Asegura el cumplimiento de regulaciones de
  protección de datos
- **Gestión de recursos**: Controla el uso de espacio en disco y ancho de banda
- **Experiencia de usuario**: Proporciona feedback claro sobre el estado de las
  cargas

## ¿Qué debe incluir?

### Componentes de Seguridad

#### 1. Validación de Archivos
- Verificación de tipo MIME real
- Validación de extensiones permitidas
- Límites de tamaño de archivo
- Verificación de integridad

#### 2. Sanitización
- Nombres de archivo seguros
- Eliminación de metadatos peligrosos
- Normalización de rutas
- Limpieza de contenido

#### 3. Almacenamiento Seguro
- Directorios privados
- Estructura de carpetas organizadas
- Backup y versionado
- Cifrado de archivos sensibles

#### 4. Control de Acceso
- Autenticación para descarga
- Permisos granulares
- Logging de accesos
- Rate limiting

## ¿Qué debo hacer?

### 1. Implementar Validación de Archivos Backend

#### Service de Validación de Archivos

```php
<?php
// app/Application/Services/FileSecurityService.php

namespace App\Application\Services;

use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Str;

class FileSecurityService
{
    private const ALLOWED_MIME_TYPES = [
        'image/jpeg' => ['jpg', 'jpeg'],
        'image/png' => ['png'],
        'image/gif' => ['gif'],
        'image/webp' => ['webp'],
        'application/pdf' => ['pdf'],
        'application/msword' => ['doc'],
        'application/vnd.openxmlformats-officedocument.wordprocessingml.document' => ['docx'],
        'application/vnd.ms-excel' => ['xls'],
        'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' => ['xlsx'],
        'text/plain' => ['txt'],
        'text/csv' => ['csv']
    ];

    private const MAX_FILE_SIZE = 10 * 1024 * 1024; // 10MB
    private const MAX_FILES_PER_USER = 100;

    public function validateFile(UploadedFile $file, array $options = []): array
    {
        $errors = [];
        $warnings = [];

        // Opciones personalizables
        $maxSize = $options['max_size'] ?? self::MAX_FILE_SIZE;
        $allowedTypes = $options['allowed_types'] ?? array_keys(self::ALLOWED_MIME_TYPES);
        $allowedExtensions = $options['allowed_extensions'] ?? $this->getAllowedExtensions($allowedTypes);

        // 1. Verificar que el archivo existe y se subió correctamente
        if (!$file->isValid()) {
            $errors[] = 'Error en la carga del archivo: ' . $file->getErrorMessage();
            return ['errors' => $errors, 'warnings' => $warnings];
        }

        // 2. Verificar tamaño
        if ($file->getSize() > $maxSize) {
            $maxSizeMB = round($maxSize / (1024 * 1024), 2);
            $errors[] = "El archivo es demasiado grande. Tamaño máximo permitido: {$maxSizeMB}MB";
        }

        // 3. Verificar tipo MIME real (no confiar en la extensión)
        $actualMimeType = $this->getActualMimeType($file);
        if (!in_array($actualMimeType, $allowedTypes)) {
            $errors[] = "Tipo de archivo no permitido: {$actualMimeType}";
        }

        // 4. Verificar extensión
        $extension = strtolower($file->getClientOriginalExtension());
        if (!in_array($extension, $allowedExtensions)) {
            $errors[] = "Extensión de archivo no permitida: {$extension}";
        }

        // 5. Verificar consistencia entre MIME type y extensión
        if (!$this->isMimeTypeConsistentWithExtension($actualMimeType, $extension)) {
            $warnings[] = 'La extensión del archivo no coincide con su contenido real';
        }

        // 6. Verificar nombre de archivo
        $filenameErrors = $this->validateFilename($file->getClientOriginalName());
        $errors = array_merge($errors, $filenameErrors);

        // 7. Verificar contenido malicioso (para archivos de texto)
        if ($this->isTextFile($actualMimeType)) {
            $maliciousContent = $this->detectMaliciousContent($file);
            if ($maliciousContent) {
                $errors[] = 'El archivo contiene contenido potencialmente malicioso';
            }
        }

        // 8. Verificar archivos ejecutables
        if ($this->isExecutableFile($file)) {
            $errors[] = 'No se permiten archivos ejecutables';
        }

        return [
            'errors' => $errors,
            'warnings' => $warnings,
            'mime_type' => $actualMimeType,
            'extension' => $extension,
            'size' => $file->getSize()
        ];
    }

    public function sanitizeFilename(string $filename): string
    {
        // Obtener solo el nombre base (sin path)
        $filename = basename($filename);

        // Remover caracteres peligrosos
        $filename = preg_replace('/[^a-zA-Z0-9._-]/', '_', $filename);

        // Remover múltiples puntos consecutivos
        $filename = preg_replace('/\.{2,}/', '.', $filename);

        // Prevenir archivos ocultos
        $filename = ltrim($filename, '.');

        // Limitar longitud
        if (strlen($filename) > 255) {
            $extension = pathinfo($filename, PATHINFO_EXTENSION);
            $name = pathinfo($filename, PATHINFO_FILENAME);
            $name = substr($name, 0, 255 - strlen($extension) - 1);
            $filename = $name . '.' . $extension;
        }

        // Asegurar que no esté vacío
        if (empty($filename) || $filename === '.') {
            $filename = 'file_' . uniqid() . '.txt';
        }

        return $filename;
    }

    public function generateSecureFilename(UploadedFile $file): string
    {
        $extension = strtolower($file->getClientOriginalExtension());
        $hash = hash('sha256', $file->getClientOriginalName() . time() . random_bytes(16));
        
        return substr($hash, 0, 32) . '.' . $extension;
    }

    public function storeFileSecurely(UploadedFile $file, string $directory = 'uploads'): array
    {
        // Validar archivo
        $validation = $this->validateFile($file);
        if (!empty($validation['errors'])) {
            return [
                'success' => false,
                'errors' => $validation['errors'],
                'warnings' => $validation['warnings'] ?? []
            ];
        }

        try {
            // Generar nombre seguro
            $secureFilename = $this->generateSecureFilename($file);
            
            // Crear subdirectorio por fecha para organizar
            $dateDirectory = $directory . '/' . date('Y/m/d');
            
            // Almacenar archivo en directorio privado
            $path = $file->storeAs($dateDirectory, $secureFilename, 'private');

            // Crear registro en base de datos
            $fileRecord = $this->createFileRecord($file, $path, $secureFilename);

            return [
                'success' => true,
                'file_id' => $fileRecord->id,
                'original_name' => $file->getClientOriginalName(),
                'stored_name' => $secureFilename,
                'path' => $path,
                'size' => $file->getSize(),
                'mime_type' => $validation['mime_type'],
                'warnings' => $validation['warnings'] ?? []
            ];

        } catch (\Exception $e) {
            return [
                'success' => false,
                'errors' => ['Error al almacenar el archivo: ' . $e->getMessage()]
            ];
        }
    }

    private function getActualMimeType(UploadedFile $file): string
    {
        // Usar finfo para obtener el tipo MIME real
        $finfo = finfo_open(FILEINFO_MIME_TYPE);
        $mimeType = finfo_file($finfo, $file->getPathname());
        finfo_close($finfo);

        return $mimeType ?: $file->getMimeType();
    }

    private function isMimeTypeConsistentWithExtension(string $mimeType, string $extension): bool
    {
        return isset(self::ALLOWED_MIME_TYPES[$mimeType]) && 
               in_array($extension, self::ALLOWED_MIME_TYPES[$mimeType]);
    }

    private function validateFilename(string $filename): array
    {
        $errors = [];

        // Verificar caracteres peligrosos
        if (preg_match('/[<>:"|?*\\\\\/]/', $filename)) {
            $errors[] = 'El nombre del archivo contiene caracteres no válidos';
        }

        // Verificar nombres reservados (Windows)
        $reservedNames = ['CON', 'PRN', 'AUX', 'NUL', 'COM1', 'COM2', 'COM3', 'COM4', 
                         'COM5', 'COM6', 'COM7', 'COM8', 'COM9', 'LPT1', 'LPT2', 
                         'LPT3', 'LPT4', 'LPT5', 'LPT6', 'LPT7', 'LPT8', 'LPT9'];
        
        $nameWithoutExtension = pathinfo($filename, PATHINFO_FILENAME);
        if (in_array(strtoupper($nameWithoutExtension), $reservedNames)) {
            $errors[] = 'El nombre del archivo está reservado por el sistema';
        }

        // Verificar que no sea solo espacios o puntos
        if (trim($filename, '. ') === '') {
            $errors[] = 'El nombre del archivo no es válido';
        }

        return $errors;
    }

    private function detectMaliciousContent(UploadedFile $file): bool
    {
        $content = file_get_contents($file->getPathname());
        
        $maliciousPatterns = [
            '/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/i',
            '/javascript:/i',
            '/vbscript:/i',
            '/on\w+\s*=/i',
            '/<iframe/i',
            '/<object/i',
            '/<embed/i',
            '/eval\s*\(/i',
            '/system\s*\(/i',
            '/exec\s*\(/i',
            '/shell_exec\s*\(/i',
            '/passthru\s*\(/i',
            '/base64_decode\s*\(/i'
        ];

        foreach ($maliciousPatterns as $pattern) {
            if (preg_match($pattern, $content)) {
                return true;
            }
        }

        return false;
    }

    private function isTextFile(string $mimeType): bool
    {
        return strpos($mimeType, 'text/') === 0 || 
               in_array($mimeType, ['application/json', 'application/xml']);
    }

    private function isExecutableFile(UploadedFile $file): bool
    {
        $executableExtensions = [
            'exe', 'bat', 'cmd', 'com', 'pif', 'scr', 'vbs', 'js', 'jar', 
            'php', 'py', 'rb', 'pl', 'sh', 'bash', 'ps1', 'msi'
        ];
        
        $extension = strtolower($file->getClientOriginalExtension());
        return in_array($extension, $executableExtensions);
    }

    private function getAllowedExtensions(array $allowedMimeTypes): array
    {
        $extensions = [];
        foreach ($allowedMimeTypes as $mimeType) {
            if (isset(self::ALLOWED_MIME_TYPES[$mimeType])) {
                $extensions = array_merge($extensions, self::ALLOWED_MIME_TYPES[$mimeType]);
            }
        }
        return array_unique($extensions);
    }

    private function createFileRecord(UploadedFile $file, string $path, string $secureFilename): object
    {
        // Este método debería crear un registro en la base de datos
        // Implementar según el modelo de FileUpload
        return (object) [
            'id' => uniqid(),
            'original_name' => $file->getClientOriginalName(),
            'stored_name' => $secureFilename,
            'path' => $path,
            'size' => $file->getSize(),
            'mime_type' => $this->getActualMimeType($file),
            'user_id' => auth()->id(),
            'created_at' => now()
        ];
    }
}
```

#### Modelo de Archivo

```php
<?php
// app/Infrastructure/Persistence/Eloquent/Models/FileUploadModel.php

namespace App\Infrastructure\Persistence\Eloquent\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class FileUploadModel extends Model
{
    use SoftDeletes;

    protected $table = 'file_uploads';

    protected $fillable = [
        'id',
        'user_id',
        'original_name',
        'stored_name',
        'path',
        'size',
        'mime_type',
        'hash',
        'is_public',
        'download_count',
        'expires_at'
    ];

    protected $casts = [
        'is_public' => 'boolean',
        'size' => 'integer',
        'download_count' => 'integer',
        'expires_at' => 'datetime',
        'created_at' => 'datetime',
        'updated_at' => 'datetime',
        'deleted_at' => 'datetime'
    ];

    public $incrementing = false;
    protected $keyType = 'string';

    public function user(): BelongsTo
    {
        return $this->belongsTo(UserModel::class, 'user_id');
    }

    public function getHumanReadableSizeAttribute(): string
    {
        $bytes = $this->size;
        $units = ['B', 'KB', 'MB', 'GB', 'TB'];
        
        for ($i = 0; $bytes > 1024 && $i < count($units) - 1; $i++) {
            $bytes /= 1024;
        }
        
        return round($bytes, 2) . ' ' . $units[$i];
    }

    public function isExpired(): bool
    {
        return $this->expires_at && $this->expires_at->isPast();
    }

    public function canBeDownloadedBy($user): bool
    {
        // Archivo público o propietario
        if ($this->is_public || $this->user_id === $user->id) {
            return true;
        }

        // Verificar permisos específicos
        // Implementar lógica de permisos según necesidades
        return false;
    }
}
```

### 2. Controlador de Archivos Seguro

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V1/FileUploadController.php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\Services\FileSecurityService;
use App\Infrastructure\Http\Controllers\Controller;
use App\Infrastructure\Http\Requests\V1\FileUploadRequest;
use App\Infrastructure\Persistence\Eloquent\Models\FileUploadModel;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Log;

class FileUploadController extends Controller
{
    public function __construct(
        private FileSecurityService $fileSecurityService
    ) {}

    public function upload(FileUploadRequest $request): JsonResponse
    {
        try {
            $file = $request->file('file');
            
            // Validar límites por usuario
            $this->checkUserUploadLimits($request->user());
            
            // Almacenar archivo de forma segura
            $result = $this->fileSecurityService->storeFileSecurely($file);
            
            if (!$result['success']) {
                return response()->json([
                    'message' => 'Error al subir el archivo',
                    'errors' => $result['errors']
                ], 422);
            }

            // Log de auditoría
            Log::info('File uploaded', [
                'user_id' => $request->user()->id,
                'file_id' => $result['file_id'],
                'original_name' => $result['original_name'],
                'size' => $result['size'],
                'mime_type' => $result['mime_type'],
                'ip' => $request->ip()
            ]);

            return response()->json([
                'message' => 'Archivo subido exitosamente',
                'data' => [
                    'file_id' => $result['file_id'],
                    'original_name' => $result['original_name'],
                    'size' => $result['size'],
                    'mime_type' => $result['mime_type']
                ],
                'warnings' => $result['warnings'] ?? []
            ], 201);

        } catch (\Exception $e) {
            Log::error('File upload failed', [
                'user_id' => $request->user()->id,
                'error' => $e->getMessage(),
                'ip' => $request->ip()
            ]);

            return response()->json([
                'message' => 'Error interno del servidor',
                'error' => 'No se pudo procesar la carga del archivo'
            ], 500);
        }
    }

    public function download(Request $request, string $fileId): Response
    {
        try {
            $file = FileUploadModel::findOrFail($fileId);
            
            // Verificar permisos
            if (!$file->canBeDownloadedBy($request->user())) {
                Log::warning('Unauthorized file download attempt', [
                    'user_id' => $request->user()->id,
                    'file_id' => $fileId,
                    'ip' => $request->ip()
                ]);
                
                abort(403, 'No tienes permisos para descargar este archivo');
            }

            // Verificar si el archivo está expirado
            if ($file->isExpired()) {
                abort(410, 'El archivo ha expirado');
            }

            // Verificar que el archivo existe físicamente
            if (!Storage::disk('private')->exists($file->path)) {
                Log::error('File not found on disk', [
                    'file_id' => $fileId,
                    'path' => $file->path
                ]);
                
                abort(404, 'Archivo no encontrado');
            }

            // Incrementar contador de descarga
            $file->increment('download_count');

            // Log de auditoría
            Log::info('File downloaded', [
                'user_id' => $request->user()->id,
                'file_id' => $fileId,
                'original_name' => $file->original_name,
                'download_count' => $file->download_count,
                'ip' => $request->ip()
            ]);

            // Preparar headers de seguridad para descarga
            $headers = [
                'Content-Type' => $file->mime_type,
                'Content-Disposition' => 'attachment; filename="' . $file->original_name . '"',
                'Content-Length' => $file->size,
                'Cache-Control' => 'no-cache, no-store, must-revalidate',
                'Pragma' => 'no-cache',
                'Expires' => '0',
                'X-Content-Type-Options' => 'nosniff',
                'X-Frame-Options' => 'DENY'
            ];

            return Storage::disk('private')->response($file->path, $file->original_name, $headers);

        } catch (\Exception $e) {
            Log::error('File download failed', [
                'user_id' => $request->user()->id,
                'file_id' => $fileId,
                'error' => $e->getMessage(),
                'ip' => $request->ip()
            ]);

            abort(500, 'Error al descargar el archivo');
        }
    }

    public function delete(Request $request, string $fileId): JsonResponse
    {
        try {
            $file = FileUploadModel::findOrFail($fileId);
            
            // Verificar que el usuario es el propietario o tiene permisos
            if ($file->user_id !== $request->user()->id && !$request->user()->isAdmin()) {
                abort(403, 'No tienes permisos para eliminar este archivo');
            }

            // Eliminar archivo físico
            if (Storage::disk('private')->exists($file->path)) {
                Storage::disk('private')->delete($file->path);
            }

            // Soft delete en la base de datos
            $file->delete();

            // Log de auditoría
            Log::info('File deleted', [
                'user_id' => $request->user()->id,
                'file_id' => $fileId,
                'original_name' => $file->original_name,
                'ip' => $request->ip()
            ]);

            return response()->json([
                'message' => 'Archivo eliminado exitosamente'
            ]);

        } catch (\Exception $e) {
            Log::error('File deletion failed', [
                'user_id' => $request->user()->id,
                'file_id' => $fileId,
                'error' => $e->getMessage(),
                'ip' => $request->ip()
            ]);

            return response()->json([
                'message' => 'Error al eliminar el archivo'
            ], 500);
        }
    }

    public function list(Request $request): JsonResponse
    {
        $files = FileUploadModel::where('user_id', $request->user()->id)
            ->latest()
            ->paginate(20);

        return response()->json([
            'data' => $files->items(),
            'meta' => [
                'current_page' => $files->currentPage(),
                'last_page' => $files->lastPage(),
                'per_page' => $files->perPage(),
                'total' => $files->total()
            ]
        ]);
    }

    private function checkUserUploadLimits($user): void
    {
        $uploadCount = FileUploadModel::where('user_id', $user->id)->count();
        $maxUploads = $user->isAdmin() ? 1000 : 100;

        if ($uploadCount >= $maxUploads) {
            throw new \Exception('Has alcanzado el límite máximo de archivos subidos');
        }

        // Verificar límite diario
        $dailyUploads = FileUploadModel::where('user_id', $user->id)
            ->whereDate('created_at', today())
            ->count();
        
        $maxDailyUploads = $user->isAdmin() ? 200 : 20;

        if ($dailyUploads >= $maxDailyUploads) {
            throw new \Exception('Has alcanzado el límite diario de subidas');
        }
    }
}
```

### 3. Request de Validación

```php
<?php
// app/Infrastructure/Http/Requests/V1/FileUploadRequest.php

namespace App\Infrastructure\Http\Requests\V1;

use Illuminate\Foundation\Http\FormRequest;

class FileUploadRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true; // Autorización manejada por middleware
    }

    public function rules(): array
    {
        return [
            'file' => [
                'required',
                'file',
                'max:10240', // 10MB en KB
                'mimes:jpg,jpeg,png,gif,webp,pdf,doc,docx,xls,xlsx,txt,csv'
            ],
            'is_public' => 'boolean',
            'expires_at' => 'nullable|date|after:now'
        ];
    }

    public function messages(): array
    {
        return [
            'file.required' => 'Debe seleccionar un archivo',
            'file.file' => 'El archivo seleccionado no es válido',
            'file.max' => 'El archivo no puede ser mayor a 10MB',
            'file.mimes' => 'Tipo de archivo no permitido',
            'expires_at.after' => 'La fecha de expiración debe ser futura'
        ];
    }
}
```

### 4. Frontend - Componente de Subida Segura

```typescript
// src/components/FileUpload/SecureFileUpload.tsx
import React, { useState, useCallback } from 'react';
import { useDropzone } from 'react-dropzone';

interface FileUploadProps {
   onUploadSuccess?: (file: UploadedFile) => void;
   onUploadError?: (error: string) => void;
   maxSize?: number;
   allowedTypes?: string[];
   multiple?: boolean;
}

interface UploadedFile {
   id: string;
   originalName: string;
   size: number;
   mimeType: string;
}

const SecureFileUpload: React.FC<FileUploadProps> = ({
   onUploadSuccess,
   onUploadError,
   maxSize = 10 * 1024 * 1024, // 10MB
   allowedTypes = ['image/jpeg', 'image/png', 'image/gif', 'application/pdf'],
   multiple = false
}) => {
   const [uploading, setUploading] = useState(false);
   const [uploadProgress, setUploadProgress] = useState(0);

   const validateFile = (file: File): string[] => {
      const errors: string[] = [];

      // Verificar tamaño
      if (file.size > maxSize) {
         const maxSizeMB = Math.round(maxSize / (1024 * 1024));
         errors.push(`El archivo es demasiado grande. Tamaño máximo: ${maxSizeMB}MB`);
      }

      // Verificar tipo MIME
      if (!allowedTypes.includes(file.type)) {
         errors.push(`Tipo de archivo no permitido: ${file.type}`);
      }

      // Verificar extensión
      const allowedExtensions = ['jpg', 'jpeg', 'png', 'gif', 'pdf', 'doc', 'docx'];
      const extension = file.name.split('.').pop()?.toLowerCase();
      if (!extension || !allowedExtensions.includes(extension)) {
         errors.push(`Extensión no permitida: ${extension}`);
      }

      // Verificar nombre de archivo
      if (file.name.length > 255) {
         errors.push('El nombre del archivo es demasiado largo');
      }

      const dangerousChars = /[<>:"|?*\\\/]/;
      if (dangerousChars.test(file.name)) {
         errors.push('El nombre del archivo contiene caracteres no válidos');
      }

      // Verificar que no sea un archivo ejecutable
      const executableExtensions = ['exe', 'bat', 'cmd', 'scr', 'vbs', 'js', 'php', 'py'];
      if (extension && executableExtensions.includes(extension)) {
         errors.push('No se permiten archivos ejecutables');
      }

      return errors;
   };

   const uploadFile = async (file: File): Promise<void> => {
      setUploading(true);
      setUploadProgress(0);

      const formData = new FormData();
      formData.append('file', file);

      try {
         const response = await fetch('/api/v1/files/upload', {
            method: 'POST',
            headers: {
               'Authorization': `Bearer ${localStorage.getItem('auth_token')}`,
               'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]')?.getAttribute('content') || ''
            },
            body: formData
         });

         if (!response.ok) {
            const errorData = await response.json();
            throw new Error(errorData.message || 'Error al subir el archivo');
         }

         const result = await response.json();
         
         if (onUploadSuccess) {
            onUploadSuccess(result.data);
         }

         // Mostrar advertencias si las hay
         if (result.warnings && result.warnings.length > 0) {
            console.warn('Advertencias del archivo:', result.warnings);
         }

      } catch (error) {
         const errorMessage = error instanceof Error ? error.message : 'Error desconocido';
         if (onUploadError) {
            onUploadError(errorMessage);
         }
      } finally {
         setUploading(false);
         setUploadProgress(0);
      }
   };

   const onDrop = useCallback((acceptedFiles: File[], rejectedFiles: any[]) => {
      // Manejar archivos rechazados
      rejectedFiles.forEach(({ file, errors }) => {
         const errorMessages = errors.map((e: any) => e.message).join(', ');
         if (onUploadError) {
            onUploadError(`${file.name}: ${errorMessages}`);
         }
      });

      // Procesar archivos aceptados
      acceptedFiles.forEach(file => {
         const validationErrors = validateFile(file);
         if (validationErrors.length > 0) {
            if (onUploadError) {
               onUploadError(`${file.name}: ${validationErrors.join(', ')}`);
            }
            return;
         }

         uploadFile(file);
      });
   }, [onUploadSuccess, onUploadError, maxSize, allowedTypes]);

   const { getRootProps, getInputProps, isDragActive } = useDropzone({
      onDrop,
      multiple,
      maxSize,
      accept: allowedTypes.reduce((acc, type) => {
         acc[type] = [];
         return acc;
      }, {} as Record<string, string[]>)
   });

   return (
      <div className="secure-file-upload">
         <div
            {...getRootProps()}
            className={`dropzone ${isDragActive ? 'active' : ''} ${uploading ? 'uploading' : ''}`}
         >
            <input {...getInputProps()} />
            
            {uploading ? (
               <div className="upload-progress">
                  <div className="progress-bar">
                     <div 
                        className="progress-fill" 
                        style={{ width: `${uploadProgress}%` }}
                     />
                  </div>
                  <p>Subiendo archivo...</p>
               </div>
            ) : (
               <div className="upload-prompt">
                  {isDragActive ? (
                     <p>Suelta el archivo aquí...</p>
                  ) : (
                     <div>
                        <p>Arrastra un archivo aquí o haz clic para seleccionar</p>
                        <small>
                           Tipos permitidos: {allowedTypes.join(', ')}<br/>
                           Tamaño máximo: {Math.round(maxSize / (1024 * 1024))}MB
                        </small>
                     </div>
                  )}
               </div>
            )}
         </div>

         <div className="file-security-info">
            <h4>🔒 Información de Seguridad</h4>
            <ul>
               <li>✅ Los archivos se escanean en busca de contenido malicioso</li>
               <li>✅ Solo se permiten tipos de archivo seguros</li>
               <li>✅ Los archivos se almacenan de forma segura y encriptada</li>
               <li>✅ El acceso está protegido por autenticación</li>
            </ul>
         </div>
      </div>
   );
};

export default SecureFileUpload;
```

### 5. Middleware de Rate Limiting para Archivos

```php
<?php
// app/Infrastructure/Http/Middleware/FileUploadRateLimit.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\RateLimiter;

class FileUploadRateLimit
{
    public function handle(Request $request, Closure $next, string $limit = '10,1')
    {
        [$attempts, $decay] = explode(',', $limit);

        $key = 'file_upload:' . $request->user()->id . ':' . $request->ip();

        if (RateLimiter::tooManyAttempts($key, $attempts)) {
            $seconds = RateLimiter::availableIn($key);
            
            return response()->json([
                'message' => 'Demasiados intentos de subida. Intenta de nuevo en ' . $seconds . ' segundos.',
                'retry_after' => $seconds
            ], 429);
        }

        RateLimiter::hit($key, $decay * 60);

        return $next($request);
    }
}
```

### 6. Configuración de Almacenamiento Seguro

```php
<?php
// config/filesystems.php

return [
    'disks' => [
        'private' => [
            'driver' => 'local',
            'root' => storage_path('app/private'),
            'throw' => false,
        ],
        
        'secure_uploads' => [
            'driver' => 'local',
            'root' => storage_path('app/secure/uploads'),
            'url' => null, // No URL pública
            'visibility' => 'private',
            'throw' => false,
        ],
        
        // Para archivos que requieren cifrado
        'encrypted' => [
            'driver' => 'local',
            'root' => storage_path('app/encrypted'),
            'throw' => false,
        ]
    ]
];
```

### 7. Job para Escaneo de Virus

```php
<?php
// app/Application/Jobs/ScanFileForVirus.php

namespace App\Application\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Log;

class ScanFileForVirus implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(
        private string $fileId,
        private string $filePath
    ) {}

    public function handle(): void
    {
        try {
            $fullPath = Storage::disk('private')->path($this->filePath);
            
            // Integración con ClamAV u otro antivirus
            $result = $this->scanWithClamAV($fullPath);
            
            if ($result['infected']) {
                // Mover archivo a cuarentena
                $this->quarantineFile($fullPath);
                
                // Actualizar estado en base de datos
                $this->markFileAsInfected($this->fileId, $result['virus']);
                
                // Notificar al administrador
                Log::warning('Virus detected in uploaded file', [
                    'file_id' => $this->fileId,
                    'path' => $this->filePath,
                    'virus' => $result['virus']
                ]);
            } else {
                // Marcar como limpio
                $this->markFileAsClean($this->fileId);
            }

        } catch (\Exception $e) {
            Log::error('Virus scan failed', [
                'file_id' => $this->fileId,
                'error' => $e->getMessage()
            ]);
        }
    }

    private function scanWithClamAV(string $filePath): array
    {
        // Implementar integración con ClamAV
        $command = "clamscan --no-summary --infected {$filePath}";
        $output = shell_exec($command);
        
        if (strpos($output, 'FOUND') !== false) {
            $virus = trim(str_replace('FOUND', '', $output));
            return ['infected' => true, 'virus' => $virus];
        }
        
        return ['infected' => false, 'virus' => null];
    }

    private function quarantineFile(string $filePath): void
    {
        $quarantinePath = storage_path('app/quarantine/' . basename($filePath));
        rename($filePath, $quarantinePath);
    }

    private function markFileAsInfected(string $fileId, string $virus): void
    {
        // Actualizar en base de datos
        // FileUploadModel::where('id', $fileId)->update([
        //     'status' => 'infected',
        //     'virus_info' => $virus
        // ]);
    }

    private function markFileAsClean(string $fileId): void
    {
        // Actualizar en base de datos
        // FileUploadModel::where('id', $fileId)->update([
        //     'status' => 'clean',
        //     'scanned_at' => now()
        // ]);
    }
}
```

## Tips

### Mejores Prácticas de Seguridad

1. **Validación múltiple**:
   - Validar tanto en frontend como backend
   - No confiar solo en la extensión del archivo
   - Verificar MIME type real con finfo

2. **Almacenamiento seguro**:
   - Nunca almacenar en directorios públicos
   - Usar nombres de archivo aleatorios
   - Separar por fecha para organización

3. **Control de acceso**:
   - Autenticación obligatoria para descarga
   - Verificar permisos específicos
   - Logging de todas las operaciones

4. **Límites y cuotas**:
   - Establecer límites por usuario
   - Rate limiting para cargas
   - Límites de espacio en disco

### Configuración de Nginx para Archivos

```nginx
# Configuración Nginx para directorio de archivos
location /api/files/ {
    # Rate limiting específico para archivos
    limit_req zone=uploads burst=5 nodelay;
    
    # Tamaño máximo de archivo
    client_max_body_size 10M;
    
    # Timeouts para uploads
    client_body_timeout 60s;
    client_header_timeout 60s;
    
    proxy_pass http://backend;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}

# Bloquear acceso directo a archivos subidos
location /storage/app/private/ {
    deny all;
    return 404;
}
```

## Ejemplos

### Comando Artisan para Limpieza

```php
<?php
// app/Console/Commands/CleanupExpiredFiles.php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Infrastructure\Persistence\Eloquent\Models\FileUploadModel;
use Illuminate\Support\Facades\Storage;

class CleanupExpiredFiles extends Command
{
    protected $signature = 'files:cleanup {--force : Skip confirmation}';
    protected $description = 'Cleanup expired and orphaned files';

    public function handle(): void
    {
        $this->info('Iniciando limpieza de archivos...');

        // Archivos expirados
        $expiredFiles = FileUploadModel::where('expires_at', '<', now())->get();
        $this->info("Archivos expirados encontrados: {$expiredFiles->count()}");

        if ($expiredFiles->count() > 0 && ($this->option('force') || $this->confirm('¿Eliminar archivos expirados?'))) {
            foreach ($expiredFiles as $file) {
                if (Storage::disk('private')->exists($file->path)) {
                    Storage::disk('private')->delete($file->path);
                }
                $file->forceDelete();
            }
            $this->info('Archivos expirados eliminados');
        }

        // Archivos huérfanos (sin registro en BD)
        $this->cleanupOrphanedFiles();

        $this->info('Limpieza completada');
    }

    private function cleanupOrphanedFiles(): void
    {
        $allFiles = Storage::disk('private')->allFiles('uploads');
        $registeredFiles = FileUploadModel::pluck('path')->toArray();
        
        $orphanedFiles = array_diff($allFiles, $registeredFiles);
        
        if (count($orphanedFiles) > 0) {
            $this->info("Archivos huérfanos encontrados: " . count($orphanedFiles));
            
            if ($this->option('force') || $this->confirm('¿Eliminar archivos huérfanos?')) {
                foreach ($orphanedFiles as $file) {
                    Storage::disk('private')->delete($file);
                }
                $this->info('Archivos huérfanos eliminados');
            }
        }
    }
}
```

### Testing de Subida de Archivos

```php
<?php
// tests/Feature/FileUploadTest.php

namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;

class FileUploadTest extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();
        Storage::fake('private');
    }

    /** @test */
    public function user_can_upload_valid_file()
    {
        $user = UserModel::factory()->create();
        $file = UploadedFile::fake()->image('test.jpg', 1000, 1000);

        $response = $this->actingAs($user)
            ->postJson('/api/v1/files/upload', [
                'file' => $file
            ]);

        $response->assertStatus(201)
                ->assertJsonStructure([
                    'data' => ['file_id', 'original_name', 'size', 'mime_type']
                ]);

        Storage::disk('private')->assertExists($response->json('data.path'));
    }

    /** @test */
    public function upload_rejects_malicious_file()
    {
        $user = UserModel::factory()->create();
        
        // Crear archivo con contenido malicioso
        $maliciousContent = '<?php system($_GET["cmd"]); ?>';
        $file = UploadedFile::fake()->createWithContent('malicious.php', $maliciousContent);

        $response = $this->actingAs($user)
            ->postJson('/api/v1/files/upload', [
                'file' => $file
            ]);

        $response->assertStatus(422);
    }

    /** @test */
    public function upload_respects_size_limits()
    {
        $user = UserModel::factory()->create();
        $file = UploadedFile::fake()->create('large.pdf', 15000); // 15MB

        $response = $this->actingAs($user)
            ->postJson('/api/v1/files/upload', [
                'file' => $file
            ]);

        $response->assertStatus(422)
                ->assertJsonValidationErrors(['file']);
    }
}
```

## Navegación

[⬅️ Validaciones de Seguridad](./validaciones-seguridad.md) |
[🏠 README Principal](../../README.md) |
[Autenticación Laravel Sanctum ➡️](./autenticacion-laravel-sanctum.md)
