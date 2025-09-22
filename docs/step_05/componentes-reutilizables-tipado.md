# Componentes Reutilizables con Tipado

## Conocimientos involucrados
- React
- TypeScript
- Diseño modular
- Principios de reutilización

## Responsable
- Equipo de desarrollo frontend

## ¿Qué es?

Los componentes reutilizables con tipado son piezas de interfaz de usuario
desarrolladas en React con TypeScript que pueden ser utilizadas en múltiples
partes de la aplicación. Estos componentes están diseñados con interfaces
TypeScript claras, props bien definidas y un diseño modular que facilita su
mantenimiento y escalabilidad.

## ¿Por qué es importante?

- **Consistencia UI**: Garantiza una experiencia de usuario uniforme en toda la
  aplicación
- **Mantenibilidad**: Centraliza el código de interfaz, facilitando
  actualizaciones y correcciones
- **Reutilización**: Reduce la duplicación de código y acelera el desarrollo
- **Tipado fuerte**: Previene errores en tiempo de compilación y mejora la
  experiencia de desarrollo
- **Testing simplificado**: Componentes aislados son más fáciles de testear
- **Documentación viva**: Las interfaces TypeScript sirven como documentación
  del componente

## ¿Qué debe incluir?

### Categorías de Componentes

```
components/
├── common/           # Componentes genéricos básicos
│   ├── Button/       # Botones con variantes
│   ├── Input/        # Campos de entrada
│   ├── Modal/        # Modales y diálogos
│   ├── Loading/      # Indicadores de carga
│   └── Badge/        # Etiquetas y badges
├── forms/            # Componentes específicos de formularios
│   ├── LoginForm/    # Formulario de login
│   ├── UserForm/     # Formulario de usuario
│   └── SearchForm/   # Formulario de búsqueda
├── layout/           # Componentes de estructura
│   ├── Header/       # Cabecera de la aplicación
│   ├── Footer/       # Pie de página
│   ├── Sidebar/      # Barra lateral
│   └── Container/    # Contenedores de layout
└── business/         # Componentes específicos del negocio
    ├── UserCard/     # Tarjeta de usuario
    ├── ProductList/  # Lista de productos
    └── Dashboard/    # Componentes del dashboard
```

### Estructura de Componente

```
ComponentName/
├── ComponentName.tsx      # Componente principal
├── ComponentName.test.tsx # Tests del componente
├── ComponentName.scss     # Estilos específicos (opcional)
├── index.ts              # Export barrel
└── types.ts              # Tipos específicos (opcional)
```

## ¿Qué debo hacer?

### 1. Definir Interfaces TypeScript

```typescript
// src/types/common.ts
export interface BaseComponentProps {
   className?: string;
   children?: React.ReactNode;
   testId?: string;
}

export type Size = "small" | "medium" | "large";
export type Variant =
   | "primary"
   | "secondary"
   | "success"
   | "warning"
   | "danger";
export type Theme = "light" | "dark";
```

### 2. Crear Componentes Base

```typescript
// src/components/common/Button/types.ts
import { BaseComponentProps, Size, Variant } from "@types/common";

export interface ButtonProps extends BaseComponentProps {
   variant?: Variant;
   size?: Size;
   disabled?: boolean;
   loading?: boolean;
   fullWidth?: boolean;
   icon?: React.ReactNode;
   iconPosition?: "left" | "right";
   onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
   type?: "button" | "submit" | "reset";
   form?: string;
}
```

### 3. Implementar Componentes Reutilizables

```typescript
// src/components/common/Button/Button.tsx
import React, { forwardRef } from "react";
import { ButtonProps } from "./types";

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
   (
      {
         children,
         variant = "primary",
         size = "medium",
         disabled = false,
         loading = false,
         fullWidth = false,
         icon,
         iconPosition = "left",
         onClick,
         type = "button",
         className = "",
         testId,
         form,
         ...rest
      },
      ref,
   ) => {
      const baseClasses = "btn";
      const variantClasses = `btn--${variant}`;
      const sizeClasses = `btn--${size}`;
      const stateClasses = [
         disabled && "btn--disabled",
         loading && "btn--loading",
         fullWidth && "btn--full-width",
      ].filter(Boolean).join(" ");

      const classes = [
         baseClasses,
         variantClasses,
         sizeClasses,
         stateClasses,
         className,
      ]
         .filter(Boolean)
         .join(" ");

      const renderContent = (): React.ReactNode => {
         if (loading) {
            return (
               <>
                  <span className="btn__spinner" />
                  <span className="btn__text">Cargando...</span>
               </>
            );
         }

         if (icon) {
            return (
               <>
                  {iconPosition === "left" && (
                     <span className="btn__icon">{icon}</span>
                  )}
                  <span className="btn__text">{children}</span>
                  {iconPosition === "right" && (
                     <span className="btn__icon">{icon}</span>
                  )}
               </>
            );
         }

         return <span className="btn__text">{children}</span>;
      };

      return (
         <button
            ref={ref}
            type={type}
            className={classes}
            disabled={disabled || loading}
            onClick={onClick}
            data-testid={testId}
            form={form}
            {...rest}
         >
            {renderContent()}
         </button>
      );
   },
);

Button.displayName = "Button";
```

