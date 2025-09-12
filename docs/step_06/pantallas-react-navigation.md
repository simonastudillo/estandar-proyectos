# Pantallas y React Navigation

## ¿Qué es?

Es la implementación del sistema de navegación en React Native usando React
Navigation, definiendo la estructura de pantallas, flujos de navegación y
patrones de UX móvil. Incluye la configuración de navigators, manejo de estado
de navegación y implementación de patrones de navegación específicos para móvil.

## ¿Por qué es importante?

- **UX Móvil**: Navegación intuitiva siguiendo patrones nativos
- **Performance**: Navegación optimizada con lazy loading
- **State Management**: Gestión del estado de navegación
- **Deep Linking**: Soporte para URLs profundas
- **Accesibilidad**: Navegación accesible para todos los usuarios
- **Tipado Seguro**: Navegación type-safe con TypeScript

## ¿Qué debe incluir?

### Tipos de Navigation

- Stack Navigation para flujos lineales
- Tab Navigation para navegación principal
- Drawer Navigation para menús laterales
- Modal Navigation para overlays

### Patrones de Navegación

- Authentication Flow
- Main App Flow
- Nested Navigation
- Modal Stacks

### Gestión de Estado

- Navigation State persistente
- Params tipados entre pantallas
- Deep linking configurado
- Back handler personalizado

### UX/UI

- Transiciones suaves entre pantallas
- Gestos nativos (swipe back, etc.)
- Manejo de safe areas
- Loading states en navegación

## ¿Qué debo hacer?

### 1. Instalación de Dependencias

```bash
# Core navigation
npm install @react-navigation/native
npm install @react-navigation/native-stack
npm install @react-navigation/bottom-tabs
npm install @react-navigation/drawer

# Dependencies
npm install react-native-screens react-native-safe-area-context
npm install react-native-gesture-handler react-native-reanimated

# iOS additional setup
cd ios && pod install && cd ..
```

### 2. Configuración de Types

**types/navigation.ts**:

```typescript
import { NativeStackScreenProps } from "@react-navigation/native-stack";
import { BottomTabScreenProps } from "@react-navigation/bottom-tabs";
import { CompositeScreenProps } from "@react-navigation/native";

// Auth Stack
export type AuthStackParamList = {
   Login: undefined;
   Register: undefined;
   ForgotPassword: undefined;
   ResetPassword: { token: string };
};

// Main Stack
export type MainStackParamList = {
   TabNavigator: undefined;
   Profile: { userId: string };
   Settings: undefined;
   EditProfile: { userId: string };
};

// Tab Navigator
export type TabParamList = {
   Home: undefined;
   Search: undefined;
   Notifications: undefined;
   Profile: undefined;
};

// Root Stack
export type RootStackParamList = {
   Auth: undefined;
   Main: undefined;
   Modal: { screen: string; params?: any };
};

// Screen Props Types
export type AuthStackScreenProps<T extends keyof AuthStackParamList> =
   NativeStackScreenProps<AuthStackParamList, T>;

export type MainStackScreenProps<T extends keyof MainStackParamList> =
   CompositeScreenProps<
      NativeStackScreenProps<MainStackParamList, T>,
      NativeStackScreenProps<RootStackParamList>
   >;

export type TabScreenProps<T extends keyof TabParamList> = CompositeScreenProps<
   BottomTabScreenProps<TabParamList, T>,
   NativeStackScreenProps<MainStackParamList>
>;

declare global {
   namespace ReactNavigation {
      interface RootParamList extends RootStackParamList {}
   }
}
```

### 3. Root Navigator

**navigation/RootNavigator.tsx**:

```typescript
import React from "react";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import { useAppSelector } from "@/store/hooks";
import { AuthNavigator } from "./AuthNavigator";
import { MainNavigator } from "./MainNavigator";
import { RootStackParamList } from "@/types/navigation";

const Stack = createNativeStackNavigator<RootStackParamList>();

export const RootNavigator: React.FC = () => {
   const { isAuthenticated, isLoading } = useAppSelector((state) => state.auth);

   if (isLoading) {
      return <LoadingScreen />;
   }

   return (
      <Stack.Navigator screenOptions={{ headerShown: false }}>
         {isAuthenticated
            ? <Stack.Screen name="Main" component={MainNavigator} />
            : <Stack.Screen name="Auth" component={AuthNavigator} />}
      </Stack.Navigator>
   );
};
```

### 4. Auth Navigator

**navigation/AuthNavigator.tsx**:

```typescript
import React from "react";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import { AuthStackParamList } from "@/types/navigation";
import { LoginScreen } from "@/screens/auth/LoginScreen";
import { RegisterScreen } from "@/screens/auth/RegisterScreen";
import { ForgotPasswordScreen } from "@/screens/auth/ForgotPasswordScreen";

const Stack = createNativeStackNavigator<AuthStackParamList>();

export const AuthNavigator: React.FC = () => {
   return (
      <Stack.Navigator
         initialRouteName="Login"
         screenOptions={{
            headerStyle: {
               backgroundColor: "#007AFF",
            },
            headerTintColor: "#fff",
            headerTitleStyle: {
               fontWeight: "bold",
            },
         }}
      >
         <Stack.Screen
            name="Login"
            component={LoginScreen}
            options={{ title: "Iniciar Sesión" }}
         />
         <Stack.Screen
            name="Register"
            component={RegisterScreen}
            options={{ title: "Registrarse" }}
         />
         <Stack.Screen
            name="ForgotPassword"
            component={ForgotPasswordScreen}
            options={{ title: "Recuperar Contraseña" }}
         />
      </Stack.Navigator>
   );
};
```

### 5. Main Navigator con Tabs

**navigation/MainNavigator.tsx**:

```typescript
import React from "react";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import { createBottomTabNavigator } from "@react-navigation/bottom-tabs";
import Icon from "react-native-vector-icons/Ionicons";
import { MainStackParamList, TabParamList } from "@/types/navigation";
import { HomeScreen } from "@/screens/main/HomeScreen";
import { SearchScreen } from "@/screens/main/SearchScreen";
import { NotificationsScreen } from "@/screens/main/NotificationsScreen";
import { ProfileScreen } from "@/screens/main/ProfileScreen";

const Stack = createNativeStackNavigator<MainStackParamList>();
const Tab = createBottomTabNavigator<TabParamList>();

const TabNavigator: React.FC = () => {
   return (
      <Tab.Navigator
         screenOptions={({ route }) => ({
            tabBarIcon: ({ focused, color, size }) => {
               let iconName: string;

               switch (route.name) {
                  case "Home":
                     iconName = focused ? "home" : "home-outline";
                     break;
                  case "Search":
                     iconName = focused ? "search" : "search-outline";
                     break;
                  case "Notifications":
                     iconName = focused
                        ? "notifications"
                        : "notifications-outline";
                     break;
                  case "Profile":
                     iconName = focused ? "person" : "person-outline";
                     break;
                  default:
                     iconName = "circle";
               }

               return <Icon name={iconName} size={size} color={color} />;
            },
            tabBarActiveTintColor: "#007AFF",
            tabBarInactiveTintColor: "gray",
            headerShown: false,
         })}
      >
         <Tab.Screen name="Home" component={HomeScreen} />
         <Tab.Screen name="Search" component={SearchScreen} />
         <Tab.Screen name="Notifications" component={NotificationsScreen} />
         <Tab.Screen name="Profile" component={ProfileScreen} />
      </Tab.Navigator>
   );
};

export const MainNavigator: React.FC = () => {
   return (
      <Stack.Navigator>
         <Stack.Screen
            name="TabNavigator"
            component={TabNavigator}
            options={{ headerShown: false }}
         />
         <Stack.Screen
            name="Profile"
            component={ProfileScreen}
            options={{ title: "Perfil" }}
         />
         <Stack.Screen
            name="Settings"
            component={SettingsScreen}
            options={{ title: "Configuración" }}
         />
      </Stack.Navigator>
   );
};
```

### 6. Pantalla Base con Tipos

**screens/BaseScreen.tsx**:

```typescript
import React from "react";
import {
   SafeAreaView,
   ScrollView,
   StatusBar,
   StyleSheet,
   View,
} from "react-native";
import { useSafeAreaInsets } from "react-native-safe-area-context";

interface BaseScreenProps {
   children: React.ReactNode;
   scrollable?: boolean;
   backgroundColor?: string;
   statusBarStyle?: "default" | "light-content" | "dark-content";
}

export const BaseScreen: React.FC<BaseScreenProps> = ({
   children,
   scrollable = false,
   backgroundColor = "#fff",
   statusBarStyle = "dark-content",
}) => {
   const insets = useSafeAreaInsets();

   const content = scrollable
      ? <ScrollView style={styles.scrollView}>{children}</ScrollView>
      : children;

   return (
      <View style={[styles.container, { backgroundColor }]}>
         <StatusBar
            barStyle={statusBarStyle}
            backgroundColor={backgroundColor}
         />
         <SafeAreaView style={[styles.safeArea, { paddingTop: insets.top }]}>
            {content}
         </SafeAreaView>
      </View>
   );
};

const styles = StyleSheet.create({
   container: {
      flex: 1,
   },
   safeArea: {
      flex: 1,
   },
   scrollView: {
      flex: 1,
   },
});
```

