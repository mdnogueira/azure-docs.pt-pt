---
title: "Testar e depurar tarefas U-SQL através da utilização de execução local e o SDK do Azure Data Lake U-SQL | Microsoft Docs"
description: "Saiba como utilizar o Azure Data Lake Tools para Visual Studio e o SDK do Azure Data Lake U-SQL para testar e depurar tarefas U-SQL na estação de trabalho local."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Testar e depurar tarefas U-SQL utilizando local executar e o SDK do Azure Data Lake U-SQL

Pode utilizar as Ferramentas do Azure Data Lake para o Visual Studio e o SDK U-SQK do Data Lake do Azure para executar tarefas de U-SQL na estação de trabalho, tal como com o serviço do Azure Data Lake. Estas duas funcionalidades de execução local poupam tempo no teste e na depuração de tarefas U-SQL.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Compreender a pasta raiz dos dados e o caminho do ficheiro

A execução local e o SDK de U-SQL necessitam de uma pasta de raiz dos dados. A pasta de raiz dos dados é um "armazenamento local" para a conta de computação local. É equivalente à conta do Azure Data Lake Store de uma conta de Data Lake Analytics. Mudar para uma pasta raiz de dados diferentes é semelhante à mudança para uma conta de armazenamento diferentes. Se pretender aceder a dados normalmente partilhados com pastas de raiz de dados diferente, tem de utilizar caminhos absolutos os scripts. Ou, criar ligações simbólicas de sistema de ficheiros (por exemplo, **mklink** no NTFS) na pasta raiz dos dados para que apontem para os dados partilhados.

A pasta raiz dos dados é utilizada para:

- Armazenar metadados, incluindo bases de dados, tabelas, as funções valorizadas por tabela (TVFs) e assemblagens.
- Procure os caminhos de entrada e de saída que são definidos como caminhos relativos em U-SQL. Utilizar caminhos relativos torna mais fácil de implementar os seus projetos de U-SQL no Azure.

Pode utilizar um caminho relativo e um caminho absoluto local scripts U-SQL. O caminho relativo é relativo ao caminho da pasta de raiz de dados especificado. Recomendamos que utilize "/" como o separador de caminho para tornar os scripts compatível com o lado do servidor. Seguem-se alguns exemplos de caminhos relativos e os respetivos caminhos absolutos equivalentes. Nestes exemplos, C:\LocalRunDataRoot é a pasta de raiz dos dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/Input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/Input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/Input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Utilize local a executar a partir do Visual Studio

Ferramentas do Data Lake para Visual Studio fornece uma experiência de execução local do U-SQL no Visual Studio. Ao utilizar esta funcionalidade, pode:

- Execute um script de U-SQL localmente, juntamente com assemblagens c#.
- Depurar uma assemblagem c# localmente.
- Criar, ver e eliminar catálogos de U-SQL (bases de dados locais, assemblagens, esquemas e tabelas) no Explorador de servidores. Também pode encontrar o catálogo local também no Explorador de servidores.

    ![Ferramentas do Data Lake para o catálogo de local de execução local do Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

O instalador de ferramentas do Data Lake cria uma pasta de C:\LocalRunRoot a ser utilizado como a pasta de raiz de dados predefinida. O paralelismo de execução local predefinido é 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Para configurar a execução local no Visual Studio

1. Abra o Visual Studio.
2. Abra **Explorador de servidores**.
3. Expanda **Azure** > **do Data Lake Analytics**.
4. Clique em de **Data Lake** e, em seguida, clique em **opções e definições**.
5. Na árvore à esquerda, expanda **do Azure Data Lake**e, em seguida, expanda **geral**.

    ![Ferramentas do Data Lake para Visual Studio execução local configurar as definições](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Um projeto do Visual Studio U-SQL é necessário para a execução local executar. Esta peça é diferente de executar scripts U-SQL do Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Para executar um script U-SQL localmente
1. A partir do Visual Studio, abra o projeto U-SQL.   
2. Um script U-SQL no Explorador de soluções com o botão direito e, em seguida, clique em **submeter Script**.
3. Selecione **(Local)** como a conta de análise para executar o script localmente.
Também pode clicar o **(Local)** conta no topo da janela de script e, em seguida, clique em **submeter** (ou utilize o Ctrl + F5 atalho de teclado).

    ![Ferramentas do Data Lake para as tarefas de envio de execução local do Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblagens C# localmente

Pode depurar assemblagens c# sem as submeter e registar no serviço do Azure Data Lake Analytics. Pode definir pontos de interrupção no ficheiro code-behind e num projeto C# referenciado.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Para depurar código local no ficheiro code-behind

1. Configurar pontos de interrupção no ficheiro code-behind.
2. Prima F5 para depurar o script localmente.

> [!NOTE]
   > O procedimento seguinte só funciona no Visual Studio 2015. No Visual Studio anterior poderá ter de adicionar manualmente os ficheiros pdb.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Para depurar código local num projeto C# referenciado

1. Crie um projeto de Assemblagem C# e crie-o para gerar a dll de saída.
2. Registe a dll com uma instrução U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Defina pontos de interrupção no código C#.
4. Prima F5 para depurar o script com referência à dll c# localmente.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Execução do SDK do Data Lake U-SQL de local de utilização

Além de executar scripts U-SQL localmente ao utilizar o Visual Studio, pode utilizar o SDK do Azure Data Lake U-SQL para executar scripts U-SQL localmente com interfaces de programação e da linha de comandos. Através destes, pode dimensionar o teste local do U-SQL.

Saiba mais sobre [SDK do Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Passos seguintes

* Para ver uma consulta mais complexa, consulte [analisar registos de Web site com o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver os detalhes da tarefa, consulte [Browser de tarefa de utilização e a vista de tarefas para tarefas do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para utilizar a vista de execução de vértice, consulte [utilize a vista de execução de vértice ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