### 4. Crear Componente de Input

```typescript
// src/components/common/Input/Input.tsx
import React, { forwardRef, useState } from "react";
import { BaseComponentProps } from "@types/common";

export interface InputProps extends BaseComponentProps {
   type?: "text" | "email" | "password" | "number" | "tel" | "url";
   label?: string;
   placeholder?: string;
   value?: string;
   defaultValue?: string;
   disabled?: boolean;
   readonly?: boolean;
   required?: boolean;
   error?: string;
   helperText?: string;
   autoComplete?: string;
   autoFocus?: boolean;
   maxLength?: number;
   minLength?: number;
   onChange?: (event: React.ChangeEvent<HTMLInputElement>) => void;
   onBlur?: (event: React.FocusEvent<HTMLInputElement>) => void;
   onFocus?: (event: React.FocusEvent<HTMLInputElement>) => void;
   startIcon?: React.ReactNode;
   endIcon?: React.ReactNode;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
   (
      {
         type = "text",
         label,
         placeholder,
         value,
         defaultValue,
         disabled = false,
         readonly = false,
         required = false,
         error,
         helperText,
         autoComplete,
         autoFocus = false,
         maxLength,
         minLength,
         onChange,
         onBlur,
         onFocus,
         startIcon,
         endIcon,
         className = "",
         testId,
         ...rest
      },
      ref,
   ) => {
      const [focused, setFocused] = useState(false);
      const hasError = Boolean(error);
      const hasValue = Boolean(value || defaultValue);

      const containerClasses = [
         "input-container",
         focused && "input-container--focused",
         hasError && "input-container--error",
         disabled && "input-container--disabled",
         className,
      ].filter(Boolean).join(" ");

      const inputClasses = [
         "input",
         hasValue && "input--has-value",
         startIcon && "input--with-start-icon",
         endIcon && "input--with-end-icon",
      ].filter(Boolean).join(" ");

      const handleFocus = (event: React.FocusEvent<HTMLInputElement>): void => {
         setFocused(true);
         onFocus?.(event);
      };

      const handleBlur = (event: React.FocusEvent<HTMLInputElement>): void => {
         setFocused(false);
         onBlur?.(event);
      };

      return (
         <div className={containerClasses}>
            {label && (
               <label className="input-label">
                  {label}
                  {required && <span className="input-label__required">*</span>}
               </label>
            )}

            <div className="input-wrapper">
               {startIcon && (
                  <span className="input-icon input-icon--start">
                     {startIcon}
                  </span>
               )}

               <input
                  ref={ref}
                  type={type}
                  className={inputClasses}
                  placeholder={placeholder}
                  value={value}
                  defaultValue={defaultValue}
                  disabled={disabled}
                  readOnly={readonly}
                  required={required}
                  autoComplete={autoComplete}
                  autoFocus={autoFocus}
                  maxLength={maxLength}
                  minLength={minLength}
                  onChange={onChange}
                  onFocus={handleFocus}
                  onBlur={handleBlur}
                  data-testid={testId}
                  {...rest}
               />

               {endIcon && (
                  <span className="input-icon input-icon--end">{endIcon}</span>
               )}
            </div>

            {(error || helperText) && (
               <div className="input-feedback">
                  {error
                     ? <span className="input-error">{error}</span>
                     : <span className="input-helper">{helperText}</span>}
               </div>
            )}
         </div>
      );
   },
);

Input.displayName = "Input";
```

### 5. Crear Componente Modal

