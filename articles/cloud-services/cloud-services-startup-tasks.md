<properties 
pageTitle="Ejecutar tareas de inicio de los servicios de nube Azure | Microsoft Azure" 
description="Las tareas de inicio ayudan a preparar el entorno para la aplicación del servicio de nube. Esto le enseña cómo hacerlos y cómo funcionan las tareas de inicio" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Cómo configurar y ejecutar las tareas de inicio para un servicio de nube

Puede utilizar las tareas de inicio para realizar operaciones antes de una función. Las operaciones que desee realizar incluyen instalar un componente, registrar los componentes COM, establecer las claves del registro o iniciar un proceso de larga ejecución.

>[AZURE.NOTE] Las tareas de inicio no son aplicables a las máquinas virtuales, sólo para Web de servicio de nube y las funciones de trabajo.

## <a name="how-startup-tasks-work"></a>Cómo funcionan las tareas de inicio

Las tareas de inicio son acciones que se realizan antes de comenzarán de sus roles y se definen en el archivo [finalmente] mediante el elemento de [tarea] dentro del elemento de [Inicio] . Con frecuencia, las tareas de inicio son archivos por lotes, pero también pueden ser aplicaciones de consola o archivos por lotes que inicie los scripts de PowerShell.

Las variables de entorno pasan información a una tarea de inicio y almacenamiento local puede utilizarse para pasar información de una tarea de inicio. Por ejemplo, una variable de entorno puede especificar la ruta de acceso a un programa que desea instalar y poder escribir archivos al almacenamiento local que puede leer posteriormente por sus funciones.

La tarea de inicio puede registrar información y errores en el directorio especificado por la variable de entorno **TEMP** . Durante la tarea de inicio, la variable de entorno **TEMP** se resuelve en el *C:\\recursos\\temp\\[guid]. [ roleName]\\RoleTemp* directorio cuando se ejecutan en la nube.

Las tareas de inicio también se puede ejecutar varias veces entre reinicios. Por ejemplo, se ejecutará la tarea de inicio cada vez que el papel se recicla y recicla papel podría no incluir siempre un reinicio. Las tareas de inicio deben escribirse de forma que les permite ejecutarse varias veces sin problemas.

Las tareas de inicio deben terminar con un **errorlevel** o código de salida de cero para el proceso de inicio para completar. Si una tarea de inicio termina con un **errorlevel**de distinto de cero, la función no se iniciará.


## <a name="role-startup-order"></a>Orden de inicio de la función

A continuación muestra el procedimiento de inicio de la función en Azure:

1. La instancia se marca como **inicial** y no recibe tráfico.

2. Todas las tareas de inicio se ejecutan según su atributo **taskType** .
    - Las tareas **simples** se ejecutan sincrónicamente, uno a la vez.
    - Las tareas de **segundo** **plano** y se inician de forma asincrónica, en paralelo a la tarea de inicio.  
       
    > [AZURE.WARNING] IIS no esté completamente configurado durante la etapa de la tarea de inicio del proceso de inicio, por lo que los datos específicos de la función no esté disponibles. Las tareas de inicio que requieren datos específicos de la función deben utilizar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Se inicia el proceso de host de la función y se crea el sitio en IIS.

4. Se llama al método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

5. La instancia se marca como **Listo** y el tráfico se enruta a la instancia.

6. Se llama al método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) .


## <a name="example-of-a-startup-task"></a>Ejemplo de una tarea de inicio

Las tareas de inicio se definen en el archivo [finalmente] , en el elemento de **tarea** . El atributo de **línea de comandos** especifica el nombre y los parámetros del archivo por lotes de inicio o comando de la consola, el atributo **executionContext** especifica el nivel de privilegio de la tarea de inicio, y el atributo **taskType** especifica cómo se ejecutará la tarea.

En este ejemplo, una variable de entorno **MyVersionNumber**, se crea para la tarea de inicio y establecida en el valor "**1.0.0.0**".

**Finalmente**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

En el ejemplo siguiente, el archivo por lotes **Startup.cmd** escribe la línea "la versión actual es 1.0.0.0" en el archivo StartupLog.txt en el directorio especificado por la variable de entorno TEMP. El `EXIT /B 0` línea garantiza que la tarea de inicio termina con un **errorlevel** de cero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] En Visual Studio, debe establecerse la propiedad de **Copiar en el directorio de resultados** para el archivo de proceso por lotes de inicio para **Copiar siempre** para asegurarse de que el archivo de proceso por lotes de inicio se implementa correctamente al proyecto en Azure (**approot\\bin** de los roles de Web y **approot** para funciones de trabajo).

## <a name="description-of-task-attributes"></a>Descripción de los atributos de la tarea

Lo siguiente describe los atributos del elemento de **tarea** en el archivo [finalmente] :

