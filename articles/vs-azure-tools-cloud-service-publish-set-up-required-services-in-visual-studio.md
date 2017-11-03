---
title: "Preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio | Microsoft Docs"
description: "Saiba os procedimentos para configurar os serviços de conta de nuvem e de armazenamento e configurar a sua aplicação do Azure."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: cc4fb87e559f554634ae062a59bee31f0831da64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio
## <a name="overview"></a>Descrição geral
Antes de poder publicar um projeto de serviço em nuvem, tem de configurar os seguintes serviços:

* A **serviço em nuvem** para executar as funções no ambiente do Azure
* A **conta de armazenamento** que fornece acesso aos serviços tabela, fila e Blob.

Utilize os procedimentos seguintes para configurar estes serviços e configurar a sua aplicação

## <a name="create-a-cloud-service"></a>Criar um serviço cloud
Para publicar um serviço em nuvem do Azure, primeiro tem de criar um serviço em nuvem, que executa as funções no ambiente do Azure. Pode criar um serviço em nuvem a [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), conforme descrito na secção **para criar um serviço em nuvem utilizando o portal clássico do Azure**, mais adiante neste tópico. Também pode criar um serviço em nuvem no Visual Studio, utilizando o Assistente para publicação.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Para criar um serviço em nuvem utilizando o Visual Studio
1. Abra o menu de atalho para o projeto do Azure e escolha **publicar**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Se não tiver iniciado sessão, iniciar sessão com o nome de utilizador e palavra-passe para a conta Microsoft ou uma conta organizacional associado à sua subscrição do Azure.
3. Escolha o **seguinte** botão para avançar para o **definições** página.

    ![Definições comuns do Assistente de publicação](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. No **serviços em nuvem** lista, escolha **criar novo**. O **criar serviços do Azure** é apresentada a caixa de diálogo.
5. Introduza o nome do seu serviço em nuvem. O nome faz parte do URL para o seu serviço e, por conseguinte, tem de ser globalmente exclusivo. O nome não é sensível.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Para criar um serviço em nuvem utilizando o portal clássico do Azure
1. Iniciar sessão para o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.
2. (opcional) Para apresentar uma lista de serviços em nuvem que já tiver criado, escolha a ligação de serviços em nuvem no lado esquerdo da página.
3. Escolha o  **+**  ícone na inferior esquerda canto e, em seguida, escolha **serviço em nuvem** no menu que surgir. Ecrã outra com duas opções, **criação rápida** e **criação personalizada**, é apresentada. Se optar por **criação rápida**, pode criar um serviço em nuvem apenas especificando o URL e a região em que esta será fisicamente alojada. Se optar por **criação personalizada**, pode publicar imediatamente um serviço em nuvem, especificando um pacote (ficheiro. cspkg), um ficheiro de configuração (. cscfg) e um certificado. Criação personalizada não é necessária se pretender publicar o seu serviço em nuvem utilizando o **publicar** comando num projeto do Azure. O **publicar** comando está disponível no menu de atalho para um projeto do Azure.
4. Escolha **criação rápida** mais tarde publicar o seu serviço em nuvem utilizando o Visual Studio.
5. Especifique um nome do serviço em nuvem. O URL completo é apresentado junto ao nome.
6. Na lista, escolha a região onde está localizadas a maior parte dos utilizadores.
7. Na parte inferior da janela, escolha o **criar serviço de nuvem** ligação.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta do storage fornece acesso aos serviços tabela, fila e Blob. Pode criar uma conta de armazenamento utilizando o Visual Studio ou o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Para criar uma conta de armazenamento utilizando o Visual Studio
1. No **Explorador de soluções**, abra o menu de atalho para o **armazenamento** nó e, em seguida, escolha **criar conta de armazenamento**.

    ![Criar uma nova conta de armazenamento do Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Selecione ou introduza as seguintes informações para a nova conta de armazenamento na **criar conta de armazenamento** caixa de diálogo.

   * A subscrição do Azure para o qual pretende adicionar a conta de armazenamento.
   * O nome que pretende utilizar para a nova conta de armazenamento.
   * A região ou grupo de afinidade, (por exemplo, EUA Oeste ou Ásia Oriental).
   * O tipo de replicação que pretende utilizar para a conta de armazenamento, tal como com redundância geográfica.
3. Quando tiver terminado, escolha **criar**. A nova conta de armazenamento é apresentado no **armazenamento** lista **Explorador de servidores**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Para criar uma conta de armazenamento utilizando o portal clássico do Azure
1. Iniciar sessão para o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.
2. (Opcional) Para ver as contas do storage, escolha o **armazenamento** ligação no painel no lado esquerdo da página.
3. No canto inferior esquerdo da página, escolha o  **+**  ícone.
4. No menu que surgir, selecione **armazenamento**e, em seguida, escolha **criação rápida**.
5. Dê um nome que irá resultar num url exclusivo da conta de armazenamento.
6. Dê um nome de serviço de nuvem. O URL completo é apresentado junto ao nome.
7. Na lista de regiões, escolha uma região onde está localizadas a maior parte dos utilizadores.
8. Especifique se pretende ativar a georreplicação. Se ativar a georreplicação, os dados serão guardados em várias localizações físicas para reduzir a possibilidade de perda. Esta funcionalidade torna o armazenamento mais dispendioso, mas pode reduzir o custo, Ativando a geolocalização ao criar a conta de armazenamento em vez de adicionar a funcionalidade mais tarde. Para obter mais informações, consulte [georreplicação](http://go.microsoft.com/fwlink/?LinkId=253108).
9. Na parte inferior da janela, escolha o **criar conta de armazenamento** ligação.

Depois de criar a sua conta do storage, verá os URLs que pode utilizar para aceder a recursos em cada um dos serviços de armazenamento do Azure bem como as chaves de acesso primária e secundária para a sua conta. Utilize estas chaves para autenticar pedidos efetuados contra os serviços de armazenamento.

> [!NOTE]
> A chave de acesso secundária fornece o mesmo acesso à sua conta de armazenamento como a chave de acesso primária e é gerada uma cópia de segurança a chave de acesso primária comprometida. Além disso, é recomendado que voltar a gerar as chaves de acesso regularmente. Pode modificar uma definição de cadeia de ligação para utilizar a chave secundária ao regenerar a chave primária, em seguida, pode modificar-a para utilizar a chave primária regenerada ao regenerar a chave secundária.
>
>

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configure a sua aplicação para utilizar os serviços fornecidos pela conta de armazenamento
Tem de configurar qualquer função que acede aos serviços de armazenamento para utilizar os serviços de armazenamento do Azure que criou. Para tal, pode utilizar várias configurações de serviço para o projeto do Azure. Por predefinição, dois são criadas no seu projeto do Azure. Ao utilizar várias configurações de serviço, pode utilizar a mesma cadeia de ligação no código, mas tem um valor diferente para uma cadeia de ligação na configuração de cada serviço. Por exemplo, pode utilizar uma configuração de serviço para executar e depurar a aplicação localmente, utilizando o emulador do storage do Azure e uma configuração de serviço diferentes para publicar a aplicação no Azure. Para obter mais informações sobre configurações de serviço, consulte [configurar o Azure projeto através de várias configurações de serviço](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Para configurar a sua aplicação utilizar os serviços que fornece a conta de armazenamento
1. No Visual Studio, abra a solução do Azure. No Explorador de soluções, abra o menu de atalho para cada função no projeto do Azure que acede aos serviços de armazenamento e escolha **propriedades**. É apresentada uma página com o nome da função no editor do Visual Studio. A página apresenta os campos para o **configuração** separador.
2. Nas páginas de propriedade para a função, escolha **definições**.
3. No **a configuração do serviço** lista, escolha o nome da configuração do serviço que pretende editar. Se pretender efetuar alterações a todas as configurações de serviço para esta função, pode escolher **todas as configurações**.  Para obter mais informações sobre como atualizar configurações de serviço, consulte a secção **gerir cadeias de ligação de contas do Storage** no tópico [configuram as funções para um serviço em nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
4. Para modificar as definições de cadeia de ligação, escolha o **...** junto a definição. O **criar cadeia de ligação de armazenamento** é apresentada a caixa de diálogo.
5. Em **estabelecer ligação utilizando**, escolha o **sua subscrição** opção.
6. No **subscrição** lista, escolha a sua subscrição. Se a lista de subscrições não incluem que pretende, escolha o **transferir definições de publicação** ligação.
7. No **nome da conta** lista, escolha o nome da sua conta de armazenamento. Ferramentas do Azure obtém automaticamente as credenciais da conta de armazenamento utilizando o ficheiro. publishsettings. Para especificar manualmente as credenciais da conta de armazenamento, escolha o **introduzir manualmente as credenciais** opção e, em seguida, continuar com este procedimento. Pode obter o nome da conta de armazenamento e a chave primária do [portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se não pretender especificar a sua conta do storage definições manualmente, escolha o **OK** botão para fechar a caixa de diálogo.
8. Escolha o **introduza a conta de armazenamento** ligação de credenciais.
9. No **nome da conta** caixa, introduza o nome da conta de armazenamento.

   > [!NOTE]
   > Inicie sessão no [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, em seguida, escolha o **armazenamento** botão. O portal mostra uma lista de contas de armazenamento. Se escolher uma conta, é aberta uma página para o mesmo. Pode copiar o nome da conta do storage a partir desta página. Se estiver a utilizar uma versão anterior do portal clássico, o nome da sua conta de armazenamento é apresentado no **contas do Storage** vista. Para copiar este nome, destacá-la no **propriedades** janela deste ver e, em seguida, escolha as chaves de Ctrl-C. Colar o nome para o Visual Studio, escolha o **nome da conta** texto caixa e, em seguida, escolha as chaves de Ctrl + V.
   >
   >
10. No **chave da conta** caixa, introduza a chave primária, ou copie e cole-à partir de [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
     Para copiar esta chave:

    1. Na parte inferior da página para a conta de armazenamento adequados, escolha o **gerir chaves** botão.
    2. No **gerir chaves de acesso** página, selecione o texto da chave de acesso primária e, em seguida, escolha as chaves de Ctrl + C.
    3. Nas ferramentas do Azure, cole a chave para o **chave da conta** caixa.
    4. Tem de selecionar uma das opções seguintes para determinar a forma como o serviço acedem a conta de armazenamento:

       * **Utilizar HTTP**. Esta é a opção padrão. Por exemplo, `http://<account name>.blob.core.windows.net`.
       * **Utilizar o HTTPS** para uma ligação segura. Por exemplo, `https://<accountname>.blob.core.windows.net`.
       * **Especifique os pontos finais personalizados** para cada um dos três serviços. Em seguida, pode escrever estes pontos finais no campo para o serviço específico.

         > [!NOTE]
         > Se criar os pontos finais personalizados, pode criar uma cadeia de ligação mais complexa. Quando utilizar este formato de cadeia, pode especificar pontos finais de serviço de armazenamento que incluem um nome de domínio personalizado que registou para a sua conta de armazenamento com o serviço Blob. Também pode conceder acesso apenas aos recursos do blob num contentor único através de uma assinatura de acesso partilhado. Para obter mais informações sobre como criar os pontos finais personalizados, consulte [configurar cadeias de ligação de armazenamento do Azure](storage/common/storage-configure-connection-string.md).
         >
         >
11. Para guardar estas alterações de cadeia de ligação, escolha o **OK** botão e, em seguida, escolha o **guardar** botão na barra de ferramentas. Depois de guardar estas alterações, pode obter o valor desta cadeia de ligação no código utilizando [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando publicar a aplicação no Azure, escolha a configuração do serviço que contém a conta de armazenamento do Azure para a cadeia de ligação. Depois da aplicação for publicada, certifique-se de que a aplicação funciona conforme esperado nos serviços de armazenamento do Azure

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a publicação de aplicações para o Azure a partir do Visual Studio, consulte [publicação de um serviço em nuvem com as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).
