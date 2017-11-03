---
title: Gerir o cluster DC/OS do Azure com a IU do Marathon | Microsoft Docs
description: "Implemente contentores num serviço de cluster do Serviço de Contentor do Azure utilizando a IU da Web do Marathon."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microserviços, Mesos, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b00088bb005519dc5d533433308c0e3e33c7f433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>Gerir um cluster DC/SO do Azure Container Service através da Marathon Web UI
O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação.

Enquanto as estruturas estejam disponíveis para muitas cargas de trabalho populares, este documento descreve como começar a implementar contentores com o Marathon. 


## <a name="prerequisites"></a>Pré-requisitos
Antes de avançarmos, necessita de um cluster DC/OS configurado no Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

* [Implementar um cluster do Azure Container Service](container-service-deployment.md)
* [Ligar a um cluster do Azure Container Service](../container-service-connect.md)

> [!NOTE]
> Este artigo pressupõe que é de túnel para o cluster DC/SO através da sua porta local 80.
>

## <a name="explore-the-dcos-ui"></a>Explorar a IU do DC/OS
Com um túnel do Secure Shell (SSH) [estabelecido](../container-service-connect.md), aceda a http://localhost/. Este procedimento permite carregar a IU da Web do DC/OS e mostrar as informações sobre o cluster, tais como recursos utilizados, agentes ativos e serviços em execução.

![IU do DC/OS](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorar a IU do Marathon
Para ver a IU do Marathon, aceda a http://localhost/marathon. Neste ecrã, pode iniciar um novo contentor ou outra aplicação no cluster do DC/OS do Serviço de Contentor do Azure. Também pode ver informações sobre contentores e aplicações em execução.  

![IU do Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatado para Docker
Para implementar um novo contentor utilizando o Marathon, clique em **Criar Aplicação** e introduza as seguintes informações nos separadores do formulário:

| Campo | Valor |
| --- | --- |
| ID |nginx |
| Memória | 32 |
| Imagem |nginx |
| Rede |Com bridge |
| Porta do Anfitrião |80 |
| Protocolo |TCP |

![IU da Nova Aplicação – Geral](./media/container-service-mesos-marathon-ui/dcos4.png)

![IU da Nova Aplicação – Contentor do Docker](./media/container-service-mesos-marathon-ui/dcos5.png)

![IU da Nova Aplicação – Deteção de Serviços e Portas](./media/container-service-mesos-marathon-ui/dcos6.png)

Se pretender mapear estaticamente a porta do contentor para uma porta no agente, terá de utilizar o Modo JSON. Para tal, mude o assistente da Nova Aplicação para **Modo JSON** utilizando o botão de alternar. Em seguida, introduza o seguinte definição na secção `portMappings` da definição da aplicação. Este exemplo vincula a porta 80 do contentor à porta 80 do agente DC/OS. Pode voltar a mudar este assistente do Modo JSON após efetuar esta alteração.

```none
"hostPort": 80,
```

![IU da Nova Aplicação – exemplo de porta 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Se pretender ativar as verificações de estado de funcionamento, defina um caminho no separador **Verificações do Estado de Funcionamento**.

![Nova IU da aplicação – verificações de estado de funcionamento](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

O cluster do DC/OS é implementado com um conjunto de agentes públicos e privados. Para que o cluster possa aceder às aplicações a partir da Internet, tem de implementar as aplicações para um agente público. Para fazê-lo, selecione o separador **Opcional** do assistente da Nova Aplicação e introduza **slave_public** para **Funções dos Recursos Aceites**.

Em seguida, clique em **Criar Aplicação**.

![IU da Nova Aplicação – definição de agente público](./media/container-service-mesos-marathon-ui/dcos14.png)

De voltar à página principal do Marathon, pode ver o estado de implementação do contentor. Inicialmente, vê o estado **A implementar**. Após uma implementação bem-sucedida, o estado é alterado para **Em execução**.

![IU da página principal do Marathon – estado de implementação do contentor](./media/container-service-mesos-marathon-ui/dcos7.png)

Quando voltar a mudar para o IU da Web do DC/OS (http://localhost/), verá que uma tarefa (neste caso, um contentor formatado para Docker) está em execução no cluster do DC/OS.

![IU da Web do DC/OS – tarefa em execução no cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

Para ver que o nó de cluster que a tarefa está a executar, clique no separador **Nós**.

![IU da Web do DC/OS – nó de cluster da tarefa](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Alcançar o contentor

Neste exemplo, a aplicação está em execução num nó de agente público. Aceder a aplicação da internet ao navegar para o agente do FQDN do cluster: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, em que:

* **DNSPREFIX** é o prefixo DNS que forneceu quando implementou o cluster.
* **REGION** é a região no qual o grupo de recursos está localizado.

    ![Nginx da Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Passos seguintes
* [Trabalhar com o DC/OS e a API do Marathon](container-service-mesos-marathon-rest.md)

* Descrição aprofundada sobre o Azure Container Service com Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
