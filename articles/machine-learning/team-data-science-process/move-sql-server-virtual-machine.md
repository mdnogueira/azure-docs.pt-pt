---
title: "Mover dados para o SQL Server numa máquina virtual do Azure | Microsoft Docs"
description: Mova dados de ficheiros simples ou a partir de um servidor de SQL no local para o SQL Server numa VM do Azure.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: b8c936163e8e0880d3518f44dba107a0393fd11f
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server numa máquina virtual do Azure
Este tópico descreve as opções para mover dados de ficheiros simples (formatos de CSV ou TSV) ou de um servidor de SQL no local para o SQL Server numa máquina virtual do Azure. Estas tarefas para mover dados para a nuvem fazem parte do processo de ciência de dados de equipa.

Para um tópico descreve as opções para mover dados para uma base de dados do SQL do Azure para o Machine Learning, consulte [mover dados para uma base de dados do SQL do Azure para o Azure Machine Learning](move-sql-azure.md).

O **menu** abaixo as ligações para tópicos que descrevem como a ingestão de dados nos outros ambientes de destino onde os dados podem ser armazenados e processados durante o processo de ciência do equipa dados (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

A tabela seguinte resume as opções para mover dados para o SQL Server numa máquina virtual do Azure.

| <b>ORIGEM</b> | <b>DESTINO: SQL Server numa VM do Azure</b> |
| --- | --- |
| <b>Ficheiro simples</b> |1. <a href="#insert-tables-bcp">Utilitário de cópia da linha de comandos em massa (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Consulta SQL de inserção em massa</a><br> 3. <a href="#sql-builtin-utilities">Gráficos utilitários incorporados no SQL Server</a> |
| <b>SQL Server no local</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Implementar uma base de dados do SQL Server para um Assistente de VM do Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportar para um ficheiro simples</a><br> 3. <a href="#sql-migration">Assistente de migração de base de dados do SQL Server</a> <br> 4. <a href="#sql-backup">Base de dados back cópias de segurança e restauro</a><br> |

Tenha em atenção que este documento parte do princípio de que os comandos SQL são executados do SQL Server Management Studio ou no Explorador de base de dados do Visual Studio.

> [!TIP]
> Como alternativa, pode utilizar [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar e agendar um pipeline que irá mover dados para uma VM do SQL Server no Azure. Para obter mais informações, consulte [copiar dados com o Azure Data Factory (atividade de cópia)](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Pré-requisitos
Este tutorial parte do princípio de que tem:

* Um **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um **conta do storage do Azure**. Irá utilizar uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Depois de ter criado a conta de armazenamento, terá de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [gerir as chaves de acesso de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Aprovisionado **do SQL Server numa VM do Azure**. Para obter instruções, consulte [configurar uma máquina virtual do servidor SQL do Azure como um servidor de bloco de notas IPython para análise avançada](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Mover dados a partir de uma origem de ficheiro simples para o SQL Server numa VM do Azure
Se os dados num ficheiro simples (disposto num formato de linha/coluna),-pode ser movido para a VM do SQL Server no Azure através de métodos seguintes:

1. [Utilitário de cópia da linha de comandos em massa (BCP)](#insert-tables-bcp)
2. [Consulta SQL de inserção em massa](#insert-tables-bulkquery)
3. [Gráficos utilitários incorporados no SQL Server (importar/exportar, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Utilitário de cópia da linha de comandos em massa (BCP)
BCP é um utilitário da linha de comandos instalado com o SQL Server e é uma das formas mais rápidas para mover os dados. Funciona em todos os três variantes de SQL Server (SQL Server no local, SQL Azure e VM do SQL Server no Azure).

> [!NOTE]
> **Onde os meus dados devem ser para BCP?**  
> Embora não seja necessário, ter ficheiros que contêm dados de origem localizados no mesmo computador como o destino do SQL Server permite transferências mais rápidas (rede velocidade vs disco local velocidade de e/s). Pode mover os ficheiros simples que contém os dados para a máquina onde o SQL Server é instalado utilizando a cópia de ficheiros várias as ferramentas como [AZCopy](../../storage/common/storage-use-azcopy.md), [Explorador de armazenamento do Azure](http://storageexplorer.com/) ou windows copiar/colar através do protocolo de ambiente de trabalho remoto (RDP).
>
>

1. Certifique-se de que a base de dados e tabelas são criadas na base de dados de SQL Server de destino. Eis um exemplo de como fazê-lo nesse utilizando o `Create Database` e `Create Table` comandos:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Gere o ficheiro de formato que descreve o esquema da tabela ao emitir o comando seguinte a partir da linha de comandos da máquina onde está instalado o bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Inserir os dados na base de dados utilizando o comando bcp da seguinte forma. Isto deve trabalhar a partir da linha de comandos partindo do princípio de que o SQL Server está instalado no mesmo computador:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Otimizar o BCP insere** consulte o artigo seguinte ['Diretrizes para otimizar a importação de em massa'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) para otimizar a essas inserções.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Parallelizing inserções de movimento de dados mais rápido
Se os dados que está a mover for grandes, que pode acelerar coisas em simultâneo executando vários comandos do BCP em paralelo num Script do PowerShell.

> [!NOTE]
> **Macrodados ingestão** para otimizar o carregamento para conjuntos de dados de grandes e muito grande de dados, as tabelas de base de dados lógico e físico utilizando várias tabelas de partição e grupos de ficheiros de partição. Para obter mais informações sobre como criar e carregar dados para tabelas de partição, consulte [paralelas tabelas de partição de SQL de carga](parallel-load-sql-partitioned-tables.md).
>
>

O script do PowerShell de exemplo abaixo demonstram inserções paralelas com o bcp:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
      }


    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }


    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }

    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserção em massa
[Efetuar a consulta de SQL de inserção em massa](https://msdn.microsoft.com/library/ms188365) podem ser utilizados para importar dados para a base de dados de ficheiros de linha/coluna com base em (tipos suportados são abrangidos o[preparar dados em massa exportar ou importar (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tópico.

Eis alguns comandos de exemplo para inserção em massa tem como abaixo:  

1. Analisar os dados e definir as opções personalizadas antes de importar para se certificar de que a base de dados do SQL Server assume que o mesmo formato de quaisquer campos especiais, tais como as datas. Eis um exemplo de como definir o formato de data como dia de ano mês (se os dados contém a data no formato de dia de ano meses):

        SET DATEFORMAT ymd;    
2. Importe dados a utilizar as instruções de importação em volume:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Utilitários incorporados no SQL Server
Pode utilizar o SQL Server integrações Services (SSIS) para importar dados para a VM do SQL Server no Azure a partir de um ficheiro simples.
SSIS está disponível em dois ambientes de studio. Para obter mais informações, consulte [Integration Services (SSIS) e ambientes de Studio](https://technet.microsoft.com/library/ms140028.aspx):

* Para obter detalhes sobre o SQL Server Data Tools, consulte [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Para obter detalhes sobre o Assistente de importação/exportação, consulte [Assistente de exportação e importação do servidor SQL](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Mover dados do SQL Server no local para o SQL Server numa VM do Azure
Também pode utilizar as seguintes estratégias de migração:

1. [Implementar uma base de dados do SQL Server para um Assistente de VM do Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar para ficheiro simples](#export-flat-file)
3. [Assistente de migração de base de dados do SQL Server](#sql-migration)
4. [Base de dados back cópias de segurança e restauro](#sql-backup)

Iremos descrevem cada um deles abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implementar uma base de dados do SQL Server para um Assistente de VM do Microsoft Azure
O **implementar uma base de dados do SQL Server para um assistente do Microsoft Azure VM** é uma forma simple e recomendada para mover dados a partir de uma instância do SQL Server no local para o SQL Server numa VM do Azure. Para obter passos detalhados, bem como ver um debate de outras alternativas, consulte [migrar uma base de dados para o SQL Server numa VM do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportar para ficheiro simples
Vários métodos que podem ser utilizados em massa a exportação de dados de um servidor de SQL no local, conforme documentado no [em massa de importação e exportação de dados (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) tópico. Este documento aborda o programa de cópia em massa (BCP) como um exemplo. Assim que os dados são exportados para um ficheiro simples, pode ser importado para outro servidor de SQL Server através da importação em volume.

1. Exportar os dados do SQL Server no local para um ficheiro com o utilitário bcp de forma

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Criar a base de dados e a tabela na VM do SQL Server no Azure com o `create database` e `create table` para o esquema da tabela exportadas no passo 1.
3. Crie um ficheiro de formato para descrever o esquema da tabela de dados a ser exportado/importado. Detalhes do ficheiro de formato descritos [criar um ficheiro de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Geração de ficheiro de formato ao executar o BCP da máquina do SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Geração de ficheiro de formato quando em execução BCP remotamente um servidor de SQL

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Utilizar qualquer um dos métodos descritos na secção [mover dados a partir do ficheiro de origem](#filesource_to_sqlonazurevm) para mover os dados em ficheiros simples para um SQL Server.

### <a name="sql-migration"></a>Assistente de migração de base de dados do SQL Server
[Assistente de migração de base de dados do SQL Server](http://sqlazuremw.codeplex.com/) fornece uma forma amigável de utilizador para mover dados entre duas instâncias do SQL server. Permite que o utilizador mapear o esquema de dados entre origens e tabelas de destino, selecione os tipos de coluna e várias outras funcionalidades. Utiliza a cópia em massa (BCP) nos bastidores. Uma captura de ecrã do ecrã de boas-vindas para que o Assistente de migração de base de dados do SQL Server é mostrada abaixo.  

![Assistente de migração do SQL Server][2]

### <a name="sql-backup"></a>Base de dados back cópias de segurança e restauro
SQL Server suporta:

1. [Base de dados back cópias de segurança e restaurar a funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (ambos para um ficheiro local ou bacpac exportar para o blob) e [aplicações de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx) (utilizando bacpac).
2. Capacidade de criar diretamente as VMs de SQL Server no Azure com uma base de dados copiado ou copiar para uma base de dados existente do SQL Azure. Para obter mais detalhes, consulte [utilizar o Assistente de base de dados de cópia](https://msdn.microsoft.com/library/ms188664.aspx).

Uma captura de ecrã da cópia de segurança da base de dados de segurança/restauro opções do SQL Server Management Studio é mostrado abaixo.

![Ferramenta de importação do SQL Server][1]

## <a name="resources"></a>Recursos
[Migrar uma base de dados para o SQL Server numa VM do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Descrição geral do SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
