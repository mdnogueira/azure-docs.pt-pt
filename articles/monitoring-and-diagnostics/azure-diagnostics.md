---
title: "Descrição geral de diagnóstico do Azure | Microsoft Docs"
description: "Utilize o diagnóstico do Azure para depuração, medir o desempenho, monitorização, análise de tráfego em serviços em nuvem, as máquinas virtuais e recursos de infraestrutura de serviço"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: 0c6e4d9d2a3744f607b72364f3944c700acd070c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-diagnostics"></a>O que é o diagnóstico do Azure
Diagnóstico do Azure é a capacidade no Azure que permite a recolha de dados de diagnóstico sobre uma aplicação implementada. Pode utilizar a extensão de diagnóstico de um número de diferentes origens. São atualmente suportados Web de serviço de nuvem do Azure e funções de trabalho, máquinas virtuais do Azure com o Microsoft Windows e o Service Fabric. Outros serviços do Azure têm os seus próprios diagnóstico separado.

## <a name="data-you-can-collect"></a>É possível recolher de dados
Diagnóstico do Azure pode recolher os seguintes tipos de dados:

| Origem de dados | Descrição |
| --- | --- |
| Contadores de desempenho |Sistema operativo e os contadores de desempenho personalizado |
| Registos de aplicações |Mensagens de rastreio escritas pela sua aplicação |
| Registos de eventos do Windows |Informações enviadas para o sistema de registo de eventos do Windows |
| Origem de evento de .NET |Código de escrita de eventos utilizando o .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) classe |
| Registos de IIS |Informações sobre sites de web do IIS |
| O manifesto com base em ETW |Eventos de rastreio para o Windows de eventos gerados por qualquer processo |
| Informações de falhas |Informações sobre o estado do processo em caso de uma falha de aplicação |
| Registos de erros personalizados |Registos criados pela sua aplicação ou serviço |
| Registos de diagnóstico de infraestrutura do Azure |Informações sobre diagnósticos próprio |

A extensão de diagnóstico do Azure pode transferir estes dados para uma conta de armazenamento do Azure ou enviá-lo para serviços como o [Application Insights](../application-insights/app-insights-cloudservices.md). Pode utilizar os dados para a depuração e resolução de problemas, medir o desempenho, a utilização de recursos, análise de tráfego e planeamento da capacidade de monitorização e auditoria.

## <a name="versioning"></a>Controlo de versões
Consulte [histórico de controlo de versões de diagnóstico do Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Passos seguintes
Escolha o serviço está a tentar recolher diagnósticos no e utilize os artigos seguintes para começar a utilizar. Utilize as ligações de geral de diagnóstico do Azure para referência para tarefas específicas.

## <a name="web-apps"></a>Aplicações Web
Tenha em atenção que as aplicações Web não utilizam diagnósticos do Azure. Localizar as informações equivalentes em [aplicações Web](../app-service/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços em nuvem com o diagnóstico do Azure
* Se utilizar o Visual Studio, consulte [utilize o Visual Studio para rastrear uma aplicação de serviços em nuvem](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar a utilizar. Caso contrário, consulte
* [Como monitorizar serviços em nuvem com o diagnóstico do Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configurar o diagnóstico do Azure numa aplicação de serviços Cloud](../cloud-services/cloud-services-dotnet-diagnostics.md)

Para obter tópicos mais avançados, consulte

* [Utilizar o diagnóstico do Azure com o Application Insights para serviços em nuvem](../application-insights/app-insights-cloudservices.md)
* [Analisar o fluxo de uma aplicação de serviços em nuvem com o diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Utilize o PowerShell para configurar diagnósticos no serviços Cloud](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Máquinas virtuais utilizando o diagnóstico do Azure
* Se utilizar o Visual Studio, consulte [utilize o Visual Studio para rastreio Virtual Machines do Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar a utilizar. Caso contrário, consulte
* [Configurar uma Máquina Virtual no Azure do diagnóstico do Azure](../virtual-machines-dotnet-diagnostics.md)

Para obter tópicos mais avançados, consulte

* [Utilize o PowerShell para configurar diagnósticos em Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma Máquina Virtual do Windows com a monitorização e diagnóstico com o modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric através do diagnóstico do Azure
Começar a utilizar em [monitorizar uma aplicação de Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico de Service Fabric estão disponíveis na árvore de navegação à esquerda, depois de obter a este artigo.

## <a name="general-azure-diagnostics-articles"></a>Artigos geral do diagnóstico do Azure
* [Configuração de esquema de diagnóstico do Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) -aprender a alterar o ficheiro de esquema para recolher e encaminhar os dados de diagnóstico. Tenha em atenção que também pode utilizar o Visual Studio para alterar o ficheiro de esquema.
* [Como os dados de diagnóstico do Azure são armazenados no Storage do Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) -conhecer os nomes das tabelas e blobs onde os dados de diagnóstico são escritos.
* Saiba como [utilizar os contadores de desempenho no Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
* Saiba como [informações de diagnóstico de rota Azure ao Application Insights](azure-diagnostics-configure-application-insights.md)
* Se tiver problemas com a iniciar o diagnóstico ou localizar os dados nas tabelas de armazenamento do Azure, consulte [resolução de problemas do diagnóstico do Azure](azure-diagnostics-troubleshooting.md)
