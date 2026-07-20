# 🎛️ KeyForge

**Convierte tu móvil en un mando a distancia para tu PC con Windows.**

Crea botones que abren programas, pulsan atajos de teclado, controlan OBS, Spotify, tus luces y mucho más. Escaneas un QR con el móvil y ya tienes tu propio "stream deck" — sin comprar hardware, sin cuentas, sin nube: todo corre en tu red local.

---

## ✨ Qué puede hacer

- **Botones y carpetas** ilimitados, con emojis, imágenes y colores
- **Macros multi-paso**: cada botón puede hacer varias cosas seguidas, con espera configurable entre paso y paso (ej. `Win+R` → esperar 400 ms → escribir `cmd` → Enter)
- **Asistente de acciones**: escribe lo que quieres ("bajar volumen", "luz roja") y te sugiere qué usar, con los campos ya preconfigurados
- **Probar sin guardar**: ejecuta la macro en construcción desde el editor y ve al instante si funciona
- **Controles deslizantes** de volumen (general o por programa): arrastras en vez de dar toques
- **Favoritos** con estrella (fila fija arriba del mando), **modo organizar** manteniendo pulsado (arrastra los botones como en iOS), sonidos y vibración al pulsar
- **Modos de botón**: un toque, mantener pulsado (repite), o alternar (on/off con pasos distintos)
- **Panel de estado del PC** en el móvil: CPU, RAM, temperaturas y GPU con gráficas
- **Seguridad**: PIN opcional para la red, tokens enmascarados, freno anti fuerza bruta, aviso al importar backups con scripts
- **Copias de seguridad** por archivo o **traspaso a otro PC por QR** (enlace temporal de un solo uso)
- **Tutorial guiado** la primera vez, estilo videojuego
- **19 integraciones** (detalle abajo)

---

## 📋 Requisitos mínimos

