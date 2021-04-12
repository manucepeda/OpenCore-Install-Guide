# Recolectando archivos

* Versión soportada: 0.6.8

Esta sección es para obtener otros archivos necesarios para arrancar macOS, esperamos que conozcas bien tu hardware  antes de comenzar y que hayas hecho un hackintosh antes, ya que no entraremos en detalle aquí. 

> ¿Cuál es la mejor manera de averiguar si mi hardware es compatible?

Consulta la [**Página de limitaciones de hardware**](/macos-limits.md) para obtener una mejor idea de lo que requiere macOS para arrancar, el soporte de hardware entre Clover y OpenCore es bastante similar.

> ¿Cuáles son algunas formas de averiguar qué hardware tengo?

Mira la siguiente página: [Encontrando tu hardware](./find-hardware.md)

## Drivers del firmware

Los controladores de firmware son controladores utilizados por OpenCore en el entorno UEFI. Se requieren principalmente para arrancar una computadora, ya sea ampliando la capacidad de parcheo de OpenCore o mostrándole diferentes tipos de discos en el selector de OpenCore (específicamente, discos HFS).

* **Dato a tener en cuenta sobre la ubicación**: Estos archivos **deben** colocarse en `EFI / OC / Drivers /`

### Universal

::: tip Drivers requeridos 

Para la mayoría de los sistemas, solo necesitarás 2 controladores `.efi` para comenzar a funcionar:

