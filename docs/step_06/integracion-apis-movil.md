# Integración APIs Móvil

## Conocimientos Involucrados
- Manejo de APIs RESTful
- Optimización de conectividad y rendimiento
- Implementación de estrategias de cache y persistencia

## Responsable
- Equipo de Desarrollo

## ¿Qué es?

Es la implementación de servicios para consumir APIs RESTful en la aplicación
móvil, adaptando la lógica de comunicación con el backend a las necesidades
específicas de móvil. Incluye manejo de conectividad, cache offline,
sincronización de datos y optimizaciones de performance para dispositivos
móviles.

## ¿Por qué es importante?

- **Conectividad Variable**: Manejo de conexiones intermitentes
- **Performance**: Optimización para redes móviles lentas
- **Battery Life**: Uso eficiente de batería en requests
- **Offline Support**: Funcionalidad sin conexión
- **Data Usage**: Minimización del uso de datos
- **User Experience**: Respuesta rápida y fluida

## ¿Qué debe incluir?

### Cliente HTTP Configurado

- Axios configurado para móvil
- Interceptors para auth y errores
- Timeout y retry políticas
- Request/response transformation

### Manejo de Conectividad

- Detección de estado de red
- Queue de requests offline
- Sincronización automática
- Conflict resolution

### Cache y Persistencia

- Cache de responses HTTP
- Estrategias de invalidación
- Storage local optimizado
- Data synchronization

### Optimizaciones Móviles

- Request batching
- Image optimization
- Compression
- Progressive loading

## ¿Qué debo hacer?

### 1. Instalación de Dependencias

```bash
# HTTP Client
npm install axios

# Network detection
npm install @react-native-netinfo/netinfo

# Cache and storage
npm install @react-native-async-storage/async-storage
npm install react-query # o @tanstack/react-query para cache

# Image handling
npm install react-native-fast-image

# File system
npm install react-native-fs
```

### 2. Configuración Base de Axios

**services/httpClient.ts**:

```typescript
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from "axios";
import AsyncStorage from "@react-native-async-storage/async-storage";
import NetInfo from "@react-native-netinfo/netinfo";
import Config from "react-native-config";

class HttpClient {
   private client: AxiosInstance;
   private requestQueue: Array<() => Promise<any>> = [];
   private isOnline: boolean = true;

   constructor() {
      this.client = axios.create({
         baseURL: Config.API_URL,
         timeout: 10000,
         headers: {
            "Content-Type": "application/json",
            "Accept": "application/json",
         },
      });

      this.setupInterceptors();
      this.setupNetworkMonitoring();
   }

   private setupInterceptors(): void {
      // Request interceptor
      this.client.interceptors.request.use(
         async (config) => {
            // Add auth token
            const token = await AsyncStorage.getItem("authToken");
            if (token) {
               config.headers.Authorization = `Bearer ${token}`;
            }

            // Add request ID for tracking
            config.metadata = { requestId: this.generateRequestId() };

            console.log(
               `[API Request] ${config.method?.toUpperCase()} ${config.url}`,
            );
            return config;
         },
         (error) => {
            console.error("[API Request Error]", error);
            return Promise.reject(error);
         },
      );

      // Response interceptor
      this.client.interceptors.response.use(
         (response: AxiosResponse) => {
            console.log(
               `[API Response] ${response.status} ${response.config.url}`,
            );
            return response;
         },
         async (error) => {
            console.error(
               "[API Response Error]",
               error.response?.status,
               error.config?.url,
            );

            // Handle auth errors
            if (error.response?.status === 401) {
               await this.handleAuthError();
            }

            // Handle network errors
            if (!error.response && !this.isOnline) {
               return this.queueRequest(() =>
                  this.client.request(error.config)
               );
            }

            return Promise.reject(error);
         },
      );
   }

   private setupNetworkMonitoring(): void {
      NetInfo.addEventListener((state) => {
         const wasOffline = !this.isOnline;
         this.isOnline = state.isConnected ?? false;

         // Process queued requests when coming back online
         if (wasOffline && this.isOnline) {
            this.processRequestQueue();
         }
      });
   }

   private async handleAuthError(): Promise<void> {
      await AsyncStorage.removeItem("authToken");
      await AsyncStorage.removeItem("refreshToken");
      // Navigate to login screen
      // NavigationService.navigate('Login');
   }

   private generateRequestId(): string {
      return Math.random().toString(36).substr(2, 9);
   }

   private async queueRequest(request: () => Promise<any>): Promise<any> {
      return new Promise((resolve, reject) => {
         this.requestQueue.push(async () => {
            try {
               const result = await request();
               resolve(result);
            } catch (error) {
               reject(error);
            }
         });
      });
   }

   private async processRequestQueue(): Promise<void> {
      console.log(
         `[Queue] Processing ${this.requestQueue.length} queued requests`,
      );

      const queue = [...this.requestQueue];
      this.requestQueue = [];

      for (const request of queue) {
         try {
            await request();
         } catch (error) {
            console.error("[Queue Error]", error);
         }
      }
   }

   // Public methods
   async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
      const response = await this.client.get<T>(url, config);
      return response.data;
   }

   async post<T>(
      url: string,
      data?: any,
      config?: AxiosRequestConfig,
   ): Promise<T> {
      const response = await this.client.post<T>(url, data, config);
      return response.data;
   }

   async put<T>(
      url: string,
      data?: any,
      config?: AxiosRequestConfig,
   ): Promise<T> {
      const response = await this.client.put<T>(url, data, config);
      return response.data;
   }

   async delete<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
      const response = await this.client.delete<T>(url, config);
      return response.data;
   }

   // Upload with progress
   async uploadFile(
      url: string,
      file: any,
      onProgress?: (progress: number) => void,
   ): Promise<any> {
      const formData = new FormData();
      formData.append("file", file);

      return this.client.post(url, formData, {
         headers: {
            "Content-Type": "multipart/form-data",
         },
         onUploadProgress: (progressEvent) => {
            if (onProgress && progressEvent.total) {
               const progress = (progressEvent.loaded / progressEvent.total) *
                  100;
               onProgress(Math.round(progress));
            }
         },
      });
   }
}

export const httpClient = new HttpClient();
```

