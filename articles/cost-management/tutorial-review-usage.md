---
title: "Reveja os custos na gestão de custo do Azure e de utilização | Microsoft Docs"
description: "Reveja os custos para controlar as tendências, detetar inefficiencies e criar alertas e de utilização."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 36ebffb41211e443cc1619df46f50247945cc57c
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="review-usage-and-costs"></a>Os custos e utilização de revisão

Gestão de custo do Azure por Cloudyn mostra-lhe utilização e custos de modo a que pode monitorizar tendências, detetar inefficiencies e criam alertas. Todos os dados de utilização e o custo, é apresentado nos relatórios e Cloudyn dashboards. Os exemplos neste tutorial guiá-lo embora rever os custos com dashboards e relatórios e de utilização. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Controlar a utilização e as tendências de custo
> * Detetar inefficiencies de utilização
> * Crie alertas para as despesas ou overspending invulgar



## <a name="open-the-cloudyn-portal"></a>Abra o portal de Cloudyn

Reveja os custos no portal do Cloudyn e utilização de todas as. Abra o portal de Cloudyn do portal do Azure ou navegue para https://app.cloudyn.com e inicie sessão.

## <a name="track-usage-and-cost-trends"></a>Controlar a utilização e as tendências de custo

Controlar dinheiro real despendido para utilização e os custos com relatórios ao longo do tempo para identificar tendências. Para começar a procurar tendências, utilize o relatório de custo real ao longo do tempo. No menu de relatórios na parte superior do portal, clique em **custo** > **análise de custos** > **custo real ao longo do tempo**. Quando abre pela primeira vez o relatório, nenhum grupo ou filtros são aplicados ao mesmo.

Segue-se um relatório de exemplo:

![relatório de exemplo](./media/tutorial-review-usage/actual-cost01.png)

O relatório mostra todos os gastos nos últimos 30 dias. Para ver os gastos apenas para serviços do Azure, aplicam-se o grupo de serviço e, em seguida, filtre para todos os serviços do Azure. A imagem seguinte mostra os serviços filtrados.

![serviços filtrados](./media/tutorial-review-usage/actual-cost02.png)

No exemplo anterior, menos dinheiro era despendido a iniciar no 2017-08-31 que antes. Tendência do custo continua para os serviços sobre nove dias. Em seguida, gastos adicionais continua como anteriormente. No entanto, existem demasiadas colunas podem ocultar uma tendência óbvias. Pode alterar a vista de relatório para uma linha ou a área de gráfico para ver os dados apresentados no outras vistas. A imagem seguinte mostra a tendência mais claramente.

![tendência de relatório](./media/tutorial-review-usage/actual-cost03.png)

No exemplo, verá claramente que o armazenamento do Azure custo ignorados ao iniciar no 2017-08-31 enquanto gastos com outros serviços do Azure permanecer nível. Por isso, as causas que redução no gastos? Neste exemplo, alguns empregados são foram de férias longe do trabalho e não foi possível utilizar o serviço de armazenamento.

Para ver um vídeo tutorial sobre tendências de utilização e o custo de controlo, consulte o artigo [análise da sua nuvem faturação dados vs. o período de tempo com o Azure custo Management Cloudyn](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Detetar inefficiencies de utilização

Relatórios de otimizador melhorar a eficiência, otimizar a utilização e identificam formas de poupar dinheiro despendido nos seus recursos de nuvem. São especialmente útil com recomendações de dimensionamento económicos que se destina a ajudar a reduzir as VMs inativas ou dispendiosas.

Um problema comum, que afeta as organizações quando estes inicialmente mover recursos para a nuvem é a sua estratégia de virtualização. Geralmente utilizam uma abordagem semelhante à que utilizou para criar máquinas virtuais para o ambiente de virtualização no local. E, em partem do princípio de que os custos são reduzidos, movendo as respetivas VMs no local para a nuvem, como-é. No entanto, essa abordagem não é provável que reduzir os custos.

O problema é que a respetiva infraestrutura existente já foi paga para. Os utilizadores foi possível criar e manter grande VMs em execução se de que gostou — inativo ou não e com pouca consequence. Mover VMs de grandes dimensões ou inativas para a nuvem, é provável que *aumentar* os custos. Custo de alocação de recursos é importante quando introduzir para contratos com fornecedores de serviços em nuvem. Tem de pagar para confirmar se utilizar o recurso totalmente ou não.

O relatório de recomendações de dimensionamento de custo eficaz identifica potenciais reduções anuais comparando a capacidade de tipo de instância VM para os respetivos histórica da CPU e dados de utilização de memória.  

No menu de relatórios na parte superior do portal, clique em **otimizador** > **preços otimização** > **recomendações de dimensionamento de custo eficaz**. Filtre o fornecedor do Azure para ver apenas as VMs do Azure. Eis uma imagem de exemplo.

![VMs do Azure](./media/tutorial-review-usage/sizing01.png)

Neste exemplo, $3,114 foi possível guardar ao seguir as recomendações para alterar os tipos de instância VM. Clique no símbolo de adição (+) em **detalhes** para a primeira recomendação. Seguem-se detalhes sobre a recomendação primeiro.

![detalhes de recomendação](./media/tutorial-review-usage/sizing02.png)

Ver os IDs de instância VM ao clicar junto ao símbolo de adição **lista de candidatos**.

![Lista de candidatos](./media/tutorial-review-usage/sizing03.png)

Para ver um vídeo tutorial sobre como detetar inefficiencies de utilização, consulte o artigo [otimizar o tamanho da VM no Azure custo Management Cloudyn](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Criar alertas para a dedicar muito invulgar

Podem alertá intervenientes automaticamente para anomalias de gastos e overspending riscos. Pode criar alertas de utilização de relatórios que suporte alertas com base na atribuição e o custo de limiares de forma rápida e fácil.

Criar um alerta para qualquer gastos utilizando o relatório qualquer custo. Neste exemplo, utilize o relatório de custo real ao longo do tempo para ser notificado quando gastos de VM do Azure está prestes a sua atribuição total. No menu de relatórios na parte superior do portal, clique em **custo** > **análise de custos** > **custo real ao longo do tempo**. Definir **grupos** para **serviço** e defina **filtro no serviço** para **VM do Azure**. Na parte superior direita do relatório, clique em **ações** e, em seguida, selecione **agendar relatórios**.

Utilize o **agendamento** separador para si próprio enviar uma mensagem de e-mail do relatório utilizando a frequência com que pretende. As etiquetas, agrupamentos e filtragens é utilizado estão incluídas no relatório enviado por e-mail. Clique em de **limiar** separador e selecione escolha **vs custo real. Limiar**. Se tiver uma atribuição total de $ 500 000 e notificação que desejava quando os custos perto sobre metade, crie um **alerta vermelho** em $250,000 e um **alerta amarela** em $240,000. Em seguida, escolha o número de alertas consecutivos. Quando receber o número total de alertas que especificou, não existem alertas adicionais são enviadas. Guarde o relatório agendado.

![relatório de exemplo](./media/tutorial-review-usage/schedule-alert01.png)

Também pode escolher os vs de percentagem de custo. Métrica de limiar de orçamento criar alertas. Ao utilizar esse métrica, pode utilizar percentagens de orçamento em vez de valores de moeda.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Controlar a utilização e as tendências de custo
> * Detetar inefficiencies de utilização
> * Crie alertas para as despesas ou overspending invulgar


Avançar para o próximo tutorial para saber mais sobre como controlar o acesso a dados.

> [!div class="nextstepaction"]
> [Controlar o acesso a dados](tutorial-user-access.md)
