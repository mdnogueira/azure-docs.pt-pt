---
title: Gerir o Azure Data Lake Analytics utilizando o portal do Azure | Microsoft Docs
description: Saiba como gerir sobre contas do Data Lake Analytics, origens de dados, os utilizadores e tarefas.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: e49d1a0e0ccc6567d0a6841817667717ff5dba76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Gerir o Azure Data Lake Analytics utilizando o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas de Azure Data Lake Analytics, origens de dados de conta, os utilizadores e tarefas utilizando o portal do Azure. Para ver tópicos de gestão sobre a utilização de outras ferramentas, clique num separador no topo da página.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerir contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Intelligence + análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: O nome da conta do Data Lake Analytics.
   2. **Subscrição**: subscrição do Azure utilizada para a conta.
   3. **Grupo de recursos**: grupo de recursos do Azure na qual pretende criar a conta. 
   4. **Localização**: Centro de dados do Azure para a conta de Data Lake Analytics. 
   5. **Arquivo data Lake**: O arquivo predefinido a ser utilizada para a conta de Data Lake Analytics. A conta do Azure Data Lake Store e a conta de Data Lake Analytics tem de ser na mesma localização.
4. Clique em **Criar**. 

### <a name="delete-a-data-lake-analytics-account"></a>Eliminar uma conta do Data Lake Analytics

Antes de eliminar uma conta de Data Lake Analytics, elimine a conta de Data Lake Store predefinida.

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Eliminar**.
3. Escreva o nome da conta.
4. Clique em **Eliminar**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gerir origens de dados

O Data Lake Analytics suporta as seguintes origens de dados:

* Data Lake Store
* Storage do Azure

Pode utilizar o Explorador de dados para procurar as origens de dados e realizar operações de gestão de ficheiros básico. 

### <a name="add-a-data-source"></a>Adicionar uma origem de dados

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **origens de dados**.
3. Clique em **Adicionar origem de dados**.
    
   * Para adicionar uma conta de Data Lake Store, é necessário o nome de conta e o acesso à conta para poder consultá-lo.
   * Para adicionar o Blob storage do Azure, terá da conta de armazenamento e a chave de conta. Para encontrá-los, vá para a conta do storage no portal.

## <a name="set-up-firewall-rules"></a>Configurar regras de firewall

Pode utilizar o Data Lake Analytics para bloqueio mais baixo de acesso à sua conta do Data Lake Analytics ao nível da rede. Pode ativar uma firewall, especifique um endereço IP ou definir um intervalo de endereços IP para os seus clientes fidedignos. Depois de ativar estas medidas, apenas os clientes que tenham os endereços IP no intervalo definido podem ligar para o arquivo.

Se a outros serviços do Azure, como o Azure Data Factory ou VMs, ligar à conta do Data Lake Analytics, certifique-se de que **permitir que os serviços do Azure** está ativada **no**. 

### <a name="set-up-a-firewall-rule"></a>Configurar uma regra de firewall

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. No menu à esquerda, clique em **Firewall**.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode utilizar o **Assistente para adicionar utilizador** facilmente aprovisionar novos utilizadores do Data Lake.

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. No lado esquerdo, em **introdução**, clique em **Assistente para adicionar utilizador**.
3. Selecione um utilizador e, em seguida, clique em **selecione**.
4. Selecione uma função e, em seguida, clique em **selecione**. Para definir um programador de novo para utilizar o Azure Data Lake, selecione o **para programadores do Data Lake Analytics** função.
5. Selecione as listas de controlo de acesso (ACL) para as bases de dados do U-SQL. Quando estiver satisfeito com as suas opções, clique em **selecione**.
6. Selecione as ACLs de ficheiros. Para o arquivo predefinido, não altere as ACLs para a pasta raiz "/" e para a pasta de /system. Clique em **Selecionar**.
7. Reveja todas as alterações selecionadas e, em seguida, clique em **executar**.
8. Quando o assistente estiver concluído, clique em **feito**.

## <a name="manage-role-based-access-control"></a>Gerir o controlo de acesso baseado em funções

Como outros serviços do Azure, pode utilizar o controlo de acesso baseado em funções (RBAC) para controlar a forma como os utilizadores interagem com o serviço.

As funções RBAC padrão tem as seguintes funcionalidades:
* **Proprietário**: pode submeter tarefas, monitorizar tarefas, cancelar tarefas a partir de qualquer utilizador e configurar a conta.
* **Contribuidor**: pode submeter tarefas, monitorizar tarefas, cancelar tarefas a partir de qualquer utilizador e configurar a conta.
* **Leitor**: monitorizar tarefas.

