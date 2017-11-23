---
title: "Gerir os custos, utilizando a gestão de custo do Azure | Microsoft Docs"
description: "Gerir os custos ao utilizar relatórios de alocação e análise de custos e encargos de custo."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/21/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bfbcded98814500a03b2b79b0248c84f8f043dc0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="manage-costs-by-using-azure-cost-management"></a>Gerir os custos, utilizando a gestão de custo do Azure

Gerir os custos e elaborar relatórios de análise de custos no Azure custo Management Cloudyn atribuindo os custos com base nas etiquetas. O processo de atribuição de custo atribui os custos aos recursos da nuvem foram consumidos. Os custos totalmente são alocados quando todos os recursos são categorizados com etiquetas. Depois dos custos são alocados, pode fornecer análise de custos ou encargos aos seus utilizadores com dashboards e relatórios. No entanto, muitos recursos poderão ser requeira ou untaggable quando começar a utilizar o custo de gestão.

Por exemplo, poderá obter reimbursed para os custos de engenharia. Terá de conseguir mostrar a sua equipa de engenharia que precisa de uma quantidade específica, com base em custos de recursos. Pode apresentar um relatório para todos os recursos foram consumidos marcados *engenharia*.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilize etiquetas personalizadas para alocar os custos.
> * Crie relatórios de análise de custos e o estorno.

## <a name="use-custom-tags-to-allocate-costs"></a>Utilize etiquetas personalizadas para alocar os custos

Quando inicia a alocação de custo, é a primeira coisa que fizer a definir o âmbito utilizando um modelo de custo. O modelo de custo não altera os custos, distribui-lo-los. Quando cria um modelo de custo, segmentar os dados através da entidade de custo, a conta ou a subscrição e, por várias etiquetas. Etiquetas de exemplo comum podem incluir um código de faturação, o Centro de custos ou o nome do grupo. Etiquetas também ajudam a efetuar análise de custos ou estorno para outras partes da sua organização.

Para criar um modelo de alocação de custo personalizadas, selecione **custo** &gt; **custo gestão** &gt; **alocação de custo de 360°** no menu do relatório.

![Custo de 360 de alocação de seleção](./media/tutorial-manage-costs/cost-allocation-360.png)

No **custo alocação 360** página, selecione **adicionar** e, em seguida, introduza um nome e descrição para o modelo de custo. Selecione todas as contas ou contas individuais. Se pretender utilizar contas individuais, pode selecionar múltiplas contas de vários fornecedores de serviço na nuvem. Em seguida, clique em **categorização** para escolher as etiquetas de detetados categorizam os seus dados de custo. Escolha as etiquetas (categorias) que pretende incluir no seu modelo. No exemplo seguinte, o **unidade** etiquetas está selecionada.

![Categorização de modelo de exemplo de custos](./media/tutorial-manage-costs/cost-model01.png)



O exemplo mostra que 14,444 $ é não categorizados (sem etiquetas).

Em seguida, selecione **recursos não categorizados** e selecione os serviços que não alocado os custos. Em seguida, defina regras para alocar os custos.

Por exemplo, poderá demorar os custos de armazenamento do Azure e distribuir os custos igualmente para máquinas de virtuais (VMs) do Azure. Para tal, selecione o **armazenamento do Azure** serviço, selecione **proporcional ao Categorized**e, em seguida, selecione **VM do Azure**. Em seguida, selecione **criar**.

![Regra de atribuição do exemplo custo modelo para distribuição igual](./media/tutorial-manage-costs/cost-model02.png)



Um exemplo de diferentes, poderá atribuir todos os seus custos de rede do Azure para uma unidade de negócio específicas da sua organização. Para tal, selecione o **Azure/rede** serviço e, em seguida, selecione **distribuição explícita**. Em seguida, definir a percentagem de distribuição para 100 e selecionar a unidade de negócio —**G&amp;A** na imagem seguinte:

![Regra de atribuição do exemplo custo modelo para uma unidade de negócio específicas](./media/tutorial-manage-costs/cost-model03.png)



Para todos os recursos não categorizados restantes, crie regras de atribuição adicionais.

Se tiver quaisquer instâncias Amazon Web Services (AWS) reservados não atribuído, pode atribuir-lhes a categorias marcadas com **instâncias reservado**.

Para ver informações sobre as escolhas que efetuou para alocar os custos, selecione **resumo**. Para guardar as suas informações e continuar a trabalhar nas regras adicionais mais tarde, selecione **guardar como rascunho**. Ou, para guardar as suas informações e ter Cloudyn começar a processar o seu modelo de alocação de custo, selecione **guardar e ativar**.

