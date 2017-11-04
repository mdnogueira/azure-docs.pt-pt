---
title: "Código de XEvent eventos ficheiros de base de dados SQL | Microsoft Docs"
description: "Fornece o PowerShell e o Transact-SQL para um exemplo de código em duas fases, que demonstra o destino de ficheiro de eventos num evento expandido na SQL Database do Azure. Storage do Azure é uma parte necessária neste cenário."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: bbb10ecc-739f-4159-b844-12b4be161231
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: genemi
ms.openlocfilehash: abf660e3fafd1a5020cdf9a6beb5b73252b72cfc
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino de ficheiro de evento expandido eventos na base de dados do SQL Server

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Pretende um exemplo de código de conclusão de uma forma robusta informações de captura e relatórios para um evento expandido.

No Microsoft SQL Server, o [destino de ficheiro de evento](http://msdn.microsoft.com/library/ff878115.aspx) é utilizado para armazenar saídas de eventos para um ficheiro de disco rígido local. Mas esses ficheiros não estão disponíveis para a SQL Database do Azure. Em vez disso, utilizamos o serviço de armazenamento do Azure para suportar o destino de ficheiro de eventos.

Este tópico apresenta um exemplo de código de duas fases:

* PowerShell, para criar um contentor de armazenamento do Azure na nuvem.
* Transact-SQL:
  
  * Para atribuir o contentor de armazenamento do Azure para um destino de ficheiro de eventos.
  * Para criar e iniciar a sessão do evento e assim sucessivamente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer base de dados que pode criar uma tabela.
  
  * Opcionalmente, pode [criar um **AdventureWorksLT** base de dados de demonstração](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (ssms.exe), idealmente, a última versão atualização mensal. 
  Pode transferir o ssms.exe mais recente do:
  
  * Tópico intitulado [transferir SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Uma ligação direta para a transferência.](http://go.microsoft.com/fwlink/?linkid=616025)
* Tem de ter o [módulos do Azure PowerShell](http://go.microsoft.com/?linkid=9811175) instalado.
  
  * Os módulos fornecem comandos como - **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Código do PowerShell contentor de armazenamento do Azure

Do PowerShell é a fase 1 do exemplo de código de duas fases.

O script começa com comandos para limpar após uma possível anterior executar e rerunnable.

1. Cole o script do PowerShell no editor de texto simples, tais como o Notepad.exe e guarde o script como um ficheiro com a extensão **. ps1**.
2. Inicie o ISE do PowerShell como administrador.
3. Na linha de comandos, escreva<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e, em seguida, prima Enter.
4. No ISE do PowerShell, abra o **. ps1** ficheiro. Execute o script.
5. O script primeiro inicia uma nova janela, no qual poderá iniciar sessão no Azure.
   
   * Se voltar a executar o script sem perturbar a sua sessão, tem a opção conveniente de comentar terminar o **Add-AzureAccount** comando.

![ISE do PowerShell, com o módulo do Azure instalado, pronto para executar o script.][30_powershell_ise]

### <a name="powershell-code"></a>Código do PowerShell

Este script do PowerShell pressupõe que já estiver a executar o cmdlet Import-Module para o módulo de AzureRm. Para obter documentação de referência, consulte [Browser de módulo do PowerShell](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Add-AzureRmAccount (or  Login-AzureRmAccount), just one time.';
#Add-AzureRmAccount;   # Same as  Login-AzureRmAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'West US';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzureRmStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Tome nota dos alguns valores com nome que o script do PowerShell imprime quando este termina. Tem de editar esses valores para o script de Transact-SQL que se segue como fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Transact-SQL. código de que utiliza o contentor de armazenamento do Azure

* Fase 1 do código de exemplo, foi executado um script do PowerShell para criar um contentor de armazenamento do Azure.
* Em seguida fase 2, o seguinte script Transact-SQL tem de utilizar o contentor.

O script começa com comandos para limpar após uma possível anterior executar e rerunnable.

O script do PowerShell indicadas alguns valores nomeados quando terminou. Tem de editar o script de Transact-SQL a utilizar esses valores. Localizar **TODO** do script Transact-SQL para localizar os pontos de edição.

1. Abra o SQL Server Management Studio (ssms.exe).
2. Ligar à base de dados SQL Database do Azure.
3. Clique para abrir um novo painel de consulta.
4. Cole o seguinte script Transact-SQL no painel de consulta.
5. Localizar todos os **TODO** no script e efetue as edições adequadas.
6. Guarde e, em seguida, execute o script.


> [!WARNING]
> O valor da chave SAS gerado pelo script do PowerShell anterior pode começar com um '?' (ponto de interrogação). Quando utiliza a chave SAS no seguinte script T-SQL, terá *remover à esquerda '?'* . Caso contrário, poderão ser bloqueados seus esforços de segurança.


### <a name="transact-sql-code"></a>Código de Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Se não for possível ligar ao executar o destino, tem de parar e reiniciar a sessão do evento:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Saída

Quando tiver concluído o script Transact-SQL, clicar numa célula sob o **event_data_XML** cabeçalho da coluna. Um  **<event>**  elemento é apresentado que mostra uma instrução de ATUALIZAÇÃO.

Eis um  **<event>**  elemento que foi gerado durante os testes:


```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```


O script de Transact-SQL anterior utilizar a seguinte função do sistema para ler o event_file:

* [fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Obter uma explicação sobre as opções avançadas para a visualização de dados de eventos expandidos está disponível em:

* [Avançadas visualização de dados de destino de eventos expandidos](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Converter o exemplo de código para ser executada no SQL Server

Suponha que pretendia executar o anterior exemplo de Transact-SQL no Microsoft SQL Server.

* Simplicidade, seria pretende substituir completamente a utilização do contentor de armazenamento do Azure com um ficheiro simple, tais como **C:\myeventdata.xel**. O ficheiro deverá ser escrito no disco rígido local do computador que aloja o SQL Server.
* Não terá qualquer tipo de instruções Transact-SQL para **CREATE MASTER KEY** e **criar CREDENCIAL**.
* No **eventos criar sessão** instrução, no respetivo **adicionar destino** cláusula, teria de substituir o valor de Http atribuído efetuadas **filename =** com uma cadeia de caminho completo como **C:\myfile.xel**.
  
  * Não existe nenhuma conta de armazenamento do Azure tem de estar envolvida.

## <a name="more-information"></a>Mais informações

Para obter mais informações sobre as contas e contentores no serviço de armazenamento do Azure, consulte:

* [Como utilizar o Blob storage a partir do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Nomenclatura e referência de contentores, Blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Trabalhar com o recipiente-raiz](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lesson 1: Criar uma política de acesso armazenada e uma assinatura de acesso partilhado um contentor do Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Lesson 2: Criar uma credencial de SQL Server utilizando uma assinatura de acesso partilhado](http://msdn.microsoft.com/library/dn466435.aspx)
* [Eventos expandidos para o Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

