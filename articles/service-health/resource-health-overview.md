---
title: "Descrição geral de estado de funcionamento de recursos do Azure | Microsoft Docs"
description: "Descrição geral do Estado de funcionamento de recursos do Azure"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 040d58a81a9b41fe660e4276d698bf884f90bb6c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-overview"></a>Descrição geral do Estado de funcionamento de recursos do Azure
 
O estado de funcionamento dos recursos ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

Enquanto [Azure estado](https://status.azure.com) informa sobre problemas de serviço que afetam a um conjunto amplo de clientes do Azure, o estado de funcionamento do recurso disponibiliza um dashboard personalizado do Estado de funcionamento dos seus recursos. Estado de funcionamento de recursos mostra todas as vezes que os recursos foram disponíveis no passado devido a problemas de serviço do Azure. Isto torna simples de compreender se um SLA foi violado. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>O que é considerado um recurso e como funciona o estado de funcionamento do recurso decide se um recurso está em bom estado ou não?
Um recurso é uma instância de um tipo de recurso oferecido por um serviço do Azure através do Azure Resource Manager, por exemplo: uma máquina virtual, uma aplicação web ou uma base de dados do SQL Server.

Estado de funcionamento do recurso depende sinais emitidos pelos diferentes serviços do Azure para avaliar se um recurso está em bom estado ou não. Se um recurso é mau estado de funcionamento, o estado de funcionamento do recurso analisa informações adicionais para determinar a origem do problema. Também identifica as ações a Microsoft está a demorar para corrigir o problema ou que ações pode tomar para resolver a causa do problema. 

Reveja a lista completa dos tipos de recursos e verificações de estado de funcionamento [estado de funcionamento de recursos do Azure](resource-health-checks-resource-types.md) para obter detalhes adicionais sobre o modo como o estado de funcionamento é avaliado.

## <a name="health-status-provided-by-resource-health"></a>Estado de funcionamento fornecido pelo Estado de funcionamento de recursos
O estado de funcionamento de um recurso é um dos seguintes Estados:

### <a name="available"></a>Disponível
O serviço não detetou quaisquer eventos afetar o estado de funcionamento do recurso. Nos casos em que o recurso recuperou de um período de indisponibilidade não planeado durante as últimas 24 horas, verá o **recuperou recentemente** notificação.

![Máquina de virtual de disponibilidade de estado de funcionamento do recurso](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Não disponível
O serviço detetou uma plataforma em curso ou evento de plataforma não afetar o estado de funcionamento do recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Estes eventos são acionados por vários componentes da infraestrutura do Azure e incluem tanto ações agendadas, como a manutenção planeada e incidentes inesperadas, como um reinício do anfitrião não planeada.

Estado de funcionamento do recurso fornece detalhes adicionais sobre o evento, o processo de recuperação e permite-lhe contactar o suporte, mesmo se não tiver um Microsoft Active Directory suporta o contrato.

![Máquina de virtual de indisponível de estado de funcionamento do recurso devido a evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos de plataforma não
Estes eventos são acionados por ações executadas pelos utilizadores, por exemplo parar uma máquina virtual ou atingir o número máximo de ligações a uma Cache de Redis.

![Máquina de virtual de indisponível de estado de funcionamento do recurso devido a evento de plataforma não](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconhecido
Este estado de funcionamento indica que o estado de funcionamento do recurso não recebeu informações sobre este recurso durante mais de 10 minutos. Apesar deste Estado não é uma indicação definitiva o estado do recurso, é um ponto de dados importantes no processo de resolução de problemas:
* Se o recurso está em execução conforme esperado o estado do recurso será atualizado em disponível após alguns minutos.
* Se ocorrerem problemas com o recurso, o estado de funcionamento desconhecido pode sugerir que o recurso é afetado por um evento na plataforma.

![Máquina virtual do Resource health desconhecido](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>Comunicar um Estado incorreto
Se em qualquer momento considerar o atual estado de funcionamento está incorreto, pode indique clicando **comunicar o estado de funcionamento incorreto**. Nos casos em que são afetados por um problema do Azure, Aconselhamo-lo para contactar o suporte do painel de estado de funcionamento de recursos. 

![Estado de funcionamento de recursos comunicar o estado incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informação histórica
Pode aceder a 14 dias de dados históricos de estado de funcionamento, clicando em **ver histórico de** no painel de estado de funcionamento do recurso. 

![Histórico de relatórios do Estado de funcionamento do recurso](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introdução
Para abrir o estado de funcionamento de recursos para um recurso
1.  Inicie sessão no portal do Azure.
2.  Navegue para o recurso.
3.  No menu de recursos localizado no lado esquerdo, clique em **estado de funcionamento do recurso**.

![Abrir o estado de funcionamento de recursos a partir do painel de recursos](./media/resource-health-overview/from-resource-blade.png)

Pode também aceder ao estado de funcionamento do recurso clicando **mais serviços**e escrever **estado de funcionamento do recurso** na caixa de texto de filtro para abrir o **ajuda + suporte** painel. Por fim, clique em [ **estado de funcionamento do recurso**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abra o estado de funcionamento de recursos do serviço mais](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passos seguintes

Consulte estes recursos para saber mais sobre o estado de funcionamento de recursos:
-  [Tipos de recursos e o estado de funcionamento verifica-se no estado de funcionamento de recursos do Azure](resource-health-checks-resource-types.md)
-  [Perguntas mais frequentes sobre o estado de funcionamento de recursos do Azure](resource-health-faq.md)




