---
title: ALM no Azure Machine Learning | Microsoft Docs
description: "Aplique as melhores práticas de gestão de ciclo de vida de aplicação no Azure Machine Learning Studio"
keywords: "Controlo de versão do Azure ML, gestão de ciclo de vida de aplicações, ALM, AML,"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: haining
ms.openlocfilehash: 9d1fcc761115c64fafb811d6ca1c2389babfdc15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Gestão de ciclo de vida de aplicação no Azure Machine Learning Studio
Azure Machine Learning Studio é uma ferramenta para o desenvolvimento de experimentações de machine learning que são operacionalizadas na plataforma em nuvem do Azure. Este é, como o IDE do Visual Studio e o serviço em nuvem dimensionáveis intercalado com uma plataforma única. Pode incorporar padrão práticas de gestão de ciclo de vida de aplicação (ALM), do controlo de versões de vários recursos para execução automática e a implementação, Azure Machine Learning Studio. Este artigo descreve algumas das opções e abordagens.

## <a name="versioning-experiment"></a>Experimentação de controlo de versões
Existem duas formas recomendadas para a versão das suas experimentações. Pode confiar no histórico de execução incorporado, ou exportar a experimentação no formato de JavaScript Object Notation (JSON) e geri-lo externamente. Cada abordagem é fornecido com o respetivo os profissionais de TI e contras.

### <a name="experiment-snapshots-using-run-history"></a>Utilizar o histórico de execuções de instantâneos de experimentação
No modelo de execução do Azure Machine Learning Studio learning experimentação, sempre que clica o **executar** botão no editor de experimentação, um instantâneo imutável da experimentação é submetido para o Programador de tarefas. Pode ver esta lista de instantâneos clicando a **histórico de execuções** botão na barra de comandos na vista de editor de experimentação.

![Botão de histórico de execução](./media/version-control/runhistory.png)

Pode, em seguida, abra o instantâneo no modo de está bloqueado ao clicar no nome da experimentação no momento que a experimentação foi submetida para executar e o instantâneo foi tirada. Tenha em atenção que apenas o primeiro item na lista, que representa a experimentação atual, se encontra num estado editável. Também tenha em atenção que cada instantâneo pode estar em estado de vários Estados bem, incluindo concluído (parcial executar) falhou, com falhas (parcial executar), ou de rascunho.

![Lista de histórico de execução](./media/version-control/runhistorylist.png)

Depois de o abrir, pode guardar a experimentação de instantâneo como uma nova experimentação e depois modificá-lo. Se o instantâneo de experimentação contém recursos, tais como um modelo preparado, transformação ou conjunto de dados que tenham atualizado de versões, o instantâneo mantém as referências para a versão original quando o instantâneo foi tirado. Se guardar o instantâneo bloqueado como uma nova experimentação, o Azure Machine Learning Studio detetar a existência de uma versão mais recente estes recursos e atualiza automaticamente na nova experiência.

Se eliminar a experimentação, são eliminados todos os instantâneos dessa experiência.

