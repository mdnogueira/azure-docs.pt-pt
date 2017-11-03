---
title: "Descrição geral do DSC da automatização do Azure | Microsoft Docs"
description: "Uma descrição geral do Automation DSC do Azure pretendido Estado Configuration (), os termos e problemas conhecidos"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "PowerShell dsc, configuração de estado pretendido, azure do powershell dsc"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: 468321fa6863d78bc0d179fbe5c2ed6195040d50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-dsc-overview"></a>Descrição geral do DSC da automatização do Azure

Automation DSC do Azure é um serviço Azure que permite-lhe escrever, gerir e compilar PowerShell pretendido Estado Configuration (DSC) [configurações](https://msdn.microsoft.com/powershell/dsc/configurations), importar [recursos de DSC](https://msdn.microsoft.com/powershell/dsc/resources)e atribuir as configurações para nós de destino, todos na nuvem.

## <a name="why-use-azure-automation-dsc"></a>Porquê utilizar o DSC de automatização do Azure

Automation DSC do Azure fornece várias vantagens relativamente ao utilizar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de solicitação incorporadas

A automatização do Azure fornece um [servidor de solicitação do DSC](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver) para que nós de destino recebem automaticamente as configurações, está em conformidade com o estado pretendido e devolver relatórios sobre a sua conformidade.
O servidor de solicitação incorporada na automatização do Azure elimina a necessidade de configurar e manter o seu próprio servidor de solicitação.
A automatização do Azure pode visar virtuais ou físicas Windows ou Linux máquinas, na nuvem ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos de DSC

Automation DSC do Azure oferece a mesma camada de gestão para [configuração de estado pretendido do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) como automatização do Azure oferece para processamento de scripts do PowerShell.

O portal do Azure ou a partir do PowerShell, pode gerir todos os seus DSC configurações, recursos e nós de destino.

![Captura de ecrã do painel de automatização do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importar dados de relatórios para análise de registos

Nós que são geridos com o Automation DSC do Azure enviarem dados de estado de relatórios detalhados para o servidor de solicitação incorporada.
Pode configurar o DSC de automatização do Azure para enviar estes dados para a sua área de trabalho de análise de registos do Microsoft Operations Management Suite (OMS).
Para saber como enviar dados de estado de DSC para a sua área de trabalho de análise de registos, consulte [reencaminhar Automation DSC do Azure dados de relatórios para análise de registos do OMS](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Vídeo de introdução

Prefere ver do que ler? Tem a ver o seguinte vídeo de Maio de 2015, quando Automation DSC do Azure foi anunciada pela primeira vez.

>[!NOTE]
>Apesar dos conceitos e o ciclo de vida descritos neste vídeo estarem corretos, DSC de automatização do Azure tem progredido muito desde que o vídeo foi gravado.
>Está agora disponível normalmente, tem uma IU muito mais extensa no portal do Azure e suporta muitas funcionalidades adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Passos seguintes

* Para saber como nós de carregar para serem geridos pelo Automation DSC do Azure, consulte [máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md)
* Para começar a utilizar o DSC de automatização do Azure, consulte o artigo [introdução ao Azure Automation DSC](automation-dsc-getting-started.md)
* Para saber mais sobre como compilar as configurações de DSC para que pode atribuir-lhes para nós de destino, consulte o artigo [compilar configurações de DSC de automatização do Azure](automation-dsc-compile.md)
* Para referência de cmdlet do PowerShell para o Automation DSC do Azure, consulte [cmdlets do Automation DSC do Azure](/powershell/module/azurerm.automation/#automation)
* Para obter informações sobre preços, consulte [preços do Automation DSC do Azure](https://azure.microsoft.com/pricing/details/automation/)
* Para ver um exemplo de utilização do Azure Automation DSC num pipeline a implementação contínua, consulte [a implementação contínua para IaaS VMs utilizando o Automation DSC do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)