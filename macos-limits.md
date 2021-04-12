# Limitaciones de Hardware

Con macOS, hay numerosas limitaciones de hardware que debes tener en cuenta antes de iniciar una instalación. Esto se debe a la reducida cantidad de hardware que admite Apple, por lo que estamos limitados por Apple o por los parches que ha creado la comunidad.

Las secciones principales a verificar son:

* [CPU](#sporte-de-cpus)
* [GPU](#soporte-de-gpus)
* [Placa base](#soporte-de-placas-madre)
* [Almacenamiento](#compatibilidad-de-almacenamiento)
* [Internet cableado (Ethernet)](#conexion-a-internet-cableada)
* [Internet inalámbrico (WiFi)](#conexion-a-internet-inalambrica)
* [Otros](#otros)

Para guías más detalladas en el tema, véase:

* [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
  * Verifica si tu GPU es compatible y qué version de macOS puedes correr. 
* [Wireless Buyers Guide](https://dortania.github.io/Wireless-Buyers-Guide/)
  * Verifica si tu tarjeta WiFi es compatible.
* [Anti-Hardware Buyers Guide](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/)
  * Guía en general en qué evitar y qué problemas podrías enfrentarte con tus componentes. 

## Soporte de CPUs

Para el soporte de CPUs, tenemos el siguiente desglose:

* CPUs de 32 y 64 bits son soportadas
  * Sin embargo, esto requiere que el SO soporte tu arquitectura, mira los requerimientos de CPU abajo
* Las CPUs de escritorio de Intel son compatibles.
  * Esta guía soporta desde Sandy Bridge hasta Comet Lake.
  * Las series Bulldozer (15h), Jaguar (16h) y Ryzen (17h) AMD de escritorio.
  * CPUs de laptop **no** son compatibles.
~~* CPUs de gama alta y servidores de Intel.
  * Esta guía soporta desde Haswell-E hasta Cascade Lake X.
~~* CPUs de la serie i y Xeon para laptops
  * Esta guía soporta desde Ivy Bridge hasta Ice Lake.
  ~~* A tener en cuenta: los CPUs de la serie Atom, Celeron y Pentium no son compatibles.~~
* Las secciones tachadas no se encuentran disponibles por el momento, pero estarán incluidas en el futuro.
  
::: details Requerimientos de la CPU

Requerimientos de arquitectura

* CPUs de 32-Bits son soportadas desde 10.4.1 hasta 10.6.8
  * Ten en cuenta que 10.7.x requiere de un espacio de usuario de 64 bits, lo cual limita las CPUs de 32 bits a 10.6
* Las CPUs de 64 bits son soportadas de 10.4.1 en adelante. 

Requerimientos de SEE:

* SSE3 es necesario para todas las versiones de Intel de OS X/macOS
* SSSE3 es requerido para todas las versiones de 64 bits de OS X/macOS
  * Para las CPUs que no tengan SSSE3 (como ciertos Pentiums de 64 bits), recomendamos correr un espacio de usuario de 32 bits (`i386-user32`)
* SSE4 es requerido para macOS 10.12 y posterior
* SSE4.2 es necesario para macOS 10.14 y posterior
  * Las CPUs con SSE4.1 son soportadas con [telemetrap.kext](https://forums.macrumors.com/threads/mp3-1-others-sse-4-2-emulation-to-enable-amd-metal-driver.2206682/post-28447707)
  * Los drivers nuevos de AMD también requieren SSE4.2 para tener soporte de Metal, para resolver esto dirígete aquí (post en inglés): [MouSSE: SSE4.2 emulation](https://forums.macrumors.com/threads/mp3-1-others-sse-4-2-emulation-to-enable-amd-metal-driver.2206682/)

Requerimientos de Firmware:

* OS X 10.4.1 hasta 10.4.7 requiere EFI32(como la versión IA32(32-bit) de OpenCore)
  * OS X 10.4.8 hasta 10.7.5 soportan ambos EFI32 y EFI64
* OS X 10.8 y posterior requieren EFI64(es decir una versión de OpenCore X64(64-bit))
* OS X 10.7 hasta 10.9 requiere OpenPartitionDxe.efi para arrancar desde la partición de recuperación (recovery en inglés)

Requerimientos del Kernel:

* OS X 10.4 y 10.5 requieren Kexts de 32-bits debido a que sólo soportan un kernelspace de 32 bits
  * OS X 10.6 y 10.7 soportan kernelspaces de ambos 32 y 64 bits
* OS X 10.8 y posterior requieren Kexts de 64 bits debido a que sólo soportan un kernelspace de 64 bits
  * Corre `lipo -archs` para saber la arquitectura que soporta tu kext (recuerda correr esto en el binario y no el bundle .kext)

Notas especiales:

* Lilu y sus plugins requieren de 10.8 y posterior para operar
  * Recomendamos usar FakeSMC para veriones viejas de OS X
* OS X 10.6 y anterior requieren de RebuildAppleMemoryMap habilitado
  * Esto es para resolver un kernel panic temprano

:::
  
::: details Tabla de CPUs soportadas de Intel

*Nota: Los links informativos están en inglés*

| Generación de CPU | Soporte Inicial | Última versión soportada | Notas | CPUID |
| :--- | :--- | :--- | :--- | :--- |
| [Pentium 4](https://en.wikipedia.org/wiki/Pentium_4) | 10.4.1 | 10.5.8 | Sólo utilizado en kits de desarrolladores | 0x0F41 |
| [Yonah](https://en.wikipedia.org/wiki/Yonah_(microprocessor)) | 10.4.4 | 10.6.8 | 32Bit | 0x0006E6 |
| [Conroe](https://en.wikipedia.org/wiki/Conroe_(microprocessor)), [Merom](https://en.wikipedia.org/wiki/Merom_(microprocessor)) | 10.4.7 | 10.11.6 | No SSE4 | 0x0006F2 |
| [Penryn](https://en.wikipedia.org/wiki/Penryn_(microarchitecture)) | 10.4.8 | 10.13.6 | No SSE4.2 | 0x010676 |
| [Nehalem](https://en.wikipedia.org/wiki/Nehalem_(microarchitecture)) | 10.5.6 | Actual | N/A | 0x0106A2 |
| [Lynnfield](https://en.wikipedia.org/wiki/Lynnfield_(microprocessor)), [Clarksfield](https://en.wikipedia.org/wiki/Clarksfield_(microprocessor)) | 10.6.5 | Actual | Sin soporte de iGPU en 10.14+ | 0x0106E0 |
| [Westmere, Clarkdale, Arrandale](https://en.wikipedia.org/wiki/Westmere_(microarchitecture)) | 10.6.5 | Actual | Sin soporte de iGPU en 10.14+ | 0x0206C0 |
| [Sandy Bridge](https://en.wikipedia.org/wiki/Sandy_Bridge) | 10.6.7 | Actual | Sin soporte de iGPU en 10.14+ | 0x0206A0(M/H) |
| [Ivy Bridge](https://en.wikipedia.org/wiki/Ivy_Bridge_(microarchitecture)) | 10.7.3 | Actual | Sin soporte de iGPU en 11+ | 0x0306A0(M/H/G) |
| [Ivy Bridge-E5](https://en.wikipedia.org/wiki/Ivy_Bridge_(microarchitecture)) | 10.9.2 | Actual | N/A | 0x0306E0 |
| [Haswell](https://en.wikipedia.org/wiki/Haswell_(microarchitecture)) | 10.8.5 | Actual | N/A | 0x0306C0(S) |
| [Broadwell](https://en.wikipedia.org/wiki/Broadwell_(microarchitecture)) | 10.10.0 | Actual | N/A | 0x0306D4(U/Y) |
| [Skylake](https://en.wikipedia.org/wiki/Skylake_(microarchitecture)) | 10.11.0 | Actual | N/A | 0x0506e3(H/S) 0x0406E3(U/Y) |
| [Kaby Lake](https://en.wikipedia.org/wiki/Kaby_Lake) | 10.12.4 | Actual | N/A | 0x0906E9(H/S/G) 0x0806E9(U/Y) |
| [Coffee Lake](https://en.wikipedia.org/wiki/Coffee_Lake) | 10.12.6 | Actual | sopporte de igpu fue añadido en 10.13.6 | 0x0906EA(S/H/E) 0x0806EA(U)|
| [Amber](https://en.wikipedia.org/wiki/Kaby_Lake#List_of_8th_generation_Amber_Lake_Y_processors), [Whiskey](https://en.wikipedia.org/wiki/Whiskey_Lake_(microarchitecture)), [Comet Lake](https://en.wikipedia.org/wiki/Comet_Lake_(microprocessor)) | 10.14.1 | Actual | N/A | 0x0806E0(U/Y) |
| [Comet Lake](https://en.wikipedia.org/wiki/Comet_Lake_(microprocessor)) | 10.15.4 | Actual | N/A | 0x0906E0(S/H)|
| [Ice Lake](https://en.wikipedia.org/wiki/Ice_Lake_(microprocessor)) | 10.15.4 | Actual | N/A | 0x0706E5(U) |
| [Rocket Lake](https://en.wikipedia.org/wiki/Rocket_Lake) | ^^ | ^^ | Requiere CPUID de Comet Lake | 0x0A0671 |

:::

**Para obtener información más detallada, consulta aquí: [Guía de compradores Anti-Hackintosh](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/)**

## Soporte de GPUs

*Nota: Los links de información están en inglés*

El soporte de GPUs se vuelve mucho más complicado debido a la cantidad casi infinita de GPUs en el mercado, pero el desglose general es el siguiente:

* Las GPUs AMD basadas en GCN son compatibles con las versiones más recientes de macOS.
  * Sin embargo, las APUs de AMD no son compatibles.
  * Las GPUs de AMD con [núcleos basados en Lexa](https://www.techpowerup.com/gpu-specs/amd-lexa.g806) de la serie Polaris tampoco son compatibles.
* El soporte de GPUs de Nvidia es complicado:
  * Las GPUs de la serie [Maxwell(9XX)](https://en.wikipedia.org/wiki/GeForce_900_series) y [Pascal(10XX)](https://en.wikipedia.org/wiki/GeForce_10_series) están limitadas a macOS 10.13: High Sierra
  * La serie de GPUs [Turing de Nvidia(20XX,](https://en.wikipedia.org/wiki/GeForce_20_series)[16XX)](https://en.wikipedia.org/wiki/GeForce_16_series) **no son compatibles con ninguna versión de macOS**
  * La serie de GPUs [Ampere de Nvidia (30XX)](https://en.wikipedia.org/wiki/GeForce_30_series) **no están soportadas en ninguna versión de macOS**
  * Las GPUs de la serie [Kepler(7XX)](https://en.wikipedia.org/wiki/GeForce_700_series) son compatibles en las series más nuevas de macOS (Incluyendo macOS 11: Big Sur)
    * Esto es debido a que Apple sigue soportando algunas [MacBook Pros con GPUs de Nvidia](https://dortania.github.io/GPU-Buyers-Guide/modern-gpus/nvidia-gpu.html)
* iGPUs de Intel de la serie [GT2+](https://en.wikipedia.org/wiki/Intel_Graphics_Technology) 
  * Esta guía cubre iGPUs desde Ivy Bridge hasta Ice lake
  * Cabe mencionar que GT2 se refiere a la serie del iGPU, iGPUs de la serie GT1, encontrada en Pentiums, Celerons y Atoms no son compatibles con macOS.

Una nota importante para las **Laptops con GPU dedicada**:

* El 90% de las GPUs dedicadas en laptops no funcionarán porque están cableadas en una configuración que macOS no admite (gráficos intercambiables). Con las GPUs dedicadas de NVIDIA, esto generalmente se llama Optimus. No es posible utilizar estas GPUs para la pantalla interna, por lo que generalmente se recomienda desactivarlas y apagarlas (se tratará más adelante en esta guía).
* Sin embargo, en algunos casos, la GPU dedicada alimenta cualquier salida externa (HDMI, mini DisplayPort, etc.), que puede o no funcionar; en caso de que funcione, deberás mantener la tarjeta en funcionamiento.
* Sin embargo, hay algunas laptops que en raros casos no tienen gráficos intercambiables, por lo que se puede usar la tarjeta dedicada (si es compatible con macOS). Sin embargo, el cableado y la configuración de estas generalmente causan problemas.

**Si quieres una lista completa de las GPUs compatibles, visita la [Guía de compra de GPUs](https://dortania.github.io/GPU-Buyers-Guide/)**

::: details Tabla de soporte de GPUs Intel

*Nota: Los links de información están en inglés*

| Generación de GPU | Soporte Inicial | Última versión soportada | Notas |
| :--- | :--- | :--- | :--- |
| [GMA de 3ra gen](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Third_generation) | 10.4.1 | 10.7.5 | Requiere de un kernel de 32 bits |
| [GMA de 4ta gen](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen4) | 10.5.0 | 10.7.5 | Requiere de un kernel de 32 bits |
| [Arrendale (HD Graphics)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen5) | 10.6.5 | 10.13.6 | N/A |
| [Sandy Bridge (HD 3000)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen6) | 10.6.7 | 10.13.6 | N/A |
| [Ivy Bridge (HD 4000)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen7) | 10.7.3 | 10.15.7 | N/A |
| [Haswell (HD 4XXX, 5XXX)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen7) | 10.8.5 | Actual | N/A |
| [Broadwell (5XXX, 6XXX)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen8) | 10.10.0 | Actual | N/A |
| [Skylake (HD 5XX)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen9) | 10.11.0 | Actual | N/A |
| [Kaby Lake (HD 6XX)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen9) | 10.12.4 | Actual | N/A |
| [Coffee Lake (UHD 6XX)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen9) | 10.13.6 | Actual | N/A |
| [Comet Lake (UHD 6XX)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen9) | 10.15.4 | Actual | N/A |
| [Ice Lake (Gx)](https://en.wikipedia.org/wiki/List_of_Intel_graphics_processing_units#Gen11) | 10.15.4 | Actual | Requiere de `-igfxcdc` y `-igfxdvmt` en tus boot-args |

:::

::: details Tabla de soporte de GPUs AMD

*Nota: Los links informativos están en inglés*

| Generación de GPU | Soporte inicial | Última versión soportada | Notas |
| :--- | :--- | :--- | :--- |
| [X800](https://en.wikipedia.org/wiki/Radeon_X800_series) | 10.3.x | 10.7.5 | Requiere de un kernel 32bit |
| [X1000](https://en.wikipedia.org/wiki/Radeon_X1000_series) | 10.4.x | 10.7.5 | N/A |
| [Terascale](https://en.wikipedia.org/wiki/TeraScale_(microarchitecture)) | 10.4.x | 10.13.6 | N/A |
| [Terascale 2/3](https://en.wikipedia.org/wiki/TeraScale_(microarchitecture)) | 10.6.x | 10.13.6 | N/A |
| [GCN 1](https://en.wikipedia.org/wiki/Graphics_Core_Next) | 10.8.3 | Actual | N/A |
| [GCN 2/3](https://en.wikipedia.org/wiki/Graphics_Core_Next) | 10.10.x | Actual | N/A |
| [Polaris 10](https://en.wikipedia.org/wiki/Radeon_RX_400_series), [20](https://en.wikipedia.org/wiki/Radeon_RX_500_series) | 10.12.1 | Actual | N/A |
| [Vega 10](https://en.wikipedia.org/wiki/Radeon_RX_Vega_series) | 10.12.6 | Actual | N/A |
| [Vega 20](https://en.wikipedia.org/wiki/Radeon_RX_Vega_series) | 10.14.5 | Actual | N/A |
| [Navi 10](https://en.wikipedia.org/wiki/Radeon_RX_5000_series) | 10.15.1 | Actual | Requiere `agdpmod=pikera` en los boot-args |
| [Navi 20](https://en.wikipedia.org/wiki/Radeon_RX_6000_series) | <span style="color:red"> N/A </span> | <span style="color:red"> N/A </span> | <span style="color:red"> Los drivers actuales no funcionan </span> |

:::

::: details Tabla de soporte de GPUs Nvidia

*Nota: Los links informativos están en inglés*

| Generación de GPU | Soporte Inicial | Última versión soportada | Notas |
| :--- | :--- | :--- | :--- |
| [GeForce 6](https://en.wikipedia.org/wiki/GeForce_6_series) | 10.2.x | 10.7.5 | Requiere de un kernel 32bit |
| [GeForce 7](https://en.wikipedia.org/wiki/GeForce_7_series) | 10.4.x | 10.7.5 | N/A |
| [Tesla](https://en.wikipedia.org/wiki/Tesla_(microarchitecture)) | 10.4.x | 10.13.6 | N/A |
| [Tesla V2](https://en.wikipedia.org/wiki/Tesla_(microarchitecture)#Tesla_2.0) | 10.5.x | 10.13.6 | N/A |
| [Fermi](https://en.wikipedia.org/wiki/Fermi_(microarchitecture)) | 10.7.x | 10.13.6 | N/A |
| [Kepler](https://en.wikipedia.org/wiki/Kepler_(microarchitecture)) | 10.7.x | Actual | N/A |
| [Kepler V2](https://en.wikipedia.org/wiki/Kepler_(microarchitecture)) | 10.8.x | Actual | N/A |
| [Maxwell](https://en.wikipedia.org/wiki/Maxwell_(microarchitecture)) | 10.10.x | 10.13.6 | Requiere de los [Web Drivers](https://www.nvidia.com/download/driverResults.aspx/159341/)|
| [Pascal](https://en.wikipedia.org/wiki/Pascal_(microarchitecture)) | 10.12.4 | 10.13.6 | Requiere de los [Web Drivers](https://www.nvidia.com/download/driverResults.aspx/159341/) |
| [Turing](https://en.wikipedia.org/wiki/Turing_(microarchitecture)) | N/A | N/A | No hay drivers disponibles |
| [Ampere](https://en.wikipedia.org/wiki/Ampere_(microarchitecture)) | N/A | N/A | No hay drivers disponibles |

:::

## Soporte de placas madre

En su mayor parte, todas las placas madre son compatibles siempre que la CPU lo sea tambíen. Previamente, las placas B550 tenía problemas:

* ~~[La placas base AMD B550](https://www.amd.com/es/chipsets/b550)~~

Sin embargo, gracias a desarrollos recientes, las placas B550 son booteables con la adición de SSDT-CPUR. Daremos más información en ambas recolectando archivos y la página de config.plist de Zen.

## Compatibilidad de almacenamiento

En su mayor parte, todas las unidades basadas en SATA son compatibles y la mayoría de las unidades NVMe también. Sin embargo, tenemos unas pocas excepciones:

* **SSDs NVMe Samsung PM981, PM991 y Micron 2200S**
  * Estos SSD no son compatibles desde el primer momento (lo que causa un "kernel panic") y, por lo tanto, requieren de [NVMeFix.kext](https://github.com/acidanthera/NVMeFix/releases) para corregir este problema. Ten en cuenta que estas unidades pueden causar problemas de arranque incluso con NVMeFix.kext.
  * Otro caso similar fue con la SSD NVMe 970 EVO Plus de Samsung, pero esto fue corregido con una actualización de firmware. Puedes obtenerla via Windows usando la herramienta Samsung Magician o con una ISO booteable [aquí](https://www.samsung.com/semiconductor/minisite/ssd/download/tools/).
  * También para tener en cuenta, las computadoras portátiles que usan [Intel Optane](https://www.intel.com/content/www/us/en/architecture-and-technology/optane-memory.html) o [Micron 3D XPoint](https://www.micron.com/products/advanced-solutions/3d-xpoint-technology) para la aceleración de discos duros, no son compatibles con macOS. Sin embargo algunos usuarios han reportado éxito en Catalina incluso con soporte de lectura y escritura, pero recomendamos encarecidamente quitar la unidad para evitar posibles problemas de arranque.

  * **Intel 600p**
   * Por más que es posible arrancar desde estos, por favor ten en cuenta que este modelo puede causar muchos problemas. [Any fix for Intel 600p NVMe Drive? #1286](https://github.com/acidanthera/bugtracker/issues/1286)

## Conexión a internet cableada

Prácticamente todos los adaptadores de red con cable tienen algún tipo de soporte en macOS, ya sea por los controladores incorporados o por kexts hechos por la comunidad. Las principales excepciones son:

* Ethernet i225 2.5GBe de Intel
  * Encontrado en placas base Comet Lake de gama alta de escritorio.
  * Son postibles soluciones alternativas: [Fuente](https://www.hackintosh-forum.de/forum/thread/48568-i9-10900k-gigabyte-z490-vision-d-er-läuft/?postID=606059#post606059) y [Ejemplo](/config.plist/comet-lake#add-2)
* NICs de servidores de Intel
  * Hay soluciones alternativas para los chipsets [X520 y X540](https://www.tonymacx86.com/threads/how-to-build-your-own-imac-pro-successful-build-extended-guide.229353/)
* NICs de servidores Mellanox y Qlogic

## Conexión a internet inalámbrica

La mayoría de las tarjetas WiFi que vienen con las laptops no son compatibles, ya que generalmente son Intel/Qualcomm. Si tienes suerte, es posible que tengas una tarjeta Atheros compatible, pero son compatibles hasta High Sierra.

La mejor opción es tener una tarjeta Broadcom compatible; consulta la [Guía de compras de WiFi](https://dortania.github.io/Wireless-Buyers-Guide/) para obtener recomendaciones.

## Otros

* **Sensores de huellas**
  * Actualmente no hay forma de emular el sensor Touch ID, por lo que los sensores de huellas digitales no funcionarán.
* **Reconocimiento Facial con Windows Hello**
  * Algunas laptops vienen con WHFR que está conectado a I2C (y se usa a través de tu iGPU), esas no funcionarán.
  * Otras vienen con WHFR que está conectado por USB, y si tienes suerte, puedes obtener la funcionalidad de la cámara, pero nada más.
* **Tecnología Intel Smart Sound**
  * Las laptops con Intel SST no tendrán nada conectado a través de ellas (generalmente micrófono interno) funcionando, ya que no es compatible. Puedes consultar con el Administrador de dispositivos en Windows.
* **Combo de headphone jack**
  * Es posible que algunas laptops con un headphone jack combinado para auriculares no reciban la entrada de audio a través de ellas y tengan que usar el micrófono incorporado o un dispositivo externo de entrada de audio a través de USB.
* **Puertos Thunderbolt USB-C**
  * En el caso de los hackintosh, la compatibilidad con Thunderbolt todavía es dudosa en macOS, aún más con los controladores Alpine Ridge, que son los que tienen la mayoría de las laptops actuales. Han habido intentos de mantener el controlador encendido, lo que permite que funcionen Thunderbolt y USB-C, pero esto trae como consecuencia "kernel panics" y/o la pérdida de USB-C después de suspender la laptop. Si deseas utilizar el lado USB-C del puerto y poder suspender tu hack, debes enchufarlo en el arranque y mantenerlo enchufado.
  * Nota: Esto no se aplica solo a los puertos USB-C, solo a los puertos combinados Thunderbolt 3 y USB-C.
  * Deshabilitar Thunderbolt en la BIOS también resolverá esto.

* Nota: El wifi de Intel está soportado pero no de forma oficial (es un driver de terceros) en macOS. Mira la [Guía de compradores de Wifi](https://dortania.github.io/Wireless-Buyers-Guide/) para obtener más información.
