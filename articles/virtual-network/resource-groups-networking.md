---
title: "Descrição geral do fornecedor de recursos de rede | Microsoft Docs"
description: Saiba mais sobre o novo fornecedor de recursos de rede no Gestor de recursos do Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="network-resource-provider"></a>Fornecedor de Recursos de Rede
É necessário um underpinning no êxito de empresas da atualidade, é a capacidade para criar e gerir aplicações com suporte de rede de grande escala de uma forma célere, flexível, segura e repetíveis. O Azure Resource Manager permite-lhe criar essas aplicações, como uma única coleção de recursos em grupos de recursos. Esses recursos são geridos através de vários fornecedores de recursos no Gestor de recursos.

O Azure Resource Manager depende de fornecedores de recursos diferentes para fornecer acesso aos seus recursos. Existem três fornecedores de recursos principais: rede, armazenamento e computação. Este documento descreve as características e os benefícios do fornecedor de recursos de rede, incluindo:

* **Metadados** – pode adicionar informações a recursos através de etiquetas. Estas etiquetas podem ser utilizadas para controlar a utilização de recursos entre grupos de recursos e as subscrições.
* **Maior controlo da sua rede** - recursos de rede são aproximadamente ligado conjugados e pode controlá-los de forma mais granular. Isto significa que tem mais flexibilidade para gerir os recursos de rede.
* **A configuração mais rápida** -porque os recursos de rede aproximadamente ligado são essenciais, pode criar e orquestrar recursos de rede em paralelo. Isto foi significativamente configuração tempo reduzido.
* **Controlo de acesso baseado em funções** -RBAC fornece funções predefinidas, com o âmbito de segurança específicos, além de lhe permitir a criação de funções personalizadas para gestão segura.
* **Mais fácil gestão e implementação** -é mais fácil de implementar e gerir aplicações, uma vez que pode pode criar uma pilha de aplicação completo como uma única coleção de recursos num grupo de recursos. E mais rápido para implementar, uma vez que pode implementar fornecendo simplesmente um payload JSON do modelo.
* **Personalização rápida** -pode utilizar modelos declarativos estilo para ativar a personalização repetíveis e rápida das implementações.
* **Personalização repetíveis** -pode utilizar modelos declarativos estilo para ativar a personalização repetíveis e rápida das implementações.
* **Interfaces de gestão** -pode utilizar qualquer um dos seguintes interfaces para gerir os recursos:
  * API baseada no REST
  * PowerShell
  * SDK .NET
  * Node.JS SDK
  * SDK Java
  * CLI do Azure
  * Portal de Pré-visualização
  * Linguagem de modelo do Resource Manager

## <a name="network-resources"></a>Recursos de rede
Agora, pode gerir recursos de rede de forma independente, em vez de ter todos eles geridos através de um recurso de computação único (uma máquina virtual). Isto assegura um nível mais elevado de flexibilidade e a agilidade na composição uma infraestrutura complexos e grande escala, num grupo de recursos.

Uma vista conceptual de uma implementação de exemplo que envolvem uma aplicação de várias camadas é apresentada abaixo. Cada recurso que vê, como NICs, endereços IP públicos e VMs, pode ser gerido de forma independente.

![Modelo de recursos de rede](./media/resource-groups-networking/Figure2.png)

Cada recurso contém um conjunto comum de propriedades e definir a respetiva propriedade individuais. As propriedades comuns são:

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **nome** |Nome de recurso exclusivo. Cada tipo de recurso tem o seus próprio restrições de nomenclatura. |NIC01 PIP01, VM01, |
| **localização** |Região do Azure em que reside o recurso |westus, eastus |
| **ID** |Identificação de URI com base em exclusiva |/subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

Pode verificar as propriedades individuais dos recursos nas secções abaixo.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de gestão
Pode gerir os recursos de rede do Azure através de interfaces diferentes. Neste documento incidirá na tow essas interfaces: API de REST e modelos.

### <a name="rest-api"></a>API REST
Conforme mencionado anteriormente, os recursos de rede podem ser geridos através de uma variedade de interfaces, incluindo a REST API, .NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Portal do Azure e os modelos.

