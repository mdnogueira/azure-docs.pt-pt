---
title: "Visar Atualizações com Coleções do SCCM na Gestão de Atualizações do OMS | Microsoft Docs"
description: "Este artigo destina-se a ajudar a configurar o System Center Configuration Manager com esta solução para gerir atualizações de computadores geridos pelo SCCM."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: c3ae8da65e03fe9e11b5657a6a40d02de0567da6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management-preview"></a>Integrar o System Center Configuration Manager com a Gestão de Atualizações do OMS [Pré-visualização]

Os clientes que investiram no System Center Configuration Manager para gerir PCs, servidores e dispositivos móveis também contam com a sua potência e maturidade na gestão de atualizações de software como parte do respetivo ciclo de gestão de atualizações de software (SUM).  

Tendo como base a integração existente hoje em dia entre o OMS e o Configuration Manager, pode comunicar e atualizar os servidores Windows geridos através da criação e pré-configuração de implementações de atualizações de software no Configuration Manager e obter o estado detalhado das implementações de atualizações concluídas com a [solução de Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md). Se utilizar o Configuration Manager para reportar a conformidade de atualizações, mas não para gerir implementações de atualizações com os seus servidores Windows, pode continuar a reportar para o Configuration Manager enquanto as atualizações de segurança são geridas com a solução de Gestão de Atualizações do OMS.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter a [solução de Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md) adicionada à sua área de trabalho do Log Analytics e ligada juntamente com a sua conta de Automatização no mesmo grupo de recursos e região.   
* Os servidores Windows atualmente geridos pelo seu ambiente do System Center Configuration Manager também precisam de reportar para a área de trabalho do Log Analytics que também tem a solução de Gestão de Atualizações ativada.  
* Esta funcionalidade suporta a atual versão do ramo 1606 e superior do System Center Configuration Manager.  Para integrar o seu site de administração central do Configuration Manager ou um site primário autónomo com o Log Analytics e importar coleções, reveja [Ligar o Configuration Manager ao Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Os agentes do Windows têm de ser configurados para comunicar com um servidor de Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update, caso não recebam atualizações de segurança do Configuration Manager.   

A forma de gerir os clientes alojados no IaaS do Azure com o seu ambiente existente do Configuration Manager depende principalmente da ligação que tem entre os datacenters do Azure e a sua infraestrutura. Esta ligação afeta quaisquer alterações de estrutura que possa ter de fazer na infraestrutura do Configuration Manager e o custo relacionado para suportar essas alterações necessárias.  Para compreender quais as considerações de planeamento que precisa de avaliar antes de continuar, reveja [Configuration Manager no Azure - Perguntas mais frequentes](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking).    

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerir atualizações de software a partir do Configuration Manager 

Se pretender continuar a gerir implementações de atualizações a partir do Configuration Manager, execute os passos seguintes.  O OMS liga ao Configuration Manager para aplicar atualizações aos computadores cliente ligados à sua área de trabalho do Log Analytics. O conteúdo da atualização está disponível na cache do computador cliente como se a implementação fosse gerida pelo Configuration Manager.  

1. Crie uma implementação de atualização de software a partir do site de nível superior na hierarquia do Configuration Manager com o processo descrito em [Implementar o processo de atualização de software](https://docs.microsoft.com/en-us/sccm/sum/deploy-use/deploy-software-updates).  A única definição que tem de ser configurada de forma diferente de uma implementação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de transferência do pacote de implementação. Este comportamento é gerido pela solução de Gestão de Atualizações do OMS através da criação de uma implementação de atualização agendada no passo seguinte.  
2. No portal do Azure, selecione a sua conta de Automatização no ecrã **Conta de automatização** e crie uma variável de tipo booleano com o nome **UseOMSForSCCMUpdates** com o valor **true**, seguindo a secção [Criar uma nova variável com o portal do Azure](../automation/automation-variables.md#to-create-a-new-variable-with-the-azure-portal).
3. No portal do OMS, abra o dashboard Gestão de Atualizações.  Crie uma nova implementação, seguindo os passos descritos na secção [Criar uma Implementação de Atualização](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) e selecione a coleção do Configuration Manager adequada representada como um grupo de computadores do OMS na lista pendente.  Tenha em consideração os seguintes pontos importantes:
    1. Se uma janela de manutenção estiver definida na coleção de dispositivos selecionada do Configuration Manager, os membros da coleção honram-na em vez da definição **Duração** especificada na implementação agendada no OMS.
    2. Os membros da coleção de destino têm de ter uma ligação à Internet (direta, através de um servidor proxy ou através do Gateway do OMS).  

Depois de concluir a implementação da atualização com a solução OMS, os computadores de destino que são membros do grupo de computadores selecionado irão instalar as atualizações à hora agendada a partir da respetiva cache do cliente local.  Pode [ver o estado de implementação da atualização](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments) para monitorizar os resultados da implementação.  

### <a name="manage-software-updates-from-oms"></a>Gerir atualizações de software a partir do OMS

Para gerir atualizações para as VMs do Windows Server que são clientes do Configuration Manager, precisa de configurar a política de cliente para desativar a funcionalidade de Gestão de Atualizações de Software para todos os clientes geridos por esta solução.  Por predefinição, as definições de cliente visam todos os dispositivos na hierarquia.  Para obter mais informações sobre esta definição de política e sobre como configurá-la, reveja [Como configurar as definições de cliente no System Center Configuration Manager](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings).  

Depois de fazer esta alteração de configuração, crie uma nova implementação, seguindo os passos descritos na secção [Criar uma Implementação de Atualização](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) e selecione a coleção do Configuration Manager adequada representada como um grupo de computadores do OMS na lista pendente. 

