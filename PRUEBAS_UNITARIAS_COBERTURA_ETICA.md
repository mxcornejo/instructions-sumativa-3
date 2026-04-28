# Pruebas Unitarias: Cobertura, Módulos Críticos y Aspectos Éticos

## 1. Descripción general del proyecto

El sistema se compone de dos módulos Maven independientes:

| Módulo                      | Rol                                                                 | Paquete raíz                |
| --------------------------- | ------------------------------------------------------------------- | --------------------------- |
| **proyecto** (frontend MVC) | Interfaz web con Thymeleaf, se autentica contra el backend vía REST | `com.duoc.seguridadcalidad` |
| **backend** (API REST)      | Expone endpoints de recetas y autenticación JWT                     | `com.duoc.backend`          |

---

## 2. Pruebas unitarias desarrolladas

### 2.1 Módulo `proyecto` (frontend MVC)

| Archivo de prueba                   | Clase bajo prueba                | Casos cubiertos                                                                                                                                                                                                                                                            |
| ----------------------------------- | -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SeguridadcalidadApplicationTest`   | `SeguridadcalidadApplication`    | Carga del contexto de Spring                                                                                                                                                                                                                                               |
| `AppConfigTest`                     | `AppConfig`                      | Registro de bean `RestTemplate`                                                                                                                                                                                                                                            |
| `SecurityConfigTest`                | `SecurityConfig`                 | Acceso a rutas públicas (`/`, `/login`, `/buscar`, `/receta/{id}`), redirección ante receta inexistente, carga del filtro de seguridad                                                                                                                                     |
| `HomeControllerTest`                | `HomeController`                 | Renderizado de vista `index` con recetas recientes y populares                                                                                                                                                                                                             |
| `LoginControllerTest`               | `LoginController`                | Visualización de formulario de login y de error de credenciales                                                                                                                                                                                                            |
| `RecipeControllerTest`              | `RecipeController`               | Detalle de receta encontrada y redirección ante receta no encontrada                                                                                                                                                                                                       |
| `SearchControllerTest`              | `SearchController`               | Búsqueda sin parámetros (retorna todo), búsqueda por nombre, por tipo de cocina, por país y dificultad, por ingrediente, presencia de atributos de búsqueda en el modelo                                                                                                   |
| `LoginDtoTest`                      | `LoginRequest` / `LoginResponse` | Constructores, getters y setters; validación de campos no nulos                                                                                                                                                                                                            |
| `RecipeModelTest`                   | `Recipe`                         | Getters, setters y todos los campos del modelo                                                                                                                                                                                                                             |
| `BackendAuthenticationProviderTest` | `BackendAuthenticationProvider`  | Credenciales válidas → token JWT en sesión, credenciales nulas → `BadCredentialsException`, error 401 del backend → `BadCredentialsException`, error de servicio → `AuthenticationServiceException`, `supports()` con tipo correcto e incorrecto, cuerpo de respuesta nulo |
| `RecipeServiceTest`                 | `RecipeService`                  | `getAllRecipes`, `getRecentRecipes`, `getPopularRecipes`, `getRecipeById` (encontrada / no encontrada), manejo de errores de conexión al backend                                                                                                                           |

### 2.2 Módulo `backend` (API REST)

| Archivo de prueba            | Clase bajo prueba        | Casos cubiertos                                                                                                                                                                                                                                                       |
| ---------------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BackendApplicationTest`     | `BackendApplication`     | Carga del contexto de Spring Boot                                                                                                                                                                                                                                     |
| `SecurityConfigTest`         | `SecurityConfig`         | Configuración del filtro de seguridad, beans de seguridad expuestos                                                                                                                                                                                                   |
| `AuthControllerTest`         | `AuthController`         | Login con credenciales válidas → 200 + token Bearer, login con credenciales inválidas → 401 con cuerpo de error                                                                                                                                                       |
| `RecetaControllerTest`       | `RecetaController`       | Listado de todas las recetas, búsqueda con filtros, obtención por ID (encontrada / no encontrada), recientes, populares                                                                                                                                               |
| `GlobalExceptionHandlerTest` | `GlobalExceptionHandler` | `BadCredentialsException` → HTTP 401, `UsernameNotFoundException` → HTTP 401, presencia de clave `"error"` en el cuerpo de respuesta                                                                                                                                  |
| `JwtAuthFilterTest`          | `JwtAuthFilter`          | Petición sin cabecera Authorization → cadena continúa, cabecera sin prefijo `Bearer` → cadena continúa, token válido → usuario autenticado en `SecurityContext`, token malformado → `JwtException` capturado sin autenticar, token expirado/inválido → contexto vacío |
| `UserDetailsServiceImplTest` | `UserDetailsServiceImpl` | Carga de usuario existente, excepción cuando no existe                                                                                                                                                                                                                |
| `DataInitializerTest`        | `DataInitializer`        | Carga inicial de datos cuando el repositorio está vacío, omisión cuando ya existen datos                                                                                                                                                                              |
| `JwtServiceTest`             | `JwtService`             | Generación de token no nulo, formato de tres partes, extracción de username, validación con mismo usuario → `true`, validación con usuario diferente → `false`, token expirado → `ExpiredJwtException`                                                                |
| `RecetaServiceTest`          | `RecetaService`          | `getAll` con resultados y vacía, `getById` encontrado y no encontrado, `getRecientes`, `getPopulares` ordenados por popularidad descendente, `buscar` sin filtros y con filtro de nombre                                                                              |

