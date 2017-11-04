---
title: Novidades no Azure Machine Learning | Microsoft Docs
description: "Novas funcionalidades que estão disponíveis no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 1e3dc7e1375488ae9473cdd26f4d00871a091fbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-azure-machine-learning"></a>Novidades do Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>A versão de 2017 de Março de atualizações do Microsoft Azure Machine Learning fornece a funcionalidade seguinte:



* Capacidade dedicada para o Azure Machine Learning BES tarefas

    Machine Learning conjunto do Batch processamento utiliza o [do Azure Batch](../../batch/batch-technical-overview.md) serviço para fornecer a escala gerida pelo cliente para o serviço de execução de lote do Azure Machine Learning. Processamento de conjunto do batch permite-lhe criar conjuntos do Azure Batch no qual pode submeter tarefas de lote e os executar de forma previsível.

    Para obter mais informações, consulte [serviço Azure Batch para tarefas de Machine Learning](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>A versão de Agosto de 2016 das atualizações do Microsoft Azure Machine Learning fornece as seguintes funcionalidades:
* Serviços Web clássicos agora podem ser geridos no novo [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/) portal que fornece um local para gerir todos os aspetos do seu serviço Web.    
  * Que fornece o serviço web [estatísticas de utilização](manage-new-webservice.md).
  * Simplifica o teste de chamadas do Azure Machine Learning remoto-pedido com dados de exemplo.
  * Fornece uma nova página de teste do serviço de execução do Batch com o histórico de submissão de dados e a tarefa de exemplo.
  * Fornece uma gestão mais fácil do ponto final.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>A versão de Julho de 2016 das atualizações do Microsoft Azure Machine Learning fornece as seguintes funcionalidades:
* Serviços Web são agora geridos como geridos através de recursos do Azure [do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) interfaces, permitindo os seguintes melhoramentos:
  * Existem novas [REST APIs](https://msdn.microsoft.com/library/azure/Dn950030.aspx) implementar e gerir o seu Gestor de recursos baseados em serviços Web.
  * Há uma nova [serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/) portal que fornece um local para gerir todos os aspetos do seu serviço Web.
* Incorpora um novo modelo de implementação de serviço web com base na subscrição e região multi utilizando o Gestor de recursos com base em APIs tirar partido do fornecedor de recursos do Resource Manager para serviços Web.
* Apresenta novos [planos de preços](https://azure.microsoft.com/pricing/details/machine-learning/) e planeie as capacidades de gestão utilizando o novo RP do Gestor de recursos de faturação.
  * Agora, pode [implementar o seu serviço web em várias regiões](how-to-deploy-to-multiple-regions.md) sem necessidade de criar uma subscrição em cada região.
* Fornece o serviço web [estatísticas de utilização](manage-new-webservice.md).
* Simplifica o teste de chamadas do Azure Machine Learning remoto-pedido com dados de exemplo.
* Fornece uma nova página de teste do serviço de execução do Batch com o histórico de submissão de dados e a tarefa de exemplo.

Além disso, o Machine Learning Studio foi atualizada para permitir a implementação para o novo modelo de serviço Web ou continuar a implementar o modelo de serviço Web clássico. 

