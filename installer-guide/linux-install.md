# Creando el instalador en Linux

* Versión soportada: 0.6.8

Si bien no necesitas una nueva instalación de macOS para usar OpenCore, algunos usuarios prefieren tener una nueva instalación con sus actualizaciones del gestor de arranque.

Para comenzar necesitarás lo siguiente:

* Un USB de 4GB
* [macrecovery.py](https://github.com/acidanthera/OpenCorePkg/releases)

## Descargando macOS

Ahora, para comenzar utiliza `cd` para llegar a la [carpeta de macrecovery](https://github.com/acidanthera/OpenCorePkg/releases) y luego corre el siguiente comando

```sh
# Ajusta este comando para que apunte a la ubicación de la carpeta
cd ~/Downloads/OpenCore-0/Utilities/macrecovery/
```

Luego corre uno de los siguientes comandos dependiendo del SO que deseas arrancar:

```sh
# Lion(10.7):
python ./macrecovery.py -b Mac-2E6FAB96566FE58C -m 00000000000F25Y00 download
python ./macrecovery.py -b Mac-C3EC7CD22292981F -m 00000000000F0HM00 download

# Mountain Lion(10.8):
python ./macrecovery.py -b Mac-7DF2A3B5E5D671ED -m 00000000000F65100 download

# Mavericks(10.9):
python ./macrecovery.py -b Mac-F60DEB81FF30ACF6 -m 00000000000FNN100 download

# Yosemite(10.10):
python ./macrecovery.py -b Mac-E43C1C25D4880AD6 -m 00000000000GDVW00 download

# El Capitan(10.11):
python ./macrecovery.py -b Mac-FFE5EF870D7BA81A -m 00000000000GQRX00 download

# Sierra(10.12):
python ./macrecovery.py -b Mac-77F17D7DA9285301 -m 00000000000J0DX00 download

# High Sierra(10.13)
python ./macrecovery.py -b Mac-7BA5B2D9E42DDD94 -m 00000000000J80300 download
python ./macrecovery.py -b Mac-BE088AF8C5EB4FA2 -m 00000000000J80300 download

# Mojave(10.14)
python ./macrecovery.py -b Mac-7BA5B2DFE22DDD8C -m 00000000000KXPG00 download

# Catalina(10.15)
python ./macrecovery.py -b Mac-00BE6ED71E35EB86 -m 00000000000000000 download

# Latest version
# ie. Big Sur(11)
python ./macrecovery.py -b Mac-E43C1C25D4880AD6 -m 00000000000000000 download
```

![](../images/installer-guide/legacy-mac-install-md/download-done.png)

* **Nota**: Dependiendo del SO obtendrás archivos BaseSystem o RecoveryImage. Las dos actúan de la misma manera, por lo que cuando hacemos referencia a BaseSystem la misma información aplica a RecoveryImage
## Creando el instalador

Esta sección tendrá como objetivo crear las distintas particiones en tu USB. Puedes utilizar tu programa favorito, ya sea `gdisk`` fdisk` `parted`` gparted` o `gnome-disks`. Esta guía se centrará en `gdisk` ya que es agradable y puede cambiar el tipo de partición más adelante, ya que la necesitamos para que macOS Recovery HD pueda arrancar. (La distro utilizada aquí es Ubuntu 18.04, otras versiones o distros pueden funcionar)


Créditos a [midi1996](https://github.com/midi1996) por su trabajo en la [guía de instalación por Internet](https://midi1996.github.io/hackintosh-internet-install-gitbook/), en la que esta guía está basada. 

### Método 1

1. Corre `lsblk` y determina el bloque de tu USB
  ![lsblk](../images/installer-guide/linux-install-md/unknown-5.png)
2. Corre `sudo gdisk /dev/<el bloque de tu USB>`
   1. Si eres preguntado  qué tabla de particiones utilizar, selecciona GPT
      ![Selecciona GPT](../images/installer-guide/linux-install-md/unknown-6.png)
   2. Envía `p` para imprimir las particiones de tu bloque \(y verifica que es la que necesitas\)
      ![](../images/installer-guide/linux-install-md/unknown-13.png)
   3. Envía `o` para limpiar la tabla de particiones y crear una nueva GPT (si no está vacía)
      1. Confirma con `y`
         ![](../images/installer-guide/linux-install-md/unknown-8.png)
   4. send `n`
      1. `número de la partición`:  mantener en blanco para usar el predeterminado 
      2. `primer sector`:  mantener en blanco para usar el predeterminado 
      3. `segundo sector`:  mantener en blanco para usar el disco entero
      4. `Código hexadecimal o GUID`: `0700` para el tipo de partición de datos básicos de Microsoft
   5. Envía `w`
      * Confirma con `y`
      ![](../images/installer-guide/linux-install-md/unknown-9.png)
      * En algunos casos es necesario reiniciar, pero raramente. Si quieres estar seguro, reinicia tu computadora. También puedes intentar volver a enchufar tu USB.
   6. Cierra `gdisk` enviandoando `q` (normalmente debería cerrarse solo)
3. Usa `lsblk` para determinar los identificadores de tu partición
4. Corre `sudo mkfs.vfat -F 32 -n "OPENCORE" /dev/<el bloque de particiones de tu USB>` para formatear tu USB a FAT32 y llamarlo OPENCORE
5. Luego utiliza `cd` para llegar a `/OpenCore/Utilities/macrecovery/` y deberías ver unos archivos `.dmg` y `.chunklist`
   1. Monta tu partición USB con `udisksctl` (`udisksctl mount -b /dev/<el bloque de partición de tu USB>`, (sudo no es requerido en la mayoría de los casos) o con `mount` (`sudo mount /dev/<el bloque de partición de tu USB> /donde/montas/tus/cosas`, sudo es necesario)
   2. Utiliza `cd` para llegar a tu USB y luego corre `mkdir com.apple.recovery.boot` en la raíz de tu la partición FAT32 de tu USB
   3. Ahora utiliza `cp` o `rsync` para llevar ambos `BaseSystem.dmg` y `BaseSystem.chunklist` a la carpeta `com.apple.recovery.boot`.
### Método 2 (en caso de que el método 1 no funcionó)

En la terminal:

1. corre `lsblk` y determina el bloque de tu dispositivo USB
   ![](../images/installer-guide/linux-install-md/unknown-11.png)
2. corre `sudo gdisk /dev/<el bloque de tu USB>`
   1. Si eres preguntado qué tabla de particiones usar, selecciona GPT
      ![](../images/installer-guide/linux-install-md/unknown-12.png)
   2. Envía `p` para imprimir los bloques de las particiones \(y verifica que es la que necesitas\)
      ![](../images/installer-guide/linux-install-md/unknown-13.png)
   3. Envía `o` para borrar la tabla de particiones y crear una nueva GPT (si no está vacía)
      1. Confirma con `y`
         ![](../images/installer-guide/linux-install-md/unknown-14.png)
   4. Envía `n`
      1. partition number: mantener en blanco para usar el predeterminado 
      2. first sector: mantener en blanco para usar el predeterminado
      3. last sector: `+200M` para crear una partición de 200MB que luego llamarás OPENCORE
      4. Hex code or GUID: `0700` para el tipo de partición de datos básicos de Microsoft
      ![](../images/installer-guide/linux-install-md/unknown-15.png)
   5. Envía `n`
      1. partition number: mantener en blanco para usar el predeterminado 
      2. first sector: mantener en blanco para usar el predeterminado 
      3. last sector: mantener en blanco por defecto \(o puedes hacerlo `+3G` si deseas particionar el USB aún más\)
      4. Hex code or GUID: `af00` para el tipo de particiones de Apple (HFS/HFS+)
      ![](../images/installer-guide/linux-install-md/unknown-16.png)
   6. Envía `w`
      * Confirma con `y`
      ![](../images/installer-guide/linux-install-md/unknown-17.png)
      * En algunos casos es necesario reiniciar, pero raramente. Si quieres estar seguro, reinicia tu computadora. También puedes intentar volver a enchufar tu USB.
   7. Cierra `gdisk` Envíando `q` (normalmente debería cerrarse solo)
3. Usa `lsblk` de nuevo para determinar el disco de 200MB y la otra partición
   ![](../images/installer-guide/linux-install-md/unknown-18.png)
4. corre `sudo mkfs.vfat -F 32 -n "OPENCORE" /dev/<tu bloque de particiones de 200MB>` para formatear la partición de 200MB a FAT32 y ponerle el nombre "OPENCORE"
5. luego usa `cd` para llegar a `gibmacos-master/macOS\ Downloads/publicrelease/xxx-xxxxx - 10.x.x macOS xxx` y deberías ver un archivo `pkg`
   ![](../images/installer-guide/linux-install-md/unknown-19.png)
   1. descarga `p7zip-full` (dependiendo en las herramientas de tu distro; los usuarios de macrecovery.py pueden saltearse esto)
      * para Ubuntu/Basadas en Ubuntu corre `sudo apt install p7zip-full`
      * para arch arch/basadas en arch corre `sudo pacman -S p7zip`
      * para el resto, deberías saber como hacerlo
      * para todas las distros: **asegúrate que estas usando bash para que 7zip funcione**.
   2. corre esto `7z e -txar *.pkg *.dmg; 7z e *.dmg */Base*` para extraer `BaseSystem.dmg` y `BaseSystem.chunklist` (usuarios de macrecovery.py pueden saltearse esto)
   3. descarga `dmg2img` (disponible en la mayoria de las distros)
   4. corre `dmg2img -l BaseSystem.dmg` y determina qué partición tiene la propiedad `disk image`
      ![](../images/installer-guide/linux-install-md/unknown-20.png)
   5. corre `dmg2img -p <el número de la partición> -i BaseSystem.dmg -o <tu bloque de partición 3GB+>` para extraer y escribir la imagen de recuperación a la partición
      * Esto llevará un tiempo. Y aún más si estás usando un USB lento.
      ![](../images/installer-guide/linux-install-md/unknown-21.png)

## Ahora con todo esto hecho, dirígete a [Configurando tu EFI](./opencore-efi.md) para terminar tu trabajo
