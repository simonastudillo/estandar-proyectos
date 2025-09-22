# Configuración React Native + TypeScript

## Conocimientos Involucrados
- Configuración de entornos de desarrollo
- Uso de TypeScript y React Native CLI
- Herramientas de calidad de código (ESLint, Prettier)

## Responsable
- Equipo de Desarrollo

## ¿Qué es?

Es el proceso de configuración inicial de un proyecto React Native con
TypeScript, estableciendo la base técnica para el desarrollo de la aplicación
móvil. Incluye la configuración del entorno, herramientas de desarrollo,
linting, y la estructura base del proyecto con tipado fuerte.

## ¿Por qué es importante?

- **Tipado Fuerte**: TypeScript previene errores en tiempo de compilación
- **Desarrollo Productivo**: Mejores herramientas de desarrollo y debugging
- **Consistencia**: Mismas convenciones que el proyecto web
- **Mantenibilidad**: Código más fácil de mantener y refactorizar
- **Team Collaboration**: Mejor experiencia para equipos de desarrollo
- **Performance**: Optimizaciones y detección temprana de problemas

## ¿Qué debe incluir?

### Configuración Base

- React Native CLI con template TypeScript
- Configuración de Metro Bundler
- Setup de simuladores iOS/Android
- Configuración de herramientas de desarrollo

### Herramientas de Calidad

- ESLint con reglas específicas para React Native
- Prettier para formateo de código
- TypeScript con configuración estricta
- Husky para git hooks

### Estructura del Proyecto

- Organización de carpetas consistente
- Configuración de path mapping
- Setup de assets y recursos
- Configuración de constantes y variables de entorno

### Debugging y Testing

- React Native Debugger
- Flipper para debugging avanzado
- Jest para testing unitario
- Configuración de sourcemaps

## ¿Qué debo hacer?

### 1. Instalación y Configuración Inicial

```bash
# Verificar prerequisites
npx react-native doctor

# Crear proyecto con TypeScript template
npx react-native init MyProjectMobile --template react-native-template-typescript

cd MyProjectMobile

# Instalar dependencias adicionales
npm install --save-dev @types/react @types/react-native
npm install --save-dev @typescript-eslint/eslint-plugin @typescript-eslint/parser
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
```

### 2. Configuración TypeScript

**tsconfig.json**:

```json
{
   "compilerOptions": {
      "target": "esnext",
      "lib": ["es2017", "es2018", "es2019", "es2020"],
      "allowJs": true,
      "skipLibCheck": true,
      "strict": true,
      "forceConsistentCasingInFileNames": true,
      "noEmit": true,
      "esModuleInterop": true,
      "module": "esnext",
      "moduleResolution": "node",
      "resolveJsonModule": true,
      "isolatedModules": true,
      "jsx": "react-jsx",
      "baseUrl": "./src",
      "paths": {
         "@/*": ["*"],
         "@/components/*": ["components/*"],
         "@/screens/*": ["screens/*"],
         "@/services/*": ["services/*"],
         "@/types/*": ["types/*"],
         "@/utils/*": ["utils/*"],
         "@/store/*": ["store/*"]
      }
   },
   "include": [
      "src/**/*",
      "App.tsx",
      "index.js"
   ],
   "exclude": [
      "node_modules",
      "babel.config.js",
      "metro.config.js",
      "jest.config.js"
   ]
}
```

### 3. Configuración ESLint

**.eslintrc.json**:

```json
{
   "root": true,
   "extends": [
      "@react-native-community",
      "@typescript-eslint/recommended",
      "prettier"
   ],
   "parser": "@typescript-eslint/parser",
   "plugins": ["@typescript-eslint", "prettier"],
   "rules": {
      "prettier/prettier": "error",
      "@typescript-eslint/no-unused-vars": "error",
      "@typescript-eslint/explicit-function-return-type": "warn",
      "@typescript-eslint/no-explicit-any": "warn",
      "react-native/no-inline-styles": "warn",
      "react-hooks/exhaustive-deps": "warn"
   },
   "ignorePatterns": ["node_modules/", "dist/", "build/"]
}
```

### 4. Configuración Prettier

**.prettierrc.json**:

```json
{
   "semi": true,
   "trailingComma": "es5",
   "singleQuote": true,
   "printWidth": 80,
   "tabWidth": 2,
   "useTabs": false,
   "bracketSpacing": true,
   "arrowParens": "avoid"
}
```

### 5. Estructura de Carpetas

```
src/
├── components/          # Componentes reutilizables
│   ├── common/         # Componentes comunes (Button, Input, etc.)
│   ├── forms/          # Componentes de formularios
│   └── navigation/     # Componentes de navegación
├── screens/            # Pantallas de la aplicación
│   ├── auth/          # Pantallas de autenticación
│   ├── main/          # Pantallas principales
│   └── profile/       # Pantallas de perfil
├── navigation/         # Configuración de navegación
├── hooks/              # Custom hooks
├── services/           # Servicios API y lógica de negocio
├── store/              # Redux store y slices
├── types/              # Definiciones TypeScript
├── utils/              # Utilidades y helpers
├── constants/          # Constantes de la aplicación
├── assets/             # Imágenes, fuentes, etc.
└── styles/             # Estilos globales y temas
```

### 6. Configuración de Metro Bundler

**metro.config.js**:

