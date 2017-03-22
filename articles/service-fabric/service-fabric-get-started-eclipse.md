---
title: "Introdução ao Plug-in do Eclipse para o Azure Service Fabric | Microsoft Docs"
description: "Introdução ao Plug-ind do Eclipse para o Azure Service Fabric."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Introdução ao Plug-in do Eclipse para a programação de aplicações Java no Service Fabric
O Eclipse é um do IDEs mais utilizados para Programadores de Java. Neste artigo, vamos discutir como pode configurar o seu ambiente de programação do Eclipse para trabalhar com o Service Fabric. Este artigo irá ajudá-lo a instalar o plug-in, criar aplicações do Service Fabric e implementá-las num cluster do Service Fabric no local ou remoto.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Instalar ou atualizar o Plug-in do Service Fabric no Eclipse Neon
O Service Fabric disponibiliza um plug-in para o **IDE Eclipse para Programadores de Java** que pode simplificar o processo de criação e implementação de serviços Java.

1. Confirme que tem as mais recentes versões do Eclipse **Neon** e do Buildship (versão&1;.0.17 ou posterior) instaladas. Para verificar as versões dos componentes instalados, escolha ``Help => Installation Details``. Pode atualizar o Buildship ao seguir as instruções fornecidas [aqui][buildship-update]. Para verificar e atualizar se a versão do Eclipse Neon é a mais recente, pode aceder a ``Help => Check for Updates``.

2. Para instalar o plug-in do Service Fabric, selecione ``Help => Install New Software...``.
  1. Na caixa de texto "Work with” (“Trabalhar com"), introduza ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Clique em Adicionar.

  ![Plug-in do Eclipse Neon para o Service Fabric][sf-eclipse-plugin-install]

  3. Escolha o plug-in do Service Fabric e clique em “Seguinte”.
  4. Continue a instalação e aceite o contrato de licença de utilizador final.

Se já tiver o plug-in do Eclipse para o Service Fabric instalado, confirme que tem a versão mais recente. Para verificar se ainda pode ser atualizado, siga ``Help => Installation Details``. Em seguida, procure o Service Fabric na lista de plug-ins instalados e clique em Atualizar. Se houver atualizações pendentes, estas serão obtidas e instaladas.