```typescript
// src/components/common/Modal/Modal.tsx
import React, { useEffect, useRef } from "react";
import { createPortal } from "react-dom";
import { BaseComponentProps } from "@types/common";

export interface ModalProps extends BaseComponentProps {
   isOpen: boolean;
   onClose: () => void;
   title?: string;
   size?: "small" | "medium" | "large" | "fullscreen";
   closable?: boolean;
   maskClosable?: boolean;
   footer?: React.ReactNode;
   closeIcon?: React.ReactNode;
}

export const Modal: React.FC<ModalProps> = ({
   isOpen,
   onClose,
   title,
   size = "medium",
   closable = true,
   maskClosable = true,
   footer,
   closeIcon = "×",
   children,
   className = "",
   testId,
}) => {
   const modalRef = useRef<HTMLDivElement>(null);

   useEffect(() => {
      const handleEscape = (event: KeyboardEvent): void => {
         if (event.key === "Escape" && closable) {
            onClose();
         }
      };

      if (isOpen) {
         document.addEventListener("keydown", handleEscape);
         document.body.style.overflow = "hidden";
      }

      return () => {
         document.removeEventListener("keydown", handleEscape);
         document.body.style.overflow = "unset";
      };
   }, [isOpen, closable, onClose]);

   const handleMaskClick = (event: React.MouseEvent): void => {
      if (event.target === event.currentTarget && maskClosable) {
         onClose();
      }
   };

   if (!isOpen) return null;

   const modalClasses = [
      "modal",
      `modal--${size}`,
      className,
   ].filter(Boolean).join(" ");

   const modalContent = (
      <div
         className="modal-overlay"
         onClick={handleMaskClick}
         data-testid={testId}
      >
         <div
            ref={modalRef}
            className={modalClasses}
            role="dialog"
            aria-modal="true"
         >
            {(title || closable) && (
               <div className="modal-header">
                  {title && <h2 className="modal-title">{title}</h2>}
                  {closable && (
                     <button
                        type="button"
                        className="modal-close"
                        onClick={onClose}
                        aria-label="Cerrar modal"
                     >
                        {closeIcon}
                     </button>
                  )}
               </div>
            )}

            <div className="modal-body">
               {children}
            </div>

            {footer && (
               <div className="modal-footer">
                  {footer}
               </div>
            )}
         </div>
      </div>
   );

   return createPortal(modalContent, document.body);
};
```

### 6. Crear Hook Personalizado para Modales

```typescript
// src/hooks/useModal.ts
import { useCallback, useState } from "react";

interface UseModalReturn {
   isOpen: boolean;
   open: () => void;
   close: () => void;
   toggle: () => void;
}

export const useModal = (initialState = false): UseModalReturn => {
   const [isOpen, setIsOpen] = useState(initialState);

   const open = useCallback(() => {
      setIsOpen(true);
   }, []);

   const close = useCallback(() => {
      setIsOpen(false);
   }, []);

   const toggle = useCallback(() => {
      setIsOpen((prev) => !prev);
   }, []);

   return {
      isOpen,
      open,
      close,
      toggle,
   };
};
```

## Tips

### Mejores Prácticas para Componentes

1. **Usar forwardRef**: Para componentes que necesitan acceso al DOM

```typescript
export const Input = forwardRef<HTMLInputElement, InputProps>((props, ref) => {
   // implementación
});
```

2. **Props opcionales con valores por defecto**:

```typescript
interface ButtonProps {
   variant?: "primary" | "secondary"; // Opcional
}

const Button: React.FC<ButtonProps> = ({
   variant = "primary", // Valor por defecto
}) => {
   // implementación
};
```

3. **Composición sobre herencia**:

```typescript
// ✅ Bueno: Componente compuesto
const Card = ({ children, className }: CardProps) => (
   <div className={`card ${className}`}>{children}</div>
);

const CardHeader = ({ children }: { children: React.ReactNode }) => (
   <div className="card-header">{children}</div>
);

const CardBody = ({ children }: { children: React.ReactNode }) => (
   <div className="card-body">{children}</div>
);

// Uso
<Card>
   <CardHeader>Título</CardHeader>
   <CardBody>Contenido</CardBody>
</Card>;
```

4. **Event handlers tipados**:

```typescript
interface ButtonProps {
   onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
   onSubmit?: (event: React.FormEvent<HTMLFormElement>) => void;
}
```

### Patrones de Diseño

1. **Compound Components**:

```typescript
// src/components/common/Tabs/Tabs.tsx
const TabsContext = React.createContext<TabsContextType | null>(null);

export const Tabs: React.FC<TabsProps> & {
   List: typeof TabList;
   Tab: typeof Tab;
   Panels: typeof TabPanels;
   Panel: typeof TabPanel;
} = ({ children, defaultValue, onChange }) => {
   // lógica del contexto
   return (
      <TabsContext.Provider value={contextValue}>
         {children}
      </TabsContext.Provider>
   );
};

Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panels = TabPanels;
Tabs.Panel = TabPanel;
```

2. **Render Props Pattern**:

