# Testing Dispositivos y Simuladores

## ¿Qué es?

Es la implementación de estrategias de testing específicas para aplicaciones
móviles, incluyendo testing en simuladores, dispositivos reales, testing
automatizado y validación de funcionalidades nativas. Cubre desde testing
unitario hasta testing end-to-end en diferentes dispositivos y sistemas
operativos.

## ¿Por qué es importante?

- **Compatibilidad**: Asegurar funcionamiento en diferentes dispositivos
- **Performance**: Validar rendimiento en hardware real
- **Funcionalidades Nativas**: Probar acceso a cámara, GPS, etc.
- **UX/UI**: Verificar experiencia en pantallas reales
- **Regresiones**: Detectar problemas antes del release
- **Calidad**: Mantener alta calidad en todas las plataformas

## ¿Qué debe incluir?

### Testing Automatizado

- Jest para testing unitario
- Detox para testing E2E
- Appium para cross-platform testing
- Maestro para UI testing

### Testing en Simuladores

- iOS Simulator setup
- Android Emulator setup
- Testing en múltiples versiones de OS
- Simulación de condiciones de red

### Testing en Dispositivos Reales

- Device farm setup
- Testing en diferentes marcas/modelos
- Performance testing
- Battery usage testing

### Testing de Funcionalidades Nativas

- Camera testing
- GPS/Location testing
- Push notifications testing
- Biometric authentication testing

## ¿Qué debo hacer?

### 1. Configuración de Entorno de Testing

```bash
# Testing dependencies
npm install --save-dev jest @testing-library/react-native
npm install --save-dev detox
npm install --save-dev @testing-library/jest-native

# Detox CLI
npm install -g detox-cli

# iOS testing tools
brew tap wix/brew
brew install applesimutils

# Android testing tools (si usas macOS)
brew install android-platform-tools
```

### 2. Configuración Jest

**jest.config.js**:

```javascript
module.exports = {
   preset: "react-native",
   setupFilesAfterEnv: [
      "@testing-library/jest-native/extend-expect",
      "<rootDir>/src/test/setup.ts",
   ],
   testPathIgnorePatterns: [
      "/node_modules/",
      "/e2e/",
   ],
   transformIgnorePatterns: [
      "node_modules/(?!(react-native|@react-native|react-navigation|@react-navigation)/)",
   ],
   collectCoverageFrom: [
      "src/**/*.{ts,tsx}",
      "!src/**/*.d.ts",
      "!src/test/**",
      "!src/**/__tests__/**",
   ],
   coverageThreshold: {
      global: {
         branches: 80,
         functions: 80,
         lines: 80,
         statements: 80,
      },
   },
   testEnvironment: "node",
   moduleNameMapping: {
      "^@/(.*)$": "<rootDir>/src/$1",
   },
};
```

### 3. Setup de Testing

**src/test/setup.ts**:

```typescript
import "react-native-gesture-handler/jestSetup";

// Mock AsyncStorage
jest.mock(
   "@react-native-async-storage/async-storage",
   () =>
      require(
         "@react-native-async-storage/async-storage/jest/async-storage-mock",
      ),
);

// Mock NetInfo
jest.mock("@react-native-netinfo/netinfo", () => ({
   fetch: jest.fn(() =>
      Promise.resolve({
         isConnected: true,
         isInternetReachable: true,
         type: "wifi",
      })
   ),
   addEventListener: jest.fn(() => jest.fn()),
}));

// Mock React Navigation
jest.mock("@react-navigation/native", () => ({
   useNavigation: () => ({
      navigate: jest.fn(),
      goBack: jest.fn(),
      reset: jest.fn(),
   }),
   useRoute: () => ({
      params: {},
   }),
}));

// Mock Redux store
jest.mock("@/store/hooks", () => ({
   useAppDispatch: () => jest.fn(),
   useAppSelector: (selector: any) =>
      selector({
         auth: { isAuthenticated: true, user: null },
         network: { isConnected: true },
      }),
}));

// Mock native modules
jest.mock("react-native-permissions", () => ({
   check: jest.fn(() => Promise.resolve("granted")),
   request: jest.fn(() => Promise.resolve("granted")),
   PERMISSIONS: {
      IOS: { CAMERA: "ios.permission.CAMERA" },
      ANDROID: { CAMERA: "android.permission.CAMERA" },
   },
   RESULTS: {
      GRANTED: "granted",
      DENIED: "denied",
      BLOCKED: "blocked",
   },
}));

// Silence console warnings during tests
global.console = {
   ...console,
   warn: jest.fn(),
   error: jest.fn(),
};

// Mock Flipper
jest.mock("react-native-flipper", () => ({
   logger: {
      info: jest.fn(),
      warn: jest.fn(),
      error: jest.fn(),
   },
}));
```