### 7. Pantalla de Ejemplo con Navegación

**screens/main/HomeScreen.tsx**:

```typescript
import React from "react";
import { StyleSheet, Text, View } from "react-native";
import { TabScreenProps } from "@/types/navigation";
import { BaseScreen } from "../BaseScreen";
import { Button } from "@/components/common/Button";

type Props = TabScreenProps<"Home">;

export const HomeScreen: React.FC<Props> = ({ navigation }) => {
   const handleNavigateToProfile = (): void => {
      navigation.navigate("Profile", { userId: "user123" });
   };

   const handleNavigateToSettings = (): void => {
      navigation.navigate("Settings");
   };

   return (
      <BaseScreen>
         <View style={styles.container}>
            <Text style={styles.title}>Bienvenido</Text>
            <Text style={styles.subtitle}>
               Esta es la pantalla principal de la aplicación
            </Text>

            <View style={styles.buttonContainer}>
               <Button
                  title="Ver Perfil"
                  onPress={handleNavigateToProfile}
                  style={styles.button}
               />
               <Button
                  title="Configuración"
                  onPress={handleNavigateToSettings}
                  variant="secondary"
                  style={styles.button}
               />
            </View>
         </View>
      </BaseScreen>
   );
};

const styles = StyleSheet.create({
   container: {
      flex: 1,
      padding: 20,
      justifyContent: "center",
   },
   title: {
      fontSize: 28,
      fontWeight: "bold",
      textAlign: "center",
      marginBottom: 10,
   },
   subtitle: {
      fontSize: 16,
      textAlign: "center",
      marginBottom: 30,
      color: "#666",
   },
   buttonContainer: {
      gap: 15,
   },
   button: {
      marginHorizontal: 20,
   },
});
```

## Tips

### Navigation Performance

- **Lazy Loading**: Usa lazy loading para pantallas pesadas
- **Memoization**: Memoiza componentes que no cambian frecuentemente
- **Stack Size**: Limita el tamaño del stack de navegación
- **Gestures**: Configura gestures apropiados para la plataforma

### UX Best Practices

- **Consistent Navigation**: Mantén patrones consistentes
- **Visual Feedback**: Proporciona feedback visual en transiciones
- **Loading States**: Maneja estados de carga durante navegación
- **Error Handling**: Implementa manejo de errores en navegación

### Deep Linking

```typescript
// Configuración de deep linking
const linking = {
   prefixes: ["myapp://"],
   config: {
      screens: {
         Auth: {
            screens: {
               Login: "login",
               Register: "register",
            },
         },
         Main: {
            screens: {
               TabNavigator: {
                  screens: {
                     Home: "home",
                     Profile: "profile",
                  },
               },
               Profile: "user/:userId",
            },
         },
      },
   },
};
```

### Accessibility

- **Screen Readers**: Configura labels para screen readers
- **Focus Management**: Maneja el focus correctamente
- **Gestures**: Proporciona alternativas a gestures complejos
- **Color Contrast**: Asegura contraste adecuado

## Ejemplos

### Navigation Hook Personalizado

```typescript
// hooks/useTypedNavigation.ts
import { useNavigation } from "@react-navigation/native";
import { NativeStackNavigationProp } from "@react-navigation/native-stack";
import { MainStackParamList } from "@/types/navigation";

export const useTypedNavigation = () => {
   return useNavigation<NativeStackNavigationProp<MainStackParamList>>();
};
```

### Modal Navigation

```typescript
// Configuración de modals
<Stack.Group screenOptions={{ presentation: "modal" }}>
   <Stack.Screen name="Modal" component={ModalScreen} />
</Stack.Group>;
```

### Conditional Navigation

```typescript
// Navegación condicional basada en estado
const ConditionalNavigator: React.FC = () => {
   const { user, isLoading } = useAppSelector((state) => state.auth);

   if (isLoading) return <LoadingScreen />;

   if (!user) return <AuthNavigator />;

   if (!user.profileComplete) return <OnboardingNavigator />;

   return <MainNavigator />;
};
```

### Custom Header

```typescript
// Header personalizado
const CustomHeader: React.FC<{ title: string }> = ({ title }) => (
   <View style={styles.header}>
      <Text style={styles.headerTitle}>{title}</Text>
   </View>
);

// Uso en Screen
<Stack.Screen
   name="Home"
   component={HomeScreen}
   options={{
      header: () => <CustomHeader title="Inicio" />,
   }}
/>;
```

## Navegación

[⬅️ Configuración React Native + TypeScript](./configuracion-react-native-typescript.md)
| [🏠 README Principal](../../README.md) |
[Sincronización Estado Redux ➡️](./sincronizacion-estado-redux.md)
