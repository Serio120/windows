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

# ppppp
