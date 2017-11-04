---
title: "A aplicação de teste de carga utilizando o Visual Studio Team Services | Microsoft Docs"
description: "Saiba como realçar teste as aplicações de Service Fabric do Azure utilizando o Visual Studio Team Services."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: e8e270ce865d4da3ee219958b308db2c1c89b11b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>A aplicação de teste de carga utilizando o Visual Studio Team Services
Este artigo mostra como utilizar funcionalidades de teste de carga do Microsoft Visual Studio para realçar o teste de uma aplicação. Utiliza um Azure Service Fabric com monitorização de estado do serviço de back-end e um sem monitorização de estado do serviço web front-end. A aplicação de exemplo utilizada aqui está um simulador de localização de avião. Forneça um ID de avião, tempo de partida e de destino. Back-end da aplicação processa os pedidos e o front-end apresenta num mapa avião que corresponde aos critérios.

O diagrama seguinte ilustra a aplicação de Service Fabric que irá testar.

![Diagrama da aplicação exemplo avião localização][0]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a utilizar, precisa de fazer o seguinte:

* Obter uma conta do Visual Studio Team Services. Pode obter uma gratuitamente em [Visual Studio Team Services](https://www.visualstudio.com).
* Obter e instalar o Visual Studio 2013 ou Visual Studio 2015. Este artigo utiliza a edição do Visual Studio 2015 Enterprise, mas o Visual Studio 2013 e outras edições deverão funcionar da mesma forma.
* Implemente a sua aplicação para um ambiente de teste. Consulte [como implementar aplicações para um cluster remoto, utilizando o Visual Studio](service-fabric-publish-app-remote-cluster.md) para obter informações sobre esta.
* Compreenda o padrão de utilização da sua aplicação. Estas informações são utilizadas para simular o padrão de carga.
* Compreenda o objetivo de para o teste de carga. Isto ajuda a interpretar e analise os resultados de teste de carga.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Criar e executar o projeto de desempenho da Web e de teste de carga
### <a name="create-a-web-performance-and-load-test-project"></a>Criar um projeto de desempenho da Web e de teste de carga
1. Abra o Visual Studio 2015. Escolha **ficheiro** > **novo** > **projeto** na barra de menus para abrir o **novo projeto** caixa de diálogo.
2. Expanda o **Visual c#** nós e escolher **teste** > **projeto Web desempenho e de teste de carga**. Dê um nome de projeto e, em seguida, escolha o **OK** botão.

    ![Captura de ecrã da caixa de diálogo novo projeto][1]

    Deverá ver um novo projeto de desempenho da Web e de teste de carga no Explorador de soluções.

    ![Captura de ecrã do Explorador de soluções que mostra o novo projeto][2]

### <a name="record-a-web-performance-test"></a>Gravar um teste de desempenho da web
1. Abra o projeto. webtest.
2. Escolha o **adicionar gravar** ícone para iniciar uma sessão de gravação no seu browser.

    ![Captura de ecrã do ícone Adicionar gravação num browser][3]

    ![Captura de ecrã do botão do registo num browser][4]
3. Navegue para a aplicação de Service Fabric. O painel de gravação deve mostrar os pedidos web.

    ![Captura de ecrã de pedidos da web no painel de gravação][5]
4. Execute uma sequência de ações que espera que os utilizadores a executar. Estas ações são utilizadas como um padrão para gerar a carga.
5. Quando tiver terminado, escolha o **parar** botão para parar a gravação.

    ![Captura de ecrã do botão para parar][6]

    O projeto. webtest no Visual Studio deve ter capturado uma série de pedidos. Os parâmetros dinâmicos são substituídos automaticamente. Neste momento, pode eliminar todos os pedidos adicionais, repetido de dependência que não fazem parte do seu cenário de teste.
6. Guarde o projeto e, em seguida, escolha o **executar teste** comando para executar o teste de desempenho da web localmente e certifique-se de que tudo funciona corretamente.

    ![Captura de ecrã do comando Executar teste][7]

### <a name="parameterize-the-web-performance-test"></a>Parametrizar o teste de desempenho da web
Pode parametrizar o teste de desempenho da web, convertendo-o para um teste de desempenho da web codificadas e, em seguida, editar o código. Como alternativa, é possível vincular o teste de desempenho da web para uma lista de dados para que o teste itera através de dados. Consulte [gerar e execute um teste de desempenho da web codificadas](https://msdn.microsoft.com/library/ms182552.aspx) para obter detalhes sobre como converter o desempenho da web de teste para um teste codificado. Consulte [adicionar uma origem de dados para um teste de desempenho da web](https://msdn.microsoft.com/library/ms243142.aspx) para obter informações sobre como ao vincular dados para um teste de desempenho da web.

Neste exemplo, irá converter o teste de desempenho da web um teste-coded para que possa substitua o ID de avião com um GUID gerado e adicionar mais pedidos a enviar flights localizações diferentes.

### <a name="create-a-load-test-project"></a>Criar um projeto de teste de carga
Um projeto de teste de carga é composto por um ou mais cenários descritos pelo teste de desempenho da web e o teste da unidade, juntamente com as definições de teste de carga especificado adicionais. Os passos seguintes mostram como criar um projeto de teste de carga:

1. No menu de atalho do seu projeto de desempenho da Web e de teste de carga, escolha **adicionar** > **teste de carga**. No **teste de carga** assistente, escolha o **seguinte** botão para configurar as definições de teste.
2. No **carregar padrão** secção, escolha se pretende uma carga de constante de utilizador ou a uma carga de passo, que começa com alguns utilizadores e aumenta os utilizadores ao longo do tempo.

    Se tiver uma boa estimativa da quantidade de carga de utilizador e pretende ver o desempenho do sistema atual, escolha **carregar constante**. Se o seu objetivo é saber se o sistema efetua consistentemente em várias cargas, escolha **passo carga**.
3. No **testar mistura** secção, escolha o **adicionar** botão e, em seguida, selecione o teste de que pretende incluir no teste de carga. Pode utilizar o **distribuição** coluna para especificar a percentagem do totais testes que executar para cada teste.
4. No **executar definições** secção, especifique a duração de teste de carga.

   > [!NOTE]
   > O **testar iterações** opção está disponível apenas quando executar um teste de carga localmente utilizando o Visual Studio.
   >
   >
5. No **localização** secção **executar definições**, especifique a localização onde os pedidos de teste de carga são gerados. O assistente pode solicitar-lhe para iniciar sessão sua conta de serviços da equipa. Iniciar sessão e, em seguida, escolha uma localização geográfica. Quando tiver terminado, escolha o **concluir** botão.
6. Depois de criado o teste de carga, abra o projeto de .loadtest e escolha atual executar a definição, tais como **executar definições** > **executar Settings1 [Directory]**. Esta ação abre as definições de execução no **propriedades** janela.
7. No **resultados** secção o **configurações de execução** janela de propriedades, o **armazenamento de detalhes de temporização** deve ter a definição **nenhum** como respetivo valor predefinido. Altere este valor para **todos os detalhes individuais** para obter mais informações sobre a carga de resultados do teste. Consulte [carregar teste](https://www.visualstudio.com/load-testing.aspx) para obter mais informações sobre como estabelecer ligação com o Visual Studio Team Services e execute um teste de carga.

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Execute o teste de carga, utilizando o Visual Studio Team Services
Escolha o **executar teste de carga** comando para iniciar o execução do teste.

![Captura de ecrã do comando executar o teste de carga][8]

## <a name="view-and-analyze-the-load-test-results"></a>Ver e analisar os resultados do teste de carga
Como a carga de teste avança ser, as informações de desempenho é graphed. Deverá ver algo semelhante ao seguinte gráfico.

![Captura de ecrã do gráfico de desempenho para os resultados do teste de carga][9]

1. Escolha o **transferir relatório** ligação perto da parte superior da página. Após o relatório é transferido, escolha o **Ver relatório** botão.

    No **gráfico** separador, pode ver gráficos para vários contadores de desempenho. No **resumo** separador, são apresentados os resultados do teste geral. O **tabelas** separador mostra o número total de testes de carga com êxito e falha.
2. Escolha as ligações número no **teste** > **falha** e **erros** > **contagem** colunas para ver o erro detalhes.

    O **detalhe** separador apresenta informações virtuais de cenário de utilizador e de teste para pedidos falhados. Estes dados podem ser útil se o teste de carga incluir vários cenários.

Consulte [analisar carregar testar os resultados na vista de gráficos do analisador de teste de carga](https://www.visualstudio.com/load-testing.aspx) para obter mais informações sobre a visualização de carga resultados do teste.

## <a name="automate-your-load-test"></a>Automatizar o teste de carga
Visual Studio Team Services carregar teste fornece APIs para o ajudar a gerir os testes de carga e analisar os resultados numa conta do Team Services. Consulte [nuvem carga testar as APIs Rest](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar e diagnosticar os serviços de uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
