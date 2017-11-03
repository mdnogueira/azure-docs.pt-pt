---
title: "Exemplo de Script do PowerShell do Azure - atualização de uma aplicação de Service Fabric | Microsoft Docs"
description: "Azure PowerShell Script de exemplo - atualização de uma aplicação de Service Fabric."
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
ms.openlocfilehash: 8f6ab60861c422d083686a6ad5fb880c3e236f59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-service-fabric-application"></a>Atualizar uma aplicação de Service Fabric

Este script de exemplo atualiza uma instância de aplicação de Service Fabric em execução para a versão 1.3.0. O script copia o novo pacote de aplicação para o arquivo de imagens do cluster, regista o tipo de aplicação e remove o pacote de aplicação desnecessários.  O script inicia uma atualização monitorizada e continuamente verifica o estado de atualização até que a atualização estiver concluída ou reverte. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo do PowerShell de Service Fabric com o [SDK de Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Obtém todas as aplicações no cluster de Service Fabric ou uma aplicação específica.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Obtém o estado de uma atualização da aplicação de Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Obtém os tipos de aplicação de Service Fabric registados no cluster de Service Fabric. |
| [ServiceFabricApplicationType anular o registo](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Anula o registo de um tipo de aplicação de Service Fabric.  |
| [Cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia um pacote de aplicação de Service Fabric para o arquivo de imagens.  |
| [Registar ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Regista um tipo de aplicação de Service Fabric. |
| [Início ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Atualiza uma aplicação de Service Fabric para a versão de tipo de aplicação especificada. |
| [Remover ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Remove um pacote de aplicação de Service Fabric o arquivo de imagens.|


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do PowerShell de Service Fabric, consulte [documentação do Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Exemplos do Powershell adicionais para o Azure Service Fabric podem ser encontrados no [exemplos do PowerShell do Azure](../service-fabric-powershell-samples.md).
