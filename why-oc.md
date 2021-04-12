
# ¿Por qué OpenCore encima de Clover y otros?

* Versión soportada 0.6.8

Esta sección es un breve resumen de por qué la comunidad ha estado haciendo la transición a OpenCore. Aquellos que solo quieren una máquina con macOS pueden omitir esta página.

- [¿Por qué OpenCore encima de Clover y otros?](#por-qué-opencore-encima-de-clover-y-otros)
  - [Características de OpenCore](#características-de-opencore)
  - [Soporte de Software](#soporte-de-software)
  - [Inyección de Kexts](#inyección-de-kexts)
- [Mitos comunes](#mitos-comunes)
  - [OpenCore no es estable debido a que está en beta](#opencore-no-es-estable-debido-a-que-está-en-beta)
  - [OpenCore siempre inyecta datos de SMBIOS y ACPI a otros sistemas operativos](#opencore-siempre-inyecta-datos-de-smbios-y-acpi-a-otros-sistemas-operativos)
  - [OpenCore requiere de una instalación limpia](#opencore-requiere-de-una-instalación-limpia)
  - [Does OpenCore only support limited versions of macOS](#does-opencore-only-support-limited-versions-of-macos)
  - [¿OpenCore soporta hardware antiguo?](#opencore-soporta-hardware-antiguo)
  - [¿OpenCore soporta el arranque de Windows/Linux?](#opencore-soporta-el-arranque-de-windowslinux)
  - [La legalidad de hackintoshing](#la-legalidad-de-hackintoshing)
  - [¿macOS soporta GPUs de Nvidia?](#macos-soporta-gpus-de-nvidia)

## Características de OpenCore

* En promedio, los sistemas con OpenCore se inician más rápido que los que usan Clover, ya que se realizan menos parches innecesarios.
* Mejor estabilidad general debido a que los parches pueden ser mucho más precisos:
  * [Actualización 10.15.4 de macOS (Post en inglés)](https://www.reddit.com/r/hackintosh/comments/fo9bfv/macos_10154_update/)
  * Los parches AMD no necesitan actualizarse con cada actualización de seguridad menor
* Mayor seguridad en general:
  * No es necesario deshabilitar la Protección de integridad del sistema (SIP)
  * Soporte de FileVault 2 incorporado
  * [Vaulting](https://dortania.github.io/OpenCore-Post-Install/universal/security.html#Vault) Permite crear EFIs instantáneas que eviten modificaciones no deseadas
  * Soporte de arranque seguro o "secure boot" (actualmente en auditoría de seguridad, vendrá próximamente)
* Cambios de SO con BootCamp y la selección del dispositivo de arranque son compatibles ya que OpenCore lee las variables NVRAM configuradas por el disco de arranque como una Mac real.
* Omite la tecla de acceso rápido de arranque a través de boot.efi. Mantiene presionada la tecla `Option` o `ESC` al inicio para elegir un dispositivo de arranque, `Cmd+R` para ingresar a Recuperación o `Cmd+Opt+P+R` para restablecer NVRAM.

## Soporte de Software

La principal razón por la que alguien puede querer cambiar de otros gestores de arranque a OpenCore es en realidad por el soporte de software:

* No se hacen mas pruebas de Kexts con Clover:
  * Tienes un bug con un kext? Muchos desarrolladores incluyendo la organización [Acidanthera](https://github.com/acidanthera), la cual hace la mayoría de tus Kexts favoritos, no te darán soporte a menos que estés utilizando OpenCore.
* Muchos drivers de firmware han sido fusionados en OpenCore:
  * [Soporte de APFS](https://github.com/acidanthera/AppleSupportPkg)
  * [Soporte de FileVault](https://github.com/acidanthera/AppleSupportPkg)
  * [Parches de Firmware](https://github.com/acidanthera/AptioFixPkg)
* [Parches para AMD](https://github.com/AMD-OSX/AMD_Vanilla/tree/opencore):
  * Tienes hardware basado en AMD? Bueno, los parches de kernel requeridos para iniciar macOS ya no están soportados por Clover, y sólo funcionan con OpenCore.

## Inyección de Kexts

Para entender mejor el sistema de inyección de Kexts de OpenCore, primero debemos mirar cómo funciona Clover:

1. Parchea el SIP para deshabilitarlo
2. Parchea para habilitar el código zombie del XNU para poder inyectar kexts
3. Parchea la condición de carrera con inyección de kexts
4. Inyecta los kexts
5. Parchea el SIP para habilitarlo de nuevo

Cosas a tener en cuenta con el método de Clover:

* El llamado al código zombie del XNU no se ha utilizado desde 10.7, es sorprendente que Apple no haya quitado este código aún.
  * Con este parche, las actualizaciones del SO se rompen frecuentemente, como ha ocurrido recientemente con macOS 10.14.4 y 10.15
  * Deshabilita el SIP y luego intenta habilitarlo nuevamente, no creo que sea necesario decir mucho más.
* Probablemente se rompa con 10.16
* Soporta macOS hasta versiones antiguas (10.5 en adelante)

Hechémosle un vistazo al método de OpenCore:

1. Toma el llamado "prelinked kernel" y kexts listos para inyectar
2. Reconstruye el caché en el ambiente del EFI con los nuevos kexts
3. Agrega este nuevo cache

Cosas a tener en cuenta con el método de OpenCore:

* El formato del OS Agnostic como el prelinked kernel se ha quedado igual desde 10.6 (V2), por lo que es dificil de romper el soporte.
  * OpenCore también soporta el prelinked kernel (V1, encontrada en 10.4 y 10.5, cacheless, Mkext y KernelCollections por lo que también tiene soporte para todas las versiones de Intel de OS X/macOS. Sin embargo, el soporte adecuado ha sido agregado en 10.6 y posterior.
* Es mucho más estable, ya que se hacen mucho menos parches.

# Mitos comunes

## OpenCore no es estable debido a que está en beta

Respuesta corta: No

Respuesta larga: No

El número de versión de OpenCore no representa la calidad del proyecto. En cambio, es más una manera de ver el progreso del proyecto. Acidanthera sigue teniendo muchas cosas que quieren hacer con este proyecto, incluyendo refinamiento en general y soporte de aún más características.

Por ejemplo, OpenCore pasa por una auditoría de seguridad para asegurar que cumple con el arranque seguro UEFI, y es el único bootloader de Hackintosh que se somete a estas rigurosas revisiones y que cuenta con soporte de éste.

La versión 0.6.1 fue originalmente designada para ser el lanzamiento oficial de OpenCore ya que tendría soporte adecuado para Arranque Seguro UEFI/de Apple, y este sería el aniversario de un año de el lanzamiento de OpenCore como una herramienta pública. Sin embargo, debido a distintas circunstancias rodeando a macOS Big Sur y la reescritura del prelinker de OpenCore para soportar a dicha versión, se decidió posponer el lanzamiento oficial de 1.0.0 por otro año.

Plan de programación/Hoja de ruta:

* 2019: Año de beta
* 2020: Año del arranque seguro
* 2021: Año del refinamiento

Así que por favor no veas el número de versión como un obstáculo, y más como algo que estás esperando.

## OpenCore siempre inyecta datos de SMBIOS y ACPI a otros sistemas operativos

Por defecto, OpenCore asumirá que todos los sistemas operativos deben ser tratados por igual en lo que refiere a la información de ACPI y SMBIOS. La razón de esto se divide en tres partes principales:

* Esto permite soporte adecuado de multiboot como [BootCamp](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootcamp.html)
* Evita DSDTs mal hechos y anima a prácticas adecuadas de ACPI
* Evita casos extremos donde la información es inyectada varias veces, algo comúnmente visto con Clover
  * Por ejemplo; cómo manejerías la inyección de SMBIOS y datos de ACPI una vez que booteaste boot.efi, pero después te echa? Los cambios ya están en la memoria, por lo que intentar deshacerlos puede ser peligroso. Esto es la razón por la que el método de Clover está mal visto.

Sin embargo, hay quirks en OpenCore que permiten que la inyección de SMBIOS esté limitada a macOS mediante un parche vinculado a el parcheo de dónde macOS lee la información de SMBIOS. El quirk `CustomSMIOSGuid` con `CustomSMBIOSMode` puesto en `Custom` puede romperse en el futuro, por lo que sólo recomendamos esta opción en el evento de que cierto software se rompa en otros sistemas operativos. Para tener mejor estabilidad, por favor deshabilita estos quirks.

## OpenCore requiere de una instalación limpia

Esto no es cierto en el caso de que tengas una instalación "Vanilla". Esto se refiere a si el sistema operativo en sí se ha alterado en alguna manera, como instalando kexts de terceros en el volúmen del sistema u otras modificaciones no admitidas por Apple. Cuando tu sistema haya sido manipulado en gran medida por tí o por utilidades de terceros como Hackintool, recomendamos una nueva instalación para evitar posibles problemas.

Nota para usuarios de Clover: Reestablece tu NVRAM al hacer una instalación con OpenCore, ya que muchas de las variables de Clover pueden entrar en conflicto con OpenCore y macOS.

## Does OpenCore only support limited versions of macOS

Desde 0.6.2, ahora puedes arrancar **todas** las versiones de Intel de macOS partiendo de 10.4! El soporte adecuado depende de tu hardware, así que verifícalo por ti mismo: [Limitaciones de Hardware](macos-limits.md)

::: details Galería de instalación de macOS

Acidanthera ha probado muchas versiones, y [Khronokernel](https://github.com/khronokernel) ha corrido muchas versiones de OS X en su HP DC 7900(Core2 Quad Q8300). Esta es una galería de lo que ha probado:

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.4-Tiger.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.5-Leopard.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.6-Snow-Loepard.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.7-Lion.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.8-MountainLion.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.9-Mavericks.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.10-Yosemite.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.12-Sierra.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.13-HighSierra.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/10.15-Catalina.png)

![](./images/installer-guide/legacy-mac-install-md/dumpster/11-Big-Sur.png)

:::

## ¿OpenCore soporta hardware antiguo?

Por el momento, la mayoría del hardware de Intel está soportado siempre que el sistema operativo lo soporte. Sin embargo, por favor dirígete a la [página de limitaciones de Hardware](macos-limits.md) para obtener más información sobre qué hardware está soportado en qué versiones de macOS

Actualmente, las CPUs de Yonah en adelante han sido testeadas correctamente con OpenCore.

## ¿OpenCore soporta el arranque de Windows/Linux?

OpenCore funciona en la misma manera que cualquier otro bootloader, por lo que respeta a los otros sistemas operativos de la misma manera. Para cualquier otro SO donde el bootloader tiene una ruta o nombre irregular, puedes simplemente agregarlo a la sección BlessOverride

## La legalidad de hackintoshing

Si hablamos de hackintoshing, podemos decir que se encuenta en un "área gris" legal, principalmente porque si bien esto no es ilegal, estamos infringiendo el EULA (El acuerdo de licencia). La razón por la que no es ilegal es la siguiente:

* Estamos descargando macOS [directamente desde los servidores de Apple](https://github.com/acidanthera/OpenCorePkg/blob/0.6.8/Utilities/macrecovery/macrecovery.py#L125)
* Estamos haciendo esto como una organización sin fines de lucro para la enseñanza y uso personal
  * Las personas que planean en usar su Hackintosh para el trabajo o venderla deberían echarle un vistazo al [Caso Pystar](https://en.wikipedia.org/wiki/Psystar_Corporation) y sus leyes regionales

Mientras que la EULA establece que macOS sólo debe ser instalado en Macs reales ([sección 2B-i](https://www.apple.com/legal/sla/docs/macOSCatalina.pdf)) o en máquinas virtuales corriendo en Macs geniunas ([sección 2B-iii](https://www.apple.com/legal/sla/docs/macOSCatalina.pdf)), no hay ninguna ley aplicable que pueda prohibir esto por completo. Sin embargo, sitios que reempaqueten y modifiquen instaladores de macOS pueden potencialmente entrar en el riesgo de tener [Reclamos de DMCA (Derechos de Autor de la Era Digital; Digital Millennium Copyright Act en inglés)](https://es.wikipedia.org/wiki/Digital_Millennium_Copyright_Act) o algo similar.

* **Nota**: No somos asesores legales oficiales, así que por favor asesórate correctamente tú mismo y discute esto con tus abogados si tienes alguna preocupación.

## ¿macOS soporta GPUs de Nvidia?

Debido a problemas relacionados con la compatibilidad de Nvidia en las versiones más recientes de macOS, muchos usuarios han llegado a la conclusión de que macOS nunca soportó las GPUs de Nvidia y, en este momento, no lo hacen. Sin embargo, Apple todavía mantiene y soporta Macs con GPUs Nvidia en su último sistema operativo, como los modelos de MacBook Pro 2013 con GPU Kepler.

El problema principal gira en torno a las GPU Nvidia más nuevas, ya que Apple dejó de hacer máquinas con ellas y, por lo tanto, nunca tuvo soporte oficial para el sistema operativo de Apple, sino que los usuarios tuvieron que confiar en que Nvidia les dé drivers como un terceros. Debido a problemas con el Arranque seguro recientemente introducido por Apple, ya no podían soportar los WebDrivers y, por lo tanto, Nvidia no podía publicarlos para plataformas más nuevas, limitándolos a mac OS 10.13, High Sierra.

Para obtener más información sobre el soporte de sistemas operativos, dirígete aquí: [Guía de comradores de GPU](https://dortania.github.io/GPU-Buyers-Guide/)