* [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)
  * Necesario para ver volúmenes HFS (es decir, instaladores de macOS y particiones/imágenes de recuperación). **No mezclar con otros drivers HFS**
  * En el caso de que tengas Sandy Bridge y anterior (al igual que Ivy Bridge de gama baja (i3 y Celerons), mira la seccion legacy abajo.
* [OpenRuntime.efi](https://github.com/acidanthera/OpenCorePkg/releases)
  * Reemplazo para [AptioMemoryFix.efi](https://github.com/acidanthera/AptioFixPkg), usado como una extensión para OpenCore que sirve para ayudar a parchear boot.efi, para arreglos relacionados a NVRAM y para una mejor administración de memoria.

:::

### Usuarios Legacy 

Además de lo anterior, si tu hardware no es compatible con UEFI (2011 y anterior), necesitarás lo siguiente. Presta atención a cada entrada, ya que es posible que no necesites todas:

* [OpenUsbKbDxe.efi](https://github.com/acidanthera/OpenCorePkg/releases)
  * Se utiliza para el menú de selección de OpenCore en **sistemas legacy que ejecutan DuetPkg**, [no recomendado e incluso dañino en UEFI (Ivy Bridge y más reciente)](https://applelife.ru/threads/opencore-obsuzhdenie-i-ustanovka.2944066/page-176#post-856653)

* [HfsPlusLegacy.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlusLegacy.efi)
  * Variante legacy de HfsPlus, utilizada para sistemas que carecen de soporte de instrucción RDRAND. Esto es generalmente visto en Sandy Bridge y anterior (al igual que Ivy Bridge de gama baja, como i3 y Celerons) 
  
* [OpenPartitionDxe](https://github.com/acidanthera/OpenCorePkg/releases)
  * Este archivo está incluido en OpenCorePkg en EFI/OC/Drivers
	    * Nota: Usuarios de OpenDuet (es decir, sin UEFI) tendrán este driver incorporado, por lo que no lo requieren.
    * No es necesario para OS X 10.10, Yosemite, y posterior

Estos archivos irán en tu carpeta Drivers en tu EFI:

::: details Específicos de 32 bits

Para aquellos que tengan CPUs de 32 bits, querrás agarrar estos drivers también:

* [HfsPlus32](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus32.efi)
  * Alternativa a HfsPlusLegacy pero para CPUs de 32 bits, no lo mezcles con otros drivers HFS .efi

:::

## Kexts

Un kext es una extensión del kernel, o **k**ernel **ext**ension en inglés, puedes pensar en esto como un driver para macOS, estos archivos irán en la carpeta Kexts en tu EFI.

* **Nota para Windows y Linux**: Los kexts se verán como carpetas normales en tu sistema operativo, **verifica** que la carpeta que estás instalando tiene una extensión .kext visible (y no agregues una manualmente si falta).
  * Si cualquier kext también include un archivo `.dSYM`, puedes simplemente eliminarlo, ya que sólo son para depuración. 
* **Nota importante sobre la ubicación**: Estos archivos **deben** ser puestos en `EFI/OC/Kexts/`.

Todos los kexts mencionados abajo pueden ser encontrados **pre-compilados** [en el repositorio de kexts](http://kexts.goldfish64.com/). Los kexts aquí son compilados cada vez que hay un commit. 

### Kexts imprescindibles

Sin los siguientes 2, ningún sistema es booteable:

* [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)
  * Emula el chip SMC que se encuentra en Macs reales, sin este macOS no se iniciará
  * La alternativa es FakeSMC, que puede tener mejor o peor soporte, más comúnmente utilizado en hardware Legacy.
  * Requiere 10.6 o más reciente
* [Lilu](https://github.com/acidanthera/Lilu/releases)
  * Un kext para parchear muchos procesos, requerido para AppleALC, WhateverGreen, VirtualSMC y muchos otros kexts. Sin Lilu, no funcionarán.
  * Ten en cuenta que Lilu y sus plugins requieren de OS X 10.8 y posterior para funcionar
  
::: details Kexts imprescindibles Legacy

Si estás planeando en arrancar OS X 10.7 y anterior, querrás optar por el kext de abajo en vez de VirtualSMC:

* [FakeSMC-32](https://github.com/khronokernel/Legacy-Kexts/blob/master/32Bit-only/Zip/FakeSMC-32.kext.zip?raw=true)

Recuerda que si no estás planeando en arrancar estos sistemas operativos antiguos, puedes ignorar estos kexts

* **Nota de OS X 10.4 y 10.5**: Incluso en CPUs de 64 bits, el kernelspace de OS X continúa siendo de 32 bits, por lo que recomendamos el uso de FakeSMC-32 en conjunto con VirtualSMC, específicamente configurando la `Arch` de FakeSMC-32 a `i386` y la de VirtualSMC a `x86_64`. Esto será discutido luego en la guía.

:::


### Plugins de VirtualSMC

Los complementos a continuación no son necesarios para bootear, y simplemente agregan funcionalidad adicional al sistema, como la supervisión del hardware (ten en cuenta que VirtualSMC soporta 10.6, los plugins podrían requerir 10.8+):

* SMCProcessor.kext
  * Utilizado para monitorear la temperatura de la CPU, **No funciona en sistemas basados en CPUs de AMD**
* SMCSuperIO.kext
  * Utilizado para monitorear la velocidad de los ventiladores, **No funciona en sistemas basados en CPUs de AMD**
* SMCLightSensor.kext
  * Utilizado para el sensor de luz ambiental en laptops, **las computadoras de escritorio pueden ignorar esto**
  * No lo uses si no tienes un sensor de luz ambiental, de lo contrario este kext puede causar problemas
* SMCBatteryManager.kext
  * Utilizado para medir lecturas de batería en laptops, **las computadoras de escritorio pueden ignorar esto**
  * No usar hasta que la batería haya sido parcheada correctamente, de lo contrario puede causar problemas
* SMCDellSensors.kext
  * Permite una supervisión y un control más preciso de los ventiladores en las computadoras Dell. No lo uses si no tienes una computadora Dell compatible

### Gráficos

* [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)
  * Utilizado para DRM, el boardID, arreglos del framebuffer, etc., todas las GPUs se benefician de este kext.
  * Ten en cuenta que el archivo SSDT-PNLF.dsl incluido sólo se requiere para laptops y computadoras All-In-One, consulta [Introducción a ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) para obtener más información

### Audio


* [AppleALC](https://github.com/acidanthera/AppleALC/releases)
  * Usado para parchear AppleHDA, permitiendo el soporte de la mayoría de controladores de sonido a bordo
  * AMD 15h/16h pueden tener problemas con esto y los sistemas Ryzen/Threadripper rara vez tienen soporte de micrófono.
  * Requiere OS X 10.8 o más reciente

  
::: details Kexts de audio Legacy

Para aquellos que planeen en arrancar 10.7 y más antiguo podrían querer optar por estos kexts en vez de AppleALC:

* [VoodooHDA](https://sourceforge.net/projects/voodoohda/)
  * Requiere OS X 10.6 o más nuevo
  
* [VoodooHDA-FAT](https://github.com/khronokernel/Legacy-Kexts/blob/master/FAT/Zip/VoodooHDA.kext.zip)
  * Similar al de arriba, pero este soporta kernels de 32 y 64 bits, por lo que es perfecto para arrancar OS X 10.4-5 y para CPUs de 32 bits


:::

### Ethernet

Ahora asumiremos que sabes qué tarjeta de ethernet tiene tu sistema, recuerda que las páginas de especificaciones probablemente incluyan esta información

* [IntelMausi](https://github.com/acidanthera/IntelMausi/releases)
  * Requerido por la mayoría de NICs de Intel, chipsets basados en I211 necesitarán el kext SmallTreeIntel82576.
  * Los NICs 82578, 82579, i217, i218 e i219 de Intel son soportados oficialmente.
  * Requiere OS X 10.9 o más nuevo, los usuarios de 10.8 y más antiguo pueden usar IntelSnowMausi en vez de éste para versiones más antiguas
* [SmallTreeIntel82576 kext](https://github.com/khronokernel/SmallTree-I211-AT-patch/releases)
  * Necesario para NICs i211 de Intel, este kext está basado en el kext SmallTree pero ha sido parcheado para soportar I211.
  * Necesario para la mayoría de placas base AMD con NICs de Intel
  * Requiere OS X 10.9-12 (v1.0.6), macOS 10.13-14(v1.2.5), macOS 10.15+(v1.3.0)
* [AtherosE2200Ethernet](https://github.com/Mieze/AtherosE2200Ethernet/releases)
  * Necesario para NICs Atheros y Killer
  * Requiere OS X 10.8 o más nuevo
  * Nota: Los modelos Atheros Killer E2500 en realidad están basados en Realtek, si tienes uno de estos por favor utiliza [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases) en vez de este
* [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases)
  * Para el Ethernet Gigabit de Realtek
  * Requiere OS X 10.8-11(2.2.0), 10.12-13(v2.2.2), 10.14+(2.3.0)
* [LucyRTL8125Ethernet](https://www.insanelymac.com/forum/files/file/1004-lucyrtl8125ethernet/)
  * Para el Ethernet Realtek de 2.5Gb
* Para las NICs i225-V de Intel, los parches se mencionan en la sección de "Device Properties" en la guía para Comet Lake de escritorio. No se requiere kext.
  * Requiere macOS 10.15 y posterior

::: details Kexts de ethernet Legacy

Relevante para instalaciones legacy de macOS o hardware antiguo.

* [AppleIntele1000e](https://github.com/chris1111/AppleIntelE1000e/releases)
  * Principalmente relevante para controladores de ethernet Intel 10/100MBe 
  * Requiere 10.6 o más nuevo
* [RealtekRTL8100](https://www.insanelymac.com/forum/files/file/259-realtekrtl8100-binary/)
  * Principalmente relevante para controladores de ethernet Realtek basados en 10/100MBe 
  * Requiere de macOS 10.12 o más nueva con la versión 2.0.0+
* [BCM5722D](https://github.com/chris1111/BCM5722D/releases)
  * Principalmente relevante para controladores de Etherenet Broadcom basados en BCM5722
  * Requiere de OS X 10.6 o más nuevo

:::

También ten en cuenta que ciertas NICs están soportadas nativamente en macOS:

::: details Controladores de Ethernet nativos

#### Series Aquantia

```md
# AppleEthernetAquantiaAqtion.kext
pci1d6a,1    = Aquantia AQC107
pci1d6a,d107 = Aquantia AQC107
pci1d6a,7b1  = Aquantia AQC107
pci1d6a,80b1 = Aquantia AQC107
pci1d6a,87b1 = Aquantia AQC107
pci1d6a,88b1 = Aquantia AQC107
pci1d6a,89b1 = Aquantia AQC107
pci1d6a,91b1 = Aquantia AQC107
pci1d6a,92b1 = Aquantia AQC107
pci1d6a,c0   = Aquantia AQC113
pci1d6a,4c0  = Aquantia AQC113
```

**Nota**: Debido a algún firmware desactualizado enviado con muchas NICs Aquantia, podrías tener que actualizarlo en Windows/Linux para asegurarte de que es compatible con macOS
#### Series Intel

```md
# AppleIntel8254XEthernet.kext
pci8086,1096 = Intel 80003ES2LAN
pci8086,100f = Intel 82545EM
pci8086,105e = Intel 82571EB/82571GB

# AppleIntelI210Ethernet.kext
pci8086,1533 = Intel I210
pci8086,15f2 = Intel I225LM (Added in macOS 10.15)

# Intel82574L.kext
pci8086,104b = Intel 82566DC
pci8086,10f6 = Intel 82574L

```

#### Series Broadcom

```md
# AppleBCM5701Ethernet.kext
pci14e4,1684 = Broadcom BCM5764M
pci14e4,16b0 = Broadcom BCM57761
pci14e4,16b4 = Broadcom BCM57765
pci14e4,1682 = Broadcom BCM57762
pci14e4,1686 = Broadcom BCM57766
```

:::

### USB

* [USBInjectAll](https://github.com/Sniki/OS-X-USB-Inject-All/releases)
  * Se utiliza para inyectar controladores USB Intel en sistemas sin puertos USB definidos en ACPI
  * **No funciona** en CPUs de AMD
  * No debería ser necesario en Skylake de escritorio y posterior
    * AsRock es tonto y necesita esto
    * Sin embargo, recomendamos el uso de este en laptops Coffee Lake y más antiguas

* [XHCI-unsupported](https://github.com/RehabMan/OS-X-USB-Inject-All)
  * Necesario para controladores USB no nativos
  * Sistemas basados en CPUs de AMD no necesitan esto
  * Chipsets que comúnmente necesitan esto:
    * H370
    * B360
    * H310
    * Z390 (No necesario en Mojave y posterior)
    * X79
    * X99
    * Placas madre AsRock (en las placas base Intel específicamente, sin embargo las placas B460/Z490+ no lo necesitan)

### WiFi y Bluetooth

#### Intel

* [AirportItlwm](https://github.com/OpenIntelWireless/itlwm/releases)
  * Agrega soporte para una gran variedad de tarjetas de WiFi Intel y funciona nativamente gracias a la integración de IO80211Family
  * Requiere macOS 10.13 o posterior al igual que el arranque seguro de Apple para funcionar correctamente
* [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases)
  * Agrega soporte de Bluetooth a macOS cuando es emparejado con una tarjeta inalámbrica Intel
  * Requiere de macOS 10.13 o más nuevo

::: details Más información en la habilitación de AirportItlwm

Para habilitar el soporte de AirportItlwm con OpenCore, necesitarás:

* Habilitar `Misc -> Security -> SecureBootModel` por configurarlo a `Default` o a otro valor válido
  * Esto es discutido luego tanto en la guía y en la página de post instalación: [Arranque seguro de Apple](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html)
* Si no puedes habilitar SecureBootModel, puedes forzar la inyección de IO80211Family(**No recomendado**)
  * Configura lo siguiente debajo de `Kernel -> Force` en tu config.plist (discutido luego en la guía):
    * Catalina and older need not concern

![](./images/ktext-md/force-io80211.png)

:::

#### Broadcom

* [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup/releases)
  * Se usa para parchear tarjetas Broadcom que no son de Apple, **no funcionará en Intel, Killer, Realtek, etc.**
* [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases)
  * Se utiliza para cargar firmware en el chipset Bluetooth Broadcom, requerido para todas las tarjetas que no sean Apple/Airport de Fenvi.
  * Debe ser emparejado con BrcmFirmwareData.kext
    * BrcmPatchRAM3 para 10.15+ (debe estar acompañado de BrcmBluetoothInjector)
    * BrcmPatchRAM2 para 10.11-10.14
    * BrcmPatchRAM para 10.10 o anterior

::: details Orden de carga de BrcmPatchRAM 

El órden en `Kernel -> Add` debe ser:

1. BrcmBluetoothInjector
2. BrcmFirmwareData
3. BrcmPatchRAM3

Sin embargo ProperTree hará esto por ti, asi que no debes preocuparte

:::

### Kexts específicos de AMD

* [~~NullCPUPowerManagment~~](https://www.youtube.com/watch?v=dQw4w9WgXcQ)
  * Tenemos una solución mucho mejor conocida como `DummyPowerManagement` que se encuentra en `Kernel -> Quirks` en su config.plist, esto se tratará en una página luego
* [XLNCUSBFIX](https://cdn.discordapp.com/attachments/566705665616117760/566728101292408877/XLNCUSBFix.kext.zip)
  * Arreglo de USBs para sistemas AMD FX, no recomendado para Ryzen
* [VoodooHDA](https://sourceforge.net/projects/voodoohda/)
  * Audio para sistemas FX y soporte de Mic+Audio en panel frontal para sistemas Ryzen, no mezclar con AppleALC. La calidad de audio es notablemente peor que AppleALC en CPUs Zen. Requiere de OS X 10.6 o más reciente

### Extras

* [AppleMCEReporterDisabler](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip)
  * Útil desde Catalina en adelante para deshabilitar el kext AppleMCEReporter que causará pánicos en el kernel en las CPUs AMD y los sistemas de doble socket.
  * SMBIOS afectadas:
    * MacPro6,1
    * MacPro7,1
    * iMacPro1,1
* [CpuTscSync](https://github.com/lvs1974/CpuTscSync/releases)
  * Necesario para sincronizar el TSC en algunas de las placas madre de servidores y HEDT de Intel, sin este macOS puede ser extremadamente lento o incluso no booteable. 

* [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)
  * Se utiliza para arreglar la administración de energía y la inicialización en NVMes que no sean de Apple, requiere macOS 10.14 o posterior

### Específicos de laptops

Para saber qué tipo de teclado y trackpad tienes, consulta el Administrador de dispositivos en Windows o `dmesg | grep input` en Linux

#### Input drivers

* [VoodooPS2](https://github.com/acidanthera/VoodooPS2/releases)
  * Necesario para sistemas con teclados y trackpads PS/2
  * Usuarios de Trackpad también deben emparejar esto con [VoodooInput](https://github.com/acidanthera/VoodooInput/releases)
* [VoodooRMI](https://github.com/VoodooSMBus/VoodooRMI/releases/)
  * Para sistemas con dispositivos basados en SMBus, principalmente para trackpads y trackpoints. Comunmente encontrado en dispositivos Synaptics.
  * Requiere de OS X 10.11 o más nuevo para funciones MT2
* [VoodooI2C](https://github.com/alexandred/VoodooI2C/releases)
  * Se utiliza para reparar dispositivos I2C, los cuales se encuentran en algunas laptops con touchpads y pantallas táctiles más elegantes. 
  * Debe ser emparejado a un plugin:
    * VoodooI2CHID - Implementa la especificación del dispositivos HID de Microsoft.
    * VoodooI2CElan - Implementa soporte para dispositivos propietarios de Elan. (no funciona en ELAN1200 +, usa el HID en vez de este)
    * VoodooI2CSynaptics - Implementa soporte para dispositivos propietarios de Synaptics.
    * VoodooI2CFTE - Implementa soporte para el touchpad FTE1001.
    * VoodooI2CUPDDEngine - Implementa el soporte de drivers Touchbase.

#### Otros

Consulta [Kexts.md](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Kexts.md) para obtener una lista completa de los kexts compatibles

## SSDTs

Probablemente cuando veas todos esos SSDTs en la carpeta AcpiSamples te preguntarás si necesitas alguno de ellos. Por eso, ahora repasaremos qué SSDT(s) necesitas en **TU sección ACPI específica de tu config.plist**, ya que los SSDTs que necesitas son específicos de cada plataforma. Algunos pueden ser incluso específicos de algun sistema en particular donde deben configurarse. Es posible que te pierdas fácilmente si te doy una lista de SSDTs para elegir.

[Comenzando con ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) tiene una sección más a fondo sobre SSDTs que incluye la información de cómo compilarlos en diferentes plataformas.

Un dato rápido importante de los SSDTs necesarios por si no lo sabías (este es el código fuente, deberás compilarlos en un archivo .aml):

### PCs de escritorio

| Platforms | **CPU** | **EC** | **AWAC** | **NVRAM** | **USB** |
| :-------: | :-----: | :----: | :------: | :-------: | :-----: |
| Penryn | N/A | [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | N/A | N/A | N/A |
| Lynnfield y Clarkdale | ^^ | ^^ | ^^ | ^^ | ^^ |
| SandyBridge | [CPU-PM](https://dortania.github.io/OpenCore-Post-Install/universal/pm.html#sandy-and-ivy-bridge-power-management) (Correr en post instalación) | ^^ | ^^ | ^^ | ^^ |
| Ivy Bridge | ^^ | ^^ | ^^ | ^^ | ^^ |
| Haswell | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html) | ^^ | ^^ | ^^ | ^^ |
| Broadwell | ^^ | ^^ | ^^ | ^^ | ^^ |
| Skylake | ^^ | [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | ^^ | ^^ | ^^ |
| Kaby Lake | ^^ | ^^ | ^^ | ^^ | ^^ |
| Coffee Lake | ^^ | ^^ | [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) | [SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html) | ^^ |
| Comet Lake | ^^ | ^^ | ^^ | ^^ | [SSDT-RHUB](https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub.html) |
| AMD (15/16h) | N/A | ^^ | N/A | ^^ | N/A |
| AMD (17/19h) | [SSDT-CPUR para B550 y A520](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml) | ^^ | ^^ | ^^ | ^^ |

### PCs de escritorio de gama alta

| Platforms | **CPU** | **EC** | **AWAC** |
| :-------: | :-----: | :----: | :------: |
| Nehalem and Westmere | N/A | [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | N/A |
| Ivy Bridge-E | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html) | ^^ | ^^ | [SSDT-UNC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/unc0) |
| Haswell-E | ^^ | [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | [SSDT-RTC0-RANGE](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) | ^^ |
| Broadwell-E | ^^ | ^^ | ^^ |
| Skylake-X | ^^ | ^^ | [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) |

### Laptops


| Platformas | **CPU** | **EC** | **Backlight** | **Trackpad I2CI2C Trackpad** | **AWAC** | **USB** | **IRQ** |
| :-------: | :-----: | :----: | :-----------: | :--------------: | :------: | :-----: | :-----: |
| Clarksfield and Arrandale | N/A | [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | [SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html) | N/A | N/A | N/A | [IRQ SSDT](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq.html) |
| SandyBridge | [CPU-PM](https://dortania.github.io/OpenCore-Post-Install/universal/pm.html#sandy-and-ivy-bridge-power-management) (Correr en post instalación) | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Ivy Bridge | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Haswell | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html) | ^^ | ^^ | [SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html) | ^^ | ^^ | ^^ |
| Broadwell | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Skylake | ^^ | [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | ^^ | ^^ | ^^ | ^^ | N/A |
| Kaby Lake | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Coffee Lake (8va Gen) y Whiskey Lake | ^^ | ^^ | [SSDT-PNLF-CFL](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html) | ^^ | [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) | ^^ | ^^ |
| Coffee Lake (9na Gen) | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Comet Lake | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Ice Lake | ^^ | ^^ | ^^ | ^^ | ^^ | [SSDT-RHUB](https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub.html) | ^^ |

Continuando:

| Platformas | **NVRAM** | **IMEI** |
| :-------: | :-------: | :------: |
|  Clarksfield y Arrandale | N/A | N/A |
| Sandy Bridge | ^^| [SSDT-IMEI](https://dortania.github.io/Getting-Started-With-ACPI/Universal/imei.html) |
| Ivy Bridge | ^^ | ^^ |
| Haswell | ^^ | N/A |
| Broadwell | ^^ | ^^ |
| Skylake | ^^ | ^^ |
| Kaby Lake | ^^ | ^^ |
| Coffee Lake (8va Gen) y Whiskey Lake | ^^ | ^^ |
| Coffee Lake (9na Gen) | [SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html) | ^^ |
| Comet Lake | N/A | ^^ |
| Ice Lake | ^^ | ^^ |

# Ahora, con todo esto hecho dirígete a [Comenzando con ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
