# Problemas de post instalación

* Versión soportada: 0.6.8

* [iMessage y Siri roto](#imessage-y-siri-roto)
* [No funciona el Audio](#no-funciona-el-audio)
* [Reinicio de la BIOS o eres enviado al modo seguro después de reiniciar/apagar](#reinicio-de-la-bios-o-eres-enviado-al-modo-seguro-despues-de-reiniciar-o-apagar)
* [Touchpad Synaptics basado en ps2 no funciona](#touchpad-synaptics-basado-en-ps2-no-funciona)
* [Arreglo para teclas de teclado PS2 que no paran de ser presionadas en Dell](arreglo-para-teclas-de-teclado-ps2-que-no-paran-de-ser-presionadas-en-dell)
* [Falta la aceleración de la GPU de macOS en AMD X570](#falta-la-aceleracion-de-la-gpu-de-macos-en-amd-x570)
* [DRM roto](#drm-roto)
* [Memory Modules Misconfigured en MacPro7,1](#memory-modules-misconfigured-en-macpro71)
* [Aplicaciones crasheando en AMD](#aplicaciones-crasheando-en-amd)
* [No hay salida de temperatura/ventiladores](#no-hay-salida-de-temperaturaventiladores)
* [AssetCache Content Caching unavailable en máquinas virtuales](#assetcache-content-caching-unavailable-en-maquinas-virtuales)
* [Error "You can't change the startup disk to the selected disk"](#error-you-cant-change-the-startup-disk-to-the-selected-disk)
* [Sistemas Coffee Lake no iniciándose luego de la suspensión del sistema](#sistemas-coffee-lake-no-iniciandose-luego-de-la-suspension-del-sistema)
* [No se puede controlar el Volumen/Brillo en monitores externos](#no-se-puede-controlar-el-volumenbrillo-en-monitores-externos)
* [Deshabilitando el SIP](#deshabilitando-el-sip)
* [Deshacer snapshots de APFS](#deshacer-snapshots-de-apfs)

## iMessage y Siri roto

Dirígete a la sección [Arreglando iServices](https://inyextciones.github.io/OpenCore-Post-Install/universal/iservices.html) para obtener más información

## No funciona el Audio

Dirígete a la seccion [Arreglando el Audio con AppleALC](https://dortania.github.io/OpenCore-Post-Install/)

## Reinicio de la BIOS o eres enviado al modo seguro despues de reiniciar o apagar

Refiérete a la sección de [Arreglando resets del RTC/CMOS](https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html)

## Touchpad Synaptics basado en ps2 no funciona

Puedes intentar usar [SSDT-Enable_DynamicEWMode.dsl](https://github.com/acidanthera/VoodooPS2/blob/master/Docs/ACPI/SSDT-Enable_DynamicEWMode.dsl).
Primero, debes abrir el administrador de dispositivos y dirigirte a la siguiente sección:

```
Administrador de dispositivos -> Mouse y otros dispositivos señaladores -> Click derecho -> Propiedades -> Detalles > Nombre del dispositivo BIOS
```

Luego agarra [SSDT-Enable_DynamicEWMode.dsl](https://github.com/acidanthera/VoodooPS2/blob/master/Docs/ACPI/SSDT-Enable_DynamicEWMode.dsl)
Por defecto, este usa PCI0.LPCB.PS2K para la ruta, por lo que debes renombrarlo acorde a tu caso.

```c
External (_SB_.PCI0.LPCB.PS2K, DeviceObj) <- Renombra esto
    Name(_SB.PCI0.LPCB.PS2K.RMCF, Package()  <- Renombra esto
```

Luego compilalo con MaciASL, copia a tu carpeta OC/ACPI, y agrégalo a tu config. Luego de esto deberías estar listo.

* Nota: Por más que esto funcionará en la mayoría de los casos, el trackpad puede laguearse mucho y podrías no tener la posibilidad de usar los botones físicos ([más detalles](https://github.com/acidanthera/bugtracker/issues/890)). Si puedes vivir sin el trackpad, esto puede llegar a ser mejor:

Encuentra la ruta ACPI de tu mouse (mira arriba), y luego agarra [SSDT-DisableTrackpadProbe.dsl](https://github.com/acidanthera/VoodooPS2/blob/master/Docs/ACPI/SSDT-DisableTrackpadProbe.dsl). Por defecto, este usa PCI0.LPCB.PS2K así que tienes que cambiarlo a tu ruta ACPI específica si es necesario:

```c
External (_SB_.PCI0.LPCB.PS2K, DeviceObj) <- Renombra esto
    Name(_SB.PCI0.LPCB.PS2K.RMCF, Package() <- Renombra esto
```

## Arreglo para teclas de teclado PS2 que no paran de ser presionadas en Dell

Para aquellos que tengan problemas en torno a teclas que no se sueltan (es decir, que se quedan apretadas indefinidamente), querrán habilitar el perfil de Dell en VoodooPS2. Primero que nada, querrás encontrar la ruta ACPI de tu teclado en el administrador de dispositivos:

```
Administrador de dispositivos -> Teclados -> Click derecho -> Propiedades -> Detalles > Nombre del dispositivo BIOS
```

Luego de esto, agarra [SSDT-KEY-DELL-WN09.dsl](https://github.com/acidanthera/VoodooPS2/blob/master/Docs/ACPI/SSDT-KEY-DELL-WN09.dsl) y cambia la ruta ACPI a la que encontraste arriba si es necesario:

```c
External (_SB_.PCI0.LPCB.PS2K, DeviceObj) <- Renombra esto
    Method(_SB.PCI0.LPCB.PS2K._DSM, 4) <- Renombra esto
```

## Falta la aceleración de la GPU de macOS en AMD X570

Verifica lo siguiente:

* La GPU está capacitada para UEFI (GTX 7XX/2013+)
* CSM está desactivado en la BIOS
* Estás forzando el link speed de PCIe 3.0

## DRM roto

Dirígete a la página de [Arreglando el DRM](https://inyextciones.github.io/OpenCore-Post-Install/universal/drm.html)

## Memory Modules Misconfigured en MacPro7,1

Agrega [MacProMemoryNotificationDisabler kext](https://github.com/IOIIIO/MacProMemoryNotificationDisabler/releases/) a `EFI/OC/Kexts` y `Kernel -> Add`. Ten en cuenta que este quirk tiene un quirk raro y requiere de WhateverGreen para funcionar correctamente.

* Nota: Este kexts son conocidos por crear inestabilidad, si recives crashes aleatorios/se congela aleatoriamente, por favor elimina este kext.

## Aplicaciones crasheando en AMD

~~Fácil de arreglar, compra Intel~~

Con AMD, cada vez que Apple llama a funciones específicas de la CPU, la aplicación no funcionará o se bloqueará por completo. Estas son algunas aplicaciones y la forma de "arreglarlas":

* Los productos de Adobe no siempre funcionan
  * Algunas formas de arreglarlos se pueden encontrar aquí: [Adobe Fixes](https://adobe.amd-osx.com/)
  * Ten en cuenta que estas correcciones solo deshabilitan la funcionalidad, no son realmente correcciones
* Las máquinas virtuales que se ejecutan fuera del marco de AppleHV no funcionarán (es decir: Parallels 15, VMware)
  * VirtualBox funciona bien ya que no usa AppleHV
  * VMware 10 y versiones anteriores también pueden funcionar
  * Se sabe que Parallels 13.1.0 y versiones anteriores también funcionan
* Docker roto
  * El docker toolbox es la única solución, ya que se basa en VirtualBox, muchas funciones no están disponibles con esta versión
* IDA Pro no se instala
  * Hay una verificación específica de Intel en el instalador, la aplicación en sí probablemente esté bien
* Las páginas web en CPUs 15/16h se crashean
  * Sigue las instrucciones aquí luego de UPDATE 5: [Corregir páginas web](https://www.insanelymac.com/forum/topic/335877-amd-mojave-kernel-development-and-testing/?do=findComment&comment=2661857)

## No hay salida de temperatura/ventiladores

Algunas cosas a tener en cuenta:

* Los menúes de iStat aún no son compatibles con las lecturas de MacPro7,1
* Los sensores incluidos de VirtualSMC no son compatibles con AMD

Para iStat, tendrás que esperar a una actualización. Para usuarios de AMD, podrán usar:

* [SMCAMDProcessor](https://github.com/trulyspinach/SMCAMDProcessor/releases)
  * Aún en la versión de beta inicial, pero se ha realizado un gran trabajo, ten en cuenta que se ha probado principalmente en Ryzen
* [FakeSMC3_with_plugins](https://github.com/CloverHackyColor/FakeSMC3_with_plugins/releases)

**Nota para AMD con FakeSMC**:

* La compatibilidad con FileVault requiere más trabajo con FakeSMC
* Asegúrate de que no haya otros kexts SMC presentes, específicamente kexts como [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)

## Suspensión crasheando en AMD

Esto se ve generalmente en las CPUs AMD que usan el controlador USB del chipset, específicamente para la serie Ryzen y más reciente. La forma principal de saber si tienes problemas con esto es verificar los logs después de suspenderla o luego de que se reactiva:

* En terminal:
  * `log show --last 1d | grep "Wake reason"` verifícalo

Debería resultar en algo así:

```
Sleep transition timed out after 180 seconds while calling power state change callbacks. Suspected bundle: com.apple.iokit.IOUSBHostFamily.
```

Puedes verificar qué controlador es XHC0 a través de IOReg y verificar la ID del proveedor (1022 para el chipset AMD). La solución para este problema de suspensión es:

* Evita el chipset USB (lo ideal es configurar `_STA = 0x0` para deshabilitar el controlador directamente con un SSDT)
* Corrige las propiedades de alimentación de USBX a lo que espera el controlador

## AssetCache Content Caching unavailable en máquinas virtuales

Errores como:

```bash
$ sudo AssetCacheManagerUtil activate
AssetCacheManagerUtil[] Failed to activate content caching: Error Domain=ACSMErrorDomain Code=5 "virtual machine"...
```

surgen debido a que sysctl expone el indicador `VMM`.

Aplica el parche del kernel [VmAssetCacheEnable](https://github.com/ofawx/VmAssetCacheEnable) para para disfrazar ese flag y permitir el funcionamiento normal.

## Error "You can't change the startup disk to the selected disk"

Esto es comúnmente causado por una configuración irregular de particiones en el disco de Windows, específicamente que EFI no es la primera partición. Para arreglar esto, debes habilitar este quirk:

* `PlatformInfo -> Generic -> AdviseWindows -> True`

::: tip Ejemplo

![](../../images/troubleshooting/troubleshooting-md/error.png)

:::

## Sistemas Coffee Lake no iniciándose luego de la suspensión del sistema

En macOS 10.15.4, hubo algunos cambios hechos al AGPM que puede causar problemas en la reactivación de sistemas Coffee Lake. Específicamente monitores conectados a un iGPU fallan en reactivarse. Para resolver esto:

* Agrega `igfxonln=1` a tus bootargs
* Asegúrate que estás usando [WhateverGreen v1.3.8](https://github.com/acidanthera/WhateverGreen/releases) y posterior

## No se puede controlar el Volumen/Brillo en monitores externos

Curiosamente, macOS ha bloqueado el control del audio digital. Para recuperar algunas funciones, la aplicación [MonitorControl](https://github.com/the0neyouseek/MonitorControl/releases) ha hecho un gran trabajo para mejorar el soporte en macOS.

## Deshabilitando el SIP

SIP o propiamente conocida como Protección de Integridad del Sistema, es una tecnología de seguridad que intenta evitar que cualquier software malicioso y el usuario dañen el sistema operativo. Introducido por primera vez con OS X El Capitan, el SIP ha crecido con el tiempo para controlar más y más cosas en macOS, incluida la limitación de ediciones a archivos restringidos y la carga de kexts de terceros con `kextload` (OpenCore no se ve afectado porque los kexts se inyectan en el arranque). Para resolver esto, Apple ha proporcionado numerosas opciones de configuración en la variable NVRAM `csr-active-config` que se puede configurar en el entorno de recuperación macOS o con la sección NVRAM de OpenCore (esta última se discutirá a continuación).

* <span style="color:red">ADVERTENCIA:</span> Deshabilitar el SIP puede romper aspectos como las actualizaciones de software en macOS 11, Big Sur, y posterior. Por favor ten cuidado y sólo deshabilita los valores especificos que necesitas en vez de deshabilitar todos los valores para prevenir estos problemas. 
   * Habilitar `CSR_ALLOW_UNAUTHENTICATED_ROOT` y `CSR_ALLOW_APPLE_INTERNAL` son opciones comunes que pueden romper las actualizaciones de software.


Puedes elegir entre varios valores para habilitar/deshabilitar ciertas "flags" del SIP. Algunas herramientas útiles para ayudarte con esto son [CsrDecode](https://github.com/corpnewt/CsrDecode) y [csrstat](https://github.com/JayBrown/csrstat-NG). Los valores comúnes son los siguientes:

* `00000000` - SIP completamente habilitado (0x0).
* `03000000` - Deshabilita la firma de los kexts (0x1) y las protecciones del sistema de archivos (0x2).
* `FF030000` - Deshabilita todas las [flags en macOS High Sierra](https://opensource.apple.com/source/xnu/xnu-4570.71.2/bsd/sys/csr.h.auto.html) (0x3ff).
* `FF070000` - Deshabilita todas las [flags en macOS Mojave](https://opensource.apple.com/source/xnu/xnu-4903.270.47/bsd/sys/csr.h.auto.html) y en [macOS Catalina](https://opensource.apple.com/source/xnu/xnu-6153.81.5/bsd/sys/csr.h.auto.html) (0x7ff) ya que Apple introdujo un valor para executable policy.
* `FF0F0000` - Disable all flags in macOS Big Sur (0xfff) which has another new [flag for authenticated root](https://eclecticlight.co/2020/06/25/big-surs-signed-system-volume-added-security-protection/).

**Nota**: Deshabilitar el SIP con OpenCore es bastante distinto comparado con Clover, específicamente que las variables del NVRAM no van a ser anuladas a menos que se indique específicamente en la sección `Delete`. Así que, si ya has configurado el SIP via OpenCore o desde macOS, debes anular la siguiente variable:

* `NVRAM -> Block -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> csr-active-config`
  
![](../../images/troubleshooting/troubleshooting-md/sip.png)

## Escribiendo a la partición del sistema en macOS

Con macOS Catalina y posterior, Apple partió el SO y los datos del usuario en dos volúmenes, donde el volumen del sistema es de sólo lectura por defecto. Para que podamos escribir en ellos debemos hacer algunas cosas:

**macOS Catalina**
1. [Deshabilita el SIP](#deshabilitando-el-sip)
2. Monta el disco para que puedas escribir en él (Corre `sudo mount -uw /` en la terminal)

**macOS Big Sur**

1. [Deshabilita el SIP](#deshabilitando-el-sip)
2. Monta el disco para que puedas escribir en él (Mira el link de abajo para obtener el comando)

* Nota: Debido a cómo son manejadas las actualizaciones del sistema operativo en macOS Big Sur y posterior, cambiar el volumen del sistema en realidad puede romper las actualizaciones. Por favor edíta con cuidado

Comandos basados en los documentos KDK de Apple:
```bash
# Primero, crea un punto de montaje para tu disco
mkdir ~/livemount

# Luego, encuentra el volumen del sistema
diskutil list

# En la lista de abajo podemos ver que el volumen de nuestro sistema es disk5s5
/dev/disk5 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +255.7 GB   disk5
                                 Physical Store disk4s2
   1:                APFS Volume ⁨Big Sur HD - Data⁩       122.5 GB   disk5s1
   2:                APFS Volume ⁨Preboot⁩                 309.4 MB   disk5s2
   3:                APFS Volume ⁨Recovery⁩                887.8 MB   disk5s3
   4:                APFS Volume ⁨VM⁩                      1.1 MB     disk5s4
   5:                APFS Volume ⁨Big Sur HD⁩              16.2 GB    disk5s5
   6:              APFS Snapshot ⁨com.apple.os.update-...⁩ 16.2 GB    disk5s5s

# Monta el disco (es decir, disk5s5)
sudo mount -o nobrowse -t apfs  /dev/disk5s5 ~/livemount

# Ahora puedes hacer cambios libremente al volumen del sistema
# Si editaste S*/L*/Kernel, S*/L*/Extensions o L*/Extensions,
# tendrás que rearmar el caché del kernel

sudo kmutil install --volume-root ~/livemount --update-all

# Finalmente, una vez que termines de editar el volumen del sistema querrás crear una nueva snapshot

sudo bless --folder ~/livemount/System/Library/CoreServices --bootefi --create-snapshot
```

## Deshacer snapshots de APFS

Con macOS Big Sur, el volumen del sistema tiene las llamadas "snapshots", que te permiten deshacer cambios en caso de que tengas problemas con actualizaciones del sistemas. Gracias a que las snapshots se crean cada vez que hay una actualización, tenemos unas cuantas instancias en las que tenemos la oportunidad de revertir:

Para hacer esto, primero necesitarás bootear desde la partición de recuperación y seleccionar "Restore From Time Machine Backup":

![](./../../images/troubleshooting/troubleshooting-md/snapshots.jpg)

* [Créditos a Lifewire por la imágen](https://www.lifewire.com/roll-back-apfs-snapshots-4154969)