# Documentación API BioPad LT Pro

## Configuración Base
- **IP del Dispositivo**: 192.168.100.46
- **Puerto**: 8090
- **Protocolo**: HTTP
- **Contraseña**: BioP4d
- **Variables de Entorno**: `{{url}}` y `{{pass}}`

---

## VARIABLES DE ENTORNO DE POSTMAN

```json
{
  "url": "http://192.168.100.46:8090",
  "pass": "BioP4d"
}
```

---

## 1. OBTENCIÓN DE EVENTOS

### 1.1 Buscar Registros por Rango de Fechas
```http
GET /newFindRecords
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `personId` (string): ID de persona específica o "-1" para todos
- `startTime` (string): Fecha inicio "YYYY-MM-DD HH:MM:SS" (usar "0" si no se consulta por tiempo)
- `endTime` (string): Fecha fin "YYYY-MM-DD HH:MM:SS" (usar "0" si no se consulta por tiempo)

**Parámetros opcionales:**
- `length` (integer): Cantidad de registros (1-1000, default: 1000)
- `model` (integer): Tipo de reconocimiento:
  - `-1`: Todos los tipos (default)
  - `0`: Reconocimiento facial
  - `1`: Doble autenticación Rostro&Tarjeta
  - `2`: Comparación Rostro&Tarjeta
  - `3`: Reconocimiento por tarjeta
  - `4`: Botón de salida
  - `5`: Verificación remota
  - `6`: Verificación por contraseña
  - `7`: Doble verificación Rostro&Contraseña
  - `8`: Verificación por código QR
  - `9`: Verificación por huella dactilar
  - `10`: Doble verificación QR&Rostro
  - `11`: Verificación Tarjeta&Contraseña
  - `12`: Verificación por cédula/ID
  - `13`: Verificación Rostro&Huella
- `order` (integer): `1` para orden ascendente por tiempo (default: descendente)
- `index` (integer): Número de página (inicia en 0)

**Ejemplo:**
```
GET {{url}}/newFindRecords?pass={{pass}}&personId=1&startTime=2025-06-05 02:26:00&endTime=2025-06-05 02:26:05
```

**Respuesta esperada:**
```json
{
    "code": "LAN_SUS-0",
    "data": {
        "pageInfo": {
            "index": 0,
            "length": 1000,
            "size": 1,
            "total": 1
        },
        "records": [
            {
                "aliveType": 2,
                "attendance": null,
                "dstOffset": 0,
                "id": "16",
                "idcardNum": "",
                "identifyType": 1,
                "isImgDeleted": 1,
                "isPass": false,
                "maskState": 3,
                "model": 0,
                "name": "david garcia",
                "passTimeType": 3,
                "passbackTriggerType": null,
                "path": "",
                "permissionTimeType": 3,
                "personId": "1",
                "recModeType": 3,
                "recType": 1,
                "state": 1,
                "time": 1749068760965,
                "type": 0,
                "workCode": ""
            }
        ]
    },
    "msg": "search successful",
    "result": 1,
    "success": true
}
```
   
---

## 2. ENVÍO DE USUARIOS

### 2.1 Crear Usuario
```http
POST /person/create
Content-Type: application/x-www-form-urlencoded
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `person` (JSON string): Objeto con datos del usuario

**Parámetros opcionales (solo dispositivos extranjeros):**
- `face1` (JSON string): Datos de imagen facial 1
- `face2` (JSON string): Datos de imagen facial 1 
- `face3` (JSON string): Datos de imagen facial 1

**Estructura completa del objeto person:**
```json
{
  "id": "001",
  "name": "Nombre Completo",
  "idcardNum": "",
  "facePermission": 2,
  "idCardPermission": 2,
  "faceAndCardPermission": 2,
  "faceAndQrCodePermission": 2,
  "iDNumbetPermission": 2,
  "qrCode": "",
  "tag": "",
  "phone": "",
  "password": "",
  "passwordPermission": 2,
  "fingerPermission": 1,
  "qrCodePermission": 1,
  "cardAndPasswordPermission": 1,
  "faceAndPasswordPermission": 1,
  "fingerAndPasswordPermission": 1
}
```

**Estructura del objeto face (dispositivos extranjeros):**
```json
{
  "faceId": "001",
  "url": "url_de_imagen",
  "base64": "imagen_en_base64",
  "isEasyWay": "false",
  "bbox": {
    "bottom": 206,
    "empty": false,
    "left": 100,
    "right": 407,
    "top": 95
  }
}
```

