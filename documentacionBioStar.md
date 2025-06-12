# Documentación de la Colección Postman: BioStar 2 API

Esta colección está diseñada para interactuar con la API REST de Suprema BioStar 2, un sistema de control de acceso biométrico y gestión de usuarios/dispositivos. La documentación sigue el orden actual de la colección, proporcionando una explicación detallada de cada endpoint, parámetros, métodos HTTP, ejemplos de solicitud/respuesta, scripts de pre-solicitud y test.

---

## VARIABLES DE ENTORNO DE POSTMAN
Ejemplo de variables usadas durante la ejecución:

| Variable        | Valor           | Descripción                   |
|----------------|------------------|-------------------------------|
| `bsid`         | `abc123xyz`      | Token de sesión               |
| `iterIDVal`    | `101`            | ID temporal de iteración     |
| `nuid`         | `1002`           | Nuevo ID de usuario generado |



```json
{
  "url": "https://127.0.0.1",
}
```

---

## Autorización

### Login

**Método**: `POST`  
**URL**: `{{baseUrl}}/api/login`  
**Descripción**: Autentica al usuario en la sesión actual. Devuelve un token de sesión (`bs-session-id`) que se utiliza para operaciones posteriores.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "login_id": "admin",
  "password": "admin"
}
```

**Scripts de Prueba**:
```javascript
// Guarda bs-session-id en variable de colección
if (pm.response.to.have.status(200)) {
    let params = pm.response.headers.get('bs-session-id');
    pm.collectionVariables.set("bsid", params);
}

pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Success",
  "session_id": "abc123xyz"
}
```

---

### Logout

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/logout`  
**Descripción**: Cierra la sesión activa. No requiere parámetros adicionales.

**Scripts de Prueba**:
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Logout successful"
}
```

---

## Usuarios

### Listar Todos los Usuarios

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/users?group_id=1&limit=0&offset=0&order_by=user_id:false&last_modified=0`  
**Descripción**: Obtiene una lista de todos los usuarios registrados en la base de datos de BioStar 2.

**Parámetros de Consulta**:

| Parámetro         | Valor Predeterminado | Descripción |
|------------------|----------------------|-------------|
| `group_id`       | `1`                  | Filtra por grupo de usuarios |
| `limit`          | `0`                  | Límite de registros a devolver (0 para todos) |
| `offset`         | `0`                  | Desplazamiento de registros |
| `order_by`       | `user_id:false`      | Ordenar por campo (false: ascendente, true: descendente) |
| `last_modified`  | `0`                  | Filtrar por fecha de modificación |

**Respuesta Ejemplo**:
```json
{
  "users": [
    {"id": "1", "name": "Admin"},
    {"id": "2", "name": "John Doe"}
  ]
}
```

---

### Ver Detalles de un Usuario

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/users/:id`  
**Descripción**: Muestra información detallada sobre un usuario específico.

**Parámetros de URL**:
- `:id`: ID del usuario (por ejemplo: `3`)

**Script de Pre-Solicitud**:
```javascript
if (pm.iterationData.get("iterIDVal") === undefined) {
    pm.collectionVariables.set("iterIDVal", 2);
} else {
    var val = pm.iterationData.get("iterIDVal");
    pm.collectionVariables.set("iterIDVal", val);
}
```

**Respuesta Ejemplo**:
```json
{
  "id": "3",
  "name": "Jane Smith",
  "email": "jane@example.com",
  "groups": ["All Users"]
}
```

---

### Crear Nuevo Usuario

**Método**: `POST`  
**URL**: `{{baseUrl}}/api/users`  
**Descripción**: Crea un nuevo usuario en la base de datos.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "name": "Nuevo Usuario",
  "user_id": "1002",
  "access_groups": [{"id": "1"}],
  "department": "IT",
  "email": "nuevo@ejemplo.com"
}
```

**Scripts de Test**:
```javascript
const jsonData = pm.response.json();
pm.collectionVariables.set("nuid", jsonData.User.user_id);
```

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Usuario creado",
  "User": {
    "id": "101",
    "user_id": "1002"
  }
}
```

---

### Actualizar Usuario

**Método**: `PUT`  
**URL**: `{{baseUrl}}/api/users/:id`  
**Descripción**: Actualiza la información de un usuario existente.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "name": "Usuario Actualizado",
  "email": "actualizado@ejemplo.com"
}
```

