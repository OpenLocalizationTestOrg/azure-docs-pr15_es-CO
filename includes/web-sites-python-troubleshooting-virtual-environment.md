El script de implementación omitirá la creación del entorno virtual en Azure si detecta que ya existe un entorno virtual compatible.  Esto puede acelerar la instalación considerablemente.  Se omitirán los paquetes que ya están instalados por pip.

En ciertas situaciones, puede que desee obligar a eliminar ese entorno virtual.  Deseará hacerlo si decide incluir un entorno virtual como parte de su repositorio.  También puede hacer esto si debe deshacerse de algunos paquetes, o para probar cambios en requirements.txt.

Existen unas cuantas opciones para administrar el entorno virtual existente en Azure:

### <a name="option-1-use-ftp"></a>Opción 1: Utilizar FTP

Con un cliente FTP, conectar con el servidor y podrá eliminar la carpeta env.  Tenga en cuenta que algunos clientes FTP (por ejemplo, los exploradores web) pueden ser de sólo lectura y no le permiten eliminar carpetas, así que es conveniente para asegurarse de que al utilizar a un cliente FTP con esa capacidad.  El nombre de host FTP y el usuario se muestran en la hoja de sus aplicaciones web en el [Portal de Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opción 2: Alternar runtime

Aquí es una alternativa que aprovecha el hecho de que el script de implementación eliminará la carpeta env al no coincide con la versión deseada de Python.  Esto efectivamente eliminar el entorno existente y crear uno nuevo.

1. Cambiar a una versión diferente de Python (a través de runtime.txt o la **Configuración de la aplicación** en el Portal de Azure)
1. GIT push algunos cambios (omitir los errores de instalación de pip si existe)
1. Volver a la versión inicial de Python
1. GIT push algunos cambios otra vez

### <a name="option-3-customize-deployment-script"></a>Opción 3: Personalizar la secuencia de comandos de implementación

Si ha personalizado el script de implementación, puede cambiar el código en deploy.cmd para obligarle a eliminar la carpeta env.
