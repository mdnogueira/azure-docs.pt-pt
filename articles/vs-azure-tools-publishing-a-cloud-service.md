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
ms.date: 8/14/2017
ms.author: kraigb
ms.openlocfilehash: e617d600dbc8287eea737fc4969833e873365288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicação de um serviço em nuvem com as ferramentas do Azure
A utilização das Ferramentas do Azure para Microsoft Visual Studio permite-lhe publicar a aplicação do Azure diretamente a partir do Visual Studio. O Visual Studio suporta a publicação integrada no ambiente de Teste ou Produção de um serviço cloud.

Antes de poder publicar uma aplicação do Azure, tem de ter uma subscrição do Azure. Tem também de configurar uma conta de serviço e o armazenamento de nuvem a ser utilizado pela sua aplicação. Pode configurar estes no [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!IMPORTANT]
> Quando publica, pode selecionar o ambiente de implementação do serviço em nuvem. Também tem de selecionar uma conta de armazenamento que é utilizada para armazenar o pacote de aplicações para implementação. Após a implementação, o pacote de aplicação é removido da conta do storage.
> 
> 

Quando estiver a desenvolver e testar uma aplicação do Azure, pode utilizar o Web Deploy para publicar as alterações de forma incremental para as funções da web. Depois de publicar a aplicação num ambiente de implementação, implementar Web permite-lhe implementar alterações diretamente à máquina virtual que está a executar a função da web. Não é necessário para o pacote e publicar a aplicação do Azure completa sempre que pretende atualizar a função da web para testar as alterações. Com esta abordagem pode ter as alterações de função web disponíveis na nuvem para fins de teste sem aguardar para que a aplicação publicada a um ambiente de implementação.

Para publicar a aplicação do Azure e a atualização uma função da web através do Web Deploy, utilize os seguintes procedimentos:

* Publicar ou pacote de aplicação do Azure a partir do Visual Studio
* Uma função da web como parte do desenvolvimento e teste de ciclo de atualização

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicar ou pacote de aplicação do Azure a partir do Visual Studio
Quando publicar a aplicação do Azure, pode efetuar uma das seguintes tarefas:

* Criar um pacote de serviço: pode utilizar este pacote e o ficheiro de configuração de serviço para publicar a aplicação a um ambiente de implementação a partir de [Portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
* Publicar o projeto do Visual Studio do Azure: para publicar a aplicação diretamente no Azure, utilize o assistente publicar. Para informações, consulte [publicar Assistente da aplicação Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Para criar um pacote de serviço a partir do Visual Studio
1. Quando estiver pronto para publicar a aplicação, abra o Explorador de soluções, abra o menu de atalho para o projeto do Azure que contém as funções e clicar em publicar.
2. Para criar um pacote de serviço só, siga estes passos:  
   
   1. No menu de atalho para o projeto do Azure, escolha **pacote**.
   2. No **empacotar a aplicação Azure** caixa de diálogo, escolha a configuração do serviço para o qual pretende criar um pacote e, em seguida, escolha a configuração de compilação.
   3. (opcional) Para ativar o ambiente de trabalho remoto para o serviço em nuvem depois de publicá-lo, selecione o **ativar o ambiente de trabalho remoto para todas as funções** caixa de verificação e, em seguida, selecione **definições** para configurar o ambiente de trabalho remoto. Se pretender que o serviço de nuvem de depuração, depois, publicá-lo, ativar a depuração remota selecionando **ativar remoto depurador para todas as funções**.
      
      Para obter mais informações, consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md).
   4. Para criar o pacote, escolha o **pacote** ligação.
      
      Explorador de ficheiros mostra a localização do ficheiro do pacote criado recentemente. Pode copiar nesta localização, para que possa utilizar ao [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   5. Para publicar este pacote para um ambiente de implementação, tem de utilizar esta localização como a localização do pacote ao criar um serviço em nuvem e implementar este pacote para um ambiente com o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
3. (Opcional) Para cancelar o processo de implementação, no menu de atalho para o item de linha no registo de atividade, escolha **Cancelar e remover**. Isto interrompe o processo de implementação e elimina o ambiente de implementação do Azure.
   
   > [!NOTE]
   > Para remover este ambiente de implementação, após esta ter sido implementada, tem de utilizar o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   > 
   > 
4. (Opcional) Depois das instâncias da função tem sido iniciado, o Visual Studio mostra automaticamente o ambiente de implementação no **serviços em nuvem** nó no Explorador de servidores. Aqui, pode ver o estado das instâncias de função individuais. Consulte [recursos de gestão do Azure com o Explorador de nuvem](vs-azure-tools-resources-managing-with-cloud-explorer.md). A ilustração seguinte mostra as instâncias da função enquanto estiverem ainda no estado de inicialização:
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Uma função da Web como parte do desenvolvimento e teste de ciclo de atualização
Se a infraestrutura de back-end da aplicação está estável, mas as funções da web precisam de mais de induzirem frequentes atualizar, pode utilizar o Web Deploy para atualizar apenas uma função da web no seu projeto. Isto é útil quando não quiser reconstruir e voltar a implementar as funções de trabalho de back-end, ou se tiver várias funções da web e de que pretende atualizar apenas uma das funções web.

### <a name="requirements"></a>Requisitos
Eis os requisitos para utilizar o Web Deploy para atualizar a função da web:

* **Apenas para desenvolvimento e teste fins:** as alterações são efetuadas diretamente à máquina virtual que está a executar a função da web. Se esta máquina virtual tem de ser reciclados, as alterações serão perdidas porque o pacote original que publicou é utilizado para recriar a máquina virtual para a função. Tem de voltar a publicar a aplicação para obter as alterações mais recentes para a função da web.
* **Funções da web só podem ser atualizadas:** as funções de trabalho não podem ser atualizadas. Além disso, não é possível atualizar o RoleEntryPoint na web role.cs.
* **Só pode suportar uma única instância de uma função web:** não pode ter várias instâncias de qualquer função da web no seu ambiente de implementação. No entanto, são suportadas várias funções da web cada com apenas uma instância.
* **Tem de ativar ligações de ambiente de trabalho remotas:** isto é necessário para que o Web Deploy pode utilizar o utilizador e palavra-passe para ligar à máquina virtual para implementar as alterações no servidor que está a executar o serviços de informação Internet (IIS). Além disso, poderá ter de estabelecer ligação à máquina virtual para adicionar um certificado fidedigno para IIS nesta máquina virtual. (Isto garante que a ligação remota para o IIS, que é utilizado pelo Web Deploy é segura.)

O procedimento seguinte parte do princípio de que está a utilizar o **publicar aplicação Azure** assistente.

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Para ativar o Web Deploy quando publicar a aplicação
1. Para ativar o **ativar Web Deploy** para todos os web caixa de verificação de funções, primeiro tem de configurar ligações de ambiente de trabalho remotas. Selecione **ativar o ambiente de trabalho remoto** para todas as funções e, em seguida, forneça as credenciais que serão utilizadas para ligar remotamente no **configuração do ambiente de trabalho remoto** caixa que aparece. Consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md) para obter mais informações.
2. Para ativar o Web Deploy para todas as funções da web na sua aplicação, selecione **ativar Web Deploy para todas as funções da web**.
   
    Um triângulo amarelo de aviso é apresentado. O Web Deploy utiliza um certificado autoassinado, não fidedigno por predefinição, o que não é recomendada para carregar os dados confidenciais. Se precisar de proteger este processo para dados confidenciais, pode adicionar um certificado SSL a utilizar para ligações Web Deploy. Este certificado tem de ser um certificado fidedigno. Para obter informações sobre como fazê-lo, consulte a secção **para tornar Web implementar Secure** mais adiante neste tópico.
3. Escolha **seguinte** para mostrar o **resumo** ecrã e, em seguida, escolha **publicar** para implementar o serviço em nuvem.
   
    O serviço em nuvem é publicado. A máquina virtual que é criada tem ligações remotas ativadas para o IIS para que o Web Deploy pode ser utilizado para atualizar as funções da web sem republishing-los.
   
   > [!NOTE]
   > Se tiver mais do que uma instância configurada para uma função da web, é apresentada uma mensagem de aviso a indicar que cada função da web será limitada a uma instância apenas no pacote criado para publicar a aplicação. Selecione **OK** para continuar. Como indicado na secção de requisitos, pode ter mais do que uma função da web, mas apenas uma instância de cada função.
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Para atualizar a função da Web através do Web Deploy
1. Para utilizar o Web Deploy, efetuar alterações de código para o projeto para qualquer uma das suas funções da web no Visual Studio que pretende publicar e, em seguida, clique com o botão direito este nó do projeto na sua solução e aponte para **publicar**. O **publicar Web** é apresentada a caixa de diálogo.
2. (Opcional) Se tiver adicionado um certificado fidedigno do SSL a utilizar para ligações remotas para o IIS, pode desmarcar a **certificados não fidedignos permitir** caixa de verificação. Para obter informações sobre como adicionar um certificado para tornar o Web Deploy seguro, consulte a secção **para tornar Web implementar segura** mais adiante neste tópico.
3. Para utilizar o Web Deploy, o mecanismo de publicação tem de nome de utilizador e palavra-passe que configurou para a sua ligação de ambiente de trabalho remota quando publicada pela primeira vez o pacote.
   
   1. No **nome de utilizador**, introduza o nome de utilizador.
   2. No **palavra-passe**, introduza a palavra-passe.
   3. (Opcional) Se pretender guardar esta palavra-passe neste perfil, escolha **palavra-passe de guardar**.
4. Para publicar as alterações para a função da web, escolha **publicar**.
   
    A linha de estado apresenta **publicar iniciado**. Quando concluir a publicação, **publicar com êxito** aparece. Agora que foram implementadas as alterações para a função da web na sua máquina virtual. Agora pode começar a sua aplicação do Azure no ambiente do Azure para testar as suas alterações.

### <a name="to-make-web-deploy-secure"></a>Tornar a Web implementar segura
1. O Web Deploy utiliza um certificado autoassinado, não fidedigno por predefinição, o que não é recomendada para carregar os dados confidenciais. Se precisar de proteger este processo para dados confidenciais, pode adicionar um certificado SSL a utilizar para ligações Web Deploy. Este certificado tem de ser um certificado fidedigno, o qual obter de uma autoridade de certificação (AC).
   
    Para tornar o Web Deploy segura para cada máquina virtual para cada uma das suas funções da web, tem de carregar o certificado fidedigno que pretende utilizar para a web implementar o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Isto certifica-se de que o certificado foi adicionado à máquina virtual que é criada para a função da web quando publicar a aplicação.
2. Para adicionar um certificado fidedigno do SSL para o IIS utilize para ligações remotas, siga estes passos:
   
   1. Para ligar à máquina virtual que está a executar a função da web, selecione a instância de função da web no **Cloud Explorer** ou **Explorador de servidores**e, em seguida, escolha o **estabeleçam ligação através do ambiente de trabalho remoto**  comando. Para obter passos detalhados sobre como ligar à máquina virtual, consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md).
      
      O browser irá solicitar-lhe transferir um. Ficheiro RDP.
   2. Para adicionar um certificado SSL, abra o serviço de gestão no Gestor do IIS. No Gestor de IIS, ativar o SSL, abrindo o **enlaces** ligação no **ação** painel. O **Adicionar enlace de Site** é apresentada a caixa de diálogo. Escolha **adicionar**e, em seguida, escolha HTTPS no **tipo** na lista pendente. No **certificado SSL** lista, escolha o certificado SSL que tinha assinado por uma AC e que carregou para o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Para obter mais informações, consulte [configurar definições de ligação para o serviço de gestão](http://go.microsoft.com/fwlink/?LinkId=215824).
      
      > [!NOTE]
      > Se adicionar um certificado SSL fidedigno, triângulo de aviso amarelo já não aparece no **publicar assistente**.
      > 
      > 

## <a name="include-files-in-the-service-package"></a>Incluir ficheiros no pacote de serviço
Poderá ter de incluir ficheiros específicos no seu pacote de serviço para que fiquem disponíveis na máquina virtual que é criada para uma função. Por exemplo, pode querer adicionar uma .exe ou um ficheiro. msi, que é utilizado por um script de arranque ao seu pacote de serviço. Ou poderá ter de adicionar uma assemblagem que necessita de um projeto de função web ou função de trabalho. Para incluir os ficheiros têm de ser adicionados à solução para a sua aplicação do Azure.

### <a name="to-include-files-in-the-service-package"></a>Para incluir os ficheiros no pacote de serviço
1. Para adicionar uma assemblagem de serviço, utilize os seguintes passos:
   
   1. No **Explorador de soluções** abra o nó do projeto do projeto que está em falta a assemblagem referenciada.
   2. Para adicionar a assemblagem para o projeto, abra o menu de atalho para o **referências** pasta e, em seguida, escolha **Adicionar referência**. É apresentada a caixa de diálogo Adicionar referência.
   3. Escolha a referência de que pretende adicionar e, em seguida, escolha o **OK** botão.
      
      A referência é adicionada à lista sob o **referências** pasta.
   4. Abra o menu de atalho para a assemblagem que adicionou e escolha **propriedades**. O **propriedades** surge a janela.
      
      Para incluir esta assemblagem num pacote de serviço, o **lista Local de cópia** escolha **verdadeiro**.
2. No **Explorador de soluções** abra o nó do projeto do projeto que está em falta a assemblagem referenciada.
3. Para adicionar a assemblagem para o projeto, abra o menu de atalho para o **referências** pasta e, em seguida, escolha **Adicionar referência**. O **Adicionar referência** é apresentada a caixa de diálogo.
4. Escolha a referência de que pretende adicionar e, em seguida, escolha o **OK** botão.
   
    A referência é adicionada à lista sob o **referências** pasta.
5. Abra o menu de atalho para a assemblagem que adicionou e escolha **propriedades**. É apresentada a janela Propriedades.
6. Para incluir esta assemblagem num pacote de serviço, o **Cópia Local** lista, escolha **verdadeiro**.
7. Para incluir os ficheiros no pacote de serviço que foram adicionados ao seu projeto de função da web, abra o menu de atalho para o ficheiro e, em seguida, escolha **propriedades**. Do **propriedades** janela, escolha **conteúdo** do **ação criar** caixa de listagem.
8. Para incluir os ficheiros no pacote de serviço que foram adicionados ao seu projeto de função de trabalho, abra o menu de atalho para o ficheiro e, em seguida, escolha **propriedades**. Do **propriedades** janela, escolha **copiar se for mais recente** do **copiar para o diretório de saída** caixa de listagem.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a publicação para o Azure a partir do Visual Studio, consulte [publicar Assistente da aplicação Azure](vs-azure-tools-publish-azure-application-wizard.md).

