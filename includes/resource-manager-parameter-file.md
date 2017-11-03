Se utilizar um ficheiro de parâmetros para passar valores de parâmetros durante a implementação, terá de criar um ficheiro JSON com formato semelhante ao seguinte exemplo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

O tamanho do ficheiro de parâmetros não pode ser superior a 64 KB.

Se tiver de fornecer um valor sensível para um parâmetro (por exemplo, uma palavra-passe), adicione esse valor para um cofre de chaves. Obter o Cofre de chaves durante a implementação, conforme mostrado no exemplo anterior. Para obter mais informações, consulte [passar valores seguros durante a implementação](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md). 

