---
title: "Atualizar recursos no Azure geridos aplicações | Microsoft Docs"
description: "Descreve como trabalhar nos recursos de geridos no grupo de recursos para um Azure aplicações geridas."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 59dce2fe7d91cc80f991e5ff298be7757ae19ef4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabalhar com recursos a geridos em aplicações geridas do grupo de recursos do Azure

Este artigo descreve como atualizar recursos que são implementados como parte de uma aplicação gerida. Como o publicador de uma aplicação gerida, terá acesso aos recursos no grupo de recursos geridos. Para atualizar estes recursos, tem de localizar o grupo de recursos geridos associado uma aplicação gerida e aceder o recursos nesse grupo de recursos.

Este artigo pressupõe que implementou a aplicação gerida no [geridos aplicação Web (IaaS) com os serviços de gestão do Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) projeto de exemplo. Que aplicações geridas incluem um **Standard_D1_v2** máquina virtual. Se não tiver implementado essa aplicação gerida, ainda pode utilizar este artigo para se familiarizar com os passos para atualizar um grupo de recursos geridos.

A imagem seguinte mostra as aplicações geridas implementadas.

![Aplicações geridas implementadas](./media/update-managed-resources/deployed.png)

Neste artigo, utilizar a CLI do Azure para:

* Identificar a aplicação gerida
* Identificar o grupo de recursos geridos
* Identificar os recursos de máquina virtual no grupo de recursos geridos
* Altere o tamanho da VM (à um tamanho mais pequeno, se não utilizados ou superior para suportar mais carga)
* Atribuir uma política para o grupo de recursos geridos pelo localizações permitidas

## <a name="get-managed-application-and-managed-resource-group"></a>Obter aplicações geridas e grupo de recursos geridos

Para obter as aplicações geridas num grupo de recursos, utilize:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obter o ID do grupo de recursos geridos, utilize:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Redimensionar VMs no grupo de recursos geridos

Para ver as máquinas virtuais no grupo de recursos geridos, forneça o nome do grupo de recursos geridos.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Para atualizar o tamanho das VMs, utilize:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Após a conclusão da operação, certifique-se de que a aplicação está em execução no v2 D2 padrão.

![Aplicações geridas utilizando D2 padrão v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicar a política de grupo de recursos geridos

O grupo de recursos geridos e atribuição de uma política, obter esse âmbito. A política **e56962a6-4747-49cd-b67b-bf8b01975c4c** uma política incorporadas para especificar localizações permitidas.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Para ver localizações permitidas, utilize:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A atribuição de política é apresentada no portal.

![Atribuição de política do Vista](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral de aplicações gerido](overview.md).
* Para projetos de exemplo, consulte [projetos de exemplo para o Azure geridos aplicações](sample-projects.md).
* Para obter informações sobre a publicação de aplicações geridas no Azure Marketplace, consulte [as aplicações no mercado gerido](publish-marketplace-app.md).