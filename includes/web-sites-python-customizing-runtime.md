Azure determinará la versión de Python que se utilizará para su entorno virtual con la siguiente prioridad:

1. versión especificada en runtime.txt en la carpeta raíz
1. la versión especificada en la configuración de Python en la configuración de la aplicación web (la **configuración** > hoja de**Configuración de la aplicación** para la aplicación web en el Portal de Azure)
1. 2.7 de Python es el valor predeterminado si no se especifica ninguna de las anteriores

Valores válidos para el contenido de 

    \runtime.txt

son los siguientes:

- Python 2.7
- 3.4 de Python

Si la versión micro (tercer dígito) se especifica, se omite.
