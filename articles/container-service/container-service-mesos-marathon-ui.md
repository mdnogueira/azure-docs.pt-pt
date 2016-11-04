---
title: Gestão de contentores do Serviço de Contentor do Azure através da IU da Web | Microsoft Docs
description: Implemente contentores num serviço de cluster do Serviço de Contentor do Azure utilizando a IU da Web do Marathon.
services: container-service
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Contentores, Microserviços, Mesos, Azure

ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: nepeters

---
# Gestão de contentores através da IU da Web
O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação.

Apesar de as estruturas estarem disponíveis para muitas cargas de trabalho populares, este documento descreve como pode criar e dimensionar as implementações de contentores com o Marathon. Para poder trabalhar com estes exemplos, necessita de um cluster do DC/OS configurado num Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

* [Implementar um cluster do Serviço de Contentor do Azure](container-service-deployment.md)
* [Ligar a um cluster do Serviço de Contentor Azure](container-service-connect.md)

## Explorar a IU do DC/OS
Com um túnel do Secure Shell (SSH) estabelecido, aceda a http://localhost/. Este procedimento permite carregar a IU da Web do DC/OS e mostrar as informações sobre o cluster, tais como recursos utilizados, agentes ativos e serviços em execução.

![IU do DC/OS](media/dcos/dcos2.png)

## Explorar a IU do Marathon
Para ver a IU do Marathon, aceda a http://localhost/Marathon. Neste ecrã, pode iniciar um novo contentor ou outra aplicação no cluster do DC/OS do Serviço de Contentor do Azure. Também pode ver informações sobre contentores e aplicações em execução.  

![IU do Marathon](media/dcos/dcos3.png)

## Implementar um contentor formatado para Docker
Para implementar um novo contentor utilizando o Marathon, clique no botão **Criar Aplicação** e introduza as seguintes informações no formulário:

| Campo | Valor |
| --- | --- |
| ID |nginx |
| Imagem |nginx |
| Rede |Com bridge |
| Porta do Anfitrião |80 |
| Protocolo |TCP |

![IU da Nova Aplicação – Geral](media/dcos/dcos4.png)

![IU da Nova Aplicação – Contentor do Docker](media/dcos/dcos5.png)

![IU da Nova Aplicação – Deteção de Serviços e Portas](media/dcos/dcos6.png)

Se pretender mapear estaticamente a porta do contentor para uma porta no agente, terá de utilizar o Modo JSON. Para tal, mude o assistente da Nova Aplicação para **Modo JSON** utilizando o botão de alternar. Em seguida, introduza o seguinte na secção `portMappings` da definição da aplicação. Este exemplo vincula a porta 80 do contentor à porta 80 do agente DC/OS. Pode voltar a mudar este assistente do Modo JSON após efetuar esta alteração.

```none
"hostPort": 80,
```

![IU da Nova Aplicação – exemplo de porta 80](media/dcos/dcos13.png)

O cluster do DC/OS é implementado com um conjunto de agentes públicos e privados. Para que o cluster possa aceder às aplicações a partir da Internet, tem de implementar as aplicações para um agente público. Para fazê-lo, selecione o separador **Opcional** do assistente da Nova Aplicação e introduza **slave_public** para **Funções dos Recursos Aceites**.

![IU da Nova Aplicação – definição de agente público](media/dcos/dcos14.png)

De voltar à página principal do Marathon, pode ver o estado de implementação do contentor.

![IU da página principal do Marathon – estado de implementação do contentor](media/dcos/dcos7.png)

Quando voltar a mudar para o IU da Web do DC/OS (http://localhost/), verá que uma tarefa (neste caso, um contentor formatado para Docker) está em execução no cluster do DC/OS.

![IU da Web do DC/OS – tarefa em execução no cluster](media/dcos/dcos8.png)

Também pode ver que o nó de cluster que a tarefa está a executar.

![IU da Web do DC/OS – nó de cluster da tarefa](media/dcos/dcos9.png)

## Dimensionar os contentores
Pode utilizar a IU do Marathon para dimensionar a contagem de instâncias de um contentor. Para tal, navegue até à página do **Marathon**, selecione o contentor que pretende dimensionar e clique no botão **Dimensionar**. Na caixa de diálogo **Dimensionar Aplicação**, introduza o número de instâncias do contentor que pretende e selecione **Dimensionar Aplicação**.

![IU do Marathon – caixa de diálogo Dimensionar Aplicação](media/dcos/dcos10.png)

Depois de concluída a operação de dimensionamento, verá várias instâncias da mesma tarefa distribuídas pelos agentes DC/OS.

![Dashboard da IU da Web do DC/OS – difusão de tarefas entre agentes](media/dcos/dcos11.png)

![IU da Web do DC/OS – nós](media/dcos/dcos12.png)

## Passos seguintes
* [Trabalhar com o DC/OS e a API do Marathon](container-service-mesos-marathon-rest.md)

Descrição aprofundada sobre o Azure Container Service com Mesos

> [AZURE.VIDEO] azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]
> 
> 

<!--HONumber=Sep16_HO3-->


