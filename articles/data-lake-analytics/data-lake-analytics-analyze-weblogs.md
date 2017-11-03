---
title: Analisar os registos de Web site utilizando o Azure Data Lake Analytics | Microsoft Docs
description: 'Saiba como analisar os registos de Web site com o Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 52d19297ae5c34f9daf5e42250a53a78e0168192
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analisar registos de sites com o Azure Data Lake Analytics
Saiba como analisar os registos de Web site utilizando o Data Lake Analytics, especialmente em localizar saída que referrers Ocorreu erros quando tentou visitar o Web site.

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2015 ou Visual Studio 2013**.
* **[Data Lake Tools para Visual Studio](http://aka.ms/adltoolsvs)**.

    Depois das ferramentas do Data Lake para Visual Studio estar instalada, verá um **Data Lake** item no **ferramentas** menu no Visual Studio:

    ![Menu do Visual Studio do U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Conhecimento básico de Data Lake Analytics e as ferramentas do Data Lake para Visual Studio**. Para começar a utilizar, consulte:

  * [Desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Uma conta de Data Lake Analytics.**  Consulte [criar uma conta do Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Instale os dados de exemplo.** No Portal do Azure, abra a conta do Data Lake Analytics e clique em **Scripts de exemplo** no menu da esquerda, em seguida, clique em **copiar dados de exemplo**. 

## <a name="connect-to-azure"></a>Ligar ao Azure
Antes de poder criar e testar quaisquer scripts U-SQL, primeiro tem de ligar para o Azure.

**Para ligar ao Data Lake Analytics**

1. Abra o Visual Studio.
2. Clique em **Data Lake > Opções e definições**.
3. Clique em **sessão**, ou **alteração utilizador** se alguém tem sessão iniciada e siga as instruções.
4. Clique em **OK** para fechar a caixa de diálogo Opções e definições.

**Para procurar as contas de Data Lake Analytics**

1. A partir do Visual Studio, abra **Explorador de servidores** por prima **CTRL + ALT + S**.
2. No **Explorador de Servidores**, expanda **Azure** e, em seguida, expanda **Data Lake Analytics**. Deve ver uma lista das suas contas do Data Lake Analytics, caso existam. Não é possível criar contas de Data Lake Analytics a partir do studio. Para criar uma conta, veja [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desenvolver aplicações U-SQL
Uma aplicação de U-SQL é basicamente um script de U-SQL. Para saber mais sobre U-SQL, consulte [introdução de U-SQL](data-lake-analytics-u-sql-get-started.md).

Pode adicionar operadores definido pelo utilizador de adição para a aplicação.  Para obter mais informações, consulte [utilizador desenvolver U-SQL definidos operadores para tarefas de Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para criar e submeter uma tarefa do Data Lake Analytics**

1. Clique em de **ficheiro > novo > projeto**.
2. Selecione o tipo de projeto U-SQL.

    ![novo projeto Visual Studio em U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. Visual studio cria uma solução com um ficheiro de script. usql.
4. Introduza o seguinte script no ficheiro de script. usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Para compreender o U-SQL, consulte [introdução à linguagem U-SQL do Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).    
5. Adicione um novo script do U-SQL ao seu projeto e introduza o seguinte:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Comutador back para o script U-SQL primeiro e, junto ao **submeter** botão, especifique a conta de análise.
7. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**. Certifique-se os resultados no painel de resultados.
8. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**.
9. Certifique-se a **conta Analytics** é a tarefa em que pretende executar a tarefa e, em seguida, clique em **submeter**. Os resultados da submissão e a ligação da tarefa ficam disponíveis na janela de Resultados das Ferramentas do Data Lake para Visual Studio quando a submissão estiver concluída.
10. Aguarde até que a tarefa é concluída com êxito.  Se a tarefa falhou, provavelmente está em falta o ficheiro de origem.  Consulte a secção pré-requisitos deste tutorial. Para informações adicionais de resolução de problemas, consulte [monitorizar e resolver problemas de tarefas do Azure Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Quando a tarefa estiver concluída, deverá ver o ecrã seguinte:

    ![o Data lake analytics analisar registos de sites de blogues](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Agora, repita os passos 7 a 10 para **Script1.usql**.

**Para ver o resultado da tarefa**

1. No **Explorador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda a sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, faça duplo clique na conta de Armazenamento do Data Lake predefinida e, em seguida, clique em **Explorador**.
2. Faça duplo clique em **amostras** para abrir a pasta e, em seguida, faça duplo clique em **saídas**.
3. Faça duplo clique em **UnsuccessfulResponsees.log**.
4. Pode também fazer duplo clique no ficheiro de saída dentro da vista do gráfico da tarefa para navegar diretamente para a saída.

## <a name="see-also"></a>Consultar também
Para iniciar o Data Lake Analytics com ferramentas diferentes, veja:

* [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução ao Data Lake Analytics com o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao Data Lake Analytics com o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)