Utilize a função de Data Lake Analytics programador para permitir que os programadores de U-SQL utilizar o serviço do Data Lake Analytics. Pode utilizar a função do programador do Data Lake Analytics:
* Submeta tarefas.
* Monitorizar o progresso das tarefas submetidas por qualquer utilizador e o estado da tarefa.
* Consulte os scripts U-SQL de tarefas submetidas por qualquer utilizador.
* Cancele apenas as seus próprios tarefas.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicionar utilizadores ou grupos de segurança para uma conta de Data Lake Analytics

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **(IAM) do controlo de acesso** > **adicionar**.
3. Selecione uma função.
4. Adicione um utilizador.
5. Clique em **OK**.

>[!NOTE]
>Se um utilizador ou um grupo de segurança tem de submeter tarefas, estes também necessitam de permissão na conta de armazenamento. Para obter mais informações, consulte [proteger os dados armazenados no Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gerir tarefas

### <a name="submit-a-job"></a>Submeter uma tarefa

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.

2. Clique em **nova tarefa**. Para cada tarefa, configure:

    1. **Nome da tarefa**: O nome da tarefa.
    2. **Prioridade**: números inferiores têm maior prioridade. Se duas tarefas são colocados em fila, aquele com o valor de prioridade inferior executa primeiro.
    3. **Paralelismo**: O número máximo de processos de computação para reservar para esta tarefa.

3. Clique em **Submeter Tarefa**.

### <a name="monitor-jobs"></a>Monitorizar trabalhos

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **ver todas as tarefas**. É apresentada uma lista de todas as tarefas de Active Directory e recentemente concluídas na conta.
3. Opcionalmente, clique em **filtro** para ajudar a encontrar as tarefas por **intervalo de tempo**, **nome da tarefa**, e **autor** valores. 

### <a name="monitoring-pipeline-jobs"></a>Monitorizar tarefas de pipeline
As tarefas que façam parte de um pipeline funcionam em conjunto, normalmente, sequencialmente, para realizar um cenário específico. Por exemplo, pode ter um pipeline que cleans, extrai, transforma, agrega a utilização informações de cliente. Tarefas de pipeline são identificadas utilizando a propriedade de "Pipeline" quando a tarefa foi submetida. Tarefas agendadas utilizando ADF V2 terão automaticamente esta propriedade preenchida. 

Para ver uma lista de tarefas U-SQL que fazem parte de pipelines: 

1. No portal do Azure, aceda às suas contas do Data Lake Analytics.
2. Clique em **tarefa Insights**. No separador "Todas as tarefas" será predefinidas, que mostra uma lista de execução, colocado em fila e terminou tarefas.
3. Clique em de **Pipeline tarefas** separador. Será apresentada uma lista de tarefas de pipeline juntamente com as estatísticas agregadas para cada pipeline.

### <a name="monitoring-recurring-jobs"></a>Monitorizar tarefas periódicas
Uma tarefa periódica é aquele que tem a mesma lógica de negócio, mas utiliza dados de entrada diferentes, sempre que é executado. Idealmente, trabalhos recorrentes devem sempre concluída com êxito e ter tempo de execução relativamente estável; monitorização destes comportamentos irá ajudar a garantir que a tarefa está em bom estada. Trabalhos recorrentes são identificados utilizando a propriedade "Recurrence". Tarefas agendadas utilizando ADF V2 terão automaticamente esta propriedade preenchida.

Para ver uma lista de tarefas U-SQL que são recorrente: 

1. No portal do Azure, aceda às suas contas do Data Lake Analytics.
2. Clique em **tarefa Insights**. No separador "Todas as tarefas" será predefinidas, que mostra uma lista de execução, colocado em fila e terminou tarefas.
3. Clique em de **tarefas periódicas** separador. Será apresentada uma lista de trabalhos recorrentes, juntamente com as estatísticas agregadas para cada tarefa periódica.

## <a name="manage-policies"></a>Gerir políticas

### <a name="account-level-policies"></a>Políticas ao nível da conta

Estas políticas que se aplicam a todos os trabalhos numa conta do Data Lake Analytics.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de AUs numa conta do Data Lake Analytics
Uma política controla o número total de análise de unidades (AUs), pode utilizar a sua conta do Data Lake Analytics. Por predefinição, o valor é definido para 250. Por exemplo, se este valor é definido para 250 AUs, pode fazer com que uma tarefa em execução com 250 AUs atribuídos a ou 10 tarefas em execução com 25 AUs cada. Tarefas adicionais que são submetidas são colocados em fila até terminar as tarefas em execução. Quando terminar tarefas em execução, AUs são libertados para as tarefas em fila para ser executada.

Para alterar o número de AUs para a sua conta do Data Lake Analytics:

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **AUs máximo**, mova o controlo de deslize para selecionar um valor ou introduza o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se precisar de mais do que a predefinição (250) AUs, no portal, clique em **ajuda + suporte** submeter um pedido de suporte. O número de AUs disponíveis na sua conta do Data Lake Analytics pode ser aumentado.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de tarefas que podem ser executados em simultâneo
Uma política controla o número de tarefas podem executar em simultâneo. Por predefinição, este valor é definido para 20. Se a Data Lake Analytics tem AUs disponíveis, as novas tarefas agendadas para serem de imediato até que o número total de tarefas em execução atinge o valor desta política. Quando atingir o número máximo de tarefas que podem ser executados em simultâneo, as tarefas subsequentes são colocados em fila na ordem de prioridade até à conclusão de uma ou mais tarefas em execução (dependendo da disponibilidade de AU).

Para alterar o número de tarefas que podem ser executados em simultâneo:

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **máximo número de executar tarefas**, mova o controlo de deslize para selecionar um valor ou introduza o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se precisar de executar mais do que o número predefinido (20) de tarefas, no portal, clique em **ajuda + suporte** submeter um pedido de suporte. O número de tarefas que podem ser executados em simultâneo na sua conta do Data Lake Analytics pode ser aumentado.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Quanto aos metadados de tarefa de manter e de recursos 
Quando os utilizadores executarem tarefas U-SQL, o serviço do Data Lake Analytics mantém relacionados todos os ficheiros. Ficheiros relacionados incluem o script U-SQL, os ficheiros DLL referenciados o script U-SQL, recursos compilados e estatísticas. Os ficheiros estão na pasta /system/ da conta de armazenamento do Azure Data Lake predefinida. Esta política controla quanto estes recursos são armazenados antes de estes sejam eliminados automaticamente (a predefinição é 30 dias). Pode utilizar estes ficheiros para depuração e para otimização de desempenho de tarefas que irá voltar a executar no futuro.

Para alterar o período de tempo para manter os metadados de tarefa e os recursos:

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **dias a manter a tarefa consulta**, mova o controlo de deslize para selecionar um valor ou introduza o valor na caixa de texto.  
4. Clique em **Guardar**.

### <a name="job-level-policies"></a>Políticas ao nível da tarefa
Com as políticas ao nível da tarefa, pode controlar os AUs máximos e que utilizadores individuais (ou membros dos grupos de segurança específicos), podem definir das tarefas que submetem de prioridade máxima. Isto permite-lhe controlar os custos tarifas pelos utilizadores. Também permite-lhe controlo pode ter o efeito que tarefas agendadas no tarefas de produção de alta prioridade que estão em execução na mesma conta do Data Lake Analytics.

O Data Lake Analytics tem duas políticas que podem ser definidas ao nível da tarefa:

* **AU valor limite por tarefa**: os utilizadores apenas podem submeter as tarefas que têm cópia de segurança para este número de AUs. Por predefinição, este limite é o mesmo que o limite máximo de AU para a conta.
* **Prioridade**: os utilizadores apenas podem submeter as tarefas com prioridade inferior ou igual a este valor. Tenha em atenção que um número mais alto significa uma prioridade mais baixa. Por predefinição, está definida para 1, que é a prioridade mais elevada possível.

Há uma política predefinida definida em cada conta. A política predefinida é aplicada a todos os utilizadores da conta. Pode definir políticas adicionais de utilizadores e grupos específicos. 

> [!NOTE]
> Políticas ao nível da conta e políticas ao nível da tarefa aplicam-se em simultâneo.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um grupo ou utilizador específico

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **limites de submissão de tarefa**, clique em de **Adicionar política** botão. Em seguida, selecione ou introduza as seguintes definições:
    1. **Nome da política de computação**: introduza um nome de política, para relembrá-do objetivo da política.
    2. **Selecionar utilizador ou grupo**: selecione o utilizador ou grupo esta política aplica-se a.
    3. **Definir o limite de AU tarefa**: definir o limite de AU aplica-se para o utilizador ou grupo selecionado.
    4. **Definir o limite de prioridade**: definir o limite de prioridade que se aplica para o utilizador ou grupo selecionado.

4. Clique em **OK**.

5. A nova política é listada no **predefinido** tabela de política, em **limites de submissão de tarefa**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Eliminar ou editar uma política existente

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Em **limites de submissão de tarefa**, localizar a política que pretende editar.
4.  Para ver o **eliminar** e **editar** opções, na coluna mais à direita da tabela, clique em **...** .

### <a name="additional-resources-for-job-policies"></a>Recursos adicionais para políticas de tarefa
* [Mensagem de blogue de descrição geral de política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Mensagem de blogue de políticas ao nível da conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Mensagem de blogue de políticas ao nível da tarefa](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics utilizando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerir o Azure Data Lake Analytics utilizando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

