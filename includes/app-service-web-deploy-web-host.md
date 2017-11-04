### <a name="app-service-plan"></a>Plano do App Service
Cria o plano de serviço para alojar a aplicação web. Forneça o nome do plano através do **hostingPlanName** parâmetro. A localização do plano é a mesma localização que utilizou para o grupo de recursos. O preço tamanho de camadas e de trabalho estão especificados no **sku** e **workerSize** parâmetros

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

