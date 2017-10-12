---
title: 'Tutorial: DevOps com o Portal do Azure | Microsoft Docs'
description: "Conheça os vários fluxos de trabalho do DevOps no Portal do Azure."
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2016
ms.author: mlearned
ms.openlocfilehash: b590fb06a3dba8aec66a380217269e1ca39bb5e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-devops-with-the-azure-portal"></a>Tutorial: DevOps com o Portal do Azure
A plataforma do Azure está cheia de fluxos de trabalho flexíveis do DevOps. Neste tutorial, irá aprender a tirar partido das funcionalidades do Portal do Azure para desenvolver, testar, implementar, resolver problemas, monitorizar e gerir aplicações em execução. Este tutorial concentra-se no seguinte:

1. Criar uma aplicação Web e ativar a implementação contínua
2. Desenvolver e testar uma aplicação
3. Monitorizar e a Resolver Problemas de uma aplicação
4. Tarefas de gestão de aplicações gerais

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Criar uma aplicação Web e ativar a implementação contínua
Criar uma aplicação Web com o [Serviço de Aplicações do Azure](https://azure.microsoft.com/services/app-service/), que irá utilizar o resto deste tutorial. Inicialmente, irá ativar a implementação contínua do seu repositório de código de origem no nosso ambiente do Azure em execução.

1. Inicie sessão no Portal do Azure
2. Selecione **Serviços Aplicacionais** &gt; **ícone Adicionar** e introduza um nome, escolha a sua subscrição e crie um novo grupo de recursos para servir como contentor para o serviço.
   
   Os grupos de recursos permitem-lhe gerir vários aspetos da solução como a faturação, implementações e a monitorização de tudo como um único grupo através do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   
   ![image1][image1]
3. Após alguns instantes, o seu serviço de aplicações é criado. Dispense alguns minutos para explorar as várias opções de menu para o serviço no portal.
   
   ![image2][image2]    
4. Clique no URL. Repare na variedade de opções disponíveis para ferramentas e repositórios. Também pode utilizar os idiomas e arquiteturas à sua escolha, incluindo .NET, Java e Ruby.
   
   ![image3][image3]    
5. O Portal do Azure torna a implementação contínua um processo simples que envolve apenas alguns passos simples. No portal do Azure, escolha as definições do ícone para o serviço de aplicações que acabou de criar.
   
   ![image4][image4]
   
   A partir do painel que abre à direita, desloque-se para a secção de publicação.
   
   ![image5][image5]
6. Em seguida, configure algumas definições para ativar a implementação contínua da aplicação. Clique na Origem de Implementação e, em seguida, clique em Escolher Origem. Repare na variedade de opções disponíveis para origens de repositório.
   
   ![image6][image6]
7. Para este exemplo escolha o GitHub. Opcionalmente, escolha o repositório à sua escolha e configure as credenciais de autorização.
   
   ![image7][image7]
8. Depois da autorização para o seu repositório, pode escolher um projeto e ramo que pretende implementar. Existem vários exemplos fictícios listados abaixo.
   
   ![image8][image8]
9. Assim que escolher o seu projeto e ramo, clique em ok. Deve começar a ver as notificações de uma implementação.
   
   ![image9][image9]
10. Regresse ao GitHub para ver o webhook que foi criado para integrar o repo do controlo de origem com o Azure. O Portal do Azure permite a integração com o GitHub com apenas alguns passos simples.
    
    ![image10][image10]
11. Para demonstrar a implementação contínua, adicione rapidamente algum conteúdo ao repositório. Para obter um exemplo simples, adicione um ficheiro de texto de exemplo a um repo do GitHub. Pode utilizar o .NET, o Ruby, o Python ou outro tipo de aplicação com o Serviço de Aplicações. Pode adicionar um ficheiro de texto, ASP.NET MVC, Java ou aplicação Ruby ao repo à sua escolha.
    
    ![image11][image11]
12. Após efetuar alterações ao seu repositório, verá uma nova implementação iniciar na área de notificações do portal. Clique em Sincronizar se não vir rapidamente as alterações efetuadas no seu repositório.
    
    ![image12][image12]
13. Neste momento, se tentar carregar a página para o serviço de aplicações, poderá receber um erro 403. Neste exemplo, é porque não existe nenhuma configuração de documento predefinida típica para a página, como um ficheiro index.htm ou default.html. Pode resolver isto rapidamente com a ferramentas no Portal do Azure.  No Portal do Azure escolhas as Definições &gt; Definições da Aplicação.
    
     ![image13][image13]
14. É aberto um painel para as definições da aplicação. Introduza o nome da página "SamplePage.html" e clique em Guardar. Dispense uns minutos para explorar as outras definições.
    
    ![image14][image14]
15. Opcionalmente, atualize o seu URL do browser para se certificar de que vê as alterações esperadas. Neste caso, existe algum texto simples agora a preencher a página. Cada alteração adicional ao repositório resultaria numa nova implementação automática.
    
    ![image15][image15]
    
    Ativar a implementação contínua com o Portal do Azure é uma experiência simples. Também pode criar pipelines de versão mais complexos e utilizar muitas outras técnicas com controlo de origem existente e sistemas de integração contínua para implementar para o Azure, tal como tirar partido da criação automatizada e sistemas de gestão de versões.

## <a name="develop-and-test-an-app"></a>Desenvolver e testar uma aplicação
Em seguida, faça algumas alterações ao código de base e implemente rapidamente essas alterações. Irá também configurar alguns testes de desempenho da aplicação Web.

1. No Portal do Azure escolha Serviços Aplicacionais no painel de navegação e localize o seu Serviço de Aplicações.
   
   ![image16][image16]
2. Clique em Ferramentas
   
   ![image17][image17]
3. Repare na categoria de desenvolvimento em Ferramentas. Existem várias ferramentas úteis aqui que nos permitem trabalhar com aplicações, sem sair do Portal do Azure. Clique em Consola.
   
   ![image18][image18]
4. Na janela da consola, pode emitir comandos em tempo real para a sua aplicação. Escreva o comando dir e clique em enter. Tenha em atenção que os comandos que precisam de privilégios elevados não funcionam.
   
   ![image19][image19]
5. Regresse à categoria Desenvolver e escolha o Visual Studio Online. Nota: o Visual Studio Online tem agora o nome Visual Studio Team Services.
   
   ![image20][image20]
6. Ative a experiência de edição no browser para a sua Aplicação.
   
   ![image21][image21]
7. É instalada uma extensão da Web para a sua aplicação. As extensões adicionam funcionalidade de forma rápida e fácil às aplicações do Azure. Observe alguns dos outros tipos de extensão disponíveis na captura de ecrã abaixo.
   
   ![image22][image22]
8. Assim que a extensão do Visual Studio Online é instalada, clique em Ir.
   
   ![image23][image23]
9. É aberto um separador do browser onde vê um IDE de desenvolvimento diretamente no browser. Observe que a experiência abaixo está no Chrome.
   
   ![image24][image24]
10. Pode efetuar várias atividades, como editar ficheiros, adicionar ficheiros e pastas e descarregar conteúdo do site em tempo real. Faça uma edição rápida no ficheiro SamplePage.html.
    
    ![image25][image25]
11. Dentro de momentos, as alterações são guardadas automaticamente. Se regressar à página, pode ver as alterações. Tenha em conta que as edições em tempo real como estas provavelmente não se adequam a ambientes de produção. No entanto, as ferramentas facilitam muito mais a realização de pequenas alterações em ambientes de desenvolvimento e teste.
    
    ![image26][image26]
    
    ![image27][image27]
12. Regresse ao painel de ferramentas e na categoria Desenvolver, clique em Teste de Desempenho.
    
    ![image28][image28]
13. Tem de configurar uma conta de serviços da equipa. Leia este artigo para obter mais detalhes: [Criar uma Conta de Serviços da Equipa](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. Clique em Novo para criar um teste de desempenho.
    
    ![image29][image29]
    
    Configure os vários valores e clique em Executar Teste na parte inferior do diálogo para iniciar um teste de desempenho.
    
    ![image30][image30]
    
    ![image31][image31]
15. Assim que o teste iniciar, pode monitorizar o estado.
    
    ![image32][image32]
    
    Assim que o teste estiver concluído, clique no resultado para obter mais detalhes.
    
    ![image33][image33]
16. Neste exemplo, criou um pequeno teste, pelo que existem dados limitados para analisar, mas pode ver várias métricas, bem como voltar a executar o teste a partir desta vista. O Portal do Azure torna a criação, a execução e a análise de testes de desempenho da Web num processo mais fácil. As capturas de ecrã abaixo apresentam os dados de desempenho.
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Monitorizar e a resolver problemas de uma aplicação
O Azure oferece muitas funcionalidades de monitorização e resolução de problemas de aplicações em execução.

1. No Portal do Azure para a nossa aplicação Web, escolha Ferramentas.
   
   ![image37][image37]
2. Na categoria Resolução de Problemas, observe as várias opções para utilizar ferramentas para resolver potenciais problemas com uma aplicação em execução. Pode fazer coisas como monitorizar o tráfego HTTP em Tempo Real, ativar a autorrecuperação, ver registos e muito mais.
   
   ![image38][image38]
3. Escolha as Métricas de Site para obter rapidamente uma vista de alguns códigos HTTP.
   
   ![image39][image39]
4. Escolha Diagnósticos como um Serviço. Selecione o tipo de aplicação e, em seguida, escolha Executar.
   
   ![image40][image40]
   
   Começa uma coleção.
   
   ![image41][image41]
5. Pode escolher o registo adequado para diagnosticar potenciais problemas. Tem de ativar o registo para ver todas as opções de dados disponíveis, como os Registos de HTTP.
   
   ![image42][image42]
   
   Ao clicar no ficheiro Informações da Memória, pode descarregar e analisar um relatório de análise de DebugDiag para o ajudar a encontrar potenciais problemas.
   
   ![image43][image43]
6. Para ver mais dados, terá de ativar o registo adicional. No Portal do Azure, navegue para a aplicação Web e escolha Definições.
   
   ![image44][image44]
7. Desloque para baixo para a categoria de funcionalidades e escolha os registos de Diagnóstico.
   
      ![image45][image45]
8. Observe as várias opções de registo. Ative o registo de servidor Web e clique em guardar.
   
   ![image46][image46]
9. Regresse à área de ferramentas para a aplicação e escolha Diagnósticos como um serviço e clique em Executar para voltar a executar a recolha de dados.
   
   ![image47][image47]
10. Com a definição de registo de HTTP ativada, agora pode ver os dados recolhidos para Registos de HTTP.
    
    ![image48][image48]
11. Ao clicar no registo de ficheiros HTML, produz um relatório baseado em browser avançado para uma investigação mais aprofundada.
    
    ![image49][image49]
12. Regresse à secção de ferramentas no Portal do Azure para a aplicação. Desloque-se para a secção Ferramentas e escolha o Explorador de Processos.
    
    ![image50][image50]
13. Ao escolher o Explorador de Processos, pode ver os detalhes sobre os processos em execução. Observe abaixo que pode explorar os processos e até mesmo eliminá-los, tudo a partir do Portal do Azure.
    
    ![image51][image51]
    
    ![image52][image52]
14. Regresse ao painel Definições à esquerda. Clique em Novo pedido de suporte.
    
    ![image53][image53]
15. A partir do painel à direita, pode preencher os detalhes sobre os problemas, introduzir informações de contacto e até mesmo carregar dados de diagnóstico. O Portal do Azure permite trabalhar com o suporte da Microsoft, uma experiência totalmente integrada.
    
    ![image54][image54]
    
    ![image55][image55]
    
    O Portal do Azure ajuda-o a oferecer experiências de ferramentas fantásticas e familiares, para o ajudar a monitorizar e resolver problemas das nossas aplicações em execução. Também pode agir rapidamente ao realizar tarefas, como reciclar processos, ativar e desativar várias coleções de dados e até mesmo integrar com o suporte profissional da Microsoft.

## <a name="general-application-management"></a>Gestão de Aplicações Gerais
Ao gerir aplicações, muitas vezes, terá de realizar uma grande variedade de atividades, como a configuração de estratégias de cópia de segurança, a implementação e gestão de fornecedores de identidades e a configuração do controlo de acesso baseado em Funções. Tal como acontece com outras experiências de DevOps, a plataforma do Azure integra estas tarefas diretamente no portal.

1. Para se certificar de que impede que a Aplicação Web perca dados, tem de configurar cópias de segurança. Navegue para a área de Definições para a sua Aplicação Web.
   
   ![image56][image56]
2. No painel à direita, desloque para baixo para a categoria Funcionalidades.
   
    ![image57][image57]
3. Selecione Cópias de segurança; é aberto um painel à direita.
   
   ![image58][image58]
4. Clique em Configurar, escolha uma conta de armazenamento a partir do painel à direita.
   
   ![image59][image59]
5. Agora crie e selecione um contentor de armazenamento para guardar as cópias de segurança. Clique em criar na parte inferior do painel. Em seguida, selecione o contentor.
   
   ![image60][image60]
6. Depois de escolher o contentor, pode configurar as agendas, bem como as cópias de segurança do programa de configuração para as bases de dados. Para este cenário, clique no ícone de guardar.
   
    ![image61][image61]
7. Depois de guardar, desloque-se para o painel da esquerda para Cópias de Segurança. Clique em Efetuar Cópia de Segurança Agora para criar a aplicação de segurança da aplicação.
   
    ![image62][image62]
8. Dentro de momentos, verá uma cópia de segurança criada. Observe a opção Restaurar Agora na captura de ecrã abaixo.
   
    ![image63][image63]
9. Clique em Restaurar Agora e examine as opções do painel à direita. Pode escolher uma cópia de segurança adequada e restaurar facilmente para um estado anterior, conforme necessário. O portal do Azure ajudou-nos a ativar facilmente uma estratégia simples de recuperação após desastres para a aplicação.
   
    ![image64][image64]
10. Regresse ao painel Definições à esquerda e, em Funcionalidades selecione Autenticação/Autorização.
    
     ![image65][image65]
11. No painel à direita, selecione Autenticação do Serviço de Aplicações. Observe as várias opções que pode configurar com fornecedores populares.
    
     ![image66][image66]
12. Escolha o fornecedor da sua preferência e observe as opções para o âmbito. Pode fornecer um ID e o Segredo da Aplicação e ativar rapidamente a autenticação do Facebook para a aplicação. O Portal do Azure permite a autenticação como uma solução de chave na mão para aplicações.
    
     ![image67][image67]
13. Regresse ao painel Definições e selecione Utilizadores na categoria Gestão de Recursos.
    
     ![image68][image68]
14. No painel à direita examine as várias opções para adicionar funções e utilizadores. O Portal do Azure permite-lhe controlar facilmente o RBAC (Controlo de acesso baseado em funções) para a aplicação.
    
     ![image69][image69]

## <a name="summary"></a>Resumo
Este tutorial demonstrou algumas das vantagens da plataforma do Azure, ao ativar rapidamente a implementação contínua para uma aplicação Web, efetuar várias atividades de desenvolvimento e teste, monitorizar e resolver problemas de uma aplicação em tempo real e, por último, gerir estratégias fundamentais, como a recuperação após desastre, de identidade e de controlo de acesso baseado em funções. A plataforma do Azure permite uma experiência integrada para estes fluxos de trabalho do DevOps e pode trabalhar de forma eficaz, permanecendo no contexto da tarefa em execução.

## <a name="next-steps"></a>Passos seguintes
* O Azure Resource Manager é importante para ativar DevOps na plataforma do Azure.  Para saber mais, visite [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para saber mais sobre a implementação do Serviço de Aplicações do Azure, visite [Implemente a sua aplicação no Serviço de Aplicações do Azure](../app-service/app-service-deploy-local-git.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png