| Qué | Detalle |
|---|---|
| **Sistema** | Windows 10 u 11 (las acciones usan PowerShell y utilidades de Windows) |
| **Node.js** | Versión 18 o superior — [nodejs.org](https://nodejs.org) |
| **Red** | El PC y el móvil deben estar en la **misma red WiFi/LAN** |
| **Navegador móvil** | Cualquiera moderno (Chrome, Safari, Firefox) |

> El firewall de Windows preguntará la primera vez si permites a Node.js aceptar conexiones — di que sí en **redes privadas**, o el móvil no podrá conectar.

## 🚀 Instalación

1. Descarga o clona este repositorio
2. Doble clic en **`Iniciar.bat`** — la primera vez instala dependencias, crea un acceso directo y arranca el servidor en la bandeja del sistema
3. En el PC se abre la **configuración**: `http://localhost:8080/config.html`
4. En la pestaña **Conexión** hay un QR: escanéalo con el móvil y se abre el **mando**
5. Crea tu primer botón y a disfrutar

Para desarrollo:
```bash
cd core
npm install
npm start        # arranca el servidor
npm test         # ejecuta la batería de pruebas (9 tests)
```

---

## 🔌 Integraciones y sus requisitos

Cada integración se activa (o no) desde la pestaña **Integraciones** — todas son opcionales y cada tarjeta explica su configuración paso a paso dentro de la app. Resumen de requisitos:

### Streaming

| Integración | Requisitos |
|---|---|
| **OBS Studio** | OBS 28+ con el servidor WebSocket activado (Herramientas → Ajustes del servidor WebSocket). Necesitas host, puerto (4455 por defecto) y la contraseña que muestra OBS |
| **Streamlabs Desktop** | Activar la API remota en Ajustes → Remote Control; usa el mismo protocolo que OBS (puerto 59650) |
| **Twitch** | Una app registrada en [dev.twitch.tv](https://dev.twitch.tv/console) (Client ID + Secret) y vincular tu cuenta desde la propia tarjeta |
| **YouTube Live** | Proyecto propio en Google Cloud con la YouTube Data API v3 activada (Client ID + Secret OAuth); se vincula desde la tarjeta |
| **StreamElements** | Tu JWT token (streamelements.com → cuenta → Show secrets) |
| **VTube Studio** | VTube Studio abierto con su API activada (puerto 8001); el primer uso pide permiso dentro de VTube y se guarda el token |

### Mensajería y avisos

| Integración | Requisitos |
|---|---|
| **Discord** | Un webhook del canal (Ajustes del canal → Integraciones → Webhooks). *Solo envía mensajes; silenciarte en Discord se hace con botones de teclas + Keybinds globales de Discord* |
| **Telegram** | Un bot creado con @BotFather (token) y tu chat ID |
| **ntfy (push al móvil)** | La app gratuita [ntfy](https://ntfy.sh) en el móvil, suscrita a un tema con el nombre que elijas |

### Casa y luces

| Integración | Requisitos |
|---|---|
| **Philips Hue** | El puente Hue en tu red; la vinculación es automática (te pide pulsar el botón físico del puente) |
| **Home Assistant** | URL de tu instancia y un token de acceso de larga duración (perfil de usuario → tokens) |
| **Elgato Key Light** | La IP de la luz en tu red (visible en Elgato Control Center o en el router) |
| **WLED (tiras LED)** | La IP del controlador WLED (la misma que usas en su interfaz web) |

### Sistema y audio

| Integración | Requisitos |
|---|---|
| **Volumen por aplicación** | [SoundVolumeView](https://www.nirsoft.net/utils/sound_volume_view.html) de NirSoft (gratis, un solo .exe): descárgalo y pon su ruta en la tarjeta. **También lo usan los controles deslizantes** |
| **Estado del PC (temperaturas/GPU)** | [LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) abierto con su servidor web activado (Options → Remote Web Server). Sin él, se muestran solo CPU y RAM |
| **Control de procesos** | Nada extra — usa `taskkill`/`start` de Windows |
| **Webhook universal** | Nada — llama a cualquier URL/API que quieras (IFTTT, Zapier, tu propio server...) |
| **PowerShell** | Nada — ejecuta el script que escribas, sin restricciones (⚠️ para quien sabe lo que hace) |

---

## 🔐 Seguridad (léelo si compartes la WiFi)

- **Sin PIN**: cualquiera en tu red puede usar el mando. Bien para casa, mal para redes compartidas.
- **Con PIN** (Ajustes → Seguridad): el móvil lo pide para ejecutar, y las acciones sensibles (cambiar configuración, exportar) lo exigen desde la red. Desde el propio PC (localhost) nunca hace falta.
- Los **tokens y contraseñas** de integraciones nunca viajan en claro por la API (se enmascaran), y hay un freno progresivo contra fuerza bruta del PIN.
- Los **backups exportados** sí llevan los secretos en claro (para poder restaurarlos): trátalos como algo sensible.
- Al **importar** un backup con pasos de PowerShell o webhooks, la app te avisa expresamente — no importes backups de desconocidos.
- El tráfico va por HTTP en tu LAN (hay HTTPS opcional con certificado autofirmado en el puerto 8443).

## 🧪 Pruebas

```bash
cd core && npm test
```
Arranca un servidor de prueba y verifica seguridad (PIN, enmascarado de secretos, preservación al reguardar) y funcionamiento básico. 9 pruebas.

## 📁 Estructura

```
keyforge/
├─ Iniciar.bat            ← doble clic y listo
├─ LÉEME.txt
└─ core/
   ├─ server.js           ← Express + rutas de la API
   ├─ tray.js             ← icono de bandeja
   ├─ lib/                ← estado, seguridad, ejecución, hardware
   ├─ integrations/       ← catálogo y ejecución de las 19 integraciones
   ├─ public/             ← config.html (PC), remote.html (móvil), portada
   └─ test/test.js        ← batería de pruebas
```

## ❓ Preguntas rápidas

**¿Funciona fuera de casa?** No por diseño: solo red local. Es una decisión de seguridad.

**¿Mac o Linux?** No — las acciones dependen de PowerShell y utilidades de Windows.

**¿El móvil necesita app?** No, es una página web. Puedes "Añadir a pantalla de inicio" y se comporta como una app.

**¿Win+R funciona?** Sí — las combinaciones con la tecla Windows usan un mecanismo especial (`keybd_event`), porque el estándar de Windows (SendKeys) no la soporta.

---

Hecho con cariño, un móvil y muchas ganas de no levantarse de la silla. 🛋️
