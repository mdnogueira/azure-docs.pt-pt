---
title: "Armazenamento frequente, esporádico e de arquivos para blobs | Microsoft Docs"
description: "Armazenamento frequente, esporádico e de arquivos para contas de Armazenamento de blobs do Azure"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 544b11d74a926fe62b8ceca51570ce9d2ee7e6e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Armazenamento de Blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivos (pré-visualização)

## <a name="overview"></a>Descrição geral

O Armazenamento do Azure disponibiliza três camadas de armazenamento para o Armazenamento de objetos de blobs, para que possa armazenar os seus dados de forma mais económica consoante o modo como os utiliza. A **camada de armazenamento de acesso frequente** do Azure está otimizada para armazenar dados que são acedidos com frequência. A **camada de armazenamento de acesso esporádico** do Azure está otimizada para armazenar dados que são acedidos com pouca frequência e armazenados durante um mês, pelo menos. A [camada de armazenamento de arquivos (pré-visualização)](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) está otimizada para armazenar dados que são raramente acedidos e armazenados durante, pelo menos, seis meses, com requisitos de latência flexíveis (na ordem das horas). A camada de armazenamento de *arquivos* só pode ser utilizada ao nível dos blobs e não ao nível de toda a conta de armazenamento. Os dados na camada de armazenamento de acesso esporádico podem tolerar disponibilidade ligeiramente inferior, mas ainda requerem uma durabilidade elevada, bem como tempo de acesso e características de débito semelhantes aos dados de acesso frequente. Relativamente aos dados de acesso esporádico e de arquivo, os custos de acesso superiores e um SLA com uma disponibilidade ligeiramente inferior são perdas aceitáveis face aos custos de armazenamento muito mais reduzidos.

Atualmente, os dados armazenados na nuvem estão a crescer a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado. Os dados armazenados na nuvem podem ser diferentes em termos da forma como são gerados, processados e acedidos ao longo da respetiva duração. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na nuvem e são raramente acedidos após serem armazenados ou não são acedidos de todo.

Cada um destes cenários de acesso a dados beneficia de uma camada diferenciada de armazenamento, otimizada para um padrão de acesso específico. Com as camadas de armazenamento frequente, esporádico e de arquivo, o Armazenamento de blobs do Azure dá resposta a esta necessidade de camadas de armazenamento diferenciadas com modelos de preços distintos.

## <a name="blob-storage-accounts"></a>Contas do Blob Storage

As **Contas do Blob Storage** são contas do Storage especializadas para armazenar os dados não estruturados como blobs (objetos) no Storage do Azure. Com as contas do Armazenamento de blobs, pode agora escolher as camadas de armazenamento frequente e esporádico ao nível da conta, ou as camadas frequentes, esporádicas e de arquivo ao nível do blob, com base nos padrões de acesso. Armazene os dados inativos e raramente acedidos ao custo de armazenamento mais baixo, os dados inativos acedidos com menos frequência a um custo de armazenamento inferior face aos dados frequentes e armazene os dados ativos acedidos mais frequentemente ao custo de acesso mais baixo. As contas de Armazenamento de blobs são semelhantes às contas de armazenamento para fins gerais existentes e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho que utiliza atualmente, incluindo 100 por cento de consistência com a API dos blobs de blocos e dos blobs de acréscimo.

> [!NOTE]
> As contas de armazenamento de Blobs suportam apenas blobs de blocos e blobs de acréscimo e não suportam blobs de páginas.

As contas do Armazenamento de Blobs expõem o atributo **Access Tier**, que permite especificar a camada de armazenamento como de acesso **Frequente** ou **Esporádico**, consoante os dados armazenados na conta. Se o padrão de utilização dos dados sofrer uma alteração, pode também alternar entre estas camadas de armazenamento em qualquer altura. A camada de arquivo (pré-visualização) só pode ser aplicada ao nível do blob.

