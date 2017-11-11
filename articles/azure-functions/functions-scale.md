---
title: "Comparação de planos de alojamento de funções do Azure | Microsoft Docs"
description: "Saiba como escolher entre o plano de consumo de funções do Azure e o plano do App Service."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure funções, funções, o plano de consumo, plano do app service, o processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 423eee65040a11695d9f6c18d64948e4c3d3aafe
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-functions-hosting-plans-comparison"></a>Comparação de planos de alojamento das funções do Azure

## <a name="introduction"></a>Introdução

Pode executar as funções do Azure em dois modos diferentes: plano de consumo e o plano do App Service do Azure. O plano de consumo atribui automaticamente a capacidade de computação quando o código está em execução, aumenta horizontalmente de forma conforme necessário para processar carga e, em seguida, dimensiona quando o código não está em execução. Por isso, não tem de pagar VMs Inativas e não têm capacidade de reserva antecipadamente. Este artigo incida no plano de consumo, um [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) modelo de aplicação. Para obter mais informações sobre como funciona o plano do App Service, consulte o [descrição geral dos planos do App Service do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Se não estiver familiarizado com as funções do Azure, consulte o [descrição geral das funções do Azure](functions-overview.md).

Quando cria uma aplicação de função, tem de configurar um plano de alojamento para as funções que contém a aplicação. O modo, uma instância do *anfitrião das funções do Azure* executa as funções. O tipo de controlos de plano:

* Como são ampliar instâncias de anfitrião.
* Os recursos que estão disponíveis para cada anfitrião.

Atualmente, tem de escolher o tipo do plano de alojamento durante a criação da aplicação de função. Não é possível alterá-la mais tarde. 

Um plano de serviço de aplicações pode dimensionar entre camadas alocar diferentes quantidade de recursos. No plano de consumo, as funções do Azure automaticamente processa todos os alocação de recursos.

## <a name="consumption-plan"></a>Plano de consumo

Quando estiver a utilizar um plano de consumo, instâncias de anfitrião das funções do Azure são adicionadas dinamicamente e removidas com base no número de eventos recebidos. Este plano dimensiona automaticamente e são-lhe cobrados os recursos de computação apenas quando as suas funções estão em execução. Um plano de consumo, pode executar uma função para um máximo de 10 minutos. 

> [!NOTE]
> O tempo limite predefinido para funções de um plano de consumo é de 5 minutos. O valor pode ser aumentado para 10 minutos para a aplicação de função alterando a propriedade `functionTimeout` no [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).

Faturação é baseada no número de execuções, tempo de execução e a memória utilizada. Faturação é agregada em todas as funções dentro de uma aplicação de função. Para obter mais informações, consulte o [das funções do Azure a página de preços].

O plano de consumo a predefinição é o plano de alojamento e oferece as seguintes vantagens:
- Paga apenas quando as suas funções estão em execução.
- Aumentar horizontalmente automaticamente, mesmo durante períodos de alta carregar.

## <a name="app-service-plan"></a>Plano do App Service

O plano de serviço de aplicações, as aplicações de função executam em VMs dedicadas em básicas, Standard, Premium e SKUs isolado, semelhantes às Web Apps, API Apps e aplicações móveis. VMs dedicadas são atribuídas às suas aplicações de serviço de aplicações, o que significa que o anfitrião de funções está sempre em execução.

Tenha em consideração um plano de serviço de aplicações nos seguintes casos:
- Tem as VMs existentes, subutilizadas que já estejam a executar outras instâncias de serviço de aplicações.
- Prevê que as aplicações de função para executar continuamente ou quase continuamente. Neste caso, um plano do App Service pode ser mais económico.
- Precisa de mais opções de CPU ou memória ao que é fornecido no plano de consumo.
- Tem de executar mais do que o tempo de execução máximo permitido no plano de consumo (de 10 minutos).
- Precisa de funcionalidades que só estão disponíveis num plano de serviço de aplicações, como o suporte para o ambiente de serviço de aplicações, conectividade VNET/VPN e tamanhos de VM maiores. 

Uma VM desassocia custo de número de execuções, tempo de execução e memória utilizada. Como resultado, não será paga mais do que o custo da instância de VM que alocar. Para obter mais informações sobre como funciona o plano do App Service, consulte o [descrição geral dos planos do App Service do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Com um plano de serviço de aplicações, pode manualmente aumentar horizontalmente adicionando mais instâncias VM ou, pode ativar o dimensionamento automático. Para obter mais informações, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). Também pode aumentar verticalmente ao escolher um plano de serviço aplicacional diferente. Para obter mais informações, consulte [aumentar verticalmente a uma aplicação no Azure](../app-service/web-sites-scale.md). 

Se estiver a planear executar funções JavaScript um plano de serviço de aplicações, deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte o [escolha planos de serviços aplicacionais single-core](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
###Always On

Se executar um plano de serviço de aplicações, deverá ativar a **Always On** definição para que a aplicação de função é executada corretamente. Um plano de serviço de aplicações, o tempo de execução de funções passa inativo após alguns minutos de inatividade, pelo que só acionadores HTTP "reativará" as suas funções. Isto é semelhante à forma como WebJobs tem de ter Always On ativado. 

Always On só está disponível no plano de serviço aplicacional. Um plano de consumo, a plataforma ativa função aplicações automaticamente.

## <a name="storage-account-requirements"></a>Requisitos de conta de armazenamento

Um plano de consumo ou um plano do App Service, uma aplicação de função necessita de uma conta de armazenamento do Azure geral que suporta o armazenamento de Blobs do Azure, filas, ficheiros e tabela. Internamente, as funções do Azure utiliza o armazenamento do Azure para operações como a gestão de acionadores e execuções de função de registo. Algumas contas de armazenamento não suportam as filas e tabelas, tais como contas de armazenamento apenas de BLOBs (incluindo o armazenamento premium) e contas de armazenamento para fins gerais com a replicação de armazenamento com redundância de zona. Estas contas são filtradas do **conta de armazenamento** painel quando estiver a criar uma aplicação de função.

Para saber mais sobre os tipos de conta de armazenamento, consulte o artigo [introduzir os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Como funciona o plano de consumo

O plano de consumo, o controlador de escala dimensiona automaticamente recursos de CPU e memória adicionando instâncias adicionais do anfitrião de funções, com base no número de eventos que as funções são acionadas no. Cada instância de anfitrião de funções está limitada a 1,5 GB de memória.

Quando utiliza o consumo de plano de alojamento, ficheiros de código de função são armazenados em partilhas de ficheiros do Azure numa conta de armazenamento principal da função. Ao eliminar a conta de armazenamento principal da aplicação de função, os ficheiros de código de função são eliminados e não podem ser recuperados.

> [!NOTE]
> Quando estiver a utilizar um acionador de blob um plano de consumo, podem existir até um atraso de 10 minutos processar novos blobs, se uma aplicação de função tornou-se inativo. Depois da aplicação de função está em execução, blobs são processados imediatamente. Para evitar este atraso inicial, considere uma das seguintes opções:
> - O anfitrião da aplicação de função no plano de serviço de aplicações, com o Always On ativado.
> - Utilize outro mecanismo para acionar o blob processar, tais como uma mensagem de fila que contém o nome do blob. Por exemplo, consulte o [script do c# e exemplos de JavaScript para o blob de entrada e saída enlaces](functions-bindings-storage-blob.md#input--output---example).

### <a name="runtime-scaling"></a>Dimensionamento de tempo de execução

As funções do Azure utiliza um componente denominado o *controlador escala* para monitorizar a taxa de eventos e determinar se deve aumentar ou reduzir no horizontalmente. O controlador de escala utiliza a heurística para cada tipo de Acionador. Por exemplo, quando estiver a utilizar um acionador de armazenamento de filas do Azure, dimensiona consoante o comprimento da fila e a duração da mensagem de fila mais antiga.

A unidade de escala é a aplicação de função. Quando a aplicação de função é ampliada, são atribuídos recursos adicionais para executar várias instâncias do anfitrião das funções do Azure. Por outro lado, como a computação a pedido é reduzida, o controlador de escala remove as instâncias de anfitrião de função. O número de instâncias, eventualmente, é dimensionado para baixo para zero quando não funciona em execução dentro de uma aplicação de função.

![Controlador de escala eventos de monitorização e criação de instâncias](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>Modelo de faturação

Faturação para o plano de consumo é descrito detalhadamente no [das funções do Azure a página de preços]. Utilização é agregada ao nível da aplicação de função e contagens apenas o tempo que o código de função é executado. Seguem-se as unidades de faturação: 
* **Consumo de recursos em segundos de gigabyte (GB-s)**. Calculada como uma combinação de tamanho de memória e tempo de execução para todas as funções dentro de uma aplicação de função. 
* **Execuções**. Contados sempre que uma função é executada em resposta a um acionador de eventos.

[das funções do Azure a página de preços]: https://azure.microsoft.com/pricing/details/functions