### 3. Servicios API Específicos

**services/userService.ts**:

```typescript
import { httpClient } from "./httpClient";
import { CreateUserDto, UpdateUserDto, User } from "@/types/user";

class UserService {
   private readonly baseUrl = "/users";

   async getUsers(): Promise<User[]> {
      return httpClient.get<User[]>(this.baseUrl);
   }

   async getUserById(id: string): Promise<User> {
      return httpClient.get<User>(`${this.baseUrl}/${id}`);
   }

   async createUser(userData: CreateUserDto): Promise<User> {
      return httpClient.post<User>(this.baseUrl, userData);
   }

   async updateUser(id: string, userData: UpdateUserDto): Promise<User> {
      return httpClient.put<User>(`${this.baseUrl}/${id}`, userData);
   }

   async deleteUser(id: string): Promise<void> {
      return httpClient.delete(`${this.baseUrl}/${id}`);
   }

   async uploadAvatar(userId: string, imageUri: string): Promise<User> {
      const file = {
         uri: imageUri,
         type: "image/jpeg",
         name: "avatar.jpg",
      };

      return httpClient.uploadFile(`${this.baseUrl}/${userId}/avatar`, file);
   }

   // Método optimizado para móvil - carga paginada
   async getUsersPaginated(page: number = 1, limit: number = 20): Promise<{
      users: User[];
      total: number;
      hasMore: boolean;
   }> {
      const response = await httpClient.get<{
         data: User[];
         meta: { total: number; page: number; limit: number };
      }>(`${this.baseUrl}?page=${page}&limit=${limit}`);

      return {
         users: response.data,
         total: response.meta.total,
         hasMore: response.data.length === limit,
      };
   }
}

export const userService = new UserService();
```

### 4. Cache con React Query

**hooks/useUsers.ts**:

