# Porque me pasa esto:
```powershell
C:\Users\maria>WINGET SEARCH VLC
"WINGET" no se reconoce como un comando interno o externo,
programa o archivo por lotes ejecutable.
```
---

Eso significa que tu equipo no encuentra `winget` en el PATH, y casi siempre es por una de estas causas:

**1. Windows desactualizado o sin el "Instalador de aplicación"**
Winget viene incluido en Windows 10 (versión 1809 o superior) y Windows 11, pero depende del componente **Instalador de aplicación** (App Installer). Si no está instalado o está viejo, el comando no existe.

- Abre la **Microsoft Store**, busca **"Instalador de aplicación"** y pulsa *Actualizar* o *Instalar*.
- Cierra y vuelve a abrir el símbolo del sistema después.

**2. Es una instalación reciente o una versión LTSC/Server**
Algunas ediciones (como Windows 10 LTSC o Windows Server) no incluyen winget ni la Microsoft Store. En ese caso hay que instalarlo manualmente desde el repositorio oficial de GitHub (`microsoft/winget-cli`, sección *Releases*), descargando el archivo `.msixbundle` y abriéndolo.

**3. El alias de ejecución está desactivado**
Ve a **Configuración → Aplicaciones → Configuración avanzada de aplicaciones → Alias de ejecución de aplicaciones** (en Windows 10: *Aplicaciones y características → Alias de ejecución de aplicaciones*) y comprueba que **Instalador de aplicación / winget** esté activado.

**4. Terminal sin reiniciar**
Si lo acabas de instalar, cierra todas las ventanas de consola y abre una nueva.

**Cómo comprobar tu versión de Windows:** pulsa `Win + R`, escribe `winver` y mira que sea la 1809 o posterior.

Una vez resuelto, prueba con `winget --version` y luego `winget search vlc`. Por cierto, el comando funciona igual en mayúsculas o minúsculas, así que ese no es el problema.

Si después de actualizar el Instalador de aplicación sigue fallando, dime qué versión de Windows tienes (lo que sale en `winver`) y lo vemos.

# Aquí tienes una captura:

![Image](#)

---

Tu Windows 11 Home (26H2) es muy reciente y debería traer winget de serie, así que el problema no es la versión. Lo más probable es que el **Instalador de aplicación** esté sin registrar, dañado o desactualizado. Prueba en este orden:

**1. Registrar de nuevo el Instalador de aplicación**
Abre **PowerShell** (no el símbolo del sistema) y ejecuta:

```powershell
Add-AppxPackage -RegisterByFamilyName -MainPackage Microsoft.DesktopAppInstaller_8wekyb3d8bbwe
```

Espera a que termine, cierra la ventana, abre una nueva y prueba `winget --version`.

**2. Actualizar desde la Microsoft Store**
Busca **"Instalador de aplicación"** en la Store y pulsa *Actualizar*. Si te dice que está al día pero sigue sin funcionar, pasa al paso siguiente.

**3. Comprobar el alias de ejecución**
Ve a **Configuración → Aplicaciones → Configuración avanzada de aplicaciones → Alias de ejecución de aplicaciones** y asegúrate de que **Instalador de aplicación (winget.exe)** está activado.

**4. Instalarlo manualmente**
Descarga el archivo `.msixbundle` más reciente desde `https://aka.ms/getwinget` y ábrelo con doble clic (pulsa *Instalar* o *Actualizar*). Otra opción es bajarlo desde la sección *Releases* de `microsoft/winget-cli` en GitHub.

Si ninguno funciona, dime qué error te sale al ejecutar el comando del paso 1 (o al abrir el `.msixbundle`) y lo miramos.

# Ahora tengo esta salida

```powershell
PS C:\Users\maria> winget --version
winget : El término 'winget' no se reconoce como nombre de un cmdlet, función, archivo de script o programa
ejecutable. Compruebe si escribió correctamente el nombre o, si incluyó una ruta de acceso, compruebe que dicha ruta
es correcta e inténtelo de nuevo.
En línea: 1 Carácter: 1
+ winget --version
+ ~~~~~~
    + CategoryInfo          : ObjectNotFound: (winget:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException

PS C:\Users\maria>
```
---
Sigue sin aparecer, así que vamos a ver si el paquete está instalado o falta del todo. En PowerShell ejecuta:

```powershell
Get-AppxPackage -Name Microsoft.DesktopAppInstaller
```

- **Si no devuelve nada**, el Instalador de aplicación no está instalado. Pasa a la reparación de abajo.
- **Si devuelve datos**, está instalado pero el alias o el PATH fallan. Revisa que **Instalador de aplicación** esté activado en *Configuración → Aplicaciones → Configuración avanzada de aplicaciones → Alias de ejecución de aplicaciones*, y prueba a cerrar sesión y volver a entrar.

**Reparación con el método oficial de Microsoft** (también en PowerShell, pega las líneas una a una):

```powershell
Install-PackageProvider -Name NuGet -Force | Out-Null
Install-Module -Name Microsoft.WinGet.Client -Force -Repository PSGallery | Out-Null
Repair-WinGetPackageManager -AllUsers
```

Si te pregunta por confiar en el repositorio PSGallery, responde `S` o `Y`. Cuando termine, cierra la ventana, abre una nueva y prueba `winget --version`.

**Alternativa manual**, si lo anterior falla:

```powershell
cd $env:TEMP
Invoke-WebRequest -Uri https://aka.ms/getwinget -OutFile winget.msixbundle
Add-AppxPackage winget.msixbundle
```

Si da error de dependencias, el mensaje suele mencionar `VCLibs` o `UI.Xaml`. Si te sale algún error en cualquiera de los pasos, pégamelo tal cual y lo resolvemos.

# Se instaló sin errores

```powershell
Install-PackageProvider -Name NuGet -Force | Out-Null
Install-Module -Name Microsoft.WinGet.Client -Force -Repository PSGallery | Out-Null
Repair-WinGetPackageManager -AllUsers
```
---

Perfecto, eso suele indicar que la reparación se ha aplicado. Ahora toca comprobar que funciona:

1. **Cierra por completo** la ventana de PowerShell y abre una nueva (si puedes, cierra también el símbolo del sistema).
2. Ejecuta:

```powershell
winget --version
```

Si te devuelve un número de versión (algo como `v1.x.xxxxx`), ya está listo. Entonces prueba:

```powershell
winget search vlc
```

La primera vez puede pedirte que aceptes los términos de los orígenes (*msstore* y *winget*); escribe `S` o `Y` y pulsa Enter.

**Si sigue sin reconocer `winget`** después de abrir una ventana nueva, dos cosas más que probar:

- **Cerrar sesión y volver a entrar** (o reiniciar el equipo) para que Windows recargue el PATH y los alias.
- Comprobar si se instaló pero no está en el PATH, ejecutando en PowerShell:

```powershell
Get-AppxPackage -Name Microsoft.DesktopAppInstaller | Select-Object Name, Version, InstallLocation
```

Si devuelve algo, copia aquí el resultado y vemos cómo llamarlo directamente o arreglar el alias. Si no devuelve nada, avísame y probamos la instalación manual.

# Ha funcionado

¡Genial, me alegro de que ya funcione! 🎉

Ahora ya puedes usar winget para instalar VLC. Primero búscalo para confirmar el identificador exacto:

```powershell
winget search vlc
```

Y luego instálalo con:

```powershell
winget install VideoLAN.VLC
```

Un par de trucos útiles para el futuro:

- `winget upgrade` muestra qué programas tienen actualizaciones disponibles.
- `winget upgrade --all` los actualiza todos de una vez.
- `winget uninstall nombre` desinstala un programa.

Si te surge algún problema al instalar VLC o cualquier otro programa, dime y lo vemos.

# Una pregunta, ¿Es mejor instalar y actualizar programa así?

Depende de lo que valores, pero para la mayoría de la gente es una buena opción, con algunos matices.

**Ventajas de winget**
- Es rápido y cómodo: instalas o actualizas varios programas con un solo comando, sin buscar webs ni pasar por asistentes de "Siguiente, Siguiente".
- Es más seguro que descargar de buscadores: evitas webs falsas, instaladores con adware o botones de descarga engañosos. Los paquetes del repositorio de winget se validan y apuntan al instalador oficial del fabricante.
- `winget upgrade --all` centraliza las actualizaciones de muchos programas en un solo sitio, en vez de que cada uno tenga su propio actualizador.
- Es útil para reinstalar un equipo: puedes guardar la lista de programas y reinstalarlos todos de golpe.

**Limitaciones**
- No todos los programas están en el repositorio, y a veces la versión tarda unos días en actualizarse tras salir la nueva.
- No actualiza todo: algunos programas con instalador propio o instalados de otra forma no aparecen en `winget upgrade`.
- Algunas actualizaciones pueden pedirte que cierres el programa o fallar si está en uso.
- Si prefieres una interfaz gráfica, es menos cómodo que hacer clic en una web.

**Mi recomendación:** usa winget para programas comunes (VLC, navegadores, 7-Zip, etc.) y para mantenerlos al día, y descarga directamente de la web oficial lo que no encuentres. Lo importante, vayas por donde vayas, es no bajar instaladores de webs desconocidas.

Para actualizar, puedes lanzar `winget upgrade` de vez en cuando (por ejemplo una vez al mes) para ver qué hay pendiente antes de usar `--all`. Así controlas qué se actualiza.

> **BY CLAUDE SONNET 5.5 MEDIO**


