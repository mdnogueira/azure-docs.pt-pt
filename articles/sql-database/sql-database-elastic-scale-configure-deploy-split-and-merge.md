---
title: "Implementar um serviço de divisão de intercalação | Microsoft Docs"
description: "Utilize a intercalação de divisão demasiado para mover dados entre bases de dados em partição horizontal."
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: 9a993c0f-7052-46cd-aa59-073bea8d535a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 203e1f8842c229088102412afa5de8f967837041
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-a-split-merge-service"></a>Implementar um serviço de divisão/intercalação
A ferramenta de intercalação de divisão permite-lhe mover dados entre bases de dados em partição horizontal. Consulte [mover dados entre bases de dados de nuvem de escalamento horizontal](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Transferir os pacotes de divisão de intercalação
1. Transferir a versão mais recente do NuGet da [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Abra uma linha de comandos e navegue para o diretório onde transferiu nuget.exe. A transferência inclui comandos do PowerShell.
3. Transferir o pacote de divisão de intercalação mais recente para o diretório atual com o comando abaixo:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Os ficheiros são colocados num diretório com o nome **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** onde *x.x.xxx.x* reflete o número de versão. Localizar os ficheiros do serviço de divisão de intercalação no **content\splitmerge\service** subdiretório e os scripts do PowerShell de divisão de intercalação (e dlls de cliente necessários) no **content\splitmerge\powershell** subdiretório.

## <a name="prerequisites"></a>Pré-requisitos
1. Crie uma base de dados de BD SQL do Azure que será utilizado como a base de dados de estado de intercalação de divisão. Aceda ao [Portal do Azure](https://portal.azure.com). Crie um novo **base de dados SQL**. Dê um nome de base de dados e criar um novo administrador e a palavra-passe. Lembre-se de que registe o nome e a palavra-passe para utilização posterior.
2. Certifique-se de que o servidor de BD SQL do Azure permite que os serviços do Azure ligar ao mesmo. No portal, no **as definições da Firewall**, certifique-se a **permitir o acesso aos serviços do Azure** definição está definida como **no**. Clique no ícone "Guardar".
   
   ![Serviços permitidos][1]
3. Crie uma conta de armazenamento do Azure que será utilizada para a saída de diagnóstico. Aceda ao portal do Azure. Na barra da esquerda, clique em **novo**, clique em **dados + armazenamento**, em seguida, **armazenamento**.
4. Crie um serviço em nuvem do Azure que irá conter o seu serviço de divisão de intercalação.  Aceda ao portal do Azure. Na barra da esquerda, clique em **novo**, em seguida, **computação**, **serviço em nuvem**, e **criar**. 

## <a name="configure-your-split-merge-service"></a>Configurar o serviço de divisão de intercalação
### <a name="split-merge-service-configuration"></a>Configuração do serviço de divisão de intercalação
1. Na pasta na qual transferiu as assemblagens de divisão de intercalação, crie uma cópia do **ServiceConfiguration.Template.cscfg** ficheiro enviada juntamente com **SplitMergeService.cspkg** e mude o nome **Serviceconfiguration. Cscfg**.
2. Abra **serviceconfiguration. Cscfg** num editor de texto como o Visual Studio que valida a entradas, tais como o formato de thumbprints de certificado.
3. Criar uma nova base de dados ou escolha uma base de dados existente para servir como a base de dados de estado para operações de divisão de intercalação e obter a cadeia de ligação dessa base de dados. 
   
   > [!IMPORTANT]
   > Neste momento, a base de dados de estado tem de utilizar o agrupamento em Latim (SQL Server\_Latin1\_geral\_CP1\_CI\_AS). Para obter mais informações, consulte [nome de agrupamento do Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Com BD SQL do Azure, a cadeia de ligação é, normalmente, o formato:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Introduza esta cadeia de ligação no ficheiro cscfg em ambos os **SplitMergeWeb** e **SplitMergeWorker** secções de função na definição de ElasticScaleMetadata.
5. Para o **SplitMergeWorker** função, introduza uma cadeia de ligação válida ao armazenamento do Azure para o **WorkerRoleSynchronizationStorageAccountConnectionString** definição.

### <a name="configure-security"></a>Configurar a segurança
Para obter instruções detalhadas para configurar a segurança do serviço, consulte o [configuração de segurança de divisão de intercalação](sql-database-elastic-scale-split-merge-security-configuration.md).

Para efeitos de uma implementação de teste simples para este tutorial, um conjunto mínimo de passos de configuração será executada para obter o serviço de cópia de segurança e em execução. Estes passos ativam apenas uma conta de computador/executá-los para comunicar com o serviço.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Criar um novo diretório e deste diretório execute o seguinte comando utilizando um [linha de comandos do programador para Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) janela:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

É-lhe pedido para uma palavra-passe proteger a chave privada. Introduza uma palavra-passe segura e confirmá-la. Em seguida, é-lhe pedido para a palavra-passe a ser utilizado mais uma vez depois disso. Clique em **Sim** no final importá-la para o arquivo de raiz de autoridades de certificação fidedigna.

### <a name="create-a-pfx-file"></a>Criar um ficheiro PFX
Execute o seguinte comando a partir da janela do mesma onde foi executada makecert; Utilize a mesma palavra-passe que utilizou para criar o certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importar o certificado de cliente para o arquivo pessoal
1. No Explorador do Windows, faça duplo clique **MyCert.pfx**.
2. No **Assistente para importar certificados** selecione **utilizador atual** e clique em **seguinte**.
3. Confirme o caminho do ficheiro e clique em **seguinte**.
4. Escreva a palavra-passe, deixe **incluir propriedades expandidas todos os** marcada e clique em **seguinte**.
5. Deixe **selecionar automaticamente o arquivo de certificados [...]**  marcada e clique em **seguinte**.
6. Clique em **concluir** e **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Carregar o ficheiro PFX para o serviço em nuvem
1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Selecione **serviços em nuvem**.
3. Selecione o serviço em nuvem que criou acima para o serviço de divisão/intercalação.
4. Clique em **certificados** no menu superior.
5. Clique em **carregar** na barra inferior.
6. Selecione o ficheiro PFX e introduza a mesma palavra-passe, conforme apresentado acima.
7. Depois de concluída, copie o thumbprint de certificado da nova entrada na lista.

### <a name="update-the-service-configuration-file"></a>Atualizar o ficheiro de configuração de serviço
Cole o thumbprint do certificado copiado acima para o atributo de valor do thumbprint destas definições.
Para a função de trabalho:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Para a função da web:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

. Tenha em atenção que, para produção implementações separam certificados deve ser utilizada para a AC, de encriptação, o certificado de servidor e certificados de cliente. Para obter instruções detalhadas sobre isto, consulte [configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Implementar o serviço
1. Aceda ao [Portal do Azure](https://manage.windowsazure.com).
2. Clique em de **serviços em nuvem** separador à esquerda e selecione o serviço em nuvem que criou anteriormente.
3. Clique em **Dashboard**.
4. Escolha o ambiente de teste, em seguida, clique em **carregue uma nova implementação de teste**.
   
   ![Transição][3]
5. Na caixa de diálogo, introduza uma etiqueta de implementação. Para o 'Pacote de' e 'Configuração', clique em 'Do Local' e escolha o **SplitMergeService.cspkg** de ficheiros e o ficheiro cscfg que configurou anteriormente.
6. Certifique-se de que a caixa de verificação com etiqueta **implementar mesmo que uma ou mais funções contenham uma única instância** está marcada.
7. Prima o botão de marcas de escala no direito de parte inferior para iniciar a implementação. Espere que o Se demorar alguns minutos a concluir.

   ![Carregar][4]

## <a name="troubleshoot-the-deployment"></a>Resolver problemas de implementação
Se a função da web falhar a ficar online, é provável que um problema com a configuração de segurança. Certifique-se de que o SSL está configurado conforme descrito acima.

Se a função de trabalho não consegue ficar online, mas é concluída com êxito a função da web, é provavelmente um problema ao ligar à base de dados de estado que criou anteriormente.

* Certifique-se de que a cadeia de ligação no seu cscfg está correta.
* Certifique-se de que o servidor e base de dados existem e de que o id de utilizador e palavra-passe estão corretos.
* Para a BD SQL do Azure, a cadeia de ligação deve ter o formato:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Certifique-se de que o nome do servidor não começa com **https://**.
* Certifique-se de que o servidor de BD SQL do Azure permite que os serviços do Azure ligar ao mesmo. Para tal, Abra https://manage.windowsazure.com, clique em "Bases de dados do SQL Server" no lado esquerdo, clique em "Servidores" na parte superior e selecione o seu servidor. Clique em **configurar** na parte superior e certifique-se de que o **serviços do Azure** definição está definida como "Yes". (Consulte a secção de pré-requisitos na parte superior deste artigo).

## <a name="test-the-service-deployment"></a>Testar a implementação de serviço
### <a name="connect-with-a-web-browser"></a>Ligar com um browser
Determine o ponto final da web do serviço da divisão de intercalação. Pode encontrar isto no portal clássico do Azure acedendo ao **Dashboard** do seu serviço em nuvem e à procura em **URL do Site** no lado direito. Substitua **http://** com **https://** , uma vez que as definições de segurança predefinidas desativar o ponto final de HTTP. Carregar a página para este URL para o seu browser.

### <a name="test-with-powershell-scripts"></a>Testar com scripts do PowerShell
A implementação e o seu ambiente podem ser testadas através da execução de scripts do PowerShell de exemplo incluído.

Os ficheiros de script incluídos são:

1. **SetupSampleSplitMergeEnvironment.ps1** -configura uma camada de dados de teste para dividir/intercalação (consulte a tabela abaixo para uma descrição detalhada)
2. **ExecuteSampleSplitMerge.ps1** -executa operações de teste no teste (consulte a tabela abaixo para uma descrição detalhada) de camada de dados
3. **GetMappings.ps1** - nível superior script imprime terminar o estado atual dos mapeamentos de partições horizontais de exemplo.
4. **ShardManagement.psm1** -script de programa auxiliar que encapsula num wrapper a API de ShardManagement
5. **SqlDatabaseHelpers.psm1** -script de programa auxiliar para criar e gerir bases de dados SQL
   
   <table style="width:100%">
     <tr>
       <th>Ficheiro do PowerShell</th>
       <th>Passos</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Cria uma base de dados do Gestor de mapa do ID de partição horizontal</td>
     </tr>
     <tr>
       <td>2.    Criar 2 partições horizontais bases de dados.
     </tr>
     <tr>
       <td>3.    Cria um mapa de partições horizontais para essas bases de dados (elimina quaisquer partições horizontais existente mapeia nessas bases de dados). </td>
     </tr>
     <tr>
       <td>4.    Cria uma tabela de exemplo pequeno em ambas as shards e preenche a tabela de uma das seguintes os shards.</td>
     </tr>
     <tr>
       <td>5.    Declara o SchemaInfo para a tabela em partição horizontal.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Ficheiro do PowerShell</th>
       <th>Passos</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Envia um pedido de divisão para o serviço de divisão de intercalação web front-end que divide meio os dados a partir de partições horizontais primeiro para o ID de partição horizontal segundo.</td>
     </tr>
     <tr>
       <td>2.    Consulta o front-end da web para o estado do pedido de divisão e aguarda até que o pedido seja concluído.</td>
     </tr>
     <tr>
       <td>3.    Envia um pedido de intercalação para o serviço de divisão de intercalação web front-end que move os dados das partições horizontais segundo de volta para o ID de partição horizontal primeiro.</td>
     </tr>
     <tr>
       <td>4.    Consulta o front-end da web para o estado do pedido de intercalação e aguarda até que o pedido seja concluído.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Utilize o PowerShell para verificar a implementação
1. Abra uma nova janela do PowerShell e navegue para o diretório onde transferiu o pacote de divisão de intercalação e, em seguida, navegue para o diretório de "powershell".
2. Criar um servidor de base de dados SQL do Azure (ou escolha um servidor existente) onde serão criados o Gestor de mapa de partições horizontais e shards.
   
   > [!NOTE]
   > O script de SetupSampleSplitMergeEnvironment.ps1 cria todas estas bases de dados no mesmo servidor por predefinição para manter o script simples. Isto não é uma restrição do serviço de intercalação de divisão de si próprio.
   >
   
   Um início de sessão de autenticação de SQL com acesso de leitura/escrita para os DBs serão necessários para o serviço de divisão de intercalação mover dados e atualizar o mapa de partições horizontais. Uma vez que o serviço de divisão de intercalação é executado na nuvem, não suporta atualmente a autenticação integrada.
   
   Certifique-se de que o servidor de SQL do Azure está configurado para permitir o acesso do endereço IP da máquina estes scripts a executar. Pode encontrar esta definição sob o servidor de SQL do Azure / configuração / endereços IP permitidos.
3. Execute o script de SetupSampleSplitMergeEnvironment.ps1 para criar o ambiente de exemplo.
   
   Execução deste script irá eliminar os dados enviados quaisquer dados de gestão de mapa de partições horizontais existentes estruturas na base de dados do Gestor de mapa de partições horizontais manager e os shards. Poderá ser útil voltar a executar o script se pretender reinicializar o mapa de partições horizontais ou shards.
   
   Linha de comandos de exemplo:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Execute o script de Getmappings.ps1 para ver os mapeamentos de que existem atualmente no ambiente de exemplo.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Execute o script de ExecuteSampleSplitMerge.ps1 para executar uma operação de divisão (mover meio os dados de partições horizontais primeiro para o ID de partição horizontal segundo) e, em seguida, uma operação de intercalação (mover os dados de volta para o ID de partição horizontal primeiro). Se configurou SSL e à esquerda do ponto final de http desativado, certifique-se de que utiliza o ponto final https://.
   
   Linha de comandos de exemplo:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Se receber o abaixo erro, é mais provável um problema com certificado do Web ponto final. Tente ligar-se ao ponto final do Web com o seu browser favorito e verifique se existe um erro de certificado.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Se foi bem sucedida, o resultado deverá ser semelhante a abaixo:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Experimente outros tipos de dados! Todos estes scripts demorar o parâmetro - ShardKeyType opcional que permite-lhe especificar o tipo de chave. A predefinição é Int32, mas também pode especificar Int64, Guid ou Binary.

## <a name="create-requests"></a>Pedidos de criação
O serviço pode ser utilizado, utilizando a IU da web ou ao importar e utilizar o módulo do PowerShell de SplitMerge.psm1 que irá submeter pedidos através da função da web.

O serviço pode mover dados em tabelas em partição horizontal e tabelas de referência. Uma tabela em partição horizontal tem uma coluna de chave de fragmentação e tem os dados da linha diferente em cada partição horizontal. Uma tabela de referência não é a, pelo que contém os mesmos dados de linha em todas as partições horizontais. As tabelas de referência são úteis para dados que não são alterados frequentemente e é utilizado para associação, com a tabelas nas consultas.

Para efetuar uma operação de intercalação dividido, tem de declarar as tabelas em partição horizontal e tabelas de referência que pretende ter movido. Isto é conseguido com a **SchemaInfo** API. Esta API está no **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** espaço de nomes.

1. Para cada tabela em partição horizontal, crie um **ShardedTableInfo** que descrevem o nome do esquema da tabela principal do objeto (opcional, por predefinição "dbo"), o nome da tabela e o nome da coluna nessa tabela que contém a chave de fragmentação.
2. Para cada tabela de referência, crie um **ReferenceTableInfo** que descrevem o nome do esquema da tabela principal do objeto (opcional, por predefinição "dbo") e o nome da tabela.
3. Adicionar os objetos de TableInfo acima para um novo **SchemaInfo** objeto.
4. Obter uma referência a um **ShardMapManager** objeto e chamada **GetSchemaInfoCollection**.
5. Adicionar o **SchemaInfo** para o **SchemaInfoCollection**, fornecendo o nome do mapa de partições horizontais.

Um exemplo desta situação pode ser visto no SetupSampleSplitMergeEnvironment.ps1 script.

O serviço de divisão de intercalação não criar a base de dados de destino (ou o esquema para quaisquer tabelas na base de dados) para si. Têm de ser previamente criadas antes de enviar um pedido para o serviço.

## <a name="troubleshooting"></a>Resolução de problemas
Poderá ver o abaixo mensagem ao executar os scripts do powershell de exemplo:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Este erro significa que o certificado SSL não está configurado corretamente. Siga as instruções na secção 'Ligar com um browser'.

Se não podem submeter pedidos vir isto:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Neste caso, de verificação do ficheiro de configuração, em particular a definição de **WorkerRoleSynchronizationStorageAccountConnectionString**. Normalmente, este erro indica que a função de trabalho não foi possível com êxito inicializar a base de dados de metadados na primeira utilização. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

