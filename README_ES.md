# SEUS AI Backend — API inicial

Este paquete contiene un backend FastAPI de ejemplo para conectarse a PostgreSQL y registrar usuarios y solicitudes de video.

## Funciones incluidas
- `GET /health`: comprobación básica.
- `GET /v1/db-check`: prueba conexión a PostgreSQL (requiere token).
- `POST /v1/users`: registra/actualiza un usuario de prueba.
- `POST /v1/videos`: guarda una solicitud de generación en la tabla `video_generations`.
- `GET /v1/videos/{generation_id}`: consulta el estado.
- `GET /v1/videos?user_id=...`: lista solicitudes de un usuario.
- `GET /v1/me/plan?user_id=...`: consulta información de suscripción.

## Limitaciones importantes
- **Todavía no genera videos reales**: `POST /v1/videos` solo registra una tarea en PostgreSQL. No hay un proveedor conectado.
- El token Bearer compartido es solo para pruebas. No lo uses como autenticación final para usuarios de una app pública.
- No hay procesamiento de pagos. Los planes son datos de la base; no validan una suscripción real.
- Antes de producción necesitas autenticar usuarios con JWT/OAuth, autorización por propietario en cada endpoint, rate limits, validación de contenido, almacenamiento seguro de archivos, logs sin datos sensibles, migraciones y copias de seguridad.
- No expongas PostgreSQL directamente a Internet. La app Android debe hablar con la API HTTPS, nunca con la base de datos directamente.
- La clave del proveedor de video debe quedarse en el servidor y nunca dentro de la APK.

## Preparación local
1. Instala Python 3.12 y PostgreSQL.
2. Ejecuta el esquema SQL de `SEUS_AI_Base_Datos` sobre una base nueva.
3. Copia `.env.example` a `.env`.
4. Define `DATABASE_URL` y genera un `API_TOKEN` largo y aleatorio.
5. Instala dependencias: `pip install -r requirements.txt`.
6. Inicia: `uvicorn app.main:app --reload --host 127.0.0.1 --port 8000`.
7. Abre `/docs` en el navegador local para probar endpoints.

## Crear token de prueba
Genera un valor aleatorio largo; por ejemplo, en Python:
`python -c "import secrets; print(secrets.token_urlsafe(32))"`

No reutilices el token de ejemplo ni subas el `.env` a GitHub.

## Arquitectura
Android → API HTTPS (este backend) → PostgreSQL → proveedor de video IA / almacenamiento de objetos.

Para habilitar generación real, implementa un adaptador específico para un proveedor y el procesamiento asíncrono de tareas, además de moderación y límites de gasto. El precio de S/ 1 al mes no garantiza generaciones ilimitadas con proveedores que cobran por uso.
