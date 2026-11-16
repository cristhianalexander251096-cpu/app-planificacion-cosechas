# APP Empresarial · Planificación de Cosechas

Aplicación HTML preparada para publicarse en Azure Static Web Apps y conectarse al sitio de SharePoint **GESTIÓN DE PRODUCCIÓN** mediante Microsoft Graph.

## Estructura

- `index.html`: aplicación completa.
- `staticwebapp.config.json`: fallback de SPA y encabezados de seguridad.
- `manifest.webmanifest`: metadatos de aplicación web.
- `.github/workflows/azure-static-web-apps.yml`: despliegue automático desde GitHub.

## Listas requeridas en SharePoint

- `CAT_Piscinas`
- `CAT_Motivos_Cosecha`
- `CAT_EstadoPiscinas`
- `Cosechas`
- `HistorialCosechas`

## 1. Registrar la aplicación en Microsoft Entra ID

1. Abra Microsoft Entra admin center.
2. Vaya a **App registrations > New registration**.
3. Nombre sugerido: `APP_Planificacion_Cosechas_Web`.
4. Seleccione cuentas de este directorio organizacional únicamente.
5. En **Authentication**, agregue una plataforma **Single-page application (SPA)**.
6. Agregue como Redirect URI la URL de Azure Static Web Apps, por ejemplo:
   `https://nombre-generado.azurestaticapps.net`
7. Agregue también el dominio personalizado cuando exista.
8. En **API permissions > Microsoft Graph > Delegated permissions**, agregue:
   - `User.Read`
   - `Sites.ReadWrite.All`
9. Conceda consentimiento de administrador si la política del tenant lo requiere.
10. Copie el **Application (client) ID** y **Directory (tenant) ID**.

No cree ni coloque un Client Secret en este proyecto. Es una SPA con autenticación delegada.

## 2. Crear Azure Static Web Apps

1. Cree un repositorio GitHub y copie en la raíz todos los archivos de esta carpeta.
2. En Azure Portal, cree un recurso **Static Web App**.
3. Seleccione el repositorio y la rama `main`.
4. Build preset: `Custom`.
5. App location: `/`.
6. API location: vacío.
7. Output location: vacío.
8. Azure puede crear su propio workflow. Si utiliza el workflow incluido, guarde el token de implementación como secreto de GitHub:
   `AZURE_STATIC_WEB_APPS_API_TOKEN`.

## 3. Configurar la aplicación publicada

Abra la URL publicada y entre en **Conexión empresarial**:

- Tenant ID: ID del directorio.
- Client ID: ID de la aplicación registrada.
- Hostname: por ejemplo `empresa.sharepoint.com`.
- Ruta del sitio: ruta real del sitio, por ejemplo `/sites/GESTIONDEPRODUCCION`.
- Modo: `Microsoft Graph`.

Después seleccione **Guardar configuración** e **Iniciar sesión Microsoft 365**.

## Consideraciones de SharePoint

- Los nombres visibles de las listas deben coincidir exactamente.
- El usuario autenticado necesita acceso a las listas.
- La columna de búsqueda `Piscina` en `Cosechas` se envía mediante `PiscinaLookupId`.
- Si un nombre interno de columna difiere del supuesto en `index.html`, revise la respuesta de Microsoft Graph y ajuste el objeto `fields`.

## Seguridad

- No almacene secretos en JavaScript.
- Use HTTPS.
- Limite el acceso en SharePoint mediante grupos empresariales.
- Para producción con privilegio mínimo, reemplace `Sites.ReadWrite.All` por permisos más restringidos usando `Sites.Selected` y una arquitectura con backend seguro. La versión actual usa permisos delegados para operar con la identidad del usuario.
