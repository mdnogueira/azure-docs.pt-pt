---
title: "Aprovisionar e implementar micro-serviços previsibilidade no Azure"
description: "Saiba como implementar uma aplicação é composta por Micro-serviços no serviço de aplicações do Azure como uma única unidade e de forma previsível utilizando modelos de grupo de recursos JSON e scripts do PowerShell."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.openlocfilehash: 3719e037f1564411a8f94d1ca962ba1ef6b5d435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Aprovisionar e implementar micro-serviços previsibilidade no Azure
Este tutorial mostra como aprovisionar e implementar uma aplicação é composta por [micro-serviços](https://en.wikipedia.org/wiki/Microservices) no [App Service do Azure](/services/app-service/) como uma única unidade e de forma previsível utilizando modelos de grupo de recursos JSON e Scripts do PowerShell. 

Quando o aprovisionamento e implementação de aplicações de alta escala que são compostas por altamente desassociada micro-serviços, repetibilidade e à previsibilidade são cruciais para êxito. [App Service do Azure](/services/app-service/) permite-lhe criar micro-serviços que incluem as web apps, aplicações móveis, as API apps e as logic apps. [O Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite-lhe gerir todas as micro-serviços como uma unidade, juntamente com as dependências de recursos, tais como a base de dados e definições de controlo de origem. Agora, também pode implementar uma aplicação a utilizar modelos JSON e de script do PowerShell simples. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>O que irá fazer
O tutorial, irá implementar uma aplicação que inclui:

* Dois web apps (ou seja, dois micro-serviços)
* Um back-end da base de dados SQL
* As definições de aplicação, cadeias de ligação e o controlo de origem
* Informações de aplicação, alertas, definições de dimensionamento automático

## <a name="tools-you-will-use"></a>Ferramentas que irá utilizar
Neste tutorial, irá utilizar as seguintes ferramentas. Uma vez que não é abrangente debate sobre ferramentas, vou de pen o cenário de ponto a ponto e apenas dão-lhe uma introdução breve para cada um, e onde pode encontrar mais informações no mesmo. 

### <a name="azure-resource-manager-templates-json"></a>Modelos do Azure Resource Manager (JSON)
Sempre que criar uma aplicação web no App Service do Azure, por exemplo, o Gestor de recursos do Azure utiliza um modelo JSON para criar o grupo de recursos completo com os recursos do componente. Um modelo complexo do [Azure Marketplace](/marketplace) como o [WordPress dimensionável](/marketplace/partners/wordpress/scalablewordpress/) aplicação pode incluir a base de dados MySQL, contas de armazenamento, o plano de serviço de aplicações, a aplicação web em si, de regras de alertas, as definições de aplicação, definições de dimensionamento automático e mais e todos os estes modelos estão disponíveis para si através do PowerShell. Para obter informações sobre como transferir e utilizar estes modelos, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Para obter mais informações sobre os modelos Azure Resource Manager, consulte [criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>2.6 do Azure SDK para Visual Studio
O SDK mais recente contém melhoramentos para o suporte de modelo do Resource Manager no editor de JSON. Pode utilizar esta opção para rapidamente criar um modelo de grupo de recursos a partir do zero ou abra um modelo JSON existente (por exemplo, um modelo transferido galeria) para modificação, preencher o ficheiro de parâmetros e até mesmo implementar o grupo de recursos diretamente a partir de um recurso do Azure Solução de grupo.

Para obter mais informações, consulte [2.6 do SDK do Azure para Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>O Azure PowerShell 0.8.0 ou posterior
A partir da versão 0.8.0, a instalação do PowerShell do Azure inclui o módulo do Azure Resource Manager, para além do módulo do Azure. Este novo módulo permite-lhe a implementação de grupos de recursos de script.

Para obter mais informações, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure
Isto [ferramenta preview](https://resources.azure.com) permite-lhe explorar as definições JSON de todos os grupos de recursos na sua subscrição e os recursos individuais. Na ferramenta, pode editar as definições JSON de um recurso, eliminar uma hierarquia completa dos recursos e criar novos recursos.  As informações prontamente disponíveis nesta ferramenta são muito útil para a criação de modelo, porque mostra as propriedades que tem de definir para um determinado tipo de recurso, os valores corretos, etc. Pode criar o grupo de recursos no mesmo a [Portal do Azure](https://portal.azure.com/), em seguida, verifique as definições de JSON na ferramenta Explorador para o ajudar a templatize o grupo de recursos.

### <a name="deploy-to-azure-button"></a>Implementar para botão do Azure
Se utilizar o GitHub para controlo de origem, pode colocar um [implementar no Azure botão](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) no seu ficheiro Leia-me. MD, que permite uma implementação de ativar a chave de IU para o Azure. Enquanto pode fazê-lo para qualquer aplicação web simples, pode expandir esta opção para ativar a implementação de um grupo de recursos completa ao colocar um ficheiro azuredeploy. JSON na raiz do repositório. Este ficheiro JSON, que contém o modelo do grupo de recursos, será utilizado por implementar para botão do Azure para criar o grupo de recursos. Por exemplo, consulte o [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) exemplo, o que irá utilizar neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obter o modelo de grupo de recursos de exemplo
Agora vamos direito ao mesmo.

1. Navegue para o [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) exemplo do serviço de aplicações.
2. No readme.md, clique em **implementar no Azure**.
3. Está a ser direcionado para o [implementar para o azure](https://deploy.azure.com) -lhe pedido para parâmetros de implementação de entrada e de site. Tenha em atenção que a maioria dos campos está preenchida com o nome do repositório e algumas cadeias de aleatórias para si. Pode alterar todos os campos se quiser, mas as coisas só tem de introduzir o início de sessão administrativo do SQL Server e a palavra-passe, em seguida, clique em **seguinte**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Em seguida, clique em **implementar** para iniciar o processo de implementação. Assim que executa o processo de conclusão, clique em de http://todoapp*XXXX*. azurewebsites.net ligação para procurar a aplicação implementada. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A IU deverá ser um pouco lenta quando o utilizador primeiro procura ao mesmo porque as aplicações estão a iniciar cópia de segurança, em vez convencê---se que é uma aplicação totalmente funcional.
5. Na página de implementar, clique em de **gerir** ligação para ver a nova aplicação no Portal do Azure.
6. No **Essentials** lista pendente, clique na ligação do grupo de recursos. Tenha em atenção que a aplicação web já está ligada para o repositório do GitHub em **projeto externo**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. No painel do grupo de recursos, tenha em atenção que já existem duas aplicações web e uma base de dados do SQL Server no grupo de recursos.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tudo o que vimos apenas dentro de alguns minutos abreviados é uma aplicação de microsserviço de dois totalmente implementado, com todos os componentes, dependências, definições, base de dados e publicação contínua, configurada por uma orquestração automatizada no Gestor de recursos do Azure. Tudo isto foi feito ao duas coisas:

* A implementar no botão do Azure
* azuredeploy. JSON na raiz do repositório

Pode implementar esta aplicação mesma dezenas, centenas ou milhares de tempos e ter a mesma configuração sempre exata. A repetibilidade e a previsão desta abordagem permite-lhe implementar aplicações de grande escala com facilidade e confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examine (ou editar) AZUREDEPLOY. JSON
Agora vamos ver como o repositório do GitHub foi configurado. Irá utilizar o editor de JSON no SDK .NET da Azure, por isso, se ainda não instalou [2.6 de SDK do .NET do Azure](https://azure.microsoft.com/downloads/), fazê-lo agora.

1. Clone o [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) repositório utilizando a ferramenta de git favorito. Na captura de ecrã abaixo, posso estou fazê-lo no Explorador de equipa no Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. A partir da raiz do repositório, abra o azuredeploy. JSON no Visual Studio. Se não vir o painel de contorno de JSON, terá de instalar o SDK .NET do Azure.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Vou não descrever todos os detalhes do formato JSON, mas o [mais recursos](#resources) secção tem ligações para a linguagem de modelo do grupo de recursos de aprendizagem. Aqui, basta vou para lhe mostrar as funcionalidades interessantes que podem ajudar a começar em tornar o seu próprio modelo personalizado para a implementação de aplicação.

### <a name="parameters"></a>Parâmetros
Observe a secção de parâmetros que estão a maioria destes parâmetros que o **implementar no Azure** botão pede-lhe para introduzir. O site atrás do **implementar no Azure** botão preenche a IU utilizando parâmetros definidos na azuredeploy. JSON de entrada. Estes parâmetros são utilizados em toda as definições de recursos, tais como os nomes de recursos, os valores de propriedade, etc.

### <a name="resources"></a>Recursos
No nó de recursos, pode ver que são definidos 4 recursos de nível superior, incluindo uma instância do SQL Server, um plano de serviço de aplicações e duas aplicações web. 

#### <a name="app-service-plan"></a>Plano do App Service
Vamos começar com um recurso de nível de raiz simple no JSON. No contorno de JSON, clique em plano do App Service com o nome **[hostingPlanName]** para realçar o código JSON correspondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Tenha em atenção que o `type` elemento Especifica a cadeia para um plano de serviço de aplicações (que foi chamado um farm de servidores de uma hora por extenso, por extenso há) e outros elementos e propriedades estão preenchidas na utilização de parâmetros definidos no ficheiro JSON e este recurso não tem qualquer aninhadas recursos.

> [!NOTE]
> Tenha em atenção que o valor de `apiVersion` diz ao Azure a versão de API REST para utilizar a definição do recurso JSON com e pode afetar como o recurso deve ser formatado no interior do `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Em seguida, clique no recurso do SQL Server com o nome **SQLServer** no contorno de JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Tenha em atenção o seguinte sobre o código realçado de JSON:

* A utilização de parâmetros garante que os recursos criados são com o nome e configurados de forma a que o se tornarem consistentes entre si.
* O recurso de SQLServer tem dois recursos aninhados, cada um tem um valor diferente para `type`.
* Os recursos aninhados dentro `“resources”: […]`, onde são definidas na base de dados e as regras de firewall, tem um `dependsOn` elemento que especifica o ID de recurso do recurso de SQLServer de nível de raiz. Isto indica o Azure Resource Manager, "antes de criar este recurso, o que já devem existir outros recursos; e se a outros recursos que está definido no modelo, em seguida, crie que primeiro".
  
  > [!NOTE]
  > Para obter informações detalhadas sobre como utilizar o `resourceId()` funcionar, consulte [funções de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* O efeito do `dependsOn` elemento é que o Azure Resource Manager pode saber quais os recursos que podem ser criados em paralelo e os recursos que têm de ser criados sequencialmente. 

#### <a name="web-app"></a>Aplicação Web
Agora, vamos avançar para as real as web apps, que são mais complicadas. Clique na aplicação web do [variables('apiSiteName')] no contorno de JSON para realçar o código de JSON. Irá notar que coisas são obter muito mais interessantes. Para esta finalidade, posso falar sobre as funcionalidades de um de cada:

##### <a name="root-resource"></a>Recurso de raiz
A aplicação web depende dois recursos diferentes. Isto significa que o Azure Resource Manager irá criar a aplicação web apenas depois do plano de serviço de aplicações e a instância do SQL Server são criados.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Definições de aplicação
As definições de aplicações também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

No `properties` elemento para `config/appsettings`, tem duas definições de aplicação no formato `“<name>” : “<value>”`.

* `PROJECT`é um [definição KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que indica a implementação do Azure que projeto para utilizar uma solução de Visual Studio multi projeto. Posso irá mostrar-lhe mais tarde como controlo de origem está configurado, mas uma vez que o código de ToDoApp numa solução multi projeto Visual Studio, temos esta definição.
* `clientUrl`é simplesmente uma aplicação de código da aplicação a definição que utiliza.

##### <a name="connection-strings"></a>Cadeias de ligação
As cadeias de ligação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

No `properties` elemento para `config/connectionstrings`, cada cadeia de ligação também esteja definida como um par de valor de nome:, com o formato específico de `“<name>” : {“value”: “…”, “type”: “…”}`. Para o `type` elemento, os valores possíveis são `MySql`, `SQLServer`, `SQLAzure`, e `Custom`.

> [!TIP]
> Para obter uma lista definitiva dos tipos de cadeia de ligação, execute o seguinte comando no Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Controlo de código fonte
As definições de controlo de origem também são definidas como um recurso aninhado. O Azure Resource Manager utiliza este recurso para configurar a publicação contínua (consulte advertência no `IsManualIntegration` mais tarde) e também para iniciar a implementação do código da aplicação automaticamente durante o processamento do ficheiro JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`e `branch` deve ser pretty intuitiva e deve apontar para o repositório de Git e o nome do ramo para publicar a partir da. Novamente, estes são definidos pelos parâmetros de entrada. 

Tenha em atenção no `dependsOn` elemento que, além do recurso de aplicações web, `sourcecontrols/web` também depende `config/appsettings` e `config/connectionstrings`. Isto acontece porque depois `sourcecontrols/web` é configurado, o processo de implementação do Azure irá automaticamente tentar implementar, criar e iniciar o código da aplicação. Por conseguinte, inserir esta dependência ajuda-o a certificar-se de que a aplicação tem acesso para as definições de aplicação necessária e cadeias de ligação antes do código de aplicação é executado. 

> [!NOTE]
> Tenha em atenção que `IsManualIntegration` está definido como `true`. Esta propriedade é necessária neste tutorial, porque não é, na verdade, o proprietário do repositório do GitHub e, por conseguinte, não é, na verdade, conceder permissão para o Azure para configurar a publicação contínua de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (ou seja, push automáticas repositório atualizações para o Azure). Pode utilizar o valor predefinido `false` para o repositório especificado apenas se tiver configurado as credenciais do proprietário GitHub no [portal do Azure](https://portal.azure.com/) antes. Por outras palavras, se tiver configurado o controlo de origem para o GitHub ou o BitBucket para qualquer aplicação no [Portal do Azure](https://portal.azure.com/) anteriormente, utilizar o utilizador as credenciais, em seguida, o Azure irá lembrar as credenciais e utilizá-las sempre que implementar qualquer aplicação do GitHub ou o BitBucket no futuro. No entanto, se ainda não o fez já, implementação do modelo JSON irão falhar quando tenta configurar as definições de controlo de origem da aplicação web, porque este não é possível iniciar sessão no GitHub ou o BitBucket com credenciais do proprietário do repositório do Azure Resource Manager.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Comparar o modelo JSON com o grupo de recursos implementados
Aqui, pode percorrer os painéis de todos os web da aplicação no [Portal do Azure](https://portal.azure.com/), mas não existe outra ferramenta tal como se úteis, não é mais. Vá para o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta de pré-visualização, o que dá-lhe uma representação JSON de todos os grupos de recursos nas suas subscrições, tal como realmente existem no back-end do Azure. Também pode ver como a hierarquia JSON o grupo de recursos no Azure corresponde com a hierarquia no ficheiro de modelo que é utilizada para criá-la.

Por exemplo, quando devo para o [Explorador de recursos do Azure](https://resources.azure.com) ferramenta e expanda os nós no Explorador do, posso ver o grupo de recursos e os recursos de nível de raiz que são recolhidos sob os respetivos tipos de recursos correspondentes.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se lhe desagregar para uma aplicação web, deve conseguir ver detalhes de configuração de aplicação web semelhantes a baixo captura de ecrã:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Novamente, os recursos aninhados devem ter uma hierarquia muito semelhante às existentes no seu ficheiro de modelo JSON e deverá ver as definições da aplicação, cadeias de ligação, etc., corretamente apresentado no painel de JSON. A ausência de definições aqui poderá indicar um problema com o ficheiro JSON e pode ajudar a resolver o ficheiro de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implementar o modelo de grupo de recursos por si
O **implementar no Azure** botão é elevado, mas permite-lhe implementar o modelo de grupo de recursos no azuredeploy. JSON, apenas se já tiver instalada azuredeploy. JSON no GitHub. O SDK .NET da Azure também fornece ferramentas para implementar qualquer ficheiro de modelo JSON diretamente a partir do seu computador local. Para tal, siga os passos abaixo:

1. No Visual Studio, clique em **ficheiro** > **novo** > **projeto**.
2. Clique em **Visual c#** > **nuvem** > **grupo de recursos do Azure**, em seguida, clique em **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. No **selecionar modelo do Azure**, selecione **modelo em branco** e clique em **OK**.
4. Arraste azuredeploy. JSON para o **modelo** pasta do projeto novo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A partir do Explorador de soluções, abra o azuredeploy. JSON copiado.
6. Apenas com vista à, demonstração, vamos adicionar alguns recursos do Application Insight padrão para o nosso ficheiro JSON, clicando **adicionar recursos**. Se estiver interessado em implementar o ficheiro JSON apenas, avance para os passos de implementar.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecione **Application Insights para aplicações Web**, em seguida, certifique-se de uma aplicação de web e o plano de serviço de aplicações existente está selecionada e, em seguida, clique em **adicionar**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Agora poderá ver vários recursos novos que, consoante o recurso e o que faz, têm dependências no plano do App Service ou a aplicação web. Estes recursos não estão ativados pela respetiva definição existente e que vai alterar que.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. No contorno de JSON, clique em **appInsights dimensionamento automático** para realçar o código de JSON. Esta é a definição de dimensionamento para o seu plano de serviço de aplicações.
9. No código JSON realçado, localize o `location` e `enabled` propriedades e configure-os conforme mostrado abaixo.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. No contorno de JSON, clique em **CPUHigh appInsights** para realçar o código de JSON. Este é um alerta.
11. Localize o `location` e `isEnabled` propriedades e configure-os conforme mostrado abaixo. Faça o mesmo para os outros três alertas (bulbs roxa).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Agora, está pronto para implementar. Clique com o botão direito no projeto e selecione **implementar** > **nova implementação**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Inicie sessão na sua conta do Azure se ainda não o tiver feito.
14. Selecione um grupo de recursos existente na sua subscrição ou criar um novo, um, selecione **azuredeploy. JSON**e, em seguida, clique em **Editar parâmetros**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Agora poderá editar todos os parâmetros definidos no ficheiro de modelo numa tabela nice. Os parâmetros que definem as predefinições já terão os respetivos valores predefinidos e parâmetros que definem uma lista de valores permitidos serão apresentados como as dropdowns.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Preencha todos os parâmetros vazios e utilize o [endereço de repositório do GitHub para ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) no **repoUrl**. Em seguida, clique em **guardar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Dimensionamento automático é uma funcionalidade oferecida na **padrão** camada ou superiores e ao nível do plano de alertas são funcionalidades oferecidas no **básico** escalão ou superior, terá de definir o **sku** parâmetro para **padrão** ou **Premium** para ver todos os novos App Insights recursos leves cópias de segurança.
    > 
    > 
16. Clique em **implementar**. Se tiver selecionado **guardar palavras-passe**, a palavra-passe será guardada no ficheiro de parâmetro **em texto simples**. Caso contrário, irá ser-lhe pedido para a introduzir a palavra-passe de base de dados durante o processo de implementação.

Já está! Agora, apenas tem de ir para o [Portal do Azure](https://portal.azure.com/) e [Explorador de recursos do Azure](https://resources.azure.com) ferramenta para ver as definições de dimensionamento automático adicionadas ao seu JSON e novos alertas implementado a aplicação.

Os passos nesta secção principalmente feito o seguinte:

1. Preparar o ficheiro de modelo
2. Criar um ficheiro de parâmetros em com o ficheiro de modelo
3. Implementar o ficheiro de modelo com o ficheiro de parâmetros

O último passo facilmente é feito por um cmdlet do PowerShell. Para ver o que foi Visual Studio quando a aplicação implementada, abra Scripts\Deploy AzureResourceGroup.ps1. Há uma grande quantidade de código existe, mas apenas vou para realçar o código de pertinentes que tem de implementar o ficheiro de modelo com o ficheiro de parâmetros.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O cmdlet última, `New-AzureResourceGroup`, é o que, na verdade, executa a ação. Tudo isto deve demonstrar que, com a ajuda de ferramentas, é relativamente fácil de implementar a aplicação de nuvem de forma previsível. Sempre que executar o cmdlet no mesmo modelo com o mesmo ficheiro de parâmetros, que vai obter o mesmo resultado.

## <a name="summary"></a>Resumo
No DevOps, repetibilidade e à previsibilidade são chaves para qualquer implementação efetuada com êxito de uma aplicação de alta escala compostas por Micro-serviços. Neste tutorial, implementou uma aplicação de microsserviço dois para o Azure como um grupo de recursos única utilizando o modelo Azure Resource Manager. Hopefully,-lhe forneceu o conhecimento de que precisa para iniciar a conversão da aplicação no Azure a um modelo pode aprovisionar e implementá-lo de forma previsível. 

<a name="resources"></a>

## <a name="more-resources"></a>Mais recursos
* [Linguagem de modelo de Gestor de recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Criação de modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funções de modelo de Gestor de recursos do Azure](../azure-resource-manager/resource-group-template-functions.md)
* [Implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Resolução de problemas com implementações do grupo de recursos no Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

