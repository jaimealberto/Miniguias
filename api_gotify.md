# API Gotify
Cómo trabajar con el servidor Gotify mediante API para integración con scripts y aplicaciones.

## Prerrequisitos
- Servidor Gotify en funcionamiento
- Application Token (para enviar) o Client Token (para leer/borrar)

## Tokens

| Token | Uso |
|---|---|
| Application Token | Solo POST (enviar mensajes) |
| Client Token | GET (leer) y DELETE (borrar) |

> El administrador puede crear usuarios pero no puede ver los mensajes de las aplicaciones de otros usuarios. No existe función nativa para compartir aplicaciones entre usuarios.

## ✅ 1. Enviar un mensaje

```bash
curl -X POST "http://<HOST>/message?token=<APP_TOKEN>" \
     -H "Content-Type: application/json" \
     -d '{"title": "Título", "message": "Mensaje", "priority": 5}'
```

## ✅ 2. Listar aplicaciones

```bash
curl -H "X-Gotify-Key: <CLIENT_TOKEN>" http://<HOST>/application
```

## ✅ 3. Borrar mensajes de una aplicación por nombre

```bash
#!/bin/bash
URL="http://<HOST>"
CLIENT_TOKEN="<CLIENT_TOKEN>"
APP_NAME="<nombre_app>"

APP_ID=$(curl -s -H "X-Gotify-Key: $CLIENT_TOKEN" "$URL/application" | \
         grep -Po '(?<={"id":)\d+(?=,"token":[^,]*,"name":"'"$APP_NAME"'")')

if [ -z "$APP_ID" ]; then
    echo "No se encontró ninguna aplicación con el nombre: $APP_NAME"
else
    curl -s -o /dev/null -w "%{http_code}" -X DELETE \
         "$URL/application/$APP_ID/message" \
         -H "X-Gotify-Key: $CLIENT_TOKEN"
fi
```

## ✅ 4. Borrar una aplicación por ID

```bash
curl -X DELETE "http://<HOST>/application/<APP_ID>" \
     -H "X-Gotify-Key: <CLIENT_TOKEN>"
```

## Referencias
- [REST API Gotify](https://gotify.github.io/api-docs/)
