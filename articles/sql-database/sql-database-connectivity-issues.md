---
title: "Corrija um erro de ligação de SQL, erro transitório | Microsoft Docs"
description: "Saiba como resolver problemas, diagnosticar e prevenir um erro de ligação do SQL Server ou um erro transitório na SQL Database do Azure. "
keywords: "ligação SQL, cadeia de ligação, problemas de conectividade, erro transitório, erro de ligação"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: daleche
ms.openlocfilehash: dda284b45e2e8a35a7228d77afef0ad058c8ea42
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Resolver problemas, diagnosticar e prevenir erros de ligação e erros transitórios do SQL para a Base de Dados SQL
Este artigo descreve como impedir, resolver problemas, diagnosticar e atenuar os erros de ligação e erros transitórios que a aplicação cliente encontra quando interage com a SQL Database do Azure. Saiba como configurar a lógica de repetição, criar a cadeia de ligação e ajustar as outras definições de ligação.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Transitório erros (falhas transitórias)
Um erro transitório - além disso, erro transitório - tem uma causa subjacente que fica logo que seja resolvido. Uma causa ocasional de erros transitórios é quando o sistema do Azure rapidamente altera recursos de hardware para um melhor balanceamento de carga várias cargas de trabalho. Na maioria destes eventos de reconfiguração frequentemente concluídos na menos de 60 segundos. Durante este intervalo de tempo de reconfiguração, poderá ter problemas de conectividade com a SQL Database do Azure. Aplicações ligar à SQL Database do Azure devem ser criadas para esperar estes erros transitórios, lidar com os mesmos ao implementar a lógica de repetição no respetivo código em vez de analisá-las aos utilizadores como erros de aplicações.