> [!NOTE]
> Caso a instalação ou atualização do plug-in do Eclipse para o Service Fabric estiver a demorar muito tempo no seu Eclipse, tal deve-se ao facto de o Eclipse tentar sempre obter os metadados de todas as alterações novas ocorridas em todos os sites de atualização registados na sua instância do Eclipse. Assim, para acelerar a instalação ou atualização, pode utilizar um pequeno truque. Aceda a `Available Software Sites` e desmarque todos, exceto o que está a apontar para a localização do plug-in do Service Fabric, `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Utilizar o Eclipse para criar uma aplicação do Service Fabric

1. Aceda a ``File => New => Other``. Selecione ``Service Fabric Project``. Clique em ``Next``.

    ![Página 1 de Novo Projeto do Service Fabric][create-application/p1]

2. Dê um nome ao seu projeto. Clique em ``Next``.

    ![Página 2 de Novo Projeto do Service Fabric][create-application/p2]

3. Selecione o Modelo de Serviço a partir do conjunto de modelos disponível (Ator, Sem Monitorização de Estado, Contentor ou Convidado - executável). Clique em ``Next``.

    ![Página 3 de Novo Projeto do Service Fabric][create-application/p3]

4. Introduza o nome do Serviço e/ou os detalhes relevantes do mesmo nesta página e clique em ``Finish``.

    ![Página 4 de Novo Projeto do Service Fabric][create-application/p4]

5. Quando cria o seu primeiro do Service Fabric, é-lhe perguntado se quer definir a perspetiva do Service Fabric. Selecione ``yes`` para continuar.

    ![Página 5 de Novo Projeto do Service Fabric][create-application/p5]

6. Depois de criado com êxito, o projeto terá o aspeto seguinte -

    ![Página 6 de Novo Projeto do Service Fabric][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Criar e implementar a aplicação do Service Fabric com o Eclipse

* Clique com o botão direito do rato na aplicação do Service Fabric que acabou de criar acima. Selecione a opção ``Service Fabric`` no menu de contexto. É apresentado um submenu com várias opções, que será parecido com -

    ![Menu de Clique com o Botão Direito do Rato do Service Fabric][publish/RightClick]

  Depois de clicar nas opções para criar, recriar e limpar, as ações pretendidas são efetuadas.
  - ``Build Application`` cria a aplicação sem limpar
  - ``Rebuild Application`` faz uma limpeza e criação da aplicação
  - ``Clean Application`` limpa a aplicação dos artefactos criados


* Também pode escolher implementar, anular a implementação e publicar a aplicação a partir deste menu.
  - ``Deploy Application`` implementa no seu cluster local
  - ``Publish Application...`` pergunta-lhe que perfil de publicação quer selecionar, entre ``Local.json`` e ``Cloud.json``. Estes ficheiros JSON são utilizados para armazenar informações (como pontos finais de ligações e informações de segurança) necessárias para ligar ao cluster no local ou na cloud (Azure).

  ![Menu de Clique com o Botão Direito do Rato do Service Fabric][publish/Publish]

* Existe uma forma alternativa de implementar a aplicação do Service Fabric com Configurações de Execução do Eclipse.

  1. Escolha ``Run => Run Configurations``. Selecione a configuração de execução ``ServiceFabricDeployer``, em ``Grade Project``.
  2. No separador ``Arguments``, no painel do lado direito, especifique **local** ou **cloud** como o ``publishProfile``. A configuração predefinida é **local**. Para implementar num cluster remoto/na cloud, selecione **cloud**.
  3. Confirme que são preenchidas nos perfis de publicação as informações adequadas, ao editar o `Local.json` ou `Cloud.json`, conforme adequado, com detalhes dos pontos finais e credenciais de segurança, se existirem.
  4. Certifique-se de que ``Working Directory``, no painel direito, em ``Grade Project``, aponta para a aplicação que pretende implementar. Se não for esse o caso, basta clicar no botão ``Workspace...`` e selecionar a aplicação que quer.
  5. Clique em **Aplicar** e em **Executar**.

A sua aplicação é criada e implementada ao fim de alguns momentos. Pode monitorizar o respetivo estado no Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Adicionar um serviço novo do Service Fabric à sua aplicação do Service Fabric

Pode utilizar os passos seguintes para adicionar um serviço novo do Service Fabric a uma aplicação do Service Fabric já existente:

1. Clique com o botão direito no projeto ao qual pretende adicionar um serviço para abrir o menu de contexto e selecione a opção “Service Fabric”. É apresentado um submenu com várias opções.

    ![Página 1 de Adicionar Serviço do Service Fabric][add-service/p1]

2. Selecione a opção `Add ServiceFabric Service`, que o vai orientar ao longo do conjunto de passos seguintes para adicionar um serviço ao projeto.
3. Selecione o modelo de Serviço que pretende adicionar ao seu projeto e clique em “Seguinte”.

    ![Página 2 de Adicionar Serviço do Service Fabric][add-service/p2]

4. Introduza o Nome do Serviço (e outros detalhes necessários, como e quando aplicável) e clique no botão “Adicionar Service”, em baixo.  

    ![Página 3 de Adicionar Serviço do Service Fabric][add-service/p3]

5. Depois de o serviço ser adicionado com êxito, toda a estrutura do projeto será semelhante a -

    ![Página 4 de Adicionar Serviço do Service Fabric][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Atualizar a sua aplicação Java do Service Fabric

Suponhamos que criou o projeto ``App1`` com o plug-in do Eclipse para o Service Fabric e que o implementou com o plug-in para criar uma aplicação com o nome ``fabric:/App1Application`` com o tipo de aplicação ``App1AppicationType`` e a versão de aplicação 1.0. Agora, quer atualizar a aplicação sem que esta fique indisponível.

Faça a alteração à aplicação e recrie o serviço modificado.  Atualize o ficheiro de manifesto do serviço modificado (``ServiceManifest.xml``) com as versões atualizadas do serviços (e com Código, Configuração ou Dados, conforme adequado). Modifique também o manifesto da aplicação (``ApplicationManifest.xml``) com o número da versão atualizada da aplicação e o serviço modificado.  

Para atualizar a aplicação com o Eclipse, pode criar uma configuração de execução duplicada e utilizá-la para atualizar a aplicação como e quando precisar, ao seguir os passos abaixo -
1. Escolha ``Run => Run Configurations``. Clique na seta pequena à esquerdo de ``Grade Project``, no painel da esquerda.
2. Clique com o botão direito do rato em ``ServiceFabricDeployer`` e selecione ``Duplicate``. Dê um nome novo a esta configuração, digamos ``ServiceFabricUpgrader``.
3. No painel direito, no separador ``Arguments``, altere ``-Pconfig='deploy'`` para ``-Pconfig=upgrade`` e clique em ``Apply``.
4. Acabou de criar e guardar uma configuração de execução para atualizar a sua aplicação, que pode ``Run`` quando quiser. Desta forma, fica resolvida a obtenção da versão de tipo de aplicação atualizada mais recente a partir do ficheiro de manifesto da aplicação.

Pode, agora, utilizar o Service Fabric Explorer para monitorizar a atualização da aplicação. A aplicação estaria atualizada ao fim de alguns minutos.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

