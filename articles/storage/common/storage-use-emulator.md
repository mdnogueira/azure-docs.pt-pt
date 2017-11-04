---
title: Utilizar o emulador de armazenamento do Azure para desenvolvimento e teste | Microsoft Docs
description: "O emulador do storage do Azure fornece um ambiente de desenvolvimento local livre para desenvolver e testar as suas aplicações de armazenamento do Azure. Saiba como os pedidos são autenticados, como ligar para o emulador da sua aplicação e como utilizar a ferramenta da linha de comandos."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: tamram
ms.openlocfilehash: 7d86d5e8547d977c07cfbb0597b74382172a8472
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utilizar o emulador de armazenamento do Azure para desenvolvimento e teste

O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços tabela, fila e Blob do Azure para fins de desenvolvimento. Utilizar o emulador do storage, pode testar a aplicação localmente, os serviços de armazenamento sem criar uma subscrição do Azure ou incorrer em custos. Quando estiver satisfeito com a forma como a aplicação está a funcionar no emulador, pode mudar para utilizar uma conta de armazenamento do Azure na nuvem.

## <a name="get-the-storage-emulator"></a>Obter o emulador de armazenamento
O emulador de armazenamento está disponível como parte do [SDK do Microsoft Azure](https://azure.microsoft.com/downloads/). Também pode instalar o emulador de armazenamento utilizando o [instalador autónomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (transferência direta). Para instalar o emulador do storage, tem de ter privilégios administrativos no seu computador.

O emulador de armazenamento atualmente é executado apenas no Windows. Os considerar um emulador do storage para o Linux, uma opção é a Comunidade mantida, o emulador de armazenamento de código aberto [Azurite](https://github.com/arafato/azurite).

> [!NOTE]
> Dados criados numa versão do emulador de armazenamento não são garantidos esteja acessível ao utilizar uma versão diferente. Se precisar de manter os dados para a longo prazo, recomendamos que armazene os dados numa conta de armazenamento do Azure, em vez de no emulador do storage.
> <p/>
> O emulador de armazenamento depende de versões específicas das bibliotecas de OData. Substituir as DLLs de OData utilizado pelo emulador de armazenamento com outras versões não é suportada e pode provocar um comportamento inesperado. No entanto, qualquer versão do OData suportado pelo serviço de armazenamento pode ser utilizado para enviar pedidos para o emulador.
>

## <a name="how-the-storage-emulator-works"></a>Como funciona o emulador de armazenamento
O emulador de armazenamento utiliza uma instância local do Microsoft SQL Server e o sistema de ficheiros local para emular os serviços de armazenamento do Azure. Por predefinição, o emulador de armazenamento utiliza uma base de dados no Microsoft SQL Server 2012 Express LocalDB. Pode optar por configurar o emulador do storage para aceder a uma instância local do SQL Server em vez da instância da LocalDB. Para obter mais informações, consulte o [início e inicializar o emulador do storage](#start-and-initialize-the-storage-emulator) secção neste artigo.

O emulador do storage estabelece ligação ao SQL Server ou LocalDB através da autenticação Windows.

Existem algumas diferenças de funcionalidade entre o emulador de armazenamento e serviços de armazenamento do Azure. Para obter mais informações sobre estas diferenças, consulte o [diferenças entre o emulador do storage e o armazenamento do Azure](#differences-between-the-storage-emulator-and-azure-storage) secção neste artigo.

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar o emulador de armazenamento
Para iniciar o emulador do storage do Azure:
1. Selecione o **iniciar** botão ou prima o **Windows** chave.
1. Começa a escrever `Azure Storage Emulator`.
1. Selecione o emulador na lista de aplicações apresentadas.

Quando inicia o emulador de armazenamento, será apresentada uma janela de linha de comandos. Pode utilizar esta janela de consola para iniciar e parar o emulador do storage, limpar dados, obter o estado e inicializar o emulador. Para obter mais informações, consulte o [referência de ferramenta da linha de comandos do emulador de armazenamento](#storage-emulator-command-line-tool-reference) secção neste artigo.

Quando o emulador está em execução, verá um ícone na área de notificação da barra de tarefas do Windows.

Quando fechar a janela de linha de comandos do emulador de armazenamento, o emulador do storage continuará a ser executado. Para repor a janela de consola do emulador do Storage, siga os passos anteriores como se a iniciar o emulador de armazenamento.

Da primeira vez que executa o emulador de armazenamento, o ambiente do armazenamento local é inicializado para si. O processo de inicialização cria uma base de dados no LocalDB e reservas portas HTTP para cada serviço de armazenamento local.

O emulador de armazenamento é instalado por predefinição para `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Pode utilizar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) para trabalhar com recursos do emulador de armazenamento local. Procure "(desenvolvimento)" em "As contas do Storage" na árvore de recursos de Explorador de armazenamento depois de ter instalado e iniciado o emulador de armazenamento.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar o emulador de armazenamento para utilizar uma base de dados diferente do SQL Server
Pode utilizar a ferramenta de linha de comandos de emulador de armazenamento ao inicializar o emulador do storage para apontar para uma instância de base de dados do SQL Server que não seja a instância da LocalDB predefinido:

1. Abra a janela de consola do emulador do Storage, conforme descrito no [início e inicializar o emulador do storage](#start-and-initialize-the-storage-emulator) secção.
1. Na janela da consola, escreva o seguinte comando, onde `<SQLServerInstance>` é o nome da instância do SQL Server. Para utilizar LocalDB, especifique `(localdb)\MSSQLLocalDb` que a instância do SQL Server.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Também pode utilizar o comando seguinte, que direciona o emulador para utilizar a instância predefinida do SQL Server:

  `AzureStorageEmulator.exe init /server .\\`

  Em alternativa, pode utilizar o seguinte comando, reinicializa a base de dados para a instância da LocalDB predefinido:

  `AzureStorageEmulator.exe init /forceCreate`

Para obter mais informações sobre estes comandos, consulte [referência de ferramenta da linha de comandos do emulador de armazenamento](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Pode utilizar o [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para gerir as instâncias do SQL Server, incluindo a instalação de LocalDB. No SMSS **ligar ao servidor** caixa de diálogo, especifique `(localdb)\MSSQLLocalDb` no **nome do servidor:** campo ligar à instância da LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticação de pedidos para o emulador de armazenamento
Depois de ter instalado e iniciado o emulador do storage, pode testar o seu código nele. Tal como acontece com o Storage do Azure na nuvem, todos os pedidos que efetuar contra o emulador de armazenamento tem de ser autenticado, a menos que é um pedido anónimo. Pode autenticar pedidos contra o emulador de armazenamento através da autenticação de chave partilhada ou com uma assinatura de acesso partilhado (SAS).

### <a name="authenticate-with-shared-key-credentials"></a>Autenticar com as credenciais de chave partilhada
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre cadeias de ligação, consulte [cadeias de ligação de configurar o armazenamento de Azure](../storage-configure-connection-string.md).

### <a name="authenticate-with-a-shared-access-signature"></a>Autenticar com uma assinatura de acesso partilhado
Algumas bibliotecas de cliente do storage do Azure, tais como a biblioteca de Xamarin, só suportam a autenticação com um token de assinatura (SAS) de acesso partilhado. Pode criar o token SAS utilizando uma ferramenta como o [Explorador de armazenamento](http://storageexplorer.com/) ou outra aplicação que suporta a autenticação de chave partilhada.

Também pode gerar um token SAS com o Azure PowerShell. O exemplo seguinte gera um token SAS com todas as permissões para um contentor do blob:

1. Instalar o Azure PowerShell se ainda não o fez (utilizando a versão mais recente do Azure PowerShell cmdlets é recomendado). Para obter instruções de instalação, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Abra o Azure PowerShell e execute os comandos seguintes, substituindo `ACCOUNT_NAME` e `ACCOUNT_KEY==` com as suas próprias credenciais, e `CONTAINER_NAME` com um nome à sua escolha:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

A assinatura de acesso partilhado URI resultante para o novo contentor deve ser semelhante a:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

A assinatura de acesso partilhado criada com este exemplo é válida durante um dia. A assinatura concede acesso completo (leitura, escrita, eliminar, lista) para os blobs no contentor.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Using partilhado assinaturas de acesso (SAS) no Storage do Azure](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Endereçamento recursos no emulador do storage
Os pontos finais de serviço para o emulador do storage são diferentes das de uma conta de armazenamento do Azure. A diferença é porque o computador local não efetuar a resolução do nome de domínio, a necessidade dos pontos finais emulador de armazenamento para endereços locais.

Quando resolver um recurso de uma conta de armazenamento do Azure, utilize o esquema seguinte. O nome da conta faz parte do nome de anfitrião do URI e o recurso que está a ser resolvido faz parte do caminho URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Por exemplo, o URI seguinte é um endereço válido para um blob de uma conta de armazenamento do Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

No entanto, o emulador de armazenamento, porque o computador local não efetuar a resolução do nome de domínio, o nome da conta é parte do caminho URI em vez do nome de anfitrião. Utilize o seguinte formato URI para um recurso no emulador do storage:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Por exemplo, o seguinte endereço poderá ser utilizado para aceder a um blob no emulador do storage:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Os pontos finais de serviço para o emulador do storage são:

* Serviço blob:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Serviço fila:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Serviço tabela:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>A conta secundária com RA-GRS de endereçamento
A partir da versão 3.1, o emulador de armazenamento suporta a replicação georredundante de acesso de leitura (RA-GRS). Para recursos de armazenamento na nuvem e no emulador local, pode aceder à localização secundária ao acrescentar - secundário para o nome da conta. Por exemplo, o seguinte endereço poderá ser utilizado para aceder a um blob com secundária só de leitura no emulador do storage:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Para obter acesso programático para o secundário com o emulador de armazenamento, utilize a biblioteca de clientes de armazenamento para .NET versão 3.2 ou posterior. Consulte o [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obter mais detalhes.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referência de ferramenta da linha de comandos do emulador de armazenamento
A partir de versão 3.0, é apresentada uma janela da consola quando inicia o emulador de armazenamento. Utilize a linha de comandos na janela da consola para iniciar e parar o emulador, bem como a consulta para o estado e efetuar outras operações.

> [!NOTE]
> Se tiver o Microsoft Azure instalado do emulador de computação, é apresentado um ícone de tabuleiro do sistema quando iniciar o emulador de armazenamento. Faça duplo clique no ícone de revela um menu que fornece uma forma gráfica para iniciar e parar o emulador de armazenamento.
>
>

### <a name="command-line-syntax"></a>Sintaxe de linha de comandos
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opções
Para ver a lista de opções, escreva `/help` na linha de comandos.

| Opção | Descrição | Comando | Argumentos |
| --- | --- | --- | --- |
| **Começar** |Inicia o emulador de armazenamento de cópia de segurança. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: iniciar o emulador no processo atual em vez de criar um novo processo. |
| **Parar** |Interrompe o emulador de armazenamento. |`AzureStorageEmulator.exe stop` | |
| **Estado** |Imprime o estado do emulador de armazenamento. |`AzureStorageEmulator.exe status` | |
| **Limpar** |Limpa os dados em todos os serviços especificados na linha de comandos. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*blob*: apaga dados de Blobs. <br/>*fila*: limpa os dados de fila. <br/>*tabela*: limpa os dados da tabela. <br/>*todos os*: limpa todos os dados em todos os serviços. |
| **Init** |Efetua uma única inicialização para configurar o emulador. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-servidor serverName\instanceName*: Especifica o servidor que aloja a instância do SQL Server. <br/>*-sqlinstance instanceName*: Especifica o nome da instância do SQL Server para ser utilizado numa instância de servidor predefinida. <br/>*-forcecreate*: forçando a criação da base de dados SQL, mesmo que já existe. <br/>*-skipcreate*: ignora a criação da base de dados SQL. Isto tem precedência sobre - forcecreate.<br/>*-reserveports*: tentar reservar as portas HTTP associadas aos serviços.<br/>*-unreserveports*: tentativas para remover as reservas para as portas HTTP associado aos serviços. Isto tem precedência sobre - reserveports.<br/>*-inprocess*: efetua inicialização no processo atual em vez de gerar um novo processo. O processo atual deve ser iniciado com permissões elevadas se alterar as reservas de porta. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador do storage e o armazenamento do Azure
Porque o emulador do storage é um ambiente emulado em execução numa instância local do SQL Server, existem diferenças de funcionalidade entre o emulador e uma conta de armazenamento do Azure na nuvem:

* O emulador do storage suporta apenas uma única conta fixa e uma chave de autenticação conhecidos.
* O emulador de armazenamento não é um serviço de armazenamento escalável e não suporta um grande número de clientes em simultâneo.
* Conforme descrito em [endereçamento recursos no emulador do storage](#addressing-resources-in-the-storage-emulator), recursos são tratados de forma diferente no emulador do storage em comparação com uma conta de armazenamento do Azure. Esta diferença-se ao facto de resolução de nomes de domínio está disponível na nuvem mas não no computador local.
* A partir da versão 3.1, a conta de emulador de armazenamento suporta a replicação georredundante de acesso de leitura (RA-GRS). No emulador, todas as contas têm RA-GRS ativada e nunca se qualquer desfasamento entre as réplicas primários e secundários. As operações de obter estatísticas de serviço Blob, obter estatísticas da fila de serviço e obter estatísticas de serviço tabela são suportadas na conta secundária e devolverá sempre o valor da `LastSyncTime` elemento resposta como a hora atual de acordo com a base de dados subjacente do SQL Server.
* O serviço de ficheiro e os pontos finais de serviço do protocolo SMB não são atualmente suportados no emulador do storage.
* Se utilizar uma versão dos serviços de armazenamento que ainda não é suportada pelo emulador, o emulador do storage devolve um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - pedido incorreto).

### <a name="differences-for-blob-storage"></a>Diferenças para o Blob storage
As seguintes diferenças aplicam-se ao Blob storage no emulador do:

* O blob storage emulador só suporta tamanhos até 2 GB.
* Cópia incremental permite instantâneos de substituída blobs para ser copiado, que devolve uma falha no serviço.
* Verificar as diferenças entre os intervalos de página Get não funcionam entre instantâneos copiados utilizando o Blob de cópia Incremental.
* Uma operação Put Blob poderá ter sucesso contra um blob que existe no emulador do storage com uma concessão ativa, mesmo que não foi especificado o ID de concessão no pedido.
* Operações não são suportadas pelo emulador de BLOBs de acréscimo. Tentativa de uma operação no blob de acréscimo devolve um erro de FeatureNotSupportedByEmulator (código de estado HTTP 400 - pedido incorreto).

### <a name="differences-for-table-storage"></a>Diferenças para armazenamento de tabelas
As seguintes diferenças se aplicam ao armazenamento de tabelas no emulador do:

* Propriedades de data no serviço tabela no emulador do storage suportam apenas o intervalo suportado pelo SQL Server 2005 (que têm de ser posterior a 1 de Janeiro de 1753). Todas as datas antes de 1 de Janeiro de 1753 são alteradas para este valor. A precisão da datas está limitada a precisão do SQL Server 2005, que significa que as datas são precisas para 1/300th de um segundo.
* O emulador do storage suporta valores de propriedade de chave de linha e chave de partição de menos de 512 bytes cada. Além disso, o tamanho total do nome da conta, o nome da tabela e nomes de propriedade de chave em conjunto não pode exceder 900 bytes.
* O tamanho total de uma linha numa tabela no emulador do storage está limitado a menos de 1 MB.
* No emulador do storage, escreva as propriedades dos dados `Edm.Guid` ou `Edm.Binary` suportam apenas a `Equal (eq)` e `NotEqual (ne)` cadeias de filtro de operadores de comparação na consulta.

### <a name="differences-for-queue-storage"></a>Diferenças para armazenamento de filas
Não existem não existem diferenças específicas para o armazenamento de filas no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de versão do emulador de armazenamento
### <a name="version-52"></a>Versão 5.2
* O emulador do storage suporta agora a versão de 2017-04-17 dos serviços de armazenamento em pontos finais do serviço tabela, fila e Blob.
* Corrigido um erro em que os valores de propriedade de tabela não foram a ser corretamente codificados.

### <a name="version-51"></a>Versão 5.1
* Corrigido um erro em que o emulador de armazenamento foi devolver o `DataServiceVersion` cabeçalho algumas respostas onde o serviço não tenha.

### <a name="version-50"></a>Versão 5.0
* O instalador do emulador de armazenamento já não verifica a existência de MSSQL existente e instala o .NET Framework.
* O instalador do emulador de armazenamento já não cria a base de dados como parte da instalação. Base de dados ainda será criado se for necessário como parte de arranque.
* Criação da base de dados já não necessita de elevação.
* As reservas de porta já não são necessárias para o arranque.
* Adiciona as seguintes opções para `init`: `-reserveports` (necessita de elevação), `-unreserveports` (necessita de elevação), `-skipcreate`.
* A opção de IU do emulador de armazenamento no ícone de tabuleiro do sistema agora inicia a interface de linha de comandos. O GUI antigo já não está disponível.
* Algumas DLLs foram removidas ou mudados.

### <a name="version-46"></a>Versão 4.6
* O emulador do storage suporta agora versão 2016-05-31 dos serviços de armazenamento em pontos finais do serviço tabela, fila e Blob.

### <a name="version-45"></a>Versão 4.5
* Corrigido um erro que provocou a inicialização e de instalação do emulador de armazenamento para falhar quando a base de dados de cópia de segurança foi mudado.

### <a name="version-44"></a>Versão 4.4
* O emulador do storage suporta agora versão 2015-12-11 dos serviços de armazenamento em pontos finais do serviço tabela, fila e Blob.
* Recolha de lixo o emulador de armazenamento dos dados blob agora é mais eficiente ao lidar com grandes quantidades de blobs.
* Corrigido um erro que provocou o contentor ACL XML para ser validado de forma ligeiramente diferente do modo como o serviço de armazenamento não-lo.
* Corrigido um erro que, por vezes, max e min valores DateTime sejam comunicados no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3
* O emulador do storage suporta agora versão 2015-07-08 dos serviços de armazenamento em pontos finais do serviço tabela, fila e Blob.

### <a name="version-42"></a>Versão 4.2
* O emulador do storage suporta agora versão 2015-04-05 dos serviços de armazenamento em pontos finais do serviço tabela, fila e Blob.

### <a name="version-41"></a>Versão 4.1
* O emulador do storage suporta agora a versão 2015-02-21 dos serviços de armazenamento na tabela, fila e Blob pontos finais de serviço, exceto as novas funcionalidades de BLOBs de acréscimo.
* Se utilizar uma versão dos serviços de armazenamento que ainda não é suportada pelo emulador, o emulador devolve uma mensagem de erro significativo. Recomendamos que utilize a versão mais recente do emulador. Se ocorrer um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - pedido incorreto),. Transfira a versão mais recente do emulador de armazenamento.
* Corrigido um erro wherein um provável de antecipação condição que causou tabela entidade de dados incorreto durante as operações de intercalação em simultâneo.

### <a name="version-40"></a>Versão 4.0
* O emulador do storage executável foi mudado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3.2
* O emulador do storage suporta agora a versão 2014-02-14 dos serviços de armazenamento em pontos finais do serviço tabela, fila e Blob. Pontos finais de serviço do ficheiro não são atualmente suportados no emulador do storage. Consulte [controlo de versões para os serviços de armazenamento do Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) para obter detalhes sobre a versão 2014-02-14.

### <a name="version-31"></a>Versão 3.1
* Armazenamento georredundante com acesso de leitura (RA-GRS) é agora suportado o emulador de armazenamento. A obter estatísticas de serviço Blob, obter estatísticas de serviço de fila e obter APIs de estatísticas de serviço tabela são suportadas para a conta secundária e devolverá sempre o valor do elemento de resposta LastSyncTime como a hora atual de acordo com a base de dados subjacente do SQL Server. Para obter acesso programático para o secundário com o emulador de armazenamento, utilize a biblioteca de clientes de armazenamento para .NET versão 3.2 ou posterior. Consulte a biblioteca de clientes de armazenamento do Microsoft Azure para referência do .NET para obter mais detalhes.

### <a name="version-30"></a>Versão 3.0
* O emulador do storage do Azure já não está incluído no mesmo pacote como o emulador de computação.
* A interface de utilizador gráfica do emulador de armazenamento foi preterida favor de uma interface de linha de comandos passível de ter scripts. Para obter detalhes sobre a interface de linha de comandos, consulte a referência de ferramenta de linha de comandos de emulador de armazenamento. A interface gráfica irá continuem a estar presentes na versão 3.0, mas só pode ser acedida quando o emulador de computação é instalado ao clicar no ícone de tabuleiro de sistema e selecionando mostrar IU do emulador de armazenamento.
* Versão 2013-08-15 dos serviços de armazenamento do Azure agora é totalmente suportada. (Anteriormente nesta versão apenas era suportada pela versão 2.2.1 do emulador do Storage pré-visualização.)

## <a name="next-steps"></a>Passos seguintes

* Avaliar o emulador de armazenamento em diferentes plataformas, mantido por Comunidade open source para [Azurite](https://github.com/arafato/azurite). 
* [Exemplos de armazenamento do Azure através do .NET](../storage-samples-dotnet.md) contém ligações para vários exemplos de código, pode utilizar quando desenvolver a sua aplicação.
* Pode utilizar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) trabalhar com recursos na sua conta de armazenamento de nuvem e no emulador do storage.
