---
title: "Como publicar uma aplicação Web num serviço em nuvem do Azure a partir do Visual Studio e migrar | Microsoft Docs"
description: "Saiba como migrar e publicar a aplicação web num serviço em nuvem do Azure utilizando o Visual Studio"
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
ms.date: 11/10/2017
ms.author: kraigb
ms.openlocfilehash: d5d41ab47c17a024900efc88ba0a006da63ab246
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Como: migrar e publicar uma aplicação Web num serviço em nuvem do Azure a partir do Visual Studio

Para tirar partido dos serviços de alojamento e capacidade de dimensionamento do Azure, pode querer migrar e implementar a sua aplicação web num serviço em nuvem do Azure. São necessárias apenas alterações mínimas. Este artigo abrange implementar apenas; nos serviços de nuvem para o App Service, consulte [implementar uma aplicação web no App Service do Azure](app-service/app-service-deploy-local-git.md).

> [!Important]
> Esta migração só é suportada para os projetos ASP.NET, Silverlight, WCF e WCF fluxo de trabalho específicos. Não é suportada para projetos de ASP.NET Core. Consulte [suportados modelos de projeto](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrar um projeto de serviços em nuvem

1. Clique com o botão direito no projeto de aplicação web e selecione **converter > converter para o projeto de serviço em nuvem do Microsoft Azure**. (Se este comando não é apresentada se já tiver um projeto de função da web na solução.)
1. Visual Studio cria um projeto de serviço em nuvem na solução que contém a função da web necessária. O nome deste projeto é o mesmo, como o projeto de aplicação com e o sufixo `.Azure`.
1. Visual Studio também define o **Cópia Local** propriedade como true para todas as assemblagens que são necessárias para MVC 2, MVC 3, MVC 4 e as aplicações de empresas do Silverlight. Esta propriedade adiciona estas assemblagens para o pacote de serviço que é utilizado para a implementação.

   > [!Important]
   > Se tiver outros assemblagens ou os ficheiros necessários para esta aplicação web, tem de definir manualmente as propriedades para estes ficheiros. Para obter informações sobre como definir estas propriedades, consulte [incluem ficheiros no pacote de serviço](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Erros e avisos

Quaisquer avisos ou erros ocorridos indicam problemas a corrigir antes de implementar no Azure, tais como as assemblagens em falta.

Se criar a sua aplicação, executá-la localmente, utilizando o emulador de computação ou publicá-lo no Azure, poderá ver o erro: "o caminho especificado, o nome de ficheiro ou ambos são demasiado longos." Este erro indica que o comprimento do nome completamente qualificado de projeto do Azure excede 146 carateres. Para corrigir o problema, mova a sua solução numa pasta diferente com um caminho mais curto.

Para obter mais informações sobre como processe quaisquer avisos como erros, consulte [configurar um projeto de serviço em nuvem do Azure com o Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Teste a migração localmente

1. No Visual Studio **Explorador de soluções**, o projeto de serviço em nuvem foi adicionada com o botão direito e selecione **definir como projeto de arranque**.
1. Selecione **depurar > Iniciar depuração** (F5) para iniciar o ambiente de depuração do Azure. Este ambiente especificamente fornece emulação vários serviços do Azure.

### <a name="use-an-azure-sql-database-for-your-application"></a>Utilizar uma base de dados do SQL do Azure para a sua aplicação

Se tiver uma cadeia de ligação para a sua aplicação web que utiliza uma base de dados do SQL Server no local, terá de migrar a base de dados para a SQL Database do Azure em vez disso e atualizar a cadeia de ligação. Para obter orientações sobre a este processo, consulte os tópicos seguintes:

- [Migração de base de dados do SQL Server para a base de dados do SQL Server na nuvem](sql-database/sql-database-cloud-migrate.md)
- [Utilize o .NET (c#) com o Visual Studio para ligar e consultar e a SQL database do Azure](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Publicar a aplicação no serviço de nuvem do Azure

1. Criar nuvem necessária contas de serviço e de armazenamento na sua subscrição do Azure, tal como descrito no [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. No Visual Studio, clique com o botão direito no projeto de aplicação e selecione **publicar para o Microsoft Azure...**  (que é diferente do comando "Publicar...".).
1. No **publicar aplicação Azure** que aparece, inicie sessão com a conta com a sua subscrição do Azure e selecione **seguinte >**.
1. No **definições > definições comuns** separador, selecione o serviço de nuvem de destino do **serviço em nuvem** na lista pendente, juntamente com o seu ambiente que escolheu e configurações. 
1. No **definições > Definições Avançadas**, selecionar a conta de armazenamento a utilizar, em seguida, selecione **seguinte >**.
1. No **diagnóstico**, escolha se pretende enviar informações para o Application Insights.
1. Selecione **seguinte >** para ver um resumo, em seguida, selecione **publicar** para iniciar a implementação.
1. O Visual Studio abre uma janela de registo de atividade onde pode controlar o progresso:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcional) Para cancelar o processo de implementação, clique com o botão direito do item de linha no registo de atividade e escolha **Cancelar e remover**. Este comando interrompe o processo de implementação e elimina o ambiente de implementação do Azure. Nota: para remover este ambiente de implementação, após esta ter sido implementada, tem de utilizar o [portal do Azure](https://portal.azure.com).
1. (Opcional) Depois das instâncias da função tem sido iniciado, o Visual Studio mostra automaticamente o ambiente de implementação no **Explorador de servidores > Serviços Cloud** nós. Aqui pode ver o estado das instâncias de função individuais.
1. Para aceder à sua aplicação após a implementação, selecione a seta junto a sua implementação quando o estado **concluído** aparece no **registo de atividade do Azure** juntamente com o URL. Consulte a tabela seguinte para obter detalhes sobre como iniciar um tipo específico de aplicação web do Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Utilizar o emulador de computação e iniciar a aplicação no Azure

Todos os tipos de aplicação podem ser iniciados num browser ligado ao depurador do Visual Studio, selecionando **depurar > Iniciar depuração** (F5). Com um projeto de aplicação Web em branco do ASP.NET, tem de adicionar primeiro um `.aspx` página na sua aplicação e defini-lo como a página de início para o projeto web.

A tabela seguinte fornece detalhes sobre o início da aplicação no Azure:

   | Tipo de aplicação Web | em execução no Azure |
   | --- | --- | --- |
   | Aplicação ASP.NET Web<br/>(incluindo MVC 2, MVC 3, MVC 4) | Selecione o URL no **implementação** separador o **registo de atividade do Azure**. |
   | Aplicação Web de ASP.NET vazio | Se tiver uma predefinição `.aspx` página na sua aplicação, selecione o URL no **implementação** separador o **registo de atividade do Azure**. Para navegar para uma página diferente, introduza um URL do formulário seguinte num browser:`<deployment_url>/<page_name>.aspx` |
   | Aplicação Silverlight<br/>Aplicação de negócio do Silverlight<br/>Aplicação de navegação do Silverlight | Navegue para a página específica para a sua aplicação utilizando o seguinte formato de URL:`<deployment_url>/<page_name>.aspx` |
    Aplicação de serviço WCF<br/>Aplicação do serviço de fluxo de trabalho WCF | Definir o `.svc` ficheiros como a página de início para o seu projeto de serviço de WCF. Em seguida, navegue para`<deployment_url>/<service_file>.svc` |
   | Entidades dinâmico de ASP.NET<br/>ASP.NET dinâmico dados Linq para SQL Server | Atualize a cadeia de ligação conforme descrito na secção seguinte. Em seguida, navegue para `<deployment_url>/<page_name>.aspx`. Para Linq para SQL Server, tem de utilizar uma base de dados SQL do Azure. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Atualizar uma cadeia de ligação para entidades dinâmico de ASP.NET

1. Crie uma SQL database do Azure para uma aplicação web de entidades dinâmicos do ASP.NET, como descrito anteriormente (#use-an-azuresql-database-for-your-application).
1. Adicione as tabelas e campos que sejam necessárias para esta base de dados do portal do Azure.
1. Especifique uma cadeia de ligação no `web.config` com o seguinte formato de ficheiro e guarde o ficheiro:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Atualização do *connectionString* valor com a cadeia de ligação do ADO.NET para a base de dados do SQL Azure da seguinte forma:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Modelos de projeto suportados

As aplicações que podem ser migradas e publicadas nos serviços em nuvem tem de utilizar um dos modelos na tabela abaixo. Não é suportado o ASP.NET Core.

| Grupo de modelo | Modelo de projeto |
| --- | --- |
| Web | Aplicação ASP.NET Web (.NET Framework) |
| Web | Aplicação Web ASP.NET MVC 2 |
| Web | Aplicação Web ASP.NET MVC 3 |
| Web | Aplicação Web de ASP.NET MVC4 |
| Web | Aplicação Web de ASP.NET vazio (ou Site) |
| Web | Aplicação de Web vazio de ASP.NET MVC 2 |
| Web | Aplicação Web de entidades de dados dinâmico de ASP.NET |
| Web | ASP.NET dinâmico dados Linq para aplicação de Web de SQL Server |
| Silverlight | Aplicação Silverlight |
| Silverlight | Aplicação de negócio do Silverlight |
| Silverlight | Aplicação de navegação do Silverlight |
| WCF | Aplicação de serviço WCF |
| WCF | Aplicação do serviço de fluxo de trabalho WCF |
| Fluxo de trabalho | Aplicação do serviço de fluxo de trabalho WCF |

## <a name="next-steps"></a>Passos seguintes

- [Preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [A configurar com o nome de credenciais de autenticação](vs-azure-tools-setting-up-named-authentication-credentials.md).
