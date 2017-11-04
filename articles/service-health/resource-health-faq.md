---
title: Estado de funcionamento de recursos do Azure FAQ | Microsoft Docs
description: "Descrição geral do Estado de funcionamento de recursos do Azure"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/05/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 794117b6f383bdd1851681864e99b3c1ef077f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-faq"></a>FAQ do Estado de funcionamento do recurso do Azure
Saiba as respostas a questões recorrentes sobre o estado de funcionamento de recursos de Azure.

## <a name="what-is-azure-resource-health"></a>O que é o estado de funcionamento de recursos de Azure?
O estado de funcionamento dos recursos ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>O que o estado de funcionamento do recurso destina?
Assim que foi detetado um problema com um recurso, o estado de funcionamento do recurso pode ajudar a diagnosticar a causa raiz. Fornece ajuda a mitigar o problema e o suporte técnico quando precisar de mais ajuda com problemas de serviço do Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Verifica o estado de funcionamento são efetuadas pelo Estado de funcionamento do recurso?
Estado de funcionamento do recurso efetua várias verificações com base no [tipo de recurso](resource-health-checks-resource-types.md). Estas verificações foram concebidas para implementar três tipos de problemas: 
- Eventos não planeados, por exemplo, um reinício inesperado de anfitrião
- Eventos planeados, como atualizações do SO do anfitrião agendada
- Eventos acionados pelas ações do utilizador, por exemplo, um utilizador iniciar uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que cada um do Estado de funcionamento significa?
Existem três Estados de funcionamento de diferentes:
- Está disponível: Sabemos de quaisquer problemas conhecidos na plataforma do Azure que pode estar a afetar este recurso
- Indisponível: Estado de funcionamento do recurso detetou problemas que têm maior impacto no recurso
- Desconhecido: Estado de funcionamento do recurso não é possível determinar o estado de funcionamento de um recurso porque foi parado receber informações acerca do mesmo. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o estado desconhecido? Algo está errado os meus recursos?
O estado de funcionamento está definido para desconhecido quando o estado de funcionamento do recurso para receber informações sobre um recurso específico. Enquanto este estado não é uma indicação definitiva do Estado do recurso, nos casos em que existem problemas, pode indicar que há um problema do Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso que não está disponível?
Pode submeter um pedido de suporte no painel Estado de funcionamento do recurso. Não é necessário um contrato de suporte com a Microsoft para abrir um pedido quando o recurso não está disponível porque os eventos de plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Estado de funcionamento do recurso distinguir entre indisponibilidade cased por problemas de plataforma versus algo que posso foi?
Sim, quando um recurso não está disponível, o estado de funcionamento do recurso identifica a causa raiz, dentro de uma destas categorias: 
-   Ação iniciada pelo utilizador
-   Evento planeado 
-   Evento não planeado

No portal, ações iniciada pelo utilizador são apresentadas utilizando um ícone de notificação azul, enquanto eventos planeados e não são apresentados utilizando um ícone de aviso vermelho. São fornecidos mais detalhes no [descrição geral do Estado de funcionamento do recurso](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Pode integrar o estado de funcionamento do recurso com a minha ferramentas de monitorização?
Estado de funcionamento de recursos é um serviço concebido para ajudar a diagnosticar e atenuar problemas de serviço do Azure que afetam os seus recursos. Apesar de poder utilizar a API de estado de funcionamento de recursos através de programação obter o estado de funcionamento, recomendamos que utilize métricas para monitorizar os recursos. Quando é detetado um problema, o estado de funcionamento do recurso ajuda-o a determinar a causa de raiz e orienta-o ações para resolvê-los. Visite [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) para saber mais sobre como pode utilizar as métricas para verificar os seus recursos.

## <a name="where-do-i-find-resource-health"></a>Onde posso encontrar o estado de funcionamento do recurso?
Depois de iniciar sessão portal do Azure, existem várias formas, pode aceder ao estado de funcionamento de recursos:
- Navegue para o recurso. No painel de navegação esquerdo, selecione **estado de funcionamento de recursos**
- Aceda ao painel de monitorização do Azure.  No painel de navegação esquerdo, selecione **estado de funcionamento do recurso**.
- Abra o **ajuda + suporte** painel, selecionando o ponto de interrogação no canto superior direito do portal e, em seguida, selecionar **ajuda + suporte**. Uma vez que abre o painel, selecionar **estado de funcionamento de recursos**

Também pode utilizar a API de estado de funcionamento de recursos para obter informações sobre o estado de funcionamento dos seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>Estado de funcionamento do recurso está disponível para todos os tipos de recursos?
A lista de verificações de estado de funcionamento e tipos de recurso suportados através do Estado de funcionamento de recursos pode ser encontrada [aqui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se os meus recursos está a ser mostrada disponível, mas se considerar que não é?"
Quando a verificação do Estado de funcionamento de um recurso, imediatamente abaixo o estado de funcionamento, pode clicar em **comunicar o estado de funcionamento incorreto**. Antes de submeter o relatório, tem a opção de fornecer detalhes adicionais sobre o motivo pelo qual considerar que o atual estado de funcionamento está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Estado de funcionamento do recurso está disponível para todas as regiões do Azure? 
Estado de funcionamento do recurso está disponível em todos os geos do Azure, exceto as seguintes regiões:
- Gov (US) - Virginia
- Gov (US) - Iowa
- US DoD Leste
- US DoD Centro
- Alemanha Central
- Alemanha Nordeste
- Leste da China
- China Norte

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>Como estado de funcionamento do recurso é diferente do Dashboard de estado de funcionamento do serviço ou as notificações de serviço de portal do Azure?
As informações fornecidas pelo Estado de funcionamento do recurso são mais específicas que o que é fornecido pelo Dashboard de estado de funcionamento do serviço Azure.

Enquanto [Azure estado](https://status.azure.com) e as notificações de serviço portal informam sobre problemas de serviço que afetam a um conjunto amplo de clientes (por exemplo uma região do Azure), o estado de funcionamento do recurso expõe mais granular sobre eventos que são relevantes apenas para o recurso específico. Por exemplo, se um anfitrião inesperadamente é reiniciado, o estado de funcionamento do recurso alerta apenas esses clientes cujas máquinas virtuais estavam a ser executadas nesse anfitrião.

É importante notar que, ao fornecer-lhe visibilidade completa dos eventos afetar os recursos, o estado de funcionamento de recursos também apresenta eventos publicados no notificações de serviço e o Dashboard de estado de funcionamento do serviço.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>É necessário ativar o estado de funcionamento de recursos para cada recurso?
Não, as informações de estado de funcionamento estão disponíveis para todos os tipos de recursos disponíveis através do Estado de funcionamento do recurso. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>É necessário ativar o estado de funcionamento do recurso para a minha organização?
Não.  Estado de funcionamento de recursos do Azure está acessível no portal do Azure sem quaisquer requisitos de configuração.

## <a name="is-resource-health-available-free-of-charge"></a>É o estado de funcionamento de recursos disponíveis gratuitamente?
Sim.  Estado de funcionamento de recursos do Azure é gratuitamente.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações que fornece o estado de funcionamento do recurso?
Com base no estado de funcionamento, o estado de funcionamento do recurso fornece recomendações com o objetivo de reduzir o tempo que gasto a resolução de problemas. Para obter recursos disponíveis, encontrar o foco de recomendações sobre como resolver os clientes de problemas mais comuns. Se o recurso está indisponível devido a um evento não planeado do Azure, o foco será na prestar assistência, durante e após o processo de recuperação. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o estado de funcionamento de recursos:
-  [Descrição geral do Estado de funcionamento de recursos do Azure](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)
