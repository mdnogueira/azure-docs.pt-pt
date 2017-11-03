---
title: "Guia de criação de um serviço de dados para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implementar um serviço de dados para comprar no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: c0c9362f1c2e15c947aaaf7187f3383ad243140f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Serviço de dados de publicação guia para o Azure Marketplace
> [!IMPORTANT]
> **Neste momento, estamos já não integração qualquer nova editores de serviço de dados. Novo dataservices não irá obter aprovada para listagem.** Se tiver uma aplicação de negócio de SaaS que pretende publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço do programador que pretende publicar no Azure Marketplace, pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Depois de concluir o passo 1, [criação da conta e o registo](marketplace-publishing-accounts-creation-registration.md), é guiado, através de [geral não técnico](marketplace-publishing-pre-requisites.md) e [requisitos técnicos](marketplace-publishing-data-service-creation-prerequisites.md) de uma oferta de serviço de dados no Azure Marketplace. Agora iremos irá guiá-lo através dos passos para criar uma oferta de serviço de dados no [Portal publicação] [ link-pubportal] para o Azure Marketplace.

## <a name="1----login-to-the-publishing-portal"></a>1.    Início de sessão para a publicação do Portal.
Aceda a [https://publish.windowsazure.com](https://publish.windowsazure.com.)

**Para a primeira hora no início de sessão Portal de publicação, utilize a mesma conta na qual vendedor perfil da sua empresa foi registado no Centro de programadores.**  (Mais tarde, pode adicionar qualquer empregado da sua empresa como coadministrador no Portal de publicação).

Clique em de **publicar dos serviços de dados** mosaico se este for o primeiro início de sessão no portal de publicação.

## <a name="2----choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.    Escolha **serviços de dados** no menu de navegação no lado esquerdo.
  ![desenho](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3----create-a-new-data-service"></a>3.    Criar um novo serviço de dados
Preencha o título para a oferta de serviço de dados nova e clique em "+" à direita.

  ![desenho](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4----review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.    Reveja o submenu sob o serviço de dados recentemente criado no menu de navegação.
Clique em de **instruções** separador e rever todos os passos necessários necessários para publicar corretamente o serviço de dados no Azure Marketplace.

> [!TIP]
> Sempre pode clicar em hiperligações na página "Instruções" ou separadores no submenu a oferta de serviço de dados no lado esquerdo.
> 
> 

## <a name="5----create-a-new-plan"></a>5.    Crie um novo plano.
### <a name="offers-plans-transactions"></a>Oferece, esquemas, as transações.
Cada oferta pode ter vários esquemas, mas tem de ter, pelo menos, um (1) o plano. Quando os utilizadores finais subscrevem oferta podem subscrever para um plano a oferta. Cada plano define como os utilizadores finais serão capazes de utilizar o seu serviço.

Azure Marketplace suporta atualmente mensal subscrição transação baseada em modelo apenas para serviços de dados, ou seja, os utilizadores finais serão pagar mensal, de acordo com o preço do plano específico que subscreveram e serão capazes de consumir por cada número do mês de transação definida pelo plano.

Cada transação, normalmente, definida como número de registos que irá devolver o seu serviço de dados com base na consulta enviada para o serviço. A predefinição é 100. Número de transações devolvidos para cada consulta irá ser um número de registos divididos por 100 e arredondados até o número inteiro mais próximo.

É responsabilidade de camada de serviço do Azure Marketplace para monitorizar o número de (medição) de transações consumidos por cada consulta.

> [!IMPORTANT]
> Os utilizadores finais que atingiu o limite de transação durante o mês serão impedidos de continuar a utilizar o serviço até fim do respetivo ciclo de subscrição mensal.
> 
> O plano ou um dos planos pode (mas não tem) incluem um número ilimitado de transações.
> 
> 

### <a name="create-a-plan"></a>Crie um plano.
1. Clique em **"+"** junto a "Adicionar um novo plano".
2. Escolha uma das opções: **ilimitada** ou **limitado** utilização para este plano.  Se limitado, em seguida, forneça o número de transações irá permitir que o plano de consumir num mês.
   
    ![desenho](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    Portal de publicação será também sugerir "Identificador do plano", que será utilizada para comunicar com os utilizadores finais, o nome do plano na IU e também utilizada pelo serviço de colocação no mercado local para identificar o plano. Pode alterar o "identificador planear" se quiser.
   
   > [!NOTE]
   > "Identificador planear" tem de ser exclusivo no âmbito de cada oferta. Como muitos outros identificadores utilizado no identificador de planear a publicação Portal será bloqueado após a primeiro a publicação de produção e não poderá alterar este identificador.
   > 
   > 
3. Clique para aceitar a escolha.
4. Em seguida, será pedido algumas questões adicionais sobre o seu plano recentemente criado.
   
    ![desenho](media/marketplace-publishing-data-service-creation/step-5.2.png)

| Pergunta | Significância |
| --- | --- |
| **Este plano é toda a mundo livre e disponível?** |Pode criar um plano de completamente de gratuito. Se for o único plano nesta oferta – significa que está a publicar "Oferecem livre" no Marketplace. Se estiver apenas para um (poucos) plano, o it dá-lhe uma opção para oferecer os utilizadores finais para saber mais sobre o serviço com um número relativamente pequeno de transações por mês.  Se a resposta é "Sim", não serão pedidas a nenhum mais perguntas. |

> [!NOTE]
> Os utilizadores finais sempre pode atualizar para os planos pagos.
> 
> 

| Pergunta | Significância |
| --- | --- |
| **É a versão de avaliação gratuita disponível?** |Pode escolher entre "Sem versão de avaliação" de todo ou atribua uma opção para utilizar o seu plano de "Um mês". Os publicadores, como utilizar esta opção para fornecer aos utilizadores finais a possibilidade de compreender as vantagens da oferta gratuitamente durante um mês. |

> [!IMPORTANT]
> Os utilizadores finais apenas poderão comprar uma versão de avaliação gratuita se estes tenham estabelecido instrumento de pagamento por exemplo, cartão de crédito, contrato enterprise.
> 
> Após um mês da versão de avaliação gratuita, Azure Marketplace iniciará charging clientes o preço a partir da data da subscrição, a menos que o cliente iniciou o cancelamento da subscrição. Nenhuma notificação especial será fornecida aos utilizadores finais.
> 
> 

| Pergunta | Significância |
| --- | --- |
| **Este plano requer um código de promoção para comprar?** |Os publicadores tem uma opção para limitar o acesso aos respetivos planos de serviço ao fornecer um código especial, denominado "A Promocode" a clientes específicos. Apenas os utilizadores finais que terão este Promocode poderá subscrever o plano. Se selecionar "Não", em seguida, concorda com que todas as pessoas a região onde está disponível a oferta (consulte [guia conteúdo de Marketing Marketplace](marketplace-publishing-push-to-staging.md) para obter mais detalhes) poderá subscrever este plano. Não existem mais perguntas serão pedidas. |
| **Também ocultariam este plano de qualquer pessoa que não tem um código de promoção válido?** |Se a resposta à pergunta anterior é "Sim" o publicador tem uma opção para remover completamente este plano de apresentação na IU do Marketplace. Significa, os clientes não irão ver este plano na página de detalhes a oferta. Os utilizadores finais que irão receber um promocode compre, poderá subscrevê-lo utilizando esta promocode. |

## <a name="6----create-your-marketplace-marketing-content"></a>6.    Criar o Marketplace conteúdo de marketing
Para saber como fornecer informações necessárias na **Marketing, preço, suporte e categorias** separadores visitam [guia conteúdo de Marketing Marketplace](marketplace-publishing-push-to-staging.md) que são comuns a todos os artefactos publicados no Azure Marketplace.  

## <a name="7----connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.    Ligar a sua oferta ao seu serviço (SQL Azure com base ou serviço Web baseado em).
Clique em de **serviços de dados** submenu.

Na metade superior da página, será solicitado que forneça a oferta **espaço de nomes**.  

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.png)

O abaixo pergunta irá definir como publicador é vai expõe recém-criado oferta no Azure Marketplace. (Para obter mais detalhes, consulte o [dados serviços técnico guia pré-requisitos](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.2.png)

**O serviço de base de dados com base de publicação**

Clique em **base de dados**. Será apresentada a seguinte página:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.3.png)

Para criar um mapeamento de CSDL para o conjunto de dados com base do BD SQL do Azure:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.4.png)

E, em seguida, para cada tabela

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.6.png)

Se serviço Web

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> Leitura [mapeamento existente web do serviço OData através de CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) para instruções detalhadas e exemplos de criação de um serviço Web de CSDL.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Agora que criou a oferta de serviço de dados, certifique-se que conclua as instruções de [guia conteúdo de Marketing Marketplace](marketplace-publishing-push-to-staging.md) antes de avançar para o [testar o seu serviço de dados no modo de transição](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Veja Também
* [Introdução: Como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* Se estiver interessado em compreender o processo de mapeamento de OData e o objetivo geral, leia este artigo [mapeamento de OData do serviço de dados](marketplace-publishing-data-service-creation-odata-mapping.md) para rever as definições, estruturas e instruções.
* Se estiver interessado em aprender e compreender os nós específicos e os respetivos parâmetros, leia este artigo [dados serviço OData mapeamento de nós](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definições e uma explicação, exemplos e contexto de cenários de utilização.
* Se estiver interessado em rever exemplos, leia este artigo [dados serviço OData mapeamento exemplos](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver o código de exemplo e compreender a sintaxe de código e o contexto.

[link-pubportal]:https://publish.windowsazure.com
