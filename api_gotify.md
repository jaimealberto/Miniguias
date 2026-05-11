# <img src="img/gotify.png" width="50" height="50"> Notas Api Gotify <img src="img/avatar.png" alt="Yo" width="50" height="50">

Como trabajar con el servidor Gotify utilizando la API para integración con scripts, aplicaciones, etc.
En Gotify, el sistema de permisos es por usuario. Los usuarios solo pueden ver y gestionar las aplicaciones y los mensajes que ellos mismos han creado. Esto puede ser una limitación a tener en cuenta. No existe una función nativa para "compartir" una aplicación existente con otro usuario o dar permisos de visión de forma directa





## **⚠️ Notas**
El administrador puede crear usuarios, pero no puede ver los mensajes de las aplicaciones de otros usuarios.

Resumen de Tokens

- Application Token: Solo para POST (enviar).
- Client Token: Para GET (leer) y DELETE (borrar). 

## Links relacionados:
- [Rest-Api Gotify](https://gotify.github.io/api-docs/)




<!-- 

curl -X POST "http://172.16.1.22/message?token=AyFFa.NyFW.srmj" \
     -H "Content-Type: application/json" \
     -d '{"title": "Todo ok", "message": "Respaldo finalizado", "priority": 5}'


#!/bin/bash

# Configuración
URL="http://172.16.1.22"
CLIENT_TOKEN="CAtuSVFaA1iMpU2"
APP_NAME="JanetTaskOk"

# 1. Obtener el ID de la aplicación buscando por su nombre
APP_ID=$(curl -s -H "X-Gotify-Key: $CLIENT_TOKEN" "$URL/application" | \
         grep -Po '(?<={"id":)\d+(?=,"token":[^,]*,"name":"'"$APP_NAME"'")')

# 2. Verificar si se encontró el ID y borrar los mensajes
if [ -z "$APP_ID" ]; then
    echo "No se encontró ninguna aplicación con el nombre: $APP_NAME"
else
    echo "Borrando mensajes de la app '$APP_NAME' (ID: $APP_ID)..."
    RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" -X DELETE \
         "$URL/application/$APP_ID/message" \
         -H "X-Gotify-Key: $CLIENT_TOKEN")

    if [ "$RESPONSE" == "200" ]; then
        echo "¡Mensajes eliminados con éxito!"
    else
        echo "Error al borrar. Código HTTP: $RESPONSE"
    fi
fi

curl -X DELETE "http://172.16.1.22/application/3" \
     -H "X-Gotify-Key: CAtuSVFaA1iMpU2"





Links 
[Jaimealberto.io](https://jaimealberto.io)
[Punto de fichero Underlines](#underlines)
[Fichero externo](directorio/fichero)

## H2

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.

```bash
caja de código
```
## Guia gráfica

![](img/updateW11.png)
-->