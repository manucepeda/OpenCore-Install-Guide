# Apoyando las guías

**Nota**: Esta es una guía de la comunidad que no está respaldada oficialmente por Acidanthera. Por favor no molesten a Acidanthera con problemas de esta guía.

¿Quieres ayudar a apoyar la guía? Bueno, hay algunas maneras en las que puedes ayudar!

* [Contribuir via problmeas](#contribuir-via-problemas-issues)
* [Contribuir via Pull Requests](#contribuir-via-pull-requests)
* [Contribuir via Traducciones](#contribuir-via-traducciones)

Nota: Para aquellos que quieran contribuir financieramente, lo apreciamos mucho, pero somos una organización sin fines de lucro. Hacemos esto para enseñar, no para ganar dinero. Si te sobra dinero, te recomendamos que lo dones a una organización benéfica. [Crohn's and Colitis Canada](https://crohnsandcolitis.donorportal.ca/Donation/DonationDetails.aspx?L=en-CA&G=159&F=1097&T=GENER) es una que recomendamos si no tienes ninguna en mente.

## Contribuir via problemas (issues)

Contribuir a través de problemas es bastante simple pero hay ciertas reglas:

* Mantén la pestaña de problemas con temas dedicados solo a problemas de las guías, **y no con problemas de hackintosh personales**. No es el lugar para discutir problemas de tu instalación.
* Si se trata de un error tipográfico o de una mejor aclaración, indica en qué página estaba. Agradeceríamos no ir a una búsqueda del tesoro para encontrar dónde están estos problemas.

Puedes encontrar el bugtracker aquí: [Bugtracker](https://github.com/dortania/bugtracker)

## Contribuir via pull requests

Algunas pautas al contribuir a través de PRs:

* Usa tu cerebro (por favor).
* Revisa tus presentaciones.
* Podemos denegar los PRs si consideramos que no encaja en la guía o tiene información inexacta. Generalmente te diremos por qué lo rechazamos o pediremos revisiones.
  * En el caso de que sean contribuciones grandes, agradeceríamos las fuentes de la información para que sea más fácil para nosotros verificar que la información que proporcionas es válida
* Las imágenes deben estar alojadas localmente en el repositorio bajo la carpeta `../ images /`
* Tu PR debe ejecutarse a través de un "markdown lint" y debe tener todos los problemas solucionados.
* En general, intenta a evitar el uso de herramientas que no sean de Acidanthera cuando sea posible. Generalmente queremos evitar el uso de herramientas de terceros, aunque si es imposible de lo contrario, puedes vincularla.
  * Herramientas explícitamente prohibidas:
    * UniBeast, MultiBeast y KextBeast
      * Se puede encontrar más información aquí: [Tonymacx86-stance](https://github.com/khronokernel/Tonymcx86-stance)
    * TransMac
      * Conocido por crear USBs que no funcionan correctamente
    * Instaladores de Niresh
      * Quisiéramos evitar la piratería con las guías

### Cómo Contribuir

La mejor manera de probar tus commits y asegurarte de que su formato es correcto es descargar Node.js y luego correr `npm install` para instalar las dependencias. Cuando corres `npm run dev`, configurará un webserver local al que puedes conectar para ver los cambios que has hecho. `npm test` también arrojará cualquier error sobre el formato y la corrección ortográfica. Si quieras usar `markdownlint` para intentar a arreglar automáticamente arreglar el linting, corre `npm run fix-lint`.

Instrucciones paso-a-paso:

* [Forkear este repo](https://github.com/dortania/OpenCore-Install-Guide/fork/)
* Instalar las herraminetas requeridas:
  * [Node.js](https://nodejs.org/)
* Hacer tus cambios.
* Construir el sitio:
  * `npm install` (Para instalar todos los plugins requeridos)
  * `npm run dev` (Tener una vista previa del sitio)
    * Se puede encontrar en `http://localhost:8080`
* Revisar linting y errores ortográficos:
  * `npm test`
  * `npm run lint` y `npm run spellcheck` (para correrlos individualmente)
  * `npm run fix-lint` (Para solucionar cualquier problema potencial)

### Tips

Algunas herramientas que facilitan la contribución:

* [Typora] (https://typora.io) para renderizado de rebajas en tiempo real.
* [TextMate] (https://macromates.com) para encontrar/reemplazar en masa de forma fácil.
* [Github de escritorio](https://desktop.github.com) para una GUI más fácil de usar.

## Contribuir via Traducciones

Aunque las guías de Dortania están basadas principalmente en inglés, sabemos que hay muchos otros idiomas en el mundo y que no todos hablan inglés con fluidez. Si quieres ayudar a traducir nuestras guías a idiomas diferentes, estamos más que felices de apoyarte.

Cosas principales a tener en cuenta:

* Las traducciones deben ser un fork dedicado que no formará parte o se fusionará con las guías de Dortania
* Los forks deben indicar que son traducciones de Dortania y que no son oficiales
* Los forks también deben cumplir con nuestra [Licencia](LICENSE.md)

Si se cumple lo anterior, ¡estás libre a hostear tu traducción sin problema! Los sitios de Dortania son construidos con [VuePress] (https://vuepress.vuejs.org) usando [Travis-CI] (https://travis-ci.org) y finalmente alojados en [Github Pages] (https://pages.github.com), así que no hay nigún costo por alojar tu propia traducción.

Si tienes alguna pregunta o inquietud con las traducciones o el alojamiento, no dudes en comunicarte con nosotros mediante nuestro [Bugtracker](https://github.com/dortania/bugtracker).

Traducciones conocidas actualmente:

* [InyextcionES](https://github.com/InyextcionES/OpenCore-Install-Guide)(Español)
* [macOS86](https://macos86.gitbook.io/guida-opencore/)(Italiano, ya no se mantiene)
* [Technopat](https://www.technopat.net/sosyal/konu/opencore-ile-macos-kurulum-rehberi.963661/)(Turco)
* [ThrRip](https://github.com/ThrRip/OpenCore-Install-Guide)(Chino)

Ten en cuenta que estas traducciones están sujetas a las preferencias de los autores, los cambios de traducción y los errores humanos. Por favor, ten esto en cuenta al leer dado que ya no son guías oficiales de Dortania.

**Créditos de la traducción: [Guía original](https://dortania.github.io/OpenCore-Install-Guide/)**
