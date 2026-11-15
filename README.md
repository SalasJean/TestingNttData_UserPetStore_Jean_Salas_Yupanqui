# Suite de Pruebas en postman - bruno de APIs (CRUD PetStore & Flujo eCommerce - opcional)

Este proyecto contiene la suite de pruebas en postman y tambien burno para evaluar dos servicios RESTful: el **CRUD completo de Usuarios en Swagger Petstore (petstore.swagger.io/v2) ** y el flujo de **Autenticación con Bearer Token en DummyJSON eCommerce**.

- **Autor:** Jean Salas Yupanqui

- **Herramienta:** Postman (Collection v2.1) - bruno

- **Tipo de Pruebas:** Funcionales, Validación de Esquemas, Pruebas Positivas  y Pruebas Negativas 


##  Configuración de Variables de Entorno (Environment)

Para ejecutar esta colección de forma dinámica se requiere un entorno en Postman con las siguientes variables:

| Variable | Tipo | Valor Inicial / Ejemplo | Descripción |
| - | - | - | - |
| `baseUrl` | URL | `https://petstore.swagger.io/v2` | Servidor base de Swagger PetStore |
| `baseDummy` | URL | `https://dummyjson.com` | Servidor base de eCommerce DummyJSON |
| `username` | String | `piero` | Usuario dinámico para rutas del CRUD |
| `password` | String | `admin1234` | contrasenia dinamica para las rutas del crud |
| `token` | Secret | *(Generado dinámicamente)* | Token extraído en el Login del eCommerce osea en el proceso de login|



## Matriz de Endpoints (Mapeo CRUD y Auth)

| Servicio | Método | Endpoint | Acción / Operación | Código Esperado |
| - | - | - | - | - |
| **PetStore** | `POST` | `\{\{baseUrl\}\}/user` | **Create:** Crear nuevo usuario | `200 OK` |
| **PetStore** | `GET` | `\{\{baseUrl\}\}/user/\{\{username\}\}` | **Read:** Consultar usuario por username | `200 OK` / `404` |
| **PetStore** | `PUT` | `\{\{baseUrl\}\}/user/\{\{username\}\}` | **Update:** Modificar datos del usuario | `200 OK` |
| **PetStore** | `DELETE` | `\{\{baseUrl\}\}/user/\{\{username\}\}` | **Delete:** Eliminar usuario del sistema | `200 OK` / `404` |
| **PetStore** | `GET` | `\{\{baseUrl\}\}/user/login` | **Login:** Iniciar sesión de usuario | `200 OK` |
| **eCommerce** | `POST` | `\{\{baseDummy\}\}/auth/login` | **Auth:** Iniciar sesión y generar JWT | `200 OK` |
| **eCommerce** | `GET` | `\{\{baseDummy\}\}/auth/me` | **Profile:** Consultar perfil con Bearer Token | `200 OK` / `401` |



## Estrategia de Pruebas 

Para garantizar una cobertura integral de aseguramiento de calidad, se diseñaron dos grupos de pruebas:

### 1. Pruebas Positivas (Happy Path - Escenarios Exitosos)

Se valida que las APIs respondan con el formato y los códigos de éxito esperados ante datos correctos:

- **CP-01 (`createUser`):** Registro exitoso del usuario `piero` y verificación de tiempo de respuesta óptimo.

- **CP-02 (`GetUserPorNombre`):** Consulta exitosa del usuario previamente creado mediante la variable `\{\{username\}\}`.

- **CP-03 (`ActualizarUser`):** Modificación del usuario a nombre de `Carlos` devolviendo `200 OK`.

- **CP-04 (`LoginApiEcomerce`):** Inicio de sesión con credenciales válidas (`emilys` / `emilyspass`) y extracción automática del token de acceso a la variable `\{\{token\}\}`.

- **CP-05 (`GetUserProfileEcomerce`):** Consumo del endpoint protegido `/auth/me` enviando el `Bearer Token`, validando que el perfil retornado pertenezca a `emilys` y contenga `id` y `email` válido.


### 2. Pruebas Negativas (Unhappy Path - Escenarios de Error Controlado)

Como parte de las buenas prácticas de QA, se ejecutan peticiones diseñadas intencionalmente para validar cómo responde la API ante entradas erróneas:

- **CN-01 (`GetUserPorNombre - 404 Not Found`):**

  - **Acción:** Consultar un usuario inexistente (ejemplo: `\{\{baseUrl\}\}/user/usuario\_que\_no\_existe\_999`).

  - **Resultado Esperado:** Código HTTP `404 Not Found` con mensaje `"User not found"`.

- **CN-02 (`LoginApiEcomerce - 400 Bad Request / Credenciales Inválidas`):**

  - **Acción:** Enviar contraseña errónea (`password: "clave\_incorrecta"`).

  - **Resultado Esperado:** Código HTTP `400 Bad Request` indicando credenciales inválidas.

- **CN-03 (`GetUserProfileEcomerce - 401 Unauthorized / Token Inválido`):**

  - **Acción:** Consultar `/auth/me` enviando un token corrupto o vacío en el encabezado `Authorization`.

  - **Resultado Esperado:** Código HTTP `401 Unauthorized` bloqueando el acceso al recurso privado.

- **CN-04 (Fallo de Aserción de Rendimiento):**

  - **Acción:** El test de `createUser` exige un tiempo `\< 200ms`. Si la red o el servidor demora 350ms, el test arroja un fallo controlado demostrando que la aserción vigila la degradación de rendimiento.


## Evidencias de Pruebas (Test Execution)

### A. Ejecución de Casos Positivos (Happy Path)

#### 1. Creación de Usuario (`POST /user`)
![alt text](image-1.png)
#### 2. Login user/login
![alt text](image-2.png)
#### 3. Get {{baseDummy}}/auth/login correcto
![alt text](image-3.png)
#### 4. Get {{baseDummy}}/auth/login incorecto
![alt text](image-4.png)
#### 5 Get user por nombre
![alt text](image-5.png)
#### 6 Get user por nombre pero mal datos
![alt text](image-6.png)
#### 7 Delete Borrar datos con combre mal y bien
![alt text](image-7.png)
![alt text](image-8.png)
#### 8 Put Actualizar datos
![alt text](image-9.png)
> Se muestra la extracción del token y su uso exitoso con Bearer Token, con todos los tests en verde (`PASS`).




