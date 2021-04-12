# Proceso de arranque

* Versión soportada: 0.6.8

A la hora de solucionar los problemas de tu hackintosh, puede llegar a ser dificil entender *dónde* te estás quedando trancado ya que la palabra(s) en la que estás trancado puede no llegar a tener un resultado que te sirva en Google. Por más que esta página no solucionará todos tus problemas, seguramente te ayude a entender mejor dónde te estás quedando trancado en el proceso de arranque de macOS.

## Arranque de OpenCore

Esta sección será corta, ya que los problemas de arranque de OpenCore son bastante raros y usualmente son simples errores del usuario. 

* El sistema se prende y busca dispositivos de arranque
* El sistema localiza BOOTx64.efi en tu USB de OpenCore en EFI/BOOT/
* BOOTx64.efi es cargado lo que genera la carga de OpenCore.efi en EFI/OC/
* Son aplicadas las propiedades NVRAM
* Se cargan los drivers EFI desde EFI/OC/Drivers
* Se instala el GOP (Graphics Output Protocol)
* Se cargan las tablas ACPI desde EFI/OC/ACPI
* Los datos de SMBIOS son aplicados
* OpenCore carga y muestra todas las opciones de arranque posibles
* Luego arrancas al instalador de macOS

Si estás teniendo problemas de arranque en este punto, estos son algunos de los problemas a los que te podrías estar enfrentando:

