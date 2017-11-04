Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada parâmetros que contém todos os valores de parâmetros.
Deve definir um parâmetro para esses valores que irão variar com base no projeto que está a implementar ou com base no ambiente que esteja a implementar. Não defina parâmetros para valores que irão sempre permaneça igual. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados. 

Quando definir parâmetros, utilize o **allowedValues** campo para especificar que os valores de um utilizador pode fornecer durante a implementação. Utilize o **defaultValue** campo para atribuir um valor para o parâmetro se não for fornecido nenhum valor durante a implementação.

Iremos descrevem cada um dos parâmetros no modelo.

### <a name="sitename"></a>SiteName
O nome da aplicação web que pretende criar.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
O nome do plano do App Service para utilizar para alojar a aplicação web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU
O escalão de preço para o plano de alojamento.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

O modelo define os valores que são permitidos para este parâmetro e atribui um valor predefinido (S1), se for especificado nenhum valor.

### <a name="workersize"></a>workerSize
O tamanho da instância do plano de alojamento (pequeno, médio ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

O modelo define os valores que são permitidos para este parâmetro (0, 1 ou 2) e atribui um valor predefinido (0) se for especificado nenhum valor. Os valores correspondem às pequena, média e grande.

