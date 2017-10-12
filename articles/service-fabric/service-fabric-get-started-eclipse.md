---
title: Plug-in do Azure Service Fabric para Eclipse | Microsoft Docs
description: "Introdução ao plug-in do Service Fabric para Eclipse."
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
ms.date: 08/21/2016
ms.author: saysa
ms.openlocfilehash: 4fa77da8665908553072792d7f2ede47bf5567dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in do Service Fabric para desenvolvimento de aplicações Java de Eclipse
O Eclipse é um dos ambientes de desenvolvimento integrados (IDEs) mais utilizados para programadores Java. Neste artigo, descrevemos como configurar o seu ambiente de desenvolvimento do Eclipse para funcionar com o Azure Service Fabric. Saiba como instalar o plug-in do Service Fabric e criar uma aplicação do Service Fabric e implementá-la num cluster do Service Fabric local ou remoto no Eclipse Neon.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Instalar ou atualizar o plug-in do Service Fabric no Eclipse Neon
Pode instalar um plug-in do Service Fabric no Eclipse. O plug-in pode ajudar a simplificar o processo de criação e implementação de serviços Java.

1.  Confirme que tem instaladas as versões mais recentes do Eclipse Neon e do Buildship (1.0.17 ou posterior):
    -   Para verificar as versões dos componentes instalados, no Eclipse Neon, escolha **Ajuda** > **Detalhes da Instalação**.
    -   Para atualizar o Buildship, veja [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Plug-ins do Eclipse para Gradle).
    -   Para procurar e instalar atualizações do Eclipse Neon, aceda a **Ajuda** > **Procurar Atualizações**.

2.  Para instalar o plug-in do Service Fabric, no Eclipse Neon, aceda a **Ajuda** > **Instalar Software Novo**.
  1.    Na caixa **Trabalhar com**, introduza **http://dl.microsoft.com/eclipse**.
  2.    Clique em **Adicionar**.

         ![Plug-in do Service Fabric para Eclipse Neon][sf-eclipse-plugin-install]
  3.    Selecione o plug-in do Service Fabric e clique em **Seguinte**.
  4.    Conclua os passos de instalação e aceite os Termos de Licenciamento para Software Microsoft.

Se já tiver o plug-in do Service Fabric instalado, confirme que tem a versão mais recente. Para verificar se existem atualizações disponíveis, aceda a **Ajuda** > **Detalhes da Instalação**. Na lista de plug-ins instalados, selecione Service Fabric e clique em **Atualizar**. As atualizações disponíveis são instaladas.

