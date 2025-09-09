# Step 6: Desarrollo de Aplicación Móvil (React Native)

## ¿Qué es?

Es la etapa de desarrollo de la aplicación móvil usando React Native con
TypeScript, compartiendo la lógica de negocio y estado con la aplicación web.
Esta fase permite crear aplicaciones nativas para iOS y Android con una base de
código compartida, manteniendo la consistencia con el ecosistema React ya
establecido.

## ¿Por qué es importante?

- **Experiencia Omnicanal**: Ofrece la misma funcionalidad en todos los
  dispositivos
- **Reutilización de Código**: Comparte lógica, servicios y estado con la web
- **Performance Nativa**: Acceso a funcionalidades específicas del dispositivo
- **Mantenibilidad**: Un solo equipo puede manejar web y móvil
- **Time to Market**: Desarrollo más rápido que aplicaciones nativas separadas
- **Consistencia**: Misma arquitectura y patrones de la aplicación web

## ¿Qué debe incluir?

### Configuración Base

- Setup de React Native con TypeScript
- Configuración de Metro Bundler
- Setup de herramientas de desarrollo (Flipper, React Native Debugger)
- Configuración de ESLint y Prettier para móvil

### Arquitectura Móvil

- Estructura de carpetas consistente con la web
- Componentes específicos para móvil (TouchableOpacity, FlatList, etc.)
- Manejo de navegación con React Navigation
- Adaptación de patrones de diseño para móvil

### Estado y Datos

- Sincronización de Redux store con la web
- Adaptación de servicios API para móvil
- Manejo de estado offline con Redux Persist
- Sincronización de datos cuando vuelve la conectividad

### UI/UX Móvil

- Componentes siguiendo Material Design / iOS Human Interface Guidelines
- Manejo de diferentes tamaños de pantalla
- Gestos y animaciones nativas
- Manejo de orientación de pantalla

### Funcionalidades Nativas

- Acceso a cámara y galería
- Notificaciones push
- Almacenamiento local seguro
- Geolocalización
- Biometría (huella, Face ID)

## ¿Qué debo hacer?

### 1. Configuración Inicial

```bash
# Instalar React Native CLI
npm install -g @react-native-community/cli

# Crear proyecto
npx react-native init MyProjectMobile --template react-native-template-typescript

# Instalar dependencias base
npm install @reduxjs/toolkit react-redux
npm install @react-navigation/native @react-navigation/native-stack
npm install react-native-screens react-native-safe-area-context
```

### 2. Estructura de Carpetas

```
src/
├── components/          # Componentes reutilizables móvil
├── screens/            # Pantallas de la aplicación
├── navigation/         # Configuración de navegación
├── hooks/              # Custom hooks (compartidos con web)
├── services/           # API calls (compartidos con web)
├── store/              # Redux store (compartido con web)
├── types/              # Tipos TypeScript (compartidos con web)
├── utils/              # Utilidades (compartidas con web)
├── assets/             # Imágenes, fuentes específicas de móvil
└── constants/          # Constantes específicas de móvil
```

### 3. Implementación Gradual

1. **[Configuración React Native + TypeScript](./configuracion-react-native-typescript.md)**
2. **[Desarrollo de Pantallas y Navegación](./pantallas-react-navigation.md)**
3. **[Sincronización de Estado Redux](./sincronizacion-estado-redux.md)**
4. **[Integración con APIs](./integracion-apis-movil.md)**
5. **[Testing en Dispositivos](./testing-dispositivos-simuladores.md)**
6. **[Preparación para App Stores](./preparacion-app-stores.md)**

### 4. Sincronización con Web

```typescript
// Compartir tipos
export interface User {
   id: string;
   email: string;
   name: string;
}

// Compartir servicios API
export class ApiService {
   static async getUsers(): Promise<User[]> {
      // Lógica compartida entre web y móvil
   }
}

// Compartir slices de Redux
export const userSlice = createSlice({
   name: "user",
   initialState,
   reducers: {
      // Reducers compartidos
   },
});
```

## Tips

### Desarrollo

