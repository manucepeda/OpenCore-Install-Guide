# Proceso de instalación

Ahora que has terminado de configurar OpenCore, finalmente puedes iniciar, lo más importante a tener en cuenta:

* Habilitea la configuración de BIOS óptima para macOS
* Lee la [guía de Multiboot](https://dortania.github.io/OpenCore-Multiboot/)
  * Principalmente relevante para aquellos que tienen un solo disco para múltiples sistemas operativos
* Una copia de la [página de solución de problemas](../troubleshooting/troubleshooting.md)
* Lee el [proceso de arranque de macOS](../troubleshooting/boot.md)
  * Puede ayudar a aquellos que están instalando por primera vez a comprender mejor dónde pueden estar atascados
* Un montón de paciencia

## Revisando tu trabajo

La última cosa que debemos hacer antes de intentar arrancar es mirar la estructura y archivos de tu EFI:

EFI buena          |  EFI mala
:-------------------------:|:-------------------------:
![](../images/installation/install-md/good-efi.png)  |  ![](../images/installation/install-md/bad-efi.png)
*Carpeta EFI encontrada en la partición EFI |* Falta la carpeta EFI
*Los archivos ACPI están compilados(.aml) |* Los archivos ACPI no están compilados (.dsl)
*El DSDT no está incluido |* El DSDT está incluido
*Se eliminaron los drivers innecesarios (.efi) |* Deja los drivers predeterminados
*Se eliminaron las herramientas (tools) innecesarias (.efi) |* Deja las herramientas predeterminadas
*Todos los archivos en la carpeta Kexts terminan en .kext |* Incluye código base y carpetas
*config.plist debajo de EFI/OC |* No se cambió el nombre del .plist ni está en el lugar correcto
*Sólo se usan kexts que necesitas |* Se descargan todos los kexts listados

Acuérdate de que el sanity checker es tu amigo:

* [**Sanity Checker**](https://opencore.slowgeek.com)

## Arrancando el USB de OpenCore

Entonces, ahora estás listo para finalmente colocar el USB en tu computadora y arrancarla, recuerda que tu PC seguirá utilizando la unidad interna con el SO que estás usando por defecto, por lo que tendrás que ingresar a la BIOS o al menú de arranque y seleccionar el USB. Tendrás que consultar el manual de usuario o buscar en Google para averiguar que tecla Fn accede a la BIOS y al menú de arranque (Esc, F2, F10, F12, etc.)

Una vez que inicie el USB, es probable que recibas las siguientes opciones de inicio:

1. Windows
2. macOS Base System (External) / Install macOS Catalina (External)
3. OpenShell.efi
4. Reset NVRAM

En nuestro caso, **la Opción 2** es la que queremos. Dependiendo de cómo se realizó el instalador, puede mostrarse como como **"macOS Base System (External)"**  si se creó en Linux o Windows e **"Install macOS Catalina (External)"** si se creó en macOS.

## Instalador de macOS

Finalmente has arrancado el instalador, has pasado por verbose y llegado al instalador. Ahora que has llegado hasta aquí, esto es lo más importante a tener en cuenta:

* Las unidades en las que deseas instalar macOS **deben** tener un esquema de partición GUID **y** APFS
  * High Sierra en discos duros mecánicos (HDD) y todos los usuarios de Sierra deberán usar macOS Journaled (HFS+)
* La unidad **debe** también tener una partición de 200 MB
  * De forma predeterminada, macOS configurará unidades recién formateadas con una partición de 200 MB
  * Ve a la [guía de Multiboot](https://hackintosh-multiboot.gitbook.io/hackintosh-multiboot/) para obtener más información sobre cómo particionar una unidad de Windows

Una vez que comience la instalación, tendrás que esperar hasta que el sistema se reinicie. Una vez más, querrás iniciar en OpenCore, pero en lugar de seleccionar su instalador/disco de recuperación USB, tendrás que seleccionar la opción "macOS Installer" en el disco duro para continuar con la instalación. Debería obtener un logotipo de manzana, y después de unos minutos debería aparecer un temporizador en la parte inferior que diga "x minutos restantes". Este puede ser un buen momento para tomar una bebida o comer algo, ya que esto llevará un tiempo. Puede reiniciarse un par de veces más, pero si todo va bien, finalmente deberías llegar a la pantalla de bienvenida, donde podrás configurar todo.

Estás dentro! 🎉
Deberás ir a la página de post instalación para terminar de configurar tu sistema.
