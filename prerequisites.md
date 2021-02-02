# Comenzando con OpenCore

Antes de comenzar a hacer un sistema basado en OpenCore, debemos repasar algunas cosas.

## Prerequisitos
  
1. _**[CRUCIAL]**_ Tiempo y paciencia.
   * No comienzes a trabajar en esto si tienes plazos que cumplir o trabajo importante para hacer. Los hackintoshes no son algo en lo que debas confiar como máquina de trabajo. 
2. _**[CRUCIAL]**_ **CONOCE TU HARWARE**
   * El nombre de tu CPU al igual que su generación
   * Tu(s) GPU(s)
   * La configuración de tus dispositivos de almacenamiento (HDD/SSD, NVMe/AHCI/RAID/IDE)
   * El modelo de tu PC/Laptop si es de un OEM
   * Tu **Chipset de ethernet**
   * Tu chipset WLAN/Bluetooth
3. _**[CRUCIAL]**_ **CONOCIMINETO BÁSICO DE LAS LINEAS DE COMANDOS Y CÓMO USAR TERMINAL/CMD**
   * Esto nó solo es [CRUCIAL], es la base de toda esta guía. No te podemos ayudar si no sabes como usar `cd` o si no sabes cómo borrar un archivo, por ejemplo.
4. _**[CRUCIAL]**_ Una computadora que es compatible, como se ve en la sección de _**Compatibilidad**_ .
   * [Página de limitaciones de hardwarwe](/macos-limits.md)
5. _**[CRUCIAL]**_ Un USB con un mínimo de:
   * 16GB si usarás macOS para crear el USB
   * 4GB si usarás Windows o Linux para crear el USB
6. _**[CRUCIAL]**_ Una **conección a Ethernet** (No adaptadores WiFi, y los adaptadores USB de Ethernet pueden o no funcionar dependiendo del soporte de macOS). Debes saber el modelo de tu tarjeta LAN.
   * Debes tener ya sea un puerto de Ethernet físico, o un adaptador/"dongle" compatible con macOS. En el caso de que tengas una [tarjeta WiFi compatible](https://dortania.github.io/Wireless-Buyers-Guide/), puedes usarla.
     * Ten en cuenta que la mayoría de las tarjetas WiFi no son compatibles con macOS.
   * Para las personas que no pueden usar ethernet pero tienen un teléfono android, pueden conectar su teléfono android a el WiFi y después compartir internet via USB con [HoRNDIS](https://joshuawise.com/horndis#available_versions).
7. _**[CRUCIAL]**_ **Una instalación adecuada de un SO:**
   * Puede ser:
     * macOS (mejor si es una versión relativamente nueva)
     * Windows (Windows 10, 1703 o posterior)
     * Linux (con Python 2.7 o posterior), asegúrate que esté limpia y funcionando correctamente.
   * Para usuarios de Windows o Linux, **15GB** de espacio libre en el disco que estés trabajando. En Windows, el disco de tu SO, (C:), debe tener al menos 15GB libres.
   * Para usuarios de macOS, **30GB** de espacio libre en el disco de tu sistema.
      * La mayoría de las herramientas utilizadas en esta guía también requerirán de que tengas [Python instalado](https://www.python.org/downloads/)
