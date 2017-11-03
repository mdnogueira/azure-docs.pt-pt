---
title: Compreender o limite de gastos do Azure | Microsoft Docs
description: "Descreve como funciona o Azure limite de gastos e como removê-lo"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli
ms.openlocfilehash: 9313f3bd09b24db8e33b1f89eb5aebea871605b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Compreender o limite e como removê-lo de gastos do Azure

Limite no Azure de gastos existe para impedir que os gastos durante o período de crédito. Todos os clientes novos que inscreva-se a versão de avaliação ou ofertas inclui créditos através de vários meses têm o limite de gastos ativado por predefinição. O limite de gastos é $0. Não pode ser alterada. O limite de gastos não está disponível para tipos de subscrição como subscrições Pay As You Go e planos de compromisso. Consulte o [lista completa das ofertas do Azure e a disponibilidade de limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

**Está à procura de alertas de faturação?** Consulte [configurar alertas de crédito ou de faturação para as subscrições do Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>O que acontece quando atinjo o limite de gastos?

Quando os resultados de utilização em encargos esgotar os as quantidades mensais incluídos na sua subscrição, os serviços que implementou estão desativados para o resto desse período de faturação. 

Por exemplo, quando passam todos os crédito incluído na sua subscrição, serviços em nuvem que implementou são removidos de produção e as máquinas virtuais do Azure são paradas e anular atribuídas. Os dados nas suas contas de armazenamento e bases de dados estão disponíveis de um modo só de leitura.

No início do período de faturação seguinte, se a oferta da subscrição inclui créditos através de vários meses, a subscrição será automaticamente reativada. Em seguida, pode voltar a implementar os serviços de nuvem e têm acesso total aos seus contas de armazenamento e bases de dados.

Iremos enviar notificações por e-mail quando atingiu o limite de gastos da sua subscrição. Iniciar sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions), e ver as notificações sobre subscrições atingiram o limite de gastos.

Se tiver uma versão de avaliação gratuita e atingiu o limite de gastos, pode [atualizar para pay as you go](billing-upgrade-azure-subscription.md) para remover o limite de gastos e ter a subscrição automaticamente reativada.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de contas

Pode remover o limite de gastos a qualquer momento, desde que exista um método de pagamento válido associado à sua subscrição. Para ofertas de que tem crédito através de vários meses, pode também reativar o limite de gastos no início do período da sua faturação seguinte.

Para remover o seu limite de gastos, siga estes passos:

1. Iniciar sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma subscrição.
1. Se a subscrição está desativada devido a ser atingido o limite de gastos, clique nesta notificação: "Subscrição atingiu o limite de gastos e foi desativada para evitar encargos." Caso contrário, clique em **remover limite de gastos** no **o estado da subscrição** área.
1. Selecione uma opção que seja apropriada para si.

|Opção|Efeito|
|-------|-----|
|Remover o limite de gastos indefinidamente|Remove o limite de gastos sem que se volte a ativar automaticamente no início do próximo período de cobrança.|
|Remover o limite de gastos para o período de faturação atual|Remove o limite de gastos para que se volte a ativar automaticamente no início do próximo período de cobrança.|

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Por que motivo devo remover o limite de gastos?

O limite de gastos pode impedi-lo de implementação ou utilizar determinados terceiros e serviços Microsoft. Estes são os cenários em que deve remover o limite de gastos da sua subscrição.

* Planeia implementar imagens de serviços primários como o Oracle e serviços como o Visual Studio Team Services. Este cenário faz com que a exceder o limite de gastos quase imediatamente e faz com que a subscrição desativada.
* Tem serviços que não podem ser interrompidos.
* Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Estas definições serão perdidas quando os serviços e recursos são desalocar.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Como ativar no limite de gastos após a removê-lo?

Esta funcionalidade só está disponível quando o limite de gastos indefinidamente foi removido. Altere-o para ativar automaticamente no início do período de faturação seguinte.

1. Iniciar sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolha **ativar o limite no próximo período de faturação de gastos \<data de faturação período de início\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Como definir a um limite de gastos personalizado?

Temos personalizada gaste os limites de hoje. No entanto, pode optar por [utilizar alertas de faturação para controlar o gastam](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>O limite de gastos impedir que todos os custos do Azure?

Alguns [serviços da Microsoft e de terceiros imagem corporativa serviços](billing-understand-your-azure-marketplace-charges.md) pode implicar custos sob o método de pagamento, mesmo se está definido um limite de gastos. Os exemplos incluem licenças do Visual Studio, premium do Azure Active Directory, planos de suporte e a maioria dos terceiros imagem corporativa serviços vendida através de [Azure Marketplace](https://azure.microsoft.com/marketplace/).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
