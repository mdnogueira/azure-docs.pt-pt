---
title: "Começar a utilizar com a auditoria de base de dados SQL do Azure | Microsoft Docs"
description: Utilize a auditoria de base de dados SQL do Azure para controlar eventos de base de dados para um registo de auditoria.
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.openlocfilehash: c97a9d96dbe6d9bc9eaa189384acad7579365e82
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-sql-database-auditing"></a>Introdução à auditoria da base de dados SQL
Auditoria de base de dados SQL do Azure controla os eventos de base de dados e escreve-los para uma auditoria iniciar sessão na sua conta do storage do Azure. Auditoria também:

* Ajuda a manter a conformidade de regulamentação, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que poderão indicar preocupações para a empresa ou suspeitas de violação de segurança.

* Ativa e facilita a conformidade com as normas de conformidade, apesar de não garantir a compatibilidade. Para obter mais informações sobre o Azure programas que conformidade de padrões de suporte, consulte o [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Auditoria de descrição geral da SQL database do Azure
Pode utilizar a auditoria de base de dados SQL para:


* **Manter** um registo de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados a serem auditados.
* **Relatório** na atividade de base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar a trabalhar rapidamente com atividade e o relatório de eventos.
* **Analisar** relatórios. Pode encontrar eventos suspeitos, atividade invulgar e tendências.

Pode configurar a auditoria para diferentes tipos de categorias de evento, conforme explicado no [configurar a auditoria da base de dados](#subheading-2) secção.

Os registos de auditoria são escritos para armazenamento de Blobs do Azure na sua subscrição do Azure.


## <a id="subheading-8"></a>Definir o nível de servidor vs. a política de auditoria de nível de base de dados

Uma política de auditoria pode ser definida para uma base de dados específica ou como uma política de servidor predefinida:

* Uma política de servidor aplica-se às bases de dados recentemente criados e todas as existentes no servidor.

* Se *auditoria de blob do servidor está ativado*,- *sempre aplica-se a base de dados*. A base de dados será auditado, independentemente das definições de auditoria de base de dados.

* Ativar a auditoria de blob na base de dados, além de permitir a no servidor, *não* substituir ou alterar qualquer uma das definições de auditoria de blob de servidor. Ambas as auditorias existirá lado. Por outras palavras, a base de dados é auditada duas vezes em paralelo; uma vez pela política do servidor e uma vez pela política de base de dados.

   > [!NOTE]
   > Deve evitar a ativar a auditoria de blob do servidor e base de dados auditoria de blob em conjunto, a menos que:
    > * Pretende utilizar outro *conta de armazenamento* ou *período de retenção* para uma base de dados específica.
    > * Pretende categorias para uma base de dados específico que diferem do resto das bases de dados no servidor ou tipos de eventos de auditoria. Por exemplo, poderá ter inserções de tabela que tem de ser auditadas apenas para uma base de dados específica.
   >
   > Caso contrário, recomendamos que ativar a auditoria de blob apenas ao nível do servidor e deixe o nível de base de dados de auditoria desativada para todas as bases de dados.


## <a id="subheading-2"></a>Configurar a auditoria da base de dados
A secção seguinte descreve a configuração de auditoria no portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Vá para o **definições** painel do SQL Server da base de dados/SQL server que pretende de auditoria. No **definições** painel, selecione **deteção de ameaças e auditoria**.

    <a id="auditing-screenshot"></a>![Painel de navegação][1]
3. Se preferir configurar uma política de auditoria do servidor, pode selecionar o **ver definições do servidor** ligação no painel de auditoria da base de dados. Pode ver ou modificar o definições de auditoria do servidor. Políticas de auditoria do servidor aplicam-se às bases de dados recentemente criados e todas as existentes neste servidor.

    ![Painel de navegação][2]
4. Se preferir ativar a auditoria de blob no nível da base de dados, para **auditoria**, selecione **ON**e para **auditoria tipo**, selecione **Blob**.

    Se estiver ativada a auditoria de blob de servidor, a auditoria da base de dados configurada existirá side by side with a auditoria de blob do servidor.

    ![Painel de navegação][3]
5. Para abrir o **armazenamento de registos de auditoria** painel, selecione **detalhes armazenamento**. Selecione a conta de armazenamento do Azure onde serão guardados os registos e, em seguida, selecione o período de retenção. Os registos antigos serão eliminados. Em seguida, clique em **OK**.
   >[!TIP]
   >Para tirar o máximo partido os modelos de relatórios de auditoria, utilize a mesma conta de armazenamento para todas as bases de dados auditadas.

    <a id="storage-screenshot"></a>![Painel de navegação][4]
6. Se pretender personalizar os eventos auditados, pode fazê-lo através do PowerShell ou a API REST. 
7. Depois de configurar as definições de auditorias, pode ativar a nova funcionalidade de deteção de ameaças e configurar mensagens de correio eletrónico para receber alertas de segurança. Quando utilizar a deteção de ameaças, recebe alertas proativa em atividades de base de dados anómalas, que podem indicar a potenciais ameaças de segurança. Para obter mais informações, consulte [como começar a deteção de ameaças](sql-database-threat-detection-get-started.md).
8. Clique em **Guardar**.





## <a id="subheading-3"></a>Analisar registos de auditoria e relatórios
Os registos de auditoria são agregados na conta do storage do Azure que escolheu durante a configuração. Pode explorar os registos de auditoria utilizando uma ferramenta como [Explorador de armazenamento do Azure](http://storageexplorer.com/).

Registos de auditorias de blob são guardados como uma coleção de ficheiros de BLOBs num contentor com o nome **sqldbauditlogs**.

Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e o formato de registo, consulte o [referência de formatos de registo de auditoria de Blob](https://go.microsoft.com/fwlink/?linkid=829599).

Existem vários métodos que pode utilizar para ver os registos de auditoria de blob:

* Utilize o [portal do Azure](https://portal.azure.com).  Abra a base de dados relevante. Na parte superior da base de dados **deteção de ameaças e auditoria** painel, clique em **ver registos de auditoria**.

    ![Painel de navegação][7]

    Um **registos de auditoria** é aberto o painel, partir do qual poderá ver os registos.

    - Pode ver as datas específicas clicando **filtro** na parte superior do **registos de auditoria** painel.
    - Pode alternar entre os registos de auditoria que foram criados por uma auditoria de política de política ou a base de dados do servidor.

       ![Painel de navegação][8]

* Utilize a função de sistema **sys.fn_get_audit_file** (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre como utilizar esta função, consulte o [sys.fn_get_audit_file documentação](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Utilize **intercalar os ficheiros de auditoria** no SQL Server Management Studio (começando com o SSMS 17):
    1. A partir do menu do SSMS, selecione **ficheiro** > **abra** > **intercalar os ficheiros de auditoria**.

        ![Painel de navegação][9]
    2. O **adicionar ficheiros de auditoria** é aberta a caixa de diálogo. Selecione uma do **adicionar** opções escolher se pretende intercalar os ficheiros de auditoria a partir de um disco local ou importá-los a partir do armazenamento do Azure. É necessário para fornecer os detalhes de armazenamento do Azure e a chave da conta.

    3. Depois de todos os ficheiros de intercalação foram adicionados, clique em **OK** para concluir a operação de intercalação.

    4. Para abrir o ficheiro intercalado no SSMS, onde pode ver e analisá-lo, bem como de exportá-lo para um ficheiro XEL ou CSV ou a uma tabela.

* Utilize o [sincronizar aplicações](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que foi criado. Este é executado no Azure e utiliza o Operations Management Suite (OMS) análise de registos APIs públicas para enviar registos de auditoria SQL para OMS. A aplicação de sincronização pushes registos de auditoria SQL para análise de registos do OMS para consumo através do dashboard de análise de registos do OMS.

* Utilize o Power BI. Pode ver e analisar dados de registo de auditoria no Power BI. Saiba mais sobre [Power BI e acesso de um modelo transferível](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Transferir ficheiros de registo do contentor de blob Storage do Azure através do portal ou utilizando uma ferramenta como [Explorador de armazenamento do Azure](http://storageexplorer.com/).
    * Depois de transferir um ficheiro de registo localmente, pode fazer duplo clique o ficheiro para o abrir, visualizar e analisar os registos no SSMS.
    * Também pode transferir vários ficheiros em simultâneo através do Explorador de armazenamento do Azure. Faça duplo clique uma subpasta específica e selecione **guardar como** guardar numa pasta local.

* Métodos adicionais:
   * Depois de transferir vários ficheiros ou numa subpasta que contém ficheiros de registo, é possível intercalá-los localmente, tal como descrito nas instruções de ficheiros de auditoria de intercalação do SSMS descritas anteriormente.

   * Auditoria de blob de ver os registos através de programação:

     * Utilize o [leitor de eventos expandidos](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) c# biblioteca.
     * [Consultar os ficheiros de eventos expandidos](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) utilizando o PowerShell.




## <a id="subheading-5"></a>Práticas de produção
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditorias bases de dados de georreplicação</a>
Com georreplicação bases de dados, quando ativar a auditoria na base de dados primária a base de dados secundária tem uma política de auditoria idêntica. Também é possível configurar a auditoria na base de dados secundária ao ativar a auditoria no **servidor secundário**, independentemente da base de dados principal.

* Ao nível do servidor (**recomendado**): Ativar a auditoria em ambos os **servidor primário** , bem como o **servidor secundário** -as bases de dados primários e secundários serão cada auditados independentemente com base na respetiva respetiva política de ao nível do servidor.

* Ao nível da base de dados: Só pode ser configurado auditoria de bases de dados secundárias de nível de base de dados da base de dados primária, definições de auditoria.
   * Auditoria de blob tem de estar ativada no *base de dados primária próprio*, não o servidor.
   * Depois de ativar a auditoria de blob na base de dados primária, também irá tornar-se ativada na base de dados secundária.

     >[!IMPORTANT]
     >Com a auditoria a nível de base de dados, as definições de armazenamento para a base de dados secundária será idênticas de base de dados primária, fazendo com que o tráfego entre regionais. Recomendamos que ativar a auditoria apenas ao nível do servidor e deixe o nível de base de dados de auditoria desativada para todas as bases de dados.
<br>

### <a id="subheading-6">Regeneração da chave de armazenamento</a>
Na produção, que é provável que atualize periodicamente as chaves de armazenamento. Ao atualizar as suas chaves, terá de resave a política de auditoria. O processo é o seguinte:

1. Abra o **detalhes armazenamento** painel. No **chave de acesso de armazenamento** caixa, selecione **secundário**e clique em **OK**. Em seguida, clique em **guardar** no topo do painel de configuração de auditoria.

    ![Painel de navegação][5]
2. Aceda ao painel de configuração de armazenamento e voltar a gerar a chave de acesso primária.

    ![Painel de navegação][6]
3. Aceda ao painel de configuração de auditoria, mude a chave de acesso de armazenamento do secundário do primário para e, em seguida, clique em **OK**. Em seguida, clique em **guardar** no topo do painel de configuração de auditoria.
4. Volte ao painel de configuração de armazenamento e voltar a gerar a chave de acesso secundária (em preparação para ciclo de atualização a chave seguinte).

## <a name="manage-sql-database-auditing-using-azure-powershell"></a>Gerir a auditoria de base de dados SQL com o Azure PowerShell


* **Cmdlets do PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditing][101]
   * [Get-AzureRMSqlServerAuditing][102]
   * [Remover AzureRMSqlDatabaseAuditing][103]
   * [Remover AzureRMSqlServerAuditing][104]
   * [Conjunto AzureRMSqlDatabaseAuditing][105]
   * [Conjunto AzureRMSqlServerAuditing][106]

   Para obter um exemplo de script, consulte [configurar a auditoria e deteção de ameaças através do PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-sql-database-auditing-using-rest-api"></a>Gerir a auditoria de SQL da base de dados utilizando a REST API

* **API de REST - auditoria de Blob**:

   * [Criar ou atualizar a política de auditoria de Blob de base de dados](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Criar ou atualizar a política de auditoria de Blob de servidor](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Obter a política de auditoria de Blob de base de dados](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Obter a política de auditoria de Blob de servidor](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Obter o resultado da operação de auditoria de Blob de servidor](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
