---
title: "Migração da plataforma do centro de segurança do Azure | Microsoft Docs"
description: "Este documento explica algumas alterações na forma como os dados do centro de segurança do Azure são recolhidos."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-platform-migration"></a>Migração da plataforma do centro de segurança do Azure

No início de junho de 2017, o centro de segurança do Azure efetua alterações importantes na forma como os dados de segurança são recolhidos e armazenados.  Essas alterações desbloqueiam novas capacidades como a capacidade de pesquisar facilmente dados de segurança e apresentam uma maior sintonia com os serviços de gestão e monitorização do Azure.

> [!NOTE]
> A migração da plataforma não deve afetar os recursos de produção e não é necessária qualquer alteração da sua parte.


## <a name="whats-happening-during-this-platform-migration"></a>O que acontece durante a migração da plataforma?

Anteriormente, o centro de segurança utilizava o agente de monitorização do Azure para recolher dados de segurança de suas VM. Isso inclui informações sobre as configurações de segurança, que são usadas para identificar vulnerabilidades, e eventos de segurança, que são usados para detetar ameaças. Esses dados estão armazenados na(s) sua(s) conta(s) de armazenamento no Azure.

O centro de segurança utiliza o agente de monitorização da Microsoft – é o mesmo agente utilizado pelos serviços Operations Management Suite e Log Analytics. Os dados recolhidos por este agente são armazenados na *área de trabalho* do [Log Analytics](../log-analytics/log-analytics-manage-access.md) existente, associada à sua subscrição do Azure ou nas novas áreas de trabalho, tendo em conta a geolocalização da VM.

## <a name="agent"></a>Agente

Como parte da transição, o agente de monitorização da Microsoft (para [Windows](../log-analytics/log-analytics-windows-agents.md) ou [Linux](../log-analytics/log-analytics-linux-agents.md)) é instalado em todas as VM do Azure, nas quais os dados estão a ser recolhidos no momento.  Se a VM já tiver instalado o agente de monitorização da Microsoft, o centro de segurança irá tirar partido do agente atualmente instalado.

Durante um período de tempo (normalmente de alguns dias), os dois agentes serão executados lado a lado para garantir uma transição suave e sem qualquer perda de dados. Isto permitirá que a Microsoft confirme se o novo pipeline de dados está a funcionar antes de interromper a utilização do pipeline atual. Depois dessa confirmação, o agente de monitorização do Azure será removido das suas VM. Não é necessário qualquer trabalho da sua parte. Receberá um e-mail quando todos os clientes tiverem sido migrados.
 
Não é recomendável desinstalar manualmente o agente de monitorização do Azure durante a migração, pois podem existir lacunas nos dados de segurança. Consulte o [suporte e serviço de apoio ao cliente da Microsoft](https://support.microsoft.com/contactus/) se precisar de mais assistência. 

O agente de monitorização da Microsoft para Windows requer a utilização da porta TCP 443. Para mais informações, leia o [guia de resolução de problemas do centro de segurança do Azure](security-center-troubleshooting-guide.md).


> [!NOTE] 
> Uma vez que o agente de monitorização da Microsoft pode ser utilizado por outros serviços de gestão e monitorização do Azure, o agente não será desinstalado automaticamente quando desligar a recolha de dados no centro de segurança. No entanto, pode desinstalar manualmente o agente, se necessário.

## <a name="workspace"></a>Área de trabalho

Tal como descrito anteriormente, os dados recolhidos pelo agente de monitorização da Microsoft (em nome do centro de segurança do Azure) serão armazenados nas áreas de trabalho do Log Analytics existentes, associadas à sua subscrição do Azure ou a novas áreas de trabalho, tendo em conta a geolocalização da VM.

No portal do Azure, pode navegar para ver uma lista das suas áreas de trabalho do Log Analytics, incluindo aquelas criadas pelo centro de segurança. Será criado um grupo de recursos relacionados para as novas áreas de trabalho. Ambos seguem a convenção de nomenclatura:

- Área de trabalho: *DefaultWorkspace-[subscrição-ID]-[geo]*
- Grupo de Recursos: *DefaultResouceGroup-[geo]* 
 
Para áreas de trabalho criadas pelo centro de segurança do Azure, os dados são retidos durante 30 dias. Para as áreas de trabalho existentes, a retenção baseia-se no escalão de preço da área de trabalho.

> [!NOTE]
> Os dados recolhidos anteriormente pelo centro de segurança permanecem na(s) sua(s) conta(s) de armazenamento. Após a conclusão da migração, pode eliminar essas contas de armazenamento.

### <a name="oms-security-solution"></a>Solução de segurança do OMS 

Para clientes existentes que não têm a solução de segurança do OMS instalada, a Microsoft instala-a na sua área de trabalho, mas apenas em VM do Azure. Não desinstale essa solução, pois não há nenhuma correção automática se tal for efetuado na consola de gestão do OMS.


## <a name="other-updates"></a>Outras atualizações

Juntamente com a migração da plataforma, estamos a proceder a algumas pequenas atualizações:

- São suportadas outras versões do SO. Consulte a lista [aqui](security-center-faq.md#virtual-machines).
- A lista das vulnerabilidades do SO será expandida. Consulte a lista [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Os [preços](https://azure.microsoft.com/pricing/details/security-center/) serão calculados por hora (anteriormente era por dia), resultando em poupança de custos para alguns clientes.
- A recolha de dados é obrigatória e é automaticamente ativada para os clientes no escalão de preços normal.
- O centro de segurança do Azure começará a descobrir soluções antimalware que não foram implementadas com as extensões do Azure. A descoberta do Symantec Endpoint Protection e do Defender para Windows 2016 estará disponível pela primeira vez.
- As políticas de prevenção e as notificações só são configuráveis no nível *Subscrição*, mas o preço continua a poder ser definido no nível *Grupo de Recursos*.

