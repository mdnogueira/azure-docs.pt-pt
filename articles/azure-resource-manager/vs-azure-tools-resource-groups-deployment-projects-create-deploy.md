---
title: Projetos do grupo de recursos do Azure do Visual Studio | Microsoft Docs
description: Utilize o Visual Studio para criar um projeto do grupo de recursos do Azure e implemente os recursos para o Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 4bd084c8-0842-4a10-8460-080c6a085bec
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: tomfitz
ms.openlocfilehash: f82f59f363507b69a729580302c2d11202e93a87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Criar e implementar grupos de recursos do Azure através do Visual Studio
Com o Visual Studio e o [Azure SDK](https://azure.microsoft.com/downloads/), pode criar um projeto que implementa a sua infraestrutura e o código para o Azure. Por exemplo, pode definir o anfitrião web, o Web site e a base de dados para a sua aplicação e implementar essa infraestrutura juntamente com o código. Em alternativa, pode definir uma Máquina Virtual, a Virtual Network e a Conta do Storage e implemente essa infraestrutura juntamente com um script que é executado na Máquina Virtual. O projeto de implementação **Grupo de Recursos do Azure** permite-lhe implementar todos os recursos necessários numa operação única e repetida. Para obter mais informações sobre como implementar e gerir os recursos, consulte o artigo [Descrição geral do Azure Resource Manager](resource-group-overview.md).

Os projetos do Grupo de Recursos do Azure contêm modelos JSON do Azure Resource Manager, que definem os recursos que implementou para o Azure. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md). O Visual Studio permite-lhe editar estes modelos e fornece ferramentas que simplificam o trabalho com modelos.

Neste artigo, implemente uma aplicação Web e uma Base de Dados SQL. No entanto, os passos são quase os mesmos para qualquer tipo de recurso. De igual forma, pode facilmente implementar uma Máquina Virtual e os respetivos recursos relacionados. O Visual Studio fornece muitos modelos de introdução diferentes para cenários comuns de implementação.

