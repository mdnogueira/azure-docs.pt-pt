---
title: "Ligar à base de dados SQL com C e C++ | Microsoft Docs"
description: "Utilize o código de exemplo neste início rápido para criar uma moderna aplicação com C++ e de segurança por poderosa base de dados relacional na nuvem com o SQL Database do Azure."
services: sql-database
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: 
ms.assetid: 07d9e0b1-3234-4f17-a252-a7559160a9db
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 03/06/2017
ms.author: edmacauley
ms.openlocfilehash: 7c78f90c14c1915c760720948d6d7ae99ceb6f1d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Ligar à base de dados SQL com C e C++
Esta mensagem é diversificada C e C++ programadores tentar ligar-se a BD SQL do Azure. Este é dividido em secções pelo que pode ir para a secção que capture melhor o seu interesse. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Pré-requisitos para o tutorial C/C++
Certifique-se de que tem os itens seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Tem de instalar os componentes de linguagem C++ para compilar e executar este exemplo.
* [Desenvolvimento de Linux do Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Se estiver a desenvolver no Linux, também tem de instalar a extensão do Visual Studio Linux. 

## <a id="AzureSQL"></a>Base de dados SQL do Azure e do SQL Server em máquinas virtuais
SQL do Azure está incorporada no Microsoft SQL Server e foi concebida para fornecer uma elevada disponibilidade, performant e serviço dimensionável. Existem várias vantagens para utilizar o SQL Azure através da base de dados confidenciais em execução no local. Com o Azure SQL Server não tem de instalar, configurar, manter ou gerir a sua base de dados, mas apenas o conteúdo e a estrutura da base de dados. Coisas típicas que iremos preocupar com bases de dados como a tolerância a falhas e redundância são todos incorporadas. 

Atualmente, o Azure tem duas opções para alojar cargas de trabalho do SQL server: SQL database do Azure, base de dados como um serviço e o SQL server em máquinas virtuais (VM). Não podemos irá obter detalhes sobre as diferenças entre estes dois com a exceção de base de dados SQL do Azure é a melhor bet para novas aplicações baseadas na nuvem tirar partido das reduções de custos e fornecem de otimização do desempenho que serviços em nuvem. Se estiver a considerar a migração ou para expandir as suas aplicações no local para a nuvem, do SQL Server na máquina virtual do Azure pode descobrir melhor para si. Para manter as coisas simples para este artigo, vamos criar uma base de dados SQL do Azure. 

## <a id="ODBC"></a>Tecnologias de acesso a dados: ODBC e OLE DB
A ligação a BD SQL do Azure é não diferente e existem atualmente duas formas de ligar às bases de dados: ODBC (Open Database connectivity) e OLE DB (objeto de ligação e Embedding base de dados). Nos últimos anos, a Microsoft tem alinhada com [ODBC para acesso a dados relacionais nativo](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC é relativamente simples e também mais rapidamente do que OLE DB. A advertência apenas aqui é que o ODBC a utilizar uma API de C-estilo antiga. 

## <a id="Create"></a>Passo 1: Criar a base de dados SQL do Azure
Consulte o [introdução página](sql-database-get-started-portal.md) para saber como criar uma base de dados de exemplo.  Em alternativa, pode seguir este [breve vídeo de dois minutos](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) para criar uma base de dados SQL do Azure no portal do Azure.

## <a id="ConnectionString"></a>Passo 2: Obter a cadeia de ligação
Depois da base de dados SQL do Azure tiver sido aprovisionada, terá de realizar os seguintes passos para determinar as informações de ligação e adicionar o IP de cliente para acesso de firewall. 

No [portal do Azure](https://portal.azure.com/), aceda a sua base de dados SQL do Azure cadeia de ligação de ODBC utilizando o **Mostrar cadeias de ligação de base de dados** listado como parte da secção Descrição geral da base de dados: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Copie o conteúdo a **ODBC (inclui Node.js) [autenticação do SQL Server]** cadeia. Podemos utilizar esta cadeia mais tarde para ligar a partir do nosso interpretador de linha de comandos do C++ ODBC. Esta cadeia fornece detalhes, tais como o controlador, o servidor e outros parâmetros de ligação de base de dados. 

## <a id="Firewall"></a>Passo 3: Adicionar o IP para a firewall
Consulte a secção de firewall para o servidor de base de dados e adicione o [IP de cliente para a firewall seguindo estes passos](sql-database-configure-firewall-settings.md) para se certificar de que pode estabelecer uma ligação com êxito: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Neste momento, configurou a BD SQL do Azure e está pronto para ligar a partir do código C++. 

## <a id="Windows"></a>Passo 4: Ligar a partir de uma aplicação do Windows C/C++
Pode ligar facilmente à sua [BD SQL do Azure através de ODBC no Windows utilizando este exemplo](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) que baseia-se com o Visual Studio. O exemplo implementa um interpretador de linha de comandos de ODBC pode ser utilizada para ligar ao nosso BD SQL do Azure. Este exemplo assume o um ficheiro de ficheiro (DSN) do nome de origem da base de dados como um argumento da linha de comandos ou a cadeia de ligação verboso copiou anteriormente do portal do Azure. Apresente a página de propriedades para este projeto e cole a cadeia de ligação como um argumento do comando conforme mostrado aqui: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Certifique-se de que fornecer os detalhes de autenticação adequado para a base de dados como parte dessa cadeia de ligação de base de dados. 

Inicie a aplicação compilá-la. Deverá ver a seguinte janela validar uma ligação com êxito. Pode, inclusive, executar alguns comandos básicos do SQL Server como **criar tabela** para validar a conectividade da sua base de dados:

![Comandos do SQL Server](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Em alternativa, pode criar um ficheiro DSN utilizando o assistente é iniciado quando não existem argumentos do comando são fornecidos. Recomendamos que experimente esta opção, bem como. Pode utilizar este ficheiro DSN para automatização e proteger as definições de autenticação: 

![Criar ficheiro DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Parabéns! Poderá ter agora ligado com êxito para o Azure SQL C++ e ODBC a utilizar no Windows. Pode continuar a ler para o fazer, bem como a plataforma Linux. 

## <a id="Linux"></a>Passo 5: Ligar a partir de uma aplicação do Linux C/C++
Caso ainda não teve ainda as novidades, Visual Studio agora permite-lhe desenvolver, bem como a aplicação C++ Linux. Pode ler sobre este cenário novo no [Visual C++ para o desenvolvimento de Linux](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blogue. Para criar para o Linux, precisa de um computador remoto onde está a executar o distro Linux. Se não tiver uma disponível, pode definir uma cópia de segurança rapidamente utilizando [máquinas virtuais do Linux do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Para este tutorial, informe-nos partem do princípio de que tem uma distribuição Ubuntu 16.04 Linux configurar. Os passos aqui devem também se aplicam ao Ubuntu 15.10, Red Hat 6 e 7 do Red Hat. 

Os seguintes passos instalar as bibliotecas necessárias para SQL Server e ODBC para sua distro:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Inicie o Visual Studio. Em Ferramentas -> Opções -> plataforma cruzada -> Gestor de ligações, adicione uma ligação à sua caixa de Linux: 

![Opções de ferramentas](./media/sql-database-develop-cplusplus-simple/tools.png)

Uma vez estabelecido ligação através de SSH, crie um modelo de projeto vazio (Linux): 

![Novo modelo de projeto](./media/sql-database-develop-cplusplus-simple/template.png)

Em seguida, pode adicionar um [C novo ficheiro de origem e substitua-o com este conteúdo](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Utilizar o SQLAllocHandle de APIs de ODBC, SQLSetConnectAttr e SQLDriverConnect, deve ser capaz de inicializar e estabelecer uma ligação à base de dados. Como com o exemplo de ODBC do Windows, é necessário substituir a chamada de SQLDriverConnect com os detalhes da sua parâmetros de cadeia de ligação de base de dados copiados anteriormente do portal do Azure. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

A última coisa a fazer antes de o compilar consiste em Adicionar **odbc** como uma dependência de biblioteca: 

![A adição de ODBC como uma biblioteca de entrada](./media/sql-database-develop-cplusplus-simple/lib.png)

Para iniciar a aplicação, abra a consola do Linux do **depurar** menu: 

![Consola do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Se a ligação foi efetuada com êxito, deverá ver o nome de base de dados atual, impresso na consola do Linux: 

![Saída da janela de consola do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Parabéns! Concluiu com êxito o tutorial e pode agora ligar para a BD SQL do Azure de C++ em plataformas Windows e Linux.

## <a id="GetSolution"></a>Obter a solução completa do tutorial C/C++
Pode encontrar a solução GetStarted que contém todos os exemplos neste artigo no github:

* [Exemplo de ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), transferir o exemplo de ODBC do Windows C++ para ligar ao SQL do Azure
* [Exemplo de ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), transferir o exemplo de ODBC do Linux C++ para ligar ao SQL do Azure

## <a name="next-steps"></a>Passos seguintes
* Reveja o [descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [referência da API de ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todas as [capacidades da Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)

