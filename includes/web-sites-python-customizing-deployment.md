Azure determinará que la aplicación utiliza Python **Si se cumplen estas dos condiciones**:

- archivo de Requirements.txt en la carpeta raíz
- cualquier archivo .py en la carpeta raíz o un runtime.txt que especifica python

Cuando ese es el caso, utilizará una secuencia de comandos de implementación específicas de Python, que realiza la sincronización de archivos, así como otras operaciones de Python como estándar:

- Administración automática del entorno virtual
- Instalación de paquetes enumerados en requirements.txt con pip
- Creación de web.config apropiado basándose en la versión seleccionada de Python.
- Recopilar archivos estáticos para aplicaciones Django

Puede controlar ciertos aspectos de los pasos de implementación predeterminado sin tener que personalizar la secuencia de comandos.

Si desea omitir todos los pasos de implementación concretos de Python, puede crear este archivo vacío:

    \.skipPythonDeployment

Más control sobre la implementación, puede reemplazar la secuencia de comandos de implementación predeterminada mediante la creación de los archivos siguientes:

    \.deployment
    \deploy.cmd

Puede utilizar la [interfaz de línea de comandos de Azure][] para crear los archivos.  Utilice este comando desde la carpeta del proyecto:

    azure site deploymentscript --python

Cuando estos archivos no existen, Azure crea un script de implementación temporal y lo ejecuta.  Es idéntica a la que se crea con el comando anterior.

[Interfaz de línea de comandos de Azure]: http://azure.microsoft.com/downloads/