```javascript
const { getDefaultConfig } = require("metro-config");

module.exports = (async () => {
   const {
      resolver: { sourceExts, assetExts },
   } = await getDefaultConfig(__dirname);

   return {
      transformer: {
         babelTransformerPath: require.resolve("react-native-svg-transformer"),
      },
      resolver: {
         assetExts: assetExts.filter((ext) => ext !== "svg"),
         sourceExts: [...sourceExts, "svg"],
         alias: {
            "@": "./src",
         },
      },
   };
})();
```

### 7. Configuración de Variables de Entorno

**react-native-config setup**:

```bash
npm install react-native-config
cd ios && pod install && cd ..
```

**.env**:

```
API_URL=https://api.myapp.com
APP_NAME=MyApp
ENVIRONMENT=development
```

**env.d.ts**:

```typescript
declare module "react-native-config" {
   export interface NativeConfig {
      API_URL: string;
      APP_NAME: string;
      ENVIRONMENT: string;
   }

   export const Config: NativeConfig;
   export default Config;
}
```

## Tips

### Desarrollo

- **Fast Refresh**: Mantén habilitado para desarrollo rápido
- **Flipper**: Usa Flipper para debugging avanzado de Redux y network
- **Path Mapping**: Configura paths absolutos para imports limpios
- **Types**: Crea types específicos para props de navegación

### Performance

- **Bundle Analyzer**: Usa herramientas para analizar el tamaño del bundle
- **Lazy Loading**: Implementa lazy loading para componentes pesados
- **Memory Leaks**: Usa Flipper para detectar memory leaks
- **Profiling**: Usa React DevTools para profiling de componentes

### Debugging

- **Sourcemaps**: Asegúrate de que los sourcemaps estén configurados
- **Console Logs**: Usa console.tron para debugging con Reactotron
- **Crashlytics**: Integra Firebase Crashlytics para reportes de crashes
- **Network**: Monitorea las llamadas API con Flipper

### Best Practices

- **Absolute Imports**: Usa imports absolutos en lugar de relativos
- **Type Safety**: Nunca uses `any`, prefiere `unknown`
- **Error Boundaries**: Implementa error boundaries para manejo de errores
- **Performance**: Usa React.memo y useMemo cuando sea necesario

## Ejemplos

### App.tsx Principal

```typescript
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { Provider } from "react-redux";
import { store } from "@/store";
import { RootNavigator } from "@/navigation/RootNavigator";
import { ErrorBoundary } from "@/components/common/ErrorBoundary";

const App: React.FC = () => {
   return (
      <Provider store={store}>
         <ErrorBoundary>
            <NavigationContainer>
               <RootNavigator />
            </NavigationContainer>
         </ErrorBoundary>
      </Provider>
   );
};

export default App;
```

### Tipos de Navegación

```typescript
// types/navigation.ts
import { NativeStackScreenProps } from "@react-navigation/native-stack";

export type RootStackParamList = {
   Home: undefined;
   Profile: { userId: string };
   Settings: undefined;
   Login: undefined;
};

export type RootStackScreenProps<T extends keyof RootStackParamList> =
   NativeStackScreenProps<RootStackParamList, T>;

declare global {
   namespace ReactNavigation {
      interface RootParamList extends RootStackParamList {}
   }
}
```

### Configuración de Constantes

```typescript
// constants/config.ts
import Config from "react-native-config";

export const API_CONFIG = {
   BASE_URL: Config.API_URL || "https://api.myapp.com",
   TIMEOUT: 10000,
   RETRY_ATTEMPTS: 3,
} as const;

export const APP_CONFIG = {
   NAME: Config.APP_NAME || "MyApp",
   VERSION: "1.0.0",
   ENVIRONMENT: Config.ENVIRONMENT || "development",
} as const;
```

### Component Base con Tipos

```typescript
// components/common/Button.tsx
import React from "react";
import {
   StyleSheet,
   Text,
   TouchableOpacity,
   TouchableOpacityProps,
} from "react-native";

interface ButtonProps extends TouchableOpacityProps {
   title: string;
   variant?: "primary" | "secondary" | "danger";
   size?: "small" | "medium" | "large";
   loading?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
   title,
   variant = "primary",
   size = "medium",
   loading = false,
   style,
   disabled,
   ...props
}) => {
   const isDisabled = disabled || loading;

   return (
      <TouchableOpacity
         style={[
            styles.button,
            styles[variant],
            styles[size],
            isDisabled && styles.disabled,
            style,
         ]}
         disabled={isDisabled}
         {...props}
      >
         <Text style={[styles.text, styles[`${variant}Text`]]}>
            {loading ? "Loading..." : title}
         </Text>
      </TouchableOpacity>
   );
};

const styles = StyleSheet.create({
   button: {
      borderRadius: 8,
      alignItems: "center",
      justifyContent: "center",
   },
   primary: {
      backgroundColor: "#007AFF",
   },
   secondary: {
      backgroundColor: "#8E8E93",
   },
   danger: {
      backgroundColor: "#FF3B30",
   },
   small: {
      paddingHorizontal: 12,
      paddingVertical: 8,
   },
   medium: {
      paddingHorizontal: 16,
      paddingVertical: 12,
   },
   large: {
      paddingHorizontal: 20,
      paddingVertical: 16,
   },
   disabled: {
      opacity: 0.5,
   },
   text: {
      fontWeight: "600",
      color: "white",
   },
   primaryText: {
      color: "white",
   },
   secondaryText: {
      color: "white",
   },
   dangerText: {
      color: "white",
   },
});
```

## Navegación

[⬅️ Desarrollo de Aplicación Móvil](./desarrollo-movil.md) |
[🏠 README Principal](../../README.md) |
[Pantallas y React Navigation ➡️](./pantallas-react-navigation.md)