### 4. Testing de Componentes

**components/**tests**/Button.test.tsx**:

```typescript
import React from "react";
import { fireEvent, render, waitFor } from "@testing-library/react-native";
import { Button } from "../Button";

describe("Button Component", () => {
   const mockOnPress = jest.fn();

   beforeEach(() => {
      mockOnPress.mockClear();
   });

   it("renders correctly with title", () => {
      const { getByText } = render(
         <Button title="Test Button" onPress={mockOnPress} />,
      );

      expect(getByText("Test Button")).toBeTruthy();
   });

   it("calls onPress when pressed", async () => {
      const { getByText } = render(
         <Button title="Test Button" onPress={mockOnPress} />,
      );

      fireEvent.press(getByText("Test Button"));

      await waitFor(() => {
         expect(mockOnPress).toHaveBeenCalledTimes(1);
      });
   });

   it("is disabled when loading", () => {
      const { getByText } = render(
         <Button title="Test Button" onPress={mockOnPress} loading />,
      );

      fireEvent.press(getByText("Loading..."));

      expect(mockOnPress).not.toHaveBeenCalled();
   });

   it("applies correct styles for variants", () => {
      const { getByTestId } = render(
         <Button
            title="Test Button"
            onPress={mockOnPress}
            variant="danger"
            testID="danger-button"
         />,
      );

      const button = getByTestId("danger-button");
      expect(button.props.style).toMatchObject(
         expect.objectContaining({
            backgroundColor: "#FF3B30",
         }),
      );
   });
});
```

### 5. Testing de Hooks

**hooks/**tests**/useUsers.test.tsx**:

```typescript
import { renderHook, waitFor } from "@testing-library/react-native";
import { QueryClient, QueryClientProvider } from "react-query";
import { useUsers } from "../useUsers";
import { userService } from "@/services/userService";

// Mock userService
jest.mock("@/services/userService");
const mockUserService = userService as jest.Mocked<typeof userService>;

const createWrapper = () => {
   const queryClient = new QueryClient({
      defaultOptions: {
         queries: { retry: false },
         mutations: { retry: false },
      },
   });

   return ({ children }: { children: React.ReactNode }) => (
      <QueryClientProvider client={queryClient}>
         {children}
      </QueryClientProvider>
   );
};

describe("useUsers Hook", () => {
   beforeEach(() => {
      jest.clearAllMocks();
   });

   it("fetches users successfully", async () => {
      const mockUsers = [
         { id: "1", name: "John Doe", email: "john@example.com" },
         { id: "2", name: "Jane Doe", email: "jane@example.com" },
      ];

      mockUserService.getUsers.mockResolvedValue(mockUsers);

      const { result } = renderHook(() => useUsers(), {
         wrapper: createWrapper(),
      });

      await waitFor(() => {
         expect(result.current.isSuccess).toBe(true);
      });

      expect(result.current.data).toEqual(mockUsers);
      expect(mockUserService.getUsers).toHaveBeenCalledTimes(1);
   });

   it("handles error correctly", async () => {
      const errorMessage = "Failed to fetch users";
      mockUserService.getUsers.mockRejectedValue(new Error(errorMessage));

      const { result } = renderHook(() => useUsers(), {
         wrapper: createWrapper(),
      });

      await waitFor(() => {
         expect(result.current.isError).toBe(true);
      });

      expect(result.current.error).toBeTruthy();
   });
});
```