* [Trancado en `no vault provided!`](./extended/opencore-issues.md#trancado-en-no-vault-provided)
* [No puedo ver particiones de macOS](./extended/opencore-issues.md#no-puedo-ver-particiones-de-macos)
* [Pantalla negra luego del menú](./extended/opencore-issues.md#pantalla-negra-luego-del-menu)
* [Arrancar OpenCore reinicia a la BIOS](./extended/opencore-issues.md#arrancar-opencore-reinicia-a-la-bios)

Para ver el resto de posibilidades, mira aquí:

* [OpenCore booting issues](./extended/opencore-issues.md)

## Transferencia de boot.efi

![](../images/troubleshooting/boot-md/1-boot-efi.png)

Aquí es cuando el bootloader de macOS (boot.efi) entra en acción. Lo que hace específicamente es preparar el ambiente para que cargue el kernel, a donde OpenCore inyecta kexts. Si te estás quedando trancado en este punto, seguramente sea un problema relacionado a la carga de el kernel. Los principales culpables de esto son los siguientes:

* [Trancado en EndRandomSeed](./extended/kernel-issues.md#trancado-en-endrandomseed)
* [Trancado en `[EB|#LOG:EXITBS:START]`](./extended/kernel-issues.md#trancado-en-eblogexitbsstart)
* [Errores `Couldn't allocate runtime area`](./extended/kernel-issues.md#errores-couldnt-allocate-runtime-area)

Para ver el resto de problemas posibles, dirígete aquí:

* [Problemas de kernel](./extended/kernel-issues.md)

**Nota**: En macOS 10.15.4, Apple cambió el protocolo de depuración de boot.efi, por lo que verás todo un bastante más distinto a lo que era antes, aunque las mismas reglas siguen aplcando.

## Transferencia del XNU/Kernel

Ahora que boot.efi tiene todo configurado para nosotros, debemos dejar que el kernel trabaje. Comúnmente se hace referencia a esta sección como la [Fase de rooting](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/KernelProgramming/booting/booting.html):

![](../images/troubleshooting/boot-md/2-kernel-start.png)

Esta sección es en la que los datos de SMBIOS son verificados, las tablas ACPI/Kexts son cargados y macOS trata de ordenar todo. Los errores son comúnmente el resultado de:

* SSDTs corruptos
* Kexts corruptos (o establecidos incorrectamente en config.plist -> Kernel -> Add)
* Mapa de memoria estropeado

Dirígete aquí para obtener más información sobre distintos problemas a los que te puedes enfrentar:

* [Kernel Panic `Cannot perform kext summary`](./extended/kernel-issues.md#kernel-panic-cannot-perform-kext-summary)
* [Kernel Panic on `Invalid frame pointer`](./extended/kernel-issues.md#kernel-panic-on-invalid-frame-pointer)

![](../images/troubleshooting/boot-md/5-apfs-module.png)

Aquí tenemos `[ PCI configurations begin ]`, esta sección puede ser vista como una prueba de hardware, kexts y SSDTs que inyectamos, y donde IOKit intenta encontrar dispositivos a los que adherirse.

Los aspectos principales que son puestos a prueba son los siguientes:

* Embedded Controllers
* Almacenamiento(NVMe, SATA, etc)
* PCI/e
* NVRAM
* RTC
* PS2 y I2C

For more specific info on how to get around this area, see here:

* [Trancado en `RTC...`, `PCI Configuration Begins`, `Previous Shutdown...`, `HPET`, `HID: Legacy...`](./extended/kernel-issues.md#trancado-en-rtc-pci-configuration-begins-previous-shutdown-hpet-hid-legacy)

![](../images/troubleshooting/boot-md/6-USB-setup.png)

Aquí es donde entra en justo el límite de 15 puertos y el mapa de USBs entran en juego, y donde puede aparecer el famoso error "Waiting for Root Device". Las principales cosas a mirar son las siguientes:

* ["Waiting for Root Device" or Prohibited Sign error](../troubleshooting/troubleshooting.md#waiting-for-root-device-or-prohibited-sign-error)

![](../images/troubleshooting/boot-md/8-dsmos-arrived.png)

Aquí es donde nuestro FakeSMC/VirtualSMC entra en juego y hace su magia. DSMOS (**D**on't **S**teal **M**ac**OS**) en sí es un kext que verifica que tu sistema tiene un SMC y pedirá una contraseña. Si esta no está presente, DSMOS no decriptará el resto de los binarios y te quedarás trancado ahí. También podrías quedarte trancado en AppleACPICPU, que es el mismo error.

* [kextd stall[0]: AppleACPICPU](./extended/kernel-issues.md#kextd-stall0-appleacpicpu)

```
Your karma check for today:
There once was a user that whined
his existing OS was so blind,
he'd do better to pirate an OS that ran great
but found his hardware declined.
Please don't steal Mac OS!
Really, that's way uncool.
(C) Apple Computer, Inc.
```

Fuente: Dont Steal Mac OS X.kext

![](../images/troubleshooting/boot-md/9-audio.png)

Aquí es donde el driver de audio de Apple se carga, y donde AppleALC brilla. Generalmente es raro ver problemas aquí, pero si tienes algún problema aquí intenta deshabilitar AppleALC u otros kexts relacionados al audio.

![](../images/troubleshooting/boot-md/10-GPU.png)

Aquí es cuando se inicializa el driver de la GPU, y donde WhateverGreen también hace su magia. Generalmente los errores aquí son debido a la GPU y no por WhateverGreen en sí. Los errores más comunes son los siguientes:

* [Trancado en o cerca de `IOConsoleUsers: gIOScreenLock...`](./extended/kernel-issues.md#trancado-en-o-cerca-de-ioconsolessers-gioscreenlock)
* [Pantalla negra luego de `IOConsoleUsers: gIOScreenLock...` en Navi](./extended/kernel-issues.md#pantalla-negra-luego-de-ioconsoleusers-gioscreenlock-en-navi)

## Transferencia a macOS

![](../images/troubleshooting/boot-md/11-boot.png)

Al fin pasaste todo ese verbose! Si te estás quedando trancado en el logo de Apple luego de esto, hay algunas cosas que podrías mirar:

* [macOS trancado justo antes del inicio](./extended/kernel-issues.md#macos-trancado-justo-antes-del-inicio)
* [Pantalla negra luego de `IOConsoleUsers: gIOScreenLock...` en Navi](./extended/kernel-issues.md#pantalla-negra-luego-de-ioconsoleusers-gioscreenlock-en-navi)
* [Trancado en el instalador de macOS luego de 30 segundos](./extended/userspace-issues.md#trancado-en-el-instalador-de-macos-luego-de-30-segundos)
