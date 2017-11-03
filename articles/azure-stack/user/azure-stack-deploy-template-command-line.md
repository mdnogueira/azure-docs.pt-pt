---
title: Implementar modelos com a linha de comandos na pilha do Azure | Microsoft Docs
description: "Saiba como utilizar a interface de linha de comandos de várias plataformas (CLI) para implementar modelos pilha do Azure."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implementar modelos na pilha do Azure utilizando a linha de comandos

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Utilize a linha de comandos para implementar modelos Azure Resource Manager para o Kit de desenvolvimento de pilha do Azure. Modelos Azure Resource Manager implementar e aprovisionar todos os recursos para a sua aplicação numa operação única e coordenada.

## <a name="before-you-begin"></a>Antes de começar
 - [Instalar e ligar](azure-stack-connect-cli.md) à pilha do Azure com a CLI do Azure
 - Transferir os ficheiros *azuredeploy. JSON* e *azuredeploy.parameters.json* do [criar modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Implementar o modelo
Navegue para a pasta onde estes ficheiros foram transferidos e execute o seguinte comando para implementar o modelo:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Este comando implementa o modelo para o grupo de recursos **cliRG** na localização de predefinida da POC de pilha do Azure.

## <a name="validate-template-deployment"></a>Validar a implementação do modelo
Para ver esta conta de armazenamento e de grupo de recursos, utilize os seguintes comandos:

    azure group list

    azure storage account list




