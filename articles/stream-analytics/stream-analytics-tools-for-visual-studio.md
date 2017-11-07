---
title: Utilize as ferramentas do Azure Stream Analytics para o Visual Studio | Microsoft Docs
description: "Tutorial de introdução para as ferramentas de análise de fluxo do Azure para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
ms.openlocfilehash: 8e3f1ae6739896dfd1329561dbcede38a6069546
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Utilize as ferramentas do Azure Stream Analytics para o Visual Studio
Ferramentas de Stream Analytics do Azure para Visual Studio agora são geralmente disponíveis. Estas ferramentas ativar uma experiência mais rica para os utilizadores de Stream Analytics resolver problemas, bem como escrever consultas complexas e até mesmo escrever consultas localmente. Também pode exportar uma tarefa de Stream Analytics para um projeto do Visual Studio.

## <a name="introduction"></a>Introdução
Neste tutorial, irá aprender a utilizar as ferramentas de Stream Analytics para o Visual Studio para criar, criar, testar localmente, gerir e depurar as tarefas do Stream Analytics. 

Depois de concluir este tutorial, será capaz de:

* Familiarize com as ferramentas do Stream Analytics para o Visual Studio.
* Configurar e implementar uma tarefa de Stream Analytics.
* Teste a sua tarefa localmente com dados de exemplo local.
* Utilize a monitorização para resolver problemas.
* Exporte as tarefas existentes para projetos.

## <a name="prerequisites"></a>Pré-requisitos
Terá dos seguintes pré-requisitos para concluir este tutorial:

* Conclua os passos até "Criar uma tarefa de Stream Analytics" no tutorial [criar uma solução de IoT utilizando o Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Instale Visual Studio 2017, o Visual Studio 2015 ou o Visual Studio 2013 atualização 4. Enterprise (Ultimate/Premium), Professional e Community são suportadas as edições. Não é suportada a edição Express. 
* Siga o [instruções de instalação](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) instalar as ferramentas de Stream Analytics para o Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics
No Visual Studio, selecione **ficheiro** > **novo projeto**. Na lista de modelos, no lado esquerdo, selecione **Stream Analytics**e, em seguida, selecione **aplicação do Azure Stream Analytics**.
Na parte inferior da página, o projeto de entrada **nome**, **localização**, e **nome da solução** para outros projetos.

![Criação do novo projeto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

O projeto **utilização** é gerado no **Explorador de soluções**.

![Projeto de utilização no Explorador de soluções](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Selecione a subscrição correta
1. No **vista** menu, selecione **Explorador de servidores** no Visual Studio.

2. Inicie sessão com a sua conta do Azure. 

## <a name="define-input-sources"></a>Definir origens de entrada
1. No **Explorador de soluções**, expanda o **entradas** nós e mudar o nome **Input** para **EntryStream.json**. Faça duplo clique em **EntryStream.json**.

2. Para **Alias de entrada**, introduza **EntryStream**. Tenha em atenção que o alias de entrada é utilizado no script de consulta.

3. Para **tipo de origem**, selecione **fluxo de dados**.

4. Para **origem**, selecione **Hub de eventos**.

5. Para **espaço de nomes do Service Bus**, selecione o **TollData** opção na lista pendente.

6. Para **nome de Hub de eventos**, selecione **entrada**.

7. Para **nome de política do Hub de eventos**, selecione **RootManageSharedAccessKey** (o valor predefinido).

8. Para **formato de serialização de eventos**, selecione **Json**e para **codificação**, selecione **UTF8**.
   
   As definições de ter o seguinte aspeto:
   
   ![Definições de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Na parte inferior da página, selecione **guardar** para concluir o assistente. Agora pode adicionar outra origem de entrada para criar o fluxo de saída. Clique com botão direito do **entradas** nó e selecione **Novo Item**.
   
   ![Novo Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Na janela de pop-up, selecione **Stream Analytics entrada**e altere o **nome** para **ExitStream.json**. Selecione **Adicionar**.
   
    ![Adicionar Novo Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Faça duplo clique em **ExitStream.json** no projeto e siga os mesmos passos como o fluxo de entrada, preencha os campos. Para **nome do Hub de eventos**, certifique-se de introduzir **sair**, conforme mostrado na captura de ecrã seguinte:
   
    ![Definições de ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Agora que definiu dois fluxos de entrada.
   
   ![Dois fluxos de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Em seguida, adicionar entrada de dados de referência para o ficheiro de blob que contém dados de registo carro.
   
12. Clique com botão direito do **entradas** nós no projeto e, em seguida, siga o mesmo processo para as entradas de fluxo. Para **tipo de origem**, selecione **referência a dados**e para **Alias de entrada**, introduza **registo**.
   
    ![Definições de registo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Selecione o **armazenamento** conta que contém a opção com **TollData**. O nome do contentor é **TollData**e o **padrão do caminho** é **registration.json**. Este nome de ficheiro é sensível a maiúsculas e minúsculas e deve estar em minúscula.

14. Selecione **guardar** para concluir o assistente.

Agora todas as entradas são definidas.

## <a name="define-output"></a>Definir a saída
1. No **Explorador de soluções**, expanda o **entradas** nós e faça duplo clique **Output.json**.

2. Para **Alias de saída**, introduza **saída**. Para **Sink**, selecione **base de dados SQL**.

3. Para o **base de dados** nome, introduza **TollDataDB**.

4. Para **nome de utilizador**, introduza **tolladmin**. Para **palavra-passe**, introduza **123toll!**. Para **tabela**, introduza **TollDataRefJoin**.

5. Selecione **Guardar**.

   ![Definições de saída](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Consulta do Stream Analytics
Este tutorial tenta responder a várias perguntas de negócio que estão relacionados com a utilização de dados. Vamos construir consultas que podem ser utilizadas no Stream Analytics para fornecer respostas relevantes. Antes de iniciar a tarefa de Stream Analytics primeiro, vamos explorar um cenário simples e a sintaxe de consulta.

### <a name="introduction-to-stream-analytics-query-language"></a>Introdução à linguagem de consulta do Stream Analytics
Imaginemos que precisa contabilizar o número de veículos que introduza uma booth de utilização. Porque este fluxo de eventos é contínuo, tem de definir um período de tempo. Vamos modificar a questão ser "veículos quantos introduza uma booth de utilização a cada três minutos?" Esta medida é normalmente denominada como a contagem de em cascata.

Vamos ver a consulta do Stream Analytics que responde a esta pergunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Como pode ver, o Stream Analytics utiliza uma linguagem de consulta que é como SQL Server. Adiciona alguns extensões para especificar os aspetos relacionados com a hora da consulta.

Para obter mais detalhes, leia sobre [tempo gestão](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [modos de janela](https://msdn.microsoft.com/library/azure/dn835019.aspx) construções utilizadas na consulta do MSDN.

Agora que tem de escrever a primeira consulta do Stream Analytics, testá-lo através da utilização de ficheiros de dados de exemplo localizados na pasta TollApp no seguinte caminho:

**..\TollApp\TollApp\Data**

Esta pasta contém os seguintes ficheiros:

* Entry.JSON
* Exit.JSON
* Registration.JSON

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Pergunta: Número de veículos introduzir um booth de utilização
No projeto, faça duplo clique em **Script.asaql** para abrir o script no editor. Cole o script na secção anterior do editor. O editor de consultas suporta IntelliSense, cores da sintaxe e um marcador de erro.

![Editor de consultas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Testar localmente as consultas de análises de fluxo
Primeiro pode compilar a consulta para ver se existe qualquer erro de sintaxe.

1. Para validar esta consulta em relação a dados de exemplo, utilize os dados de exemplo local clicar a entrada e selecionando **Adicionar entrada local**.
   
   ![Adicionar a entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. Na janela de pop-up, selecione os dados de exemplo do caminho local. Selecione **Guardar**.
   
   ![Adicionar a entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Um ficheiro denominado **local_EntryStream.json** é adicionado automaticamente à sua pasta de entradas.
   
   ![Lista de ficheiros de entrada de pasta local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Selecione **executar localmente** no editor de consultas. Ou pode premir F5.
   
   ![Executar localmente](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Pode encontrar o caminho de saída do resultado da consola. Prima qualquer tecla para abrir a pasta de resultado.
   
   ![Execução local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Verificar os resultados na pasta local.
   
   ![Resultado da pasta local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Entrada de exemplo
Também pode apresentar exemplos dados de entrada a partir de origens de entrada para o ficheiro local. O ficheiro de configuração de entrada com o botão direito e selecione **dados de exemplo**. 

![Dados de Exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Tenha em atenção que pode apresentar exemplos apenas os event hubs ou os hubs IoT por agora. Não são suportadas outras origens de entrada. Na caixa de diálogo de pop-up, preencha o caminho local para guardar os dados de exemplo. Selecione **exemplo**.

![Configuração de dados de exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Pode ver o progresso no **saída** janela. 

![Saída de dados de exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Submete uma consulta do Stream Analytics para o Azure
1. No **Editor de consultas**, selecione **submeter para o Azure** no editor de scripts.

   ![Submeter para o Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Selecione **criar uma nova tarefa de análise do Azure Stream**. Para **nome da tarefa**, introduza **TollApp**. Escolha o correto **subscrição** na lista pendente. Selecione **submeter**.

   ![Submeter a tarefa](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Iniciar a tarefa
Agora a tarefa é criada e a vista de tarefa abre-se automaticamente. 
1. Selecione o botão de seta verde para iniciar a tarefa.

   ![Botão de tarefa de início](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Escolha a definição predefinida e selecione **iniciar**.
 
   ![Iniciar a tarefa](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Pode ver o estado da tarefa foi alterado para **executar**, e se existem eventos de entrada/saída.

   ![Resumo da tarefa e métricas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Resultados da verificação no Visual Studio
1. Abra o Explorador de servidores do Visual Studio e clique com botão direito do **TollDataRefJoin** tabela.

2. Selecione **Mostrar dados de tabela** para ver o resultado da tarefa.
   
   ![Mostrar os dados da tabela](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Veja as métricas de tarefa
Algumas estatísticas básicas de tarefas são apresentadas na **métricas de tarefa**. 

![Métricas de tarefa](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Listar a tarefa no Explorador de servidores
No **Explorador de servidores**, selecione **tarefas do Stream Analytics** e, em seguida, selecione **atualizar**. A tarefa é apresentado em **tarefas do Stream Analytics**.

![Lista de tarefas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Abrir a vista de tarefa
Expanda o nó de tarefa e faça duplo clique no **tarefas vista** nó para abrir uma vista de tarefa.

![Vista de tarefas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportar uma tarefa existente para um projeto
Existem duas formas pode exportar uma tarefa existente para um projeto.
* No **Explorador de servidores**, sob o **tarefas do Stream Analytics** nó, com o botão direito no nó de trabalho. Selecione **exportar para o novo projeto do Stream Analytics**.
   
   ![Exportar para o novo projeto do Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   O projeto gerado aparece no **Explorador de soluções**.
   
    ![Tarefa do Explorador de soluções](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* Na vista de tarefas, selecione **gerar projeto**.
   
   ![Gerar o projeto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos
 
* Testar local não funciona se a consulta tem funções georreplicação geográficos.
* Suporte do Editor não está disponível para adicionar ou alterar UDF do JavaScript.
* Testar local não suporta a gravação de saída no formato JSON. 
* Suporte não está disponível para a saída do Power BI e saída ADLS.



## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar através da utilização do Azure Stream Analytics](stream-analytics-get-started.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Gestão de Stream Analytics referência da REST API do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