A API de Rest está em conformidade com a especificação de protocolo HTTP 1.1. A estrutura URI geral da API é apresentada abaixo:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

E os parâmetros chavetas representam os seguintes elementos:

* **id de subscrição** -o id de subscrição do Azure.
* **recursos-fornecedor-espaço de nomes** -espaço de nomes para o fornecedor que está a ser utilizado. O valor para o fornecedor de recursos de rede é *Network*.
* **nome de região** -o nome da região do Azure

Os seguintes métodos HTTP são suportados quando efetuar chamadas à REST API:

* **COLOCAR** - utilizado para criar um recurso de um determinado tipo, modificar uma propriedade de recurso ou alterar uma associação entre os recursos.
* **OBTER** - utilizado para obter informações para um recurso aprovisionado.
* **ELIMINAR** - utilizada para eliminar um recurso existente.

O pedido e a resposta está em conformidade com um formato de payload JSON. Para obter mais detalhes, consulte [APIs de gestão de recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="resource-manager-template-language"></a>Linguagem de modelo do Resource Manager
Além de gerir recursos imperatively (através de APIs ou o SDK), também pode utilizar um estilo de programação declarativo para criar e gerir recursos de rede utilizando a linguagem de modelo do Resource Manager.

Uma representação de exemplo de um modelo é fornecida em baixo –

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

O modelo é principalmente uma descrição JSON dos recursos e os valores de instância injetados através dos parâmetros. O exemplo abaixo, pode ser utilizado para criar uma rede virtual com 2 sub-redes.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Tem a opção de fornecer os valores de parâmetro manualmente, ao utilizar um modelo ou pode utilizar um ficheiro de parâmetros. O exemplo abaixo mostra um conjunto de valores de parâmetro a ser utilizado com o modelo acima possíveis:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


As principais vantagens da utilização de modelos são:

* Pode criar uma infraestrutura complexa num grupo de recursos num style declarativo. A orquestração de criação dos recursos, incluindo gestão de dependência, é processada pelo Gestor de recursos.
* A infraestrutura pode ser criada de forma repetíveis por várias regiões e dentro de uma região alterando simplesmente parâmetros.
* O estilo declarativo leva a mais curto prazo na criação de modelos e disponibilizando a infraestrutura.

Para modelos de exemplo, consulte [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter mais informações sobre a linguagem de modelo do Resource Manager, consulte [linguagem de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

O modelo de exemplo acima utiliza a rede virtual e os recursos de sub-rede. Existem outros recursos de rede, que pode utilizar, conforme apresentado abaixo:

### <a name="using-a-template"></a>Utilizar um modelo
Pode implementar serviços no Azure a partir de um modelo com o PowerShell, AzureCLI, ou efetuando um clique para implementar a partir do GitHub. Para implementar serviços a partir de um modelo no GitHub, execute os seguintes passos:

1. Abra o ficheiro de template3 a partir do GitHub. Por exemplo, abra [rede Virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Clique em **implementar no Azure**e, em seguida, inicie sessão no portal do Azure com as suas credenciais.
3. Verifique o modelo e, em seguida, clique em **guardar**.
4. Clique em **Editar parâmetros** e selecione uma localização, tais como *EUA oeste*, para a vnet e sub-redes.
5. Se necessário, altere o **ADDRESSPREFIX** e **SUBNETPREFIX** parâmetros e, em seguida, clique em **OK**.
6. Clique em **selecione um grupo de recursos** e, em seguida, clique no grupo de recursos que pretende adicionar a vnet e sub-redes a. Em alternativa, pode criar um novo grupo de recursos, clicando em **ou criar um novo**.
7. Clique em **Criar**. Tenha em atenção a apresentação de mosaico **implementação do modelo de aprovisionamento**. Após a conclusão da implementação, verá um ecrã semelhante a um abaixo.

![Implementação do modelo de exemplo](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>Passos seguintes
[Linguagem de modelo de Gestor de recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)

[Funcionamento em rede do Azure – conferidas modelos](https://github.com/Azure/azure-quickstart-templates)

[O Azure Resource Manager vs. de implementação clássica](../azure-resource-manager/resource-manager-deployment-model.md)

[Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)

