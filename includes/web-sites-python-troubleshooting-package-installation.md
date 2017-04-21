Algunos paquetes no pueden instalarse con pip cuando se ejecuta en Azure.  Simplemente puede ser que el paquete no está disponible en el índice de paquete de Python.  Es posible que un compilador es necesario (un compilador no está disponible en el equipo que ejecuta la aplicación web en el servicio de aplicación de Azure).

En esta sección, veremos formas de tratar este problema.

### <a name="request-wheels"></a>Ruedas de solicitud

Si la instalación del paquete requiere un compilador, debe intentar ponerse en contacto con el propietario del paquete para solicitar que las ruedas esté disponible para el paquete.

Con la reciente disponibilidad de [Compilador de Microsoft Visual C++ para Python 2.7][], ahora es más fácil crear paquetes que tienen código nativo para Python 2.7.

### <a name="build-wheels-requires-windows"></a>Generar las ruedas (requiere Windows)

Nota: Cuando se utiliza esta opción, asegúrese de que al compilar el paquete mediante un entorno de Python que coincide con la plataforma / / versión de arquitectura que se utiliza en la aplicación web en el servicio de aplicación de Azure (Windows/32-bit/2.7 o 3.4).

Si el paquete no se instala porque requiere un compilador, puede instalar el compilador en el equipo local y construir una rueda para el paquete, que luego se va a incluir en el repositorio.

Los usuarios de Mac/Linux: Si no tiene acceso a un equipo de Windows, vea [crear una máquina Virtual de Windows ejecutando][] para crear una máquina virtual en Azure.  Se puede utilizar para construir las ruedas, agregarlos al repositorio y descartar la máquina virtual si lo desea. 

Para Python 2.7, puede instalar [Microsoft Visual C++ Compiler para Python 2.7][].

Para 3.4 de Python, puede instalar [Microsoft Visual C++ 2010 Express][].

Para generar las ruedas, necesitará el paquete rueda:

    env\scripts\pip install wheel

Utilizará `pip wheel` una dependencia de compilación:

    env\scripts\pip wheel azure==0.8.4

Esto crea un archivo .whl en la carpeta \wheelhouse.  Agregue los archivos de la carpeta y rueda de \wheelhouse en el repositorio.

Editar el requirements.txt para agregar la `--find-links` opción en la parte superior. Esto indica a pip para buscar una coincidencia exacta en la carpeta local antes de pasar al índice de paquete de python.

    --find-links wheelhouse
    azure==0.8.4

Si desea incluir todas las dependencias en la carpeta \wheelhouse y no utilizar el índice de paquete python en absoluto, puede forzar pip para omitir el índice paquete agregando `--no-index` a la parte superior de la requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personalizar la instalación

Puede personalizar la secuencia de comandos de implementación para instalar un paquete en el entorno virtual utilizando un instalador alternativo, como fácil\_instalar.  Consulte deploy.cmd para obtener un ejemplo que está comentado.  Asegúrese de que dichos paquetes no están enumerados en requirements.txt, para impedir que se instalen pip.

Agregue lo siguiente a la secuencia de comandos de implementación:

    env\scripts\easy_install somepackage

También puede usar fácil\_install para instalar desde un instalador exe (algunos son zip compatible, es muy fácil\_instalar es compatible con ellos).  Agregar el programa de instalación al repositorio y fácil invocar\_instalar pasando la ruta de acceso al archivo ejecutable.

Agregue lo siguiente a la secuencia de comandos de implementación:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Incluir el entorno virtual en el repositorio (requiere Windows)

Nota: Cuando se utiliza esta opción, asegúrese de utilizar un entorno virtual que coincide con la plataforma / / versión de arquitectura que se utiliza en la aplicación web en el servicio de aplicación de Azure (Windows/32-bit/2.7 o 3.4).

Si incluye el entorno virtual en el repositorio, puede impedir que el script de implementación se dedican a administración de un entorno virtual en Azure creando un archivo vacío:

    .skipPythonDeployment

Se recomienda que elimine el entorno virtual existente en la aplicación, para evitar que los archivos sobrantes cuando el entorno virtual se administra automáticamente.


[Crear una máquina Virtual con Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Compilador de Microsoft Visual C++ para Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
