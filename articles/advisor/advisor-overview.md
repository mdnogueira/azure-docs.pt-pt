---
title: "Introdução ao Advisor do Azure | Microsoft Docs"
description: "Utilize o Advisor do Azure para otimizar as implementações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Advisor do Azure

Saiba mais sobre o Advisor do Azure e as respetivas capacidades de chaves e obtenha respostas às perguntas mais frequentes.

## <a name="what-is-advisor"></a>O que é o Advisor?
Advisor é um consultor de nuvem personalizado que o ajuda a seguir as melhores práticas para otimizar as implementações do Azure. Este analisa a telemetria de utilização e configuração do recurso e, em seguida, recomenda soluções que podem ajudar a melhorar a eficácia de custo, desempenho, elevada disponibilidade e segurança dos seus recursos Azure.

Com o Advisor, pode:
* Obter proativa, acionável e recomendações de personalizado melhores práticas. 
* Melhorar o desempenho, a segurança e a elevada disponibilidade dos seus recursos, à medida que identificar oportunidades para reduzir o Azure geral passam.
* Obtenha recomendações com ações proposto inline.

Pode aceder ao Advisor através de [portal do Azure](https://aka.ms/azureadvisordashboard). Iniciar sessão para o [portal](https://portal.azure.com), selecione **procurar**e, em seguida, desloque-se para **Azure Advisor**. O dashboard do Advisor apresenta personalizadas recomendações para a subscrição selecionada. 

As recomendações são divididas em quatro categorias: 

* **Elevada disponibilidade**: Certifique-se e melhorar a continuidade das suas aplicações empresariais vitais. Para obter mais informações, consulte [recomendações de elevada disponibilidade do Advisor](advisor-high-availability-recommendations.md).

* **Segurança**: para detetar ameaças e vulnerabilidades que possam levar a falhas de segurança. Para obter mais informações, consulte [recomendações de segurança do Advisor](advisor-security-recommendations.md).

* **Desempenho**: para melhorar a velocidade das suas aplicações. Para obter mais informações, consulte [recomendações de desempenho do Advisor](advisor-performance-recommendations.md).

* **Custo**: para otimizar e reduzir o Azure geral passam. Para obter mais informações, consulte [recomendações do Advisor custo](advisor-cost-recommendations.md).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Para obter recomendações do Assistente de acesso, primeiro tem *registar a sua subscrição* com o Advisor. Uma subscrição é registada quando um *subscrição proprietário* inicia o dashboard do Advisor e clica no **obter recomendações** botão. Este é um *operação única*. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* para uma subscrição, um grupo de recursos ou um recurso específico.

Pode clicar uma recomendação para saber mais acerca do mesmo. Também pode saber mais sobre as ações que pode efetuar para tirar partido de uma oportunidade ou resolver um problema. 

Advisor oferece recomendações com ações de inline ou ligações de documentação. Clicar uma ação de inline leva-o através de um "journey utilizador orientada" para implementá-las. Ao clicar numa ligação de documentação aponta a documentação que descreve como implementar manualmente a ação. 

Advisor as atualizações de recomendações por hora. Se não pretender tome medidas imediatas numa recomendação, pode snooze-lo durante um período de tempo especificado ou dispensá-lo. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-access-advisor"></a>Como posso aceder Advisor?
Pode aceder ao Advisor através de [portal do Azure](https://aka.ms/azureadvisordashboard). Iniciar sessão para o [portal](https://portal.azure.com), selecione **procurar**e, em seguida, desloque-se para **Azure Advisor**. O dashboard do Advisor apresenta personalizadas recomendações para a subscrição selecionada. 

Também pode ver as recomendações do Advisor através do painel de recursos da máquina virtual. Escolha uma máquina virtual e, em seguida, desloque-se para obter recomendações do assistente no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Que permissões é necessário aceder do Advisor

Para obter recomendações do Assistente de acesso, primeiro tem *registar a sua subscrição* com o Advisor. Uma subscrição é registada quando um *subscrição proprietário* inicia o dashboard do Advisor e clica no **obter recomendações** botão. Este é um *operação única*. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* para uma subscrição, um grupo de recursos ou um recurso específico.

### <a name="how-often-are-advisor-recommendations-updated"></a>Com que frequência são atualizadas obter recomendações do Assistente?

Recomendações do assistente são atualizadas por hora.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Que recursos Advisor fornecer recomendações para?

Advisor fornece recomendações para máquinas virtuais, conjuntos de disponibilidade, gateways de aplicação, serviços de aplicações, servidores SQL, bases de dados SQL e a Cache de Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Pode snooze ou ignorar uma recomendação?

Para snooze ou ignorar uma recomendação, clique o **Snooze** botão ou a ligação. Pode especificar um tempo de suspensão período ou selecione **nunca** para dispensar a recomendação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do assistente, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de custo do Assistente](advisor-cost-recommendations.md)
