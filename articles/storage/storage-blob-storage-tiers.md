---
title: "Armazenamento de acesso esporádico do Azure para blobs | Microsoft Docs"
description: "As camadas de armazenamento para o Armazenamento de Blobs do Azure oferecem um armazenamento económico para dados de objetos com base em padrões de acesso. A camada de armazenamento de acesso esporádico está otimizada para dados que são acedidos com menor frequência."
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
ms.date: 02/27/2017
ms.author: mihauss
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 618d31e991d9405ece6533727d700e316ae85bec
ms.lasthandoff: 04/06/2017


---
# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Armazenamento de Blobs do Azure: camadas de armazenamento de acesso frequente e esporádico
## <a name="overview"></a>Descrição geral
O Armazenamento do Azure disponibiliza duas camadas de armazenamento para o armazenamento de objetos Blob, para que possa armazenar os seus dados de forma mais económica consoante o modo como os utiliza. A **camada de armazenamento de acesso frequente** do Azure está otimizada para armazenar dados que são acedidos com frequência. A **camada de armazenamento de acesso esporádico** do Azure está otimizada para armazenar dados que são acedidos com pouca frequência e de longa duração. Os dados na camada de armazenamento de acesso esporádico podem tolerar disponibilidade ligeiramente inferior, mas ainda requerem uma durabilidade elevada, bem como tempo de acesso e características de débito semelhantes aos dados de acesso frequente. Para os dados de acesso esporádico, um SLA de disponibilidade ligeiramente inferior e custos de acesso superiores são compromissos aceitáveis em troca de custos de armazenamento muito inferiores.

Atualmente, os dados armazenados na nuvem estão a crescer a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado. Os dados armazenados na nuvem podem ser diferentes em termos da forma como são gerados, processados e acedidos ao longo da respetiva duração. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na nuvem e são raramente acedidos após serem armazenados ou não são acedidos de todo.

Cada um dos cenários de acesso a dados descritos acima beneficia de uma camada diferenciada de armazenamento, otimizada para um padrão de acesso específico. Agora, com a introdução das camadas de armazenamento frequente e esporádico, o Armazenamento de Blobs do Azure dá resposta a esta necessidade de camadas de armazenamento diferenciadas com modelos de preços distintos.

## <a name="blob-storage-accounts"></a>Contas do Blob Storage
As **Contas do Blob Storage** são contas do Storage especializadas para armazenar os dados não estruturados como blobs (objetos) no Storage do Azure. Com as contas do Armazenamento de Blobs, pode escolher agora entre camadas de armazenamento frequente e esporádico para armazenar os dados acedidos com menor frequência com um custo de armazenamento inferior e armazenar os dados acedidos com maior frequência com um menor custo de acesso. As contas do Blob Storage são semelhantes às suas contas do Storage para fins gerais existentes e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho que utiliza atualmente, incluindo 100% de consistência com a API para blobs de blocos e blobs de acréscimo.

> [!NOTE]
> As contas do Blob Storage suportam apenas blobs de blocos e de acréscimo e não suportam blobs de páginas.
> 
> 

As contas do Armazenamento de Blobs expõem o atributo **Access Tier**, que permite especificar a camada de armazenamento como de acesso **Frequente** ou **Esporádico**, consoante os dados armazenados na conta. Se o padrão de utilização dos dados sofrer uma alteração, pode também alternar entre estas camadas de armazenamento em qualquer altura.

> [!NOTE]
> A alteração da camada de armazenamento poderá resultar em encargos adicionais. Consulte a secção [Preços e Faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.
> 
> 

Os exemplos de cenários de utilização da camada de armazenamento frequente incluem:

* Dados que estão a ser utilizados ativamente ou que deverão ser acedidos (para operações de leitura e escrita) com frequência.
* Dados preparados para processamento e eventual migração para a camada de armazenamento de acesso esporádico.

Os exemplos de cenários de utilização da camada de armazenamento esporádico incluem:

* Conjuntos de dados de cópia de segurança, arquivo e recuperação após desastre.
* Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
* Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)
* Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,*, ficheiros de multimédia não processados após a transcodificação para outros formatos)
* Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo,*, filmagens de câmaras de segurança, raios X/RMs antigos para organizações de cuidados de saúde, gravações de áudio e transcrições de chamadas dos clientes para serviços financeiros)

Consulte [Acerca das contas do Storage do Azure](storage-create-storage-account.md) para obter mais informações sobre contas do Storage.

