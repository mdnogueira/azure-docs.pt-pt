---
title: "Publicação de um serviço em nuvem com as ferramentas do Azure | Microsoft Docs"
description: "Saiba mais sobre como publicar projetos do serviço em nuvem do Azure utilizando o Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2017
ms.author: kraigb
ms.openlocfilehash: 933d274406951416c0e1f83dcc0d72b7f2bed527
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Publicação de um serviço em nuvem com o Visual Studio

Visual Studio, pode publicar uma aplicação diretamente no Azure, com suporte para ambientes de teste e produção de um serviço em nuvem. Quando a publicação, selecione o ambiente de implementação e a conta de armazenamento que é utilizada temporariamente para o pacote de implementação.

Quando estiver a desenvolver e testar uma aplicação do Azure, pode utilizar o Web Deploy para publicar as alterações de forma incremental para as funções da web. Depois de publicar a aplicação num ambiente de implementação, implementar Web permite-lhe implementar alterações diretamente à máquina virtual que está a executar a função da web. Não é necessário para o pacote e publicar a aplicação do Azure completa sempre que pretende atualizar a função da web para testar as alterações. Com esta abordagem, pode fazer com as alterações de função web disponíveis na nuvem para fins de teste sem aguardar para que a aplicação publicada a um ambiente de implementação.

Para publicar a aplicação do Azure e a atualização uma função da web através do Web Deploy, utilize os seguintes procedimentos:

* Publicar ou pacote de aplicação do Azure a partir do Visual Studio
* Uma função da web como parte do desenvolvimento e teste de ciclo de atualização

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicar ou pacote de aplicação do Azure a partir do Visual Studio

Quando publicar a aplicação do Azure, pode efetuar uma das seguintes tarefas:

* Criar um pacote de serviço: pode utilizar este pacote e o ficheiro de configuração de serviço para publicar a aplicação a um ambiente de implementação a partir de [portal do Azure](https://portal.azure.com).
* Publicar o projeto do Visual Studio do Azure: para publicar a aplicação diretamente no Azure, utilize o assistente publicar. Para informações, consulte [publicar Assistente da aplicação Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Para criar um pacote de serviço a partir do Visual Studio

1. Quando estiver pronto para publicar a aplicação, abra o Explorador de soluções, abra o menu de atalho para o projeto do Azure que contém as funções e clicar em publicar.
1. Para criar um pacote de serviço só, siga estes passos:

   a. No menu de atalho para o projeto do Azure, escolha **pacote**.
   b. No **empacotar a aplicação Azure** caixa de diálogo, escolha a configuração do serviço para o qual pretende criar um pacote e, em seguida, escolha a configuração de compilação.
   c. (opcional) Para ativar o ambiente de trabalho remoto para o serviço em nuvem depois de publicá-lo, selecione o **ativar o ambiente de trabalho remoto para todas as funções** caixa de verificação e, em seguida, selecione **definições** para configurar o ambiente de trabalho remoto. Se pretender que o serviço de nuvem de depuração, depois, publicá-lo, ativar a depuração remota selecionando **ativar remoto depurador para todas as funções**.

      Para obter mais informações, consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md).
   d. Para criar o pacote, escolha o **pacote** ligação.

      Explorador de ficheiros mostra a localização do ficheiro do pacote criado recentemente. Pode copiar nesta localização, para que pode utilizá-lo a partir do portal do Azure.
   e. Para publicar este pacote para um ambiente de implementação, tem de utilizar esta localização como a localização do pacote ao criar um serviço em nuvem e implementar este pacote para um ambiente com o portal do Azure.
1. (Opcional) Para cancelar o processo de implementação, no menu de atalho para o item de linha no registo de atividade, escolha **Cancelar e remover**. Este comando interrompe o processo de implementação e elimina o ambiente de implementação do Azure. Para remover o ambiente após a implementação, utilize o portal do Azure.

1. (Opcional) Depois das instâncias da função tem sido iniciado, o Visual Studio mostra automaticamente o ambiente de implementação no **serviços em nuvem** nó no Explorador de servidores. Aqui, pode ver o estado das instâncias de função individuais. Consulte [recursos de gestão do Azure com o Explorador de nuvem](vs-azure-tools-resources-managing-with-cloud-explorer.md). A ilustração seguinte mostra as instâncias da função enquanto estiverem ainda no estado de inicialização:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Uma função da web como parte do desenvolvimento e teste de ciclo de atualização

Se a infraestrutura de back-end da aplicação está estável, mas as funções da web precisam de mais de induzirem frequentes atualizar, pode utilizar o Web Deploy para atualizar apenas uma função da web no seu projeto. O Web Deploy é útil quando não quiser reconstruir e voltar a implementar as funções de trabalho de back-end, ou se tiver várias funções da web e de que pretende atualizar apenas uma das funções web.

### <a name="requirements"></a>Requisitos

Eis os requisitos para utilizar o Web Deploy para atualizar a função da web:

* **Apenas para desenvolvimento e teste fins:** as alterações são efetuadas diretamente à máquina virtual que está a executar a função da web. Se esta máquina virtual tem de ser reciclados, as alterações serão perdidas porque o pacote original que publicou é utilizado para recriar a máquina virtual para a função. Voltar a publicar a aplicação para obter as alterações mais recentes para a função da web.
* **Funções da web só podem ser atualizadas:** as funções de trabalho não podem ser atualizadas. Além disso, não é possível atualizar o RoleEntryPoint na web role.cs.
* **Só pode suportar uma única instância de uma função web:** não pode ter várias instâncias de qualquer função da web no seu ambiente de implementação. No entanto, são suportadas várias funções da web cada com apenas uma instância.
* **Ativar as ligações de ambiente de trabalho remotas:** este requisito permite utilizar o utilizador e palavra-passe para ligar à máquina virtual para implementar as alterações no servidor que está a executar o serviços de informação Internet (IIS) do Web Deploy. Além disso, poderá ter de estabelecer ligação à máquina virtual para adicionar um certificado fidedigno para IIS nesta máquina virtual. (Este certificado garante que a ligação remota para o IIS, que é utilizado pelo Web Deploy é segura).

O procedimento seguinte parte do princípio de que está a utilizar o **publicar aplicação Azure** assistente.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Ativar o Web Deploy quando publicar a aplicação

1. Para ativar o **ativar Web Deploy** para todos os web caixa de verificação de funções, primeiro tem de configurar ligações de ambiente de trabalho remotas. Selecione **ativar o ambiente de trabalho remoto** para todas as funções e, em seguida, forneça as credenciais utilizadas para ligar remotamente no **configuração do ambiente de trabalho remoto** caixa que aparece. Consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md).
1. Para ativar o Web Deploy para todas as funções da web na sua aplicação, selecione **ativar Web Deploy para todas as funções da web**.

    Um triângulo amarelo de aviso é apresentado. O Web Deploy utiliza um certificado autoassinado, não fidedigno por predefinição, o que não é recomendada para carregar os dados confidenciais. Se precisar de proteger este processo para dados confidenciais, pode adicionar um certificado SSL a utilizar para ligações Web Deploy. Este certificado tem de ser um certificado fidedigno. Para obter mais informações, consulte [tornar web implementar segura](#make-web-deploy-secure).
1. Escolha **seguinte** para mostrar o **resumo** ecrã e, em seguida, escolha **publicar** para implementar o serviço em nuvem.

    O serviço em nuvem é publicado. A máquina virtual que é criada tem ligações remotas ativadas para o IIS para que o Web Deploy pode ser utilizado para atualizar as funções da web sem republishing-los.

   > [!NOTE]
   > Se tiver mais do que uma instância configurada para uma função da web, é apresentada uma mensagem de aviso a indicar que cada função da web está limitada a uma instância apenas no pacote criado para publicar a aplicação. Selecione **OK** para continuar. Como indicado na secção de requisitos, pode ter mais do que uma função da web, mas apenas uma instância de cada função.

### <a name="update-your-web-role-by-using-web-deploy"></a>Atualizar a função da web utilizando o Web Deploy

1. Para utilizar o Web Deploy, efetuar alterações de código para o projeto para qualquer uma das suas funções da web no Visual Studio que pretende publicar e, em seguida, clique com o botão direito este nó do projeto na sua solução e aponte para **publicar**. O **publicar Web** é apresentada a caixa de diálogo.
1. (Opcional) Se tiver adicionado um certificado fidedigno do SSL a utilizar para ligações remotas para o IIS, pode desmarcar a **certificados não fidedignos permitir** caixa de verificação. Para obter informações sobre como adicionar um certificado para tornar o Web Deploy seguro, consulte a secção **para tornar Web implementar segura** posteriormente neste artigo.
1. Para utilizar o Web Deploy, o mecanismo de publicação tem de nome de utilizador e palavra-passe que configurou para a sua ligação de ambiente de trabalho remota quando publicada pela primeira vez o pacote.

   a. No **nome de utilizador**, introduza o nome de utilizador.
   b. No **palavra-passe**, introduza a palavra-passe.
   c. (Opcional) Se pretender guardar esta palavra-passe neste perfil, escolha **palavra-passe de guardar**.
1. Para publicar as alterações para a função da web, escolha **publicar**.

    A linha de estado apresenta **publicar iniciado**. Quando concluir a publicação, **publicar com êxito** aparece. Agora que foram implementadas as alterações para a função da web na sua máquina virtual. Agora pode começar a sua aplicação do Azure no ambiente do Azure para testar as suas alterações.

### <a name="make-web-deploy-secure"></a>Tornar a web implementar segura

1. O Web Deploy utiliza um certificado autoassinado, não fidedigno por predefinição, o que não é recomendada para carregar os dados confidenciais. Se precisar de proteger este processo para dados confidenciais, pode adicionar um certificado SSL a utilizar para ligações Web Deploy. Este certificado tem de ser um certificado fidedigno, o qual obter de uma autoridade de certificação (AC).

    Para tornar o Web Deploy segura para cada máquina virtual para cada uma das suas funções da web, tem de carregar o certificado fidedigno que pretende utilizar para a web implementar no portal do Azure. Este certificado certifica-se de que o certificado foi adicionado à máquina virtual que é criada para a função da web quando publicar a aplicação.
1. Para adicionar um certificado fidedigno do SSL para o IIS utilize para ligações remotas, siga estes passos:

  a. Para ligar à máquina virtual que está a executar a função da web, selecione a instância de função da web no **Cloud Explorer** ou **Explorador de servidores**e, em seguida, escolha o **estabeleçam ligação através do ambiente de trabalho remoto**  comando. Para obter passos detalhados sobre como ligar à máquina virtual, consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md). O browser pede-lhe para transferir um `.rdp` ficheiro.
   b. Para adicionar um certificado SSL, abra o serviço de gestão no Gestor do IIS. No Gestor de IIS, ativar o SSL, abrindo o **enlaces** ligação no **ação** painel. O **Adicionar enlace de Site** é apresentada a caixa de diálogo. Escolha **adicionar**e, em seguida, escolha HTTPS no **tipo** na lista pendente. No **certificado SSL** lista, escolha o certificado SSL que tinha assinado por uma AC e que carregou para o portal do Azure. Para obter mais informações, consulte [configurar definições de ligação para o serviço de gestão](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Se adicionar um certificado SSL fidedigno, triângulo de aviso amarelo já não aparece no **publicar assistente**.

## <a name="include-files-in-the-service-package"></a>Incluir ficheiros no pacote de serviço

Poderá ter de incluir ficheiros específicos no seu pacote de serviço para que fiquem disponíveis na máquina virtual que é criada para uma função. Por exemplo, pode querer adicionar uma `.exe` ou um `.msi` ficheiro que é utilizado por um script de arranque ao seu pacote de serviço. Ou poderá ter de adicionar uma assemblagem que necessita de um projeto de função web ou função de trabalho. Para incluir os ficheiros, tem de ser adicionados à solução para a sua aplicação do Azure.

1. Para adicionar uma assemblagem de serviço, utilize os seguintes passos:

   a. No **Explorador de soluções**, abra o nó do projeto do projeto que está em falta a assemblagem referenciada.
   b. Para adicionar a assemblagem para o projeto, abra o menu de atalho para o **referências** pasta e, em seguida, escolha **Adicionar referência**. É apresentada a caixa de diálogo Adicionar referência.
   c. Escolha a referência de que pretende adicionar e, em seguida, escolha **OK**. A referência é adicionada à lista sob o **referências** pasta.
   d. Abra o menu de atalho para a assemblagem que adicionou e escolha **propriedades**. O **propriedades** surge a janela.

      Para incluir esta assemblagem num pacote de serviço, o **lista Local de cópia** escolha **verdadeiro**.
1. No **Explorador de soluções** abra o nó do projeto do projeto que está em falta a assemblagem referenciada.
1. Para adicionar a assemblagem para o projeto, abra o menu de atalho para o **referências** pasta e, em seguida, escolha **Adicionar referência**. O **Adicionar referência** é apresentada a caixa de diálogo.
1. Escolha a referência de que pretende adicionar e, em seguida, escolha o **OK** botão.

    A referência é adicionada à lista sob o **referências** pasta.
1. Abra o menu de atalho para a assemblagem que adicionou e escolha **propriedades**. É apresentada a janela Propriedades.
1. Para incluir esta assemblagem num pacote de serviço, o **Cópia Local** lista, escolha **verdadeiro**.
1. Para incluir os ficheiros no pacote de serviço que foram adicionados ao seu projeto de função da web, abra o menu de atalho para o ficheiro e, em seguida, escolha **propriedades**. Do **propriedades** janela, escolha **conteúdo** do **ação criar** caixa de listagem.
1. Para incluir os ficheiros no pacote de serviço que foram adicionados ao seu projeto de função de trabalho, abra o menu de atalho para o ficheiro e, em seguida, escolha **propriedades**. Do **propriedades** janela, escolha **copiar se for mais recente** do **copiar para o diretório de saída** caixa de listagem.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a publicação para o Azure a partir do Visual Studio, consulte [publicar Assistente da aplicação Azure](vs-azure-tools-publish-azure-application-wizard.md).