Se o seu programa cliente estiver a utilizar ADO.NET, o programa está receber a indicação sobre o erro transitório por throw de um **SqlException**. O **número** propriedade pode ser comparada com a lista de erros transitórios perto da parte superior do tópico: [códigos de erro do SQL Server para as aplicações de cliente de base de dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Ligação versus comando
Irá efetuar a ligação do SQL Server ou estabelecê-la novamente, dependendo do seguinte:

* **Ocorre um erro transitório durante uma tentativa de ligação**: A ligação deve ser repetida após atrasando para vários segundos.
* **Ocorre um erro transitório durante um comando de consulta SQL**: O comando não deve ser repetido imediatamente. Em vez disso, após um atraso estabelecida a ligação deve ser raiz. Em seguida, o comando pode ser repetido.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Repita a lógica nos erros transitórios
Programas de cliente que ocasionalmente encontrarem um erro transitório são mais robustos quando estas contêm a lógica de repetição.

Quando o programa comunica com a SQL Database do Azure através de um 3rd middleware de terceiros, inquire com o fornecedor se o middleware contém a lógica de repetição de erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Princípios para repetição
* Uma tentativa de abrir uma ligação deve ser repetida se o erro transitório.
* Uma instrução SQL SELECT que falha com um erro transitório não deve ser repetida diretamente.
  
  * Em vez disso, estabelecer uma ligação nova e, em seguida, repita o SELECIONAR.
* Quando uma instrução SQL UPDATE falha com um erro transitório, deverá ser estabelecida uma ligação nova antes da ATUALIZAÇÃO for repetida.
  
  * A lógica de repetição tem de garantir que a transação de base de dados completo foi concluída, ou que toda a transação é revertida.

#### <a name="other-considerations-for-retry"></a>Outras considerações para repetição
* Um programa de batch é automaticamente iniciada após as horas de trabalho e que irá concluir antes de manhã cedo, pode suportar a muito patient com longos intervalos de tempo entre as respetivas tentativas.
* Um programa de interface de utilizador deve ter em conta a tendência humana para desistiam após demasiado longa uma espera.
  
  * No entanto, a solução não pode ser repetir a cada alguns segundos, porque essa política pode inundar o sistema com pedidos.

#### <a name="interval-increase-between-retries"></a>Aumento do intervalo entre tentativas
Recomendamos-se que atrase para 5 segundos antes de repetir a primeira. Repetir após um atraso mais curto que riscos de 5 segundos overwhelming o serviço em nuvem. Para cada repetição subsequente o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Ver um debate do *período de bloqueio* para clientes que utilizam ADO.NET está disponível no [SQL Server ligação agrupamento (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Pode também querer definir um número máximo de tentativas antes do programa automática termina.

#### <a name="code-samples-with-retry-logic"></a>Exemplos de código com a lógica de repetição
Exemplos de código com a lógica de repetição, numa variedade de linguagens de programação, estão disponíveis em:

* [Bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md)

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Testar a lógica de repetição
Para testar a lógica de repetição, tem de simular ou originar um erro que podem ser corrigidas enquanto o programa ainda está em execução.

##### <a name="test-by-disconnecting-from-the-network"></a>Testar através da rede
Uma forma de testar a sua lógica de repetição é desligar o computador de cliente da rede, enquanto o programa está em execução. O erro serão:

* **SqlException.Number** = 11001
* Mensagem: "o sistema anfitrião não é conhecido"

Como parte da primeira tentativa de repetição, o programa pode corrigir o misspelling e, em seguida, tentar estabelecer ligação.

Para tornar esta práticas, desligue o computador a partir da rede antes de iniciar o programa. Em seguida, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa para:

1. Adicione temporariamente 11001 a respetiva lista de erros a considerar como transitório.
2. Tente estabelecer ligação primeiro como habitualmente.
3. Depois de se encontrar o erro, remova 11001 da lista.
4. Apresenta uma mensagem a informar o utilizador ligue o computador da rede.
   * Colocar em pausa mais execução através de **Console.ReadLine** método ou uma caixa de diálogo com um botão OK. O utilizador premir a tecla Enter após o computador ligado à rede.
5. Tentativa de ligar, era esperado com êxito.

##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testar misspelling o nome de base de dados ao ligar
O programa pode propositadamente misspell o nome de utilizador antes da primeira tentativa de ligação. O erro serão:

* **SqlException.Number** = 18456
* Mensagem: "início de sessão do utilizador 'WRONG_MyUserName'."

Como parte da primeira tentativa de repetição, o programa pode corrigir o misspelling e, em seguida, tentar estabelecer ligação.

Para tornar esta práticos, o programa foi reconhecer um parâmetro de tempo de execução que faz com que o programa para:

1. Adicione temporariamente 18456 a respetiva lista de erros a considerar como transitório.
2. Adicione propositadamente 'WRONG_' para o nome de utilizador.
3. Depois de se encontrar o erro, remova 18456 da lista.
4. Remova 'WRONG_' o nome de utilizador.
5. Tentativa de ligar, era esperado com êxito.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros de .NET SqlConnection para tentativas de ligação
Se o seu programa cliente estabelece ligação à SQL Database do Azure utilizando a classe do .NET Framework **SqlConnection**, deve utilizar .NET 4.6.1 ou posterior (ou .NET Core), de modo que pode aproveitar a funcionalidade de tentativas de ligação. Detalhes da funcionalidade são [aqui](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Quando criar o [cadeia de ligação](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para sua **SqlConnection** objeto, deve coordenar os valores entre os seguintes parâmetros:

* ConnectRetryCount &nbsp; &nbsp; *(a predefinição é 1. Intervalo é de 0 e 255.)*
* ConnectRetryInterval &nbsp; &nbsp; *(a predefinição é 1 segundo. O intervalo é 1 e 60.)*
* Tempo limite da ligação &nbsp; &nbsp; *(a predefinição é 15 segundos. Intervalo é de 0 e 2147483647)*

Especificamente, os valores que escolheu devem certificar-true de igualdade seguintes:

* Tempo limite da ligação = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem = 3 e o intervalo = 10 segundos, um tempo limite de apenas 29 segundos teria não bastante dar ao sistema tempo suficiente para a repetição 3rd e final na ligação: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Ligação versus comando
O **ConnectRetryCount** e **ConnectRetryInterval** parâmetros permitem a **SqlConnection** objeto repita a operação de ligação sem informar ou bothering sua programa, tais como devolver controlo para o seu programa. As repetições podem ocorrer nas seguintes situações:

* chamada de método mySqlConnection.Open
* chamada de método mySqlConnection.Execute

Não há um subtlety. Se ocorrer um erro transitório ao seu *consulta* está a ser executado, o **SqlConnection** objeto não repita a operação de ligar e certamente não repita a consulta. No entanto, **SqlConnection** muito rapidamente verifica a ligação antes de enviar a consulta para execução. Se a verificação rápida Deteta um problema de ligação, **SqlConnection** repetem a operação ligar. Se a tentativa for bem sucedida, a consulta é enviada para execução.

#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve ser combinada com a lógica de repetição de aplicação?
Suponha que tem da aplicação lógica de repetição de personalizado robusto. -Lo poderá repita a operação ligar 4 vezes. Se adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 para a cadeia de ligação, irá aumentar a contagem de repetições e 4 * 3 = 12 tentativas. Poderá já não tenciona um número elevado de tentativas.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Ligações à base de dados SQL do Azure
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Ligação: Cadeia de ligação
A cadeia de ligação necessária para ligar à SQL Database do Azure é ligeiramente diferente da cadeia de ligação ao Microsoft SQL Server. Pode copiar a cadeia de ligação da base de dados a partir de [portal do Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Ligação: Endereço IP
Tem de configurar o servidor de base de dados SQL para aceitar comunicações do endereço IP do computador que aloja o seu programa cliente. Fazê-lo ao editar as definições de firewall através de [portal do Azure](https://portal.azure.com/).

Caso se esqueça configurar o endereço IP, o programa irá falhar com uma mensagem de erro à mão que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para obter mais informações, consulte: [como: configurar as definições da firewall na base de dados do SQL Server](sql-database-configure-firewall-settings.md)

<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Ligação: portas
Normalmente, apenas tem de garantir que a porta 1433 é aberta para comunicação de saída, no computador que aloja o programa de cliente.

Por exemplo, quando o programa de cliente é alojado num computador Windows, a Firewall do Windows no anfitrião permite ao abrir a porta 1433:

1. Abra o painel de controlo
2. &gt;Todos os itens do painel de controlo
3. &gt;Firewall do Windows
4. &gt;Definições avançadas
5. &gt;Regras de saída
6. &gt;Ações
7. &gt;Nova regra

Se o seu programa cliente está alojado numa máquina virtual do Azure (VM), deve ler:<br/>[Portas para além de 1433 para ADO.NET 4.5 e a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter informações gerais sobre cofiguration do endereço IP e portas, consulte: [firewall da SQL Database do Azure](sql-database-firewall-configure.md)

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Ligação: ADO.NET 4.6.1
Se o programa que utiliza classes ADO.NET como **SqlConnection** para ligar à SQL Database do Azure, recomendamos que utilize o .NET Framework versão 4.6.1 ou superior.

ADO.NET 4.6.1:

* Para a base de dados SQL do Azure, há Fiabilidade melhorada quando abrir uma ligação utilizando o **SqlConnection.Open** método. O **abra** método agora incorpora melhor esforço repetição em resposta a falhas transitórias, mecanismos de forma a determinados erros dentro do período de tempo limite da ligação.
* Suporta o agrupamento de ligação. Isto inclui uma verificação de eficiente do que o objeto de ligação fornece o seu programa está a funcionar.

Quando utiliza um objeto de ligação de um agrupamento de ligação, recomendamos que o seu programa temporariamente fechar a ligação quando não imediatamente a utilizar. Volte a abrir uma ligação não é a forma de criar uma nova ligação é dispendiosa.

Se estiver a utilizar ADO.NET 4.0 ou anterior, recomendamos que Atualize para o ADO.NET mais recente.

* A partir de Novembro de 2015, pode [transferir ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnóstico
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnósticos: Testar se podem ligar utilitários
Se o seu programa está a conseguir ligar à SQL Database do Azure, é uma opção de diagnóstico tentar estabelecer ligação com um programa de utilitário. Idealmente, o utilitário ligaria utilizando a mesma biblioteca que utiliza o seu programa.

Em qualquer computador Windows, pode tentar estes utilitários:

* SQL Server Management Studio (ssms.exe), que estabelecerá a ligação utilizando ADO.NET.
* SQLCMD.exe, que estabelecerá a ligação utilizando [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).

Assim que estiver ligado, teste se funciona de uma consulta de SQL SELECT curta.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnósticos: Verificar as portas abertas
Suponhamos suspeitar que as tentativas de ligação estão a falhar devido a problemas de porta. No seu computador pode executar um utilitário de relatórios sobre as configurações de porta.

No Linux, os seguintes utilitários poderão ser úteis:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * (Alterar o valor de exemplo para o endereço IP).

No Windows a [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilitário poderão ser úteis. Eis uma execução de exemplo que consultados a situação de porta num servidor SQL Database do Azure e que foi executada num computador portátil:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: Os erros de registo
Um problema intermitente é por vezes, melhor diagnosticado por deteção de um padrão geral sobre dias ou semanas.

O cliente pode ajudar um diagnóstico pelo registo de todos os erros que encontrar. Poderá correlacionar as entradas de registo com os dados de erro SQL Database do Azure regista próprio internamente.

Enterprise biblioteca 6 (EntLib60) oferece classes .NET gerida para ajudá-lo com o registo:

* [5 - tão fácil como baixar desativar um registo: utilizando o bloco de aplicação de registo](http://msdn.microsoft.com/library/dn440731.aspx)

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnósticos: Examine os registos do sistema erros
Seguem-se algumas instruções Transact-SQL SELECIONE esse registos de consulta do erro e outras informações.

| Consulta de registo | Descrição |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |O [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) vista oferece informações sobre eventos individuais, incluindo alguns que podem provocar erros transitórios ou falhas de conectividade.<br/><br/>Idealmente, pode correlacionar os **start_time** ou **end_time** valores com informações sobre quando o seu programa cliente teve problemas.<br/><br/>**Sugestão:** tem de ligar para o **mestre** base de dados para executar este. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |O [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) vista oferece agregadas contagens dos tipos de eventos, para obter diagnósticos adicionais.<br/><br/>**Sugestão:** tem de ligar para o **mestre** base de dados para executar este. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnóstico: Pesquisa para eventos de problemas no registo de base de dados SQL
Pode procurar entradas sobre eventos de problemas no registo de SQL Database do Azure. Tente a seguinte instrução Transact-SQL SELECT no **mestre** base de dados:

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Alguns linhas devolvidas de sys.fn_xe_telemetry_blob_target_read_file
Em seguida, é uma linha devolvida poderá aspeto. Os valores nulos apresentados, muitas vezes, não são nulos nas outras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteca de Enterprise 6
Enterprise biblioteca 6 (EntLib60) é uma estrutura de .NET classes que ajuda a implementar clientes robustos de serviços em nuvem, é um dos quais o serviço SQL Database do Azure. Pode localizar tópicos dedicados a cada área na qual EntLib60 pode ajudá-lo, visitando primeiro:

* [Biblioteca de Enterprise 6 - Abril de 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

Lógica de repetição para processamento de erros transitórios é uma área na qual EntLib60 pode ajudá-lo:

* [4 - perseverance, o segredo de todos os Triumphs: utilizando o bloco de aplicação de processamento de falhas transitórias](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)

> [!NOTE]
> O código de origem para EntLib60 está disponível para público [transferir](http://go.microsoft.com/fwlink/p/?LinkID=290898). A Microsoft tem não planos para tornar mais atualizações de funcionalidade ou atualizações de manutenção para EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes de EntLib60 para erros transitórios e tente novamente
As seguintes classes de EntLib60 são particularmente úteis para a lógica de repetição. Todas estas estão na ou que são mais sob o espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*No espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

* **RetryPolicy** classe
  
  * **ExecuteAction** método
* **ExponentialBackoff** classe
* **SqlDatabaseTransientErrorDetectionStrategy** classe
* **ReliableSqlConnection** classe
  
  * **ExecuteCommand** método

No espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** classe
* **NeverTransientErrorDetectionStrategy** classe

Seguem-se ligações para informações sobre EntLib60:

* Livre [livro transferências: Guia para programadores para a biblioteca do Microsoft Enterprise 2nd Edition](http://www.microsoft.com/download/details.aspx?id=41145)
* Melhores práticas: [Repita orientações gerais](../best-practices-retry-general.md) tem um debate aprofundado excelente de lógica de repetição.
* Transferência do NuGet de [Enterprise biblioteca - bloco de aplicação 6.0 do processamento de erros transitórios](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloco de registo
* O bloco de registo é uma solução de elevada disponibilidade flexível e configurável que lhe permite:
  
  * Criar e armazenar mensagens de registo numa grande variedade de localizações.
  * Categorizar e filtrar mensagens.
  * Recolha a nível contextual das informações que são os requisitos de registo útil para depuração e rastreio, bem como para auditoria e geral.
* O bloco de registo deduz a funcionalidade de registo de destino de registo para que o código de aplicação é consistente, independentemente da localização e o tipo de arquivo de registo de destino.

Para mais informações, consulte: [5 - como fácil como baixar fora de registo: utilizar Logging Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código de origem do método EntLib60 IsTransient
Em seguida, a partir de **SqlDatabaseTransientErrorDetectionStrategy** classe, é o código de origem do c# para o **IsTransient** método. O código fonte esclarece que erros foram considerados transitório e worthy de repetição, a partir de Abril de 2013.

Várias **//comment** linhas removidas esta cópia para realçar o facilitar a leitura.

```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Passos seguintes
* Para resolver problemas com outros problemas comuns de ligação SQL Database do Azure, visite [resolver problemas de ligação à SQL Database do Azure](sql-database-troubleshoot-common-connection-issues.md).
* [Ligação ao servidor SQL agrupamento (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)
* [*Repetir* é um 2.0 Apache licenciado para fins gerais de repetir a biblioteca de escrita em **Python**, para simplificar a tarefa de adicionar o comportamento de repetição para praticamente qualquer coisa.](https://pypi.python.org/pypi/retrying)

