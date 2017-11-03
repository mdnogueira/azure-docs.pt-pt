## <a name="virtual-network"></a>Rede Virtual
Recursos de redes (VNET) e sub-redes virtuais ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Uma VNet é caracterizada por uma coleção de espaços de endereços, definida como blocos CIDR. 

> [!NOTE]
> Os administradores de rede estiver familiarizados com notação CIDR. Se não estiver familiarizado com CIDR, [saber mais acerca do mesmo](http://whatismyipaddress.com/cidr).
> 
> 

![VNet com várias sub-redes](./media/resource-groups-networking/Figure4.png)

As VNets contêm as seguintes propriedades.

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **addressSpace** |Coleção de prefixos de endereços que compõem a VNet em notação CIDR |192.168.0.0/16 |
| **sub-redes** |Coleção de sub-redes que compõem a VNet |consulte [sub-redes](#Subnets) abaixo. |
| **ipAddress** |Endereço IP atribuído ao objeto. Esta é uma propriedade só de leitura. |104.42.233.77 |

### <a name="subnets"></a>Sub-redes
Uma sub-rede é um recurso de subordinados de uma VNet e ajuda a definir segmentos dos espaços de endereços dentro de um bloco CIDR utilizar prefixos de endereços IP. NICs podem ser adicionados a sub-redes e ligados a VMs, fornecer conectividade de várias cargas de trabalho.

Sub-redes contêm as seguintes propriedades. 

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **addressPrefix** |Prefixo de endereço único que compõem a sub-rede na notação CIDR |192.168.1.0/24 |
| **networkSecurityGroup** |NSG aplicado à sub-rede |consulte [NSGs](#Network-Security-Group) |
| **routeTable** |Tabela de rotas aplicada à sub-rede |consulte [UDR](#Route-table) |
| **ipConfigurations** |Coleção de objetos de configruation IP utilizado pelo NICs ligadas à sub-rede |consulte [UDR](#Route-table) |

Exemplo VNet no formato JSON:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Recursos adicionais
* Obter mais informações [VNet](../articles/virtual-network/virtual-networks-overview.md).
* Leia o [documentação de referência da REST API](https://msdn.microsoft.com/library/azure/mt163650.aspx) para VNets.
* Leia o [documentação de referência da REST API](https://msdn.microsoft.com/library/azure/mt163618.aspx) das sub-redes.