- **Expo vs React Native CLI**: Para proyectos complejos, usa React Native CLI
- **Hot Reload**: Configura Fast Refresh para desarrollo ágil
- **Debugging**: Usa Flipper para debugging avanzado
- **Shared Code**: Crea packages compartidos para lógica común

### Performance

- **Lazy Loading**: Implementa lazy loading para pantallas
- **Lista Virtualizadas**: Usa FlatList para listas grandes
- **Imágenes**: Optimiza imágenes y usa lazy loading
- **Bundle Size**: Analiza el tamaño del bundle regularmente

### UX/UI

- **Platform Specific**: Usa Platform.select() para diferencias iOS/Android
- **Safe Areas**: Maneja las safe areas correctamente
- **Keyboard**: Gestiona el teclado virtual adecuadamente
- **Loading States**: Implementa estados de carga consistentes

### Testing

- **Detox**: Para testing end-to-end
- **Jest**: Para testing unitario (compartido con web)
- **Device Testing**: Prueba en dispositivos reales frecuentemente
- **Multiple Screens**: Prueba en diferentes tamaños de pantalla

## Ejemplos

### Componente Base Compartido

```typescript
// components/Button/Button.tsx
interface ButtonProps {
   title: string;
   onPress: () => void;
   variant?: "primary" | "secondary";
   disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
   title,
   onPress,
   variant = "primary",
   disabled = false,
}) => {
   return (
      <TouchableOpacity
         style={[
            styles.button,
            styles[variant],
            disabled && styles.disabled,
         ]}
         onPress={onPress}
         disabled={disabled}
      >
         <Text style={[styles.text, styles[`${variant}Text`]]}>
            {title}
         </Text>
      </TouchableOpacity>
   );
};
```

### Pantalla con Estado Global

```typescript
// screens/UserListScreen.tsx
import { useAppDispatch, useAppSelector } from "../store/hooks";
import { fetchUsers } from "../store/slices/userSlice";

export const UserListScreen: React.FC = () => {
   const dispatch = useAppDispatch();
   const { users, loading } = useAppSelector((state) => state.user);

   useEffect(() => {
      dispatch(fetchUsers());
   }, [dispatch]);

   if (loading) {
      return <LoadingComponent />;
   }

   return (
      <SafeAreaView style={styles.container}>
         <FlatList
            data={users}
            keyExtractor={(item) => item.id}
            renderItem={({ item }) => <UserCard user={item} />}
         />
      </SafeAreaView>
   );
};
```

### Servicio API Compartido

```typescript
// services/apiService.ts
class ApiService {
   private baseURL: string;

   constructor() {
      this.baseURL = Platform.select({
         ios: "https://api.myapp.com",
         android: "https://api.myapp.com",
         web: "https://api.myapp.com",
      })!;
   }

   async getUsers(): Promise<User[]> {
      const response = await fetch(`${this.baseURL}/users`);
      return response.json();
   }
}

export const apiService = new ApiService();
```

---

## Navegación

**Progreso en Desarrollo de Aplicación Móvil (React Native):**

- ✅ **Desarrollo de Aplicación Móvil** ← Estás aquí
- ⏭️
  [Configuración React Native + TypeScript](./configuracion-react-native-typescript.md)
- ⏭️ [Pantallas y React Navigation](./pantallas-react-navigation.md)
- ⏭️ [Sincronización Estado Redux](./sincronizacion-estado-redux.md)
- ⏭️ [Integración APIs Móvil](./integracion-apis-movil.md)
- ⏭️ [Testing Dispositivos y Simuladores](./testing-dispositivos-simuladores.md)
- ⏭️ [Preparación App Stores](./preparacion-app-stores.md)

---

### Siguiente Paso

Continúa con
[**Configuración React Native + TypeScript**](./configuracion-react-native-typescript.md)

[⬅️ Etapa 5: testing-componentes-jest](../step_05/testing-componentes-jest.md) |
[🏠 README Principal](../../README.md) |
[➡️ Configuración React Native + TypeScript](./configuracion-react-native-typescript.md)
