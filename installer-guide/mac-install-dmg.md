# macOS Legacy: Imágenes de discos

Los instaladores de abajo fueron extraidos de discos de restauración de Macs geniunas con su bloqueo de SMBIOS desactivado, el contenido de OS X en sí no ha sido modificado de ninguna manera.

Este método en realidad depende de las imágenes alojadas por Acidanthera o Apple, y la restauración de estas a tu disco.

#### Imágenes de Acidanthera

* [OS X 10.4.10(8R4088)](https://archive.org/details/10.4.10-8-r-4088-acdt)[Mirror MEGA](https://mega.nz/folder/D3ASzLzA#7sjYXE2X09f6aGjol_C7dg)

* [OS X 10.5.7(9J3050)](https://archive.org/details/10.5.7-9-j-3050)[Mirror MEGA](https://mega.nz/folder/inRBTarD#zanf7fUbviwz3WHBU5xpCg)

* [OS X 10.6.7(10J4139)](https://archive.org/details/10.6.7-10j3250-disk-images)[Mirror MEGA](https://mega.nz/folder/z5YUhYTb#gA_IRY5KMuYpnNCg7kR3ug/file/ioQkTagI)

#### Imágenes de Apple

Ten en cuenta que estas imágenes requieren de que tengas una cuenta de desarrollador de Apple para poder acceder.

* [OS X 10.5.0 Golden Master(9a581)](https://download.developer.apple.com/Mac_OS_X/mac_os_x_v10.5_leopard_9a581/leopard_9a581_userdvd.dmg)

* [OS X 10.6.0 Golden Master(10a432)](https://download.developer.apple.com/Mac_OS_X/mac_os_x_version_10.6_snow_leopard_build_10a432/mac_os_x_v10.6_build_10a432_user_dvd.dmg)

### Restaurando el disco

Ahora viene la parte divertida, primero querrás abrir el DMG que descargaste y montarlo. Luego abre Disk Utility y formatea tu disco como macOS Extended (HFS+) con un mapa de particiones GUID:

![Formateando el USB](../images/installer-guide/mac-install-md/format-usb.png)

Luego tenemos dos opciones a seguir:

* [Una restauración ASR](#asr)(**A**pple **S**oftware **R**estore)
  * Basada en la terminal, funciona mejor con el SIP habilitado
* [Restauración utilizando Disk Utility](#disk-utility)
  * Puede requerir del SIP deshabilitado en SOs más recientes

#### ASR

Aquí simplemente debes abrir la terminal y correr lo siguiente:

```sh
sudo asr restore -source /Volumes/Mac\ OS\ X\ Install\ DVD  -target /Volumes/MyVolume -erase -noverify
```

* **Nota**: Esto puede no alinearse con tu configuración, por favor cámbialo en consecuencia:
  * Cambia `/Volumes/Mac\ OS\ X\ Install\ DVD` al nombre de tu imagen de disco montada
  * Cambia `/Volumes/MyVolume` al nombre de tu USB

Esto tomará un tiempo pero una vez que termines, puedes dirigirte a [Configurando el entorno de OpenCore](./mac-install.md#setting-up-opencore-s-efi-environment)

#### Disk Utility

Debido a algunos problemas molestos con Disk Utility, muchas restauraciones pueden fallar si el SIP está habilitado. Si tienes problemas recomendamos que uses el [Método ASR](#asr) o deshabilites el SIP.

Para comenzar, abre Disk Utility y deberías ver tu USB y la imagen de disco en la barra lateral. Desde aquí, selecciona restaurar/restore. 

![](../images/installer-guide/legacy-mac-install-md/pre-restore.png)
![](../images/installer-guide/legacy-mac-install-md/restore.png)

Esto tomará un tiempo pero una vez que termine, puedes dirigirte a [Configurando el entorno de OpenCore](./mac-install.md#setting-up-opencore-s-efi-environment)

::: details Solución de problemas

Si tienes errores como este durante la restauración:

![](../images/installer-guide/legacy-mac-install-md/sip-fail.png)

Esto seguramente signifique que el SIP debe ser deshabilitado, por lo que recomendamos que en vez sigas el [Método ASR](#asr) 

Una vez que termines, puedes dirigirte a [Configurando el entorno de OpenCore](./mac-install.md#setting-up-opencore-s-efi-environment)