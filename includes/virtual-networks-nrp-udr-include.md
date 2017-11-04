## <a name="route-tables"></a>Tabelas de rotas
Recursos de tabela de rota contém rotas utilizadas para definir a forma como o tráfego flui dentro da sua infraestrutura do Azure. Pode utilizar rotas definidas pelo utilizador (UDR) para enviar todo o tráfego de sub-rede fornecida para uma aplicação virtual, tais como um sistema de deteção intrusões ou uma firewall (IDS). Pode associar uma tabela de rotas para sub-redes. 

As tabelas de rotas contêm as seguintes propriedades.

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **rotas** |Coleção de utilizador rotas definidas na tabela de rota |consulte [rotas definidas pelo utilizador](#User-defined-routes) |
| **sub-redes** |Coleção de sub-redes é aplicada a tabela de rotas para |consulte [sub-redes](#Subnets) |

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador
Pode criar UDRs para especificar onde o tráfego deve ser enviado, com base no respetivo endereço de destino. Pode considerar que a definição de gateway predefinido com base no endereço de destino de um pacote de rede uma rota.

UDRs contêm as seguintes propriedades. 

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **addressPrefix** |Prefixo do endereço ou o endereço IP completo para o destino |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |Tipo de dispositivo, que o tráfego será enviado para |Internet VirtualAppliance, Gateway de VPN |
| **nextHopIpAddress** |Endereço IP para o salto seguinte |192.168.1.4 |

Tabela de rota de exemplo no formato JSON:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>Recursos adicionais
* Obter mais informações [UDRs](../articles/virtual-network/virtual-networks-udr-overview.md).
* Leia o [documentação de referência da REST API](https://msdn.microsoft.com/library/azure/mt502549.aspx) para tabelas de rota.
* Leia o [documentação de referência da REST API](https://msdn.microsoft.com/library/azure/mt502539.aspx) do utilizador definida rotas (UDRs).

