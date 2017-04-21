
El código de todas las funciones en una función determinada de la aplicación reside en una carpeta raíz que contiene un archivo de configuración de host y una o varias subcarpetas, cada uno de los cuales contienen el código para una función separada, como en el ejemplo siguiente

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

El archivo *host.json* contiene una configuración específica en tiempo de ejecución y se ubica en la carpeta raíz de la aplicación de la función. Para obtener información sobre las opciones que están disponibles, vea [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en el wiki de repositorio de WebJobs.Script.

Cada función tiene una carpeta que contiene uno o más archivos de código, la configuración de function.json y otras dependencias.