### 6. Configuración Detox para E2E

**.detoxrc.js**:

```javascript
module.exports = {
   testRunner: "jest",
   runnerConfig: "e2e/config.json",
   skipLegacyWorkersInjection: true,
   apps: {
      "ios.debug": {
         type: "ios.app",
         binaryPath: "ios/build/Build/Products/Debug-iphonesimulator/MyApp.app",
         build:
            "xcodebuild -workspace ios/MyApp.xcworkspace -scheme MyApp -configuration Debug -sdk iphonesimulator -derivedDataPath ios/build",
      },
      "android.debug": {
         type: "android.apk",
         binaryPath: "android/app/build/outputs/apk/debug/app-debug.apk",
         build:
            "cd android && ./gradlew assembleDebug assembleAndroidTest -DtestBuildType=debug",
      },
   },
   devices: {
      simulator: {
         type: "ios.simulator",
         device: {
            type: "iPhone 14",
         },
      },
      emulator: {
         type: "android.emulator",
         device: {
            avdName: "Pixel_4_API_30",
         },
      },
   },
   configurations: {
      "ios.sim.debug": {
         device: "simulator",
         app: "ios.debug",
      },
      "android.emu.debug": {
         device: "emulator",
         app: "android.debug",
      },
   },
};
```

### 7. Tests E2E

**e2e/login.e2e.js**:

```javascript
describe("Login Flow", () => {
   beforeAll(async () => {
      await device.launchApp();
   });

   beforeEach(async () => {
      await device.reloadReactNative();
   });

   it("should login successfully with valid credentials", async () => {
      // Wait for login screen to appear
      await waitFor(element(by.id("login-screen")))
         .toBeVisible()
         .withTimeout(5000);

      // Enter email
      await element(by.id("email-input")).typeText("test@example.com");

      // Enter password
      await element(by.id("password-input")).typeText("password123");

      // Tap login button
      await element(by.id("login-button")).tap();

      // Wait for home screen
      await waitFor(element(by.id("home-screen")))
         .toBeVisible()
         .withTimeout(10000);

      // Verify user is logged in
      await expect(element(by.text("Bienvenido"))).toBeVisible();
   });

   it("should show error with invalid credentials", async () => {
      await element(by.id("email-input")).typeText("invalid@example.com");
      await element(by.id("password-input")).typeText("wrongpassword");
      await element(by.id("login-button")).tap();

      await waitFor(element(by.text("Credenciales inválidas")))
         .toBeVisible()
         .withTimeout(5000);
   });

   it("should navigate to register screen", async () => {
      await element(by.id("register-link")).tap();

      await waitFor(element(by.id("register-screen")))
         .toBeVisible()
         .withTimeout(5000);
   });
});
```

### 8. Testing de Performance

**e2e/performance.e2e.js**:

```javascript
describe("Performance Tests", () => {
   beforeAll(async () => {
      await device.launchApp({
         enableSynchronization: false, // Para testing de performance
      });
   });

   it("should load user list within acceptable time", async () => {
      const startTime = Date.now();

      await element(by.id("users-tab")).tap();

      await waitFor(element(by.id("user-list")))
         .toBeVisible()
         .withTimeout(5000);

      const endTime = Date.now();
      const loadTime = endTime - startTime;

      expect(loadTime).toBeLessThan(3000); // Menos de 3 segundos
   });

   it("should scroll smoothly through large list", async () => {
      await element(by.id("user-list")).scroll(2000, "down");
      await element(by.id("user-list")).scroll(2000, "down");
      await element(by.id("user-list")).scroll(2000, "up");

      // Verificar que la lista sigue siendo funcional
      await expect(element(by.id("user-list"))).toBeVisible();
   });
});
```

### 9. Scripts de Testing

**package.json**:

```json
{
   "scripts": {
      "test": "jest",
      "test:watch": "jest --watch",
      "test:coverage": "jest --coverage",
      "test:e2e:ios": "detox test --configuration ios.sim.debug",
      "test:e2e:android": "detox test --configuration android.emu.debug",
      "test:build:ios": "detox build --configuration ios.sim.debug",
      "test:build:android": "detox build --configuration android.emu.debug"
   }
}
```