### <a name="exportimport-experiment-in-json-format"></a>Experimentação de exportação/importação no formato JSON
Os instantâneos do histórico de execução mantenha uma versão imutável de experimentação no Azure Machine Learning Studio sempre que é submetida para ser executada. Pode também guardar uma cópia local da experimentação e verificá-lo no seu sistema de controlo de origem Favoritos, tais como o Team Foundation Server e mais tarde voltar a criar uma experimentação de que o ficheiro local. Pode utilizar o [PowerShell do Azure Machine Learning](http://aka.ms/amlps) mini-comandos [ *exportação AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [ *importação AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) fazer.

O ficheiro JSON é uma representação textual do gráfico experimentação, que pode incluir uma referência a recursos na área de trabalho, tais como um conjunto de dados ou um modelo preparado. Não contém uma versão serializada do elemento. Se tentar importar o documento JSON voltar para a área de trabalho, os ativos referenciados tem de existir com o mesmo elemento IDs referenciados na experimentação. Caso contrário, não será capaz de aceder a experimentação importada.

## <a name="versioning-trained-model"></a>Modelo treinado do controlo de versões
Um modelo preparado no Azure Machine Learning está a ser serializado para um formato ao conhecido como um ficheiro de .iLearner e for armazenado na conta de armazenamento de Blobs do Azure associada à área de trabalho. Uma forma para obter uma cópia do ficheiro .iLearner é através da API reparametrização. [Este artigo](retrain-models-programmatically.md) explica como funciona a reparametrização API. Os passos de alto nível:

1. Configure a sua experimentação de preparação.
2. Adicione uma porta de saída do serviço web para o módulo preparar modelo ou o módulo que produz o modelo treinado, tais como otimizar Hyperparameter do modelo ou criar modelo de R.
3. Execute a experimentação de preparação e, em seguida, implementá-lo como um serviço de web de formação do modelo.
4. Chamar o ponto final de BES do serviço web formação e especifique o nome de ficheiro de .iLearner pretendida e a localização da conta de armazenamento de BLOBs onde será armazenado.
5. Recolher o ficheiro .iLearner produzidos após a conclusão da chamada de BES.

Outra forma de obter o ficheiro .iLearner é através do PowerShell commandlet [ *transferência AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Esta situação pode ser mais fácil se pretender obter uma cópia do ficheiro .iLearner sem a necessidade de reparametrização do modelo de programação.

Depois de ter o ficheiro de .iLearner que contém o modelo treinado, em seguida, pode utilizar a seus próprios estratégia de controlo de versões. A estratégia pode ser tão simple como aplicar um pre sufixo como uma convenção de nomenclatura e deixando apenas o ficheiro de .iLearner no armazenamento de BLOBs ou copiar/importá-lo no seu sistema de controlo de versão.

O ficheiro de .iLearner guardada, em seguida, pode ser utilizado para classificação através dos serviços web implementado.

## <a name="versioning-web-service"></a>Serviço web de controlo de versões
Pode implementar dois tipos de serviços web de uma experimentação do Azure Machine Learning. O serviço web clássico é fortemente conjugado com a experimentação, bem como a área de trabalho. O novo serviço web utiliza a arquitetura do Azure Resource Manager e já não está a ser integrado com a experimentação original ou a área de trabalho.

### <a name="classic-web-service"></a>Serviço web clássico
A versão de um serviço web clássico, pode tirar partido de construção de ponto final de serviço web. Eis um fluxo típico de:

1. Da sua experimentação preditiva, implemente um novo serviço web clássico, que contém um ponto final predefinido.
2. Criar um novo ponto final com o nome ep2, que expõe a versão atual do modelo preparado/experimentação.
3. Pode voltar atrás e atualizar a sua experimentação preditiva e o modelo treinado.
4. Implementar novamente a experimentação preditiva, que, em seguida, irá atualizar o ponto final predefinido. Mas isto não irá alterar ep2.
5. Criar um ponto de final adicional com o nome ep3, que expõe a nova versão da experimentação e o modelo treinado.
6. Volte ao passo 3, se necessário.

Ao longo do tempo, pode ter vários pontos finais criados no mesmo serviço web. Cada ponto final representa uma cópia de ponto no tempo da experimentação que contém a versão de pontos no tempo do modelo treinado. Em seguida, pode utilizar lógica externa para determinar o ponto final para chamar, que efetivamente significa selecionar uma versão do modelo treinado para a execução de classificação.

Pode também criar vários pontos finais do serviço web idênticas e, em seguida, aplicar o patch de diferentes versões do ficheiro .iLearner para o ponto final para alcançar o efeito semelhante. [Este artigo](create-models-and-endpoints-with-powershell.md) explica em detalhe mais como fazer.

### <a name="new-web-service"></a>Novo serviço web
Se criar um novo serviço web baseado no Azure Resource Manager, a construção de ponto final já não está disponível. Em vez disso, pode gerar ficheiros de definição (WSD) do serviço web, no formato JSON, da sua experimentação preditiva utilizando o [AmlWebServiceDefinitionFromExperiment exportação](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) commandlet do PowerShell, ou utilizando o [ *exportação AzureRmMlWebservice* ](https://msdn.microsoft.com/library/azure/mt767935.aspx) commandlet PowerShell a partir de um serviço web implementado baseado no Resource Manager.

Depois de ter o ficheiro WSD de exportado e a versão controlá-lo, também pode implementar o WSD como um novo serviço web um plano de serviço web diferente na região do Azure diferente. Basta certificar-se de que fornecer a configuração de conta de armazenamento adequados, bem como o novo ID de plano de serviço web Para aplicar o patch nos ficheiros de .iLearner diferente, pode modificar o ficheiro WSD e atualizar a referência de localização do modelo treinado e implementá-lo como um novo serviço web.

## <a name="automate-experiment-execution-and-deployment"></a>Automatizar a implementação e execução da experimentação
Um aspeto importante de ALM está a ser capazes de automatizar o processo de implementação da aplicação e a execução. No Azure Machine Learning, pode conseguir isto, utilizando o [módulo do PowerShell](http://aka.ms/amlps). Eis um exemplo de passos de ponto-a-ponto que são relevantes para um padrão ALM automatizada o processo de implementação/execução utilizando o [módulo do PowerShell do Azure Machine Learning Studio](http://aka.ms/amlps). Cada passo está ligado a um ou mais mini-comandos de PowerShell que pode utilizar para efetuar esse passo.

1. [Carregar um conjunto de dados](https://github.com/hning86/azuremlps#upload-amldataset).
2. Copiar uma experimentação de preparação para a área de trabalho a partir de um [área de trabalho](https://github.com/hning86/azuremlps#copy-amlexperiment) ou a partir de [galeria](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), ou [importar](https://github.com/hning86/azuremlps#import-amlexperimentgraph) um [exportado](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experimentação de disco local.
3. [Atualizar o conjunto de dados](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experimentação de preparação.
4. [Execute a experimentação de preparação](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Promover o modelo treinado](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Copiar uma experimentação preditiva](https://github.com/hning86/azuremlps#copy-amlexperiment) para a área de trabalho.
7. [Atualizar o modelo treinado](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) na experimentação preditiva.
8. [Execute a experimentação preditiva](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Implementar um serviço web](https://github.com/hning86/azuremlps#new-amlwebservice) da experimentação preditiva.
10. Testar o serviço web [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) ou [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) ponto final.

## <a name="next-steps"></a>Passos seguintes
* Transferir o [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) módulo e início ao automatizar as tarefas ALM.
* Saiba como [criar e gerir grande número de modelos de ML utilizando apenas uma única experimentação](create-models-and-endpoints-with-powershell.md) através do PowerShell e reparametrização API.
* Saiba mais sobre [implementar serviços web do Azure Machine Learning](publish-a-machine-learning-web-service.md).
