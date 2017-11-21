---
title: "Introdução ao Cassandra do Cosmos BD do Azure API | Microsoft Docs"
description: "Saiba como pode utilizar a base de dados do Azure Cosmos para aplicações existentes \"comparação de precisão e shift\" e criar novas aplicações utilizando a API de Cassandra utilizando os controladores de Cassandra e CQL já estiver familiarizado com."
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: fe911b69278021262f1d90f2157bb78ca48db0db
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="introduction-to-apache-cassandra-api-for-azure-cosmos-db"></a>Introdução ao Apache Cassandra API para o Azure Cosmos DB

BD do Azure do Cosmos fornece a API de Cassandra (pré-visualização) para aplicações que são escritas para Apache Cassandra que precisam de capacidades premium, como:

* [Tamanho de armazenamento escalável e débito](partition-data.md).
* [Ative a chave distribuição global](distribute-data-globally.md)
* Latências de milissegundos na ordem de um dígito no percentil 99.º.
* [Cinco níveis de consistência bem definidos](consistency-levels.md)
* [A indexação automática de dados](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem necessidade de lidar com a gestão de esquema e o índice. 
* Garantida elevada disponibilidade, todas as cópia por [líder da indústria SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>O que é o Azure Cosmos DB Apache Cassandra API?

BD do Azure do Cosmos pode ser utilizado como o arquivo de dados para aplicações escritas para [Apache Cassandra](https://cassandra.apache.org/), utilizando a API do Apache Cassandra. Isto significa que, ao utilizar existente [Apache licenciado controladores compatíveis com CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver), a aplicação de escrita para Cassandra agora pode comunicar com o Azure Cosmos DB Cassandra API. Em muitos casos, pode mudar de utilizar o Apache Cassandra a utilização Apache Cassandra API da BD do Azure Cosmos, alterando simplesmente uma cadeia de ligação. Utilizar esta funcionalidade, pode facilmente criar e executar aplicações de base de dados de Cassandra API do Azure na nuvem com distribuição global da BD do Azure Cosmos e [abrangentes líder da indústria SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db), enquanto continua a utilizar familiar as competências e ferramentas para Cassandra API.

![Cassandra API no Azure Cosmos DB](./media/cassandra-introduction/cosmosdb-cassandra.png)

A API de Cassandra permite-lhe interagir com os dados armazenados na base de dados do Cosmos do Azure utilizando as ferramentas de idioma de consulta Cassandra com base em (como CQLSH) e os controladores cliente Cassandra já estiver familiarizado com. Saiba mais sobre o assunto neste vídeo Microsoft Mechanics com o Gestor de engenharia do Principal Kirill Gavrylyuk.

> [!VIDEO https://www.youtube.com/embed/1Sf4McGN1AQ]
>

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>O que é a vantagem de utilizar o Apache Cassandra API para a base de dados do Azure Cosmos?

**Nenhuma gestão de operações**: como um valor true completamente gerido serviço, BD do Cosmos Azure garante que os administradores de Cassandra API não dispõe de preocupar com a gerir e monitorizar um conjunto de definições em ficheiros de SO, JVM e yaml e os respetivos interplay. BD do Azure do Cosmos fornece monitorização de débito, latência, armazenamento e disponibilidade e alertas configuráveis. 

**Gestão de desempenho**: base de dados do Azure Cosmos fornece SLA de segurança baixa latência leituras e escritas para o percentil 99th. Os utilizadores precisam de preocupar com muita sobrecarga operacional para fornecer boa leitura e escrita SLAs. Estes incluem, geralmente, agendamento compactação, gestão de tombstones, filtros de bloom definição e lags de réplica. Azure Cosmos DB demora ausente preocupe de gerir estes problemas e permite-lhe focar-se no materiais a entregar a aplicação.

**A indexação automática**: base de dados do Azure Cosmos indexa automaticamente todas as colunas da tabela na base de dados Cassandra API. BD do Azure do Cosmos não necessita de criação de índices secundários para acelerar a consultas. Fornece a experiência de latência baixa leitura e escrita ao efetuar a indexação consistente automática. 

**Capacidade de utilizar o código existente e ferramentas**: base de dados do Azure Cosmos fornece compatibilidade de nível de protocolo de transmissão com existentes SDKs e ferramentas. Este compatibilidade assegura que pode utilizar existentes código base com Cassandra API do Cosmos BD do Azure com alterações Triviais.

**Elasticidade débito e armazenamento**: plataforma Azure Cosmos fornece elasticidade de débito garantida em regiões através do simple portal, PowerShell ou as operações de CLI. Pode dimensionar as tabelas de base de dados do Azure Cosmos com um desempenho previsível integrado à medida que aumenta a sua aplicação. BD do Azure do Cosmos suporta tabelas Cassandra API que podem ser dimensionados para tamanhos de armazenamento virtualmente ilimitados. 

**Distribuição global e disponibilidade**: base de dados do Azure Cosmos fornece a capacidade de distribuir dados em toda a regiões do Azure para permitir que os utilizadores com uma experiência de baixa latência ao garantir a disponibilidade. BD do Azure do Cosmos fornece 99,99% de disponibilidade numa região e 99.999% ler disponibilidade em regiões com nenhuma sobrecarga de operações. BD do Cosmos do Azure está disponível no 30 + [regiões do Azure](https://azure.microsoft.com/regions/services/). Saiba mais em [distribuir dados globalmente](distribute-data-globally.md). 

**Escolha de consistência**: base de dados do Azure Cosmos fornece a opção de cinco níveis de consistência bem definidos para alcançar entre a consistência e desempenho. Estes níveis de consistência são forte, consistência vinculada, sessão, prefixo consistente e eventual. Estes níveis de consistência granular e bem definidos permitem programador tornar o som trade-offs entre consistência, disponibilidade e a latência. Saiba mais em [Using consistency levels to maximize availability and performance](consistency-levels.md) (Utilizar níveis de consistência para maximizar a disponibilidade e desempenho). 

**Nível de Enterprise**: segura e compatível por predefinição – cosmos do Azure fornece DB [certificações de conformidade](https://www.microsoft.com/trustcenter) para garantir que os utilizadores podem utilizar a plataforma sem ter de se preocupar sobre problemas de conformidade. BD do Azure do Cosmos também proporciona a encriptação em descanso e em movimento, firewall IP e os registos de auditoria para as atividades de plane do controlo.  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>Registe-se já 

Se já tiver uma subscrição do Azure, pode inscrever aderir ao programa de API Cassandra (pré-visualização) [portal do Azure](https://aka.ms/cosmosdb-cassandra-signup).  Se estiver familiarizado com o Azure, inscreva-se um [avaliação gratuita](https://azure.microsoft.com/free) onde pode obter de acesso gratuito a base de dados do Azure Cosmos de 12 meses. Conclua os seguintes passos para pedir acesso para o programa de API Cassandra (pré-visualização).

1. No [portal do Azure](https://portal.azure.com), clique em **novo** > **bases de dados** > **Azure Cosmos DB**. 

2. Na página de nova conta, selecione **Cassandra** na caixa de API. 

3. No **subscrição** caixa, selecione a subscrição do Azure que pretende utilizar para esta conta.

4. Clique em **inscrever-se na pré-visualização hoje mesmo**.

    ![Cassandra API no Azure Cosmos DB](./media/cassandra-introduction/cassandra-sign-up.png)

3. Na inscrição para pré-visualizar painel atual, clique em **OK**. 

    Depois de submeter o pedido, o estado é alterado para **com aprovação pendente** no painel de conta de novo. 

Depois de submeter o pedido, aguarde que a notificação por correio eletrónico que o seu pedido tiver sido aprovado. Devido ao elevado volume de pedidos, deverá receber a notificação dentro de uma semana. Não é necessário criar um pedido de suporte para concluir o pedido. Pedidos serão analisados pela ordem em que foram recebidos. 

## <a name="how-to-get-started"></a>Como começar
Uma vez tiver associado a um programa de pré-visualização, siga os inícios rápidos Cassandra API para criar uma aplicação utilizando a API de Cassandra:

* [Início rápido: Criar um Cassandra aplicação web com o Node.js e base de dados do Azure Cosmos](create-cassandra-nodejs.md)
* [Início rápido: Criar um Cassandra aplicação web com o Java e base de dados do Azure Cosmos](create-cassandra-java.md)
* [Início rápido: Criar um Cassandra aplicação web com o .NET e da base de dados do Azure Cosmos](create-cassandra-dotnet.md)
* [Início rápido: Criar um Cassandra aplicação web com o Python e Azure Cosmos DB](create-cassandra-python.md)

## <a name="next-steps"></a>Passos seguintes

Informações sobre a API do Azure Cosmos DB Cassandra estão integradas a documentação de base de dados do Azure Cosmos geral, mas aqui estão alguns ponteiros para começar:

* Siga o [inícios Rápidos](create-cassandra-nodejs.md) para criar uma conta e uma nova aplicação com um exemplo de Git
* Siga o [Tutorial](tutorial-develop-cassandra-java.md) para criar uma nova aplicação através de programação.
* Siga o [tutorial de dados de importar Cassandra](cassandra-import-data.md) para importar os dados existentes para a base de dados do Azure Cosmos.
* Leia o [FAQ](faq.md#cassandra).