```typescript
interface DataFetcherProps<T> {
   url: string;
   children: (data: {
      data: T | null;
      loading: boolean;
      error: string | null;
   }) => React.ReactNode;
}

export const DataFetcher = <T,>({ url, children }: DataFetcherProps<T>) => {
   const { data, loading, error } = useApi<T>(url);
   return <>{children({ data, loading, error })}</>;
};
```

## Ejemplos

### Ejemplo: Componente Card Completo

```typescript
// src/components/common/Card/Card.tsx
import React from "react";
import { BaseComponentProps } from "@types/common";

interface CardProps extends BaseComponentProps {
   variant?: "default" | "outlined" | "elevated";
   clickable?: boolean;
   onClick?: () => void;
}

export const Card: React.FC<CardProps> = ({
   children,
   variant = "default",
   clickable = false,
   onClick,
   className = "",
   testId,
}) => {
   const classes = [
      "card",
      `card--${variant}`,
      clickable && "card--clickable",
      className,
   ].filter(Boolean).join(" ");

   const Component = clickable ? "button" : "div";

   return (
      <Component
         className={classes}
         onClick={onClick}
         data-testid={testId}
         type={clickable ? "button" : undefined}
      >
         {children}
      </Component>
   );
};

// Subcomponentes
export const CardHeader: React.FC<BaseComponentProps> = ({
   children,
   className = "",
}) => (
   <div className={`card-header ${className}`}>
      {children}
   </div>
);

export const CardBody: React.FC<BaseComponentProps> = ({
   children,
   className = "",
}) => (
   <div className={`card-body ${className}`}>
      {children}
   </div>
);

export const CardFooter: React.FC<BaseComponentProps> = ({
   children,
   className = "",
}) => (
   <div className={`card-footer ${className}`}>
      {children}
   </div>
);
```

### Ejemplo: Test de Componente

```typescript
// src/components/common/Card/Card.test.tsx
import { fireEvent, render, screen } from "@testing-library/react";
import { Card, CardBody, CardFooter, CardHeader } from "./Card";

describe("Card Component", () => {
   it("renders children correctly", () => {
      render(
         <Card>
            <CardHeader>Header</CardHeader>
            <CardBody>Body content</CardBody>
            <CardFooter>Footer</CardFooter>
         </Card>,
      );

      expect(screen.getByText("Header")).toBeInTheDocument();
      expect(screen.getByText("Body content")).toBeInTheDocument();
      expect(screen.getByText("Footer")).toBeInTheDocument();
   });

   it("applies variant classes correctly", () => {
      const { container } = render(
         <Card variant="elevated" testId="test-card">
            Content
         </Card>,
      );

      const card = screen.getByTestId("test-card");
      expect(card).toHaveClass("card", "card--elevated");
   });

   it("handles click events when clickable", () => {
      const handleClick = jest.fn();

      render(
         <Card clickable onClick={handleClick}>
            Clickable card
         </Card>,
      );

      fireEvent.click(screen.getByText("Clickable card"));
      expect(handleClick).toHaveBeenCalledTimes(1);
   });

   it("renders as button when clickable", () => {
      render(
         <Card clickable>
            Button card
         </Card>,
      );

      expect(screen.getByRole("button")).toBeInTheDocument();
   });
});
```

### Ejemplo: Storybook Stories

```typescript
// src/components/common/Button/Button.stories.tsx
import type { Meta, StoryObj } from "@storybook/react";
import { Button } from "./Button";

const meta: Meta<typeof Button> = {
   title: "Common/Button",
   component: Button,
   parameters: {
      layout: "centered",
   },
   argTypes: {
      variant: {
         control: { type: "select" },
         options: ["primary", "secondary", "success", "warning", "danger"],
      },
      size: {
         control: { type: "select" },
         options: ["small", "medium", "large"],
      },
   },
};

export default meta;
type Story = StoryObj<typeof meta>;

export const Primary: Story = {
   args: {
      children: "Button",
      variant: "primary",
   },
};

export const Secondary: Story = {
   args: {
      children: "Button",
      variant: "secondary",
   },
};

export const Loading: Story = {
   args: {
      children: "Button",
      loading: true,
   },
};

export const WithIcon: Story = {
   args: {
      children: "Save",
      icon: "💾",
      iconPosition: "left",
   },
};
```

## Navegación

[⬅️ Configuración React + TypeScript + Vite](./configuracion-react-typescript-vite.md)
| [🏠 README Principal](../../README.md) |
[Redux Toolkit - Estado Global ➡️](./redux-toolkit-estado-global.md)
