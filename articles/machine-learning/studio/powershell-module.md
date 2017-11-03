---
title: "Módulo do PowerShell para o Machine Learning | Microsoft Docs"
description: "O módulo do PowerShell para Azure Machine Learning está disponível no modo de pré-visualização pública. Utilize o PowerShell para criar e gerir áreas de trabalho, experimentações, serviços web e muito mais."
keywords: "experimentação, regressão linear, algoritmos de machine learning, tutorial de machine learning, técnicas de modelação preditiva, experiência de ciência de dados"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: garye;haining
ms.openlocfilehash: 47d005e584b6cb87d27a77f56ff619d342481aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Módulo PowerShell para Microsoft Azure Machine Learning
O módulo do PowerShell do Azure Machine Learning é uma ferramenta poderosa, que permite-lhe utilizar o Windows PowerShell para gerir as áreas de trabalho, experimentações, conjuntos de dados, os serviços web clássico e muito mais.

Pode ver a documentação e transferir o módulo, em conjunto com o código de origem completo, em [https://aka.ms/amlps](https://aka.ms/amlps). 

> [!NOTE]
> O módulo do PowerShell do Azure Machine Learning está atualmente em modo de pré-visualização. O módulo irá continuar a ser melhorados e expandidos durante este período de pré-visualização. Manter a par o [Cortana Intelligence e o blogue do Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) para notícias e informações.

## <a name="what-is-the-machine-learning-powershell-module"></a>O que é o módulo PowerShell do Machine Learning?
O módulo do PowerShell do Machine Learning é um. Com base em NET DLL módulo que lhe permite gerir totalmente áreas de trabalho do Azure Machine Learning, experimentações, conjuntos de dados, os serviços web clássico e pontos finais do serviço de web clássico do Windows PowerShell. 

Juntamente com o módulo, pode transferir o código de origem completo que inclui um corretamente separados [camada da API c#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Pode referenciar esta DLL do seu projeto de .NET e gerir o Azure Machine Learning através de código .NET. Além disso, a DLL depende subjacente APIs REST que pode utilizar diretamente a partir do seu cliente favorito.

## <a name="what-can-i-do-with-the-powershell-module"></a>O que posso fazer com o módulo do PowerShell?
Seguem-se algumas das tarefas que pode realizar com este módulo do PowerShell. Consultar [documentação completa](https://aka.ms/amlps) destas e de muitas outras funções.

* Aprovisionar uma nova área de trabalho utilizando um certificado de gestão ([New- AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Exportar e importar um ficheiro JSON que representa um gráfico de experimentação ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Executar uma experiência ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Criar um serviço Web a partir de uma experiência preditiva ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Criar um ponto final num serviço web publicada ([adicionar AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Invocar um ponto final de serviço Web RRS e/ou BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Eis um exemplo rápido de como utilizar o PowerShell para executar uma experiência existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para um caso de utilização mais aprofundado, consulte este artigo sobre como utilizar o módulo do PowerShell para automatizar uma tarefa normalmente pedida: [criar vários modelos de Machine Learning e web pontos finais de serviço a partir de uma experimentação através do PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Como posso começar?
Para começar a utilizar o PowerShell do Machine Learning, transfira o [pacote da versão](https://github.com/hning86/azuremlps/releases) a partir do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear o DLL transferido/deszipada e, em seguida, importá-lo no seu ambiente do PowerShell. A maioria dos cmdlets requerem que forneça o ID da área de trabalho, o token de autorização da área de trabalho e a região do Azure onde está a área de trabalho. A forma mais simples de fornecer os valores é efetuada através de um ficheiro de config predefinido. As instruções também explicam como configurar este ficheiro. 

Se quiser, pode clonar a árvore de git, modificar o código e compilá-la localmente utilizando o Visual Studio.

## <a name="next-steps"></a>Passos seguintes
Pode encontrar a documentação completa para o módulo do PowerShell em [https://aka.ms/amlps](https://aka.ms/amlps). 

Para obter um exemplo expandido de como utilizar o módulo de um cenário do mundo real, veja o caso de utilização detalhada, [criar vários modelos de Machine Learning e web pontos finais de serviço a partir de uma experimentação através do PowerShell](create-models-and-endpoints-with-powershell.md).
