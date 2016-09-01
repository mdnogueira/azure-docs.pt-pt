<properties
    pageTitle="Módulo do PowerShell para Machine Learning | Microsoft Azure"
    description="O módulo do PowerShell para Azure Machine Learning está disponível no modo de pré-visualização pública. Utilize o PowerShell para criar e gerir áreas de trabalho, experiências, serviços Web e muito mais."
    keywords="experimentação, regressão linear, algoritmos de machine learning, tutorial de machine learning, técnicas de modelação preditiva, experiência de ciência de dados"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# Módulo PowerShell para Microsoft Azure Machine Learning

O módulo do PowerShell do Azure Machine Learning é uma poderosa ferramenta que lhe permite utilizar o Windows PowerShell para gerir áreas de trabalho, experiências, conjuntos de dados, serviços Web e muito mais.

Pode ver a documentação e transferir o módulo, em conjunto com o código de origem completo, em [https://aka.ms/amlps](https://aka.ms/amlps). 

## O que é o módulo PowerShell do Machine Learning?

O módulo do PowerShell do Machine Learning é um módulo DLL baseado em .NET que lhe permite gerir totalmente as áreas de trabalho, experiências, conjuntos de dados, serviços Web e pontos finais do serviço Web a partir do Windows PowerShell. Em conjunto com o módulo, pode transferir o código de origem completo que inclui uma [camada API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) separada corretamente. Isto significa que pode referenciar esta DLL a partir do seu próprio projeto .NET e gerir o Azure Machine Learning através do código de .NET. Além disso, a DLL depende das APIs REST subjacentes das quais pode tirar partido diretamente a partir do seu cliente favorito.

## O que posso fazer com o módulo do PowerShell?

Seguem-se algumas das tarefas que pode realizar com este módulo do PowerShell. Consultar [documentação completa](https://aka.ms/amlps) destas e de muitas outras funções.

- Aprovisionar uma nova área de trabalho utilizando um certificado de gestão ([New- AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exportar e importar um ficheiro JSON que representa um gráfico de experimentação ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Executar uma experiência ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Criar um serviço Web a partir de uma experiência preditiva ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Criar um novo ponto final num serviço Web publicado ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Invocar um ponto final de serviço Web RRS e/ou BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Eis um exemplo rápido de como utilizar o PowerShell para executar uma experiência existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para um caso de utilização mais aprofundado, veja este artigo sobre como utilizar o módulo do PowerShell para automatizar uma tarefa pedida com frequência: [Criar muitos modelos do Machine Learning e pontos finais do serviço Web a partir de uma experiência com o PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## Como posso começar?

Para começar a utilizar o PowerShell do Machine Learning, transfira o [pacote da versão](https://github.com/hning86/azuremlps/releases) a partir do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). Terá de desbloquear a DLL transferida/descompactada e, em seguida, importá-la para o ambiente do PowerShell. A maioria dos cmdlets requerem que forneça o ID da área de trabalho, o token de autorização da área de trabalho e a região do Azure onde está a área de trabalho. A forma mais simples de fornecer estes dados é através de um ficheiro config.json predefinido, que está descrito em detalhe nas instruções de instalação. Como é óbvio, pode também clonar a árvore de git e modificar/compilar o código localmente utilizando o Visual Studio.

## Passos seguintes

O módulo do PowerShell irá continuar a ser melhorado e expandido durante este período de pré-visualização. Esteja atento ao [Blogue do Cortana Intelligence e do Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) para obter mais informações e notícias.


<!--HONumber=ago16_HO4-->


