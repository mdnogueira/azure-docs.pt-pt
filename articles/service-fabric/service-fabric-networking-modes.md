---
title: "Configurar os modos de funcionamento em rede para serviços de contentor do Azure Service Fabric | Microsoft Docs"
description: Saiba como configurar os diferentes modos de rede que suporta o Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 855e315f66858210875039f91f7f05055ff7d9b9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Modos de funcionamento em rede de contentor do Service Fabric

A predefinição oferecido do modo de funcionamento em rede no cluster de Service Fabric para serviços de contentor é o `nat` modo de funcionamento em rede. Com o `nat` modo de funcionamento em rede, tendo mais do que um serviço de contentores a escutar os mesmos resultados de porta erros de implementação. Para executar vários serviços que escuta na mesma porta, o Service Fabric suporta o `Open` modo de funcionamento em rede (versão 5.7 ou superior). Com o `Open` modo de funcionamento em rede, cada serviço de contentor obtém um endereço IP atribuído dinamicamente internamente permitindo que vários serviços de escuta à mesma porta.   

Assim, com um tipo de serviço única com um ponto de final estático definido no manifesto de serviço, novos serviços podem ser criados e eliminados sem erros de implementação utilizando o `Open` modo de funcionamento em rede. Da mesma forma, um pode utilizar o mesmo `docker-compose.yml` ficheiro com os mapeamentos de porta estática para criar vários serviços.

Utilizar o IP dinamicamente atribuído para detetar serviços não é recomendado porque as alterações do endereço IP quando o serviço é reiniciado ou movido para outro nó. Utilizar apenas o **serviço Service Fabric Naming** ou **serviço DNS** para a deteção do serviço. 


> [!WARNING]
> Apenas um total de 4096 IPs são permitidas por vNET no Azure. Assim, a soma do número de nós e o número de instâncias de serviço de contentor (com `Open` redes) não pode exceder 4096 numa vNET. Para esses cenários high-density, o `nat` recomenda-se o modo de funcionamento em rede.
>

## <a name="setting-up-open-networking-mode"></a>Configurar o modo de funcionamento em rede aberto

1. Configurar o modelo Azure Resource Manager ao ativar o serviço DNS e o fornecedor de IP em `fabricSettings`. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. Configure a secção de perfil de rede para permitir que vários endereços IP configurados em cada nó do cluster. O exemplo a seguir configura a cinco endereços IP por nó (assim tiver cinco instâncias do serviço de escuta a porta em cada nó) para um cluster do Windows/Linux Service Fabric.

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 

3. Para apenas clusters do Windows, configure uma regra NSG abrir a porta UDP/53 para a vNET com os seguintes valores:

   | Prioridade |    Nome    |    Origem      |  Destino   |   Serviço    | Ação |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | Permitir  |


4. Especificar o modo de funcionamento em rede no manifesto da aplicação para cada serviço `<NetworkConfig NetworkType="Open">`.  O modo `Open` resulta no serviço de obter um endereço IP dedicado. Se não está especificado um modo, assume como o basic `nat` modo. Assim, no exemplo a seguir manifesto, `NodeContainerServicePackage1` e `NodeContainerServicePackage2` pode cada escuta à mesma porta (ambos os serviços estão à escuta em `Endpoint1`). Quando o `Open` modo de rede é specied, `PortBinding` folhas não podem ser especificadas.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Pode combinar e misturar os modos de rede diferentes nos vários serviços dentro de uma aplicação para um cluster do Windows. Assim, pode ter alguns serviços no `Open` modo e alguns no `nat` modo de funcionamento em rede. Quando um serviço está configurado com `nat`, a porta que está a escutar tem de ser exclusiva. A combinação de modos de funcionamento em rede para serviços diferentes não é suportada nos clusters do Linux. 


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre modos oferecidos pelo serviço de recursos de infraestrutura de rede.  

* [Modelo de aplicação de Service Fabric](service-fabric-application-model.md)
* [Recursos manifesto do serviço do Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implementar um contentor do Windows para o Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementar um contentor de Docker Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
