## <a name="nic"></a>NIC
Um recurso de cartão (NIC) de interface de rede oferece a conectividade de rede a uma sub-rede existente num recurso VNet. Embora possa criar uma NIC como um objeto de autónomo, tem de associá-la para outro objecto para, efetivamente, fornecer conectividade. Uma NIC pode ser utilizada para ligar uma VM a uma sub-rede, um endereço IP público ou um balanceador de carga.  

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **máquina virtual** |VM a NIC está associado. |/subscriptions/{GUID}/../microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |Endereço MAC para o NIC |qualquer valor entre 4 e 30 |
| **networkSecurityGroup** |NSG associado para o NIC |/subscriptions/{GUID}/../microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |Definições de DNS para a NIC |consulte [PIP](#Public-IP-address) |

Uma placa de Interface de rede ou NIC, representa uma interface de rede que pode ser associada a uma máquina virtual (VM). Uma VM pode ter um ou vários NICs.

![NIC numa única VM](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>Configurações de IP
Os NICs que têm um objeto subordinado com o nome **ipConfigurations** que contém as seguintes propriedades:

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **sub-rede** |A NIC de sub-rede é onnected para. |/subscriptions/{GUID}/../microsoft.Network/virtualNetworks/myvnet1/Subnets/mysub1 |
| **privateIPAddress** |Endereço IP para o NIC na sub-rede |10.0.0.8 |
| **privateIPAllocationMethod** |Método de alocação de IP |Dinâmicas ou estáticas |
| **enableIPForwarding** |Se a NIC pode ser utilizada para o encaminhamento |VERDADEIRO ou FALSO |
| **primário** |Indica se a NIC está o NIC primário para a VM |VERDADEIRO ou FALSO |
| **publicIPAddress** |PIP associada com a NIC |consulte [as definições de DNS](#DNS-settings) |
| **loadBalancerBackendAddressPools** |Fazer uma cópia de conjuntos de endereços de fim que a NIC está associada | |
| **loadBalancerInboundNatRules** |A NIC está associada de regras de NAT de Balanceador de carga de entrada | |

Exemplo público endereço IP no formato JSON:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>Recursos adicionais
* Leia o [documentação de referência da REST API](https://msdn.microsoft.com/library/azure/mt163579.aspx) para NICs.

