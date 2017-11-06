---
title: "Descrição geral do modelo de programação de serviço do Service Fabric fiável | Microsoft Docs"
description: "Saiba mais sobre o modelo de programação do Service Fabric serviço fiável e começar a escrever os seus próprios serviços."
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: masnider;
ms.openlocfilehash: 601b1c7713c9785d949c1c72000ec7f3f63dd682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-overview"></a>Descrição geral do Reliable Services
Azure Service Fabric simplifica escrever e gerir serviços fiável sem monitorização de estado e com monitorização de estado. Este tópico abrange:

* O modelo de programação Reliable Services para serviços sem monitorização de estado e com monitorização de estado.
* As escolhas que é necessário efetuar ao escrever um serviço fiável.
* Alguns cenários e exemplos de quando deve utilizar Reliable Services e como são escritos.

Reliable Services é um dos modelos de programação disponíveis no Service Fabric. O outro é o modelo de programação de Ator fiável, que fornece um modelo de programação de Ator virtual por cima do modelo de Reliable Services. Para obter mais informações sobre o modelo de programação Reliable Actors, consulte [introdução ao serviço de recursos de infraestrutura Reliable Actors](service-fabric-reliable-actors-introduction.md).

O Service Fabric gere a duração de serviços, a partir de aprovisionamento e implementação através da atualização e eliminação, através de [gestão de aplicações de Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Quais são Reliable Services?
Reliable Services dá-lhe um modelo de programação simple, poderoso, nível superior para o ajudar a express, o que é importante para a aplicação. Com os serviços fiável de modelo de programação, obter:

* Acesso para o resto dos recursos de infraestrutura do serviço de APIs de programação. Ao contrário dos serviços do Service Fabric modelada como [convidado executáveis](service-fabric-deploy-existing-app.md), obter Reliable Services para utilizar o resto das APIs de recursos de infraestrutura de serviço diretamente. Isto permite que os serviços:
  * consultar o sistema
  * Estado de funcionamento do relatório sobre entidades no cluster
  * receber notificações sobre as alterações de configuração e code
  * localizar e comunicar com outros serviços
  * (opcionalmente) a utilizar o [coleções fiável](service-fabric-reliable-services-reliable-collections.md)
  * ...e. conceder acesso à muitas outras funcionalidades, tudo a partir de um modelo de programação de primeira classe em várias linguagens de programação.
* Um modelo simple para executar o seu próprio código que se pareça com modelos que é utilizados para de programação. O código tem um ponto de entrada bem definidos e o ciclo de vida facilmente gerido.
* Um modelo de comunicação incorporável. Utilize o transporte à sua escolha, tal como HTTP com [Web API](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, ou qualquer outra função. Reliable Services fornecem algumas excelente out of box opções pode utilizar, ou pode fornecer os seus próprios.
* Para os serviços com monitorização de estado, o modelo de programação Reliable Services poder consistentemente e fiável armazenar o estado do direito dentro do seu serviço utilizando [coleções fiável](service-fabric-reliable-services-reliable-collections.md). Coleções fiáveis são um conjunto simple de classes de coleção e de elevada disponibilidade fiável que será familiar para qualquer pessoa que tenha utilizado c# coleções. Tradicionalmente, os serviços necessários sistemas externos para a gestão de estado fiável. Com coleções fiável, pode armazenar o estado junto ao seu computação com a mesma elevada disponibilidade e fiabilidade que tiver provenientes esperar elevados arquivos externos. Este modelo melhora também a latência porque que está a colocalização de computação e de estado tem de funcionar.

Veja este vídeo Microsoft Virtual Academy para uma descrição geral dos serviços fiáveis:<center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>O que faz com que Reliable Services diferentes?
Reliable Services no Service Fabric são diferentes dos serviços que pode ter escritas antes. O Service Fabric fornece escalabilidade, consistência, disponibilidade e fiabilidade.

* **Fiabilidade** - a sua service mantém-se a cópia de segurança, mesmo em ambientes pouco fiáveis, onde as máquinas falharam ou prima problemas de rede, ou em casos onde os serviços de se encontrarem erros e falhas ou falharam. Para os serviços com monitorização de estado, o estado é preservado mesmo na presença de rede ou outras falhas.
* **Disponibilidade** -o serviço é acessível e reativa. Service Fabric mantém o número pretendido de cópias a executar.
* **Escalabilidade** - os serviços são dissociados da hardware específico e podem aumentar ou diminuir o conforme necessário, através da adição ou remoção de hardware ou outros recursos. Serviços-se facilmente partições (especialmente no caso de monitorização de estado) para se certificar de que o serviço pode dimensionar e processar falhas parciais. Os serviços podem ser criados e eliminado dinamicamente através de código, ativar mais instâncias de aceleradas conforme necessário, diga em resposta aos pedidos de cliente. Por fim, o Service Fabric encoraja simples os serviços. Service Fabric permite milhares de serviços a ser aprovisionado dentro de um processo único, em vez de necessitando ou dedicação de instâncias de SO completos ou os processos para uma única instância de um serviço.
* **Consistência** -todas as informações armazenadas neste serviço podem ser garantidas para estar consistente. Isto acontece mesmo através de várias coleções fiáveis dentro de um serviço. É possível efetuar alterações em coleções dentro de um serviço de uma forma de uma forma atomic.

## <a name="service-lifecycle"></a>Ciclo de vida do serviço
Se o serviço está com monitorização de estado ou sem monitorização de estado, Reliable Services fornecem um ciclo de vida simple que lhe permite rapidamente fixação no seu código e começar a utilizar.  Existem apenas um ou dois métodos que necessita de implementar para obter o seu serviço e em execução.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** -este método é onde o serviço define o stack(s) de comunicação pretende utilizar. A comunicação da pilha, tais como [Web API](service-fabric-reliable-services-communication-webapi.md), é o que define o ponto final de escuta ou pontos finais para o serviço (como os clientes conseguirem contactar o serviço). Também define como as mensagens que são apresentados interagem com o resto do código do serviço.
* **Runasync com** -este método é onde o seu serviço é executada a respetiva lógica de negócio e onde-seria iniciar quaisquer tarefas em segundo plano que devem ser executado para a duração do serviço. O token de cancelamento é fornecido é um sinal de quando esse trabalho deverá ser interrompida. Por exemplo, se o serviço tem de solicitar mensagens fora de uma fila fiável e processá-los, este é onde ocorre que funcionam.

Se estiver a aprender sobre reliable services pela primeira vez, leia sobre! Se estiver à procura de instruções detalhadas sobre o ciclo de vida dos serviços fiáveis, pode aceda a [neste artigo](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Serviços de exemplo
Saber este modelo de programação, vamos ver rapidamente dois serviços diferentes para ver como estes peças encaixam.

### <a name="stateless-reliable-services"></a>Sem monitorização de estado Reliable Services
Um serviço sem monitorização de estado é um onde há sem estado mantido no âmbito do serviço entre chamadas. Qualquer Estado que se encontra presente é inteiramente disposable e não necessita de sincronização, replicação, persistência ou elevada disponibilidade.

Por exemplo, considere uma calculadora que tenha sem memória e recebe todas as operações para executar em simultâneo e em termos de licenciamento.

Neste caso, o `RunAsync()` (c#) ou `runAsync()` (Java) do serviço pode estar vazio, porque não existe nenhuma tarefa-processamento de em segundo plano que o serviço tem de fazer. Quando o serviço de Calculadora é criado, devolve uma `ICommunicationListener` (c#) ou `CommunicationListener` (Java) (por exemplo [Web API](service-fabric-reliable-services-communication-webapi.md)) que se abre um ponto final de escuta no algumas portas. Este ponto final de escuta hooks cópias de segurança para os métodos de cálculo diferente (exemplo: "Adicionar (n1, n2)") que define de API pública a Calculadora.

Quando é efetuada uma chamada a partir de um cliente, o método apropriado é invocado e o serviço de Calculadora executa as operações nos dados fornecidos e devolve o resultado. -Não armazena qualquer Estado.

Não armazenar qualquer Estado interno torna esta Calculadora de exemplo simples. Mas a maioria dos serviços não são verdadeiramente sem estado. Em vez disso, estes externalize o respetivo estado para alguns outro arquivo. (Por exemplo, todas as aplicações web que depende de manter o estado da sessão de um arquivo de cópia de segurança ou a cache não é sem monitorização de estado.)

Um exemplo comum da forma como são utilizados serviços sem monitorização de estado no Service Fabric é como um front-end que expõe a API de destinado ao público para uma aplicação web. O serviço de front-end, em seguida, a sua serviços com monitorização de estado para concluir um pedido de utilizador. Neste caso, as chamadas a partir de clientes são direcionadas para uma porta conhecida, por exemplo 80, onde o serviço sem monitorização de estado está a escutar. Este serviço sem monitorização de estado recebe a chamada e determina se a chamada é de uma entidade fidedigna e o serviço é destinado a.  Em seguida, o serviço sem estado reencaminha a chamada para a partição correta do serviço com monitorização de estado e aguarda uma resposta. Quando o serviço sem monitorização de estado recebe uma resposta, responde ao cliente original. Um exemplo de tal serviço é nos nossos exemplos [c#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). Esta é apenas um exemplo deste padrão de nos exemplos, existem outras pessoas, bem como outros exemplos.

### <a name="stateful-reliable-services"></a>Reliable Services com monitorização de estado
Um serviço com estado é aquele que tem de ter uma parte do estado mantido consistente e presente para que o serviço de função. Considere um serviço que constantemente calcula um média móvel dos últimos um valor com base nas actualizações recebe. Para tal, tem de ter o conjunto atual de pedidos de entrada tem de processo e a média atual. Qualquer serviço que obtém, processa e armazena informações num arquivo externo (por exemplo, um Azure blob ou tabela arquivo hoje) tem o estado monitorizado. Apenas mantém o estado no arquivo de estado externo.

Maioria dos serviços hoje armazenar o respetivo estado externamente, uma vez que o arquivo de externo que fornece a fiabilidade, disponibilidade, escalabilidade e consistência para esse Estado. No Service Fabric, os serviços não são necessários para armazenar o respetivo estado externamente. Service Fabric encarrega-se parte destes requisitos para o código do serviço e o estado do serviço.

> [!NOTE]
> Suporte para monitorização de estado Reliable Services não está disponível no Linux ainda (para c# ou Java).
>

Imaginemos que queremos escrever um serviço que processa as imagens. Para tal, o serviço aceita uma imagem e a série de conversões efetuar essa imagem. Este serviço devolve um serviço de escuta de comunicação (vamos Suponhamos é um end de WebAPI) que expõe uma API, como `ConvertImage(Image i, IList<Conversion> conversions)`. Quando recebe um pedido, o serviço armazena-a num `IReliableQueue`e devolve algumas id para o cliente, pode controlar o pedido.

Neste serviço `RunAsync()` pode ser mais complexas. O serviço tem um ciclo dentro respetivo `RunAsync()` que obtém a pedidos de `IReliableQueue` e efetua conversões pedidas. Obterem armazenar os resultados num `IReliableDictionary` para que quando o cliente volta a, podem obter as suas imagens convertidas. Para garantir que, mesmo se algo falha a imagem não é perdido, este serviço fiável seria solicitar fora da fila, execute os conversões e armazenar o resultado tudo numa só transação. Neste caso, a mensagem é removida da fila e os resultados são armazenados no dicionário de resultado apenas quando os conversões estiverem concluídas. Em alternativa, o serviço foi solicitar a imagem fora da fila e imediatamente armazene-o num arquivo remoto. Isto reduz a quantidade de estado que do serviço tem de gerir, mas aumenta complexidade dado que o serviço tem de manter os metadados necessários para gerir o armazenamento remoto. Com uma abordagem, se algo falhou no meio o pedido permanece na fila de espera para serem processados.

Um aspeto a ter em atenção sobre este serviço é que o se SOA como um serviço normal do .NET! A única diferença é que as estruturas de dados a ser utilizado (`IReliableQueue` e `IReliableDictionary`) são fornecidas pelo serviço de recursos de infraestrutura e são de elevada disponibilidade fiável, e consistente.

## <a name="when-to-use-reliable-services-apis"></a>Quando utilizar APIs de serviços fiável
Se qualquer uma das seguintes caracterizam suas necessidades de serviço de aplicações, em seguida, deve considerar a fiável APIs de serviços:

* Pretende código do serviço (e opcionalmente de estado) para ser de elevada disponibilidade e fiável
* Terá de garantias transacionais em várias unidades de estado (por exemplo, as ordens e itens de linha de ordem).
* Estado da aplicação pode ser naturalmente modelado como dicionários fiável e pelas filas.
* O código de aplicações ou estado tem de ser elevada com latência baixa leituras e escritas.
* A aplicação tem de controlar a simultaneidade ou granularidade de operações transacionadas numa ou mais coleções fiável.
* Pretende gerir as comunicações ou controlar o esquema de partições para o seu serviço.
* O código tem um ambiente de tempo de execução free-threaded.
* A aplicação precisa dinamicamente criar ou destruir dicionários fiável ou filas ou serviços todo no tempo de execução.
* Tem de controlar fornecido de recursos de infraestrutura do serviço de cópia de segurança e restaurar as funcionalidades para o estado do serviço programaticamente.
* A aplicação tem de manter histórico de alterações para o respetivas unidades de estado.
* Pretende desenvolver ou consumir fornecedores de estado personalizado, desenvolvido de terceiros de terceiros.

## <a name="next-steps"></a>Passos seguintes
* [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
* [Reliable Services avançadas de utilização](service-fabric-reliable-services-advanced-usage.md)
* [O modelo de programação Reliable Actors](service-fabric-reliable-actors-introduction.md)