Para as aplicações que requerem apenas o Blob Storage de blocos ou de acréscimo, recomendamos que utilize contas do Blob Storage, de modo a tirar partido do modelo de preços diferenciado do armazenamento em camadas. No entanto, compreendemos que tal pode não ser possível em determinadas circunstâncias em que a utilização de contas do Storage para fins gerais seria mais adequada, tais como:

* Tem de utilizar tabelas, filas ou ficheiros e pretende que os blobs estejam armazenados na mesma conta do Storage. Tenha em atenção que o armazenamento destes elementos na mesma conta não tem qualquer vantagem técnica além da utilização das mesmas chaves partilhadas.
* Ainda tem de utilizar o modelo de implementação clássica. As contas do Blob Storage estão disponíveis apenas através do modelo de implementação Azure Resource Manager.
* Tem de utilizar blobs de páginas. As contas do Blob Storage não suportam blobs de páginas. Normalmente, recomendamos que utilize blobs de blocos, a menos que necessite especificamente de blobs de páginas.
* Utiliza uma versão da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior a 14/02/2014 ou uma biblioteca de cliente com uma versão inferior à 4.x e não pode atualizar a sua aplicação.

> [!NOTE]
> Atualmente, as contas do armazenamento de Blobs são suportadas em todas as regiões do Azure.
> 
> 

## <a name="comparison-between-the-storage-tiers"></a>Comparação das camadas de armazenamento
A tabela seguinte realça a comparação entre as duas camadas de armazenamento:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Camada de armazenamento frequente</center></strong></td>
    <td><strong><center>Camada de armazenamento esporádico</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilidade</center></strong></td>
    <td><center>99.9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilidade<br>(leituras de RA-GRS)</center></strong></td>
    <td><center>99.99%</center></td>
    <td><center>99.9%</center></td>
</tr>
<tr>
    <td><strong><center>Custos de utilização</center></strong></td>
    <td><center>Custos de armazenamento superiores<br>Custos de acesso e transação inferiores</center></td>
    <td><center>Custos de armazenamento inferiores<br>Custos de acesso e transação superiores</center></td>
</tr>
<tr>
    <td><strong><center>Tamanho mínimo do objeto<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Duração de armazenamento mínima<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Latência<br>(Tempo até ao primeiro byte)<center></strong></td>
    <td colspan="2"><center>milissegundos</center></td>
</tr>
<tr>
    <td><strong><center>Metas de escalabilidade e desempenho<center></strong></td>
    <td colspan="2"><center>Igual às contas do Storage para fins gerais</center></td>
</tr>
</tbody>
</table>

> [!NOTE]
> As contas do Blob Storage suportam as mesmas metas de desempenho e escalabilidade que as contas do Storage para fins gerais. Consulte [Metas de Desempenho e Escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter mais informações.
> 
> 

## <a name="pricing-and-billing"></a>Preços e Faturação
As contas do Armazenamento de Blobs utilizam um novo modelo de preços para o Armazenamento de Blobs com base na camada de armazenamento. Ao utilizar uma conta do Blob Storage, aplicam-se as seguintes considerações de faturação:

* **Custos de armazenamento**: para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de armazenamento. O custo por gigabyte é menor para a camada de armazenamento esporádico do que para a camada de armazenamento frequente.
* **Custos do acesso a dados**: para os dados na camada de armazenamento esporádico, será cobrada uma taxa de acesso a dados por gigabyte pelas operações de leitura e escrita.
* **Custos de transação**: é cobrada uma taxa por transação para ambas as camadas. No entanto, o custo por transação para a camada de armazenamento esporádico é superior ao da camada de armazenamento frequente.
* **Custos de transferência de dados de georreplicação**: aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
* **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.
* **Alteração da camada de armazenamento**: a alteração da camada de armazenamento esporádico para armazenamento frequente está sujeita à cobrança de uma taxa igual à leitura de todos os dados existentes na conta do armazenamento para cada transição. Por outro lado, a alteração da camada de armazenamento frequente para armazenamento esporádico será gratuita.

> [!NOTE]
> Para permitir que os utilizadores experimentem as novas camadas de armazenamento e validem a funcionalidade após o lançamento, a taxa associada à alteração da camada de armazenamento de esporádico para frequente não será cobrada até 30 de junho de 2016. A partir de 1 de julho de 2016, a taxa será aplicada a todas as transições de acesso esporádico para acesso frequente. Para obter mais detalhes sobre o modelo de preços para as contas do Blob Storage, consulte a página [Preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/). Para obter mais detalhes sobre as taxas aplicáveis às transferências de dados de saída, consulte a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

## <a name="quick-start"></a>Guia de Introdução
Nesta secção, iremos demonstrar os seguintes cenários através do portal do Azure:

* Como criar uma conta do Blob Storage
* Como gerir uma conta do Blob Storage.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Criar uma conta do Armazenamento de Blobs através do portal do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Novo** > **Dados + Armazenamento** > **Conta do Storage**.
3. Introduza um nome para a conta do Storage.
   
    Este nome tem de ser globalmente exclusivo; é utilizado como parte do URL utilizado para aceder aos objetos na conta de armazenamento.  
4. Selecione **Resource Manager** como o modelo de implementação.
   
    O armazenamento em camadas só pode ser utilizado com contas de armazenamento do Resource Manager; este é o modelo de implementação recomendado para novos recursos. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).  