**Valores de permisos:**
- `0`: Deshabilitado
- `1`: Habilitado
- `2`: Super usuario

**Ejemplo completo:**
```
POST {{url}}/person/create
Content-Type: application/x-www-form-urlencoded

pass={{pass}}&person={
"id":"1007",
"name":"Juan Carlos Bedolla",
"idcardNum":"",
"facePermission":2,
"idCardPermission":2,
"iDPermission":2,
"qrCode":"",
"tag":"",
"phone":"",
"password":"",
"passwordPermission":2,
"fingerPermission":1,
"qrCodePermission":1,
"cardAndPasswordPermission":1
}
```

**Respuesta esperada:**
```json
{ 
    "code": "LAN_SUS-0", 
    "msg": "passtime set successfully", 
    "result": 1, 
    "success": true 
} 
```


---

## 3. EXTRACCIÓN DE USUARIOS CON BIOMETRÍA

### 3.1 Buscar Usuario por ID
```http
GET /person/find
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `id` (string): ID del usuario o `-1` para consultar todos los IDs

**Ejemplo:**
```
GET {{url}}/person/find?pass={{pass}}&id=1
```

**Respuesta esperada:**
```json
{
    "code": "LAN_SUS-0",
    "data": [
        {
            "cardAndPasswordPermission": 1,
            "createTime": 1748386445280,
            "faceAndCardPermission": 1,
            "faceAndQrCodePermission": 1,
            "facePermission": 2,
            "fingerPermission": 1,
            "id": "1",
            "idCardPermission": 1,
            "idcardNum": "",
            "name": "david garcia",
            "password": "",
            "passwordPermission": 1,
            "qrCode": "",
            "qrCodePermission": 1,
            "role": 0,
            "scheduleId": "",
            "tag": ""
        }
    ],
    "msg": "search successful",
    "result": 1,
    "success": true
}
```

### 3.2 Buscar Datos Biométricos Faciales
```http
POST /face/find
Content-Type: application/x-www-form-urlencoded
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `personId` (string): ID de la persona

**Parámetros opcionales (dispositivos extranjeros):**
- `base64Enable` (integer):
  - `1`: No obtener base64 (default)
  - `2`: Obtener base64

**Ejemplo:**
```
POST {{url}}/face/find
Content-Type: application/x-www-form-urlencoded

pass={{pass}}&personId=1
```

**Respuesta esperada:**
```json
{
    "code": "LAN_SUS-0",
    "data": [
        {
            "croplmgPath": "/data/record/RegisterPhoto//2025-05-27/16/165326_049.jpg",
            "faceId": "aee2e3a38079445a91356e6729bfc96c",
            "feature": "SO_LONG_STRING",
            "featureKey": "6F303DC6417ACCFBB583AA91133D1513",
            "path": "/data/record/RegisterPhoto//2025-05-27/16/165326_049.jpg",
            "personId": "1",
            "rect": "{\"bottom\":523,\"empty\":false,\"left\":105,\"right\":416,\"top\":212}"
        }
    ],
    "msg": "Photo search successfully",
    "result": 1,
    "success": true
}
```

### 3.3 Descargar Imagen Biométrica
```http
GET /download/image
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `filename` (string): Ruta completa del archivo de imagen

**Ejemplo:**
```
GET {{url}}/download/image?pass={{pass}}&filename=/data/record/RegisterPhoto//2025-05-27/16/165326_049.jpg
```

**Respuesta esperada:**
```Imagen Solicitada
```

---

## 4. GESTIÓN DE FECHA Y HORA

### 4.1 Establecer Fecha y Hora (Timestamp Unix)
```http
POST /setTime
Content-Type: application/x-www-form-urlencoded
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `timestamp` (string): Timestamp Unix en milisegundos

**Comportamiento:**
- El dispositivo actualizará su tiempo cada minuto
- Si NO está conectado a internet: mantiene el tiempo configurado
- Si está conectado a internet: se sincroniza con el tiempo de red cada minuto

**Ejemplo:**
```
POST {{url}}/setTime
Content-Type: application/x-www-form-urlencoded

pass={{pass}}&timestamp=1572278400000
```

**Respuesta esperada:**
```json
{
    "code": "LAN_SUS-0",
    "msg": "The setting is successful. If the device is not connected to the public network, this time will take effect; if the device is connected to the public network, the public network time will be used again",
    "result": 1,
    "success": true
} 
```

