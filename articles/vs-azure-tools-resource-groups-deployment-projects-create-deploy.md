<properties
   pageTitle="Projetos de Grupo de Recursos do Visual Studio do Azure | Microsoft Azure"
   description="Utilize o Visual Studio para criar um projeto do grupo de recursos do Azure e implemente os recursos para o Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="tomfitz" />

# Criar e implementar grupos de recursos do Azure através do Visual Studio

Com o Visual Studio e o [Azure SDK](https://azure.microsoft.com/downloads/), pode criar um projeto que implementa a sua infraestrutura e o código para o Azure. Por exemplo, pode definir o anfitrião web, o Web site e a base de dados para a sua aplicação e implementar essa infraestrutura juntamente com o código. Em alternativa, pode definir uma Máquina Virtual, a Virtual Network e a Conta do Storage e implemente essa infraestrutura juntamente com um script que é executado na Máquina Virtual. O projeto de implementação **Grupo de Recursos do Azure** permite-lhe implementar todos os recursos necessários numa operação única e repetida. Para obter mais informações sobre como implementar e gerir os recursos, consulte o artigo [Descrição geral do Azure Resource Manager](resource-group-overview.md).

Os projetos do Grupo de Recursos do Azure contêm modelos JSON do Azure Resource Manager, que definem os recursos que são implementados para o Azure. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md). O Visual Studio permite-lhe editar estes modelos e fornece ferramentas que simplificam o trabalho com modelos.

Neste tópico, irá implementar uma aplicação Web e a SQL Database. No entanto, os passos são quase os mesmos para qualquer recurso de tipo. De igual forma, pode facilmente implementar uma Máquina Virtual e os respetivos recursos relacionados. O Visual Studio fornece muitos modelos de introdução diferentes para cenários comuns de implementação.

