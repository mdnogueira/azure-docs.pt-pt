---
title: "Gerir uma área de trabalho do Machine Learning | Microsoft Docs"
description: "Gerir o acesso a áreas de trabalho do Azure Machine Learning, implementar e gerir serviços de web da ML API"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2f90234bdd5c917a502d24cd16256bc11c7fbed0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Gerir uma área de trabalho do Azure Machine Learning

> [!NOTE]
> Para obter informações sobre a gestão de serviços Web no portal de serviços Web Machine Learning, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md).
> 
> 

Pode gerir áreas de trabalho do Machine Learning no portal do Azure ou no portal clássico do Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Para gerir uma área de trabalho no portal do Azure:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) utilizando uma conta de administrador de subscrição do Azure.
2. Na caixa de pesquisa na parte superior da página, introduza "machine learning áreas de trabalho" e, em seguida, selecione **áreas de trabalho do Machine Learning**.
3. Clique em da área de trabalho que pretende gerir.

Além das informações de gestão de recursos padrão e as opções disponíveis, pode:

- Vista **propriedades** - esta página apresenta as informações de área de trabalho e recursos e pode alterar o grupo de recursos e subscrição esta área de trabalho está associado.
- **Ressincronizar chaves armazenamento** -a área de trabalho mantém chaves para a conta de armazenamento. Se a conta de armazenamento alterações chaves, em seguida, pode clicar em **ressincronizar chaves** para sincronizar as chaves com área de trabalho.

Para gerir os serviços web associados a esta área de trabalho, utilize o portal de serviços Web Machine Learning. Consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md) para obter informações completas.