### 4.2 Configurar Zona Horaria
```http
POST /device/setTimeZone
Content-Type: application/x-www-form-urlencoded
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo
- `timeZone` (string): Zona horaria en formato GMT (ej: "GMT+6", "GMT-5")

**Ejemplo:**
```
POST {{url}}/device/setTimeZone
Content-Type: application/x-www-form-urlencoded

pass={{pass}}&timeZone=GMT+6
```

**Respuesta esperada:**
```json
{
    "code": "LAN_SUS-0",
    "msg": "set successfully",
    "result": 1,
    "success": true
}
```

**Nota:** No existe endpoint para obtener la fecha/hora actual del dispositivo.

---

## 5. REINICIO DEL DISPOSITIVO

### 5.1 Reiniciar Dispositivo
```http
POST /restartDevice
Content-Type: application/x-www-form-urlencoded
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo

**Ejemplo:**
```
POST {{url}}/restartDevice
Content-Type: application/x-www-form-urlencoded

pass={{pass}}
```

**Respuesta esperada:**
```json
{
    "code": "LAN_SUS-0",
    "msg": "Operation is successful, the device is about to restart",
    "result": 1,
    "success": true
}
```

---

## 6. FUNCIONES AUXILIARES

### 6.1 Obtener Clave del Dispositivo
```http
GET /getDeviceKey
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo

**Ejemplo:**
```
GET {{url}}/getDeviceKey?pass={{pass}}
```

### 6.2 Consultar Configuración del Dispositivo
```http
GET /device/config
```

**Parámetros requeridos:**
- `pass` (string): Contraseña del dispositivo

**Ejemplo:**
```
GET {{url}}/device/config?pass={{pass}}
```

---

## FORMATO DE DATOS BIOMÉTRICOS

### Características de la Biometría del BioPad LT Pro:

**1. Imágenes Faciales:**
- **Formato**: JPG/JPEG
- **Almacenamiento**: Archivos físicos en rutas específicas del dispositivo
- **Acceso**: A través del endpoint `/download/image`
- **Base64**: Solo disponible en dispositivos en el extranjero con parámetro `base64Enable=2`

**2. Templates Biométricos:**
- **Facial**: Algoritmo propietario de reconocimiento facial
- **Codificación**: Templates matemáticos codificados (no Base64)
- **Extracción**: A través de `/face/find`

**3. Soporte Base64:**
- **Disponibilidad**: Solo en dispositivos en el extranjero (versiones internacionales)
- **Activación**: Parámetro `base64Enable=2` en `/face/find`
- **Creación**: Soporte de imágenes Base64 en `/person/create` con parámetros `face1`, `face2`, `face3`

---

## DETECCIÓN ANTI-SPOOFING

### Comportamiento de Seguridad:
El dispositivo BioPad LT Pro incluye **detección anti-spoofing** que:

- **Rechaza fotos estáticas**: No aprueba el acceso cuando se presenta una imagen impresa o en pantalla de una persona registrada
- **Detección de vida**: Requiere que sea una persona real (no una fotografía)
- **Algoritmos avanzados**: Analiza profundidad, movimiento y características biométricas 3D
- **Prevención de fraude**: Evita el acceso no autorizado mediante fotos o videos

---

## CÓDIGOS DE RESPUESTA COMUNES

- `0`: Éxito
- `-1`: Error de autenticación (contraseña incorrecta)
- `-2`: Parámetros faltantes o incorrectos
- `-3`: Usuario no encontrado
- `-4`: Error de dispositivo
- `-5`: Error de red/comunicación

---

## NOTAS IMPORTANTES

1. **Autenticación**: Todos los endpoints requieren el parámetro `pass`
2. **Biometría Base64**: Desactivado por default
3. **Imágenes**: Se almacenan como archivos JPG en el dispositivo
4. **Fecha/Hora**: Solo configuración via timestamp Unix (no consulta)
5. **Zona Horaria**: Configurable independientemente del timestamp
6. **Anti-Spoofing**: Activo por defecto, rechaza fotos estáticas
7. **Capacidad**: Hasta 10,000 usuarios y 100,000 registros
8. **Conectividad**: HTTP sobre TCP/IP (puerto 8090)
9. **Paginación**: Disponible en consultas de registros
10. **Sincronización**: Tiempo se sincroniza con red si hay conexión a internet

---

## FUNCIONES ADICIONALES

- `/setWifi ` - Configuración de red inalámbrica
- `/setNetInfo ` - Configuración de red cableada
- `/person/delete` - Eliminar usuario
- `/person/update` - Actualizar Usuario