> [!NOTE]
> Se a instalação ou atualização do plug-in do Service Fabric estiver lenta, tal poderá dever-se a uma definição do Eclipse. O Eclipse recolhe metadados sobre todas as alterações para atualizar os sites que estão registados na sua instância do Eclipse. Para acelerar o processo de procurar e instalar uma atualização do plug-in do Service Fabric, aceda a **Sites de Software Disponíveis**. Desmarque as caixas de verificação de todos os sites, exceto daquele que aponta para a localização do plug-in do Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Se o Eclipse não estiver a funcionar conforme esperado no Mac (ou se precisar que utilize o perfil de superutilizador), aceda à pasta **ECLIPSE_INSTALLATION_PATH** e navegue para a subpasta **Eclipse.app/Contents/MacOS**. Abra o Eclipse mediante a execução de `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Criar uma aplicação do Service Fabric no Eclipse

1.  No Eclipse Neon, aceda a **File (Ficheiro)** > **New (Novo)** > **Other (Outro)**. Selecione **Service Fabric Project** (Projeto do Service Fabric) e clique em **Next** (Seguinte).

    ![Página 1 de Novo Projeto do Service Fabric][create-application/p1]

2.  Introduza um nome para o seu projeto e, em seguida, clique em **Next** (Seguinte).

    ![Página 2 de Novo Projeto do Service Fabric][create-application/p2]

3.  Na lista de modelos, selecione **Service Template** (Modelo de Serviço). Selecione o tipo de modelo de serviço (Actor (Ator), Stateless (Sem Estado), Container (Contentor) ou Binário Convidado (Guest Binary)) e clique em **Next** (Seguinte).

    ![Página 3 de Novo Projeto do Service Fabric][create-application/p3]

4.  Introduza o nome e os detalhes do serviço e clique em **Finish** (Concluir).

    ![Página 4 de Novo Projeto do Service Fabric][create-application/p4]

5. Quando cria o seu primeiro projeto do Service Fabric, na caixa de diálogo **Open Associated Perspective** (Abrir Perspetiva Associada), clique em **Yes** (Sim).

    ![Página 5 de Novo Projeto do Service Fabric][create-application/p5]

6.  O seu projeto novo tem o seguinte aspeto:

    ![Página 6 de Novo Projeto do Service Fabric][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Compilar e implementar uma aplicação do Service Fabric no Eclipse

1.  Clique com o botão direito na aplicação do Service Fabric nova e selecione **Service Fabric**.

    ![Menu de clique com o botão direito do rato do Service Fabric][publish/RightClick]

2. No submenu, selecione a opção que pretende:
    -   Para criar a aplicação sem limpar, clique em **Build Application** (Criar Aplicação).
    -   Para criar a aplicação de forma limpa, clique em **Rebuild Application** (Recriar Aplicação).
    -   Para limpar a aplicação de artefactos criados, clique em **Clean Application** (Limpar Aplicação).

3.  Neste menu, também pode implementar, anular a implementação e publicar a sua aplicação:
    -   Para implementar no seu cluster local, clique em **Deploy Application** (Implementar Aplicação).
    -   Na caixa de diálogo **Publish Application** (Publicar Aplicação), selecione um perfil de publicação:
        -  **Local.json**
        -  **Cloud.json**

     Estes ficheiros JavaScript Object Notation (JSON) armazenam informações (como pontos finais de ligações e informações de segurança) necessárias para ligar ao cluster no local ou na cloud (Azure).

  ![Menu Publicar do Service Fabric][publish/Publish]

Outra forma de implementar a aplicação do Service Fabric é utilizar configurações de execução do Eclipse.

  1.    Aceda a **Run (Executar)** > **Run Configurations (Configurações de Execução)**.
  2.    Em **Gradle Project** (Projeto Gradle), selecione a configuração de execução **ServiceFabricDeployer**.
  3.    No painel do lado direito, no separador **Arguments** (Argumentos), em **publishProfile**, selecione **local** ou **cloud**.  A predefinição é **local**. Para implementar num cluster remoto ou na cloud, selecione **cloud**.
  4.    Para garantir que são preenchidas as informações adequadas nos perfis de publicação, edite **Local.json** ou **Cloud.json**, conforme necessário. Pode adicionar ou atualizar os detalhes do ponto final e as credenciais de segurança.
  5.    Confirme que **Working Directory** (Diretório de Trabalho) está a apontar para a aplicação que quer implementar. Para alterar a aplicação, clique no botão **Workspace** (Área de Trabalho) e selecione a aplicação que pretende.
  6.    Clique em **Apply** (Aplicar) e clique em **Run** (Executar).

A sua aplicação é criada e implementada ao fim de alguns momentos. Pode monitorizar o estado da implementação no Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Adicionar um serviço do Service Fabric à sua aplicação do Service Fabric

Para adicionar um serviço do Service Fabric a uma aplicação do Service Fabric já existente, faça o seguinte:

1.  Clique com o botão direito do rato no projeto ao qual pretende adicionar um serviço e clique em **Service Fabric**.

    ![Página 1 de Adicionar Serviço do Service Fabric][add-service/p1]

2.  Clique em **Add Service Fabric Service** (Adicionar Serviço do Service Fabric) e conclua o conjunto de passos para adicionar um serviço ao projeto.
3.  Selecione o modelo de serviço que pretende adicionar ao seu projeto e clique em **Next** (Seguinte).

    ![Página 2 de Adicionar Serviço do Service Fabric][add-service/p2]

4.  Introduza o nome do serviço (e outros detalhes, conforme necessário) e clique no botão **Add Service** (Adicionar Serviço).  

    ![Página 3 de Adicionar Serviço do Service Fabric][add-service/p3]

5.  Quando o serviço for adicionado, a estrutura geral do seu projeto será semelhante ao projeto seguinte:

    ![Página 4 de Adicionar Serviço do Service Fabric][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Editar versões do Manifesto da sua aplicação Java do Service Fabric

Para editar versões do manifesto, clique com o botão direito do rato no projeto, entre no **Service Fabric** e selecione **Editar Versões do Manifesto...**  do menu suspenso. No assistente, pode atualizar as versões do manifesto para o manifesto de aplicação, o manifesto do serviço e as versões dos pacotes **Código**, **Config** e **Dados**.

Se marcar a opção **Atualizar automaticamente as versões da aplicação e do serviço** e, em seguida, atualizar uma versão, as versões do manifesto serão atualizadas automaticamente. Por exemplo, primeiro selecione a caixa de verificação e atualize a versão do **Código** de 0.0.0 para 0.0.1 e clique em **Concluir**, em seguida, a versão do manifesto do serviço e a versão do manifesto de aplicação serão atualizadas automaticamente para 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Atualizar a sua aplicação Java do Service Fabric

Num cenário de atualização, imaginemos que utilizou o plug-in do Service Fabric no Eclipse para criar o projeto **App1**. Para o implementar, utilizou o plug-in para criar uma aplicação com o nome **fabric:/App1Application**. O tipo de aplicação é **App1AppicationType** e a versão 1.0. Agora, quer atualizar a aplicação sem interromper a disponibilidade.

Em primeiro lugar, faça eventuais alterações à aplicação e, em seguida, recrie o serviço modificado. Atualize o ficheiro de manifesto do serviço modificado (ServiceManifest.xml) com as versões atualizadas do serviço (e com Código, Configuração ou Dados, conforme relevante). Modifique também o manifesto da aplicação (ApplicationManifest.xml) com o número da versão atualizada da aplicação e o serviço modificado.  

Para atualizar a aplicação com o Eclipse Neon, pode criar um perfil de configuração de execução duplicado. Em seguida, utilize-o para atualizar a sua aplicação, conforme necessário.

1.  Aceda a **Run (Executar)** > **Run Configurations (Configurações de Execução)**. No painel do lado esquerdo, clique na seta pequena à esquerdo de **Gradle Project** (Projeto Gradle).
2.  Clique com o botão direito do rato em **ServiceFabricDeployer** e selecione **Duplicate** (Duplicado). Introduza um novo nome para esta configuração, como, por exemplo, **ServiceFabricUpgrader**.
3.  No painel direito, no separador **Arguments** (Argumentos), altere **-Pconfig='deploy'** para **-Pconfig='upgrade'** e clique em **Apply** (Aplicar).

Este processo cria e guarda um perfil de configuração de execução que pode utilizar em qualquer altura para atualizar a sua aplicação. Também obtém a versão de tipo de aplicação atualizada mais recente a partir do ficheiro de manifesto da aplicação.

A atualização da aplicação demora alguns minutos. Pode utilizar o Service Fabric Explorer para monitorizar a atualização da aplicação.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrar aplicações Java antigas do Service Fabric para utilizar com o Maven
Recentemente, movemos as bibliotecas Java do Service Fabric do SDK Java do Service Fabric para o repositório Maven. Enquanto as aplicações novas que gerar através do Eclipse irão gerar projetos com as atualizações mais recentes (que conseguirão trabalhar com o Maven), pode atualizar as aplicações Java sem estado ou de Actor do Service Fabric, que estavam a utilizar anteriormente o SDK Java do Service Fabric, para utilizar as dependências Java do Service Fabric a partir do Maven. Siga os passos mencionados [aqui](service-fabric-migrate-old-javaapp-to-use-maven.md) para garantir que a aplicação mais antiga funciona com o Maven.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

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
