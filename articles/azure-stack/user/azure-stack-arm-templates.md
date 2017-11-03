---
title: Utilizar modelos Azure Resource Manager na pilha do Azure | Microsoft Docs
description: Saiba como utilizar os modelos Azure Resource Manager na pilha do Azure para Aprovisionar recursos.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 7648855011e8f77c35713d2d2ae50f2e474a08a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utilizar modelos Azure Resource Manager na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Modelos Azure Resource Manager implementar e aprovisionar todos os recursos para a sua aplicação numa operação única e coordenada. Também pode voltar a implementar modelos para efetuar alterações aos recursos no grupo de recursos.

Estes modelos podem ser implementados com o portal de pilha do Microsoft Azure, PowerShell, a linha de comandos e o Visual Studio.

Os modelos de início rápido seguintes estão disponíveis no [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Implementar SharePoint (não alta disponibilidade)
Utilize a extensão de DSC do PowerShell para criar um farm do SharePoint 2013 inclui os seguintes recursos:

* Uma rede virtual
* Três contas de armazenamento
* Dois balanceadores de carga externo
* Uma VM configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autónomo do SQL Server 2014
* Uma VM configurada como um farm do SharePoint 2013 de uma máquina

## <a name="deploy-ad-non-high-availability"></a>Implementar AD (não alta disponibilidade)
Utilize a extensão de DSC do PowerShell para criar um servidor de controlador de domínio do AD que inclui os seguintes recursos:

* Uma rede virtual
* Uma conta de armazenamento
* Um balanceador de carga externo
* Uma VM configurada como um controlador de domínio para uma nova floresta com um único domínio

## <a name="deploy-adsql-non-high-availability"></a>Implementar AD/SQL (não alta disponibilidade)
Utilize a extensão de DSC do PowerShell para criar um servidor autónomo do SQL Server 2014 que inclui os seguintes recursos:

* Uma rede virtual
* Duas contas de armazenamento
* Um balanceador de carga externo
* Uma VM configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autónomo do SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Utilize a extensão de DSC do PowerShell para configurar uma máquina virtual existente Gestor de configuração Local (MMC) e registá-lo para um servidor de solicitação de DSC do Azure Automation conta.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Criar uma máquina virtual a partir de uma imagem de utilizador
Crie uma máquina virtual a partir de uma imagem de utilizador personalizadas. Este modelo também implementa uma rede virtual (com o DNS), o endereço IP público e uma interface de rede.

## <a name="simple-vm"></a>Simple VM
Implemente uma VM do Windows que inclui uma rede virtual (com o DNS), o endereço IP público e uma interface de rede.

## <a name="cancel-a-running-template-deployment"></a>Cancelar uma implementação de modelo em execução
Para cancelar uma implementação de modelo em execução, utilize o `Stop-AzureRmResourceGroupDeployment` cmdlet do PowerShell.

## <a name="next-steps"></a>Passos seguintes
[Implementar modelos com o portal](azure-stack-deploy-template-portal.md)

[Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