Este artigo mostra o Visual Studio 2017. Se utilizar o Visual Studio 2015 Update 2 e o Microsoft Azure SDK para .NET 2.9 ou o Visual Studio 2013 com o Azure SDK 2.9, a experiência é basicamente a mesma. Pode utilizar a versão 2.6 ou posterior do Azure SDK. No entanto, a sua experiência de interface do utilizador pode ser diferente da interface do utilizador que é mostrada neste artigo. Recomendamos vivamente que instale a versão mais recente do [Azure SDK](https://azure.microsoft.com/downloads/) antes de iniciar os passos. 

## <a name="create-azure-resource-group-project"></a>Criar projeto do Grupo de Recursos do Azure
Neste procedimento, cria um projeto de Grupo de Recursos do Azure com um modelo **Aplicação Web + SQL Server**.

1. No Visual Studio, selecione **Ficheiro**, **Novo Projeto**, escolha **C#** ou **Visual Basic**. Em seguida, selecione **Nuvem** e, em seguida, o projeto **Grupo de Recursos do Azure**.
   
    ![Projeto de Cloud Deployment](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Escolha o modelo que pretende implementar no Azure Resource Manager. Tenha em atenção que existem muitas opções diferentes com base no tipo de projeto que pretende implementar. Para este artigo, selecione o modelo **Aplicação Web + SQL**.
   
    ![Escolher um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    O modelo que escolher é apenas um ponto de partida; pode adicionar e remover recursos para satisfazer o seu cenário.
   
   > [!NOTE]
   > O Visual Studio obtém uma lista dos modelos disponíveis online. A lista pode ser alterada.
   > 
   > 
   
    O Visual Studio cria um projeto de implementação do grupo de recursos para a aplicação Web e a SQL Database.
3. Para ver o que criou, observe o nó no projeto de implementação.
   
    ![mostrar nós](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Uma vez que escolhemos o modelo Aplicação Web + SQL para este exemplo, verá os seguintes ficheiros: 
   
   | Nome de ficheiro | Descrição |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Um script do PowerShell que invoca comandos do PowerShell para implementar no Azure Resource Manager.<br />**Nota** O Visual Studio utiliza este script do PowerShell para implementar o modelo. Quaisquer alterações que efetuar neste script afetam a implementação no Visual Studio, por isso, tenha cuidado. |
   | WebSiteSQLDatabase.json |O modelo do Resource Manager que define a infraestrutura que pretende implementar no Azure e os parâmetros que pode fornecer durante a implementação. Define as dependências entre os recursos para que o Resource Manager os implemente na ordem correta. |
   | WebSiteSQLDatabase.parameters.json |Um ficheiro de parâmetros que contém valores necessários para o modelo. Passa nos valores dos parâmetros para personalizar cada implementação. |
   
    Todos os projetos de implementação do grupo de recursos contêm estes ficheiros básicos. Outros projetos poderão conter ficheiros adicionais para suportar outras funcionalidades.

## <a name="customize-the-resource-manager-template"></a>Personalizar o modelo do Resource Manager
Pode personalizar um projeto de implementação ao modificar os modelos JSON que descrevem os recursos que pretende implementar. O JSON significa JavaScript Object Notation e é um formato de dados serializados com o qual é fácil de trabalhar. Os ficheiros JSON utilizam um esquema que é referenciado na parte superior de cada ficheiro. Se pretende compreender o esquema, pode transferi-lo e analisá-lo. O esquema define os elementos que são válidos, os tipos e formatos dos campos, os valores possíveis dos valores enumerados, etc. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).

Para trabalhar no seu modelo, abra **WebSiteSQLDatabase.json**.

O editor do Visual Studio fornece ferramentas para o ajudar a editar o modelo do Resource Manager. A janela **Contorno de JSON** faz com que seja mais fácil ver os elementos definidos no seu modelo.

![mostrar o contorno de JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Selecionar qualquer um dos elementos no contorno leva-o para essa parte do modelo e realça o JSON correspondente.

![navegar no JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Pode adicionar um recurso, selecionando o botão **Adicionar Recurso** na parte superior da janela Contorno de JSON ou clicando com o botão direito do rato em **recursos** e selecionando **Adicionar Novo Recurso**.

![adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Para este tutorial, selecione **Conta do Storage** e atribua um nome. Forneça um nome com um máximo de 11 carateres e apenas números e letras minúsculas.

![adicionar armazenamento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Tenha em atenção que o recurso foi adicionado, mas também um parâmetro para a conta do Storage do tipo e uma variável para o nome da conta do Storage.

![mostrar contorno](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

O parâmetro **storageType** é predefinido com tipos permitidos e um tipo predefinido. Pode deixar estes valores ou editá-los para o seu cenário. Se não pretender que ninguém implemente uma conta de armazenamento **Premium_LRS** através deste modelo, remova-a dos tipos permitidos. 

```json
"storageType": {
  "type": "string",
  "defaultValue": "Standard_LRS",
  "allowedValues": [
    "Standard_LRS",
    "Standard_ZRS",
    "Standard_GRS",
    "Standard_RAGRS"
  ]
}
```

O Visual Studio também fornece intellisense para o ajudar a compreender que propriedades estão disponíveis ao editar o modelo. Por exemplo, para editar as propriedades para o seu plano do Serviço de Aplicações, navegue até ao recurso **HostingPlan** e adicione um valor para as **propriedades**. Repare que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

![mostrar o intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Pode definir **numberOfWorkers** para 1.

```json
"properties": {
  "name": "[parameters('hostingPlanName')]",
  "numberOfWorkers": 1
}
```

## <a name="deploy-the-resource-group-project-to-azure"></a>Implementar o projeto do Grupo de Recursos para o Azure
Agora, está pronto para implementar o projeto. Quando implementa um projeto do Grupo de Recursos do Azure, pode implementar para um grupo de recursos do Azure. O grupo de recursos é um agrupamento lógico de recursos que partilham um ciclo de vida comum.

1. No menu de atalho do nó do projeto de implementação, selecione **Implementar** > **Novo**.
   
    ![Implementar, item de menu Nova Implementação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    É apresentada a caixa de diálogo **Implementar no Grupo de Recursos**.
   
    ![Caixa de Diálogo Implementar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. Na caixa de diálogo pendente **Grupo de recursos**, escolha um grupo de recursos existente ou crie um novo. Para criar um grupo de recursos, abra a caixa pendente **Grupo de Recursos** e selecione **Criar Novo**.
   
    ![Caixa de Diálogo Implementar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    É apresentada a caixa de diálogo **Criar Grupo de Recursos**. Atribua ao grupo um nome e localização e selecione o botão **Criar**.
   
    ![Caixa de Diálogo Criar Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Para editar os parâmetros da implementação, selecione o botão **Editar Parâmetros**.
   
    ![Botão Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Forneça valores para os parâmetros vazios e selecione o botão **Guardar**. Os parâmetros vazios são **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** e **databaseName**.
   
    **hostingPlanName** especifica um nome do [Plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) a criar. 
   
    **administratorLogin** especifica o nome de utilizador do administrador do SQL Server. Não utilize nomes de administradores comuns, como **sa** ou **admin**. 
   
    **administratorLoginPassword** especifica a palavra-passe do administrador do SQL Server. A opção **Guardar palavras-passe como texto simples no ficheiro de parâmetros** não é segura; por isso, não a selecione. Uma vez que a palavra-passe não é guardada como texto simples, tem de fornecer novamente esta palavra-passe durante a implementação. 
   
    **databaseName** especifica um nome para a base de dados criar. 
   
    ![Caixa de Diálogo Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Escolha o botão **Implementar** para implementar o projeto no Azure. É aberta uma consola do PowerShell fora da instância do Visual Studio. Introduza a palavra-passe de administrador do SQL Server na consola do PowerShell quando lhe for pedido. **A consola do PowerShell poderá estar oculta atrás de outros itens ou minimizada na barra de tarefas.** Procure esta consola e selecione-a para fornecer a palavra-passe.
   
   > [!NOTE]
   > O Visual Studio poderá pedir-lhe que instale os cmdlets do Azure PowerShell. Precisa de cmdlets do Azure PowerShell para implementar grupos de recursos com êxito. Se lhe for solicitado, instale-os.
   > 
   > 
6. A implementação pode demorar alguns minutos. O estado da implementação é apresentado nas janelas **Saída**. Após concluir a implementação, a última mensagem indicará uma implementação bem sucedida com algo semelhante a:
   
        ... 
        18:00:58 - Successfully deployed template 'websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. Num browser, abra o [Portal do Azure](https://portal.azure.com/) e inicie sessão na sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos no qual implementou.
   
    ![selecionar grupo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Veja todos os recursos implementados. Repare que o nome da conta de armazenamento não é exatamente aquele que especificou quando adicionou esse recurso. A conta de armazenamento deve ser exclusiva. O modelo adiciona automaticamente uma cadeia de carateres ao nome que indicou para fornecer um nome exclusivo. 
   
    ![mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Se efetuar alterações e quiser voltar a implementar o projeto, escolha o grupo de recursos existente diretamente a partir do menu de atalho do projeto do grupo de recursos do Azure. No menu de atalho, escolha **Implementar** e, em seguida, selecione o grupo de recursos no qual acabou de implementar.
   
    ![Grupo de recursos Azure do implementado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Implementar código com a infraestrutura
Neste momento, implementou a infraestrutura para a sua aplicação, mas não existe qualquer código real implementado com o projeto. Este artigo mostra como implementar uma aplicação Web e tabelas de Bases de Dados SQL durante a implementação. Se estiver a implementar uma Máquina Virtual em vez de uma aplicação Web, execute algum código na máquina como parte da implementação. O processo de implementação do código numa aplicação Web ou de configuração de uma Máquina Virtual é quase o mesmo.

1. Adicione um projeto à sua solução do Visual Studio. Clique com o botão direito do rato na solução e selecione **Adicionar** > **Novo Projeto**.
   
    ![adicionar projeto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Adicione uma **Aplicação Web ASP.NET**. 
   
    ![adicionar aplicação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Selecione **MVC**.
   
    ![selecionar MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Depois de o Visual Studio ter criado a sua aplicação Web, verá ambos os projetos na solução.
   
    ![mostrar projetos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. Agora, tem de verificar se o projeto do grupo de recursos deteta o novo projeto. Volte ao projeto do grupo de recursos (AzureResourceGroup1). Clique com o botão direito do rato em **Referências** e selecione **Adicionar Referência**.
   
    ![adicionar referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Selecione o projeto da aplicação Web criado.
   
    ![adicionar referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    Ao adicionar uma referência, liga o projeto da aplicação Web ao projeto do grupo de recursos e define automaticamente as três propriedades da chave. Pode ver estas propriedades na janela **Propriedades** da referência.
   
      ![ver referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    As propriedades são:
   
   * As **Propriedades Adicionais** contêm a localização de transição do pacote de implementação Web que é enviada para o Armazenamento do Azure. Anote a pasta (ExampleApp) e o ficheiro (package.zip). É necessário saber estes valores, uma vez que os fornece como parâmetros ao implementar a aplicação. 
   * O **Incluir Caminho do Ficheiro** contém o caminho onde o pacote é criado. O **Incluir Destinos** contém o comando que a implementação executa. 
   * O valor predefinido de **Build;Package** permite a implementação para criar um pacote de implementação Web (package.zip).  
     
     Não é necessário um perfil de publicação, uma vez que a implementação obtém as informações necessárias das propriedades para criar o pacote.
7. Volte a WebSiteSQLDatabase.json e adicione um recurso ao modelo.
   
    ![adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Desta vez, selecione **Implementação Web de Aplicações Web**. 
   
    ![adicionar implementação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Reimplemente o projeto do grupo de recursos no grupo de recursos. Desta vez, existem alguns parâmetros novos. Não é necessário fornecer valores para **_artifactsLocation** ou **_artifactsLocationSasToken** porque o Visual Studio gera automaticamente esses valores. No entanto, tem de definir o nome da pasta e do ficheiro como o caminho que contém o pacote de implementação (apresentado como **ExampleAppPackageFolder** e **ExampleAppPackageFileName** na imagem seguinte). Forneça os valores que viu anteriormente nas propriedades de referência (**ExampleApp** e **package.zip**).
   
    ![adicionar implementação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    Para a **Conta de armazenamento de artefactos**, selecione a que foi implementada com este grupo de recursos.
10. Após concluir a implementação, selecione a aplicação Web no portal. Selecione o URL para navegar para o site.
    
     ![navegar para o site](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Repare que implementou com sucesso a aplicação ASP.NET predefinida.
    
     ![mostrar aplicação implementada](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como gerir os recursos através do portal, veja [Using the Azure portal to manage your Azure resources (Utilizar o Portal do Azure para gerir os recursos do Azure)](resource-group-portal.md).
* Para saber mais sobre modelos, consulte o artigo [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).

