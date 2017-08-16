---
title: "Diferenças do Azure Service Fabric entre o Linux e o Windows | Microsoft Docs"
description: "Diferenças entre a Pré-visualização do Azure Service Fabric no Linux e no Windows."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 68c7e1f3f51ca5bec30a0f71aaccbafa58078e69
ms.contentlocale: pt-pt
ms.lasthandoff: 06/30/2017

---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Diferenças entre o Service Fabric no Linux (pré-visualização) e no Windows (disponível em geral)

Uma vez que o Service Fabric no Linux é uma pré-visualização, existem algumas funcionalidades que são suportadas no Windows, mas no Linux ainda não. Eventualmente, os conjuntos de funcionalidades ficarão em paridade quando o Service Fabric no Linux se torna disponível em geral. Em versões futuras, essa diferença de funcionalidades será cada vez menor. Existem diferenças entre as versões mais recentes disponíveis (ou seja, entre a versão 5.6 no Windows e a versão 5.5 no Linux): 

* Coleções Fiáveis (e Serviços com Estado Fiável) 
* ReverseProxy 
* Instalador autónomo 
* Validação do esquema XML para os ficheiros de manifesto 
* Redirecionamento da consola 
* Serviço de Análise de Falhas (FAS)
* Composição e volume do docker e controladores de registo para contentores 
* Governação de recursos para contentores e serviços 
* Serviço DNS
* Suporte do Azure Active Directory
* Equivalentes do comando CLI de determinados comandos do Powershell 
* Apenas um subconjunto de comandos do Powershell pode ser executado comparativamente a um cluster do Linux (conforme expandido na secção seguinte).

>[!NOTE]
>O redirecionamento da consola não é suportado em clusters de produção, mesmo no Windows.

As ferramentas de desenvolvimento também são diferentes entre o Windows e o Linux. O VisualStudio, Powershell, VSTS e ETW são utilizados no Windows enquanto o Yeoman, Eclipse, Jenkins e LTTng são utilizados no Linux.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Cmdlets do PowerShell que não funcionam num cluster do Service Fabric do Linux

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Passos seguintes
* [Preparar o ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Use the Azure CLI to manage your Service Fabric applications (Utilizar a CLI do Azure para gerir as aplicações do Service Fabric)](service-fabric-azure-cli.md)