**Parámetros de URL**:
- `:id`: ID del usuario a actualizar

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Usuario actualizado"
}
```

---

### Eliminar Usuario(s)

**Método**: `DELETE`  
**URL**: `{{baseUrl}}/api/users?id={{iterIDVal}}`  
**Descripción**: Elimina uno o más usuarios por su ID.

**Parámetros de Consulta**:
- `id`: ID del usuario o múltiples IDs separados por `+` (ej: `1001+1002`)

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Usuarios eliminados"
}
```

---

## Dispositivos

### Listar Tipos de Dispositivos

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/device_types`  
**Descripción**: Obtiene una lista de tipos de dispositivos compatibles con BioStar 2.

**Respuesta Ejemplo**:
```json
[
  {"id": "35", "name": "Device Type A"},
  {"id": "40", "name": "Device Type B"}
]
```

---

### Ver Información del Dispositivo

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/devices/:id`  
**Descripción**: Devuelve información detallada sobre un dispositivo específico.

**Parámetros de URL**:
- `:id`: ID del dispositivo (ej: `542353521`)

**Respuesta Ejemplo**:
```json
{
  "id": "542353521",
  "name": "Main Entrance Reader",
  "type": "Biometric Device"
}
```

---

### Ver Configuración del Dispositivo

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/devices/:id/config`  
**Descripción**: Obtiene la configuración actual del dispositivo.

**Parámetros de URL**:
- `:id`: ID del dispositivo

**Respuesta Ejemplo**:
```json
{
  "config": {
    "language": "English",
    "volume": "50",
    "use_voice": "false"
  }
}
```

---

### Actualizar Firmware

**Método**: `POST`  
**URL**: `{{baseUrl}}/api/firmwares/update`  
**Descripción**: Actualiza el firmware de uno o más dispositivos.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "FirmwareFile": {
    "filename": "firmware_v1.1.1.bin",
    "device_type": "35"
  },
  "DeviceCollection": {
    "rows": [
      {"id": "538201713", "device_type_id": {"id": "35"}}
    ]
  }
}
```

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Firmware actualizado"
}
```

---

## Eventos

### Listar Tipos de Eventos

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/event_types`  
**Descripción**: Obtiene una lista de todos los tipos de eventos registrados en BioStar 2.

**Parámetros Comunes**:
- `setting_all`: `true` para listar todos los eventos.
- `is_break_glass`: `false` para excluir eventos especiales.

**Respuesta Ejemplo**:
```json
[
  {"id": "1", "name": "Acceso Permitido"},
  {"id": "2", "name": "Acceso Denegado"}
]
```

---

### Buscar Eventos