5. Na lista pendente Tipo de Conta, selecione **Armazenamento de Blobs**.
   
    É aqui que pode seleciona o tipo de conta de armazenamento. O armazenamento em camadas não está disponível no armazenamento para fins gerais; só está disponível na conta de tipo de Armazenamento de Blobs.     
   
    Tenha em atenção que, quando seleciona esta opção, a camada de desempenho fica definida como Standard. O armazenamento em camadas não está disponível com a camada de desempenho Premium.
6. Selecione a opção de replicação para a conta do Storage: **LRS**, **GRS** ou **RA-GRS**. A predefinição é **RA-GRS**.
   
    LRS = armazenamento localmente redundante; GRS = armazenamento georredundante (2 regiões); RA-GRS é armazenamento georredundante com acesso de leitura (2 regiões com acesso de leitura para a segunda).
   
    Consulte [Azure Storage replication (Replicação do Armazenamento do Azure)](storage-redundancy.md) para obter detalhes adicionais sobre as opções de replicação do Armazenamento do Azure.
7. Selecione a camada de armazenamento correta para as suas necessidades: defina a **Camada de acesso** como **Esporádica** ou **Frequente**. A predefinição é **Frequente**.
8. Selecione a subscrição na qual pretende criar a nova conta do Storage.
9. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações sobre os grupos de recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
10. Selecione a região para a sua conta de armazenamento.
11. Clique em **Criar** para criar a conta do Storage.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Alterar a camada de armazenamento numa conta do Armazenamento de Blobs através do portal do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Para navegar para a sua conta de armazenamento, selecione Todos os Recursos, em seguida, selecione a sua conta de armazenamento.
3. No painel Definições, clique em**Configuração** para ver e/ou alterar a configuração da conta.
4. Selecione a camada de armazenamento correta para as suas necessidades: defina a **Camada de acesso** como **Esporádica** ou **Frequente**.
5. Clique em Guardar na parte superior do painel.

