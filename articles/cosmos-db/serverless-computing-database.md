---
title: "Base de dados sem servidor informática - as funções do Azure e a base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como base de dados do Azure Cosmos e as funções do Azure podem ser utilizadas em conjunto para criar aplicações informáticas sem servidor condicionada por eventos."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: mimig
ms.openlocfilehash: f9bcecff4031bcf51e3885ad98da69d9be41b397
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: Base de dados sem servidor informática utilizando as funções do Azure

A computação sem servidor é tudo sobre a capacidade para se focarem em partes individuais de lógica que são repetíveis e sem monitorização de estado. Estas duas não requerem nenhuma gestão de infraestrutura e recursos consumirem apenas para os segundos ou milissegundos, são executados para. O núcleo do movimento de informático sem servidor são as funções que são disponibilizadas do ecossistema do Azure através do [das funções do Azure](https://azure.microsoft.com/services/functions).

Com a integração nativa entre o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e as funções do Azure, pode criar os acionadores de base de dados, enlaces de entrada e saída enlaces diretamente a partir da sua conta de base de dados do Azure Cosmos. Utilizar as funções do Azure e Azure Cosmos DB, pode criar e implementar aplicações sem servidor condicionada por eventos com acesso de latência baixa avançado dados para um utilizador global base.

## <a name="overview"></a>Descrição geral

BD do Azure do Cosmos e as funções do Azure permitem-lhe integrar as suas bases de dados e aplicações sem servidor das seguintes formas:

* Criar um condicionada por eventos **acionador de base de dados do Azure Cosmos** numa função do Azure. Este acionador baseia-se na [alterar feed](change-feed.md) fluxos para monitorizar o contentor do Azure Cosmos DB para as alterações. Quando são efetuadas quaisquer alterações a um contentor, a alteração de feed de fluxo é enviada para o acionador, que invoca a função do Azure.
* Em alternativa, vincular uma função do Azure para uma coleção de base de dados do Azure Cosmos utilizando um **enlace de entrada**. Enlaces de entrada ler dados a partir de um contentor quando executa uma função.
* Vincular a uma função para uma coleção de base de dados do Azure Cosmos utilizando um **vínculo de saída**. Enlaces de saída escrever dados para um contentor ao concluir a uma função.

O diagrama seguinte ilustra cada uma destas três integrações: 

![Como integram a base de dados do Azure Cosmos e as funções do Azure](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

O acionador BD do Cosmos Azure, o enlace de entrada e o enlace de saída podem ser utilizados nas combinações seguintes:
* Um acionador de base de dados do Azure Cosmos pode ser utilizado com um enlace de saída para um contentor do Azure Cosmos BD diferentes. Depois de uma função executa uma ação num item do feed de alteração é possível escrever a outro contentor (escrita ao mesmo contentor provém eficazmente criaria um ciclo de recursiva). Em alternativa, pode utilizar um acionador de base de dados do Azure Cosmos para migrar eficazmente alteradas todos os itens a partir de um contentor para um contentor diferente, com a utilização de um enlace de saída.
* Enlaces de entrada e saída enlaces para a base de dados do Azure Cosmos podem ser utilizados na mesma função do Azure. Isto funciona bem em casos quando pretender localizar certos dados com o enlace de entrada, modificá-lo na função do Azure e, em seguida, guarde-o mesmo contentor ou de um contentor diferente, após a modificação.
* Um enlace de entrada para um contentor de BD do Cosmos Azure pode ser utilizado na mesma função que um acionador de base de dados do Azure Cosmos e pode ser utilizado com ou sem uma saída, bem como de enlace. Pode utilizar esta combinação para aplicar as informações de intercâmbio de moeda atualizado (solicitadas com um enlace de entrada para um contentor do exchange) para o feed de alteração das ordens de novo no seu serviço de carrinho de compras. Total carrinho de compras atualizada, com a conversão de moeda atual aplicada, pode ser escrito para um terceiro contentor utilizando um enlace de saída.

> [!NOTE]
> Neste momento, a base de dados do Azure Cosmos acionador, enlaces de entrada e enlaces de saída funcionam com contas DocumentDB, tabela e Graph API.

## <a name="use-cases"></a>Casos de utilização

Casos de utilização seguintes demonstram algumas formas que pode efetuar a maioria dos seus dados de base de dados do Azure Cosmos - ligando-se os dados para funções de Azure condicionada por eventos.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT utilizar maiúsculas e minúsculas - acionador de base de dados do Azure Cosmos e enlace de saída

Implementações do IoT, pode invocar uma função quando claro do motor de verificação é apresentado num carro ligado.

**Implementação:** utilizar um acionador de base de dados do Azure Cosmos e um enlace de saída

1. Um **acionador de base de dados do Azure Cosmos** é utilizado para acionar eventos relacionados com alertas carro, tais como claro motor verificação futuras de um carro ligado.
2. Quando é fornecido claro do motor de verificação, os dados de sensor são enviados à base de dados do Azure Cosmos.
3. BD do Azure do Cosmos cria ou atualiza novos documentos de dados de sensor, em seguida, essas alterações são transmissão em fluxo para o acionador de base de dados do Azure Cosmos.
4. O acionador é invocado em todas as alterações de dados para a recolha de dados de sensor, como todas as alterações são transmitidas em sequência através da alteração do feed.
5. Uma condição de limiar é utilizada na função para enviar os dados de sensor para o departamento de garantia.
6. Se a temperatura também é efetuada através de um determinado valor, é também enviado um alerta para o proprietário.
7. O **vínculo de saída** da função de registo carro numa coleção de base de dados do Azure Cosmos outro para armazenar informações sobre o evento de motor de verificação de atualizações.

A imagem seguinte mostra o código de escrita no portal do Azure para este acionador.

![Criar um acionador de BD do Cosmos do Azure no portal do Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Financeiros caso - acionador de temporizador de utilização e enlace de entrada

Implementações financeiros, pode invocar uma função quando um saldo da conta do banco está sob um determinado período.

**Implementação:** enlace de entrada de um acionador de temporizador com uma base de dados do Cosmos do Azure

1. Utilizar um [acionador de temporizador](../azure-functions/functions-bindings-timer.md), pode obter as informações de saldo da conta do banco armazenadas no contentor base de dados do Azure Cosmos intervalos excedeu o tempo limite a utilizar um **enlace de entrada**.
2. Se o saldo for inferior ao limiar de saldo baixa definido pelo utilizador, em seguida, siga cópias de segurança com uma ação na função do Azure.
3. O enlace de saída pode ser um [SendGrid integração](../azure-functions/functions-bindings-sendgrid.md) que envia uma mensagem de e-mail de uma conta de serviço para os endereços de correio eletrónico identificados para cada uma das contas saldo baixa.

As imagens seguintes mostram o código no portal do Azure para este cenário.

![Ficheiro Index.js para um acionador de temporizador para um cenário financeiro](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Ficheiro Run.csx para um acionador de temporizador para um cenário financeiro](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Jogos utilizar maiúsculas e minúsculas - acionador de base de dados do Azure Cosmos e enlace de saída

No jogos, quando é criado um novo utilizador pode procurar outros utilizadores que poderão saber utilizando o [Graph API do Azure Cosmos DB](graph-introduction.md). Em seguida, pode escrever os resultados para um [base de dados de tabela de base de dados do Azure Cosmos](table-introduction.md) para obtenção fácil.

**Implementação:** utilizar um acionador de base de dados do Azure Cosmos e um enlace de saída

1. Utilizar uma base de dados do Azure Cosmos [base de dados do gráfico](graph-introduction.md) para armazenar todos os utilizadores, pode criar uma nova função com um acionador de base de dados do Azure Cosmos. 
2. Sempre que é inserido um novo utilizador, a função é invocada e, em seguida, o resultado é armazenado utilizando um **vínculo de saída**.
3. A função de consulta a base de dados do gráfico para procurar todos os utilizadores que estão diretamente relacionadas com o novo utilizador e devolve esse conjunto de dados para a função.
4. Estes dados, em seguida, são armazenados na base de dados do Cosmos Azure [base de dados de tabela](table-introduction.md) como um conjunto de chave-valor de pares, que, em seguida, pode ser facilmente obtido por qualquer aplicação front-end que mostra o novo utilizador os amigos ligados.

### <a name="retail-use-case---multiple-functions"></a>Caso de utilização de revenda - várias funções

Implementações de revenda, quando um utilizador adicione um item para as respetivas cesto tem agora a flexibilidade para criar e invocar funções para componentes de pipeline de negócio opcional.

**Implementação:** escuta de uma coleção de acionadores de múltiplas base de dados do Azure Cosmos

1. Pode criar várias funções do Azure ao adicionar a base de dados do Azure Cosmos acionadores cada - todos os que escutam a mesma alterar feed de dados de carrinho de compras. Tenha em atenção que, quando várias funções de escutam o mesmo alterar feed, uma nova coleção de concessão é necessária para cada função.
2. Sempre que um novo item é adicionado a um carrinho de compras de utilizadores, cada função de forma independente é invocada pela partir do contentor de carrinho de compras de feed de alteração.
    * Uma função pode utilizar o conteúdo o cesto atual para alterar a apresentação de outros itens que o utilizador poderão estar interessado.
    * Outra função, poderá atualizar totais de inventário.
    * Outra função pode enviar informações de cliente para determinados produtos para o departamento de marketing, o que envia-as um mailer promocional. 

    Qualquer departamento pode criar um acionador de base de dados do Azure Cosmos através da escuta para o feed de alteração e certifique-se de que não atrasar o processamento dos eventos críticos ordem do processo.

Todos estes casos de utilização, porque a função foi desassociada aplicação em si, não precisa de rotação cópias de segurança novas instâncias de aplicação sempre. Em vez disso, as funções do Azure spins cópias de segurança individuais funções para a conclusão processos discretos conforme necessário.

## <a name="tooling"></a>As ferramentas

Integração nativa entre a base de dados do Azure Cosmos e as funções do Azure está disponível no portal do Azure.
* No portal das funções do Azure, pode criar um acionador de base de dados do Azure Cosmos. Para obter instruções de início rápido, consulte [criar um acionador de BD do Cosmos do Azure no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc) ![criar um acionador de BD do Cosmos do Azure no portal das funções do Azure](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* No portal das funções do Azure, também pode adicionar enlaces de entrada de BD do Cosmos do Azure e os enlaces de saída para outros tipos de acionadores. Para obter instruções de início rápido, consulte [armazenar dados não estruturados com as funções do Azure e a base de dados do Cosmos](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Criar um acionador de BD do Cosmos do Azure no portal das funções do Azure](./media/serverless-computing-database/function-portal-input-binding.png)
*   No portal do Azure Cosmos DB, pode adicionar um acionador de base de dados do Azure Cosmos a uma aplicação de função do Azure existente no mesmo grupo de recursos.
    ![Criar um acionador de BD do Cosmos do Azure no portal das funções do Azure](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Por que motivo escolhê integração das funções do Azure para a computação sem servidor?

As funções do Azure fornece a capacidade de criar dimensionáveis unidades de trabalho ou concisas peças de lógica que podem ser executados a pedido, sem de aprovisionamento ou gerir a infraestrutura. Ao utilizar as funções do Azure, não tem de criar uma aplicação autêntica para responder a alterações na base de dados do Azure Cosmos DB, pode criar funções reutilizáveis pequenas para tarefas específicas. Além disso, também pode utilizar os dados de base de dados do Azure Cosmos como entrada ou saída para uma função do Azure em resposta a eventos, tal como um HTTP pedidos ou de um acionador excedeu o tempo limite.

BD do Cosmos do Azure é a base de dados recomendada para a arquitetura de computação sem servidor pelos seguintes motivos:

* **Instantânea acesso a todos os seus dados**: tiver acesso granular para cada valor armazenado porque Azure Cosmos DB [indexa automaticamente](indexing-policies.md) todos os dados por predefinição e disponibiliza imediatamente os índices. Isto significa que é capazes de constantemente consultar, atualizar e adicionar novos itens a sua base de dados e se tem acesso instantânea através das funções do Azure.

* **Não tem esquemas**. BD do Azure do Cosmos é sem esquemas - para que seja exclusivamente capaz de lidar com qualquer saída de dados de uma função do Azure. Esta abordagem "processar tudo" torna simples para criar uma variedade de funções que todas as saídas BD do Cosmos do Azure.

* **Débito dimensionável**. Débito pode ser escalado para cima e para baixo instantaneamente do BD Azure Cosmos. Se tiver centenas ou milhares de funções de consulta e a escrita na mesma coleção, pode dimensionar a sua [RU/s](request-units.md) para processar a carga. Todas as funções podem trabalhar em paralelo utilizando a sua alocado RU/s e os dados são garantidos ser [consistente](consistency-levels.md).

* **Replicação global**. Pode replicar dados de base de dados do Azure Cosmos [à volta de todo o mundo](distribute-data-globally.md) para reduzir a latência, localizar a georreplicação os dados mais próximo de onde são os seus utilizadores. Como com todas as consultas de base de dados do Azure Cosmos, ler dados a partir de acionadores condicionada por eventos é dados da base de dados do Cosmos Azure mais próxima ao utilizador.

Se estiver à procura para integrar com as funções do Azure para armazenar dados e não precisa de indexação avançada ou se tiver de armazenar anexos e ficheiros de suporte de dados, o [acionador do Blob Storage do Azure](../azure-functions/functions-bindings-storage-blob.md) pode ser uma opção melhor.

Benefícios das funções do Azure: 

* **Condicionada por eventos**. As funções do Azure são condicionada por eventos e podem escutar a uma alteração da base de dados do Azure Cosmos do feed. Isto significa que não precisa de criar o serviço de escuta lógica, apenas atento para que as alterações que está a escutar. 

* **Não existem limites**. As funções de executar em paralelo e o serviço spins segurança tantos conforme necessário. Defina os parâmetros.

* **Boa para tarefas rápidas**. O serviço spins cópias de segurança novas instâncias de funções sempre que um evento é acionado e fecha-os assim a função for concluída. Apenas paga a hora a que se executam as suas funções.

Se não tiver a certeza se fluxo, as Logic Apps, as funções do Azure ou WebJobs são mais adequados para a sua implementação, consulte o artigo [escolha entre fluxo, as Logic Apps, funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Passos seguintes

Agora vamos ligar BD do Cosmos do Azure e as funções do Azure por real: 

* [Criar um acionador de BD do Cosmos do Azure no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Criar um acionador de HTTP de funções do Azure com um enlace de entrada de base de dados do Azure Cosmos](https://aka.ms/cosmosdbinputbind)
* [Armazenar dados não estruturados com as funções do Azure e a base de dados do Cosmos](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [BD do Cosmos enlaces do Azure e é acionado](../azure-functions/functions-bindings-documentdb.md)


 