**línea de comandos** - especifica la línea de comandos para la tarea de inicio:

- El comando, con parámetros de línea de comandos opcionales, que comienza la tarea de inicio.
- Suele ser el nombre de un archivo de proceso por lotes .bat o .cmd.
- La tarea está en relación con el AppRoot\\carpeta Bin para la implementación. Las variables de entorno no se expanden en la determinación de la ruta y el archivo de la tarea. Si se requiere la expansión del entorno, puede crear una secuencia de comandos .cmd pequeño que llama a la tarea de inicio.
- Puede ser una aplicación de consola o un archivo por lotes que se inicia una [secuencia de comandos de PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - especifica el nivel de privilegio para la tarea de inicio. El nivel de privilegios puede ser limitado o elevado:

- **limitado**  
La tarea de inicio se ejecuta con los mismos privilegios que el rol. Cuando el atributo **executionContext** para el elemento [en tiempo de ejecución] también es **limitado**, se utilizan los privilegios de usuario.

- **elevados**  
La tarea de inicio se ejecuta con privilegios de administrador. Esto permite que las tareas de inicio para instalar programas, realizar cambios de configuración de IIS, realizar cambios en el registro y otras tareas de nivel de administrador, sin aumentar el nivel de privilegio de la propia función.  

> [AZURE.NOTE] El nivel de privilegio de una tarea de inicio no necesita ser la misma que la propia función.

**taskType** - especifica el modo en que se ejecuta una tarea de inicio.

- **simple**  
Las tareas se ejecutan de forma sincrónica, uno a la vez, en el orden especificado en el archivo [finalmente] . Cuando finaliza una tarea **simple** de inicio con un **errorlevel** de cero, se ejecuta la siguiente tarea de inicio **simple** . Si no hay ningún más **sencillas** las tareas de inicio para ejecutar, se iniciará la propia función.   

    > [AZURE.NOTE] Si la **simple** tarea finaliza con un cero **errorlevel**, se bloqueará la instancia. Tareas posteriores **simple** de inicio y el rol de sí mismo, no se iniciarán.

    Para asegurarse de que el archivo por lotes finaliza con un **errorlevel** de cero, ejecute el comando `EXIT /B 0` al final del proceso de archivo por lotes.

- **fondo**  
Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio de la función.

- **primer plano**  
Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio de la función. La diferencia clave entre un **primer plano** y una tarea en **segundo plano** es que una tarea de **primer plano** impide la función de reciclado o cerrando hasta que haya finalizado la tarea. Las tareas de **segundo plano** no tienen esta restricción.

## <a name="environment-variables"></a>Variables de entorno

Las variables de entorno son una manera de pasar información a una tarea de inicio. Por ejemplo, puede colocar la ruta de acceso a un objeto binario que contiene un programa para instalar, o números de puerto que utilizará su función o configuración de funciones de control de la tarea de inicio.

Hay dos tipos de variables de entorno para las tareas de inicio; variables de entorno estáticos y variables de entorno basadas en miembros de la clase [RoleEnvironment] . Los dos están en la sección de [entorno] del archivo [finalmente] y ambos utilizan el atributo de elemento y el **nombre** de [Variable] .

Las variables de entorno estático se utiliza el atributo **value** del elemento [Variable] . El ejemplo anterior crea la variable de entorno **MyVersionNumber** que tiene un valor estático de "**1.0.0.0**". Otro ejemplo sería crear una variable de entorno **StagingOrProduction** que se puede establecer manualmente los valores de "**provisional**" o "**producción**" para realizar acciones de inicio diferente en función del valor de la variable de entorno **StagingOrProduction** .

Las variables de entorno basadas en miembros de la clase RoleEnvironment no utilice el atributo **value** del elemento [Variable] . En su lugar, el elemento secundario de [RoleInstanceValue] , con el valor de atributo **XPath** correspondiente, se utilizan para crear una variable de entorno que se basa en un miembro específico de la clase [RoleEnvironment] . Valores para el atributo **XPath** tener acceso a distintos valores de [RoleEnvironment] se pueden encontrar [aquí](cloud-services-role-config-xpath.md).



Por ejemplo, para crear una variable de entorno es "**true**" cuando la instancia se ejecuta en el emulador de cómputo y "**false**" cuando se ejecuta en la nube, utilice los siguientes elementos de [Variable] y [RoleInstanceValue] :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Próximos pasos
Aprenda cómo realizar algunas [tareas de inicio](cloud-services-startup-tasks-common.md) con el servicio de nube.

[Paquete de](cloud-services-model-and-package.md) su servicio de nube.  


[Finalmente]: cloud-services-model-and-package.md#csdef
[Tarea]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Inicio]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[En tiempo de ejecución]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Entorno de]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx