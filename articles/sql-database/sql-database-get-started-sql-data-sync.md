---
title: "Configurar a sincronização de dados de SQL do Azure (pré-visualização) | Microsoft Docs"
description: "Este tutorial mostra como configurar a sincronização de dados de SQL do Azure (pré-visualização)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: b356bc9db9e883c2514953b516d6dd51c1807610
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="set-up-sql-data-sync-preview"></a>Configurar a sincronização de dados do SQL Server (pré-visualização)
Neste tutorial, irá aprender a configurar a sincronização de dados SQL do Azure através da criação de um grupo de sincronização de híbridos que contenha as instâncias de SQL Database do Azure e SQL Server. O novo grupo de sincronização está completamente configurado e sincroniza na agenda que definir.

Este tutorial parte do princípio de que tem, pelo menos, de alguma experiência anterior com a base de dados SQL e com o SQL Server. 

Para obter uma descrição geral da sincronização de dados do SQL Server, consulte [sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados de SQL do Azure (pré-visualização)](sql-database-sync-data.md).

Para concluir exemplos do PowerShell que mostram como configurar a sincronização de dados do SQL Server, consulte os artigos seguintes:
-   [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Utilizar o PowerShell para sincronizar entre uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Passo 1 – criar grupo de sincronização

### <a name="locate-the-data-sync-settings"></a>Localizar as definições de sincronização de dados

1.  No seu browser, navegue para o portal do Azure.

2.  No portal, localize as bases de dados do SQL Server a partir do seu Dashboard ou a partir do ícone de bases de dados SQL na barra de ferramentas.

    ![Lista de bases de dados SQL do Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  No **bases de dados SQL** página, selecione a base de dados existente do SQL Server que pretende utilizar como a base de dados do hub para sincronização de dados. Abre a página de base de dados do SQL Server.

4.  Na página de base de dados do SQL Server para a base de dados selecionada, selecione **sincronização para outras bases de dados**. Abre a página de sincronização de dados.

    ![Sincronizar a outra opção de bases de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Criar um novo grupo de sincronização

1.  Na página de sincronização de dados, selecione **novo grupo de sincronização**. O **novo grupo de sincronização** página abre-se com o passo 1, **criar grupo de sincronização**, realçado. O **criar grupo de sincronização de dados** é aberta a página também.

2.  No **criar grupo de sincronização de dados** página, efetue os seguintes procedimentos:

    1.  No **nome do grupo de sincronização** campo, introduza um nome para o novo grupo de sincronização.

    2.  No **base de dados de metadados de sincronização** secção, escolha se pretende criar uma nova base de dados (recomendado) ou utilizar uma base de dados existente.

        > [!NOTE]
        > A Microsoft recomenda a criação de uma base de dados nova, vazio para utilizar como base de dados de metadados da sincronização. Sincronização de dados cria tabelas nesta base de dados e executa uma carga de trabalho frequente. Esta base de dados é automaticamente partilhado como base de dados de metadados da sincronização de todos os grupos de sincronização na região selecionada. Não é possível alterar a base de dados de metadados de sincronização ou o respetivo nome sem a remover.

        Se tiver escolhido **nova base de dados**, selecione **criar nova base de dados.** O **base de dados SQL** é aberta a página. No **base de dados SQL** página, atribua um nome e configure a nova base de dados. Em seguida, selecione **OK**.

        Se tiver escolhido **utilizar base dados existente**, selecione a base de dados da lista.

    3.  No **sincronização automática** secção, primeiro selecione **no** ou **desativar**.

        Se tiver escolhido **no**, no **a frequência de sincronização** secção, introduza um número e selecione segundos, minutos, horas ou dias.

        ![Especifique a frequência de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  No **resolução de conflitos** secção, selecione "Hub wins" ou "Wins de membro".

        ![Especifique a forma como são resolvidos os conflitos](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Selecione **OK** e aguarde que o novo grupo de sincronização ser criada e implementada.

## <a name="step-2---add-sync-members"></a>Passo 2 - adicionar membros de sincronização

Depois do novo grupo de sincronização é criado e implementado, o passo 2, **adicionar membros de sincronização**, estiver realçado no **novo grupo de sincronização** página.

No **base de dados do Hub** secção, introduza as credenciais existentes para o servidor de base de dados do SQL Server que está localizada a base de dados do hub. Não introduza *novo* credenciais nesta secção.

![Base de dados do hub foi adicionado ao grupo de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Adicionar uma base de dados SQL do Azure

No **base de dados do membro** secção, opcionalmente, adicione uma base de dados do SQL do Azure para o grupo de sincronização selecionando **adicionar uma base de dados do Azure**. O **configurar a base de dados do Azure** é aberta a página.

No **configurar a base de dados do Azure** página, efetue os seguintes procedimentos:

1.  No **nome de membro de sincronização** campo, forneça um nome para o novo membro de sincronização. Este nome é diferente do nome da base de dados própria.

2.  No **subscrição** campo, selecione a subscrição do Azure associada para fins de faturação.

3.  No **Azure SQL Server** campo, selecione o servidor de base de dados existente do SQL Server.

4.  No **SQL Database do Azure** campo, selecione a base de dados existente do SQL Server.

5.  No **indicações de sincronização** campo, selecione sincronização bidirecional, para o Hub ou do Hub.

    ![Adicionar um novo membro de sincronização da base de dados SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  No **Username** e **palavra-passe** campos, introduza as credenciais existentes para o servidor de base de dados do SQL Server que está localizada a base de dados do membro. Não introduza *novo* credenciais nesta secção.

7.  Selecione **OK** e aguarde que o novo membro de sincronização ser criada e implementada.

    ![Novo membro de sincronização da base de dados SQL foi adicionado](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a>Adicionar uma base de dados do SQL Server no local

No **base de dados do membro** secção, opcionalmente, adicione um servidor de SQL no local para o grupo de sincronização selecionando **adicionar uma base de dados do On-Premises**. O **configurar On-Premises** é aberta a página.

No **configurar On-Premises** página, efetue os seguintes procedimentos:

1.  Selecione **escolher o Gateway do agente de sincronização**. O **selecionar agente de sincronização** é aberta a página.

    ![Escolha o gateway do agente de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  No **escolher o Gateway do agente de sincronização** página, escolha se pretende utilizar um agente existente ou criar um novo agente.

    Se tiver escolhido **existentes agentes**, selecione o agente existente na lista.

    Se tiver escolhido **criar um novo agente**, efetue os seguintes procedimentos:

    1.  Transferir o software cliente do agente de sincronização a partir da ligação fornecida e instalá-lo no computador onde está localizado o SQL Server.
 
        > [!IMPORTANT]
        > Tem de abrir a porta TCP de saída 1433 na firewall para permitir que o agente de cliente a comunicar com o servidor.


    2.  Introduza um nome para o agente.

    3.  Selecione **criar e gerar chave**.

    4.  Copie a chave do agente para a área de transferência.
        
        ![Criar um novo agente de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Selecione **OK** para fechar o **selecionar agente de sincronização** página.

    6.  No computador do SQL Server, localize e execute a aplicação de agente de sincronização do cliente.

        ![Os dados de sincronizar as aplicações de agente do cliente](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  Na aplicação de agente de sincronização, selecione **chave do agente submeter**. O **configuração de base de dados de metadados de sincronização** é aberta a caixa de diálogo.

    8.  No **configuração de base de dados de metadados de sincronização** caixa de diálogo, cole a chave do agente copiada a partir do portal do Azure. Também fornecem as credenciais existentes para o servidor da SQL Database do Azure que está localizada a base de dados de metadados. (Se tiver criado uma nova base de dados de metadados, esta base de dados se encontra no mesmo servidor que a base de dados do hub.) Selecione **OK** e aguarde que a configuração concluir.

        ![Introduza as credenciais de chave e o servidor de agente](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Se obtiver um erro de firewall neste momento, terá de criar uma regra de firewall no Azure, para permitir tráfego de entrada do computador do SQL Server. Pode criar manualmente a regra no portal, mas pode encontrá-lo mais fácil para criá-la no SQL Server Management Studio (SSMS). No SSMS, tente estabelecer ligação com a base de dados do hub no Azure. Introduza o respetivo nome como \<hub_database_name\>. database.windows.net. Para configurar a regra de firewall do Azure, siga os passos na caixa de diálogo. Em seguida, regresse à aplicação o agente de sincronização do cliente.

    9.  Na aplicação do agente de sincronização do cliente, clique em **registar** para registar uma base de dados do SQL Server com o agente. O **configuração do SQL Server** é aberta a caixa de diálogo.

        ![Adicionar e configurar uma base de dados do SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. No **configuração do SQL Server** diálogo caixa, escolha se pretende ligar-se utilizando a autenticação do SQL Server ou a autenticação do Windows. Se tiver escolhido a autenticação do SQL Server, introduza as credenciais existentes. Forneça o nome do SQL Server e o nome da base de dados que pretende sincronizar. Selecione **Testar ligação** para testar as suas definições. Em seguida, selecione **guardar**. A base de dados registado aparece na lista.

        ![Base de dados do SQL Server está agora registado](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Agora pode fechar a aplicação de agente de sincronização do cliente.

    12. No portal, no **configurar On-Premises** página, selecione **selecione a base de dados.** O **selecionar base de dados** é aberta a página.

    13. No **selecionar base de dados** na página de **nome de membro de sincronização** campo, forneça um nome para o novo membro de sincronização. Este nome é diferente do nome da base de dados própria. Selecione a base de dados da lista. No **indicações de sincronização** campo, selecione sincronização bidirecional, para o Hub ou do Hub.

        ![Selecione a base de dados no local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Selecione **OK** para fechar o **selecionar base de dados** página. Em seguida, selecione **OK** para fechar o **configurar On-Premises** página e aguarde que o novo membro de sincronização ser criada e implementada. Por fim, clique em **OK** para fechar o **selecionar membros de sincronização** página.

        ![Na base de dados local adicionado ao grupo de sincronização](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Para ligar a sincronização de dados do SQL Server e o agente local, adicione o nome de utilizador à função `DataSync_Executor`. Sincronização de dados cria esta função na instância do SQL Server.

## <a name="step-3---configure-sync-group"></a>Passo 3 - configurar o grupo de sincronização

Depois dos novos membros do grupo de sincronização são criados e implementados, passo 3, **configurar grupo de sincronização**, estiver realçado no **novo grupo de sincronização** página.

1.  No **tabelas** página, selecione uma base de dados da lista de membros do grupo de sincronização e, em seguida, selecione **atualizar esquema**.

2.  Na lista de tabelas disponíveis, selecione as tabelas que pretende sincronizar.

    ![Selecione as tabelas para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Por predefinição, estão selecionadas todas as colunas na tabela. Se não quiser sincronizar todas as colunas, desative a caixa de verificação para as colunas que não pretende sincronizar. Lembre-se de que deixe a coluna de chave primária selecionada.

    ![Selecionar campos para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Por fim, selecione **guardar**.

## <a name="faq-about-setup-and-configuration"></a>FAQ sobre a configuração e a configuração

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Frequência é que a sincronização de dados pode sincronizar os meus dados? 
O mínimo de frequência é a cada cinco minutos.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Sincronização de dados do SQL Server totalmente criar e aprovisionar tabelas?

Se as tabelas de esquema de sincronização já não são criadas na base de dados de destino, a sincronização de dados do SQL Server (pré-visualização) cria-los com as colunas que selecionou. No entanto, este comportamento não resultar de um esquema de fidelidade completa, pelos seguintes motivos:

-   Apenas as colunas que selecionou são criadas na tabela de destino. Se algumas colunas nas tabelas de origem não fazem parte do grupo de sincronização, essas colunas não são aprovisionadas nas tabelas de destino.

-   Os índices são criados apenas para as colunas selecionadas. Se o índice de tabela de origem tem colunas que não fazem parte do grupo de sincronização, os índices não são aprovisionados nas tabelas de destino.

-   Os índices em colunas do tipo XML não são aprovisionados.

-   Restrições de verificação não são aprovisionadas.

-   Não são aprovisionados existentes acionadores em tabelas de origem.

-   As vistas e procedimentos armazenados não estão criados na base de dados de destino.

Devido a estas limitações, recomendamos as seguintes ações:
-   Para ambientes de produção, Aprovisione o esquema de completo fidelidade por si.
-   Para experimentar o serviço, a funcionalidade de aprovisionamento automático de sincronização de dados do SQL Server (pré-visualização) funciona bem.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Por que razão vejo tabelas que não conseguiu criar?  
Sincronização de dados cria tabelas lado na base de dados de registo de alterações. Não elimine-os ou sincronização de dados deixa de funcionar.

### <a name="is-my-data-convergent-after-a-sync"></a>Os meus dados é convergent depois de uma sincronização?

Não necessariamente. Um grupo de sincronização com um concentrador e três spokes realizar (A, B e C), as sincronizações são Hub para um, Hub para o B e Hub para C. Se for feita uma alteração à base de dados A *depois* do Hub a uma sincronização, o que não for escrita alteração à base de dados B ou a base de dados C até a próxima tarefa de sincronização.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Como posso obter alterações de esquema para um grupo de sincronização?

Tem de efetuar alterações ao esquema manualmente.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Como exportar e importar uma base de dados de sincronização de dados?
Depois de exportar uma base de dados como um `.bacpac` de ficheiros e importar o ficheiro para criar uma nova base de dados, terá de efetue os seguintes dois procedimentos para utilizar a sincronização de dados na base de dados nova:
1.  Limpar os objetos de sincronização de dados e tabelas de lado no **nova base de dados** utilizando [este script](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Este script elimina todos os objetos de sincronização de dados necessários da base de dados.
2.  Recrie o grupo de sincronização com a nova base de dados. Se já não precisar de grupo de sincronização de antigo, elimine-o.

## <a name="faq-about-the-client-agent"></a>FAQ sobre o agente do cliente

### <a name="why-do-i-need-a-client-agent"></a>Por que motivo precisa de um agente do cliente?

O serviço de sincronização de dados do SQL Server (pré-visualização) comunica com bases de dados do SQL Server através do agente de cliente. Esta funcionalidade de segurança impede a comunicação direta com bases de dados protegido por uma firewall. Quando o serviço de sincronização de dados do SQL Server (pré-visualização) comunica com o agente, faz-lo ao utilizar encriptados ligações e um token exclusivo ou *chave do agente*. As bases de dados do SQL Server autenticar o agente utilizando a chave de cadeia e o agente de ligação. Esta estrutura fornece um elevado nível de segurança para os seus dados.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quantas instâncias do agente local de IU pode ser executada?

Pode ser executada apenas uma instância da IU.

### <a name="how-can-i-change-my-service-account"></a>Como posso alterar a minha conta de serviço?

Depois de instalar um agente de cliente, a única forma de alterar a conta de serviço é desinstalá-lo e instalar um novo agente de cliente com a nova conta de serviço.

### <a name="how-do-i-change-my-agent-key"></a>Como posso alterar a chave do agente?

Uma chave de agente só pode ser utilizada uma vez por um agente. Não podem ser reutilizada quando remova e volte a instalar um novo agente, nem podem ser utilizado por múltiplos agentes. Se precisar de criar uma nova chave para o agente existente, tem de ser certificar-se de que a mesma chave é registada com o agente do cliente e com o serviço de sincronização de dados do SQL Server (pré-visualização).

### <a name="how-do-i-retire-a-client-agent"></a>Como posso extinguir um agente do cliente?

Imediatamente invalidar ou extinguir um agente, voltar a gerar a chave no portal, mas não submetê-las na IU do agente. Voltar a gerar uma chave invalida a chave anterior irrespective se o agente correspondente estiver online ou offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Como posso mover um agente de cliente para outro computador?

Se pretender executar o agente local partir de outro computador que está atualmente no, efetue os seguintes procedimentos:

1. Instale o agente no computador pretendido.

2. Iniciar sessão portal de sincronização de dados do SQL Server (pré-visualização) e voltar a gerar uma chave de agente para o novo agente.

3. Utilize de IU o novo agente para submeter a nova chave de agente.

4. Aguarde enquanto o agente do cliente transfere a lista de bases de dados no local que foram anteriormente registados.

5. Forneça credenciais da base de dados para todas as bases de dados que são apresentadas como inacessível. Estas bases de dados tem de ser acessíveis a partir do novo computador no qual o agente está instalado.

## <a name="next-steps"></a>Passos seguintes
Parabéns! Criou um grupo de sincronização que inclui uma instância de base de dados SQL e uma base de dados do SQL Server.

Para obter mais informações sobre a sincronização de dados do SQL Server, consulte:

-   [Sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md)
-   [Melhores práticas para a sincronização de dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   [Monitor sincronização de dados SQL do Azure com a análise de registos do OMS](sql-database-sync-monitor-oms.md)
-   [Resolver problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a sincronização de dados do SQL Server:
    -   [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utilizar o PowerShell para sincronizar entre uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Transferir a documentação da API de REST de sincronização de dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para obter mais informações sobre a base de dados SQL, consulte:

-   [Descrição geral da base de dados do SQL Server](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