---

## 3. Resultados de cobertura (JaCoCo)

Los datos provienen de los reportes `jacoco.csv` generados por `mvn verify`.

### 3.1 Módulo `proyecto`

| Paquete / Clase                          | Instrucciones cubiertas | Instrucciones perdidas | % instrucciones | Ramas perdidas |
| ---------------------------------------- | :---------------------: | :--------------------: | :-------------: | :------------: |
| `model.Recipe`                           |           94            |           0            |    **100 %**    |       0        |
| `controller.HomeController`              |           22            |           0            |    **100 %**    |       0        |
| `controller.LoginController`             |            5            |           0            |    **100 %**    |       0        |
| `controller.RecipeController`            |           22            |           0            |    **100 %**    |       0        |
| `dto.LoginRequest`                       |           23            |           0            |    **100 %**    |       0        |
| `dto.LoginResponse`                      |           31            |           0            |    **100 %**    |       0        |
| `config.AppConfig`                       |            7            |           0            |    **100 %**    |       0        |
| `service.RecipeService`                  |           191           |           0            |    **100 %**    |       5        |
| `controller.SearchController`            |           87            |           3            |   **96,7 %**    |       7        |
| `security.BackendAuthenticationProvider` |           104           |           3            |   **97,2 %**    |       1        |
| **`config.SecurityConfig`**              |           94            |         **7**          |   **93,1 %**    |       0        |

> **Menor cobertura en `proyecto`:** `SecurityConfig` (93,1 %) y `SearchController` (96,7 %).

### 3.2 Módulo `backend`

| Paquete / Clase                    | Instrucciones cubiertas | Instrucciones perdidas | % instrucciones | Ramas perdidas / total |
| ---------------------------------- | :---------------------: | :--------------------: | :-------------: | :--------------------: |
| `exception.GlobalExceptionHandler` |           18            |           0            |    **100 %**    |          0/0           |
| `service.DataInitializer`          |           401           |           0            |    **100 %**    |          0/4           |
| `service.RecetaService`            |           96            |           0            |    **100 %**    |          0/14          |
| `config.SecurityConfig`            |           123           |           0            |    **100 %**    |          0/0           |
| `controller.RecetaController`      |           36            |           0            |    **100 %**    |          0/0           |
| `controller.AuthController`        |           44            |           0            |    **100 %**    |          0/0           |
| `entity.User`                      |           15            |           0            |    **100 %**    |          0/0           |
| `security.UserDetailsServiceImpl`  |           32            |           0            |    **100 %**    |          0/0           |
| `service.JwtService`               |           79            |           0            |    **100 %**    |  **1/4 → 75 %** ramas  |
| **`security.JwtAuthFilter`**       |           70            |           0            |    **100 %**    | **2/10 → 80 %** ramas  |

> **Menor cobertura en `backend`:** `JwtAuthFilter` (80 % cobertura de ramas) y `JwtService` (75 % cobertura de ramas). La cobertura de instrucciones es 100 % en ambos módulos.

---

## 4. Análisis de las brechas de cobertura

### 4.1 `SecurityConfig` (proyecto, 93,1 %)

Las 7 instrucciones no cubiertas corresponden a la cadena de configuración de rutas que requieren autenticación (`/receta/**` con rol `USER` y `ADMIN`), dado que las pruebas ejecutan las rutas con los filtros desactivados (`addFilters = false`). Esto es una limitación del entorno de prueba, no de la lógica de negocio.

### 4.2 `SearchController` (proyecto, 96,7 %)

Las 3 instrucciones y 7 ramas no cubiertas pertenecen a rutas condicionales cuando todos los parámetros de búsqueda llegan vacíos de forma simultánea con ciertos valores de borde (cadena vacía vs. `null`). Se agregaron pruebas para los casos principales, pero combinaciones de cinco parámetros opcionales generan un espacio de ramas exponencial.

### 4.3 `JwtAuthFilter` (backend, 80 % ramas)

Las 2 ramas no cubiertas representan rutas defensivas ante un `null` en el `SecurityContext` en combinación con un token previamente cargado en la misma petición (caso de doble procesamiento). Esta situación no puede ocurrir en producción con un único filtro en la cadena.

### 4.4 `JwtService` (backend, 75 % ramas)

La rama no cubierta corresponde al caso en que `extractClaim` recibe un tipo de claim no esperado; esta ruta está protegida por el manejo de excepción superior en `JwtAuthFilter`.

---

## 5. Cómo las pruebas aseguran la ausencia de sesgos y salvaguardan los aspectos éticos

### 5.1 No discriminación en el acceso

Las pruebas de `AuthController` y `BackendAuthenticationProvider` verifican que el sistema autentica **exclusivamente con base en credenciales** (usuario y contraseña), sin considerar ningún otro atributo del solicitante. Los casos de prueba demuestran que:

- Credenciales correctas → acceso garantizado (HTTP 200, token emitido).
- Credenciales incorrectas → rechazo uniforme (HTTP 401, sin información adicional que permita inferir si el usuario existe).

Esto previene ataques de enumeración de usuarios y asegura que el rechazo es **equitativo** para cualquier identidad.

### 5.2 Neutralidad en los resultados de búsqueda

`SearchControllerTest` y `RecetaServiceTest` demuestran que el motor de búsqueda opera sobre **los mismos filtros objetivos** (nombre, tipo de cocina, ingrediente, país, dificultad) para todas las consultas. Las pruebas confirman:

- Sin parámetros → se devuelven **todas** las recetas sin excepción ni priorización implícita.
- Con parámetros → el filtrado es estrictamente sobre los campos declarados, sin lógica oculta que pueda privilegiar o suprimir contenido de determinados orígenes culturales o nacionales.

Esto garantiza que el catálogo de recetas —que incluye preparaciones de múltiples culturas y países— sea igualmente accesible mediante la misma lógica de recuperación.

### 5.3 Integridad y no manipulación del token JWT

`JwtServiceTest` valida que:

- Un token generado para el usuario `"chef"` **no es válido** para el usuario `"user2"` (prueba `isTokenValid_tokenDeOtroUsuario_retornaFalse`).
- Un token expirado lanza `ExpiredJwtException`, impidiendo acceso con credenciales caducadas.

Esto protege la identidad de los usuarios y evita que un agente malintencionado pueda suplantar a otro, preservando la **equidad en el control de acceso**.

### 5.4 Protección frente a errores sin revelar información sensible

`GlobalExceptionHandlerTest` comprueba que ante `BadCredentialsException` y `UsernameNotFoundException` la respuesta siempre es HTTP 401 con el mensaje de la excepción como cuerpo. Esto garantiza:

- No se filtra si un nombre de usuario existe en el sistema.
- El mensaje de error es **consistente** independientemente de la causa real del fallo, evitando sesgo informativo que favorezca a atacantes.

### 5.5 Manejo seguro de sesiones

`BackendAuthenticationProviderTest` verifica que el token JWT se almacena **únicamente en la sesión HTTP del solicitante** tras una autenticación exitosa. La prueba `authenticate_respuestaCuerpoNulo_lanzaBadCredentials` asegura que un cuerpo de respuesta nulo del backend no produce un estado de autenticación inconsistente, lo cual podría derivar en escalada de privilegios no intencionada.

### 5.6 Resumen de garantías éticas cubiertas por las pruebas

| Principio ético                                               | Prueba(s) que lo validan                                          |
| ------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Equidad de acceso** (acceso sin discriminación)             | `AuthControllerTest`, `BackendAuthenticationProviderTest`         |
| **Neutralidad de resultados** (sin sesgo cultural/geográfico) | `SearchControllerTest`, `RecetaServiceTest`                       |
| **Protección de identidad** (no suplantación)                 | `JwtServiceTest` (`isTokenValid_tokenDeOtroUsuario_retornaFalse`) |
| **Privacidad** (no enumeración de usuarios)                   | `GlobalExceptionHandlerTest`, `BackendAuthenticationProviderTest` |
| **Integridad de sesión** (no escalada de privilegios)         | `BackendAuthenticationProviderTest`                               |
| **Transparencia de acceso a datos** (sin filtrado oculto)     | `SearchControllerTest`, `RecetaControllerTest`                    |

---

## 6. Herramientas y metodología utilizadas

- **Framework de pruebas:** JUnit 5 (`@ExtendWith(MockitoExtension.class)`)
- **Mocking:** Mockito (`@Mock`, `@InjectMocks`, `@MockitoBean`)
- **Aserciones:** AssertJ (`assertThat`, `assertThatThrownBy`)
- **Pruebas de capa web:** Spring MVC Test (`MockMvc`, `@WebMvcTest`)
- **Cobertura:** JaCoCo (reportes HTML y CSV generados con `mvn verify`)
- **Análisis estático:** SonarQube (análisis ejecutado con `mvn sonar:sonar`)
- **Principio de diseño de pruebas:** cada test cubre un único comportamiento observable (_one assertion per test concept_), facilitando el diagnóstico de regresiones y la auditabilidad del comportamiento del sistema.