```typescript
import { useMutation, useQuery, useQueryClient } from "react-query";
import { userService } from "@/services/userService";
import { CreateUserDto, User } from "@/types/user";
import { useNetworkStatus } from "@/hooks/useNetworkStatus";

export const useUsers = () => {
   const { isOnline } = useNetworkStatus();

   return useQuery(
      ["users"],
      () => userService.getUsers(),
      {
         enabled: isOnline, // Solo ejecutar si hay conexión
         staleTime: 5 * 60 * 1000, // 5 minutos
         cacheTime: 10 * 60 * 1000, // 10 minutos
         retry: (failureCount, error: any) => {
            // No reintentar si es error de auth
            if (error.response?.status === 401) return false;
            return failureCount < 3;
         },
         retryDelay: (attemptIndex) =>
            Math.min(1000 * 2 ** attemptIndex, 30000),
      },
   );
};

export const useUser = (id: string) => {
   const { isOnline } = useNetworkStatus();

   return useQuery(
      ["users", id],
      () => userService.getUserById(id),
      {
         enabled: !!id && isOnline,
         staleTime: 5 * 60 * 1000,
      },
   );
};

export const useCreateUser = () => {
   const queryClient = useQueryClient();

   return useMutation(
      (userData: CreateUserDto) => userService.createUser(userData),
      {
         onSuccess: (newUser) => {
            // Invalidar cache de usuarios
            queryClient.invalidateQueries(["users"]);

            // Agregar usuario al cache
            queryClient.setQueryData(["users", newUser.id], newUser);
         },
         onError: (error) => {
            console.error("Error creating user:", error);
         },
      },
   );
};

// Hook para carga infinita
export const useInfiniteUsers = () => {
   const { isOnline } = useNetworkStatus();

   return useInfiniteQuery(
      ["users", "infinite"],
      ({ pageParam = 1 }) => userService.getUsersPaginated(pageParam),
      {
         enabled: isOnline,
         getNextPageParam: (lastPage, pages) => {
            return lastPage.hasMore ? pages.length + 1 : undefined;
         },
         staleTime: 5 * 60 * 1000,
      },
   );
};
```

### 5. Componente con Infinite Scroll

**components/UserList.tsx**:

```typescript
import React from "react";
import {
   ActivityIndicator,
   FlatList,
   RefreshControl,
   StyleSheet,
   Text,
   View,
} from "react-native";
import { useInfiniteUsers } from "@/hooks/useUsers";
import { UserCard } from "./UserCard";
import { ErrorMessage } from "@/components/common/ErrorMessage";

export const UserList: React.FC = () => {
   const {
      data,
      error,
      fetchNextPage,
      hasNextPage,
      isFetchingNextPage,
      isLoading,
      refetch,
      isRefetching,
   } = useInfiniteUsers();

   const users = data?.pages.flatMap((page) => page.users) ?? [];

   const handleLoadMore = (): void => {
      if (hasNextPage && !isFetchingNextPage) {
         fetchNextPage();
      }
   };

   const renderFooter = (): React.ReactElement | null => {
      if (!isFetchingNextPage) return null;

      return (
         <View style={styles.footer}>
            <ActivityIndicator size="small" color="#007AFF" />
            <Text style={styles.footerText}>Cargando más usuarios...</Text>
         </View>
      );
   };

   const renderEmpty = (): React.ReactElement => (
      <View style={styles.empty}>
         <Text style={styles.emptyText}>No hay usuarios disponibles</Text>
      </View>
   );

   if (isLoading) {
      return (
         <View style={styles.loading}>
            <ActivityIndicator size="large" color="#007AFF" />
            <Text style={styles.loadingText}>Cargando usuarios...</Text>
         </View>
      );
   }

   if (error) {
      return (
         <ErrorMessage
            message="Error al cargar usuarios"
            onRetry={() => refetch()}
         />
      );
   }

   return (
      <FlatList
         data={users}
         keyExtractor={(item) => item.id}
         renderItem={({ item }) => <UserCard user={item} />}
         onEndReached={handleLoadMore}
         onEndReachedThreshold={0.5}
         ListFooterComponent={renderFooter}
         ListEmptyComponent={renderEmpty}
         refreshControl={
            <RefreshControl
               refreshing={isRefetching}
               onRefresh={refetch}
               colors={["#007AFF"]}
            />
         }
         showsVerticalScrollIndicator={false}
         contentContainerStyle={users.length === 0
            ? styles.emptyContainer
            : undefined}
      />
   );
};

const styles = StyleSheet.create({
   footer: {
      padding: 20,
      alignItems: "center",
   },
   footerText: {
      marginTop: 8,
      color: "#666",
      fontSize: 14,
   },
   loading: {
      flex: 1,
      justifyContent: "center",
      alignItems: "center",
   },
   loadingText: {
      marginTop: 10,
      fontSize: 16,
      color: "#666",
   },
   empty: {
      flex: 1,
      justifyContent: "center",
      alignItems: "center",
      padding: 40,
   },
   emptyText: {
      fontSize: 16,
      color: "#666",
      textAlign: "center",
   },
   emptyContainer: {
      flex: 1,
   },
});
```

### 6. Manejo de Imágenes Optimizado

**components/OptimizedImage.tsx**:

```typescript
import React, { useState } from "react";
import { StyleSheet, View, ViewStyle } from "react-native";
import FastImage, { FastImageProps } from "react-native-fast-image";
import { ActivityIndicator } from "react-native";

interface OptimizedImageProps extends Omit<FastImageProps, "source"> {
   uri: string;
   placeholder?: string;
   size?: number;
   style?: ViewStyle;
}

export const OptimizedImage: React.FC<OptimizedImageProps> = ({
   uri,
   placeholder,
   size = 100,
   style,
   ...props
}) => {
   const [loading, setLoading] = useState(true);
   const [error, setError] = useState(false);

   const handleLoad = (): void => {
      setLoading(false);
   };

   const handleError = (): void => {
      setLoading(false);
      setError(true);
   };

   const imageSource = {
      uri: error && placeholder ? placeholder : uri,
      priority: FastImage.priority.normal,
      cache: FastImage.cacheControl.immutable,
   };

   return (
      <View style={[styles.container, { width: size, height: size }, style]}>
         <FastImage
            source={imageSource}
            style={styles.image}
            onLoad={handleLoad}
            onError={handleError}
            resizeMode={FastImage.resizeMode.cover}
            {...props}
         />
         {loading && (
            <View style={styles.loading}>
               <ActivityIndicator size="small" color="#007AFF" />
            </View>
         )}
      </View>
   );
};

const styles = StyleSheet.create({
   container: {
      position: "relative",
      backgroundColor: "#f0f0f0",
      borderRadius: 8,
      overflow: "hidden",
   },
   image: {
      width: "100%",
      height: "100%",
   },
   loading: {
      position: "absolute",
      top: 0,
      left: 0,
      right: 0,
      bottom: 0,
      justifyContent: "center",
      alignItems: "center",
      backgroundColor: "rgba(240, 240, 240, 0.8)",
   },
});
```

## Tips

### Performance

- **Request Batching**: Agrupa múltiples requests similares
- **Image Optimization**: Usa WebP y compresión automática
- **Lazy Loading**: Carga datos solo cuando sea necesario
- **Cache Strategy**: Implementa cache inteligente con TTL

### Network Management

- **Retry Logic**: Implementa retry con backoff exponencial
- **Timeout Handling**: Usa timeouts apropiados para móvil
- **Priority Queue**: Prioriza requests críticos
- **Bandwidth Detection**: Adapta calidad según ancho de banda

### Offline Support

- **Request Queue**: Encola requests para ejecutar offline
- **Optimistic Updates**: Actualiza UI inmediatamente
- **Conflict Resolution**: Maneja conflictos de sincronización
- **Data Validation**: Valida datos antes de enviar

### Security

- **Certificate Pinning**: Implementa pinning de certificados
- **Request Signing**: Firma requests críticos
- **Data Encryption**: Encripta datos sensibles
- **Auth Token Refresh**: Maneja refresh automático de tokens

## Ejemplos

### Service con Retry Policy

```typescript
class ApiService {
   async fetchWithRetry<T>(
      url: string,
      options: AxiosRequestConfig = {},
      maxRetries: number = 3,
   ): Promise<T> {
      let lastError: any;

      for (let i = 0; i <= maxRetries; i++) {
         try {
            return await httpClient.get<T>(url, options);
         } catch (error: any) {
            lastError = error;

            // No reintentar en errores 4xx
            if (error.response?.status >= 400 && error.response?.status < 500) {
               throw error;
            }

            if (i < maxRetries) {
               await this.delay(Math.pow(2, i) * 1000); // Backoff exponencial
            }
         }
      }

      throw lastError;
   }

   private delay(ms: number): Promise<void> {
      return new Promise((resolve) => setTimeout(resolve, ms));
   }
}
```

### Upload con Progress

```typescript
export const useFileUpload = () => {
   const [progress, setProgress] = useState(0);
   const [uploading, setUploading] = useState(false);

   const upload = async (uri: string, url: string) => {
      setUploading(true);
      setProgress(0);

      try {
         const result = await httpClient.uploadFile(
            url,
            { uri, type: "image/jpeg", name: "image.jpg" },
            setProgress,
         );

         setUploading(false);
         return result;
      } catch (error) {
         setUploading(false);
         throw error;
      }
   };

   return { upload, progress, uploading };
};
```

## Navegación

[⬅️ Sincronización Estado Redux](./sincronizacion-estado-redux.md) |
[🏠 README Principal](../../README.md) |
[Testing Dispositivos y Simuladores ➡️](./testing-dispositivos-simuladores.md)