> [!NOTE]
> Para implementar ou gerir novos serviços web tem de ser atribuída uma função de contribuinte ou de administrador na subscrição em que o serviço web é implementado. Se convidar outro utilizador para uma área de trabalho de aprendizagem, tem de atribuir-lhes uma função de administrador ou de Contribuidor na subscrição antes de poderem implementar ou gerir serviços web. 
> 
>Para obter mais informações sobre a definição de permissões de acesso, consulte [atribuições de acesso de vista para utilizadores e grupos no portal do Azure - pré-visualização pública](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="use-the-azure-classic-portal"></a>Utilizar o portal clássico do Azure

Utilizar o portal clássico do Azure, pode gerir as áreas de trabalho do Machine Learning para:

* Monitorizar a forma como a área de trabalho está a ser utilizada
* Configurar a área de trabalho para permitir ou negar o acesso
* Gerir serviços Web criados na área de trabalho
* Eliminar a área de trabalho

Além disso, o separador dashboard fornece uma descrição geral da utilização da sua área de trabalho e uma leitura rápida informações da sua área de trabalho.  

> [!TIP]
> No Azure Machine Learning Studio, no **serviços WEB** separador, pode adicionar, atualizar ou eliminar um serviço Web do Machine Learning.
> 
> 

Para gerir uma área de trabalho no portal clássico do Azure:

1. Iniciar sessão para o [portal clássico do Azure](https://manage.windowsazure.com/) utilizando o Microsoft Azure conta – utilizar a conta que está associada a subscrição do Azure.
2. No painel de serviços do Microsoft Azure, clique em **MACHINE LEARNING**.
3. Clique em da área de trabalho que pretende gerir.

A página da área de trabalho tem três separadores:

* **DASHBOARD** -permite-lhe informações e ver a utilização de área de trabalho
* **CONFIGURAR** -permite-lhe gerir o acesso à área de trabalho
* **SERVIÇOS WEB** -permite-lhe gerir serviços Web que foram publicados desta área de trabalho

### <a name="to-monitor-how-the-workspace-is-being-used"></a>Para monitorizar a forma como a área de trabalho está a ser utilizada
Clique em de **DASHBOARD** separador.

A partir do dashboard, pode ver a utilização global da sua área de trabalho e obter uma leitura rápida de informações de área de trabalho.

* O **COMPUTAÇÃO** gráfico mostra os recursos de computação que está a ser utilizados por área de trabalho. Pode alterar a vista para apresentar relativo ou absoluto valores e pode alterar o período de tempo apresentado no gráfico.
* **Descrição geral da utilização** apresenta a ser utilizado por área de trabalho de armazenamento do Azure.
* **Leitura rápida** fornece um resumo das informações de área de trabalho e ligações útil.

> [!NOTE]
> O **início de sessão para ML Studio** ligação abre utilizando Account Microsoft tem atualmente sessão iniciada para o Machine Learning Studio. A Microsoft Account que utilizou para iniciar sessão no portal clássico do Azure para criar uma área de trabalho não tem permissão para abrir a área de trabalho automaticamente. Para abrir uma área de trabalho, têm de ser assinados para a Microsoft Account que foi definido como o proprietário da área de trabalho ou terá de receber um convite do proprietário para associar área de trabalho.
> 
> 

### <a name="to-grant-or-suspend-access-for-users"></a>Para conceder ou suspender o acesso de utilizadores
Clique em de **configurar** separador.

O separador de configuração, pode:

* Suspenda o acesso à área de trabalho do Machine Learning ao clicar em NEGAR. Os utilizadores já não será capazes de abrir a área de trabalho no Machine Learning Studio. Para restaurar o acesso, clique em permitir.

Para gerir contas adicionais que têm acesso à área de trabalho no Machine Learning Studio, clique em **início de sessão para ML Studio** no **DASHBOARD** separador (consulte a nota que precede relativos à **início de sessão para ML Studio**). Esta ação abre a área de trabalho no Machine Learning Studio. Aqui, clique em de **definições** separador e, em seguida, **utilizadores**. Pode clicar em **CONVIDAR utilizadores mais** conceder aos utilizadores acesso à área de trabalho, ou selecione um utilizador e clique em **remover**.

### <a name="to-manage-web-services-in-this-workspace"></a>Para gerir serviços web nesta área de trabalho
Clique em de **serviços WEB** separador.

Esta ação apresenta uma lista de serviços web publicados desta área de trabalho.
Para gerir um serviço web, clique no nome na lista para abrir a página de serviço Web.

Um serviço Web pode ter um ou mais pontos finais definidos.

* Pode definir mais pontos finais para além do ponto final "Predefinido". Para adicionar o ponto final, clique em **gerir pontos finais** na parte inferior do dashboard para abrir o portal de serviços Web do Azure Machine Learning.
* Para eliminar um ponto final (não é possível eliminar o ponto final "Predefinido"), clique na caixa de verificação no início da linha de ponto final e clique em **eliminar**. Esta ação remove o ponto final do serviço Web.
  
  > [!NOTE]
  > Se uma aplicação estiver a utilizar o ponto de final de serviço web quando o ponto final é eliminado, a aplicação irá receber um erro da próxima vez que tentar aceder ao serviço.
  > 
  > 

Clique no nome de um ponto final do serviço Web para abri-lo. 

A partir do dashboard, pode ver a utilização global do seu serviço Web durante um período de tempo. Pode selecionar o período de ver no menu pendente período no canto superior direito dos gráficos de utilização. O dashboard apresenta as seguintes informações:

* **Pedidos ao longo do tempo** mostra um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se ocorrerem picos de utilização.
* **Pedidos de resposta-pedido** apresenta o número total de chamadas de resposta-pedido o serviço recebeu o período de tempo selecionado e falha quantos dos mesmos.
* **Tempo médio de pedido-resposta computação** mostra uma média do tempo necessário para executar os pedidos recebidos.
* **Pedidos de batch** apresenta o número total de pedidos de Batch, o serviço recebeu o período de tempo selecionado e falha quantos dos mesmos.
* **Tarefa de latência média** mostra uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostra o número de agregação de erros que ocorreram em chamadas para o serviço web.
* **Os custos de serviços** apresenta os encargos para o plano de faturação associados ao serviço.

Da página de configuração, pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web. A descrição é um campo obrigatório.
* **Registo** permite-lhe ativar ou desativar o registo no ponto final de erro. Para obter mais informações sobre o registo, consulte ativar [registo para serviços web do Machine Learning](web-services-logging.md).
* **Ativar dados de exemplo** permite-lhe fornecer os dados de exemplo que pode utilizar para testar o serviço de resposta-pedido. Se o serviço web que criou no Machine Learning Studio, os dados de exemplo são obtidos a partir de dados os utilizados para preparar o seu modelo. Se tiver criado o serviço através de programação, os dados são obtidos a partir de dados de exemplo fornecido como parte do pacote do JSON.

