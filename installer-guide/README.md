# Creando el USB

* Versión soportada: 0.6.8

Requisitos:

* [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/releases), altamente recomendado utilizar la versión debug para obtener más información.
* [ProperTree](https://github.com/corpnewt/ProperTree) para editar archivos .plist (OpenCore Configurator es otra herramienta pero está altamente desactualizada y la versión de Mackie es conocida por corromper las plists **Por favor  evita este tipo de herramientas a toda costa**.
* NVRAM limpia (Esto es de gran importancia si has usado Clover antes, ya que muchas variables seguirán causando conflictos. Afortunadamente con OpenCore puedes usar la herramienta `CleanNvram` en su menú cuando `AllowNvramReset` está habilitado en tu configuración)
* Conocimiento básico de cómo funciona un Hackintosh y qué archivos requiere (por ejemplo el tipo de controlador de red) 
* Debes quitar Clover de tu sistema por completo si deseas usar OpenCore como tu gestor de arranque. Sin embargo, haz una copia de seguridad de tu EFI basada en Clover. Dirígete aquí para ver qué debe ser limpiado de tu sistema:[Conversión de Clover](https://github.com/dortania/OpenCore-Install-Guide/tree/master/clover-conversion)
### Instalador Online vs Offline

Los instaladores Offline tienen una copia completa de macOS, mientras que los online son sólo una imagen de recuperación (~500MB) que luego descarga macOS desde los servidores de Apple una vez que arranque

* Offline
  * Sólo puede ser creado desde macOS
  * Windows/Linux no tienen los drivers APFS/HFS necesitados para ensamblar un instalador completo
* Online
  * Puede ser creado desde macOS/Linux/Windows
  * Requiere una conexión a Internet que funcione a través de un adaptador de red compatible con macOS en la computadora en la que quieres instalarlo
# Creando el instalador

Dependiendo en el SO que estés corriendo, visita tu sección específica:

* [Usuarios de macOS](/installer-guide/mac-install.md)
  * Soporta desde OS X 10.4 en adelante
  * Soporta instalaciones tanto legacy como UEFI
* [Usuarios de Windows](/installer-guide/winblows-install.md)
  * Soporta desde OS X 10.7 en adelante
  * Soporta instalaciones tanto legacy como UEFI
* [Linux users(UEFI)](../installer-guide/linux-install.md)
  * Soporta desde OS X 10.7 en adelante
  * Pensado para equipos que soporten arranque UEFI