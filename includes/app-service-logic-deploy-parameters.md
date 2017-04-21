Con el Administrador de recursos de Azure, se definir parámetros para los valores que desee para especificar cuando se implementó la plantilla. La plantilla incluye una sección denominada parámetros que contiene todos los valores de parámetro.
Debe definir un parámetro para los valores que varían basado en el proyecto que se va a implementar o basado en el entorno de que implementación. No defina parámetros para los valores que siempre serán siendo la misma. Cada valor del parámetro se utiliza en la plantilla para definir los recursos que se implementan. 

Al definir los parámetros, utilice el campo **allowedValues** para especificar los valores que puede proporcionar un usuario durante la implementación. Utilice el campo **ValorPredeterminado (DefaultValue)** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Describiremos cada parámetro de la plantilla.

### <a name="logicappname"></a>logicAppName

El nombre de la lógica de la aplicación para crear.

    "logicAppName": {
        "type": "string"
    }