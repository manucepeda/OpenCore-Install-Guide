# macOS Legacy: Método offline

Este método nos permite descargar instaladores completos de Apple, aunque está limitado a 10.10, Yosemite, por lo que versiones más antiguas tendrán que ser obtenidas mediante el "Método online" mencionado abajo.

Para comenzar, dirígete a alguno de los siguientes links (información en inglés):

* [Cómo actualizar a OS X Yosemite](https://support.apple.com/es-es/HT210717)
* [Cómo actualizar a OS X El Capitan](https://support.apple.com/es-es/HT206886)
* [Cómo actualizar a macOS Sierra](https://support.apple.com/es-es/HT208202)

En el paso 4, verás `InstallOS.dmg` para Sierra o `InstallMacOSX.dmg` para El Capitan y versiones anteriores. Descarga la versión que quieres y deberías obtener un archivo .pkg.

Dependiendo de qué SO estés, puedes correr este script y dirigirte a [Configurando el instalador](#configurando-el-instalador). Sin embargo, si recives este error:

![](../images/installer-guide/legacy-mac-install-md/unsupported.png)

Esto significa que tendremos que extraer el instalador manualmente.

### Extrayendo el instaldor

Para empezar, agarra el InstallMacOSX/InstallOS.dmg y móntalo:

![](../images/installer-guide/legacy-mac-install-md/mount.png)

Luego, abre una ventana de la terminal y haz una carpeta en el escritorio. Corre los comandos uno por uno:

```sh
cd ~/Desktop
mkdir MacInstall && cd MacInstall
```

Ahora llegamos a la parte divertida, extrayendo el instalador (ten en cuenta que esto puede tomar algunos minutos):

* Para El Capitan (10.11) y anterior:

```sh
xar -xf /Volumes/Install\ OS\ X/InstallMacOSX.pkg
```

* Para Sierra (10.12):

```sh
xar -xf /Volumes/Install\ macOS/InstallOS.pkg
```

Luego, corre lo siguiente (uno por uno):

* Yosemite:

```sh
cd InstallMacOSX.pkg
tar xvzf Payload
mv InstallESD.dmg Install\ OS\ X\ Yosemite.app/Contents/SharedSupport/
mv Install\ OS\ X\ Yosemite.app /Applications
```

* El Capitan:

```sh
cd InstallMacOSX.pkg
tar xvzf Payload
mv InstallESD.dmg Install\ OS\ X\ El\ Capitan.app/Contents/SharedSupport/
mv Install\ OS\ X\ El\ Capitan.app /Applications
```

* Sierra:

```sh
cd InstallOS.pkg
tar xvzf Payload
mv InstallESD.dmg Install\ macOS\ Sierra.app/Contents/SharedSupport/
mv Install\ macOS\ Sierra.app /Applications
```

Una vez que esto está listo, puedes dirigirte a [Configurando el instalador](./mac-install.md#setting-up-the-installer)!