Este artigo foi escrito utilizando o Visual Studio 2015 Update 2 e o Microsoft Azure SDK para .NET 2.9. Se utilizar o Visual Studio 2013 com o Azure SDK 2.9, sua experiência será amplamente a mesma. Pode utilizar a versão 2.6 ou posterior do Azure SDK. No entanto, a sua experiência pode ser diferente da que é mostrada neste artigo. É vivamente recomendado que instale a versão mais recente do [Azure SDK](https://azure.microsoft.com/downloads/) antes de iniciar os passos. 

## Criar projeto do Grupo de Recursos do Azure

Neste procedimento, irá criar um projeto de Grupo de Recursos do Azure com um modelo **Aplicação Web + SQL Server**.

1. No Visual Studio, selecione **Ficheiro**, **Novo Projeto**, escolha **C#** ou **Visual Basic**. Em seguida, escolha **Nuvem**, e, em seguida, escolha **Grupo de Recursos do Azure**.

    ![Projeto de Cloud Deployment](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Escolha o modelo que pretende implementar no Azure Resource Manager. Tenha em atenção que existem muitas opções diferentes com base no tipo de projeto que pretende implementar. Para este tópico, vamos escolher o modelo **Aplicação Web + SQL Server**.

    ![Escolher um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    O modelo que escolher é apenas um ponto de partida; pode adicionar e remover recursos para satisfazer o seu cenário.

    >[AZURE.NOTE] A lista dos modelos disponíveis é obtida online e pode ser alterada.

    O Visual Studio cria um projeto de implementação do grupo de recursos para a aplicação Web e a SQL Database.

1. Expanda os nós no projeto de implementação para ver o que foi criado.

    ![mostrar nós](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Uma vez que escolhemos o modelo Aplicação Web + SQL para este exemplo, pode ver os seguintes ficheiros. 

  	|Nome de ficheiro|Descrição|
  	|---|---|
  	|Deploy-AzureResourceGroup.ps1|Um script do PowerShell que invoca comandos do PowerShell para implementar no Azure Resource Manager.<br />**Nota** este script do PowerShell é utilizado pelo Visual Studio para implementar o modelo. Quaisquer alterações que efetuar a este script irão também afetar a implementação no Visual Studio, por isso, tenha cuidado.|
  	|WebSiteSQLDatabase.json|O modelo do resource manager que define a infraestrutura que pretende implementar no Azure e os parâmetros que pode fornecer durante a implementação. Define as dependências entre os recursos para que sejam implementados na ordem correta.|
  	|WebSiteSQLDatabase.parameters.json|Um ficheiro de parâmetros que contém valores necessários para o modelo. Estes são os valores que passa para personalizar cada implementação.|

    Todos os projetos de implementação do grupo de recursos contêm estes ficheiros básicos. Outros projetos poderão conter ficheiros adicionais para suportar outras funcionalidades.

## Personalizar o modelo do Resource Manager

Pode personalizar um projeto de implementação ao modificar os modelos JSON que descrevem os recursos que pretende implementar. O JSON significa JavaScript Object Notation e é um formato de dados serializados com o qual é fácil de trabalhar. Os ficheiros JSON utilizam um esquema que é referenciado na parte superior de cada ficheiro. Pode transferir o esquema e analisá-lo se pretender que o compreendam melhor. O esquema define os elementos que são permitidos, os tipos e formatos dos campos, os valores possíveis dos valores enumerados, etc. Para saber mais sobre os elementos do modelo do Resource Manager, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).

Para trabalhar no seu modelo, abra **WebSiteSQLDatabase.json**.

O editor do Visual Studio fornece ferramentas para o ajudar a editar o modelo do resource manager. A janela **Contorno de JSON** faz com que seja mais fácil ver os elementos definidos no seu modelo.

![mostrar o contorno de JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Selecionar qualquer um dos elementos no contorno leva-o para essa parte do modelo e realça o JSON correspondente.

![navegar no JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Pode adicionar um novo recurso ao seu modelo selecionando o botão **Adicionar Recurso** na parte superior da janela Contorno de JSON ou clicando com o botão direito do rato em **recursos** e selecionando **Adicionar Novo Recurso**.

![adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Para este tutorial, selecione **Conta do Storage** e atribua um nome. Um nome da conta do Storage tem de ser apenas números e letras minúsculas e inferior a 24 carateres. O projeto adicionará uma cadeia exclusiva de 13 carateres ao nome que fornece, por isso, certifique-se de que o nome não tem mais de 11 carateres.

![adicionar armazenamento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Tenha em atenção que o recurso foi adicionado, mas também um parâmetro para a conta do Storage do tipo e uma variável para o nome da conta do Storage.

![mostrar contorno](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

O parâmetro **storageType** é predefinido com tipos permitidos e um tipo predefinido. Pode deixar estes valores ou editá-los para o seu cenário. Se não pretender permitir que ninguém implemente uma conta do Storage **Premium_LRS** através deste modelo, basta removê-lo dos tipos permitidos, conforme mostrado abaixo. 

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

O Visual Studio também fornece intellisense para o ajudar a compreender que propriedades estão disponíveis ao editar o modelo. Por exemplo, para editar as propriedades para o seu plano do App Service, navegue até ao recurso **HostingPlan** e adicione um novo valor para as **propriedades**. Repare que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

![mostrar o intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Pode definir **numberOfWorkers** para 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Implementar o projeto do Grupo de Recursos para o Azure

Agora, está pronto para implementar o projeto. Quando implementar um projeto do Grupo de Recursos do Azure, implementa-o num grupo de recursos do Azure, que é apenas um agrupamento lógico de recursos no Azure como Web Apps, bases de dados, etc.

1. No menu de atalho do nó do projeto de implementação, selecione **Implementar** > **Nova Implementação**.

    ![Implementar, item de menu Nova Implementação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    É apresentada a caixa de diálogo **Implementar no Grupo de Recursos**.

    ![Caixa de Diálogo Implementar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Na caixa de diálogo pendente **Grupo de recursos**, escolha um grupo de recursos existente ou crie um novo. Para criar um grupo de recursos, abra a caixa pendente **Grupo de Recursos** e selecione **Criar Novo …**.

    ![Caixa de Diálogo Implementar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    É apresentada a caixa de diálogo **Criar Grupo de Recursos**. Atribua ao grupo um nome e localização e selecione o botão **Criar**.

    ![Caixa de Diálogo Criar Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Pode editar os parâmetros para a implementação, selecionando o botão **Editar Parâmetros**. Forneça valores para os parâmetros e selecione o botão **Guardar**.

    ![Caixa de Diálogo Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    A opção **Guardar palavras-passe como texto simples no ficheiro de parâmetros** não é segura.

1. Escolha o botão **Implementar** para implementar o projeto no Azure. Pode ver o progresso da implementação na janela de **Resultados**. A implementação pode demorar vários minutos a concluir, consoante a configuração. Introduza a palavra-passe de administrador na consola do PowerShell quando lhe for pedido. Se o progresso da sua implementação estagnou, poderá ser porque o processo está a aguardar que introduza a palavra-passe na consola do PowerShell.

    >[AZURE.NOTE] Poderá ser-lhe pedido que instale os cmdlets do Azure PowerShell. Uma vez que estes cmdlets são necessários para implementar os grupos de recursos do Azure, tem de os instalar.
    
1. Após a implementação estar concluída, deverá ver uma mensagem na janela de **Resultados** como:

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. Num browser, abra o [Portal do Azure](https://portal.azure.com/) e inicie sessão na sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos no qual implementou.

    ![selecionar grupo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Irá ver todos os recursos implementados.

    ![mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Se efetuar alterações e quiser voltar a implementar o projeto, pode escolher o grupo de recursos existente diretamente a partir do menu de atalho do projeto do grupo de recursos Azure. No menu de atalho, escolha **Implementar** e, em seguida, selecione o grupo de recursos no qual acabou de implementar.

    ![Grupo de recursos Azure do implementado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Implementar código com a infraestrutura

Neste momento, implementou a infraestrutura para a sua aplicação, mas não existe qualquer código real implementado com o projeto. Este tópico mostra como implementar uma aplicação Web e tabelas de Bases de Dados SQL durante a implementação. Se estiver a implementar uma Máquina Virtual em vez de uma aplicação Web, execute algum código na máquina como parte da implementação. O processo de implementação do código numa aplicação Web ou de configuração de uma Máquina Virtual é quase o mesmo.

1. Na sua solução Visual Studio, adicione um **Aplicação Web ASP.NET**. 

    ![adicionar aplicação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Selecione **MVC** e desmarque o campo para **Anfitrião na nuvem** porque o projeto de grupo de recursos irá efetuar essa tarefa.

    ![selecionar MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Depois de ter sido criada a sua aplicação Web, adicione uma referência no projeto do grupo de recursos para o projeto de aplicação Web.

    ![adicionar referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Ao adicionar uma referência, liga o projeto da aplicação Web ao projeto do grupo de recursos e define automaticamente as três propriedades da chave.  
    
    - As **Propriedades Adicionais** contêm a localização de transição do pacote de implementação Web que será enviada para o Storage do Azure. 
    - O **Incluir Caminho do Ficheiro** contém o caminho onde o pacote será criado.  O **Incluir Destinos** contém o comando que a implementação irá executar. 
    - O valor predefinido de **Build;Package** permite a implementação para criar um pacote de implementação Web (package.zip).  
    
    Não é necessário um perfil de publicação, uma vez que a implementação obtém as informações necessárias das propriedades para criar o pacote.
    
      ![ver referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Adicione um novo recurso ao modelo e, desta vez, selecione **Implementação Web para Web Apps**. 

    ![adicionar implementação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Reimplemente o projeto do grupo de recursos no grupo de recursos. Desta vez, existem alguns parâmetros novos. Não é necessário fornecer valores para **_artifactsLocation** ou **_artifactsLocationSasToken** porque são gerados automaticamente. Defina a pasta e o nome de ficheiro para o caminho que contém o pacote de implementação.

    ![adicionar implementação Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Para a **Conta do Storage do artefacto**, pode utilizar a que foi implementada com este grupo de recursos.
    
Após concluir a implementação, pode navegar para o site e repare que a aplicação ASP.NET predefinida foi implementada.

![mostrar aplicação implementada](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## Passos seguintes

- Para saber mais sobre como gerir os recursos através do portal, consulte o artigo [Utilizar o Portal do Azure para gerir os recursos do Azure](./azure-portal/resource-group-portal.md).
- Para saber mais sobre modelos, consulte o artigo [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).



<!--HONumber=Jun16_HO2-->


