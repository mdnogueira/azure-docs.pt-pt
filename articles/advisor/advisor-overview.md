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
ms.openlocfilehash: 906450f75557820bb27762707c3328b08b23cccb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Advisor do Azure

Saiba mais sobre as principais funcionalidades do Advisor do Azure e obtenha respostas às perguntas mais frequentes.

## <a name="what-is-advisor"></a>O que é o Advisor?
Advisor é um consultor de nuvem personalizado que o ajuda a seguir as melhores práticas para otimizar as implementações do Azure. Este analisa a telemetria de utilização e configuração do recurso e, em seguida, recomenda soluções que podem ajudar a melhorar a eficácia de custo, desempenho, elevada disponibilidade e segurança dos seus recursos Azure.

Com o Advisor, pode:
* Obter proativa, acionável e recomendações de personalizado melhores práticas. 
* Melhorar o desempenho, a segurança e a elevada disponibilidade dos seus recursos, à medida que identificar oportunidades para reduzir o Azure geral passam.
* Obtenha recomendações com ações proposto inline.

Pode aceder ao Advisor através de [portal do Azure](https://aka.ms/azureadvisordashboard). Iniciar sessão para o [portal](https://portal.azure.com), localize **Advisor** no menu de navegação ou procure-lo no **mais serviços** menu.

O dashboard do Advisor apresenta recomendações personalizadas para todas as subscrições.  Pode aplicar os filtros para apresentar as recomendações para as subscrições específicas e tipos de recursos.  As recomendações são divididas em quatro categorias: 

* **Elevada disponibilidade**: Certifique-se e melhorar a continuidade das suas aplicações empresariais vitais. Para obter mais informações, consulte [recomendações de elevada disponibilidade do Advisor](advisor-high-availability-recommendations.md).
* **Segurança**: para detetar ameaças e vulnerabilidades que possam levar a falhas de segurança. Para obter mais informações, consulte [recomendações de segurança do Advisor](advisor-security-recommendations.md).
* **Desempenho**: para melhorar a velocidade das suas aplicações. Para obter mais informações, consulte [recomendações de desempenho do Advisor](advisor-performance-recommendations.md).
* **Custo**: para otimizar e reduzir os gastos global do Azure. Para obter mais informações, consulte [recomendações do Advisor custo](advisor-cost-recommendations.md).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-dashboard.png)

> [!NOTE]
> Para utilizar o Advisor do Azure com uma subscrição, uma subscrição *proprietário* tem de iniciar o dashboard do Advisor.  Esta ação regista a subscrição com o Advisor.  Partir desse ponto, de qualquer subscrição *proprietário*, *contribuinte*, ou *leitor* pode aceder às recomendações do Advisor para a subscrição. 

Pode clique numa categoria para ver a lista de recomendações dentro dessa categoria e selecione uma recomendação para saber mais acerca do mesmo.  Também pode saber mais sobre as ações que pode efetuar para tirar partido de uma oportunidade ou resolver um problema.

![Categoria de recomendação do Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Selecione a ação recomendada para uma recomendação implementar a recomendação.  Uma interface simples abrirá que lhe permite implementar a recomendação ou consulte a documentação que ajuda a com a implementação.  Depois de implementar uma recomendação, pode demorar até ao dia para o Advisor reconhecer que.

Se não pretender tome medidas imediatas numa recomendação, pode snooze-lo durante um período de tempo especificado ou dispensá-lo.  Se não pretender receber recomendações para uma subscrição específica ou grupo de recursos, pode configurar o Advisor só gerar recomendações para especificado subscrições e grupos de recursos.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-access-advisor"></a>Como posso aceder Advisor?
Pode aceder ao Advisor através de [portal do Azure](https://aka.ms/azureadvisordashboard). Iniciar sessão para o [portal](https://portal.azure.com), localize **Advisor** no menu de navegação ou procure-lo no **mais serviços** menu.

Também pode ver as recomendações do Advisor através da interface de recurso de máquina virtual. Escolha uma máquina virtual e, em seguida, desloque-se para obter recomendações do assistente no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Que permissões é necessário aceder do Advisor

Para obter recomendações do Assistente para uma subscrição, tem primeiro de registar a subscrição do Advisor. Uma subscrição é registada quando uma subscrição *proprietário* inicia o dashboard do Advisor. Esta é uma operação única. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* de uma subscrição.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Que recursos Advisor fornecer recomendações para?

Advisor fornece recomendações para máquinas virtuais, conjuntos de disponibilidade, gateways de aplicação, serviços de aplicações, servidores SQL, bases de dados SQL e a Cache de Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Pode snooze ou ignorar uma recomendação?

Para snooze ou ignorar uma recomendação, clique o **Snooze** ligação. Pode especificar um tempo de suspensão período ou selecione **nunca** para dispensar a recomendação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do assistente, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de custo do Assistente](advisor-cost-recommendations.md)
