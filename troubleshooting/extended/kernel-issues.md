# Problemas del kernel

* Versión soportada: 0.6.8

Aquí se cubre todo lo que puede pasar luego de que seleccionas a macOS en el menú de OpenCore hasta justo antes de que aparezca el logo de Apple y cargue la GUI

* [Trancado en `[EB|#LOG:EXITBS:START]`](#trancado-en-eblogexitbsstart)
* [Trancado en EndRandomSeed](#trancado-en-endrandomseed)
* [Kernel Panic en `Invalid frame pointer`](#kernel-panic-en-invalid-frame-pointer)
* [Trancado en [EB|LD:OFS] Err(0xE) cuando arrancas desde el volumen preboot](#trancado-en-eb-ld-ofs-err-0xe-cuando-arrancas-desde-el-volumen-preboot)
* [Trancado en `OCB: LoadImage failed - Security Violation`](#trancado-en-ocb-loadimage-failed-security)
* [Trancado en `OCABC: Memory pool allocation failure - Not Found`](#trancado-en-ocabc-memory-pool-allocation-failure---not-found)
* [Trancado en `Buffer Too Small`](#trancado-en-buffer-too-small)
* [Trancado en `Plist only kext has CFBundleExecutable key`](#trancado-en-plist-only-kext-has-cfbundleexecutable-key)
* [Trancado en `This version of Mac OS X is not supported: Reason Mac...`](#trancado-en-this-version-of-mac-os-x-is-not-supported-reason-mac)
* [Errores `Couldn't allocate runtime area`](#errores-couldnt-allocate-runtime-area)
* [Trancado en `RTC...`, `PCI ConfigurationBegins`, `Previous Shutdown...`, `HPET`, `HID: Legacy...`](#trancado-en-rtc-pci-configuration-begins-previous-shutdown-hpet-hid-legacy)
* [Trancado en ACPI Table loading en B550](#trancado-en-acpi-table-loading-on-b550)
* ["Waiting for Root Device" o signo de prohibido](#waiting-for-root-device-o-signo-de-prohibido)
* [Trancado en o cerca de `IOConsoleUsers: gIOScreenLock...`](#trancado-en-o-cerca-de-ioconsoleusers-gioscreenlockgiolockstate-3)
* [Pantalla revuelta y perturbada en laptops](#pantalla-revuelta-y-perturbada-en-laptops)
* [Pantalla negra luego de `IOConsoleUsers: gIOScreenLock...` en Navi](#pantalla-negra-luego-de-ioconsoleusers-gioscreenlock-en-navi)
* [Kernel Panic `Cannot perform kext summary`](#kernel-panic-cannot-perform-kext-summary)
* [Kernel Panic `AppleIntelMCEReporter`](#kernel-panic-appleintelmcereporter)
* [Kernel Panic `AppleIntelCPUPowerManagement`](#kernel-panic-appleintelcpupowermanagement)
* [Teclado funciona pero el trackpad no](#teclado-funciona-pero-el-trackpad-no)
* [`kextd stall[0]: AppleACPICPU`](#kextd-stall0-appleacpicpu)
* [Kernel Panic en AppleIntelI210Ethernet](appleinteli210ethernet)
* [Kernel panic en "Wrong CD Clock Frequency" con laptops Icelake](#kernel-panic-en-wrong-cd-clock-frequency-con-laptops-icelake)
* [Kernel Panic `AppleACPIPlatform` en 10.13](#kernel-panic-appleacpiplatform-en-10-13)

## Trancado en `[EB|#LOG:EXITBS:START]`

Esta sección será dividida en 3 partes, así que presta atención:

* [Problemas con Booter](#problemas-con-booter)
* [Problemas con parches del kernel](#problemas-con-parches-del-kernel)
* [Problemas con UEFI](#problemas-con-uefi)

### Problemas con Booter

* `RebuildAppleMemoryMap` puede no ser un fanático de tu firmware, y el uso de este quirk depende de tener `EnableWriteUnprotector` deshabilitado y `SyncRuntimePermissions` habilitado con la adición de tener una `Tabla de atributos de memoria (MAT)` en tu firmware. Si su firmware no tiene MATs, desactiva tanto `RebuildAppleMemoryMap` como `SyncRuntimePermissions` y luego habilita `EnableWriteUnprotector`.

Para verificar si tu placa madre tiene MATs, busca algo así en los logs:

```
OCABC: MAT support is 1
```

* `DevirtualiseMmio`
  * Algunos espacios MMIO siguen siendo requeridos para funcionar correctamente, por lo que tendrás que excluir estas regiones en Booter -> MmioWhitelist o deshabilitar este quirk por completo.or disable this quirk outright.
  * Más información aquí: [Usando DevirtualiseMmio](../extras/kaslr-fix.md#using-devirtualisemmio)

* `SetupVirtualMap`
  * Este quirk es requerido por la mayoría de los firmwares y sin este es muy común ver un pánico del kernel aquí, así que habilítalo si no lo has hecho aún.
    * Sin embargo, ciertos firmwares no funcionan con este quirk y este quirk podría ser el causante del kernel panic:
      * La serie Ice Lake de Intel
      * La serie Comet Lake de Intel
      * Las placas madre B550 y A520 de AMD
      * Placas madre TRx40 de AMD
      * Máquinas virtuales como QEMU
      * Actualizaciónes de la BIOS 3006+ en las placas X299 de ASUS (esto también aplica a otras BIOS X299 en la BIOS más reciente).

* `EnableWriteUnprotector`

  * Otro problema puede ser que macOS está teniendo conflictos con la protección de escritura del registro CR0. Para resolver esto tenemos dos opciones:
    * Si tu firmware soporta MATs (firmwares de 2018+):
      * EnableWriteUnprotector -> False
      * RebuildAppleMemoryMap -> True
      * SyncRuntimePermissions -> True
    * Para firmwares más antiguos:
      * EnableWriteUnprotector -> True
      * RebuildAppleMemoryMap -> False
      * SyncRuntimePermissions -> False

Con respecto al soporte de MATs, los firmwares construidos luego de EDK 2018 lo soportarán y muchos fabricantes incluso han agregado soporte desde las laptops Skylake. El problema es que no siempre es fácil saber si un fabricante ha actualizado el firmware, puedes verificar los registros de OpenCore para saber si el tuyo lo admite:

```
OCABC: MAT support is 1
```

Nota: `1` significa que soporta MATs, mientras que `0` significa que no.

### Problemas con parches del kernel

Esta sección será dividida entre usuarios de Intel y AMD:

#### Usuarios de AMD

* Faltan los [parches del kernel](https://github.com/AMD-OSX/AMD_Vanilla/tree/opencore) (esto sólo aplica para CPUs de AMD, asegúrate de que son parches de OpenCore y no de Clover. Clover usa `MatchOS` mientras que OpenCore tiene `MinKernel` y `Maxkernel`)
  * Ten en cuenta que si estás utilizando versiones viejas de los parches del kernel probablemente tengas el mismo problema, por lo que debes asegurarte de que estás usando los más nuevos de AMD OS X

#### Usuarios de Intel

* **AppleXcpmCfgLock** y **AppleCpuPmCfgLock**
  * Faltan parches de CFG o XCPM, por favor habilita `AppleXcpmCfgLock` y `AppleCpuPmCfgLock`
  * Alternativamente puedes deshabilitar CFG-Lock correctamente: [Fixing CFG Lock](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html)
    * Haswell y posterior sólo necesitan AppleXcpmCfgLock
    * Ivy Bridge y anterior sólo necesitan AppleCpuPmCfgLock
      * Broadwell y anterior necesitan AppleCpuPmCfgLock si están corriendo 10.10 o anterior

* **AppleXcpmExtraMsrs**
  * Esto podría llegar a ser requerido, aunque está pensado para Pentiums, HEDT u otros sistemas que no tienen soporte nativo en macOS.

### Problemas con UEFI

* **ProvideConsoleGop**
  * Necesario para transicionar a la próxima pantalla, esto era originalmente parte de AptioMemoryFix, pero ahora es parte de OpenCore con este quirk. Puede ser encontrado en UEFI -> Output
  * Ten en cuenta que desde 0.5.6 este quirk ha sido habilitado por defecto en el sample.plist
* **IgnoreInvalidFlexRatio**
  * Esto es necesario para Broadwell y anterior. **No para AMD/Skylake y posterior**

## Trancado en EndRandomSeed

El mismo error que el de arriba, dirígete allí para obtener más información: [Trancado en `[EB|#LOG:EXITBS:START]`](#trancado-en-eb-log-exitbs-start)

* Nota: Habilitando [OpenCore DEBUG](../debug.html) también puede ayudar a clarificar las cosas

## Kernel Panic en `Invalid frame pointer`

Esto se debe a algún problema relacionado con  `Booter -> Quirks` que configuraste, los principales aspectos a verificar:

* `DevirtualiseMmio`
  * Todavía se requieren ciertos espacios MMIO para funcionar correctamente, por lo que deberás excluir estas regiones en Booter -> MmioWhitelist o deshabilitar este quirk por completo
* Más información aquí: [Usando DevirtualiseMmio](../extras/kaslr-fix.md#using-devirtualisemmio)

* `SetupVirtualMap`
  * Este quirk es requerido por la mayoría de los firmwares y sin este es muy común ver un pánico del kernel aquí, así que habilítalo si no lo has hecho aún.
    * Sin embargo, ciertos firmwares no funcionan con este quirk y este quirk podría ser el causante del kernel panic:
      * La serie Ice Lake de Intel
      * La serie Comet Lake de Intel
      * Las placas madre B550 y A520 de AMD
      * Placas madre TRx40 de AMD
      * Máquinas virtuales como QEMU

Otro problema puede ser que macOS está en conflicto con la protección contra escritura del registro CR0, para resolver esto tenemos 2 opciones:

* Si tu firmware es compatible con MATs (firmware de 2018+):
  * EnableWriteUnprotector -> False
  * RebuildAppleMemoryMap -> True
  * SyncRuntimePermissions -> True
* Para firmwares más antiguos:
  * EnableWriteUnprotector -> True
  * RebuildAppleMemoryMap -> False
  * SyncRuntimePermissions -> False

Con respecto al soporte de MAT, los firmwares construidos luego de EDK 2018 lo respaldarán y muchos OEM incluso han agregado soporte desde las laptops Skylake. El problema es que no siempre es fácil saber si un OEM ha actualizado el firmware, puedes verificar los registros de OpenCore para ver si el suyo lo admite:

```
OCABC: MAT support is 1
```

Nota: `1` significa que admite MAT, mientras que `0` significa que no.

## Trancado en [EB|LD:OFS] Err(0xE) cuando arrancas desde el volumen preboot

Esto puede suceder cuando el volumen preboot no está actualizado correctamente, para arreglar esto necesitarás bootear a recovery y repararlo:

Error completo:

```
[EB|`LD:OFS] Err(0xE) @ OPEN (System\\Library\\PrelinkedKernels\\prelinkedkernel)
```

Esto puede suceder cuando el volumen preboot no está actualizado correctamente, para arreglar esto necesitarás bootear a recovery y repararlo:

1. Habilita JumpstartHotplug en UEFI -> APFS (Recovery podría no arrancar en macOS Big Sur sin esta opción)
2. Bootea a recovery
3. Abre la terminal y corre lo siguiente:

```bash
# Primero, encuentra tu volumen Preboot
diskutil list

# Desde la siguiente lista podemos ver que nuestro volument preboot es disk5s2
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

# ahora monta el volumen preboot
diskutil mount disk5s2

# Luego corre updatePreboot en el volumen Preboot
diskutil apfs updatePreboot /volume/disk5s2

```

Luego finalmente reinicia, aunque podrías tener que deshabilitar JumpstartHotplug para arrancar normalmente de nuevo.

## Trancado en `OCB: LoadImage failed - Security Violation`

```
OCSB: No suitable signature - Security Violation
OCB: Apple Secure Boot prohibits this boot entry, enforcing!
OCB: LoadImage failed - Security Violation
```

Esto es debido a manifiestos de modo seguro de Apple antiguados faltantes en tu volumen preboot, lo que hace que falle en cargar si tienes SecureBootModel configurado. La razón de la falta de estos archivos es en realidad un bug en macOS.

Para resolver esto puedes hacer alguna de las siguientes cosas:

* Deshabilitar SecureBootModel (es decir, configurar `Misc -> Secuirty -> SecureBootModel -> Disabled`)
* Reinstalar macOS con la última versión
* O copiar los manifestos del Modo Seguro de `/usr/standalone/i386` a `/Volumes/Preboot/<UUID>/System/Library/CoreServices`

Para hacerlo via terminal:

```bash
# Primero, encuentra tu volumen Preboot
diskutil list
# Desde la lista de abajo, podemos ver que nuestro volumen preboot es disk5s2
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
# Ahora monta el volumen Preboot
diskutil mount disk5s2
# Utiliza CD para llegar a tu volumen Preboot
# Ten en cuenta que el volumen en sí se encunetra en /System/Volumes/Preboot
cd /System/Volumes/Preboot
# Agarra tu UUID
ls
 46923F6E-968E-46E9-AC6D-9E6141DF52FD
 CD844C38-1A25-48D5-9388-5D62AA46CFB8
# Si aparecen más de nua (por ejemplo si arrancas varias versiones de macOS) tendrás que:
# determinar cual UUID es la correcta.
# La manera más fácil de determinar esto es imprimiendo el valor de .disk_label.contentDetails
# de cada volumen.
cat ./46923F6E-968E-46E9-AC6D-9E6141DF52FD/System/Library/CoreServices/.disk_label.contentDetails
 Big Sur HD%
cat ./CD844C38-1A25-48D5-9388-5D62AA46CFB8/System/Library/CoreServices/.disk_label.contentDetails
 Catalina HD%
# Reemplaza CD844C38-1A25-48D5-9388-5D62AA46CFB8 con tu valor de UUID
cd ~
sudo cp -a /usr/standalone/i386/. /System/Volumes/Preboot/CD844C38-1A25-48D5-9388-5D62AA46CFB8/System/Library/CoreServices

```

## Trancado en `OCABC: Memory pool allocation failure - Not Found`

Esto se debe a configuraciones incorrectas del BIOS y/o valores incorrectos del Booter. Asegúrate de que config.plist -> Booter -> Quirks sea correcto y verifica la configuración de tu BIOS:

* Above 4G Decoding está habilitado
* CSM está deshabilitado (Habilitar el modo WHQL de Windows8.1/10 puede hacer lo mismo en algunas placas madre)
* Que tu BIOS está actualizado (Z390 y HEDT son conocidos por tener firmware mal escrito)

## Trancado en `Buffer Too Small`

* Habilita Above 4G Decoding en tu BIOS

## Trancado en `Plist only kext has CFBundleExecutable key`

`Executable path` falta o es incorrecto, esto debería ser resuelto corriendo la función snapshot de ProperTree de nuevo.

## Trancado en `This version of Mac OS X is not supported: Reason Mac...`

Este error para cuando tu SMBIOS no está soportada por esa versión de macOS. Asegúrate que tus valores de SMBIOS están configuradas en `PlatformInfo->Generic` con `Automatic` habilitado. Para ver una lista completa de las SMBIOS soportadas con sus versiones, dirígete aquí: [Escogiendo el SMBIOS correcto](../extras/smbios-support.md)

::: details SMBIOS soportadas en macOS 10.15 Catalina:

* iMac13,x+
* iMacPro1,1
* MacPro6,1+
* MacBook8,1+
* MacBookAir5,x+
* MacBookPro9,x+

:::

::: details SMBIOSes soportadas en macOS 11, Big Sur

* iMac14,4+
* iMacPro1,1
* MacPro6,1+
* Macmini7,1+
* MacBook8,1+
* MacBookAir6,x+
* MacBookPro11,x+

:::

## Errores de `Couldn't allocate runtime area`

Mira [Arreglando valores slide de KASLR](../extras/kaslr-fix.md)

## Trancado en `RTC...`, `PCI Configuration Begins`, `Previous Shutdown...`, `HPET`, `HID: Legacy...`

Esta área en general es cuando muchos dispositivos PCI son configurados y preparados por primera vez y aquí es cuando la mayoría de los errores sucederán. Otros nombres pueden incluir:

* `apfs_module_start...`,
* `Waiting for Root device`,
* `Waiting on...IOResources...`,
* `previous shutdown cause...`

Lugares principales a revisar:

* **Falta parche de EC**:
  * Asegúrate de tener tu SSDT de EC tanto en EFI/OC/ACPI como en ACPI -> Add, **verifica que esté habilitado.**
  * Si no tienes uno, consíguelo aquí: [Introducción a ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
* **Conflicto IRQ**:
  * Común en las computadoras portátiles y computadoras prehechas antiguas, ejecuta la opción FixHPET de SSDTTime y agrega los parches SSDT-HPET.aml y ACPI resultantes a tu configuración (el SSDT no funcionará sin los parches ACPI)
* **Problema de asignación de PCI**:
  * **ACTUALIZA TU BIOS**, asegúrate de que esté actualizado. La mayoría de los OEM tienen una asignación de PCI rota en firmwares antiguos, especialmente en AMD
  * Asegúrate de que Above 4G decoding esté habilitado en el BIOS, si no hay una opción disponible, agrega `npci = 0x2000` a los boot args.
    * Nota de CPUs AMD: **No tengas habilitada la configuración Above 4G y npci en los argumentos de arranque, entrarán en conflicto**. Esta regla no se aplica a X99
  * Otras configuraciones de BIOS que son importantes: CSM deshabilitado, Windows 8.1 / 10 Modo UEFI habilitado

* **Problemas NVMe o SATA**:
  * A veces, si se utiliza un controlador SATA defectuoso o una unidad NVMe no compatible, normalmente puede quedarse atascado aquí. Cosas que puedes consultar:
    * No estás usando una unidad de estado sólido Samsung PM981 o Micron 2200S NVMe
    * Samsung 970 Evo Plus con el último firmware (los firmwares más antiguos eran conocidos por la inestabilidad y los bloqueos, [consulta aquí para obtener más información](https://www.samsung.com/semiconductor/minisite/ssd/download/tools/))
    * SATA HotPlug está deshabilitado en la BIOS (causa problemas más comunmente en sistemas basados ​​en CPUs AMD)
    * Asegúrate de que las unidades NVMe estén configuradas como modo NVMe en la BIOS (algunas BIOS tienen un error en el que puede configurar las unidades NVMe como SATA)
* **Falla de NVRAM**:
  * Problema común en placas madre HEDT y de la serie 300, tienes un par de caminos para seguir:
    * Series 300 de Intel: Consulta [Introducción a ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) sobre cómo hacer SSDT-PMC.aml
    * HEDT: Consulta [Emulación de NVRAM](https://dortania.github.io/OpenCore-Post-Install/misc/nvram.html) sobre cómo detener la escritura de NVRAM, ten en cuenta que para la instalación no necesitas ejecutar el script. Simplemente configura tu config.plist

* **Falta RTC**:
  * Comúnmente encontrado en la serie 300 y las placas madre X299/Cascade Lake-X refresh, debido a que el reloj RTC está deshabilitado de forma predeterminada. Consulta [Introducción a ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) sobre cómo crear un SSDT-AWAC.aml
  * Algunos firmwares de HP también deshabilitaron el RTC en el HP 250 G6 sin ninguna forma de volver a habilitarlo realmente, los usuarios maldecidos con dicho hardware necesitarán crear un reloj RTC falso para que macOS juegue:

Ejemplo de cómo se ve un RTC deshabilitado sin forma de habilitarlo (ten en cuenta que no hay ningún valor para volver a habilitarlo como `STAS`):

![](../../images/troubleshooting/troubleshooting-md/rtc.png)

## Trancado en ACPI Table loading en B550

![](../../images/troubleshooting/troubleshooting-md/OC_catalina.jpg)

Si te estás quedando trancado en/cerca de ACPI table loading con un motherboard AMD B550 o A520, agrega el siguiente SSDT:

* [SSDT-CPUR.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml)

Y por favor recuerda agregarlo a ambos EFI/OC/ACPI **y** tu config.plist debajo de ACPI -> Add (La función snapshot de ProperTree puede hacer esto por tí)

## "Waiting for Root Device" o signo de prohibido

Generalmente visto como un error de USB, hay dos formas de arreglarlo:

### Problemas de USB

Esto asume que sólo estás arrancando el instalador, y no macOS en sí

* Si estás alcanzando el límite de 15 puertos, puedes evitar esto temporalmente con `XhciPortLimit` pero para uso a largo plazo, te recomendamos que hagas un [USB map](https://inyextciones.github.io/OpenCore-Post-Install/usb/)
* Otro problema puede ser que ciertos firmwares no pasarán las propiedades de USBs a macOS, para solucionar esto podemos habilitar `UEFI -> Quirks -> ReleaseUsbOwnership` en tu config.plist
  * Habilitar XHCI Handoff en el BIOS también puede solucionar esto
* Para las CPU AMD de 15h y 16h, es posible que debas agregar lo siguiente:
  * [XLNCUSBFix.kext](https://cdn.discordapp.com/attachments/566705665616117760/566728101292408877/XLNCUSBFix.kext.zip)
* Si XLNCUSBFix aún no funciona, intenta lo siguiente:
  * [AMD StopSign-fixv5](https://cdn.discordapp.com/attachments/249992304503291905/355235241645965312/StopSign-fixv5.zip)
* Puertos USB faltantes en ACPI
  * Para el Coffee Lake de Intel y más antiguo, recomendamos que uses [USBInjectAll](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/)
  * Para Ice Lake y Comet Lake de Intel, recomendamos [SSDT-RHUB](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-RHUB.aml)
  * En el caso de AMD, recomendamos el uso de controladores de terceros como puertos USB 3.2 AsMedia

### Problemas de SATA

En raras ocasiones (principalmente en laptops), el controlador SATA no es oficialmente compatible con macOS. Para resolver esto, queremos hacer algunas cosas:

* Establecer SATA en modo AHCI en el BIOS
  * macOS no admite el modo RAID o IDE de hardware correctamente.
  * Ten en cuenta que las unidades que ya utilizan la tecnología Intel Rapid Storage (RST, soft RAID para Windows y Linux) no serán accesibles en macOS.
* [SATA-unsupported.kext](https://github.com/RehabMan/hack-tools/tree/master/kexts/SATA-unsupported.kext)
  * Agrega soporte para controladores SATA no soportados, comúnmente en laptops.
  * Para controladores SATA muy antiguos, [AHCIPortInjector.kext](https://www.insanelymac.com/forum/files/file/436-ahciportinjectorkext/) puede ser más adecuado.

Ten en cuenta que solo experimentarás este problema después de instalar macOS en la unidad, al iniciar el instalador de macOS no se producirá un error debido a problemas de SATA.

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

Habilita CSM en tus ajustes UEFI. Esto también puede aparecer como "Boot legacy ROMs" u otro ajuste legacy.

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

## Kernel Panic `Cannot perform kext summary`

Generalmente visto como un problema que rodea al prelinked kernel, específicamente que macOS está teniendo dificultades para interpretar los que inyectamos. Verifica que:

* Que tus kexts estén en el orden correcto (el kext principal y luego los complementos, Lilu siempre es el primero)
* Que los kexts con ejecutables los tienen y que los kexts de sólo plist kexts no (kexts como USBmap.kext, XHCI-unspported.kext, etc no continenen un ejecutable).
* No incluyas múltiples kexts iguales en tu config.plist (por ejemplo si incluyes varias copias de VoodooInput de múltiples kexts, recomendamos elegir el primer kext en la formación de tu config y deshabilitar el resto.

## Kernel Panic `AppleIntelMCEReporter`

Con macOS Catalina, el soporte de doble socket está roto, y un dato curioso sobre el firmware de AMD es que algunas placas informarán en realidad múltiples CPU enchufadas. Para solucionar esto, agrega [AppleMCEReporterDisabler](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip) a EFI/OC/Kexts y config.plist -> Kernel -> Add

## Kernel Panic `AppleIntelCPUPowerManagement`

Esto probablemente se deba a que NullCPUPowerManagement no está presente o no funciona, el que está alojado en la Guía Vanilla de AMD OSX está dañado. Ve a gritarle a Shannee para que lo arregle. Para solucionar el problema, elimina NullCPUPowerManagement de `Kernel -> Add` y `EFI/OC/Kexts` y luego habilita `DummyPowerManagement` en`Kernel -> Quirks`

## El teclado funciona pero el trackpad no

Asegúrate de que VoodooInput está listado *antes* que VoodooPS2 y VoodooI2C en tu config.plist.

## `kextd stall[0]: AppleACPICPU`

Esto se debe a que falta un emulador SMC o está roto, asegúrate de lo siguiente:

* Lilu y VirtualSMC están en EFI/OC/kexts y en tu config.plist
* Lilu está antes de VirtualSMC en la lista de kexts
* El último recurso es probar FakeSMC en su lugar, **no uses FakeSMC y VirtualSMC al mismo tiempo**

## Kernel Panic en AppleIntelI210Ethernet

Si tienes una placa madre Comet lake con el NIC i225-V, es posible que experimentes un kernel panic en el arranque debido al kext i210. Para resolver esto, asegúrate de tener el PciRoot correcto para tu Ethernet. Este suele ser:

* PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0, 0x0)
  * Por defecto, este es el que usan las placas madre Asus y Gigabyte
* PciRoot(0x0)/Pci(0x1C,0x4)/Pci(0x0,0x0)
  * Otros fabricantes pueden usar esto en su lugar

# Kernel panic en "Wrong CD Clock Frequency" con laptops Icelake

::: tip Ejemplo

![](../../images/troubleshooting/troubleshooting-md/cd-clock.jpg)

:::

Para resolver este kernel panic asegúrate de tener `-igfxcdc` en tus boot-args.

## Kernel Panic `AppleACPIPlatform` en 10.13

 ![](../../images/troubleshooting/troubleshooting-md/KA5UOGV.png)

 En macOS 10.13, el SO es mucho más estricto con tablas ACPI, [específicamente un bug con cómo eran manejados los "headers"](https://alextjam.es/debugging-appleacpiplatform/). Para resolver este kernel panic asegúrate de habilitar `NormalizeHeaders` en ACPI -> Quirks en tu config.plist