## Tips

### Testing Strategy

- **Pyramid Testing**: Más unit tests, menos E2E tests
- **Test Real Devices**: Prueba en dispositivos reales regularmente
- **Network Conditions**: Simula diferentes condiciones de red
- **Battery Testing**: Monitorea uso de batería durante tests

### Performance Testing

- **Memory Leaks**: Usa profiling tools para detectar memory leaks
- **FPS Monitoring**: Monitorea frame rate durante interactions
- **Bundle Size**: Revisa el tamaño del bundle regularmente
- **Cold Start**: Mide tiempo de inicio en frío

### Device Testing

- **Multiple OS Versions**: Prueba en diferentes versiones de iOS/Android
- **Screen Sizes**: Valida en diferentes tamaños de pantalla
- **Orientations**: Prueba en portrait y landscape
- **Hardware Variations**: Considera dispositivos de gama baja/alta

### CI/CD Integration

- **Automated Testing**: Integra tests en pipeline de CI/CD
- **Device Farms**: Usa servicios como Firebase Test Lab
- **Parallel Testing**: Ejecuta tests en paralelo para agilizar
- **Test Reports**: Genera reportes detallados de tests

## Ejemplos

### Custom Testing Utilities

```typescript
// test/utils.tsx
import React from "react";
import { render, RenderOptions } from "@testing-library/react-native";
import { Provider } from "react-redux";
import { NavigationContainer } from "@react-navigation/native";
import { QueryClient, QueryClientProvider } from "react-query";
import { store } from "@/store";

const AllTheProviders: React.FC<{ children: React.ReactNode }> = ({
   children,
}) => {
   const queryClient = new QueryClient({
      defaultOptions: {
         queries: { retry: false },
         mutations: { retry: false },
      },
   });

   return (
      <Provider store={store}>
         <QueryClientProvider client={queryClient}>
            <NavigationContainer>
               {children}
            </NavigationContainer>
         </QueryClientProvider>
      </Provider>
   );
};

const customRender = (
   ui: React.ReactElement,
   options?: Omit<RenderOptions, "wrapper">,
) => render(ui, { wrapper: AllTheProviders, ...options });

export * from "@testing-library/react-native";
export { customRender as render };
```

### Device-Specific Testing

```javascript
// e2e/helpers/device.js
export const getDeviceType = () => {
   if (device.getPlatform() === "ios") {
      return "ios";
   }
   return "android";
};

export const isTablet = async () => {
   const windowSize = await device.getWindowSize();
   return windowSize.width > 768;
};

export const takeScreenshot = async (name) => {
   await device.takeScreenshot(name);
};
```

### Accessibility Testing

```javascript
describe("Accessibility Tests", () => {
   it("should have proper accessibility labels", async () => {
      await expect(element(by.id("login-button")))
         .toHaveAccessibilityLabel("Iniciar sesión");

      await expect(element(by.id("email-input")))
         .toHaveAccessibilityLabel("Ingrese su email");
   });
});
```

---

## Navegación

**Progreso en Desarrollo de Aplicación Móvil (React Native):**

- ✅ [Desarrollo de Aplicación Móvil](./desarrollo-movil.md)
- ✅
  [Configuración React Native + TypeScript](./configuracion-react-native-typescript.md)
- ✅ [Pantallas y React Navigation](./pantallas-react-navigation.md)
- ✅ [Sincronización Estado Redux](./sincronizacion-estado-redux.md)
- ✅ [Integración APIs Móvil](./integracion-apis-movil.md)
- ✅ **Testing Dispositivos y Simuladores** ← Estás aquí
- ⏭️ [Preparación App Stores](./preparacion-app-stores.md)

---

### Siguiente Paso

Continúa con [**Preparación App Stores**](./preparacion-app-stores.md)

[⬅️ Integración APIs Móvil](./integracion-apis-movil.md) |
[🏠 README Principal](../../README.md) |
[➡️ Preparación App Stores](./preparacion-app-stores.md)
