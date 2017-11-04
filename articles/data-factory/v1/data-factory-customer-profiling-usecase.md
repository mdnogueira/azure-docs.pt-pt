---
title: "Caso de Utilização - Criação de Perfis de Cliente"
description: "Saiba como o Azure Data Factory é utilizado para criar um condicionada por dados de fluxo de trabalho (pipeline) para criar um perfil de clientes de jogos."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: fcba76555902fb393830f352e6274b3ecd2fba38
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="use-case---customer-profiling"></a>Caso de Utilização - Criação de Perfis de Cliente
O Azure Data Factory é um dos muitos serviços utilizados para implementar o Cortana Intelligence Suite de Aceleradores de solução.  Para obter mais informações sobre a Cortana Intelligence, visite [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Neste documento, vamos descrever um caso de utilização simples para ajudar a começar por compreender a forma como o Azure Data Factory pode resolver problemas comuns de análise.

## <a name="scenario"></a>Cenário
Contoso é uma empresa de jogos cria jogos para várias plataformas: jogos consolas, mão contido dispositivos e computadores pessoais (PCs). Como jogadores reproduzir estes jogos, é produzido grande volume de dados de registo que controla os padrões de utilização, o estilo de jogos e as preferências do utilizador.  Quando combinada com demográficos, regionais e dados de produto, Contoso pode efetuar análises para ajudá-los sobre como melhorar a experiência dos jogadores adquire de destino-las para atualizações e em jogos. 

Objetivo da Contoso é identificar oportunidades de cópia de segurança-vende/cross-vende com base no histórico de jogos do respetivos jogadores e adicionar funcionalidades convincentes ao crescimento de negócio de unidade e fornecer uma melhor experiência aos clientes. Neste caso de utilização, utilizamos uma empresa de jogos como um exemplo de uma empresa. A empresa pretende otimizar os jogos com base no comportamento dos jogadores. Estes princípios aplicam-se para as empresas que pretender interagir com os seus clientes em torno da respetiva bens e serviços e melhorar a experiência dos seus clientes.

Nesta solução, a Contoso pretende avaliar a eficácia de uma campanha de marketing que recentemente tem a ser iniciada. Iremos começar com os registos de jogos não processados, processar enriqueça-los com os dados de geolocalização, associar de dados de referência de publicidade e por último copie-os para uma base de dados do SQL do Azure para analisar o impacto da campanha.

## <a name="deploy-solution"></a>Implementar a solução
Tudo o que precisa para aceder e experimentar este caso de utilização simples é um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/), um [conta de armazenamento de Blobs do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)e um [SQL Database do Azure](../../sql-database/sql-database-get-started.md). Implementar o cliente de criação de perfis de pipeline do **pipelines de exemplo** mosaico na home page da fábrica de dados.

1. Criar uma fábrica de dados ou abrir a fábrica de dados existente. Consulte [copiar dados de armazenamento de BLOBs para base de dados SQL utilizando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter os passos criar uma fábrica de dados.
2. No **DATA FACTORY** painel data Factory, clique em de **pipelines de exemplo** mosaico.

    ![Mosaico de pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. No **pipelines de exemplo** painel, clique em de **cliente de criação de perfis** que pretende implementar.

    ![Painel de pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especificar definições de configuração para o exemplo. Por exemplo, o nome da conta de armazenamento do Azure e chave, nome do servidor SQL do Azure, base de dados, ID de utilizador e palavra-passe.

    ![Painel de exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar de especificar as definições de configuração, clique em **criar** criar/implementar o exemplo pipelines e serviços/tabelas ligadas utilizadas pelo pipelines.
6. Ver o estado de implementação no mosaico de exemplo em que clicou no anteriormente o **pipelines de exemplo** painel.

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir o **implementação concluída com êxito** mensagem no mosaico para o exemplo, feche o **pipelines de exemplo** painel.  
8. No **DATA FACTORY** painel, pode ver que os serviços ligados, conjuntos de dados e pipelines são adicionadas à fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Descrição geral da solução
Neste caso, utilize simples pode ser utilizado como um exemplo de como pode utilizar o Azure Data Factory para inserir, preparar, transformar, analisar e publicar dados.

![Fluxo de trabalho ponto a ponto](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Este figura ilustra como o pipelines de dados são apresentados no portal do Azure depois de ter implementados.

1. O **PartitionGameLogsPipeline** lê os eventos não processados de jogos a partir do blob storage e cria partições com base no ano, mês e dia.
2. O **EnrichGameLogsPipeline** associa particionados eventos jogos com dados de referência de código georreplicação e otimiza os dados ao mapear endereços IP para as localizações de georreplicação correspondentes.
3. O **AnalyzeMarketingCampaignPipeline** pipeline utiliza os dados avançados e processa-os com os dados de publicidade para criar o resultado final que contém marketing eficácia de campanha.

Neste exemplo, a fábrica de dados é utilizada para orquestrar as atividades que copiar dados de entrada, transformação e processar os dados e os dados finais para uma base de dados do SQL do Azure de saída.  Também pode visualizar a rede de pipelines de dados, geri-los e monitorizar o estado da IU.

## <a name="benefits"></a>Benefícios
Otimizar a análise do respetivo perfil de utilizador e alinhá-lo com os objetivos de negócio, a empresa jogos é capaz de rapidamente padrões de utilização de recolher e analisar a eficácia do respetivas campanhas de marketing.

