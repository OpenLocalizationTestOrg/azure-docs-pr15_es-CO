Debido a un desarrollo continuo, la versión de Android SDK instalada en Studio Android podría no coincidir con la versión en el código. El SDK de Android se hace referencia en este tutorial es versión 23, más tardar en el momento de escribir este artículo. Puede aumentar el número de versión que aparecen nuevas versiones del SDK, y se recomienda utilizar la última versión disponible.

Dos síntomas de falta de coincidencia de versión son:

1. Al generar o volver a generar el proyecto, obtendrá Gradle mensajes de error como "**no se pudo encontrar el destino Google Inc.:Google APIs:n**".

2. Objetos de Android estándares de código que debe resolver basados en `import` instrucciones pueden generar mensajes de error.

Si aparece cualquiera de ellos, la versión del SDK de Android instalado en Studio Android podría no coincidir con el destino SDK del proyecto descargado.  Para comprobar la versión, realice los siguientes cambios:


1. Android Studio, haga clic en **Herramientas** => **Android** => **Manager SDK**. Si no ha instalado la última versión de la plataforma SDK, a continuación, haga clic en para instalarla. Anote el número de versión.

2. En la ficha Explorador de proyectos, en **Secuencias de comandos de Gradle**, abra el archivo **build.gradle (modeule: app)**. Asegurarse de que los **compileSdkVersion** y **buildToolsVersion** a la última versión SDK instalada. Las etiquetas pueden tener este aspecto:
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. En el Explorador de proyectos Studio Android (ratón) en el nodo del proyecto, elija **Propiedades**y, en la columna izquierda, elija **Android**. Asegúrese de que el **Destino de compilación del proyecto** se establece en la misma versión SDK que la **targetSdkVersion**.

4. En Android Studio, el archivo de manifiesto ya no se utiliza para especificar el destino SDK y la versión mínima de SDK, a diferencia del caso con Eclipse.