**Método**: `POST`  
**URL**: `{{baseUrl}}/api/events/search`  
**Descripción**: Permite buscar eventos filtrando por condiciones y ordenar resultados.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "conditions": [
    {
      "column": "datetime",
      "operator": 3,
      "values": ["2022-03-01T15:00:00Z", "2022-03-02T16:59:59Z"]
    }
  ],
  "orders": [
    {
      "column": "datetime",
      "descending": true
    }
  ]
}
```

**Operadores Soportados**:
- `0`: Igual (`EQUAL`)
- `1`: No igual (`NOT_EQUAL`)
- `2`: Contiene (`CONTAINS`)
- `3`: Entre (`BETWEEN`)
- `4`: Like (`LIKE`)
- `5`: Mayor que (`GREATER`)
- `6`: Menor que (`LESS`)

**Respuesta Ejemplo**:
```json
{
  "events": [
    {"datetime": "2022-03-02T15:30:00Z", "user": "John Doe", "type": "Acceso Permitido"}
  ]
}
```

---

### Actualizar Historial de Alertas

**Método**: `POST`  
**URL**: `{{baseUrl}}/api/alerts`  
**Descripción**: Registra manualmente alertas generadas por eventos específicos.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "event_id": {"datetime": "2022-03-02T15:30:00Z"},
  "status": 1,
  "user_id": "1",
  "ack_message": "Alerta atendida"
}
```

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Alerta registrada"
}
```

---

## Importar/Exportar Datos

### Exportar Archivo de Datos

**Método**: `GET`  
**URL**: `{{baseUrl}}/api/users/data_export`  
**Descripción**: Genera un archivo comprimido `.tgz` con datos exportados de usuarios.

**Respuesta Ejemplo**:
```json
{
  "file": "BioStar2_20220124_173247.tgz"
}
```

---

### Importar Archivo de Datos

**Método**: `POST`  
**URL**: `{{baseUrl}}/api/users/data_import`  
**Descripción**: Importa datos desde un archivo previamente exportado.

**Cuerpo de Solicitud (raw JSON)**:
```json
{
  "file": {
    "originalName": "BioStar2_20220124_173247.tgz",
    "name": "BioStar2_20220124_173247.tgz"
  }
}
```

**Respuesta Ejemplo**:
```json
{
  "code": "0",
  "message": "Datos importados correctamente"
}
```
---

## NOTAS IMPORTANTES

1. **Autenticación**: Requiere autenticación mediante bs-session-id obtenido tras iniciar sesión.
2. **Biometría Base64**: hay soporte para imágenes y plantillas codificadas en Base64. (por ejemplo, raw_image), pero no se especifica que esté deshabilitado por defecto.
3. **Imágenes**: Se almacenan como archivos JPG o PNG. No se especifica si se guardan directamente en el dispositivo.
4. **Fecha/Hora**: existen referencias a fechas en formato estándar como "2022-06-15T15:00:00.000Z".
5. **Zona Horaria**: Configurable independientemente del timestamp
6. **Anti-Spoofing**: Hay referencias a configuraciones de detección de máscaras (enable_mask) y detección dinámica (enable_dynamic_roi), lo cual sugiere soporte para anti-spoofing.
7. **Capacidad**: Usuarios: hasta 30 000. Tarjetas (cards): hasta 30 000, tanto en modo 1:N como 1:1. Huellas dactilares: hasta 100 000 (registro 1:N y 1:1). Rostros (face): si el firmware es ≥ 1.4, soporta 4 000 rostros en reconocimiento 1:N, y hasta 30 000 rostros en 1:1
8. **Conectividad**: Usa HTTP/HTTPS sobre TCP/IP. Los puertos mencionados son 80 (HTTP) y 443 (HTTPS).
9. **Paginación**: Disponible en consultas mediante parámetros `limit` y `offset`.
10. **Sincronización**: Se menciona la posibilidad de sincronizar con servidores AD (Active Directory) y dispositivos, lo cual implica algún nivel de sincronización temporal.
---


## CONSULTAS ADICIONALES

- **Access Group**: Consultar APIs relacionadas con los grupos de acceso.
- **Access Level**: Consultar APIs relacionadas con los niveles de acceso.
- **User Groups**: Listar todos los grupos de usuarios registrados.
- **Device Group**: Listar todos los grupos de dispositivos registrados.
- **Door Groups**: Ver todos los grupos de puertas disponibles.
- **Doors**: Ver detalles de una puerta.
- **Elevator Groups**: Crear grupos de elevadores.
- **Elevators**: Crear elevadores.
- **Floor Levels**: Ver niveles de piso registrados.
- **Schedules**: Ver todos los horarios registrados.
- **Holiday Groups**: Ver todos los grupos de días festivos.
- **Trigger Actions**: Ver configuraciones de Trigger & Actions.
- **Setting**: Consultar configuraciones generales de BioStar 2.
- **Cards**: Ver todos los tipos de tarjetas disponibles.
- **Zones**: Ver todas las zonas configuradas.
- **Backup**: Ver configuración de respaldo del sistema.

---

## 📌 CONSULTAS ADICIONALES

Listado breve de las API disponibles para distintos módulos en BioStar 2:

### 🔐 Accesos

* **Grupos de Acceso**: Consultar APIs relacionadas con los grupos de acceso (Access Groups).
* **Niveles de Acceso**: Consultar APIs relacionadas con los niveles de acceso (Access Levels).
* **Horarios**: Ver todos los horarios registrados.
* **Grupos de Puertas**: Ver todos los grupos de puertas disponibles.
* **Puertas**: Ver detalles de una puerta.

### 👥 Usuarios y Grupos

* **Grupos de Usuarios**: Listar todos los grupos de usuarios registrados.
* **Tarjetas**: Ver todos los tipos de tarjetas disponibles.

### 🏢 Dispositivos y Zonas

* **Grupos de Dispositivos**: Listar todos los grupos de dispositivos registrados.
* **Zonas**: Ver todas las zonas configuradas.

### 🛗 Elevadores

* **Elevadores**: Crear elevadores.
* **Grupos de Elevadores**: Crear grupos de elevadores.
* **Niveles de Piso**: Ver niveles de piso registrados.

### 📅 Calendario y Configuración

* **Grupos de Festivos**: Ver todos los grupos de días festivos.
* **Acciones y Eventos**: Ver configuraciones de Trigger & Actions.
* **Configuración del Sistema**: Consultar configuraciones generales de BioStar 2.
* **Respaldo**: Ver configuración de respaldo del sistema.

---

