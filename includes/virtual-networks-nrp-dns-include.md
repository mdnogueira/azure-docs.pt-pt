## <a name="azure-dns"></a>DNS do Azure
O DNS do Azure é um serviço de alojamento de domínios DNS, fornecer a resolução do nome utilizando a infraestrutura do Microsoft Azure.

| Propriedade | Descrição | Valor de exemplo |
| --- | --- | --- |
| **DNSzones** |Informação de zona de domínio para registos do anfitrião de DNS de um determinado domínio |/ subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com " |

### <a name="dns-record-sets"></a>Conjuntos de registos de DNS
Zonas DNS tem um objeto subordinado com o nome do conjunto de registos. Os conjuntos de registos são uma coleção de registos de anfitrião por tipo para uma zona DNS. Tipos de registos são A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

| Propriedade | Descrição | Valor da amostra |
| --- | --- | --- |
| A |Tipo de registo de IPv4 |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |Tipo de registo de IPv6 |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/aaaa/hostrecord |
| CNAME |tipo de registo de nome canónico <sup>1</sup> |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |tipo de registo do correio |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/MX/Mail |
| NS |tipo de registo do servidor de nome |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |Início do tipo de registo de autoridade <sup>2</sup> |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |tipo de registo do serviço |/subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> só permite um valor por conjunto de registos.

<sup>2</sup> só permite um tipo de registo SOA por zona DNS. 

Exemplo de zona DNS no formato Json:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>Recursos adicionais
Leia o [documentação da REST API para zonas DNS ](https://msdn.microsoft.com/library/azure/mt130626.aspx) para obter mais informações.

Leia o [documentação da REST API para conjuntos de registos DNS](https://msdn.microsoft.com/library/azure/mt130627.aspx) para obter mais informações.

