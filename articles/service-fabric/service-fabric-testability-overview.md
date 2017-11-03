---
title: "Descrição geral do serviço de análise de falhas | Microsoft Docs"
description: "Este artigo descreve o serviço de análise de falhas no Service Fabric para inducing falhas e em execução os serviços de cenários de teste."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: f275fa5d3d6d727b016e55c188321d7e68091a33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introdução ao serviço de análise de falhas
O serviço de análise de falhas foi concebido para serviços que são criados no Microsoft Azure Service Fabric de teste. Com o serviço de análise de falhas pode induce falhas significativas e executar cenários de teste concluída contra as suas aplicações. Estes cenários de falhas e exercer e validar a vários Estados e transições que irá ocorrer um serviço em toda a respetiva duração, todos os de forma consistente, controlada e segura.

As ações são falhas individuais direcionada para um serviço para testá-lo. Um programador de serviço pode utilizá-las como blocos modulares para escrever cenários mais complicados. Por exemplo:

* Reinicia um nó para simular qualquer número de situações em que uma VM ou o computador for reiniciado.
* Mova uma réplica do seu serviço de monitorização de estado para simular o balanceamento de carga, ativação pós-falha ou atualização da aplicação.
* Invocar perda de quórum num serviço com monitorização de estado para criar uma situação em que as operações de escrita não é possível continuar porque não existe não são suficientes réplicas "cópia de segurança" ou "secundários" para aceitar novos dados.
* Invocar perda de dados num serviço com monitorização de estado para criar uma situação em que todos os Estados de memória é completamente eliminados.

Cenários são operações complexas de composto por uma ou mais ações. O serviço de análise de falhas fornece dois cenários completos incorporados:

* Cenário de Chaos
* Cenário de ativação pós-falha

## <a name="testing-as-a-service"></a>Como um serviço de teste
O serviço de análise de falhas é um serviço de sistema do Service Fabric é automaticamente iniciado com um cluster do Service Fabric. Este serviço atua como o anfitrião para a injeção de falhas, execução de cenário de teste e análise do Estado de funcionamento. 

![Serviço de análise de falhas][0]

Quando um cenário de ação ou de teste de falhas é iniciado, um comando é enviado para o serviço de análise de falhas para executar o cenário de ação ou de teste de falhas. O serviço de análise de falhas é com monitorização de estado para que possam fiável executar falhas e cenários e validar os resultados. Por exemplo, um cenário de teste de longa execução pode ser executado fiável pelo serviço de análise de falhas. E porque testes estão a ser executados no interior do cluster, o serviço pode examinar o estado do cluster e os serviços para fornecer mais informações aprofundadas sobre falhas.

## <a name="testing-distributed-systems"></a>Sistemas distribuídos de teste
Recursos de infraestrutura de serviço faz com que a tarefa de escrita e gerir aplicações dimensionáveis distribuídas significativamente mais fácil. O serviço de análise de falhas torna o teste de uma aplicação distribuída da mesma forma mais fácil. Existem três problemas principais que têm de ser resolvidos ao testar o:

1. Simulando/gerar falhas que possam ocorrer na cenários no mundo real: um dos aspetos importantes do Service Fabric é que permite que as aplicações distribuídas recuperar a partir de várias falhas. No entanto, para que a aplicação é capaz de recuperar a partir destas falhas de teste, é preciso um mecanismo para simular/gerar destas falhas do mundo real num ambiente de teste controladas.
2. A capacidade de gerar falhas correlacionadas: básicas falhas no sistema, tais como falhas de rede e falhas de máquina, são fáceis de produzir individualmente. Gerar um número significativo de cenários que podem ocorrer no mundo real, como resultado as interações uma destas falhas individuais é não trivial.
3. Experiência de unificada através de vários níveis de desenvolvimento e implementação: existem muitos sistemas de injeção de falhas que podem fazer vários tipos de falhas. No entanto, a experiência na todos estes é fraca para mover de cenários de caixa de um programador, para executar os testes mesmos em ambientes de teste de grandes dimensões, para utilizá-los para testes em produção.

Enquanto existem muitas mecanismos para resolver estes problemas, um sistema que tem a mesma com garantias necessárias – até a partir de um ambiente de programação de uma caixa, para testar em clusters de produção – está em falta. O serviço de análise de falhas ajuda os programadores da aplicação nos podermos concentrar nos respetivos lógica de negócio de teste. O serviço de análise de falhas fornece todas as funções necessárias para testar a interação do serviço com o sistema distribuída subjacente.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Cenários de falha do mundo real simulando/gerar
Para testar o robustez de um sistema distribuído contra falhas, é necessário um mecanismo para gerar falhas. Em teoria, gerar uma falha, tal como um nó para baixo parece fácil, começa atingir o mesmo conjunto de problemas de consistência que Service Fabric está a tentar resolver. Por exemplo, se quisermos encerrar um nó, o fluxo de trabalho necessário é o seguinte:

1. A partir do cliente, emita um pedido de nó de encerramento.
2. Envie o pedido para o nó correto.
   
    a. Se o nó não for encontrado, se falhar.
   
    b. Se o nó for encontrado, este deve ser devolvido apenas se o nó é encerrado.

Para verificar a falha de uma perspetiva de teste, o teste tem de saber que desta falha é induzida, a falha, na verdade, ocorrerá. A garantia de que o Service Fabric fornece é que o nó será abaixo ou já estava inativo quando o comando atingido o nó. Em ambos os casos de teste deve ser capaz de corretamente pelo motivo sobre o estado e ou não bem-sucedidos corretamente na respectiva validação. Um sistema implementado fora do Service Fabric para fazer o mesmo conjunto de falhas foi atingiu muitos rede, o hardware e problemas de software, impediriam fornecer as garantias anteriores. Na presença de problemas indicados, antes do Service Fabric irá reconfigurar o estado do cluster para resolver os problemas e, por conseguinte, o serviço de análise de falhas continuarão a poder dar o conjunto correto de garantias.

### <a name="generating-required-events-and-scenarios"></a>A gerar eventos necessários e cenários
Enquanto simulando uma falha do mundo real consistentemente tough para começar, a capacidade de gerar falhas correlacionadas é mesmo tougher. Por exemplo, uma perda de dados ocorre um serviço com estado persistente quando ocorrem os seguintes procedimentos:

1. Apenas um quórum de escrita das réplicas são processadas na replicação. Todas as réplicas secundárias lag atrás primário.
2. O quórum de escrita fica inativo devido às réplicas vai para baixo (devido a um pacote do código ou um nó vai para baixo).
3. O quórum de escrita não é possível voltar atrás cópias de segurança porque os dados das réplicas são perdidos (devido à existência de danos no disco ou reprocessamento de imagem de máquina).

Estas falhas correlacionadas acontecer no mundo real, mas não como falhas frequentemente como individuais. A capacidade de teste para estes cenários antes de poderem acontecer na produção é fundamental. Ainda mais importante é a capacidade para simular estes cenários com cargas de trabalho de produção controlado circunstâncias (no meio do dia com todos os engenheiros no deck). Que é muito melhor do que ter o mesmo acontecer pela primeira vez em produção às 2:00

### <a name="unified-experience-across-different-environments"></a>Experiência unificada entre ambientes diferentes
A prática tradicionalmente foi criar três conjuntos diferentes de experiências, um para o ambiente de desenvolvimento, outra para testes e outro para produção. O modelo foi:

1. No ambiente de desenvolvimento, produzir transições de estado que permitem que os testes de unidade dos métodos individuais.
2. No ambiente de teste, produzir falhas para permitir testes de ponto-a-ponto que exercer os vários cenários de falha.
3. Mantenha o ambiente de produção pristine para evitar eventuais falhas não natural e para garantir que existe resposta humana tempos de resposta extremamente rápida a falha.

No Service Fabric, através do serviço de análise de falhas, iremos propor ative esta opção em torno e utilizar a mesma metodologia do ambiente de programação para produção. Existem duas formas de alcançar isto:

1. Para induce falhas controladas, utilize as APIs de serviço da análise de falhas, de um ambiente de uma caixa para clusters de produção.
2. Para dar um fever que faz com que induction automática de falhas de cluster, utilize o serviço de análise de falhas para gerar falhas automáticas. Controlar a taxa de falhas através da configuração permite que o mesmo serviço ser testados de forma diferente em ambientes diferentes.

Com o Service Fabric, embora a escala de falhas de ser diferente em ambientes diferentes, os mecanismos de reais seria idênticos. Isto permite um pipeline muito mais rápida código à implementação e a capacidade para testar os serviços sob cargas pesadas do mundo real.

## <a name="using-the-fault-analysis-service"></a>Utilizar o serviço de análise de falhas
**C#**

Funcionalidades do serviço de análise de falhas são no espaço de nomes System.Fabric no pacote Microsoft.ServiceFabric NuGet. Para utilizar as funcionalidades do serviço de análise de falhas, inclua o pacote nuget como referência no projeto.

**PowerShell**

Para utilizar o PowerShell, tem de instalar o SDK de Service Fabric. Depois do SDK é instalado, o módulo do ServiceFabric PowerShell é automaticamente carregado para utilização.

## <a name="next-steps"></a>Passos seguintes
Para criar verdadeiramente serviços de escala da nuvem, é fundamental para garantir que, antes e após a implementação, que serviços podem conseguir falhas do mundo real. No mundo serviços hoje em dia, a capacidade de inovar rapidamente e mover rapidamente o código para produção é muito importante. O serviço de análise de falhas ajuda os programadores de serviço para o fazer precisamente.

Começar a testar as suas aplicações e serviços utilizando incorporada [testar cenários](service-fabric-testability-scenarios.md), ou criar os seus próprios cenários de teste utilizando o [falhas ações](service-fabric-testability-actions.md) fornecido pelo serviço de análise de falhas.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
