---
title: "Como publicar uma aplicação Web num serviço em nuvem do Azure a partir do Visual Studio e migrar | Microsoft Docs"
description: "Saiba como publicar a aplicação web num serviço em nuvem do Azure utilizando o Visual Studio e migrar."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: d5de4f5a7357cf5adde7773867356d47ad447bab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Como: migrar e publicar uma aplicação Web num serviço em nuvem do Azure a partir do Visual Studio
Para tirar partido dos serviços de alojamento e a escalabilidade do Azure, pode querer migrar e publicar a aplicação web num serviço em nuvem do Azure. Pode executar uma aplicação web no Azure com alterações mínimas à sua aplicação existente.

> [!NOTE]
> Este tópico é sobre a implementação de serviços em nuvem, não para web sites. Para obter informações sobre a implementação em web sites, consulte [implementar uma aplicação web no App Service do Azure](app-service/app-service-deploy-local-git.md).
>
>

Para obter uma lista de modelos específicos que são suportadas para o Visual c# e Visual Basic, consulte a secção **suportados modelos de projeto** mais adiante neste tópico.

Tem de ativar primeiro a aplicação web do Azure a partir do Visual Studio. A ilustração seguinte mostra os passos para publicar a aplicação web existente através da adição de um projeto do Azure para utilização na implementação da chaves. Este processo adiciona um projeto do Azure com a função da web necessária para a sua solução. Com base no tipo de projeto web que tem, as propriedades do projeto para assemblagens também são atualizadas se o pacote de serviço requer assemblagens adicionais para a implementação.

