# Escogiendo el SMBIOS correcto

A la hora de elegir el SMBIOS correcto para tu PC, tienes que entender que es más que solamente encontrar una SMBIOS con una CPU parecida. Muchas cosas pueden afectar cómo funciona tu computadora, ya que el SMBIOS determina la administración de energía, perfiles de GPU, mapa de USBs y mucho más.

Cosas principales a tener en cuenta a la hora de seleccionar tu SMBIOS:

* Tipo de CPU

* Específicamente móvil vs escritorio vs CPUs de servidores, ya que esto puede afectar en gran medida el sueño y la estabilidad general del sistema
   * Esto también determina si puede usar el XCPM de Apple y qué perfiles obtiene
     * Estos 2 se resuelven principalmente con CPUFriend: [Arreglando la administración de energía](https://inyextciones.github.io/OpenCore-Post-Install/universal/pm.html)
   * Ten en cuenta que si tienes una CPU AMD no debes preocuparte por esto
   
* Tipo de GPU
  
* Muchas cosas son afectadas aquí, como la administración de energía de la GPU (AGPM), soporte de salida de imagen (AGDP), suspensión (AGDC) y mucho más.
    * Esto es relevante especialmente cuando miramos a el SMBIOS de la [Mac Mini](#mac-mini), que sólo usa hardware móvil, el cual no encaja muy bien con hardware de escritorio doesn't match well with desktop hardware. Esta es la razón por la que no recomendamos el uso de estas a menos que se utilizen para la [serie NUC de Intel](https://www.intel.es/content/www/es/es/products/boards-kits/nuc.html) y similares que están basados en hardware móvil.
    * Las laptops también deben prestar atención aquí, debido a que Apple siempre asume que hay una GPU dedicada para el SMBIOS, por lo que todas las salidas de imagen serán enrutadas por esta. Lo anterior puede convertirse en un problema cuando una laptop Optimus tiene sus monitores externos conectados a el iGPU, y esto causa una pantalla negra, por lo que requerirá más parches.
  * Las CPUs sin un iGPU deben prestar mucha atención aquí, ya que funciones como Quicklook y similares estarán rotas si el SMBIOS espera un iGPU (es decir, todas las SMBIOS de iMacs).
    * Para estas situaciones, presta atención a las SMBIOS iMac Pro y Mac Pro
  * El DRM también tiene que ver aquí, aunque ha sido resuelto en su mayor parte aquí: [Arreglando el DRM](https://inyextciones.github.io/OpenCore-Post-Install/universal/drm.html)
  
* Soporte de SO
  * Principalmente relevante para hardware antiguo, ya que macOS puede continuar soportando la CPU; pero por otro lado no soporta el SMBIOS de esa era.
    * Las CPUs Arrandale son un muy buen ejemplo, ya que siguen teniendo soporte del SO hasta en macOS 11, Big Sur. Sin embargo, sus iGPUs son soportadas hasta 10.13.6.
* Dispositivos USB
  * Ciertos SMBIOS tendrán su propio mapa de USBs, el cual podría adherirse a nuestro hardware causando problemas con los USBs.
    * Dirígete aquí para más información: [USB Mapping](https://inyextciones.github.io/OpenCore-Post-Install/usb/)
  * Un dato a tener en cuenta es que los SMBIOS Skylake y superior también requerirán de un [dispositivo USBX](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-EC-USBX.dsl#L54L79) para arreglar la corriente de energía de los USBs
    * Dirígete aquí para más información: [Arreglar la alimentación de USBs](https://inyextciones.github.io/OpenCore-Post-Install/usb/misc/power.html)

::: details SMBIOS que soportan XCPM

| SMBIOS |
| :--- |
| MacBook8,1+ |
| MacBookAir6,x+ |
| MacBookPro11,x+ |
| Macmini7,1+ |
| iMac14,x+ |
| iMacPro1,1 |
| MacPro6,1+ |

:::

## Cómo decidir

Generalmente nuestras recomendaciones para SMBIOS son las siguientes:

1. Encuentra la generación de CPU correcta y su ["tier"](#nota-especial) más cercana posible.
2. Luego júntala con una SMBIOS con de sólo iGPU o GPU dedicada dependiendo de tu sistema.
3. Finalmente decide cosas más específicas (como CPU o GPU exacta)

También tenemos algunas notas especiales con las SMBIOS:

* iMacPro1,1 y MacPro7,1 son las únicas 2 SMBIOS que permitirán que la GPU dedicada maneje toda la carga de trabajo, incluido el renderizado en segundo plano y otras tareas que manejaría la iGPU
   * Solo recomendamos este SMBIOS si lo necesitas; sin embargo, es probable que debas corregir la administración de energía, ya que la suspensión puede interrumpirse si su hardware no es de este tipo (HEDT/Server/AMD): [Arreglar la administración de energía](https://inyextciones.github.io/OpenCore-Post-Install/universal/pm.html)
   * Ten en cuenta que esto requiere de una GPU Polaris, Vega o Navi para funcionar correctamente.
* iMac20,2 es un SMBIOS personalizado diseñado solo para la CPU i9-10910 personalizada de Apple, por lo que, a menos que tengas un i9-10900K, recomendamos usar iMac20,1
* Debe evitarse el SMBIOS MacMini a menos que esté ejecutando hardware móvil sin una pantalla incorporada
   * Los NUCs de Intel son el hardware ideal para este SMBIOS
* Las CPUs sin un iGPU **DEBEN** prestar mucha atención al seleccionar el SMBIOS, ya que Apple siempre asume que el iGPU está presente en los SMBIOS de iMac, por lo que deberás encontrar un SMBIOS sin este requisito, como iMac Pro o Mac Pro.
   * Esto también se aplica a las CPUs AMD

## Lista de SMBIOS de macOS

Esta es una lista completa de todos los SMBIOS que Apple soportó en macOS con información extra, como tipos de CPU y GPU.

* [MacBook](#macbook)
* [MacBook Air](#macbook-air)
* [MacBook Pro](#macbook-pro)
* [Mac Mini](#mac-mini)
* [iMac](#imac)
* [iMac Pro](#imac-pro)
* [Mac Pro](#mac-pro)
* [Xserve](#xserve)
* [Otras SMBIOS](#otras-smbios)

Información sacada de ambas [EveryMac](https://everymac.com) y [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)

### Nota especial

* La letra al lado de cada familia de CPUs es el "tier" de CPUs, mira la tabla debajo para tener más información:

| Letra | Tipo |
| :--- | :--- |
| Y | Móvil (Gama baja) |
| U, M | Móvil (Gama media) |
| H, QM, HQ | Móvil (Gama alta) |
| S | Escritorio |
| EP, SP, W, X | HEDT/Servidor |

### MacBook

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| MacBook1,1     | Yonah(M)        | GMA 950                       | Mac-F4208CC8       | 10.4.6 (8I2025) | 10.6.8 |
| MacBook2,1     | Merom(M)        | GMA 950                       | Mac-F4208CA9   | 10.4.8 (8N1108) | 10.7.5 |
| MacBook3,1     | Merom(M)        | GMA X3100                     | Mac-F22788C8   | 10.5 (9A3111) | 10.7.5 |
| MacBook4,1     | Penryn(M)       | GMA X3100                     | Mac-F22788A9   | 10.5.2 (9C2015) | 10.7.5 |
| MacBook5,1     | Penryn(M)       | GeForce 9400M                 | Mac-F42D89C8   | 10.5.5 (9F2114) | 10.11.6 |
| MacBook5,2     | Penryn(M)       | GeForce 9400M                 | Mac-F22788AA   | 10.5.6 (9G2110) | 10.11.6 |
| MacBook6,1     | Penryn(M)       | GeForce 9400M                 | Mac-F22C8AC8   | 10.6.1 (10A2047) | 10.13.6 |
| MacBook7,1     | Penryn(M)       | GeForce 320M                  | Mac-F22C89C8   | 10.6.3 (10D2162) | 10.13.6 |
| MacBook8,1     | Broadwell(Y)    | HD 5300                       | Mac-BE0E8AC46FE800CC | 10.10.2 (14C2061) | Actual |
| MacBook9,1     | Skylake(Y)      | HD 515                        | Mac-9AE82516C7C6B903 | 10.11.4 (15E2066) | Actual |
| MacBook10,1    | Kaby Lake(Y)    | HD 615                        | Mac-EE2EBD4B90B839A8 | 10.12.5 (16F207) | Actual |

### MacBook Air

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| MacBookAir1,1  | Merom(M)        | GMA X3100     (11")                | Mac-F42C8CC8 | 10.5.1 (9B2324) | 10.7.5 |
| MacBookAir2,1  | Penryn(M)       | GeForce 9400M (13")                | Mac-F42D88C8 | 10.5.5 | 10.11.6 |
| MacBookAir3,1  | Penryn(M)       | GeForce 320M  (11")                | Mac-942452F5819B1C1B | 10.6.4 (10F3061) | 10.13.6 |
| MacBookAir3,2  | Penryn(M)       | GeForce 320M  (13")                | Mac-942C5DF58193131B | 10.6.4 (10F3061) | 10.13.6 |
| MacBookAir4,1  | Sandy Bridge(M) | HD 3000 (11")                      | Mac-C08A6BB70A942AC2 | 10.7 (11A2063) | 10.13.6 |
| MacBookAir4,2  | Sandy Bridge(M) | HD 3000 (13")                      | Mac-742912EFDBEE19B3 | 10.7 (11A2063) | 10.13.6 |
| MacBookAir5,1  | Ivy Bridge(U)   | HD 4000 (11")                      | Mac-66F35F19FE2A0D05 | 10.7.4 (11E2520) | 10.15.6 |
| MacBookAir5,2  | Ivy Bridge(U)   | HD 4000 (13")                      | Mac-2E6FAB96566FE58C | 10.8.2 (12C2034) | 10.15.6 |
| MacBookAir6,1  | Haswell(U)      | HD 5000 (11")                      | Mac-35C1E88140C3E6CF | 10.8.4 (12E3067) | Actual |
| MacBookAir6,2  | Haswell(U)      | HD 5000 (13")                      | Mac-7DF21CB3ED6977E5 | 10.8.4 (12E3067) | Actual |
| MacBookAir7,1  | Broadwell(U)    | HD 6000 (11")                      | Mac-9F18E312C5C2BF0B | 10.10.2 (14C2507) | Actual |
| MacBookAir7,2  | Broadwell(U)    | HD 6000 (13")                      | Mac-937CB26E2E02BB01 | 10.10.2 (14C2507) | Actual |
| MacBookAir8,1  | Amber Lake(Y)   | UHD 617 (13")                      | Mac-827FAC58A8FDFA22 | 10.14.1 (18B2084) | Actual |
| MacBookAir8,1  | Amber Lake(Y)   | UHD 617 (13")                      | Mac-226CB3C6A851A671 | 10.14.5 (18F2058) | Actual |
| MacBookAir9,1  | Ice Lake(Y)     | Iris Plus G4/G7 (13")              | Mac-0CFF9C7C2B63DF8D | 10.15.4 (19E287) | Actual |

### MacBook Pro

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| MacBookPro1,1  | Yonah(M)        | Radeon X1600 (15")                 | Mac-F425BEC8 | 10.4.5 (8G1453) | 10.6.8 |
| MacBookPro1,2  | Yonah(M)        | Radeon X1600 (17")                 | Mac-F42DBEC8 | 10.4.6 (8I2032) | 10.6.8 |
| MacBookPro2,1  | Merom(M)        | Radeon X1600 (15")                 | Mac-F42189C8 | 10.4.8 (8N1051) | 10.7.5 |
| MacBookPro2,2  | Merom(M)        | Radeon X1600 (17")                 | Mac-F42187C8 | 10.4.8 (8N1037) | 10.7.5 |
| MacBookPro3,1  | Merom(M)        | GeForce 8600M GT (15/17")          | Mac-F4238BC8 | 10.4.9 (8Q1058) | 10.11.6 |
| MacBookPro4,1  | Penryn(M)       | GeForce 8600MG GT (17")            | Mac-F42C89C8 | 10.5.2 (9C2018) | 10.11.6 |
| MacBookPro5,1  | Penryn(M)       | GeForce 9400M/9600M GT (15")       | Mac-F42D86C8 | 10.5.5 (9F2114) | 10.11.6 |
| MacBookPro5,2  | Penryn(M)       | GeForce 9400M/9600M GT (17")       | Mac-F2268EC8 | 10.5.6 (9G2141) | 10.11.6 |
| MacBookPro5,3  | Penryn(M)       | GeForce 9400M/9600M GT (15")       | Mac-F22587C8 | 10.5.7 (9J3050) | 10.11.6 |
| MacBookPro5,4  | Penryn(M)       | GeForce 9400M/9600M GT (15")       | Mac-F22587A1 | 10.5.7 (9J3050) | 10.11.6 |
| MacBookPro5,5  | Penryn(M)       | GeForce 9400M/9600M GT (13")       | Mac-F2268AC8 | 10.5.7 (9J3050) | 10.11.6 |
| MacBookPro6,1  | Arrandale(M)    | HD Graphics/GeForce GT 330M (17")  | Mac-F22589C8 | 10.6.3 (10D2063a) | 10.13.6 |
| MacBookPro6,2  | Arrandale(M)    | HD Graphics/GeForce GT 330M (15")  | Mac-F22586C8 | 10.6.3 (10D2094) | 10.13.6 |
| MacBookPro7,1  | Penryn(M)       | GeForce 320M (13")                 | Mac-F222BEC8 | 10.6.3 (10D2125) | 10.13.6 |
| MacBookPro8,1  | Sandy Bridge(M) | HD 3000 (13")                      | Mac-94245B3640C91C81 | 10.6.6 (10J3210) | 10.13.6 |
| MacBookPro8,2  | Sandy Bridge(QM)| HD 3000/Radeon HD 6490M (15")      | Mac-94245A3940C91C80 | 10.6.6 (10J3210) | 10.13.6 |
| MacBookPro8,3  | Sandy Bridge(QM)| HD 3000/Radeon HD 6750M (17")      | Mac-942459F5819B171B | 10.6.6 (10J3210) | 10.13.6 |
| MacBookPro9,1  | Ivy Bridge(QM)  | HD 4000/GeForce GT 650M (15")      | Mac-4B7AC7E43945597E | 10.7.3 (11D2097) | 10.15.7 |
| MacBookPro9,2  | Ivy Bridge(M)   | HD 4000 (13")                      | Mac-6F01561E16C75D06 | 10.7.3 (11D2515) | 10.15.7 |
| MacBookPro10,1 | Ivy Bridge(QM)  | HD 4000/GeForce GT 650M (15")      | Mac-C3EC7CD22292981F | 10.7.4 (11E2068) | 10.15.7 |
| MacBookPro10,2 | Ivy Bridge(M)   | HD 4000 (13")                      | Mac-AFD8A9D944EA4843 | 10.8.2 (12C2034) | 10.15.7 |
| MacBookPro11,1 | Haswell(U)      | Iris 5100 (13")                    | Mac-189A3D4F975D5FFC | 10.9 (13A2093) | Actual |
| MacBookPro11,2 | Haswell(HQ)     | Iris Pro 5200 (15")                | Mac-3CBD00234E554E41 | 10.9 (13A3017) | Actual |
| MacBookPro11,3 | Haswell(HQ)     | Iris Pro 5200/GeForce GT 750M (15")| Mac-2BD1B31983FE1663 | 10.9 (13A3017) | Actual |
| MacBookPro11,4 | Haswell(HQ)     | Iris Pro 5200 (15")                | Mac-06F11FD93F0323C5 | 10.10.3 (14D2134) | Actual |
| MacBookPro11,5 | Haswell(HQ)     | Iris Pro 5200/Radeon R9 M370X (15")| Mac-06F11F11946D27C5 | 10.10.3 (14D2134) | Actual |
| MacBookPro12,1 | Broadwell(U)    | Iris 6100 (13")                    | Mac-E43C1C25D4880AD6 | 10.10.2 (14C2507) | Actual |
| MacBookPro13,1 | Skylake(U)      | Iris 540 (13")                     | Mac-473D31EABEB93F9B | 10.12 (16A2323a) | Actual |
| MacBookPro13,2 | Skylake(U)      | Iris 550 (13")                     | Mac-66E35819EE2D0D05 | 10.12.1 (16B2657) | Actual |
| MacBookPro13,3 | Skylake(H)      | HD 530/Radeon Pro 450 (15")        | Mac-A5C67F76ED83108C | 10.12.1 (16B2659) | Actual |
| MacBookPro14,1 | Kaby Lake(U)    | Iris Plus 640 (13")                | Mac-B4831CEBD52A0C4C | 10.12.5 (16F2073) | Actual |
| MacBookPro14,2 | Kaby Lake(U)    | Iris Plus 650 (13")                | Mac-CAD6701F7CEA0921 | 10.12.5 (16F2073) | Actual |
| MacBookPro14,3 | Kaby Lake(H)    | HD 630/Radeon Pro 555 (15")        | Mac-551B86E5744E2388 | 10.12.5 (16F2073) | Actual |
| MacBookPro15,1 | Coffee Lake(H)  | UHD 630/Radeon Pro 555X (15")      | Mac-937A206F2EE63C01 | 10.13.6 (17G2112) | Actual |
| MacBookPro15,2 | Coffee Lake(U)  | Iris Plus 655 (13")                | Mac-827FB448E656EC26 | 10.13.6 (17G2112) | Actual |
| MacBookPro15,3 | Coffee Lake(H)  | UHD 630/Radeon Pro Vega 16 (15")   | Mac-1E7E29AD0135F9BC | 10.14.1 (18B3094) | Actual |
| MacBookPro15,4 | Coffee Lake(U)  | Iris Plus 645 (13")                | Mac-53FDB3D8DB8CA971 | 10.14.5 (18F2058) | Actual |
| MacBookPro16,1 | Coffee Lake(H)  | UHD 630/Radeon Pro 5300 (16")      | Mac-E1008331FDC96864 | 10.15.1 (19B2093) | Actual |
| MacBookPro16,2 | Ice Lake(U)     | Iris Plus G4/G7 (13")              | Mac-5F9802EFE386AA28 | 10.15.4 (19E2269) | Actual |
| MacBookPro16,3 | Coffee Lake(U)  | Iris Plus 645 (13")                | Mac-E7203C0F68AA0004 | 10.15.4 (19E2269) | Actual |
| MacBookPro16,4 | Coffee Lake(H)  | UHD 630/Radeon Pro 5600M (16")     | Mac-A61BADE1FDAD7B05 | 10.15.1 (19B2093) | Actual |

### Mac Mini

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Macmini1,1     | Yonah(M)        | GMA 950                       | Mac-F4208EC8           | 10.4.5 (8H1619) | 10.6.8 |
| Macmini2,1     | Merom(M)        | GMA 950                       | Mac-F4208EAA           | 10.4.10 (8R3014) | 10.7.5 |
| Macmini3,1     | Penryn(M)       | GeForce 9400M                 | Mac-F22C86C8           | 10.5.6 (9G2030) | 10.11.6 |
| Macmini4,1     | Penryn(M)       | GeForce 320M                  | Mac-F2208EC8           | 10.6.4 (10F2025) | 10.13.6 |
| Macmini5,1     | Sandy Bridge(M) | HD 3000                       | Mac-8ED6AF5B48C039E1   | 10.7 (11A2061) | 10.13.6 |
| Macmini5,2     | Sandy Bridge(M) | Radeon HD 6630M               | Mac-4BC72D62AD45599E   | 10.7 (11A2061) | 10.13.6 |
| Macmini5,3     | Sandy Bridge(QM)| HD 3000                       | Mac-7BA5B2794B2CDB12   | 10.7 (11A2061) | 10.13.6 |
| Macmini6,1     | Ivy Bridge(M)   | HD 4000                       | Mac-031AEE4D24BFF0B1   | 10.8.1 (12B2080) | 10.15.7 |
| Macmini6,2     | Ivy Bridge(QM)  | HD 4000                       | Mac-F65AE981FFA204ED   | 10.8.1 (12B2080) | 10.15.7 |
| Macmini7,1     | Haswell(U)      | HD 5000 or Iris 5100          | Mac-35C5E08120C7EEAF   | 10.10 (14A389) | Actual |
| Macmini8,1     | Coffee Lake(H)  | UHD 630                       | Mac-7BA5B2DFE22DDD8C   | 10.14 (18A2063) | Actual |

### iMac

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| iMac4,1        | Yonah(M)        | Radeon X1600                  | Mac-F42786C8   | 10.4.4 (8G1165)      | 10.6.8 |
| iMac4,2        | Yonah(M)        | GMA 950                       | Mac-F4218EC8   | 10.4.7 (8I2057)      | 10.6.8 |
| iMac5,1        | Merom(M)        | Radeon X1600                  | Mac-F4228EC8   | 10.4.7 (8K1106)      | 10.7.5 |
| iMac5,2        | Merom(M)        | GMA 950                       | Mac-F4218EC8   | 10.4.7 (8K1106)      | 10.7.5 |
| iMac6,1        | Merom(M)        | GeForce 7300GT                | Mac-F4218FC8   | 10.4.7 (8K1123)      | 10.7.5 |
| iMac7,1        | Merom(M)        | Radeon HD 2400 XT             | Mac-F42386C8   | 10.4.10 (8R4031)      | 10.11.6 |
| iMac8,1        | Penryn(M)       | Radeon HD 2400 XT             | Mac-F227BEC8   | 10.5.2 (9C2028)      | 10.11.6 |
| iMac9,1        | Penryn(M)       | GeForce 9400M                 | Mac-F2218FA9   | 10.5.6 (9G2030)      | 10.11.6 |
| iMac10,1       | Wolfdale(S)     | GeForce 9400M                 | Mac-F221DCC8   | 10.6.1 (10A2155)      | 10.13.6 |
| iMac10,1       | Wolfdale(S)     | Radeon HD 4670                | Mac-F2268CC8   | 10.6.1 (10A2155)      | 10.13.6 |
| iMac11,1       | Lynnfield(S)    | Radeon HD 4850                | Mac-F2268DAE   | 10.6.2 (10C2234)      | 10.13.6 |
| iMac11,2       | Clarkdale(S)    | Radeon HD 4670                | Mac-F2238AC8   | 10.6.3 (10D2322a)      | 10.13.6 |
| iMac11,3       | Clarkdale(S)    | Radeon HD 5670                | Mac-F2238BAE   | 10.6.3 (10D2322a)      | 10.13.6 |
| iMac12,1       | Sandy Bridge(S) | Radeon HD 6750M               | Mac-942B5BF58194151B | 10.6.6 (10J4026)      | 10.13.x |
| iMac12,2       | Sandy Bridge(S) | Radeon HD 6770M               | Mac-942B59F58194171B | 10.6.6 (10J4026)      | 10.13.x |
| iMac13,1       | Ivy Bridge(S)   | GeForce GT 640M               | Mac-00BE6ED71E35EB86 | 10.8.2 (12C3104)      | 10.15.7 |
| iMac13,1       | Ivy Bridge(S)   | HD 4000                       | Mac-00BE6ED71E35EB86 | 10.8.2 (12C3104)      | 10.15.7 |
| iMac13,2       | Ivy Bridge(S)   | GeForce GTX 660M              | Mac-FC02E91DDD3FA6A4 | 10.8.2 (12C2037)      | 10.15.7 |
| iMac13,3       | Ivy Bridge(S)   | HD 4000                       | Mac-7DF2A3B5E5D671ED | 10.8.2 (12C2037)      | 10.15.7 |
| iMac14,1       | Haswell(S)      | Iris Pro 5200                 | Mac-031B6874CF7F642A | 10.8.4 (12E4022)      | 10.15.7 |
| iMac14,2       | Haswell(S)      | GeForce GT 750M               | Mac-27ADBB7B4CEE8E61 | 10.8.4 (12E4022)      | 10.15.7 |
| iMac14,3       | Haswell(S)      | GeForce GT 755M               | Mac-77EB7D7DAF985301 | 10.8.4 (12E4022)      | 10.15.7 |
| iMac14,4       | Haswell(U)      | HD 5000                       | Mac-81E3E92DD6088272 | 10.9.3 (13D2061)      | Actual |
| iMac15,1       | Haswell(S)      | Radeon R9 M290X               | Mac-42FD25EABCABB274 | 10.10 (14A389)      | Actual |
| iMac16,1       | Broadwell(U)    | HD 6000 or Iris Pro 6200      | Mac-A369DDC4E67F1C45 | 10.11 (15A2301)      | Actual |
| iMac16,2       | Broadwell(S)    | Iris Pro 6200                 | Mac-FFE5EF870D7BA81A | 10.11 (15A2301)      | Actual |
| iMac17,1       | Skylake(S)      | Radeon R9 M380                | Mac-DB15BD556843C820, Mac-65CE76090165799A, Mac_B809C3757DA9BB8D | 10.11 (15A4310)      | Actual |
| iMac18,1       | Kaby Lake(U)    | Iris Plus 640                 | Mac-4B682C642B45593E | 10.12.4 (16E2193)      | Actual |
| iMac18,2       | Kaby Lake(S)    | Radeon Pro 555                | Mac-77F17D7DA9285301 | 10.12.4 (16F2073)      | Actual |
| iMac18,3       | Kaby Lake(S)    | Radeon Pro 570                | Mac-BE088AF8C5EB4FA2 | 10.12.4 (16F2073)      | Actual |
| iMac19,1       | Coffee Lake(S)  | Radeon Pro 570X               | Mac-AA95B1DDAB278B95 | 10.14.4 (18E226)      | Actual |
| iMac19,2       | Coffee Lake(S)  | Radeon Pro 555X               | Mac-63001698E7A34814 | 10.14.4 (18E226)      | Actual |
| iMac20,1       | Comet Lake(S)   | Radeon Pro 5300               | Mac-CFF7D910A743CAAF | 10.15.6 (19G2005)      | Actual |
| iMac20,2       | Comet Lake(S)   | Radeon Pro 5300               | Mac-AF89B6D9451A490B | 10.15.6 (19G2005)      | Actual |

### iMac Pro

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| iMacPro1,1     | Skylake-W    | Vega 56                       | Mac-7BA5B2D9E42DDD94       | 10.13.2 (17C2111) | Actual |

### Mac Pro

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| MacPro1,1      | Woodcrest     | GeForce 7300 GT               | Mac-F4208DC8           | 10.4.7 (8K1079) | 10.7.5 |
| MacPro2,1      | Clovertown    | GeForce 7300 GT               | Mac-F4208DA9           | 10.4.9 (8P4037) | 10.7.5 |
| MacPro3,1      | Harpertown    | Radeon HD 2600 XT             | Mac-F42C88C8           | 10.5.1 (9B2117) | 10.11.6 |
| MacPro4,1      | Nehalem       | GeForce GT 120                | Mac-F221BEC8           | 10.5.6 (9G3553) | 10.11.6 |
| MacPro5,1      | Nehalem       | Radeon HD 5770                | Mac-F221BEC8           | 10.6.4 (10F2521) | 10.14.6 |
| MacPro5,1      | Westmere EP   | Radeon HD 5770                | Mac-F221BEC8           | 10.6.4 (10F2521) | 10.14.6 |
| MacPro6,1      | Ivy Bridge EP | FirePro D300                  | Mac-F60DEB81FF30ACF6   | 10.9.1 (13B4116) | Actual |
| MacPro7,1      | Cascade Lake-W| Radeon Pro 580X               | Mac-27AD2F918AE68F61   | 10.15.0 (19A583) | Actual |

### Xserve

| SMBIOS | Familia de CPU | GPU | board-id | Soporte Inicial | Última versión soportada |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Xserve1,1      | Woodcrest    | Radeon X1300                  | Mac-F4208AC8           | Server 10.4.8 (8N1215) | Server 10.7.5 |
| Xserve2,1      | Harpertown   | Radeon X1300                  | Mac-F42289C8           | Server 10.5 (9B2117) | Server 10.7.5 |
| Xserve3,1      | Nehalem EP   | GeForce GT 120                | Mac-F223BEC8           | Server 10.5.6 | 10.11.6 |

### Otras SMBIOS

Todos los modelos mencionados abajo no están soportados por OpenCore pero son documentados aquí para facilitar su referencia

::: details Notas de Apple Silicon

Información extra sobre las CPUs de Apple:

* No dependen de ACPI o UEFI
* No incluyen DeviceProperties en su firmware
* Usan iPad8,6 para aplicaciones de iOS/iPadOS

:::


#### Kit de transición de desarrolladores (Developer Transition Kit)


| SMBIOS | Año | Familia de CPU | Identificador de enclave seguro | Soporte inicial |
| :--- | :--- | :--- | :--- | :--- |
| ADP2,1 | Mediados de 2005 | Intel Prescott | N/A | 10.4.1 (8B1025) |
| ADP3,2 | Mediados de 2020 | Apple A12Z | J273 | 11.0.0 (20A5299w) |

#### Mac Mini - Apple Silicon

| SMBIOS | Familia de CPU | Identificador de enclave seguro | Soporte inicial |
| :--- | :--- | :--- | :--- |
| MacMini9,1 | Apple M1 | J274 | 11.0.0 (20A2411) |

#### MacBook Air - Apple Silicon

| SMBIOS | Familia de CPU | Identificador de enclave seguro | Soporte inicial |
| :--- | :--- | :--- | :--- |
| MacBookAir10,1 | Apple M1 | J313 | 11.0.0 (20A2411) |

#### MacBook Pro - Apple Silicon

| SMBIOS | Familia de CPU | Identificador de enclave seguro | Soporte inicial |
| :--- | :--- | :--- | :--- |
| MacBookPro17,1 | Apple M1 | J293 | 11.0.0 (20A2411) |