> [!NOTE]
> A alteração da camada de armazenamento poderá resultar em encargos adicionais. Consulte a secção [Preços e Faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.
> 
> 

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Avaliar e migrar para contas do Armazenamento de Blobs
O objetivo desta secção consiste em ajudar os utilizadores a fazerem uma transição tranquila para a utilização de contas do Armazenamento de Blobs. Há dois cenários de utilizador:

* Tem uma conta de armazenamento para fins gerais existente e pretende avaliar uma alteração para uma conta do Armazenamento de Blobs com a camada de armazenamento correta.
* Decidiu utilizar uma conta do Armazenamento de Blobs ou já tem uma e pretende avaliar se deve utilizar a camada de armazenamento frequente ou esporádico.

Em ambos os casos, a primeira prioridade é estimar os custos de armazenar e aceder aos seus dados armazenados numa conta do Armazenamento de Blobs e compará-los com os seus custos atuais.

### <a name="evaluating-blob-storage-account-tiers"></a>Avaliar camadas da conta do Armazenamento de Blobs
Para estimar os custos de armazenar e aceder aos dados armazenados numa conta do Armazenamento de Blobs, terá de avaliar o seu padrão de utilização existente ou fazer uma aproximação do seu padrão de utilização esperado. Em geral, precisa de saber o seguinte:

* O seu consumo de armazenamento: que quantidade de dados está a ser armazenada e como é que isso muda mensalmente?
* O padrão de acesso ao armazenamento: que quantidade de dados é lida da conta e escrita na mesma (incluindo dados novos)? Quantas transações são utilizadas para acesso a dados e de que tipo são?

#### <a name="monitoring-existing-storage-accounts"></a>Monitorizar contas de armazenamento existentes
Para monitorizar as suas contas de armazenamento existentes e recolher estes dados, pode utilizar a Análise do Armazenamento do Azure, que efetua o registo e fornece dados de métricas para uma conta de armazenamento.
A Análise do Armazenamento pode armazenar métricas que incluem estatísticas agregadas de transações e dados de capacidade sobre pedidos para o serviço do Armazenamento de Blobs para contas de armazenamento para fins gerais e para contas do Armazenamento de Blobs.
Estes dados são armazenados em tabelas bem conhecidas na mesma conta de armazenamento.

Para obter mais detalhes, veja [About Storage Analytics Metric (Sobre Métricas da Análise do Armazenamento)](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Storage Analytics Metrics Table Schema (Esquema da Tabela de Métricas da Análise do Armazenamento)](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas do Armazenamento de Blobs expõem o ponto final de serviço de tabelas apenas para armazenar e aceder aos dados de métricas para essa conta.
> 
> 

Para monitorizar o consumo de armazenamento para o serviço Armazenamento de Blobs, terá de ativar as métricas de capacidade.
Com esta opção ativada, os dados de capacidade são registados diariamente para um serviço Blob de uma conta de armazenamento e registados como uma entrada que é escrita na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorizar o padrão de acesso a dados para o serviço de armazenamento do Armazenamento de Blobs, terá de ativar as métricas de transação horária ao nível de uma API.
Com esta opção ativada, as transações por API são agregadas ao fim de cada hora e registadas como uma entrada que é escrita na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* regista as transações para o ponto final secundário no caso de contas de armazenamento RA-GRS.

> [!NOTE]
> No caso de ter uma conta de armazenamento para fins gerais em que tem armazenados blobs de páginas e discos da máquina virtual em conjunto com dados de blob de blocos e de acréscimo, este processo de estimativa não é aplicável. Isto acontece porque não há um modo de distinguir métricas de capacidade e de transação com base no tipo de blob apenas para blobs de blocos e de acréscimo que podem ser migrados para uma conta do Armazenamento de Blobs.
> 
> 

Para obter uma boa aproximação do seu consumo de dados e padrão de acesso, recomendamos que escolha um período de retenção para as métricas que seja representativo da sua utilização normal e o utilize para tirar conclusões.
Uma opção é manter os dados das métricas durante 7 dias e recolher os dados todas as semanas, para análise no fim do mês.
Outra opção é manter os dados das métricas para os últimos 30 dias e recolher e analisar os dados no final desse período de 30 dias.

Para obter detalhes sobre como ativar, recolher e visualizar dados de métricas, veja [Enabling Azure Storage metrics and viewing metrics data (Ativar as métricas do Armazenamento do Azure e visualizar os dados das métricas)](storage-enable-and-view-metrics.md).

> [!NOTE]
> O armazenamento, acesso e transferência de dados de análise são cobrados também como dados normais do utilizador.
> 
> 

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizar métricas de utilização para estimar os custos
##### <a name="storage-costs"></a>Custos de armazenamento
A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida pelos dados do utilizador.
A entrada mais recente na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida pelos registos de análise.

Esta capacidade total consumida pelos dados de utilizador e pelos registos de análise (se ativados) pode depois ser utilizada para estimar o custo do armazenamento dos dados na conta de armazenamento.
O mesmo método também pode ser utilizado para estimar os custos de armazenamento para blobs de blocos e de acréscimo em contas de armazenamento para fins gerais.

##### <a name="transaction-costs"></a>Custos de transação
A soma dos *'TotalBillableRequests'*, em todas as entradas de uma API na tabela de métricas de transação indica o número total de transações para essa API específica. *Por exemplo,*, o número total de transações *'GetBlob'* num determinado período pode ser calculado pela soma do total de pedidos faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para que possa estimar os custos de transação para contas do Armazenamento de Blobs, precisa de dividir as transações em três grupos, uma vez que têm um preço diferente.

* Transações de escrita como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de eliminação como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação para contas de armazenamento para fins gerais, é necessário agregar todas as transações, independentemente da operação/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de acesso a dados e custos de transferência de dados de georreplicação
Embora a análise de armazenamento não forneça a quantidade de dados lidos e escritos numa conta de armazenamento, esta pode ser aproximadamente estimada ao consultar a tabela de métricas de transação.
A soma de *'TotalIngress'* de todas as entradas de uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API específica.
Do mesmo modo, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para que possa estimar os custos de acesso a dados para contas do Armazenamento de Blobs, precisa de dividir as transações em dois grupos.

* A quantidade de dados obtidos a partir da conta de armazenamento pode ser estimada ao ver a soma de *'TotalEgress'* para, principalmente, as operações *'GetBlob'* e *'CopyBlob'*.
* A quantidade de dados escritos na conta de armazenamento pode ser estimada ao ver a soma de *'TotalIngress'* para, principalmente, as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

Também pode ser calculado o custo de transferência de dados de georreplicação para contas do Armazenamento de Blobs ao utilizar a estimativa d a quantidade de dados escritos no caso de uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado sobre o cálculo dos custos de utilização da camada de armazenamento frequente ou esporádico, consulte as perguntas mais frequentes intituladas *“O que são as camadas de acesso Frequente e Esporádico e como posso determinar qual delas utilizar?”* na [Página de Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
> 
> 

### <a name="migrating-existing-data"></a>Migração de dados existentes
Uma conta do Blob Storage é especializada para o armazenamento apenas de blobs de blocos e de acréscimo. Não é possível converter as contas de armazenamento para fins gerais existentes, que lhe permitem armazenar tabelas, filas, ficheiros, discos e também blobs, em contas do Armazenamento de Blobs. Para utilizar as camadas de armazenamento, terá de criar novas contas do Blob Storage e migrar os dados existentes para as contas recém-criadas.

Pode utilizar os métodos seguintes para migrar os dados existentes para contas do Armazenamento de Blobs a partir de dispositivos de armazenamento no local, fornecedores de armazenamento na cloud externos ou das suas contas do armazenamento para fins gerais existentes no Azure:

#### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Pode utilizar o AzCopy para copiar dados para a sua conta do Blob Storage a partir das suas contas do Storage para fins gerais existentes ou para carregar dados dos dispositivos de armazenamento no local para a conta do Blob Storage.

Para obter mais detalhes, consulte [Transferir dados com o utilitário de linha de comandos AzCopy](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados
A biblioteca de movimento de dados do Storage do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples semelhantes às do AzCopy. Isto permite-lhe tirar o máximo partido das funcionalidades fornecidas pelo AzCopy na sua aplicação de forma nativa, sem ter de executar e monitorizar instâncias externas do AzCopy.

Para obter mais detalhes, consulte [Biblioteca de Movimento de Dados do Storage do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>API REST ou Biblioteca de Cliente
Pode criar uma aplicação personalizada para migrar os dados para uma conta do Blob Storage utilizando uma das bibliotecas de cliente do Azure ou a API REST dos serviços do Storage do Azure. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais detalhes, consulte [Introdução ao Blob Storage do Azure](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação armazenados com o blob. É fundamental que os mecanismos de cópia utilizados assegurem que os metadados do blob, e, em especial, os metadados relacionados com a encriptação, são preservados. Se copiar os blobs sem estes metadados, não será possível obter novamente o conteúdo do blob. Para obter mais detalhes acerca dos metadados relacionados com a encriptação, consulte [Encriptação do Lado do Cliente do Armazenamento do Azure](storage-client-side-encryption.md).
> 
> 

## <a name="faqs"></a>Perguntas mais frequentes
1. **As contas de armazenamento existentes continuam disponíveis?**
   
    Sim, as contas do Storage existentes continuam disponíveis e permanecem inalteradas em termos de preços e funcionalidade.  Não têm a capacidade de escolher uma camada de armazenamento e não terão capacidades de camadas no futuro.
2. **Por que razão devo começar a utilizar as contas de armazenamento de Blobs e quando o devo fazer?**
   
    As contas do Blob Storage são especializadas para armazenar blobs e permitem-nos introduzir novas funcionalidades centradas em blobs. Doravante, as contas do Blob Storage são a forma recomendada de armazenar blobs, uma vez que serão introduzidas capacidades futuras tais como o armazenamento hierárquico e capacidades de camadas com base neste tipo de conta. No entanto, o momento de migração fica ao seu critério com base nos seus requisitos empresariais.
3. **Posso converter a minha conta de armazenamento existente numa conta de armazenamento de Blobs?**
   
    Não. A conta do Blob Storage é um tipo diferente de conta do Storage e terá de a criar de raiz e migrar os dados tal como explicado anteriormente.
4. **Posso armazenar objetos em ambas as camadas de armazenamento na mesma conta?**
   
    O atributo *“Access Tier”*, que indica a camada de armazenamento, é definido ao nível de uma conta e aplica-se a todos os objetos nessa conta. Não pode definir o atributo da camada de acesso ao nível do objeto.
5. **Posso alterar a camada de armazenamento da minha conta de armazenamento de Blobs?**
   
    Sim. Poderá alterar a camada de acesso ao definir o atributo *“Access Tier”* na conta de armazenamento. A alteração da camada de armazenamento aplicar-se-á a todos os objetos armazenados na conta. A alteração da camada de armazenamento de frequente para esporádico será gratuita, ao passo que a alteração de esporádico para frequente estará sujeita a um custo por GB para a leitura de todos os dados na conta.
6. **Com que frequência posso alterar a camada de armazenamento da minha conta de armazenamento de Blobs?**
   
    Apesar de não impormos uma limitação à frequência com que é possível alterar a camada de armazenamento, tenha em atenção que a alteração da camada de armazenamento de esporádico para frequente resultará em encargos significativos. Não recomendamos que altere a camada de armazenamento com frequência.
7. **Os blobs na camada de armazenamento esporádico terão um comportamento diferente dos blobs na camada de armazenamento frequente?**
   
    Os blobs na camada de armazenamento frequente têm a mesma latência que os blobs em contas de armazenamento para fins gerais. Os blobs na camada de armazenamento esporádico têm uma latência semelhante (em milissegundos) aos blobs em contas do armazenamento para fins gerais.
   
    Os blobs na camada de armazenamento esporádico terão um nível de serviço (SLA) de disponibilidade ligeiramente inferior ao dos blobs armazenados na camada de armazenamento frequente. Para obter mais detalhes, consulte [SLA para o armazenamento](https://azure.microsoft.com/support/legal/sla/storage).
8. **Posso armazenar blobs de páginas e discos de máquinas virtuais em contas de armazenamento de Blobs?**
   
    As contas do Blob Storage suportam apenas blobs de blocos e de acréscimo e não suportam blobs de páginas. Os discos de Virtual Machines do Azure são apoiados por blobs de páginas e, como consequência, não é possível utilizar contas do Blob Storage para armazenar discos de máquinas virtuais. No entanto, é possível armazenar cópias de segurança dos discos de máquinas virtuais como blobs de blocos numa conta do Blob Storage.
9. **Terei de alterar as minhas aplicações existentes para utilizar as contas de armazenamento de Blobs?**
   
    As contas do Blob Storage são 100% consistentes com a API com as contas do Storage para fins gerais para blobs de blocos e de acréscimo. Desde que a aplicação utilize blobs de blocos ou blobs de acréscimo e esteja a utilizar a versão de 14/02/2014 da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou superior, a aplicação deverá funcionar normalmente. Se estiver a utilizar uma versão mais antiga do protocolo, terá de atualizar a sua aplicação para utilizar a nova versão, de modo a trabalhar de forma totalmente integrada com ambos os tipos de contas do Storage. Em geral, recomendamos sempre que utilize a versão mais recente, independentemente da conta do Storage utilizada.
10. **Haverá alguma alteração na experiência de utilizador?**
    
    As contas do Blob Storage são muito semelhantes às contas do Storage para fins gerais para armazenar blobs de blocos e de acréscimo e suportam todas as principais funcionalidades do Storage do Azure, incluindo elevada durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Com exceção das funcionalidades e restrições específicas das contas do Armazenamento de Blobs e das respetivas camadas de armazenamento que foram descritas acima, tudo o resto permanece igual.

## <a name="next-steps"></a>Passos seguintes
### <a name="evaluate-blob-storage-accounts"></a>Avaliar as contas do Blob Storage
[Verificar a disponibilidade das contas do armazenamento de Blobs por região](https://azure.microsoft.com/regions/#services)

[Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](storage-enable-and-view-metrics.md)

[Verificar os preços do armazenamento de Blobs por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Começar a utilizar as contas do Blob Storage
[Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md)

[Mover dados de e para o Armazenamento do Azure](storage-moving-data.md)

[Transferir dados com o Utilitário de Linha de Comandos AzCopy](storage-use-azcopy.md)

[Procurar e explorar as suas contas de armazenamento](http://storageexplorer.com/)