![Publicar uma aplicação Web para o Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

> [!NOTE]
> O **converter**, **converter para o projeto de serviço em nuvem do Azure** comando é apresentado apenas para o projeto web na sua solução. Por exemplo, o comando não está disponível para um projeto do Silverlight na sua solução.
> Quando criar um pacote de serviço ou publicar a aplicação no Azure, poderão ocorrer erros ou avisos. Estes avisos e erros podem ajudar a corrigir problemas antes de implementar no Azure. Por exemplo, poderá receber um aviso sobre uma assemblagem em falta. Para obter mais informações sobre como processe quaisquer avisos como erros, consulte [configurar um projeto de serviço em nuvem do Azure com o Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Se criar a sua aplicação, executá-la localmente, utilizando o emulador de computação ou publicá-lo no Azure, poderá ver o erro seguinte no **lista de erros** janela: **o caminho especificado, o nome de ficheiro ou ambos, são demasiado longos**. Este erro ocorre porque o comprimento do nome completamente qualificado de projeto do Azure é demasiado longo. O comprimento do nome do projeto, incluindo o caminho completo, não pode ser mais do que 146 carateres. Por exemplo, este é o nome de projeto completo, incluindo o caminho de ficheiro para um projeto do Azure que é criado para uma aplicação Silverlight: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Poderá ter de mover a sua solução para um diretório diferente que tenha um caminho mais curto para reduzir o comprimento do nome do projeto completamente qualificado.
>
>

Para migrar e publicar uma aplicação web no Azure a partir do Visual Studio, siga estes passos.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Ativar uma aplicação Web para a implementação para o Azure
### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Para ativar uma aplicação web para a implementação para o Azure
1. Para ativar a sua aplicação web para a implementação para o Azure, abra o menu de atalho para um projeto web na sua solução e escolha Adicionar projeto de implementação do Azure.

    As ações seguintes ocorrem:

   * Um projeto do Azure chamado `<name of the web project>.Azure` é adicionado à solução para a sua aplicação.
   * Uma função da web para o projeto web é adicionada a este projeto do Azure.
   * O **Cópia Local** propriedade está definida como true para todas as assemblagens que são necessárias para MVC MVC 2, MVC 3, 4 e as aplicações de empresas do Silverlight. Esta ação adiciona estas assemblagens para o pacote de serviço que é utilizado para a implementação.

   > [!IMPORTANT]
   > Se tiver outros assemblagens ou os ficheiros necessários para esta aplicação web, tem de definir manualmente as propriedades para estes ficheiros. Para obter informações sobre como definir estas propriedades, consulte a secção **incluem ficheiros no pacote de serviço** posteriormente neste artigo.
   >
   > [!NOTE]
   > Se já existir uma função da web para um projeto web específico num projeto do Azure na solução, **converter**, **converter para o projeto de serviço em nuvem do Azure** não é apresentada no menu de atalho para este projeto web.
   >
   >

   Se tiver vários projetos web na sua aplicação web e pretender criar funções da web para cada projeto web, tem de efetuar os passos neste procedimento para cada projeto web. Esta ação cria projetos do Azure separados para cada função da web. Cada projeto web pode ser publicado em separado. Em alternativa, pode adicionar manualmente outra função da web para um projeto do Azure existente na sua aplicação web. Para tal, abra o menu de atalho para o **funções** no projeto do Azure, selecione **adicionar**, em seguida, **projeto de função da Web na solução**, escolher o projeto para adicionar como uma função da web e, em seguida, escolha o **OK** botão.

## <a name="use-an-azure-sql-database-for-your-application"></a>Utilizar uma base de dados SQL do Azure para a sua aplicação
Se tiver uma cadeia de ligação para a sua aplicação web que utiliza uma base de dados do SQL Server que está no local, tem de alterar esta cadeia de ligação para utilizar uma instância da base de dados do SQL Server que aloja do Azure em vez disso.

> [!IMPORTANT]
> A subscrição tem de ativar a utilização de base de dados SQL. Se aceder à sua subscrição do [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), pode determinar os serviços que fornece a sua subscrição. As seguintes instruções aplicam-se para o lançamento [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Se estiver a utilizar o [portal do Azure](http://portal.microsoft.com), avance para o procedimento seguinte.
>
>

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Para utilizar uma instância de base de dados SQL da função de web para a cadeia de ligação
1. Para criar uma instância de base de dados do SQL Server no [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), siga os passos no seguinte artigo: [criar um servidor de base de dados do SQL Server](http://go.microsoft.com/fwlink/?LinkId=225109).

   > [!NOTE]
   > Quando configurar as regras de firewall para a instância da base de dados do SQL Server, tem de selecionar o **permitir que outros serviços do Azure aceder a este servidor** caixa de verificação.
   >
   >
2. Para criar uma instância da base de dados do SQL Server a utilizar para a cadeia de ligação, siga os passos na secção seguinte no seguinte artigo: [criar uma base de dados do SQL Server](http://go.microsoft.com/fwlink/?LinkId=225110).
3. Para copiar a cadeia de ligação do ADO.NET a utilizar para a cadeia de ligação, execute os seguintes passos no [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).  

   1. Escolha o **base de dados** botão e, em seguida, abra o nó para a subscrição que utilizou para criar a instância da base de dados do SQL Server.
   2. Para apresentar as instâncias da base de dados do SQL Server disponíveis, escolha o **bases de dados SQL** nós.
   3. Para apresentar as propriedades da base de dados, escolha a base de dados. O **propriedades** é apresentada a vista.

      > [!NOTE]
      > Se o **propriedades** vista não aparece, poderá ser necessário para abri-lo utilizando o separador.
      >
      >
   4. Para apresentar as cadeias de ligação, escolha o botão de reticências (…) junto de ver.

      O **cadeias de ligação** é apresentada a caixa de diálogo.
   5. Para copiar a cadeia de ligação do ADO.NET, realce o texto e escolha as chaves de Ctrl + C.
   6. Para fechar a caixa de diálogo, escolha o **fechar** botão.
4. Para substituir a cadeia de ligação no ficheiro Web. config para utilizar esta instância de base de dados SQL, abra o ficheiro Web. config, realce a entrada de cadeia de ligação existente e, em seguida, escolha as chaves de Ctrl + V. A cadeia de ligação do ADO.NET para a instância da base de dados do SQL Server substitui a cadeia de ligação existente.
5. Também tem de adicionar o parâmetro `MultipleActiveResultSets=True` à cadeia de ligação. A cadeia de ligação deve ter o seguinte formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```
6. (Opcional) É um método alternativo para alterar a cadeia de ligação diretamente no ficheiro Web. config adicionar uma secção dos ficheiros de transformação Web. config, dependendo da configuração de compilação que utilizar para criar o pacote de serviço. Abra o ficheiro Web.Debug.Config ou o ficheiro Web.Release.Config. Adicione a secção seguinte para este ficheiro:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```
7. Guarde o ficheiro que é modificado e voltar a publicar a aplicação.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Para utilizar uma instância da base de dados do SQL Server utilizando o portal clássico do Azure
1. No [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), escolha o nó bases de dados SQL.

   * Se for apresentada a instância da base de dados do SQL Server que pretende utilizar, escolha para abri-lo.
   * Se ainda não criou quaisquer instâncias, escolha a ligação adequada e, em seguida, crie uma instância.
2. Depois de abrir ou criar uma instância de base de dados, escolha o **cadeias de ligação** ligação.
3. Na parte inferior da página, escolha a ligação para configurar definições de firewall e aceite os valores predefinidos ou configurar os valores que precisa.
4. Copie a cadeia de ligação do ADO.NET, cole-o seu ficheiro Web. config através de cadeia de ligação antiga para a base de dados no local e não se esqueça de adicionar `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicar uma aplicação Web no Azure
### <a name="to-publish-a-web-application-to-azure"></a>Para publicar uma aplicação Web no Azure
1. Para testar a aplicação no desenvolvimento local ambiente utilizando o Azure emulador de computação, abra o menu de atalho para o projeto do Azure para a função da web e escolha **definir como projeto de arranque**. Em seguida, escolha **depurar**, **iniciar depuração** (teclado: **F5**).

    O **iniciar o ambiente de depuração de Azure** é aberta a caixa de diálogo e iniciar a aplicação no browser. Para obter detalhes específicos sobre como iniciar a cada tipo de aplicação web no emulador de computação, consulte a tabela nesta secção.
2. Para configurar os serviços para a sua aplicação publicar no Azure, tem de ter uma conta Microsoft e uma subscrição do Azure. Utilize os passos no seguinte tópico para configurar os serviços: [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
3. Para publicar a aplicação web no Azure, abra o menu de atalho para o projeto web e escolha **publicar para o Azure**.

    O **publicar aplicação Azure** é aberta a caixa de diálogo e Visual Studio inicia o processo de implementação. Para obter mais informações sobre como publicar a aplicação, consulte a secção **publicar uma aplicação do Azure a partir do Visual Studio** no [publicação de um serviço em nuvem com as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).

   > [!NOTE]
   > Também pode publicar a aplicação web do projeto do Azure. Para tal, abra o menu de atalho para o projeto do Azure e escolha **publicar**.
   >
   >
4. Para ver o progresso da implementação, pode ver o **registo de atividade do Azure** janela. Este registo é automaticamente apresentado quando inicia o processo de implementação. Pode expandir o item de linha no registo de atividade para mostrar as informações detalhadas, conforme mostrado na ilustração seguinte:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)
5. (Opcional) Para cancelar o processo de implementação, abra o menu de atalho para o item de linha no registo de atividade e escolha **Cancelar e remover**. Isto interrompe o processo de implementação e elimina o ambiente de implementação do Azure.

   > [!NOTE]
   > Para remover este ambiente de implementação, após esta ter sido implementada, tem de utilizar o [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   >
   >
6. (Opcional) Depois das instâncias da função tem sido iniciado, o Visual Studio mostra automaticamente o ambiente de implementação no **computação do Azure** no nó **Cloud Explorer** ou **Explorador de servidores**. Aqui pode ver o estado das instâncias de função individuais.

    A ilustração seguinte mostra as instâncias de função **Explorador de servidores** enquanto estiverem ainda no estado de inicialização:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)
7. Para aceder à sua aplicação após a implementação, selecione a seta junto a sua implementação quando o estado **concluído** aparece no **registo de atividade do Azure**. Isto apresenta o URL para a sua aplicação web no Azure. Consulte a tabela seguinte para obter detalhes sobre como iniciar um tipo específico de aplicação web do Azure.

    A tabela seguinte lista os detalhes sobre como iniciar a aplicações web específico a partir do Azure ou executar ou depurar uma aplicação web localmente utilizando o emulador de computação do Azure:

   | Tipo de aplicação Web | Executar/depuração localmente, utilizando o emulador de computação | em execução no Azure |
   | --- | --- | --- |
   | Aplicação ASP.NET Web |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Selecione a hiperligação de URL apresentada no **implementação** separador o **registo de atividade do Azure** para carregar a página de início no browser. |
   | Aplicação Web ASP.NET MVC 2 |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Selecione a hiperligação de URL apresentada no **implementação** separador o **registo de atividade do Azure** para carregar a página de início no browser. |
   | Aplicação Web ASP.NET MVC 3 |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Selecione a hiperligação de URL apresentada no **implementação** separador o **registo de atividade do Azure** para carregar a página de início no browser. |
   | Aplicação Web de MVC do ASP.NET 4 |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Selecione a hiperligação de URL apresentada no **implementação** separador o **registo de atividade do Azure** para carregar a página de início no browser. |
   | Aplicação Web de ASP.NET vazio |Tem de adicionar uma página. aspx na sua aplicação que configurou como a página de início para o projeto web. Em seguida, na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Se tiver uma página. aspx na sua aplicação, escolha a hiperligação de URL apresentada no **implementação** separador o **registo de atividade do Azure** e esta página é carregada no browser. Se tiver uma página. aspx diferente, terá de navegar para esta página específica utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx` |
   | Aplicação Silverlight |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Terá de navegar para a página específica para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx` |
   | Aplicação de negócio do Silverlight |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Terá de navegar para a página específica para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx` |
   | Aplicação de navegação do Silverlight |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Terá de navegar para a página específica para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx` |
   | Aplicação de serviço WCF |Tem de definir o ficheiro. svc como a página de início para o seu projeto de serviço de WCF. Em seguida, na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Terá de navegar para o ficheiro svc para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of service file>.svc` |
   | Aplicação do serviço de fluxo de trabalho WCF |Tem de definir o ficheiro. svc como a página de início para o seu projeto de serviço de WCF. Em seguida, na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Terá de navegar para o ficheiro svc para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of service file>.svc` |
   | Entidades dinâmico de ASP.NET |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Tem de atualizar a cadeia de ligação (consulte a secção seguinte). Também terá de navegar para a página específica para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx` |
   | ASP.NET dinâmico dados Linq para SQL Server |Na barra de menus, escolha **depurar**, **iniciar depuração** (teclado: escolher o **F5** chave.). |Tem de seguir os passos neste procedimento: utilizar uma SQL database do Azure para a sua aplicação (consulte a secção anterior deste tópico). Também terá de navegar para a página específica para a sua aplicação com o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx` |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Atualizar uma cadeia de ligação para entidades dinâmico de ASP.NET
### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Para atualizar uma cadeia de ligação para entidades dinâmico de ASP.NET
1. Para criar uma base de dados do SQL Azure que pode ser utilizado para uma aplicação web de entidades dinâmicos do ASP.NET, siga os passos no procedimento **utilizar uma SQL database do Azure para a sua aplicação** anteriormente neste tópico.
2. Adicione as tabelas e campos que sejam necessárias para esta base de dados a partir de [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
3. A cadeia de ligação para este tipo de aplicação tem o seguinte formato no ficheiro Web. config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Atualização do *connectionString* valor com a cadeia de ligação do ADO.NET para a base de dados do SQL Azure da seguinte forma:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```
4. Para guardar o ficheiro Web. config com as alterações que efetuou à cadeia de ligação, na barra de menus escolha **ficheiro**, **guardar o Web. config**.

## <a name="supported-project-templates"></a>Modelos de projeto suportados
Para publicar uma aplicação web no Azure, a aplicação tem de utilizar um dos modelos de projeto de c# ou Visual Basic que está listado na tabela abaixo.

| Grupo de modelo de projeto | Modelo de projeto |
| --- | --- |
| Web |Aplicação ASP.NET Web |
| Web |Aplicação Web ASP.NET MVC 2 |
| Web |Aplicação Web ASP.NET MVC 3 |
| Web |Aplicação Web de ASP.NET MVC4 |
| Web |Aplicação Web de ASP.NET vazio |
| Web |Aplicação de Web vazio de ASP.NET MVC 2 |
| Web |Aplicação Web de entidades de dados dinâmico de ASP.NET |
| Web |ASP.NET dinâmico dados Linq para aplicação de Web de SQL Server |
| Silverlight |Aplicação Silverlight |
| Silverlight |Aplicação de negócio do Silverlight |
| Silverlight |Aplicação de navegação do Silverlight |
| WCF |Aplicação de serviço WCF |
| WCF |Aplicação do serviço de fluxo de trabalho WCF |
| Fluxo de trabalho |Aplicação do serviço de fluxo de trabalho WCF |

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a publicação, consulte [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Consulte também [definição de cópia de segurança denominado credenciais de autenticação](vs-azure-tools-setting-up-named-authentication-credentials.md).
