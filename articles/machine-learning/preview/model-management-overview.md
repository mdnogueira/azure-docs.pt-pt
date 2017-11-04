---
title: "Descrição geral concetual de gestão de modelo do Azure Machine Learning | Microsoft Docs"
description: "Este documento explica os conceitos de gestão de modelo do Azure Machine Learning."
services: machine-learning
author: nk773
ms.author: neerajkh, padou
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 31b859d86e82c92839462280721c5f84f1d923cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-management"></a>Gestão de Modelos do Azure Machine Learning

O Azure Machine Learning modelo Management permite-lhe gerir e implementar os fluxos de trabalho e modelos de machine learning. 

Gestão de modelo fornece capacidades para:
- Controlo de versões do modelo
- Modelos de controlo na produção
- Implementação de modelos para produção através do ambiente de computação do AzureML com [serviço de contentor Azure](https://azure.microsoft.com/services/container-service/) e [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Criar contentores de Docker com os modelos e testá-los localmente
- Reparametrização modelo automatizado
- Captura de telemetria de modelo para conhecimentos acionáveis. 

O Azure Machine Learning modelo Management fornece um registo das versões do modelo. Também permite fluxos de trabalho automatizados empacotamento e implementação de contentores de Machine Learning como REST APIs. Os modelos e as respetivas dependências de tempo de execução são reunidas no contentor de Docker baseado em Linux com a API de predição. 

Azure Machine Learning computação ambientes ajudam a configurar e gerir clusters escaláveis para alojar os modelos. O ambiente de computação baseia-se nos serviços de contentor do Azure. Serviços de contentor do Azure fornece exposição automática das APIs do Machine Learning como pontos finais da API de REST com as seguintes funcionalidades:

- Autenticação
- Balanceamento de carga
- Automático de escalamento horizontal
- Encriptação

O Azure Machine Learning modelo Management fornece mais capacidades através da CLI, API e o portal do Azure. 

A gestão de modelo do Azure Machine Learning utiliza as seguintes informações:

 - Ficheiro de modelo ou um diretório com os ficheiros de modelo
 - Criada ficheiro de Python implementar um modelo de classificação de função de utilizador
 - Ficheiro de dependência de Conda listagem dependências de tempo de execução
 - Opção de ambiente de tempo de execução, e 
 - Ficheiro de esquema para os parâmetros de API 

Estas informações são utilizadas quando efetuar as seguintes ações:

- Registar um modelo
- Criar um manifesto que é utilizado ao criar um contentor
- Criar um Docker imagem de contentor
- Implementar um contentor para o serviço de contentor do Azure
 
A figura seguinte mostra uma descrição geral de como os modelos são registados e implementados no cluster. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Criar e gerir modelos 
Pode registar modelos com a gestão de modelo do Azure Machine Learning para controlar as versões do modelo na produção. Para facilitar a reproducibility e governação, o serviço de captura todas as dependências e informações associadas. Para obter informações mais aprofundadas no desempenho, pode capturar a telemetria de modelo utilizando o SDK fornecido. Telemetria de modelo é arquivada no armazenamento fornecidos pelo utilizador. A telemetria de modelo pode ser utilizada mais tarde para analisar o desempenho do modelo, reparametrização e obtenham insights para a sua empresa.

## <a name="create-and-manage-manifests"></a>Criar e gerir manifestos 
Modelos requerem artefactos adicionais para implementar em produção. O sistema fornece a capacidade de criar um manifesto que abrange o modelo, dependências, o script de inferência (também conhecido como a classificação de script), dados de exemplo, esquema etc. Este manifesto atua como uma receitas para criar uma imagem de contentor do Docker. As empresas podem gerar automaticamente o manifesto, versões diferentes de criar e gerir os manifestos. 

## <a name="create-and-manage-docker-container-images"></a>Criar e gerir imagens de contentor do Docker 
Pode utilizar o manifesto do passo anterior para criar imagens de contentor de Docker baseado no seus respetivos ambientes. As imagens de baseada em Docker, indique as empresas a flexibilidade para executar estas imagens nos seguintes ambientes de computação:

- [Kubernetes com base em serviço de contentor do Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Serviços de contentor no local
- Ambientes de desenvolvimento
- Dispositivos de IoT

Estas imagens de com base em Docker são autónomo com todas as dependências necessárias necessárias para gerar as predições. 

## <a name="deploy-docker-container-images"></a>Implementar imagens de contentor do Docker 
Com a gestão de modelo do Azure Machine Learning, pode implementar imagens do contentor de Docker baseado com um comando único para o serviço de contentor do Azure gerida pelo ambiente de computação de ML. Estas implementações são criadas com um servidor front-end que fornece as seguintes funcionalidades:

- Predições de latência baixa à escala
- Balanceamento de carga
- O dimensionamento automático de pontos finais de ML
- Autorização da chave de API
- Documento swagger de API

Pode controlar a telemetria através de definições de configuração seguintes e escala de implementação:

- Registo do sistema e telemetria de modelo para cada nível de serviço web. Se estiver ativada, todos os registos de stdout são transmissão em fluxo para [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Telemetria de modelo é arquivada no armazenamento que fornecer. 
- Limites de dimensionamento automático e concorrência. Estas definições aumentam automaticamente o número de contentores implementados com base na carga dentro do cluster existente. Também controlar o débito e a consistência da latência de predição.

## <a name="consumption"></a>Consumo 
O Azure Machine Learning modelo Management cria REST API para o modelo implementado juntamente com o documento swagger. Pode consumir modelos implementados por chamar as APIs REST API de chave e modelo entradas para obter as predições como parte das aplicações de linha de negócio. O código de exemplo está disponível no GitHub para idiomas Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)e a c# para chamar as APIs REST. A CLI de gestão do Azure Machine Learning modelo fornece uma forma fácil de trabalhar com estas APIs REST. Pode consumir APIs com um único comando da CLI, aplicações com capacidade para swagger, ou utilizando curl. 

## <a name="retraining"></a>Reparametrização 
O Azure Machine Learning modelo Management fornece APIs que pode utilizar para a reparametrização dos seus modelos. Também pode utilizar as APIs para atualizar as implementações existentes com versões atualizadas do modelo. Como parte do fluxo de trabalho de ciências de dados, recrie o modelo no seu ambiente de experimentação. Em seguida, pode registar o modelo com o modelo de gestão e atualizar as implementações existentes. As atualizações são executadas com um único comando da CLI de ATUALIZAÇÃO. O comando de ATUALIZAÇÃO atualiza as implementações existentes sem alterar o URL da API ou a chave. As aplicações do modelo de consumo continuam a funcionar sem qualquer alteração de código e comece a obter um melhor predições utilizando o novo modelo.

O fluxo de trabalho completo que descrevem estes conceitos é capturado na figura seguinte:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) 
- São suportados os tipos de dados? Pode passar NumPy matrizes diretamente como entrada para o serviço web?

   Se está a fornecer o ficheiro de esquema que foi criado utilizando generate_schema SDK, em seguida, pode passar NumPy e/ou Pandas DF. Também pode passar quaisquer entradas de serializáveis JSON. Pode passar imagem como binária codificada cadeia bem.

- O serviço web suportam várias entradas ou analisar entradas diferentes? 

   Sim, pode demorar várias entradas empacotadas num pedido JSON como um dicionário. Cada entrada iria corresponder a uma chave de dicionário exclusivo único.

- A chamada ativada por um pedido para o web service uma chamada de bloqueio ou uma chamada assíncrona

   Se o serviço foi criado utilizando a opção de em tempo real como parte da CLI ou API, é uma chamada para bloquear/síncronas. É esperado para ser rápido em tempo real. Apesar do lado do cliente que pode chamá-la utilizando async HTTP biblioteca para evitar bloquear o thread de cliente.

- O número de pedidos de serviço web em simultâneo processa o?

   Depende da escala de serviço web e de cluster. Pode ampliar o serviço x 100 das réplicas e, em seguida, pode processar vários pedidos em simultâneo. Também pode configurar o pedido simultâneo máximo pela réplica para aumentar o débito de serviço.

- O número de pedidos pode o serviço web em fila?

   É configurável. Por predefinição, está definido para 10 ~ por única réplica, mas pode aumentar/diminui-lo aos seus requisitos de aplicação. Normalmente, aumentar a o número de pedidos colocados na fila aumenta o débito de serviço, mas efetua a worse latências em percentiles superiores. Para manter as latências consistente, poderá pretender definir a colocação em fila para um valor baixo (1-5) e aumentar o número de réplicas para processar o débito. Também pode ativar o dimensionamento automático para que o número de réplicas ajustar automaticamente com base na carga. 

- Do mesmo computador ou cluster pode ser utilizado para vários pontos finais do serviço web?

   Com certeza. Pode executar 100 x de pontos finais / no mesmo cluster. 

## <a name="next-steps"></a>Passos seguintes
Para começar a trabalhar com a gestão de modelo, consulte [configurar a gestão de modelo](model-management-configuration.md).
