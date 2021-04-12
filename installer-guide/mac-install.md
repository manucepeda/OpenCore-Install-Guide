# Creando el instalador en macOS

* Versión soportada 0.6.8

Si bien no necesitas una nueva instalación de macOS para usar OpenCore, algunos usuarios prefieren tener una nueva instalación con sus actualizaciones del gestor de arranque.

Para comenzar, queremos obtener una copia de macOS, puedes omitir esto y dirigirte a formatear el USB si solo estás haciendo un dispositivo de arranque con OpenCore y no un instalador. Para los demás, pueden descargar macOS de la App Store o con gibMacOS

## Descargando macOS moderno

* * Este método te permite descargar macOS 10.13 y posterior, para 10.12 y anterior dirígete a [Descargando macOS: SO Legacy](#descargando-macos-so-legacy)

Desde una computadora con macOS que cumple los requerimientos de la versión del SO que quieres instalar, vé directamente a el AppStore y descarga la versión de macOS que desees, y continúa en [**Configurando el instalador**](#configurando-el-instalador)

Para las computadoras que necesiten una versión específica de macOS o que no tengan acceso a el AppStore para descargar la versión deseada, pueden usar la herramienta InstallInstallMacOS de Munki

Para correrlo, simplemente copia y pega el siguiente comando en la terminal:

```sh
mkdir ~/macOS-installer && cd ~/macOS-installer && curl -O https://raw.githubusercontent.com/munki/macadmin-scripts/main/installinstallmacos.py && sudo python installinstallmacos.py
```

![](../images/installer-guide/mac-install-md/munki.png)

Como puedes ver obtendremos una lista de instaladores de macOS. Si necesitas una versión en particular, puedes seleccionarla escribiendo el número que está al lado de ella. En este caso eligiremos 10:

![](../images/installer-guide/mac-install-md/munki-process.png)

* **macOS 11, Big Sur Note**: Debido a que este SO es bastante nuevo, siguen habiendo algunos errores a resolver en algunos sistemas. Para obtener más información dirígete aquí: [OpenCore y macOS 11: Big Sur](../extras/big-sur/README.md)
  * Para usuarios novatos recomendamos macOS 10.15, Catalina
* **Nota para GPUs Nvidia**: Recuerda verificar si tu hardware soporta sistemas operativos más nuevos (mira [limitaciones de hardware](../macos-limits.md)

Esto tomará un rato ya que estamos descargando el instalador entero (+8GB), así que recomendamos que leas el resto de la guía mientras esperas.

Una vez que termine de descargarse , encontrarás un DMG conteniendo el instalador de macOS en `~/macOS-Installer/` llamado, por ejemplo, `Install_macOS_11.1-20C69.dmg`. Móntalo y encontrarás la aplicación de instalación.

* Nota: Recomendamos que muevas la aplicación de instalación a la carpeta `/Applications`,  ya que estaremos ejecutando comandos desde ahí.
* Nota 2: Correr Cmd+Shift+G en el Finder te permitirá dirigirte fácilmente a `~/macOS-installer`

![](../images/installer-guide/mac-install-md/munki-done.png)

![](../images/installer-guide/mac-install-md/munki-dmg.png)

Desde aquí, dirígete a [Configurando el instalador](#setting-up-the-installer) para terminar con tu trabajo

## Descargando macOS: SO Legacy

* Este método permite descargar versiones mucho más antiguas de OS X, actualmente soportando todas las versiones de OS X de Intel (10.4 en adelante)

  * [macOS Legacy: Método offline](./mac-install-pkg.md)
    * 10.10-10.12 Soportado
  * [macOS Legacy: Método online(10.7-10.15 soportado)](./mac-install-recovery.md)
    * 10.7-11 Soportado
  * [macOS Legacy: Imágenes de disco](./mac-install-dmg.md)
    * 10.4-10.6 Soportado

## Configurando el instalador

Ahora formatearemos el USB para prepararlo para el instalador de macOS y OpenCore. Querremos utilizar el formato macOS Extended (HFS+) con el mapa de particiones GUID. Esto creará dos particiones, la principal, `MyVolume` y una segunda llamada `EFI`, la cual es la partición de arranque donde el firmware buscará archivos de arranque. 

* Nota: Ten en cuenta que la Utilidad de Discos muestra solamente las particiones por defecto, presiona Cmd/Win+2 para mostrar todos los dispositivos (o alternativamente presiona el botón de vista)
* Nota 2: Los usuarios que estén siguiendo la sección "macOS Legacy: Método online" pueden saltearse esto y dirigirse a [Configurando el entorno de OpenCore](#configurando-el-entorno-de-opencore)

![Formateando el USB](../images/installer-guide/mac-install-md/format-usb.png)

Luego de esto, corre el comando `createinstallmedia`, proveído por [Apple](https://support.apple.com/en-us/HT201372), Ten en cuenta que el comando está hecho para USBs formateados con el nombre `MyVolume`:

```
sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```

Esto tomará un tiempo así que si quieres ve a buscar un café o continúa leyendo la guía (en realidad no deberías estar siguiendo la guía paso a paso sin haberla leído antes)

También puedes reemplazar la ruta de el comando `createinstallmedia` con la ruta de donde está localizado el instalador que descargaste previamente, lo mismo con el nombre del disco. 

::: details Comandos createinstallmedia legacy

Extraído de la propia página de Apple: [Cómo crear un instalador de arranque para macOS](https://support.apple.com/es-es/HT201372)

```sh
# Catalina
sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume

# Mojave
sudo /Applications/Install\ macOS\ Mojave.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume

# High Sierra
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume

# Sierra
sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume

# El Capitan
sudo /Applications/Install\ OS\ X\ El\ Capitan.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume --applicationpath /Applications/Install\ OS\ X\ El\ Capitan.app

# Yosemite
sudo /Applications/Install\ OS\ X\ Yosemite.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume --applicationpath /Applications/Install\ OS\ X\ Yosemite.app

# Mavericks
sudo /Applications/Install\ OS\ X\ Mavericks.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume --applicationpath /Applications/Install\ OS\ X\ Mavericks.app --nointeraction
```

:::

## Configuración Legacy

Si tienes un sistema que no soporte arranque UEFI, mira abajo:

::: details Configurando el arranque legacy

Para comenzar, necesitas lo siguiente

* BootInstall_IA32.tool o BootInstall_X64.tool
  * Estos pueden ser encontrados en el [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/releases) debajo de  `/Utilties/LegacyBoot/`
* Tu instalador USB (creado arriba)

Dentro de la carpeta de OpenCore, dirígete a `Utilities/LegacyBoot`. Aquí encontrarás un archivo llamado `BootInstall.command`. Lo que hace esto es instalar DuetPkg al disco que eligas.

![Ubicación de BootInstall](../images/extras/legacy-md/download.png)

Ahora corre esta herramienta en terminal **con sudo** (sin esto la herramienta probablemente fallará):

```sh
# Reemplaza X64 por IA32 si tienes una CPU de 32 bits
sudo ~/Downloads/OpenCore/Utilities/legacyBoot/BootInstall_X64.tool
```

![Selección del disco/escribiendo nuevo MBR](../images/extras/legacy-md/boot-disk.png)

Esto te dará una lista de discos disponibles, elige el tuyo y se te pedirá que escribas un nuevo MBR. Elige yes`[y]` y habrás terminado!

![Instalador terminado](../images/extras/legacy-md/boot-done.png)

![EFI Base](../images/extras/legacy-md/efi-base.png)

Esto te dará una partición EFI con un archivo **bootia32** o **bootx64**

:::

## Configurando el entorno de OpenCore

Configurar el entorno de OpenCore es simple, todo lo que necesitas hacer es montar la partición EFI de tu USB. Esto es hecho automáticamente cuando formateamos un disco con GUID, pero esta partición esta desmontada por defecto, aquí es cuando nuestro amigo [MountEFI](https://github.com/corpnewt/MountEFI) entra a jugar:

![MountEFI](../images/installer-guide/mac-install-md/mount-efi-usb.png)

Te darás cuenta que cuando abrimos la partición EFI, esta está vacía. Aquí comienza lo divertido. 

![Partición EFI vacía](../images/installer-guide/mac-install-md/base-efi.png)

# Ahora, con todo esto hecho dirígete a [Configurando la EFI](./opencore-efi.md) para terminar tu trabajo.