> [!NOTE]
> A alteração da camada de armazenamento poderá resultar em encargos adicionais. Veja a secção [Preços e faturação](#pricing-and-billing) para obter mais detalhes.

### <a name="hot-access-tier"></a>Escalão de acesso frequente

Os exemplos de cenários de utilização da camada de armazenamento frequente incluem:

* Dados que estão a ser utilizados ativamente ou que deverão ser acedidos (para operações de leitura e escrita) com frequência.
* Dados preparados para processamento e eventual migração para a camada de armazenamento de acesso esporádico.

### <a name="cool-access-tier"></a>Escalão de acesso esporádico

Os exemplos de cenários de utilização da camada de armazenamento esporádico incluem:

* Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
* Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
* Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

### <a name="archive-access-tier-preview"></a>Camada de acesso de arquivo (pré-visualização)

O [armazenamento de arquivos](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) tem o custo de armazenamento mais baixo e os custos de obtenção de dados mais altos em comparação com o armazenamento frequente e esporádico.

Enquanto os blobs estão no armazenamento de arquivo, não podem ser lidos, copiados, substituídos ou modificados. Do mesmo modo, também não pode criar instantâneos de blobs no armazenamento de arquivo. Contudo, pode utilizar operações existentes para eliminar, listar, obter propriedades/metadados de blobs ou alterar a camada dos seus blobs. Para ler os dados no armazenamento de arquivo, tem de, primeiro, alterar a camada do blob para frequente ou esporádica. Este processo é conhecido como “reidratação” e pode demorar até 15 horas a entrar em vigor para blobs com menos de 50 GB. O tempo necessário para blobs maiores varia consoante o limite de débito dos mesmos.

Durante a “reidratação”, pode verificar a propriedade “estado do arquivo” do blob para confirmar se a camada foi alterada. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “estado do arquivo” do blob é removida e a propriedade “camada de acesso” reflete a frequente ou esporádica.  

Os exemplos de cenários de utilização da camada de armazenamento de arquivo incluem:

* Conjuntos de dados de cópias de segurança, arquivo e recuperação após desastre de longo prazo
* Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,*, ficheiros de multimédia não processados após a transcodificação noutros formatos)
* Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo,*, filmagens de câmaras de segurança, raios X/RMs antigos para organizações de cuidados de saúde, gravações de áudio e transcrições de chamadas dos clientes para serviços financeiros)

### <a name="recommendations"></a>Recomendações