A lista de modelos de custo mostra o novo modelo de custo com **processar estado**. Pode demorar algum tempo antes da base de dados Cloudyn é atualizado com o seu modelo de custo. Quando o processamento terminar, o estado é atualizado para **concluído**. Em seguida, pode ver dados do seu modelo de custo no relatório de análise de custo em **expandido filtros** &gt; **custo modelo**.

### <a name="category-manager"></a>Gestor de categoria

Gestor de categoria é uma ferramenta de limpeza de dados de mensagens em fila que o ajuda a intercalar os valores de várias categorias (etiquetas) para criar campanhas novas. É uma simple baseadas em regras ferramenta onde seleccione uma categoria e criar regras para intercalar os valores existentes. Por exemplo, poderá ter categorias existentes para **R&amp;D** e **dev** em que ambos representam o grupo de desenvolvimento.

No portal do Cloudyn, clique o símbolo de equipamento no canto superior direito e selecione **categoria Manager**. Para criar uma nova categoria, selecione o símbolo de adição (**+**). Introduza um nome para a categoria e, em seguida, em **chaves**, introduza as chaves de categoria que pretende incluir na nova categoria.

Quando definir uma regra, pode adicionar vários valores, com uma condição OR. Também pode efetuar algumas operações básicas de cadeia. Para ambos os casos, clique o símbolo de botão de reticências (**...** ) para a direita da **regra**.

Para definir uma nova regra, o **regras** área, crie uma nova regra. Por exemplo, introduza **dev** em **regras** e, em seguida, introduza **R&amp;D** em **ações**. Quando tiver terminado, guarde a nova categoria.

A imagem seguinte mostra um exemplo de regras criadas para uma nova categoria designada **carga de trabalho**:

![Categoria de exemplo](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Origens de tag e relatórios

Dados de etiquetas que visualiza relatórios Cloudyn origem em três locais:

- Recursos do fornecedor de nuvem APIs
- Fornecedor de nuvem APIs de faturação
- Criar manualmente as etiquetas das seguintes origens:
    - Tags de entidade Cloudyn - definido pelo utilizador dados de metadados aplicados a Cloudyn entidades
    - Gestor de categoria - uma ferramenta que cria novas etiquetas com base nas regras que são aplicadas para as etiquetas existentes de limpeza de dados

Para ver as etiquetas de fornecedor de nuvem em relatórios de custo de Cloudyn tem de criar um modelo de alocação de custo personalizado utilizando 360 de alocação de custo. Para fazê-lo, aceda a **custo** > **custo gestão** > **custo alocação 360**, selecione as etiquetas pretendidas e, em seguida, definir regras para processar requeira custos. Em seguida, crie um novo modelo de custo. Posteriormente, pode visualizar relatórios no custo de análise de alocação para a vista, filtrar e ordenar sobre as suas etiquetas de recursos do Azure.

Os sinalizadores de recursos do Azure só aparecem na **custo alocação Analysis** relatórios.

Etiquetas de faturação de fornecedor de nuvem são apresentados em todos os relatórios de custo.

Etiquetas de entidade de Cloudyn etiquetas que cria manualmente e apresentados em todos os relatórios de custo.


## <a name="create-showback-and-chargeback-reports"></a>Criar relatórios de análise de custos e encargos

O método que organizações utiliza para efetuar análise de custos e encargos varia bastante. No entanto, pode utilizar qualquer um dos dashboards e relatórios no portal do Cloudyn como base para qualquer finalidade. Pode fornecer acesso de utilizador a qualquer pessoa na sua organização para que possa visualizar os dashboards e relatórios a pedido. Análise de todos os custo relatórios suportam análise de custos, porque mostrarem os utilizadores os recursos que são consumidos. E permitem aos utilizadores explorar dados de custos ou utilização que são específicos do respetivo grupo na sua organização.

Para ver os resultados da alocação de custo, abra o relatório de análise de custos e selecione o modelo de custo que criou. Em seguida, adicione um agrupamento por uma ou mais das etiquetas selecionadas no modelo de custo.

![Relatório de análise de custos](./media/tutorial-manage-costs/cost-analysis.png)

Pode facilmente criar e guardar relatórios que foco em serviços específicos consumidos pelos grupos específicos. Por exemplo, pode ter um departamento que utiliza extensivamente VMs do Azure. Pode criar um relatório que é filtrado em VMs do Azure para mostrar os custos e consumo.

Se tiver de fornecer dados de instantâneos para outras equipas, pode exportar nenhum relatório no formato PDF ou CSV.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize etiquetas personalizadas para alocar os custos.
> * Crie relatórios de análise de custos e o estorno.



Para saber mais sobre como começar Cloudyn e utilizar as suas funcionalidades, avançar para a documentação de Cloudyn.

> [!div class="nextstepaction"]
> [Documentação de Cloudyn](https://support.cloudyn.com/hc/)
