---
title: "Transações distribuídas entre bases de dados de nuvem"
description: "Descrição geral de transações da base de dados elásticas com base de dados SQL do Azure"
services: sql-database
documentationcenter: 
author: torsteng
manager: jhubbard
editor: torsteng
ms.assetid: e14df7a3-7788-4cfb-bcd1-7ad6433ef1f9
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: 012fc38075285b898599517f3e6ed5a3c9eb854d
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas entre bases de dados de nuvem
Transações da base de dados elástica para Azure SQL Database (base de dados SQL) permitem-lhe executar transações que abrangem várias bases de dados na base de dados do SQL Server. Transações da base de dados elástica para a base de dados SQL estão disponíveis para aplicações de .NET através do ADO .NET e integrar com a utilização de experiência de programação familiar de [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) classes. Para obter a biblioteca, consulte [.NET Framework 4.6.1 (instalador da Web)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, o cenário necessário normalmente em execução em Coordenador de transações distribuídas ' (MSDTC) da Microsoft. Uma vez que o MSDTC não está disponível para aplicações da plataforma-como-um-serviço no Azure, a capacidade para coordenar transações distribuídas agora foi diretamente integrada na base de dados SQL. Aplicações podem ligar a uma base de dados do SQL Server para iniciar as transações distribuídas e uma das bases de dados transparente será coordenar a transação distribuída, conforme mostrado na figura seguinte. 

  ![Transações distribuídas com base de dados do SQL do Azure através de transações da base de dados elástica ][1]

## <a name="common-scenarios"></a>Cenários comuns
Transações da base de dados elástica para a base de dados SQL permitem às aplicações fazer alterações atómicas aos dados armazenados em várias diferentes bases de dados do SQL Server. A pré-visualização está centrado nas experiências do lado do cliente desenvolvimento em c# e .NET. Uma experiência de lado do servidor com o T-SQL é planeada para um horário posterior.  
Transações da base de dados elástica destina-se os seguintes cenários:

* Aplicações de múltiplas base de dados no Azure: com este cenário, dados verticalmente estão particionados em várias bases de dados na base de dados do SQL Server, de modo a que os diferentes tipos de dados residem em bases de dados diferentes. Algumas operações exigem que as alterações aos dados que são guardados em dois ou mais bases de dados. A aplicação utiliza transações da base de dados elástica para coordenar as alterações em bases de dados e certifique-se atomicity.
* Aplicações de base de dados em partição horizontal no Azure: com este cenário, a camada de dados utiliza o [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md) ou self-fragmentação para os dados de partição horizontal entre muitas bases de dados na base de dados do SQL Server. Um caso de utilização prominent é a necessidade de efetuar alterações atómicas para uma aplicação em partição horizontal do multi-inquilino quando alterações span inquilinos. Considere para a instância de uma transferência a partir de um inquilino para outro, tanto que residem em bases de dados diferentes. Um segundo caso é fragmentação detalhada para acomodar as necessidades de capacidade para um inquilino grande que por sua vez, normalmente, implica que algumas operações atómicas precisa de stretch em várias bases de dados utilizados para o mesmo inquilino. Um terceiro cenário é atómicas atualizações para fazer referência a dados que são replicados entre bases de dados. Operações transacionadas, atómicas juntamente estas linhas podem agora ser coordenadas através de várias bases de dados utilizando a pré-visualização.
  Transações da base de dados elástica utilizam consolidação de duas fases para garantir atomicity de transação entre bases de dados. É uma boa opção para transações que envolvem inferior a 100 bases de dados num momento dentro de uma única transação. Estes limites não são impostas, mas um deve esperar desempenho e taxas de êxito para transações da base de dados elástica afecta quando exceder estes limites.

## <a name="installation-and-migration"></a>Instalação e a migração
As capacidades para transações da base de dados elástica na base de dados do SQL Server são fornecidas através de atualizações a bibliotecas .NET System.Data.dll e System.Transactions.dll. DLLs Certifique-se de que consolidação de duas fases é utilizada sempre que necessário para garantir atomicity. Para começar a desenvolver aplicações através de transações da base de dados elástica, instalar [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Quando em execução numa versão anterior do .NET framework, as transações falhará promover a transação distribuída e será emitida uma exceção.

Após a instalação, pode utilizar a transação distribuída APIs Transactions com ligações à base de dados do SQL Server. Se tiver aplicações MSDTC utilizando estas APIs, basta reconstruir as suas aplicações existentes para .NET 4.6 depois de instalar o 4.6.1 Framework. Se os seus projetos .NET 4.6 de destino, que irão utilizar automaticamente os DLLs atualizados da nova versão do Framework e transações distribuídas chamadas à API em combinação com ligações de base de dados SQL agora terá êxito.

Lembre-se de que as transações de base de dados elástica não requerem instalar MSDTC. Em vez disso, transações de base de dados elástica são geridas diretamente pelo e dentro de BD do SQL. Isto simplifica significativamente os cenários de nuvem, uma vez que uma implementação de MSDTC não é necessária utilizar as transações distribuídas com base de dados SQL. Secção 4 explica em detalhe mais como implementar transações de base de dados elástica e o .NET framework necessário, juntamente com as suas aplicações na nuvem para o Azure.

## <a name="development-experience"></a>Experiência de desenvolvimento
### <a name="multi-database-applications"></a>Aplicações de múltiplas base de dados
O código de exemplo seguinte utiliza a experiência de programação familiar com .NET Transactions. A classe de TransactionScope estabelece uma transação de ambiente no .NET. (Uma "transação de ambiente" é aquele que se encontra no thread atual.) Todas as ligações abertas no TransactionScope participam na transação. Se as bases de dados diferentes participam, a transação é automaticamente elevada a transação distribuída. O resultado da transação é controlado ao definir o âmbito para indicar uma confirmação.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Aplicações de base de dados em partição horizontal
Transações da base de dados elástica para a base de dados SQL suportam também coordenação de transações distribuídas em que utilize o método de OpenConnectionForKey da biblioteca de clientes de base de dados elástica para abrir as ligações para um dados expandido terminar camada. Considere casos em que tem de garantir a consistência transacional para as alterações em vários valores de chave de fragmentação diferentes. As ligações ao shards os valores de chave de fragmentação diferentes de alojamento são mediadas OpenConnectionForKey a utilizar. No caso de geral, as ligações podem ser shards diferentes, de forma a que garantir garantias transacionais necessita de uma transação distribuída. O exemplo de código seguinte ilustra esta abordagem. Parte do princípio de que uma variável chamada shardmap é utilizada para representar um mapa de partições horizontais da biblioteca de cliente de base de dados elástica:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para serviços de nuvem do Azure
O Azure oferece vários ofertas para aplicações de .NET do anfitrião. Uma comparação das ofertas diferentes está disponível no [comparação App Service do Azure, Cloud Services e as máquinas virtuais](../app-service/choose-web-site-cloud-service-vm.md). Se o SO convidado a oferta é inferior ao .NET 4.6.1 necessários para transações elásticas, terá de atualizar o SO convidado para 4.6.1. 

Para os serviços de aplicações do Azure, as atualizações para o SO convidado não são atualmente suportadas. Para máquinas de virtuais do Azure, basta iniciar sessão na VM e execute o instalador para o .NET framework mais recente. Para os serviços de nuvem do Azure, tem de incluir a instalação de uma versão mais recente do .NET para as tarefas de arranque da sua implementação. Os conceitos e passos documentados em [instalar o .NET numa função de serviço em nuvem](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Tenha em atenção que o installer do .NET 4.6.1 poderá requerer mais armazenamento temporário durante o processo de bootstrapping no cloud services do Azure que o instalador para .NET 4.6. Para garantir uma instalação com êxito, precisa de aumentar o armazenamento temporário para o seu serviço em nuvem do Azure no seu ficheiro servicedefinition. Csdef a secção de LocalResources e as definições de ambiente da tarefa de arranque, conforme mostrado no seguinte exemplo:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>

## <a name="transactions-across-multiple-servers"></a>Transações em vários servidores
Transações de base de dados elástica são suportadas em diferentes servidores lógicas na SQL Database do Azure. Quando as transações cruzam limites do servidor lógico, os servidores de participantes primeiro tem de ser introduzidos numa relação de comunicação mútua. Assim que tiver sido estabelecida a relação de comunicação, qualquer base de dados em qualquer um dos dois servidores pode participar em transações elásticas com bases de dados de outro servidor. Com as transações expansão mais do que dois servidores lógicas, uma relação de comunicação tem de estar no local para qualquer par de servidores lógicos.

Utilize os seguintes cmdlets PowerShell para gerir relações de comunicação entre servidores para transações da base de dados elástica:

* **Novo AzureRmSqlServerCommunicationLink**: Utilize este cmdlet para criar uma nova relação de comunicação entre dois servidores lógicas na BD SQL do Azure. A relação é simétrica o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzureRmSqlServerCommunicationLink**: Utilize este cmdlet para obter as relações de comunicação existentes e as respetivas propriedades.
* **Remover AzureRmSqlServerCommunicationLink**: Utilize este cmdlet para remover uma relação existente a comunicação. 

## <a name="monitoring-transaction-status"></a>Monitorizar o estado de transação
Utilize vistas de gestão dinâmica (DMVs) na base de dados do SQL Server para o estado do monitor e o progresso de transações da base de dados elástica em curso. Todos os DMVs relacionadas com as transações são relevantes para as transações distribuídas na base de dados do SQL Server. Pode encontrar a lista correspondente de DMVs aqui: [funções (Transact-SQL) e vistas de gestão de dinâmicas relacionados de transação](https://msdn.microsoft.com/library/ms178621.aspx).

Estes DMVs são particularmente úteis:

* **sys.dm\_tran\_Active Directory\_transações**: apresenta uma lista de transações atualmente ativas e o respetivo estado. A coluna UOW (unidade de trabalho), pode identificar as transações de diferentes subordinados que pertençam a mesma transação distribuída. Todas as transações na mesma transação distribuída transportem o mesmo valor UOW. Consulte o [documentação DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais detalhes.
* **sys.dm\_tran\_base de dados\_transações**: fornece informações adicionais sobre transações, como o posicionamento da transação no registo. Consulte o [documentação DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais detalhes.
* **sys.dm\_tran\_bloqueios**: fornece informações sobre as bloqueios que atualmente estão a ser retido por transações em curso. Consulte o [documentação DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais detalhes.

## <a name="limitations"></a>Limitações
As seguintes limitações aplicam-se atualmente para transações da base de dados elástica na base de dados do SQL Server:

* Apenas as transações em bases de dados na base de dados do SQL Server são suportadas. Outros [X / abra XA](https://en.wikipedia.org/wiki/X/Open_XA) bases de dados fora da base de dados SQL e fornecedores de recursos não podem participar em transações de base de dados elásticas. Isto significa que as transações de base de dados elástica não é possível a Stretch Database no local do SQL Server e bases de dados do Azure SQL. Para as transações distribuídas no local, continue a utilizar o MSDTC. 
* Transações de uma aplicação .NET apenas coordenado de cliente são suportadas. Suporte do lado do servidor para o T-SQL, tais como iniciar a transação distribuída é planeada, mas ainda não está disponível. 
* Não são suportadas transações nos vários serviços do WCF. Por exemplo, tem um método do serviço WCF que executa uma transação. Envolvente a chamada dentro de um âmbito de transação falhará como um [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Passos seguintes
Para perguntas sobre, contacte-no [fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, adicioná-los para o [fórum de comentários da base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



