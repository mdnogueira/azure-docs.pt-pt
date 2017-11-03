---
title: "Configurar as funções para um serviço em nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como definir e configurar funções para serviços em nuvem do Azure com o Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 17da71ac0c5ab9330b9244c0354e4d161d98229e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Configurar funções de serviço em nuvem do Azure com o Visual Studio
Um serviço em nuvem do Azure pode ter mais do que um trabalho ou funções da web. Para cada função, tem de definir a forma como essa função está configurada e também configurar como essa função é executada. Para saber mais sobre as funções nos serviços em nuvem, veja o vídeo [introdução ao Cloud Services do Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

As informações do serviço em nuvem são armazenadas nos seguintes ficheiros:

- **Servicedefinition. Csdef** -o ficheiro de definição de serviço define as definições de tempo de execução para o serviço em nuvem, incluindo as funções às quais são necessárias, os pontos finais e tamanho da máquina virtual. Nenhum dos dados armazenados no `ServiceDefinition.csdef` pode ser alterada quando a função está em execução.
- **Serviceconfiguration. Cscfg** - o ficheiro de configuração do serviço configura quantas instâncias de uma função são executadas e os valores das definições definidas para uma função. Os dados armazenados no `ServiceConfiguration.cscfg` pode ser alterada enquanto a função está em execução.

Para armazenar valores diferentes para as definições que controlam como uma função é executado, pode definir várias configurações de serviço. Pode utilizar uma configuração de serviço diferentes para cada ambiente de implementação. Por exemplo, pode definir a cadeia de ligação da conta de armazenamento para utilizar o emulador do storage do Azure local numa configuração de serviço local e crie outra configuração do serviço para utilizar o armazenamento do Azure na nuvem.

Quando cria um serviço em nuvem do Azure no Visual Studio, duas configurações de serviço são automaticamente criadas e adicionadas ao seu projeto do Azure:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Configurar um serviço em nuvem do Azure
Pode configurar um serviço em nuvem do Azure a partir do Explorador de soluções no Visual Studio, conforme mostrado nos passos seguintes:

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, clique com o botão direito no projeto e, no menu de contexto, selecione **propriedades**.
   
    ![Menu de contexto de projeto de Explorador de soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Na página de propriedades do projeto, selecione o **desenvolvimento** separador. 

    ![Página de propriedades do projeto - separador de desenvolvimento](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. No **a configuração do serviço** lista, selecione o nome da configuração do serviço que pretende editar. (Se pretender efetuar alterações a todas as configurações de serviço para esta função, selecione **todas as configurações**.)
   
    > [!IMPORTANT]
    > Se escolher uma configuração de serviço específicos, algumas propriedades estão desativadas porque só pode ser definidas para todas as configurações. Para editar estas propriedades, tem de selecionar **todas as configurações**.
    > 
    > 
   
    ![Lista de configuração do serviço para um serviço em nuvem do Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Alterar o número de instâncias de função
Para melhorar o desempenho do seu serviço em nuvem, pode alterar o número de instâncias de uma função que estejam a executar com base no número de utilizadores ou a carga esperada para uma função específica. É criada uma máquina de virtual separada para cada instância de uma função quando o serviço em nuvem executado no Azure. Este procedimento afeta a faturação para a implementação deste serviço de nuvem. Para mais informações sobre faturação, consulte [compreender a fatura do Microsoft Azure](billing/billing-understand-your-bill.md).

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, expanda o nó do projeto. Sob o **funções** nó, clique com o botão direito a função que pretende atualizar e, no menu de contexto, selecione **propriedades**.

    ![Menu de contexto da função de Azure do Explorador de soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione o **configuração** separador.

    ![Separador Configuração](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. No **a configuração do serviço** lista, selecione a configuração do serviço que pretende atualizar.
   
    ![Lista de configuração do serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. No **instância contagem** texto, introduza o número de instâncias que pretende iniciar para esta função. Cada instância é executado numa máquina virtual separada ao publicar o serviço em nuvem no Azure.

    ![Atualizar a contagem de instâncias](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Do Visual Studio, barra de ferramentas, selecione **guardar**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gerir as cadeias de ligação para contas de armazenamento
Pode adicionar, remover ou modificar as cadeias de ligação para as configurações de serviço. Por exemplo, pode pretender uma cadeia de ligação de local de uma configuração de serviço local que tem um valor de `UseDevelopmentStorage=true`. Pode também querer configurar uma configuração de serviço em nuvem que utiliza uma conta de armazenamento no Azure.

> [!WARNING]
> Ao introduzir as informações de chave de conta de armazenamento do Azure para uma cadeia de ligação da conta de armazenamento, esta informação é armazenada localmente no ficheiro de configuração do serviço. No entanto, esta informação não atualmente é armazenada como texto não encriptado.
> 
> 

Ao utilizar um valor diferente para cada configuração de serviço, não é necessário utilizar cadeias de ligação diferente no seu serviço em nuvem ou modificar o seu código quando publicar o seu serviço em nuvem para o Azure. Pode utilizar o mesmo nome para a cadeia de ligação no seu código e o valor é diferente, com base na configuração de serviço que selecionar quando criar o seu serviço em nuvem ou quando a publicá-lo.

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, expanda o nó do projeto. Sob o **funções** nó, clique com o botão direito a função que pretende atualizar e, no menu de contexto, selecione **propriedades**.

    ![Menu de contexto da função de Azure do Explorador de soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione o **definições** separador.

    ![Separador Definições](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. No **a configuração do serviço** lista, selecione a configuração do serviço que pretende atualizar.

    ![Configuração do serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Para adicionar uma cadeia de ligação, selecione **Adicionar definição**.

    ![Adicionar cadeia de ligação](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Assim que a nova definição foi adicionada à lista, atualize a linha na lista com as informações necessárias.

    ![Nova cadeia de ligação](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nome** -introduza o nome que pretende utilizar para a cadeia de ligação.
    - **Tipo** - selecione **cadeia de ligação** na lista pendente.
    - **Valor** -pode optar por introduzir a cadeia de ligação diretamente para o **valor** da célula ou selecione as reticências (…) para funcionar no **criar cadeia de ligação de armazenamento** caixa de diálogo.  

1. No **criar cadeia de ligação de armazenamento** caixa de diálogo, selecione uma opção para **estabelecer ligação utilizando**. Em seguida, siga as instruções para a opção que selecionar:

    - **Emulador de armazenamento do Microsoft Azure** -se selecionar esta opção, as restantes definições na caixa de diálogo estão desativadas por aplicam-se apenas ao Azure. Selecione **OK**.
    - **A subscrição** - se selecionar esta opção, utilize a lista pendente para o selecionar e iniciar sessão numa conta Microsoft ou adicione uma conta Microsoft. Selecione uma conta de armazenamento e de subscrição do Azure. Selecione **OK**.
    - **Introduzir manualmente as credenciais** -introduza o nome da conta de armazenamento e a chave primária ou segundo. Selecione uma opção para **ligação** (HTTPS é recomendado para a maioria dos cenários.) Selecione **OK**.

1. Para eliminar uma cadeia de ligação, selecione a cadeia de ligação e, em seguida, selecione **remover definição**.

1. Do Visual Studio, barra de ferramentas, selecione **guardar**.

## <a name="programmatically-access-a-connection-string"></a>Uma cadeia de ligação de acesso através de programação

Os passos seguintes mostram como aceder programaticamente a uma cadeia de ligação com a c#.

1. Adicione o seguinte utilizando as diretivas de para um ficheiro c# onde vai utilizar a definição:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. O seguinte código ilustra um exemplo de como aceder a uma cadeia de ligação. Substitua o &lt;ConnectionStringName > marcador de posição com o valor adequado. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Adicionar definições personalizadas para utilizar no seu serviço em nuvem do Azure
Definições personalizadas no ficheiro de configuração do serviço permitem-lhe adicionar um nome e valor para uma cadeia para uma configuração de serviço específicos. Pode optar por utilizar esta definição para configurar uma funcionalidade no seu serviço de nuvem ao ler o valor da definição e utilizar este valor para controlar a lógica no seu código. Pode alterar estes valores de configuração de serviço sem ter de recriar o seu pacote de serviço ou quando está a executar o serviço em nuvem. O código pode verificar para notificações de quando um alterações de definição. Consulte [RoleEnvironment.Changing eventos](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Pode adicionar, remover ou modificar as definições personalizadas para as configurações de serviço. Poderá pretender que estas cadeias para configurações de serviço diferentes valores diferentes.

Ao utilizar um valor diferente para cada configuração de serviço, não é necessário utilizar cadeias diferentes no seu serviço em nuvem ou modificar o seu código quando publicar o seu serviço em nuvem para o Azure. Pode utilizar o mesmo nome para a cadeia no seu código e o valor é diferente, com base na configuração de serviço que selecionar quando criar o seu serviço em nuvem ou quando a publicá-lo.

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, expanda o nó do projeto. Sob o **funções** nó, clique com o botão direito a função que pretende atualizar e, no menu de contexto, selecione **propriedades**.

    ![Menu de contexto da função de Azure do Explorador de soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione o **definições** separador.

    ![Separador Definições](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. No **a configuração do serviço** lista, selecione a configuração do serviço que pretende atualizar.

    ![Lista de configuração do serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Para adicionar uma definição personalizada, selecione **Adicionar definição**.

    ![Adicionar definição personalizada](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Assim que a nova definição foi adicionada à lista, atualize a linha na lista com as informações necessárias.

    ![Nova definição personalizada](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nome** -introduza o nome da definição.
    - **Tipo** - selecione **cadeia** na lista pendente.
    - **Valor** -introduza o valor da definição. Ou pode introduzir o valor diretamente para o **valor** da célula ou selecione o botão de reticências (…) para introduzir o valor no **Editar cadeia** caixa de diálogo.  

1. Para eliminar uma definição personalizada, selecione a definição e, em seguida, selecione **remover definição**.

1. Do Visual Studio, barra de ferramentas, selecione **guardar**.

## <a name="programmatically-access-a-custom-settings-value"></a>Valor de uma definição personalizada de acesso através de programação
 
Os passos seguintes mostram como aceder programaticamente a uma definição personalizada com c#.

1. Adicione o seguinte utilizando as diretivas de para um ficheiro c# onde vai utilizar a definição:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. O seguinte código ilustra um exemplo de como aceder a uma definição personalizada. Substitua o &lt;SettingName > marcador de posição com o valor adequado. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gerir o armazenamento local para cada instância de função
Pode adicionar armazenamento para cada instância de uma função do sistema de ficheiros local. Os dados armazenados em que o armazenamento não pode ser acedido por outras instâncias de função para que os dados são armazenados ou por outras funções.  

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, expanda o nó do projeto. Sob o **funções** nó, clique com o botão direito a função que pretende atualizar e, no menu de contexto, selecione **propriedades**.

    ![Menu de contexto da função de Azure do Explorador de soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione o **armazenamento Local** separador.

    ![Separador de armazenamento local](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. No **a configuração do serviço** lista, certifique-se de que **todas as configurações** está selecionado como aplicam as definições de armazenamento local para todas as configurações de serviço. Qualquer outro valor resulta em todos os campos de entrada na página a ser desativada. 

    ![Lista de configuração do serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Para adicionar uma entrada de armazenamento local, selecione **adicionar armazenamento Local**.

    ![Adicionar armazenamento local](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Assim que a nova entrada de armazenamento local foi adicionada à lista, atualize a linha na lista com as informações necessárias.

    ![Nova entrada de armazenamento local](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Nome** -introduza o nome que pretende utilizar para o novo armazenamento local.
    - **Tamanho (MB)** -introduza o tamanho em MB que necessita para o novo armazenamento local.
    - **Limpeza em Reciclagem de função** -Selecione esta opção para remover os dados no novo armazenamento local quando a máquina virtual para a função é reciclada.

1. Para eliminar uma entrada de armazenamento local, selecione a entrada e, em seguida, selecione **remover armazenamento Local**.

1. Do Visual Studio, barra de ferramentas, selecione **guardar**.

## <a name="programmatically-accessing-local-storage"></a>Através de programação aceder ao armazenamento local

Esta secção ilustra como aceder programaticamente ao armazenamento local utilizando c# ao escrever um ficheiro de texto de teste `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Escrever um ficheiro de texto para o armazenamento local

O código seguinte mostra um exemplo de como escrever um ficheiro de texto para o armazenamento local. Substitua o &lt;LocalStorageName > marcador de posição com o valor adequado. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>Localizar um ficheiro escrito para o armazenamento local

Para ver o ficheiro criado pelo código na secção anterior, siga estes passos:
    
1.  Na área de notificação do Windows, clique com o botão direito no ícone do Azure e, no menu de contexto, selecione **mostrar IU do emulador de computação**. 

    ![Mostrar o emulador de computação do Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Selecione a função da web.

    ![Emulador de computação do Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. No **emulador de computação do Microsoft Azure** menu, selecione **ferramentas** > **Abrir arquivo local**.

    ![Item de menu abrir arquivo local](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Quando abre a janela do Explorador do Windows, introduza ' MyLocalStorageTest.txt ' para o **pesquisa** caixa de texto e selecione **Enter** para iniciar a pesquisa. 

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre projetos do Azure no Visual Studio o lendo [configurar um projeto do Azure](vs-azure-tools-configuring-an-azure-project.md). Saiba mais sobre o esquema do serviço de nuvem o lendo [esquema referência](https://msdn.microsoft.com/library/azure/dd179398).

