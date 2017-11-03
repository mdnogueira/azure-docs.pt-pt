---
title: "Criar um serviço da Azure Search no portal do | Microsoft Docs"
description: "Aprovisione um serviço da Azure Search no portal."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 58f4eab190e40e16ed261c165ffdfc8155eeb434
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Criar um serviço da Azure Search no portal

Este artigo explica como criar ou o aprovisionar um serviço da Azure Search no portal. Para obter instruções de PowerShell, consulte [gerir da Azure Search com o PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subscrever (gratuita ou paga)

[Abrir uma conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e utilizar créditos livres para experimentar os serviços do Azure pagos. Depois de gastar créditos, mantenha a conta e continuar a utilizar os serviços do Azure gratuitos, tais como Web sites. Nunca é cobrado o seu cartão de crédito, exceto se alterar as definições e peça para ser cobrado explicitamente.

Em alternativa, [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Uma subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar para serviços pagos do Azure. 

## <a name="find-azure-search"></a>Localizar a pesquisa do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique no sinal ("+") no canto superior esquerdo.
3. Selecione **Web + móvel** > **a Azure Search**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>Nome do serviço e o ponto final do URL

Um nome de serviço faz parte do ponto final do URL em relação às quais são emitidas chamadas da API. Escreva o nome do serviço no **URL** campo. 

Requisitos de nome de serviço:
   * 2 e 60 carateres de comprimento
   * letras minúsculas, dígitos ou traços ("-")
   * Não existem traço ("-") como o primeiro 2 carateres ou o último caráter único
   * sem traços consecutivos ("-")

## <a name="select-a-subscription"></a>Selecionar uma subscrição
Se tiver mais do que uma subscrição, escolha uma também com os serviços de armazenamento de dados ou ficheiro. A pesquisa do Azure pode deteção automática armazenamento de tabelas do Azure e BLOBs, base de dados do SQL Server e base de dados do Azure Cosmos para indexação através de *indexadores*, mas apenas para serviços na mesma subscrição.

## <a name="select-a-resource-group"></a>Selecionar um grupo de recursos
Um grupo de recursos é uma coleção de serviços do Azure e os recursos utilizados em conjunto. Por exemplo, se estiver a utilizar para uma base de dados do SQL Server de índice da Azure Search, ambos os serviços devem fazer parte do mesmo grupo de recursos.

> [!TIP]
> Também eliminar um grupo de recursos elimina os serviços de dentro da mesma. Para os projetos de protótipo utilizar múltiplos serviços, colocando todas no mesmo grupo de recursos facilita limpeza depois do projeto está acima. 

## <a name="select-a-hosting-location"></a>Selecione uma localização de alojamento 
Como um serviço do Azure, a pesquisa do Azure podem ser alojada nos centros de dados em todo o mundo. Tenha em atenção que [os preços podem ser diferente](https://azure.microsoft.com/pricing/details/search/) por geografia.

## <a name="select-a-pricing-tier-sku"></a>Selecione um escalão de preço de (SKU)
[A pesquisa do Azure atualmente é oferecida na vários escalões de preços](https://azure.microsoft.com/pricing/details/search/): gratuita, básico ou padrão. Cada escalão tem o seu próprio [capacidade e limites](search-limits-quotas-capacity.md). Consulte [escolha um escalão de preço ou SKU](search-sku-tier.md) para obter orientações.

Nestas instruções, escolhemos o escalão padrão para o nosso serviço.

## <a name="create-your-service"></a>Criar o seu serviço

Lembre-se afixar o seu serviço para o dashboard para facilitar o acesso sempre que iniciar sessão.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>Dimensionar o serviço
Pode demorar alguns minutos para criar um serviço (15 minutos ou mais consoante a camada). Depois do seu serviço é aprovisionado, pode dimensionar para satisfazer as suas necessidades. Porque o escalão padrão que escolheu para o seu serviço da Azure Search, pode dimensionar o serviço em duas dimensões: réplicas e de partições. Tinha escolhido o escalão básico, só é possível adicionar réplicas. Se o serviço gratuito que aprovisionou, o dimensionamento não está disponível.

***As partições*** permitir que o serviço para armazenar e procurar mais documentos.

***Réplicas*** permitir que o serviço processar uma carga maior de consultas de pesquisa.

> [!Important]
> Tem de ter um serviço [2 réplicas para SLA só de leitura e 3 réplicas para leitura/escritam SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Aceda ao painel do serviço de pesquisa no portal do Azure.
2. No painel de navegação esquerdo, selecione **definições** > **escala**.
3. Utilize o slidebar para adicionar as réplicas ou partições.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Cada escalão tem diferentes [limites](search-limits-quotas-capacity.md) o número total de unidades de pesquisa permitido num único serviço (réplicas * partições = Total unidades de pesquisa).

## <a name="when-to-add-a-second-service"></a>Quando adicionar um serviço segundo

A grande maioria dos clientes utilizam apenas um serviço aprovisionado a uma camada que fornece o [equilíbrio de recursos com o botão direito](search-sku-tier.md). Um serviço pode alojar vários índices, sujeitos ao [os limites máximos da camada selecionar](search-capacity-planning.md), com cada índice isolado de outro. Na Azure Search, pedidos só podem ser direcionados para um índice, minimizando a possibilidade de obtenção de dados acidental ou intencional de outros índices no mesmo serviço.

Embora a maioria dos clientes utilizam apenas um serviço, redundância de serviço pode ser necessária se a requisitos operacionais de incluem o seguinte:

+ Recuperação após desastre (falha de centro de dados). A pesquisa do Azure não fornece ativação pós-falha instantânea na eventualidade de ocorrer uma falha. Para obter orientações e recomendações, consulte [Service administração](search-manage.md).
+ A investigação de modelação de vários inquilinos determinou que os serviços adicionais é a estrutura de ideal. Para obter mais informações, consulte [estrutura para vários inquilinos](search-modeling-multitenant-saas-applications.md).
+ Para aplicações implementadas globalmente, poderá necessitar de uma instância do Azure Search em várias regiões, para minimizar a latência de tráfego internacional da sua aplicação.

> [!NOTE]
> Na Azure Search, não pode segregar as cargas de trabalho de indexação e consultas; Assim, poderá nunca criar vários serviços para cargas de trabalho segregated. Um índice sempre é consultado no serviço em que foi criado (não é possível criar um índice num serviço e copiá-lo para outro).
>

Um segundo serviço não é necessário para elevada disponibilidade. Elevada disponibilidade para consultas é conseguida ao utilizar 2 ou mais réplicas no mesmo serviço. Atualizações de réplicas são sequenciais, o que significa que, pelo menos, um se encontra operacional quando uma atualização de serviço é implementada. Para obter mais informações sobre a disponibilidade, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Passos seguintes
Após o aprovisionamento de um serviço da Azure Search, estará pronto para [definir um índice](search-what-is-an-index.md) para que possa carregar e os dados de pesquisa.

Para aceder ao serviço de código ou script, forneça o URL (*nome do serviço*. search.windows.net) e uma chave. Chaves de administração conceder acesso total; chaves de consulta de conceder acesso só de leitura. Consulte [como utilizar a pesquisa do Azure no .NET](search-howto-dotnet-sdk.md) para começar a utilizar.

Consulte [compilar e consultar o índice da primeira](search-get-started-portal.md) para um tutorial rápido baseado no portal.

