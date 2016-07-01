<properties
    pageTitle="Criar uma conta do Azure Batch | Microsoft Azure"
    description="Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="marsma"/>

# Criar e gerir uma conta do Azure Batch no portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [Gestão de Batch .NET](batch-management-dotnet.md)

O [Portal do Azure][azure_portal] fornece-lhe as ferramentas necessárias para criar e gerir uma conta do Azure Batch, que pode utilizar para o processamento de cargas de trabalho paralelas em grande escala. Neste artigo, vamos explicar-lhe a criação de uma conta do Batch através do portal e realçar definições e propriedades importantes de uma conta do Batch. Por exemplo, as aplicações e serviços que desenvolver com Batch necessitam do URL da sua conta e de uma tecla de acesso para comunicar com as APIs do serviço do Batch, que se encontram ambas no portal do Azure.

>[AZURE.NOTE] O portal do Azure suporta atualmente um subconjunto das funcionalidades no serviço do Batch, incluindo a criação de contas, a gestão de definições e propriedades da conta do Batch e a criação e monitorização de conjuntos e tarefas. O conjunto de funcionalidades completo do Batch está disponível para os programadores através das APIs do Batch.

## Criar uma conta do Batch

1. Inicie sessão no [portal do Azure][azure_portal].

2. Clique em **Novo** > **Máquinas Virtuais** > **Serviço Batch**.

    ![Batch no Marketplace][marketplace_portal]

3. O painel **Nova Conta do Batch** é apresentado. Veja os itens do *a* ao *e* abaixo para obter descrições de cada elemento do painel.

    ![Criar uma conta do Batch][account_portal]

    a. **Nome da Conta**: um nome exclusivo para a sua conta do Batch. Este nome tem de ser exclusivo dentro da região do Azure onde a conta está criada (consulte *Localização* abaixo). Pode conter apenas carateres em minúsculas, números e tem de ter 3 a 24 carateres de comprimento.

    b. **Subscrição**: uma subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    c. **Grupo de recursos**: um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    d. **Localização**: uma região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos serão apresentadas como opções.

    e. **Conta de Armazenamento** (opcional): uma conta de armazenamento **Para fins gerais** que associa (liga) à sua nova conta do Batch. A funcionalidade [pacotes de aplicações](batch-application-packages.md) do Batch irá utilizar a conta de armazenamento ligada para o armazenamento e a obtenção de pacotes de aplicações. Consulte [Implementação de aplicações com pacotes de aplicações do Azure Batch](batch-application-packages.md) para obter mais informações sobre esta funcionalidade.

     > [AZURE.IMPORTANT] A regeneração de chaves numa conta de Armazenamento ligada requer considerações especiais. Consulte [Considerações para contas do Batch](#considerations-for-batch-accounts) abaixo para obter mais detalhes.

4. Clique em **Criar** para criar a conta.

  O portal indicará que está a **Implementar** a conta e, após a conclusão, uma notificação **Implementações concluídas com êxito** aparecerá em *Notificações*.

## Ver propriedades da conta do Batch

O painel de Conta do Batch apresenta várias propriedades para a conta, além de fornecer acesso a definições adicionais, tais como teclas de acesso, quotas, utilizadores e associação de contas de armazenamento.

* **URL da conta do Batch**: este URL fornece acesso à sua conta do Batch quando utilizar APIs como a API [REST de Batch][api_rest] ou a biblioteca de cliente [.NET Batch][api_net] e os respeita o seguinte formato:

    `https://<account_name>.<region>.batch.azure.com`

* **Chaves de acesso**: para ver e gerir as chaves de acesso da sua conta do Batch, clique no ícone da tecla para abrir o painel **Gerir chaves** ou clique em **Todas as definições** > **Chaves**. É necessária uma chave de acesso ao comunicar com as APIs do serviço do Batch, tal como com [REST Batch][api_rest] ou a biblioteca cliente [.NET Batch][api_net].

    ![Chaves de conta do Batch][account_keys]

* **Todas as definições**: para gerir todas as definições para a conta do Batch ou para ver as respetivas propriedades, clique em **Todas as definições** para abrir o painel **Definições**. Este painel fornece acesso a todas as definições e propriedades da conta, incluindo a visualização de quotas de contas, seleção de uma conta de Armazenamento do Azure para ligar à conta do Batch e gestão de utilizadores.

    ![Painéis de propriedades e definições de conta do Batch][5]

## Considerações para contas do Batch

* Também pode criar e gerir contas do Batch com os [cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md) e a biblioteca [.NET de Gestão do Batch](batch-management-dotnet.md).

* A conta do Batch em si não lhe é cobrada. São cobrados quaisquer recursos de computação do Azure que as soluções do Batch consumam e os recursos consumidos por outros serviços, quando as cargas de trabalho são executadas. Por exemplo, são-lhe cobrados os nós de computação nos seus conjuntos e, se utilizar a funcionalidade [pacotes de aplicações](batch-application-packages.md), são-lhe cobrados os recursos de Armazenamento do Azure utilizados para armazenar as versões de pacotes de aplicações. Consulte [Preços do Batch][batch_pricing] para obter mais informações.

* Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuir as cargas de trabalho entre contas do Batch em diferentes regiões Azure.

* Se estiver a executar várias cargas de trabalho do Batch em grande escala, tenha em atenção determinados [limites e quotas de serviço do Batch](batch-quota-limit.md) que se aplicam à sua subscrição do Azure e a cada conta do Batch. As quotas atuais numa conta do Batch são apresentadas no portal nas propriedades da conta.

* Se associar (ligar) uma conta de armazenamento à sua conta do Batch, tenha cuidado quando regenerar as chaves de acesso da conta de armazenamento. Deve voltar a gerar apenas uma chave de conta de armazenamento, clicar em **Sincronizar Chaves** no painel da conta de armazenamento ligada, aguardar 5 minutos para permitir que as chaves se propaguem para os nós de computação nos seus conjuntos e, em seguida, voltar a gerar e sincronizar a outra chave se for necessário. Se voltar a gerar ambas as chaves ao mesmo tempo, os nós de computação não conseguirão sincronizar nenhuma das chaves e estas perdem o acesso à conta de armazenamento.

  ![Regenerar chaves de conta de armazenamento][4]

> [AZURE.IMPORTANT] O Batch atualmente suporta *apenas* o tipo de conta de armazenamento **Fins gerais**, conforme descrito no passo n.º 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). Quando ligar uma conta de Armazenamento do Azure à sua conta do Batch, ligue *apenas* uma conta de armazenamento de **Fins gerais**.

## Passos seguintes

* Consulte a [Descrição geral da funcionalidade do Azure Batch](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação e tarefas e fornece uma descrição geral das funcionalidades do serviço que permitem a execução de cargas de trabalho de computação em grande escala.

* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](batch-dotnet-get-started.md). O [artigo introdutório](batch-dotnet-get-started.md) orienta-o numa aplicação em funcionamento que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regenerar chaves de conta de armazenamento"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Painéis de propriedades e definições de conta do Batch"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG



<!--HONumber=Jun16_HO2-->


