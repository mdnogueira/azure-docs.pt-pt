---
title: Depurar tarefas U-SQL | Microsoft Docs
description: "Saiba como depurar uma vértice falhada do U-SQL com o Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depurar definido pelo utilizador código c# para as tarefas que falharam U-SQL

U-SQL fornece um modelo de extensibilidade utilizar c#, pelo que pode escrever o código para adicionar a funcionalidade como um extrator personalizado ou reducer. Para obter mais informações, consulte [guia de programação para U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). Na prática, qualquer código poderá ter de depuração e sistemas de macrodados só podem fornecer o tempo de execução limitado informações tais como ficheiros de registo de depuração.

Ferramentas do Azure Data Lake para Visual Studio fornece uma funcionalidade denominada **falha vértice depurar**, que lhe permite clonar uma tarefa falhada da nuvem no seu computador local para a depuração. O clone local captura o ambiente de nuvem completo, incluindo quaisquer dados de entrada e o código de utilizador.

O vídeo seguinte demonstra falha vértice de depuração no Azure Data Lake Tools para Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio requer as seguintes dois atualizações, se não estiverem já instalados: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e [Universal C Runtime para o Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Falha ao transferir o vértice no computador local

Quando abre uma tarefa no Azure Data Lake Tools para Visual Studio, verá uma barra de alerta amarela com mensagens de erro detalhadas no separador erro.

1. Clique em **transferir** para transferir todos os recursos necessários e fluxos de entrada. Se não concluir a transferência, clique em **repita**.

2. Clique em **abra** depois de concluída a transferência para gerar um ambiente de depuração local. Uma nova instância do Visual Studio com uma solução de depuração é automaticamente criada e aberta.

![Azure vértice de transferência do visual studio de depuração do U-SQL do Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

As tarefas podem incluir os ficheiros de origem do code-behind ou assemblagens registadas e estes dois tipos de tem diferentes cenários de depuração.

- [Depurar uma tarefa falhada com code-behind](#debug-job-failed-with-code-behind)
- [Depurar uma falha na tarefa com assemblagens](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Depurar a tarefa falhou com o code-behind

Se uma tarefa de U-SQL falhar e a tarefa inclui código de utilizador (normalmente designado `Script.usql.cs` num projeto U-SQL), que o código de origem é importado para a solução de depuração.  A partir daí pode utilizar o Visual Studio (veja, variáveis, etc.) para resolver o problema de ferramentas de depuração.

> [!NOTE]
> Antes de depuração, não se esqueça de verificar **exceções de tempo de execução de idioma comum** na janela definições de exceção (**Ctrl + Alt + I**).

![Definição do visual studio de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Prima **F5** para executar o código por detrás do código. Ela apenas será executada, mas está parado por uma exceção.

2. Abra o `ADLTool_Codebehind.usql.cs` ficheiros e de configurar pontos de interrupção, em seguida, prima **F5** para depurar o código de passo a passo.

    ![Exceção de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Tarefa falhou com assemblagens de depuração

Se utilizar assemblagens registadas no script U-SQL, o sistema não é possível obter o código fonte automaticamente. Neste caso, adicione manualmente os ficheiros de código de origem dos assemblagens à solução.

### <a name="configure-the-solution"></a>Configurar a solução

1. Clique com botão direito **solução 'VertexDebug' > Adicionar > projeto existente...**  para localizar o código de origem dos assemblagens e adicione o projeto para a solução de depuração.

    ![Projeto de adicionar de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Clique com botão direito **LocalVertexHost > propriedades** na solução e copie o **diretório de trabalho** caminho.

3. Clique com botão direito **projeto de código de origem de assemblagem > propriedades**, selecione o **criar** separador no lado esquerdo e cole o caminho copiado como **saída > caminho de saída**.

    ![Caminho do pdb de conjunto de depuração do Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Prima **Ctrl + Alt + I**, verifique **exceções de tempo de execução de idioma comum** na janela definições de exceção.

### <a name="start-debug"></a>Iniciar depuração

1. Clique com botão direito **projeto de código de origem de assemblagem > reconstruir** saída .pdb ficheiros para o `LocalVertexHost` diretório de trabalho.

2. Prima **F5** e o projeto será executado até, mas está parado por uma exceção. Poderá ver a seguinte mensagem de aviso, o que pode ignorar. -Pode demorar até um minuto para aceder ao ecrã de depuração.

    ![Aviso do visual studio de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Abra o seu código de origem e configurar pontos de interrupção, em seguida, prima **F5** para depurar o código de passo a passo.

Também pode utilizar o Visual Studio (veja, variáveis, etc.) para resolver o problema de ferramentas de depuração.

> [!NOTE]
> Reconstrua o projeto de código de origem de assemblagem sempre depois de modificar o código para gerar ficheiros .pdb atualizado.

Depois de depuração, se o projeto for concluída com êxito a janela de resultados mostra a mensagem seguinte:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Êxito de depuração de U-SQL do Data Lake Analytics do Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Volte a submeter a tarefa

Depois de concluir a depuração, volte a submeter a tarefa falhada.

1. Para as tarefas com soluções de code-behind, copie o código c# para o ficheiro de origem do code-behind (normalmente `Script.usql.cs`).
2. Para as tarefas com assemblagens, registe as assemblagens. dll atualizado para a base de dados ADLA:
    1. Explorador de servidores ou Cloud Explorer, expanda o **conta ADLA > bases de dados** nós.
    2. Clique com botão direito **assemblagens** e registar o seu novo assemblagens. dll com a base de dados ADLA: ![depuração de Azure Data Lake Analytics U-SQL registar a assemblagem](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Volte a submeter a tarefa.

## <a name="next-steps"></a>Passos seguintes

- [Guia de programação para U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores definido pelo utilizador U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Tutorial: desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
