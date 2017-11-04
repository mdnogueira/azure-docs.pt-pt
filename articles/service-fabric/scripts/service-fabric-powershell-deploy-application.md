---
title: "Script do PowerShell do Azure de exemplo - implementar aplicações para um cluster | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - implementar uma aplicação para um cluster do Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 159b856606885c4ee206ba42ec72a8bd1ec5b0f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementar uma aplicação para um cluster do Service Fabric

Este script de exemplo copia um pacote de aplicação para um arquivo de imagens do cluster, regista o tipo de aplicação no cluster, remove o pacote de aplicações desnecessárias e cria uma instância de aplicação do tipo de aplicação.  Se quaisquer serviços predefinido foram definidos no manifesto da aplicação do tipo de aplicação de destino, esses serviços são criados neste momento. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo do PowerShell de Service Fabric com o [SDK de Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois do script de exemplo foi executado, o script [remover uma aplicação](service-fabric-powershell-remove-application.md) pode ser utilizado para remover a instância da aplicação, anular o registo do tipo de aplicação e eliminar o pacote de aplicações da loja de imagem.

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Cria uma ligação para um cluster do Service Fabric. |
|[Cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Arquivo de cópias de um pacote de aplicação para a imagem do cluster.  |
|[Registar ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Regista um tipo de aplicação e a versão no cluster. |
|[Novo ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Cria uma aplicação a partir de um tipo de aplicação registada. |
| [Remover ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove um pacote de aplicação de Service Fabric o arquivo de imagens.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do PowerShell de Service Fabric, consulte [documentação do Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Exemplos do Powershell adicionais para o Azure Service Fabric podem ser encontrados no [exemplos do PowerShell do Azure](../service-fabric-powershell-samples.md).
