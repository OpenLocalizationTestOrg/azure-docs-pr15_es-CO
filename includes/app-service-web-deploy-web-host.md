### <a name="app-service-plan"></a>Plan de servicio de la aplicación

Se crea el plan de servicio para hospedar la aplicación web. Debe proporcionar el nombre del plan a través del parámetro **hostingPlanName** . La ubicación del plan es la misma ubicación que se utiliza para el grupo de recursos. El tamaño de trabajador y de nivel de precios se especifican en los parámetros **sku** y **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

