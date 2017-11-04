
### <a name="cacheskuname"></a>cacheSKUName
O escalão de preço da Cache de Redis do Azure novo.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

O modelo define os valores que são permitidos para este parâmetro (básico ou Standard) e atribui um valor predefinido (básico), se for especificado nenhum valor. Basic fornece um único nó com vários tamanhos disponíveis cópias de segurança a 53 GB.
Norma fornece dois nós primário/réplica com vários tamanhos disponíveis segurança 53 GB e 99,9% de SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily
A família para o sku.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
O tamanho da nova instância da Cache de Redis do Azure. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


O modelo define os valores que são permitidos para este parâmetro (0, 1, 2, 3, 4, 5 ou 6) e atribui um valor predefinido (1) se for especificado nenhum valor. Os números correspondem ao seguinte tamanhos de cache: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

