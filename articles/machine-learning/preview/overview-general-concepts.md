---
title: "Descrição geral conceptual dos funcionalidades de pré-visualização do Azure Machine Learning | Microsoft Docs"
description: "Uma descrição geral conceptual das funcionalidades de pré-visualização do Azure Machine Learning, tais como as subscrições, contas, áreas de trabalho, projetos, etc."
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 3d4ba2ca6f7adc8b51030c02d9e9eeb2b9995bb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning---concepts"></a>Conceitos o Azure Machine Learning-

Este artigo define e descreve os conceitos que precisa de saber para utilizar o Azure Machine Learning. 

![Hierarquia de conceitos](media/overview-general-concepts/hierarchy.png)

- **Subscrição:** uma subscrição do Azure concede acesso a recursos no Azure. Porque o Azure Machine Learning está profundamente integrado com computação, armazenamento e muitos outros recursos do Azure e serviços, Workbench requer que cada utilizador tem acesso a uma subscrição do Azure válido. Os utilizadores também tem de ter permissões suficientes dentro dessa subscrição para criar recursos.


- **Conta de experimentação:** conta experimentação é um recurso do Azure necessário do Azure ML e um vehicle de faturação. Contém as áreas de trabalho, que por sua vez contêm projetos. Pode adicionar vários utilizadores, referidos como _licenças_, para uma conta de experimentação. Tem de ter acesso a uma conta de experimentação para utilizar o Azure ML Workbench para executar experimentações. 


- **Conta de gestão de modelo** uma conta de modelo de gestão também é um recurso do Azure necessário para gerir modelos do Azure ML. Pode utilizá-lo para registar modelos e manifestos, criar web de serviços e implementá-los localmente ou na nuvem. Também é o veículo outro faturação do Azure ML.


- **Área de trabalho:** uma área de trabalho é o componente principal para a partilha e de colaboração no Azure ML. Projetos estão agrupados dentro de uma área de trabalho. Uma área de trabalho, em seguida, pode ser partilhada com vários utilizadores que foram adicionados à conta de experimentação.


- **Projeto:** no Azure Machine Learning, um projeto é o contentor lógico para todo o trabalho a ser efetuado para resolver um problema. Mapeia para uma pasta único ficheiro no disco local, e pode adicionar todos os ficheiros ou sub pastas ao mesmo. Um projeto, opcionalmente, pode ser associado com um repositório de Git para controlo de origem e de colaboração.  

- **Experimentação:** do Azure ML, uma experimentação é um ou mais ficheiros de código origem que podem ser executados a partir de um ponto de entrada único. Pode conter tarefas, tais como a ingestão de dados, engenharia da funcionalidade, formação de modelo ou avaliação de modelo. Atualmente, do Azure ML suporta Python ou PySpark experimentações apenas.


- **Modelo:** Azure Machine Learning, modelos referem-se ao produto de uma experimentação do machine learning. São receitas que quando aplicadas corretamente aos dados, gerar valores previstos. Modelos podem ser implementadas para teste ou de produção ambientes e utilizadas para a classificação de dados de novo. Uma vez na produção, modelos podem ser monitorizados para dados de desempenho e que se desviam e retrained conforme necessário. 

- **Destino de computação:** um destino de computação é o recurso de computação que configurou para a execução das suas experimentações. Pode ser o computador local (Windows ou macOS), o contentor de Docker em execução no computador local ou numa VM com Linux no Azure ou um cluster do HDInsight Spark.


- **Execute:** a experimentação de serviço define uma execução como a duração de uma execução de experimentação num destino de computação. Azure ML captura informações de cada execução automaticamente e apresenta o histórico completo de uma experimentação específico sob a forma de histórico de execução.

- **Ambiente:** no Azure Machine Learning, um ambiente indica um recurso informático específico que é utilizado para implementar e gerir os seus modelos. Pode ser o computador local, uma VM com Linux no Azure ou um cluster de Kubernetes em execução no serviço de contentor do Azure, dependendo do contexto e a configuração. O seu modelo é alojado num contentor Docker em execução nestes ambientes e exposto como um ponto final de REST API.


- **Modelo gerido:** gestão de modelo permite-lhe implementar modelos como serviços web, várias versões dos seus modelos de gerir e monitorizar as métricas e desempenho. Modelos geridos estão registados com uma conta de gestão de modelo do Azure Machine Learning.

- **Manifestos:** quando o sistema de gestão de modelo implementa um modelo em produção, inclui um manifesto que pode abranger modelo, dependências, o script de classificação, dados de exemplo e esquema. O manifesto é receitas utilizada para criar uma imagem de contentor do Docker. Utilizando a gestão de modelo, pode gerar automaticamente os manifestos, versões diferentes de criar e gerir estes manifestos. 


- **Imagens:** pode utilizar manifestos ao gerar (e voltar a gerar) as imagens de Docker. Imagens de Docker de criar flexibilidade para executá-los na nuvem, no máquinas locais ou num dispositivo IoT. Imagens autónomo e incluem todas as dependências necessárias para novos dados com os modelos de classificação. 

- **Serviços:** gestão de modelo permite-lhe implementar modelos como serviços web. A lógica de serviço Web e as dependências são encapsuladas para uma imagem. Cada webservice é um conjunto de contentores com base na imagem preparado para pedidos de serviço para um determinado URL. Um serviço web é contabilizado como uma única implementação.
