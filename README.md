# InvitationMavi15
Página web para la invitación del cumpleaños de 15 de Mavi

## Álbum colaborativo (Google Drive)

La sección **Álbum colaborativo** permite:

- Mostrar un botón **Sube tu fotografía** (Google Form).
- Leer fotos públicas y mostrarlas automáticamente en la invitación.

Configuración en `index.html`:

```html
window.DRIVE_ALBUM_CONFIG = {
	uploadFormUrl: "https://forms.gle/YOUR-FORM-ID",
	feedUrl: "", // URL pública JSON con fotos (opción recomendada)
	folderId: "", // opcional, usar junto con apiKey
	apiKey: "", // opcional, API key de Google Drive
	thumbnailSize: "w1000", // opcional, tamaño miniatura Google Drive
};
```

### Formato esperado para `feedUrl`

Puede ser un array directo o un objeto con `photos`, `items` o `files`.

```json
{
	"photos": [
		{
			"name": "Foto 1",
			"imageUrl": "https://...",
			"fullUrl": "https://..."
		}
	]
}
```

También acepta strings (`"https://..."`) como URLs de imagen.

> Seguridad: si usás `apiKey` de Google Drive, restringila por dominio/referrer en Google Cloud Console.
