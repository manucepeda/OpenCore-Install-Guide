# Problemas del espacio de ususario

* Versión soportada: 0.6.8

* [Instalador de macOS en ruso](#instalador-de-macos-en-ruso)
* [Instalador de macOS dañado](#instalador-de-macos-dañado)
* [Trancado en o cerca de `IOConsoleUsers: gIOScreenLock...`](#trancado-en-o-cerca-de-ioconsoleusers-gioscreenlockgiolockstate-3)
* [Pantalla revuelta y perturbada en laptops](#pantalla-revuelta-y-perturbada-en-laptops)
* [Pantalla negra luego de `IOConsoleUsers: gIOScreenLock...` en Navi](#pantalla-negra-luego-de-ioconsoleusers-gioscreenlock-en-navi)
* [Trancado en el instalador de macOS luego de 30 segundos](#trancado-en-el-instalador-de-macos-luego-de-30-segundos)
* [macOS congelado just antes del logueo](#macos-congelado-justo-antes-del-logueo)
* [CPUS de 15h/16h se reinician luego de la pantalla de datos y privacidad](#cpus-de-15h16h-se-reinician-luego-de-la-pantalla-de-datos-y-privacidad)
* [Fallos al eliminar-formatear en Disk Utility](#fallos-al-eliminar-formatear-en-disk-utility)
* [Discos SATA no aparecen en Disk Utility](#discos-sata-no-aparecen-en-disk-utility)
* [Trancado cuando faltan 2 minutos](#trancado-cuando-faltan-2-minutos)

## Instalador de macOS en ruso

La configuración del sample predeterminada está en ruso porque los Slavs gobiernan el mundo de los Hackintoshes, verifica tu valor `prev-lang: kbd` en`NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82`. Se establece a `656e2d55533a30` para Inglés de EEUU: en-US: 0 y se puede encontrar una lista completa en [AppleKeyboardLayouts.txt](https://github.com/acidanthera/OpenCorePkg/blob/master/Utilities/AppleKeyboardLayouts/AppleKeyboardLayouts.txt ) Para aquellos que usan un editor de texto simple (es decir, UEFI Shell, Notepad++, etc.), `656e2d55533a30` se convertirá a `ZW4tVVM6MA==`

Default sample config is in Russian because slavs rule the Hackintosh world, check your `prev-lang:kbd` value under `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82`. Set to `656e2d55533a30` for American: en-US:0 and a full list can be found in [AppleKeyboardLayouts.txt](https://github.com/acidanthera/OpenCorePkg/blob/master/Utilities/AppleKeyboardLayouts/AppleKeyboardLayouts.txt). For those using with a simple text editor(ie. UEFI Shell, Notepad++, etc), `656e2d55533a30` will become `ZW4tVVM6MA==`

Es posible que también debas restablecer la NVRAM en el selector de arranque

¿Aún no funcionó? Bueno, tiempo para las armas pesadas. Forzaremos la eliminación de esa propiedad exacta y dejaremos que OpenCore la reconstruya:

`NVRAM -> Block -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> Item 0` luego configura el tipo a `String` y el valor `prev-lang:kbd`

::: tip Ejemplo

![](../../images/troubleshooting/troubleshooting-md/lang.png)

:::

## Instalador de macOS dañado

Si ha descargado macOS antes de Octubre de 2019, es probable que tenga un certificado de instalador de macOS vencido, hay 2 formas de solucionar esto:

* Descargar la copia más reciente de macOS
* Cambiar la fecha en la terminal a cuando el certificado era válido

Para ese último:

* Desconecta todos los dispositivos de red (Ethernet, deshabilita el Wifi)
* En el terminal del recovery establece la fecha al 1 de septiembre de 2019:

```
date 0901000019
```

## Trancado en o cerca de `IOConsoleUsers: gIOScreenLock...`/`gIOLockState (3...`

Esto es justo antes de que la GPU es inicializada, verifica lo siguiente:

* Que tu GPU este capacitada para UEFI (GTX 7XX/2013+)
* CSM está desactivado en la BIOS
* Estás forzando el link speed de PCIe a 3.0
* Verifica que el ig-platform-id y device-id son válidos si estás corriendo con tu iGPU.
  * UHD 630s de escritorio pueden llegar a necesitar `00009B3E`
* Prueba [arreglos de WhateverGreen](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md)
  * El boot arg `-igfxmlr`. Esto también se puede manifestar como el error de "división entre cero".
* Usuarios de iGPU Coffee Lake pueden necesitar `igfxonln=1` en 10.15.4 y posterior

## Pantalla revuelta y perturbada en laptops

Habilita CSM en tus ajustes UEFI. Esto también puede aparecer como"Boot legacy ROMs" u otro ajuste legacy.

## Pantalla negra luego de `IOConsoleUsers: gIOScreenLock...` en Navi

* Agrega `agdpmod=pikera` a tus boot args
* Cambia de conector de monitor en tu GPU
* Intenta arrancar con el SMBIOS MacPro7,1 y el boot arg `agdpmod=ignore`

Para usuarios Navi MSI, necesitarán aplicar el parche mencionado aquí: [Instalador no funcionando con 5700XT #901](https://github.com/acidanthera/bugtracker/issues/901)

Específicamente, agregando lo siguiente debajo de `Kernel -> Patch`:

```
Base:
Comment: Navi VBIOS Bug Patch
Count: 1
Enabled: YES
Find: 4154592C526F6D2300
Identifier: com.apple.kext.AMDRadeonX6000Framebuffer
Limit: 0
Mask:
MinKernel:
MaxKernel:
Replace: 414D442C526F6D2300
ReplaceMask:
Skip: 0
```

## Trancado en el instalador de macOS luego de 30 segundos

Esto probablemente se deba a que NullCPUPowerManagement no está presente o no funciona, el que está alojado en la Guía Vanilla de AMD OSX está dañado. Ve a gritarle a Shannee para que lo arregle. Para solucionar el problema, elimina NullCPUPowerManagement de `Kernel -> Add` y `EFI/OC/Kexts` y luego habilita `DummyPowerManagement` en`Kernel -> Quirks`

## macOS congelado justo antes del logueo

Este es un ejemplo común de TSC malo, para la mayoría de los sistemas agrega [CpuTscSync](https://github.com/lvs1974/CpuTscSync)

En Skylake-X, muchos firmwares, incluidos Asus y EVGA, no escribirán el TSC en todos los núcleos, por lo que tendremos que restablecer el TSC en el arranque en frío y en la reactivación luego de suspender el PC. La versión compilada se puede encontrar aquí: [TSCAdjustReset.kext](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/TSCAdjustReset.kext.zip). Ten en cuenta que  **debes** abrir el kext (Mostrar contenidos del paquete en Finder, `Contents -> Info.plist`) y cambiar el Info.plist -> `IOKitPersonalities -> IOPropertyMatch -> IOCPUNumber` a la cantidad de hilos de CPU que tener desde `0` (por ejemplo, el i9 7980xe, que tiene 18 núcleos sería `36`,  ya que tiene 36 hilos en total)

La manera más común de ver el problema del TSC:

::: tip Casos comunes posibles

Caso 1    |  Caso 2
:-------------------------:|:-------------------------:
![](../../images/troubleshooting/troubleshooting-md/asus-tsc.png)  |  ![](../../images/troubleshooting/troubleshooting-md/asus-tsc-2.png)

:::

## CPUS de 15h/16h se reinician luego de la pantalla de datos y privacidad

Sigue las instrucciones aquí luego de UPDATE 2: [Arreglar reinicio en data y privacidad](https://www.insanelymac.com/forum/topic/335877-amd-mojave-kernel-development-and-testing/?do=findComment&comment=2658085)

## Fallos al eliminar-formatear en Disk Utility

Este es 1 (o más) de 5 problemas:

* Estás formateando la partición y no el disco entero, consulta [MediaKit reporta que no hay espacio suficiente](#mediakit-reports-not-enough-space)
* DiskUtility tiene un error extraño en el que fallará en el primer borrado, intenta borrar de nuevo
* El soporte de hotplug SATA en el BIOS está causando problemas (intenta deshabilitar esta opción)
* Firmware antiguo, asegúrate de que la unidad tenga el firmware más reciente
* Y finalmente, es posible que tengas un disco malo

## Discos SATA no aparecen en DiskUtility

* Asegúrate de que el SATA Mode es AHCI en tu BIOS
* Ciertos controladores SATA pueden no ser soportados oficialmente por macOS, en estos casos querrás agregar [CtlnaAHCIPort.kext](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/CtlnaAHCIPort.kext.zip)
* Para controladores SATA muy antiguos, [AHCIPortInjector.kext](https://www.insanelymac.com/forum/files/file/436-ahciportinjectorkext/) podría ser más adecuado.

## Trancado cuando faltan 2 minutos


![](../../images/troubleshooting/troubleshooting-md/2-min-remaining.jpeg)

Este error está relacionado directamente con la parte en la que macOS escribirá ciertas variables NVRAM para que puede arrancar tu sistema luego, así que cuando hay problemas alrededor del NVRAM se quedará trancado aquí.

Para resolver esto, tenemos algunas opciones:

* Arreglo para las series 300 de Intel (es decir, Z390):
  * [SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/)
* Otros pueden configurar lo siguiente en su config.plist:
  * LegacyEnable -> YES
  * LegacyOverwrite -> YES
  * WriteFlash -> YES
