---
title: "Utilizar o portal de serviços Web do Azure Machine Learning | Microsoft Docs"
description: "Gerir o acesso a áreas de trabalho do Azure Machine Learning, implementar e gerir serviços de web da ML API"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: jhubbard
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: v-donglo
ms.openlocfilehash: ed9b4e2fc2dd7c795861dc8f1c0f2c6d61d26439
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gerir um serviço Web utilizando o portal de serviços Web do Azure Machine Learning
Pode gerir o Machine Learning novo e serviços Web clássicos através do portal de serviços Web do Microsoft Azure Machine Learning. Uma vez que os serviços Web clássicos e serviços Web novo são baseados em diferentes tecnologias subjacentes, tem capacidades de gestão ligeiramente diferente para cada um deles.

O portal de serviços Web Machine Learning, pode:

* Monitorize a forma como o serviço web está a ser utilizado.
* Configurar a descrição, Atualize as chaves para o web service (apenas para o novo), atualizar o seu armazenamento conta chave (apenas nova), ativar o registo e ativar ou desativar dados de exemplo.
* Elimine o serviço web.
* Criar, delete ou update faturação planos (apenas para o novo).
* Adicionar e eliminar os pontos finais (apenas clássico)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permissões para gerir o novo Gestor de recursos com base em serviços web

Novos serviços web são implementados como recursos do Azure. Como tal, tem de ter as permissões corretas para implementar e gerir novos serviços web.  Para implementar ou gerir novos serviços web tem de ser atribuída uma função de contribuinte ou de administrador na subscrição em que o serviço web é implementado. Se convidar outro utilizador para uma área de trabalho de aprendizagem, tem de atribuir-lhes uma função de administrador ou de Contribuidor na subscrição antes de poderem implementar ou gerir serviços web. 

Se o utilizador não tem as permissões corretas para aceder a recursos no portal de serviços Web do Azure Machine Learning, receberão o seguinte erro ao tentar implementar um serviço web:

*Falha na implementação de serviço Web. Esta conta não tem acesso suficiente para a subscrição do Azure que contém a área de trabalho. Para implementar um serviço Web do Azure, a mesma conta tem de ser convidada para a área de trabalho e ser-lhe concedida acesso à subscrição do Azure que contém a área de trabalho.*

Para obter mais informações sobre como criar uma área de trabalho, consulte [criar e a partilha de uma área de trabalho do Azure Machine Learning](create-workspace.md).

Para obter mais informações sobre a definição de permissões de acesso, consulte [atribuições de acesso de vista para utilizadores e grupos no portal do Azure - pré-visualização pública](../../active-directory/role-based-access-control-manage-assignments.md).


## <a name="manage-new-web-services"></a>Gerir serviços Web de novo
Para gerir os serviços Web novo:

1. Iniciar sessão para o [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal utilizando o Microsoft Azure conta – utilizar a conta que está associada a subscrição do Azure.
2. No menu, clique em **serviços Web**.

Esta ação apresenta uma lista de serviços Web implementados para a sua subscrição. 

Para gerir um serviço Web, clique em serviços Web. A página de serviços Web, pode:

* Clique no serviço de web geri-lo.
* Clique em de faturação planear para o serviço web para a atualização.
* Elimine um serviço web.
* Copie um serviço web e implementá-la noutra região.

Ao clicar um serviço web, abre a página de início rápido do serviço web. A página de início rápido do serviço web tem duas opções de menu que lhe permitem gerir o seu serviço web:

* **DASHBOARD** -permite-lhe ver utilização de serviço Web.
* **CONFIGURAR** - permite-lhe adicionar um texto descritivo, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar ou desativar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorizar a forma como o serviço web está a ser utilizado
Clique em de **DASHBOARD** separador.

A partir do dashboard, pode ver a utilização global do seu serviço Web durante um período de tempo. Pode selecionar o período de ver no menu pendente período no canto superior direito dos gráficos de utilização. O dashboard apresenta as seguintes informações:

* **Pedidos ao longo do tempo** mostra um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se ocorrerem picos de utilização.
* **Pedidos de resposta-pedido** apresenta o número total de chamadas de resposta-pedido o serviço recebeu o período de tempo selecionado e falha quantos dos mesmos.
* **Tempo médio de pedido-resposta computação** mostra uma média do tempo necessário para executar os pedidos recebidos.
* **Pedidos de batch** apresenta o número total de pedidos de Batch, o serviço recebeu o período de tempo selecionado e falha quantos dos mesmos.
* **Tarefa de latência média** mostra uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostra o número de agregação de erros que ocorreram em chamadas para o serviço web.
* **Os custos de serviços** apresenta os encargos para o plano de faturação associados ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique em de **configurar** opção do menu.

Pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web.
* **Título** permite-lhe introduzir um título para o serviço Web
* **Chaves** permite-lhe rodar as chaves de API primárias e secundárias.
* **Chave de conta de armazenamento** permite-lhe atualizar a chave da conta do storage associada com as alterações de serviço Web. 
* **Ativar dados de exemplo** permite-lhe fornecer os dados de exemplo que pode utilizar para testar o serviço de resposta-pedido. Se o serviço web que criou no Machine Learning Studio, os dados de exemplo são obtidos a partir de dados os utilizados para preparar o seu modelo. Se tiver criado o serviço através de programação, os dados são obtidos a partir de dados de exemplo fornecido como parte do pacote do JSON.

### <a name="managing-billing-plans"></a>Gerir planos de faturação
Clique em de **planos** opção de menu da página de início rápido de serviços web. Também pode clicar em plano associado com o serviço Web específico para gerir essa plano.

* **Novo** permite-lhe criar um novo plano.
* **Instância de plano de adicionar/remover** permite-lhe "horizontal" um plano existente para adicionar capacidade.
* **Atualização/mudança para versão anterior** permite-lhe "aumentar verticalmente" um plano existente para adicionar capacidade.
* **Eliminar** permite-lhe eliminar um plano.

Clique num plano para ver o dashboard. O dashboard dá-lhe instantâneo ou um plano de utilização ao longo de um período de tempo selecionado. Para selecionar o período de tempo para ver, clique em de **período** pendente no canto superior direito do dashboard. 

O dashboard de plano fornece as seguintes informações:

* **Planear Descrição** apresenta informações sobre os custos e a capacidade associadas com o plano.
* **Planear a utilização** mostra o número de transações e horas de computação que o plano foi cobradas.
* **Serviços Web** mostra o número de serviços Web que estão a utilizar este plano.
* **Principais por chamadas de serviço Web** apresenta os serviços Web superior quatro que estiver a efetuar chamadas são cobradas conforme o plano.
* **Principais, serviços Web por horas de computação** apresenta os serviços Web superior quatro que estão a utilizar os recursos de computação são cobrados conforme o plano.

## <a name="manage-classic-web-services"></a>Gerir serviços Web clássicos
> [!NOTE]
> Os procedimentos nesta secção são relevantes para gerir os serviços web clássico através do portal de serviços Web do Azure Machine Learning. Para obter informações sobre a gestão de serviços Web clássicos através de Machine Learning Studio e o portal clássico do Azure, consulte [gerir uma área de trabalho do Azure Machine Learning](manage-workspace.md).
> 
> 

Para gerir os serviços Web clássicos:

1. Iniciar sessão para o [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal utilizando o Microsoft Azure conta – utilizar a conta que está associada a subscrição do Azure.
2. No menu, clique em **serviços Web clássicos**.

Para gerir um serviço Web clássico, clique em **serviços Web clássicos**. A página de serviços Web clássicos pode:

* Clique no serviço de web para ver os pontos finais associados.
* Elimine um serviço web.

Quando gere um serviço Web clássico, gerir cada um dos pontos finais em separado. Ao clicar um serviço web na página de serviços Web, abre-se a lista de pontos finais associados ao serviço. 

Na página de ponto final de serviço Web clássico, pode adicionar e eliminar os pontos finais do serviço. Para obter mais informações sobre como adicionar pontos finais, consulte [criação de pontos finais](create-endpoint.md).

Clique dos pontos finais para abrir a página de início rápido do serviço web. Na página de início rápido, existem duas opções de menu que lhe permitem gerir o seu serviço web:

* **DASHBOARD** -permite-lhe ver utilização de serviço Web.
* **CONFIGURAR** - permite-lhe adicionar um texto descritivo, ativar e desativar o registo de erros, atualizar a chave para a conta de armazenamento associada ao serviço Web e ativar e desativar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorizar a forma como o serviço web está a ser utilizado
Clique em de **DASHBOARD** separador.

A partir do dashboard, pode ver a utilização global do seu serviço Web durante um período de tempo. Pode selecionar o período de ver no menu pendente período no canto superior direito dos gráficos de utilização. O dashboard apresenta as seguintes informações:

* **Pedidos ao longo do tempo** mostra um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se ocorrerem picos de utilização.
* **Pedidos de resposta-pedido** apresenta o número total de chamadas de resposta-pedido o serviço recebeu o período de tempo selecionado e falha quantos dos mesmos.
* **Tempo médio de pedido-resposta computação** mostra uma média do tempo necessário para executar os pedidos recebidos.
* **Pedidos de batch** apresenta o número total de pedidos de Batch, o serviço recebeu o período de tempo selecionado e falha quantos dos mesmos.
* **Tarefa de latência média** mostra uma média do tempo necessário para executar os pedidos recebidos.
* **Erros** mostra o número de agregação de erros que ocorreram em chamadas para o serviço web.
* **Os custos de serviços** apresenta os encargos para o plano de faturação associados ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web
Clique em de **configurar** opção do menu.

Pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web. A descrição é um campo obrigatório.
* **Registo** permite-lhe ativar ou desativar o registo no ponto final de erro. Para obter mais informações sobre o registo, consulte ativar [registo para serviços web do Machine Learning](web-services-logging.md).
* **Ativar dados de exemplo** permite-lhe fornecer os dados de exemplo que pode utilizar para testar o serviço de resposta-pedido. Se o serviço web que criou no Machine Learning Studio, os dados de exemplo são obtidos a partir de dados os utilizados para preparar o seu modelo. Se tiver criado o serviço através de programação, os dados são obtidos a partir de dados de exemplo fornecido como parte do pacote do JSON.