Consulte [Acerca das contas do Storage do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações sobre contas do Storage.

Para as aplicações que requerem apenas o Blob Storage de blocos ou de acréscimo, recomendamos que utilize contas do Blob Storage, de modo a tirar partido do modelo de preços diferenciado do armazenamento em camadas. No entanto, compreendemos que tal pode não ser possível em determinadas circunstâncias em que a utilização de contas do Storage para fins gerais seria mais adequada, tais como:

* Tem de utilizar tabelas, filas ou ficheiros e pretende que os blobs estejam armazenados na mesma conta do Storage. Tenha em atenção que o armazenamento destes elementos na mesma conta não tem qualquer vantagem técnica além da utilização das mesmas chaves partilhadas.

* Ainda tem de utilizar o modelo de implementação clássica. As contas do Blob Storage estão disponíveis apenas através do modelo de implementação Azure Resource Manager.

* Tem de utilizar blobs de páginas. As contas do Blob Storage não suportam blobs de páginas. Normalmente, recomendamos que utilize blobs de blocos, a menos que necessite especificamente de blobs de páginas.

* Utiliza uma versão da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior a 14/02/2014 ou uma biblioteca de cliente com uma versão inferior à 4.x e não pode atualizar a sua aplicação.

> [!NOTE]
> Atualmente, as contas do armazenamento de Blobs são suportadas em todas as regiões do Azure.
 

## <a name="blob-level-tiering-feature-preview"></a>Funcionalidade de camadas ao nível de blobs (pré-visualização)

Agora, as camadas ao nível do blob permitem-lhe alterar a camada dos seus dados ao nível do objeto através de uma operação individual, chamada [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Pode alterar facilmente a camada de acesso de um blob de entre as camadas frequente, esporádica ou de arquivo à medida que os padrões de utilização se modificam, sem ter de mover dados entre contas. Todas as alterações às camadas entram em vigor imediatamente, exceto se um blob estiver a ser reidratado do arquivo. Podem coexistir na mesma conta blobs nas três camadas de armazenamento. Um blob que não tenha uma camada atribuída explicitamente herda a camada da definição da camada de acesso da conta.

Para utilizar estas funcionalidades em pré-visualização, siga as instruções no [anúncio do blogue Azure Archive and Blob-Level Tiering](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) (Camadas de Arquivo e ao Nível do Blob do Azure).

Abaixo, encontram-se algumas restrições que se aplicam durante a pré-visualização da camada ao nível do blob:

* O armazenamento de arquivo só é suportado em contas do Armazenamento de blobs novas criadas na região E.U.A. Leste 2 após inscrição bem-sucedida na pré-visualização.

* A camada ao nível do blob só é suportada em contas do Armazenamento de blobs novas criadas em regiões públicas após inscrição bem-sucedida na pré-visualização.

* A camada ao nível do blob e o armazenamento de arquivo só são suportados no armazenamento [LRS] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage). Os armazenamentos [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) e [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage) serão suportados no futuro.

* Não pode alterar a camada de um blob com instantâneos.

* Não pode copiar ou criar instantâneos de blobs no armazenamento de arquivo.

## <a name="comparison-of-the-storage-tiers"></a>Comparação das camadas de armazenamento

A tabela seguinte mostra uma comparação das camadas de armazenamento frequente e esporádico. A camada ao nível do blob de arquivo está em pré-visualização, pelo que não existe nenhum SLA para a mesma.

| | **Camada de armazenamento frequente** | **Camada de armazenamento esporádico** |
| ---- | ----- | ----- |
| **Disponibilidade** | 99,9% | 99% |
| **Disponibilidade** <br> **(leituras RA-GRS)**| 99,99% | 99,9% |
| **Custos de utilização** | Custos de armazenamento superiores, custos de acesso e transação mais baixos | Custos de armazenamento inferiores, custos de acesso e transação superiores |
| **Tamanho mínimo do objeto** | N/D | N/D |
| **Duração mínima do armazenamento** | N/D | N/D |
| **Latência** <br> **(Tempo até ao primeiro byte)** | milissegundos | milissegundos |
| **Metas de escalabilidade e desempenho** | Igual às contas do Storage para fins gerais | Igual às contas do Storage para fins gerais |

> [!NOTE]
> As contas do Blob Storage suportam as mesmas metas de desempenho e escalabilidade que as contas do Storage para fins gerais. Consulte [Metas de Desempenho e Escalabilidade do Storage do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações.


## <a name="pricing-and-billing"></a>Preços e faturação
As contas do Armazenamento de blobs utilizam um modelo de preços para o armazenamento de blobs com base na camada de armazenamento. Ao utilizar uma conta do Blob Storage, aplicam-se as seguintes considerações de faturação:

* **Custos de armazenamento**: para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de armazenamento. O custo por gigabyte é menor para a camada de armazenamento esporádico do que para a camada de armazenamento frequente.

* **Custos do acesso a dados**: para os dados na camada de armazenamento esporádico, é cobrada uma taxa de acesso a dados por gigabyte pelas operações de leitura e escrita.

* **Custos de transação**: é cobrada uma taxa por transação para ambas as camadas. No entanto, o custo por transação para a camada de armazenamento esporádico é superior ao da camada de armazenamento frequente.

* **Custos de transferência de dados de georreplicação**: aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.

* **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.

* **Alteração da camada de armazenamento**: a alteração da camada de armazenamento esporádico para armazenamento frequente está sujeita à cobrança de uma taxa igual à leitura de todos os dados existentes na conta do armazenamento para cada transição. Por outro lado, a alteração da camada de armazenamento frequente para armazenamento esporádico é gratuita.

> [!NOTE]
> Para obter mais detalhes sobre o modelo de preços das contas do Armazenamento de blobs, veja a página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais detalhes sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart"></a>Início Rápido

Nesta secção, vamos demonstrar os seguintes cenários através do portal do Azure:

* Como criar uma conta do Blob Storage
* Como gerir uma conta do Blob Storage.

Nos exemplos seguintes, não pode definir a camada de acesso como arquivo porque esta definição aplica-se a toda a conta de armazenamento. O armazenamento de arquivo só pode ser definido em blobs específicos.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Criar uma conta do Armazenamento de Blobs através do portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** > **Dados + Armazenamento** > **Conta do Storage**.

3. Introduza um nome para a conta do Storage.
   
    Este nome tem de ser globalmente exclusivo; é utilizado como parte do URL utilizado para aceder aos objetos na conta de armazenamento.  

4. Selecione **Resource Manager** como o modelo de implementação.
   
    O armazenamento em camadas só pode ser utilizado com contas de armazenamento do Resource Manager; este é o modelo de implementação recomendado para novos recursos. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. Na lista pendente Tipo de Conta, selecione **Armazenamento de Blobs**.
   
    É aqui que pode seleciona o tipo de conta de armazenamento. O armazenamento em camadas não está disponível no armazenamento para fins gerais; só está disponível na conta de tipo de Armazenamento de Blobs.     
   
    Tenha em atenção que, quando seleciona esta opção, a camada de desempenho fica definida como Standard. O armazenamento em camadas não está disponível com a camada de desempenho Premium.

6. Selecione a opção de replicação para a conta do Storage: **LRS**, **GRS** ou **RA-GRS**. A predefinição é **RA-GRS**.
   
    LRS = armazenamento localmente redundante; GRS = armazenamento georredundante (2 regiões); RA-GRS é armazenamento georredundante com acesso de leitura (2 regiões com acesso de leitura para a segunda).
   
    Consulte [Azure Storage replication (Replicação do Armazenamento do Azure)](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter detalhes adicionais sobre as opções de replicação do Armazenamento do Azure.

7. Selecione a camada de armazenamento correta para as suas necessidades: defina a **Camada de acesso** como **Esporádica** ou **Frequente**. A predefinição é **Frequente**. 

8. Selecione a subscrição na qual pretende criar a nova conta do Storage.

9. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações sobre os grupos de recursos, veja [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Selecione a região para a sua conta de armazenamento.

11. Clique em **Criar** para criar a conta do Storage.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Alterar a camada de armazenamento numa conta do Armazenamento de Blobs através do portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Para navegar para a sua conta de armazenamento, selecione Todos os Recursos, em seguida, selecione a sua conta de armazenamento.

3. No painel Definições, clique em**Configuração** para ver e/ou alterar a configuração da conta.

4. Selecione a camada de armazenamento correta para as suas necessidades: defina a **Camada de acesso** como **Esporádica** ou **Frequente**.

5. Clique em Guardar na parte superior do painel.

> [!NOTE]
> A alteração da camada de armazenamento poderá resultar em encargos adicionais. Veja a secção [Preços e faturação](#pricing-and-billing) para obter mais detalhes.


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Avaliar e migrar para contas do Armazenamento de Blobs
O objetivo desta secção consiste em ajudar os utilizadores a fazerem uma transição tranquila para a utilização de contas do Armazenamento de Blobs. Há dois cenários de utilizador:

* Tem uma conta de armazenamento para fins gerais existente e pretende avaliar uma alteração para uma conta do Armazenamento de Blobs com a camada de armazenamento correta.
* Decidiu utilizar uma conta do Armazenamento de Blobs ou já tem uma e pretende avaliar se deve utilizar a camada de armazenamento frequente ou esporádico.

Em ambos os casos, a primeira prioridade é estimar os custos de armazenar e aceder aos seus dados armazenados numa conta do Armazenamento de Blobs e compará-los com os seus custos atuais.

## <a name="evaluating-blob-storage-account-tiers"></a>Avaliar camadas da conta do Armazenamento de Blobs

Para estimar os custos de armazenar e aceder aos dados armazenados numa conta do Armazenamento de blobs, tem de avaliar o seu padrão de utilização existente ou fazer uma aproximação do padrão de utilização esperado. Em geral, precisa de saber o seguinte:

* O seu consumo de armazenamento: que quantidade de dados está a ser armazenada e como é que isso muda mensalmente?

* O padrão de acesso ao armazenamento: que quantidade de dados é lida da conta e escrita na mesma (incluindo dados novos)? Quantas transações são utilizadas para acesso a dados e de que tipo são?

## <a name="monitoring-existing-storage-accounts"></a>Monitorizar contas de armazenamento existentes

Para monitorizar as suas contas de armazenamento existentes e recolher estes dados, pode utilizar a Análise do Armazenamento do Azure, que efetua o registo e fornece dados de métricas para uma conta de armazenamento. A Análise do Armazenamento pode armazenar métricas que incluem estatísticas agregadas de transações e dados de capacidade sobre pedidos para o serviço do Armazenamento de Blobs para contas de armazenamento para fins gerais e para contas do Armazenamento de Blobs. Estes dados são armazenados em tabelas bem conhecidas na mesma conta de armazenamento.

Para obter mais detalhes, veja [About Storage Analytics Metric (Sobre Métricas da Análise do Armazenamento)](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Storage Analytics Metrics Table Schema (Esquema da Tabela de Métricas da Análise do Armazenamento)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas do Armazenamento de Blobs expõem o ponto final de serviço de tabelas apenas para armazenar e aceder aos dados de métricas para essa conta.

Para monitorizar o consumo de armazenamento do serviço de Armazenamento de blobs, tem de ativar as métricas de capacidade.
Com esta opção ativada, os dados de capacidade são registados diariamente para um serviço Blob de uma conta de armazenamento e registados como uma entrada que é escrita na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorizar o padrão de acesso a dados do serviço de Armazenamento de blobs, tem de ativar as métricas de transação horária ao nível da API. Com esta opção ativada, as transações por API são agregadas ao fim de cada hora e registadas como uma entrada que é escrita na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* regista as transações para o ponto final secundário quando são utilizadas contas de armazenamento RA-GRS.

> [!NOTE]
> No caso de ter uma conta de armazenamento para fins gerais em que tem armazenados blobs de páginas e discos da máquina virtual em conjunto com dados de blob de blocos e de acréscimo, este processo de estimativa não é aplicável. Isto acontece porque não há um modo de distinguir as métricas de capacidade das de transação com base no tipo de blob apenas para blobs de blocos e de acréscimo que podem ser migrados para uma conta do Armazenamento de blobs.

Para obter uma boa aproximação do seu consumo de dados e padrão de acesso, recomendamos que escolha um período de retenção para as métricas que seja representativo da sua utilização normal e o utilize para tirar conclusões. Uma opção é manter os dados das métricas durante 7 dias e recolher os dados todas as semanas, para análise no fim do mês. Outra opção é manter os dados das métricas para os últimos 30 dias e recolher e analisar os dados no final desse período de 30 dias.

Para obter detalhes sobre como ativar, recolher e ver dados de métricas, veja [Enabling Azure Storage metrics and viewing metrics data)](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Ativar as métricas do Armazenamento do Azure e visualizar os dados das mesmas).

> [!NOTE]
> O armazenamento, o acesso e a transferência de dados de análise são cobrados também como dados normais do utilizador.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizar métricas de utilização para estimar os custos

### <a name="storage-costs"></a>Custos de armazenamento

A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida pelos dados do utilizador. A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida pelos registos de análise.

Esta capacidade total consumida pelos dados de utilizador e pelos registos de análise (se ativados) pode depois ser utilizada para estimar o custo do armazenamento dos dados na conta de armazenamento. O mesmo método também pode ser utilizado para estimar os custos de armazenamento para blobs de blocos e de acréscimo em contas de armazenamento para fins gerais.

### <a name="transaction-costs"></a>Custos de transação

A soma dos *'TotalBillableRequests'*, em todas as entradas de uma API na tabela de métricas de transação indica o número total de transações para essa API específica. *Por exemplo,*, o número total de transações *'GetBlob'* num determinado período pode ser calculado pela soma do total de pedidos faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para que possa estimar os custos de transação para as contas do Armazenamento de blobs, tem de dividir as transações em três grupos, uma vez que têm um preço diferente.

* Transações de escrita como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de eliminação como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação para contas de armazenamento para fins gerais, é necessário agregar todas as transações, independentemente da operação/API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de acesso a dados e custos de transferência de dados de georreplicação

Embora a análise de armazenamento não forneça a quantidade de dados lidos e escritos numa conta de armazenamento, esta pode ser aproximadamente estimada ao consultar a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas de uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API específica. Do mesmo modo, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para que possa estimar os custos de acesso a dados para as contas do Armazenamento de blobs, precisa de dividir as transações em dois grupos. 

* A quantidade de dados obtidos a partir da conta de armazenamento pode ser estimada ao ver a soma de *'TotalEgress'* para, principalmente, as operações *'GetBlob'* e *'CopyBlob'*.

* A quantidade de dados escritos na conta de armazenamento pode ser estimada ao ver a soma de *'TotalIngress'* para, principalmente, as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

Também pode ser calculado o custo de transferência de dados de georreplicação para contas do Armazenamento de blobs mediante a utilização da estimativa da quantidade de dados escritos se for utilizada uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado sobre o cálculo dos custos de utilização da camada de armazenamento frequente ou esporádico, veja as perguntas mais frequentes com o nome *“O que são as camadas de acesso Frequente e Esporádico e como posso determinar qual delas utilizar?”* na [Página de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
 
## <a name="migrating-existing-data"></a>Migração de dados existentes

Uma conta do Blob Storage é especializada para o armazenamento apenas de blobs de blocos e de acréscimo. Não é possível converter as contas de armazenamento para fins gerais existentes, que lhe permitem armazenar tabelas, filas, ficheiros, discos e também blobs, em contas do Armazenamento de blobs. Para utilizar as camadas de armazenamento, tem de criar contas do Armazenamento de blobs novas e migrar os dados existentes para as mesmas.

Pode utilizar os métodos seguintes para migrar os dados existentes para contas do Armazenamento de Blobs a partir de dispositivos de armazenamento no local, fornecedores de armazenamento na cloud externos ou das suas contas do armazenamento para fins gerais existentes no Azure:

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Pode utilizar o AzCopy para copiar dados para a sua conta do Blob Storage a partir das suas contas do Storage para fins gerais existentes ou para carregar dados dos dispositivos de armazenamento no local para a conta do Blob Storage.

Para obter mais detalhes, consulte [Transferir dados com o utilitário de linha de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Storage do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples, semelhantes às de AzCopy. Isto permite-lhe tirar o máximo partido das funcionalidades fornecidas pelo AzCopy na sua aplicação de forma nativa, sem ter de executar e monitorizar instâncias externas do AzCopy.

Para obter mais detalhes, consulte [Biblioteca de Movimento de Dados do Storage do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Pode criar uma aplicação personalizada para migrar os dados para uma conta do Blob Storage utilizando uma das bibliotecas de cliente do Azure ou a API REST dos serviços do Storage do Azure. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais detalhes, consulte [Introdução ao Blob Storage do Azure](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação armazenados com o blob. É fundamental que os mecanismos de cópia utilizados assegurem que os metadados do blob, e, em especial, os metadados relacionados com a encriptação, são preservados. Se copiar os blobs sem estes metadados, não será possível obter novamente o conteúdo do mesmo. Para obter mais detalhes acerca dos metadados relacionados com a encriptação, consulte [Encriptação do Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
 
## <a name="faq"></a>FAQ

1. **As contas de armazenamento existentes continuam disponíveis?**
   
    Sim, as contas do Storage existentes continuam disponíveis e permanecem inalteradas em termos de preços e funcionalidade.  Não têm a capacidade de escolher uma camada de armazenamento e não terão capacidades de camadas no futuro.

2. **Por que razão devo começar a utilizar as contas de armazenamento de Blobs e quando o devo fazer?**
   
    As contas do Blob Storage são especializadas para armazenar blobs e permitem-nos introduzir novas funcionalidades centradas em blobs. Doravante, as contas do Blob Storage são a forma recomendada de armazenar blobs, uma vez que serão introduzidas capacidades futuras tais como o armazenamento hierárquico e capacidades de camadas com base neste tipo de conta. No entanto, o momento de migração fica ao seu critério com base nos seus requisitos empresariais.

3. **Posso converter a minha conta de armazenamento existente numa conta de armazenamento de Blobs?**
   
    Não. A conta do Armazenamento de blobs é um tipo de conta de armazenamento diferente e tem de criá-la de raiz e migrar os dados tal como explicado anteriormente.

4. **Posso armazenar objetos em ambas as camadas de armazenamento na mesma conta?**
   
    O atributo *“Access Tier”* indica o valor da camada de armazenamento definido ao nível da conta e aplica-se a todos os objetos nessa conta. No entanto, a funcionalidade de camada ao nível do blob (pré-visualização) permitir-lhe-á definir a camada de acesso em blobs específicos, o que substitui a definição da camada de acesso da conta. 

5. **Posso alterar a camada de armazenamento da minha conta de armazenamento de Blobs?**
   
    Sim. Pode definir o atributo *“Access Tier”* na conta de armazenamento para alterar a camada de armazenamento. A alteração da camada de armazenamento aplica-se a todos os objetos armazenados na conta. A alteração da camada de armazenamento de frequente para esporádico é gratuita, ao passo que a alteração de esporádico para frequente está sujeita a um custo por GB para a leitura de todos os dados da conta.

6. **Com que frequência posso alterar a camada de armazenamento da minha conta de armazenamento de Blobs?**
   
    Apesar de não impormos uma limitação à frequência com que é possível alterar a camada de armazenamento, tenha em atenção que a alteração da camada de esporádica para frequente resulta em encargos significativos. Não recomendamos que altere a camada de armazenamento com frequência.

7. **O comportamento dos blobs na camada de armazenamento esporádico é diferente dos blobs na camada de armazenamento frequente?**
   
    Os blobs na camada de armazenamento frequente têm a mesma latência que os blobs em contas de armazenamento para fins gerais. Os blobs na camada de armazenamento esporádico têm uma latência semelhante (em milissegundos) aos blobs em contas do armazenamento para fins gerais.
   
    Os blobs na camada de armazenamento esporádico têm um nível de serviço (SLA) de disponibilidade ligeiramente inferior ao dos blobs armazenados na camada de armazenamento frequente. Para obter mais detalhes, consulte [SLA para o armazenamento](https://azure.microsoft.com/support/legal/sla/storage).

8. **Posso armazenar blobs de páginas e discos de máquinas virtuais em contas de armazenamento de Blobs?**
   
    As contas do Blob Storage suportam apenas blobs de blocos e de acréscimo e não suportam blobs de páginas. Os discos de Virtual Machines do Azure são apoiados por blobs de páginas e, como consequência, não é possível utilizar contas do Blob Storage para armazenar discos de máquinas virtuais. No entanto, é possível armazenar cópias de segurança dos discos de máquinas virtuais como blobs de blocos numa conta do Blob Storage.

9. **Tenho de alterar as minhas aplicações existentes para utilizar as contas do Armazenamento de blobs?**
   
    As contas do Blob Storage são 100% consistentes com a API com as contas do Storage para fins gerais para blobs de blocos e de acréscimo. Desde que a aplicação utilize blobs de blocos ou blobs de acréscimo e esteja a utilizar a versão de 14/02/2014 da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou superior, a aplicação deverá funcionar normalmente. Se estiver a utilizar uma versão mais antiga do protocolo, tem de atualizar a sua aplicação para utilizar a versão nova, de modo a trabalhar de forma totalmente integrada com ambos os tipos de contas de armazenamento. Em geral, recomendamos sempre que utilize a versão mais recente, independentemente da conta do Storage utilizada.

10. **Vai haver alterações à experiência do utilizador?**
    
    As contas do Blob Storage são muito semelhantes às contas do Storage para fins gerais para armazenar blobs de blocos e de acréscimo e suportam todas as principais funcionalidades do Storage do Azure, incluindo elevada durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Com exceção das funcionalidades e restrições específicas das contas do Armazenamento de Blobs e das respetivas camadas de armazenamento que foram descritas acima, tudo o resto permanece igual.

## <a name="next-steps"></a>Passos seguintes

### <a name="evaluate-blob-storage-accounts"></a>Avaliar as contas do Blob Storage

[Verificar a disponibilidade das contas do armazenamento de Blobs por região](https://azure.microsoft.com/regions/#services)

[Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Verificar os preços do armazenamento de Blobs por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Começar a utilizar as contas do Blob Storage

[Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md)

[Mover dados de e para o Armazenamento do Azure](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Procurar e explorar as suas contas de armazenamento](http://storageexplorer.com/)