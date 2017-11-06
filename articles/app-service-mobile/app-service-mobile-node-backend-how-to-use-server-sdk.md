---
title: Como trabalhar com o servidor de back-end Node.js SDK para Mobile Apps | Microsoft Docs
description: Saiba como trabalhar com o servidor de back-end Node.js SDK de Mobile Apps do Azure App Service.
services: app-service\mobile
documentationcenter: 
author: elamalani
manager: elamalani
editor: 
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: ab1a9dfa71c4b633392ef839bb848347fdd26431
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Como utilizar o Azure Mobile Apps Node.js SDK
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este artigo fornece informações detalhadas e exemplos de como trabalhar com um back-end do Node.js em Mobile Apps do Azure App Service.

## <a name="Introduction"></a>Introdução
Serviço de Mobile Apps para aplicações do Azure fornece a capacidade de adicionar um acesso de dados com otimização de mobile Web API a uma aplicação web.  O SDK de aplicações de Mobile do serviço de aplicações do Azure é fornecido para aplicações web do ASP.NET e Node.js.  O SDK fornece as seguintes operações:

* Operações da tabela (leitura, Insert, Update, Delete) para acesso a dados
* Operações de API de personalizado

Ambas as operações de fornecem para autenticação através de todos os fornecedores de identidade permitidos pelo App Service do Azure, incluindo os fornecedores de identidade de redes sociais, como o Facebook, Twitter, Google e Microsoft, bem como do Azure Active Directory para a identidade empresarial.

Pode encontrar exemplos para cada caso de utilização no [diretório de exemplos em GitHub].

## <a name="supported-platforms"></a>Plataformas suportadas
O Azure Mobile Apps nó SDK suporta a versão atual do LTS do nó e mais tarde.  A partir de escrita, a versão mais recente do LTS é v4.5.0 de nó.  Outras versões de nó podem funcionar, mas não são suportadas.

O SDK de nó de aplicações do Azure Mobile suporta dois controladores de base de dados - o controlador de nó mssql suporta SQL Azure e instâncias do SQL Server locais.  O controlador de sqlite3 suporta bases de dados do SQLite numa única instância apenas.

### <a name="howto-cmdline-basicapp"></a>Como: criar um Node.js básico back-end utilizando a linha de comandos
Cada back-end do Azure App Service Mobile aplicação Node.js é iniciada como uma aplicação ExpressJS.  ExpressJS é a arquitetura de serviço de web mais popular, disponível para Node.js.  Pode criar um basic [Express] aplicação da seguinte forma:

1. Numa janela do PowerShell ou comando, crie um diretório para o seu projeto.

        mkdir basicapp
2. Execute npm init ao inicializar a estrutura do pacote.

        cd basicapp
        npm init

    O comando de init npm pede-lhe um conjunto de perguntas para inicializar o projeto.  Ver o resultado de exemplo:

    ![A saída de init npm][0]
3. Instale as bibliotecas rápidas e mobile apps do azure do repositório npm.

        npm install --save express azure-mobile-apps
4. Crie um ficheiro app.js para implementar o servidor móvel básico.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Esta aplicação cria um end de WebAPI mobile-otimizada com um único ponto final (`/tables/TodoItem`) que fornece acesso não autenticado para um arquivo de dados SQL subjacente utilizando um esquema dinâmico.  É adequado para seguir do início rápido da biblioteca de cliente:

* [Guia de introdução do cliente do Android]
* [Guia de introdução do Apache Cordova cliente]
* [Guia de introdução do cliente do iOS]
* [Início rápido do cliente de loja Windows]
* [Guia de introdução do cliente do xamarin. IOS]
* [Guia de introdução do cliente do xamarin. Android]
* [Guia de introdução do cliente do xamarin. Forms]

Pode encontrar o código para esta aplicação básica no [basicapp exemplo no GitHub].

### <a name="howto-vs2015-basicapp"></a>Como: criar um back-end de nó com o Visual Studio 2015
Visual Studio 2015 requer uma extensão para desenvolver aplicações Node.js no IDE.  Para começar, instale o [Node.js ferramentas 1.1 para o Visual Studio].  Assim que estão instaladas as ferramentas de Node.js para o Visual Studio, crie uma aplicação de 4. x rápida:

1. Abra o **novo projeto** diálogo (de **ficheiro** > **novo** > **projeto …** ).
2. Expanda **modelos** > **JavaScript** > **Node.js**.
3. Selecione o **aplicação básicas do Azure Node.js Express 4**.
4. Preencha o nome do projeto.  Clique em *OK*.

    ![Novo projeto do Visual Studio 2015][1]
5. Clique com botão direito do **npm** nó e selecione **instalar novos pacotes de npm...** .
6. Terá de atualizar o catálogo de npm sobre como criar a sua primeira aplicação Node.js.  Clique em **atualizar** se necessário.
7. Introduza *-mobile apps do azure* na caixa de pesquisa.  Clique em de **-mobile apps do azure-2.0.0** do pacote, em seguida, clique em **Instalar pacote**.

    ![Instalar novos pacotes de npm][2]
8. Clique em **Fechar**.
9. Abra o *app.js* ficheiro para adicionar suporte para o SDK de aplicações móveis do Azure.  Na linha na parte inferior da biblioteca de at 6 necessitam de instruções, adicione o seguinte código:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Na aproximadamente linha 27 após as declarações de app.use, adicione o seguinte código:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Guarde o ficheiro.
10. Execute a aplicação localmente (a API é fornecida no http://localhost:3000) ou publicar no Azure.

### <a name="create-node-backend-portal"></a>Como: criar um back-end do Node.js utilizando o portal do Azure
Pode criar um direito de back-end da aplicação móvel no [portal do Azure]. Pode seguir os seguintes passos ou criar um cliente e servidor em conjunto, seguindo o [criar uma aplicação móvel](app-service-mobile-ios-get-started.md) tutorial. O tutorial contém uma versão simplificada destas instruções e é melhor para uma prova de projetos de conceito.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Volta a *começar* painel, em **criar uma API de tabela**, escolha **Node.js** como o **idioma de back-end**.
A caixa de verificação "**reconheço que esta ação substituirá todos os sites do conteúdo.**", em seguida, clique em **criar tabela TodoItem**.

### <a name="download-quickstart"></a>Como: Transferir o projeto de código do início rápido do Node.js back-end utilizando o Git
Quando cria um back-end de aplicação móvel de Node.js utilizando o portal **início rápido** painel, um projeto de Node.js é criada por si e implementada para o seu site. Pode adicionar tabelas e das APIs e editar ficheiros de código de back-end do Node.js no portal. Também pode utilizar várias ferramentas de implementação para transferir o projeto de back-end, de modo a que pode adicionar ou modificar as tabelas e das APIs e voltar a publicar o projeto. Para obter mais informações, consulte o [guia de implementação de serviço de aplicações do Azure]. o procedimento seguinte utiliza um repositório de Git para transferir o código de projeto de início rápido.

1. Instale o Git, se ainda não o tiver feito. Os passos necessários para instalar o Git variam entre sistemas operativos. Consulte [instalar o Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) para distribuições específicas do sistema operativo e a orientação de instalação.
2. Siga os passos no [ativar o repositório da aplicação de serviço de aplicações](../app-service/app-service-deploy-local-git.md#Step3) para ativar o repositório de Git para o seu site de back-end, efetuar uma nota do nome de utilizador de implementação e palavra-passe.
3. No painel de back-end da aplicação móvel, anote o **URL de clone de Git** definição.
4. Executar o `git clone` comando utilizando o Git clone URL, introduzir a palavra-passe quando necessário, como no exemplo seguinte:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Navegue para o diretório local, que, no exemplo anterior, é /todolist e tenha em atenção que os ficheiros de projeto foram transferidos. Localize o `todoitem.json` ficheiros o `/tables` diretório.  Este ficheiro define as permissões na tabela.  Também o `todoitem.js` ficheiro no mesmo diretório que define esse operação CRUD scripts para a tabela.
6. Depois de efetuar alterações aos ficheiros de projeto, execute os seguintes comandos para adicionar, consolidar, em seguida, carregue as alterações para o site:

        $ git commit -m "updated the table script"
        $ git push origin master

    Quando adicionar novos ficheiros ao projeto, primeiro terá de executar o `git add .` comando.

O site sejam publicados novamente sempre que um novo conjunto de consolidações é enviado para o site.

### <a name="howto-publish-to-azure"></a>Como: publicar o seu back-end do Node.js no Azure
Microsoft Azure fornece vários mecanismos para publicar o seu back-end do Azure App Service Mobile Apps Node.js no serviço do Azure.  Estes incluem a utilização de ferramentas de implementação integradas no Visual Studio, as ferramentas de linha de comandos e opções de implementação contínua, com base no controlo de origem.  Para obter mais informações sobre este tópico, consulte o [guia de implementação de serviço de aplicações do Azure].

App Service do Azure tem conselhos específico para a aplicação Node.js que deve consultar antes de implementar:

* Como [especifique a versão de nó]
* Como [utilizar módulos do nó]

### <a name="howto-enable-homepage"></a>Como: ativar uma página inicial para a sua aplicação
Muitas aplicações são uma combinação de web apps e móveis e a arquitetura de ExpressJS permite-lhe combinar as duas facetas.  Por vezes, no entanto, pode pretender implementar apenas uma interface móvel.  É útil fornecer uma página de destino para garantir que o serviço de aplicações está a funcionar.  Pode fornecer a sua própria home page ou ativar uma página inicial temporária.  Para ativar uma página inicial temporária, utilize o seguinte ao instanciar Mobile Apps do Azure:

    var mobile = azureMobileApps({ homePage: true });

Se apenas pretender que esta opção disponível quando desenvolver localmente, pode adicionar esta definição para sua `azureMobile.js` ficheiro.

## <a name="TableOperations"></a>Operações da tabela
O SDK de servidor de Node.js-mobile apps do azure fornece mecanismos para expor as tabelas de dados armazenadas na base de dados do Azure SQL Server como um end de WebAPI.  Operações de cinco são fornecidas.

| Operação | Descrição |
| --- | --- |
| GET /tables/*tablename* |Obter todos os registos na tabela |
| GET /tables/*tablename*/:id |Obter um registo específico na tabela |
| POST /tables/*tablename* |Criar um registo na tabela |
| PATCH /tables/*tablename*/:id |Atualizar um registo na tabela |
| Eliminar /tables/*tablename*/:id |Eliminar um registo na tabela |

Suporta a este end WebAPI [OData] e expande o esquema de tabela para suportar [sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Como: definir tabelas utilizando um esquema dinâmico
Antes de pode ser utilizada uma tabela, tem de ser definida.  As tabelas podem ser definidas com um esquema estático (em que o programador define as colunas no esquema) ou dinamicamente (onde o SDK controla o esquema com base nos pedidos de entrada). Além disso, o programador pode controlar aspetos específicos do end WebAPI adicionando código Javascript para a definição.

Como melhor prática, deve definir cada tabela num ficheiro Javascript no diretório de tabelas e utilizar o método de tables.import() para importar as tabelas.  Expandir a aplicação básica, deverá ser ajustado ficheiro app.js:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definir a tabela. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

As tabelas utilizam esquema dinâmica por predefinição.  Para desativar o esquema dinâmica globalmente, configure a definição de aplicação **MS_DynamicSchema** como falso no portal do Azure.

Pode encontrar um exemplo completo no [todo de exemplo no GitHub].

### <a name="howto-staticschema"></a>Como: definir tabelas utilizando um esquema estático
Pode definir explicitamente as colunas para expor através do end WebAPI.  O SDK de Node.js-mobile apps do azure adiciona automaticamente quaisquer colunas adicionais necessárias para a sincronização de dados offline para a lista que fornecer.  Por exemplo, as aplicações de cliente de início rápido necessitam de uma tabela com duas colunas: texto (uma cadeia) e (um valor boleano) ser concluído.  
A tabela pode ser definida numa tabela JavaScript ficheiro de definição do (localizado no diretório tabelas) da seguinte forma:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Se definir tabelas estaticamente, em seguida, também tem de chamar o método tables.initialize() para criar o esquema de base de dados no arranque.  O método tables.initialize() devolve um [promessa] para que o serviço web não serve pedidos antes da base de dados a ser inicializado.

### <a name="howto-sqlexpress-setup"></a>Como: utilizar o SQL Server Express como um arquivo de dados do desenvolvimento no seu computador local
O SDK do Azure Mobile Apps o AzureMobile aplicações nó fornece três opções para servir os dados a Box: SDK fornece três opções para servir os dados a Box:

* Utilize o **memória** controladores para fornecer um arquivo de exemplo não persistente
* Utilize o **mssql** controladores para fornecer um arquivo de dados SQL Server Express para desenvolvimento
* Utilize o **mssql** controladores para fornecer um arquivo de dados SQL Database do Azure de produção

O Azure Mobile Apps Node.js SDK utiliza o [mssql Node.js pacote] estabelecer e utilizar uma ligação ao SQL Server Express e a base de dados do SQL Server.  Este pacote requer que ative as ligações de TCP a instância do SQL Server Express.

> [!TIP]
> O controlador de memória não fornece um conjunto completo de instalações para fins de teste.  Se pretender testar o back-end localmente, recomendamos a utilização de um arquivo de dados SQL Server Express e o controlador de mssql.
>
>

1. Transfira e instale [Microsoft SQL Server 2014 Express].  Certifique-se de que instala o SQL Server 2014 Express com a edição de ferramentas.  A menos que explicitamente necessitar de suporte de 64 bits, a versão de 32 bits consome menos memória quando em execução.
2. Execute o Gestor de configuração do SQL Server 2014.

   1. Expanda o **configuração de rede do SQL Server** nó no menu da esquerda da árvore.
   2. Clique em **protocolos para o SQLEXPRESS**.
   3. Clique com botão direito **TCP/IP** e selecione **ativar**.  Clique em **OK** na caixa de diálogo de pop-up.
   4. Clique com botão direito **TCP/IP** e selecione **propriedades**.
   5. Clique em de **endereços IP** separador.
   6. Localizar o **IPAll** nós.  No **a porta TCP** campo, introduza **1433**.

          ![Configure SQL Express for TCP/IP][3]
   7. Clique em **OK**.  Clique em **OK** na caixa de diálogo de pop-up.
   8. Clique em **do SQL Server Services** no menu da esquerda da árvore.
   9. Clique com botão direito **SQL Server (SQLEXPRESS)** e selecione **reiniciar**
   10. Feche o Gestor de configuração do SQL Server 2014.
3. Executar o SQL Server 2014 Management Studio e ligue à sua instância do SQL Express local

   1. A instância no Explorador de objetos com o botão direito e selecione **propriedades**
   2. Selecione o **segurança** página.
   3. Certifique-se a **modo do SQL Server e a autenticação do Windows** é seleccionada
   4. Clique em **OK**

          ![Configure SQL Express Authentication][4]
   5. Expanda **segurança** > **inícios de sessão** no Explorador de objetos
   6. Clique com botão direito **inícios de sessão** e selecione **novo início de sessão...**
   7. Introduza um nome de início de sessão.  Selecione **Autenticação do SQL Server**.  Introduza uma palavra-passe, em seguida, introduza a mesma palavra-passe no **Confirmar palavra-passe**.  A palavra-passe tem de cumprir requisitos de complexidade do Windows.
   8. Clique em **OK**

          ![Add a new user to SQL Express][5]
   9. O novo início de sessão com o botão direito e selecione **propriedades**
   10. Selecione o **funções de servidor** página
   11. A caixa de verificação junto a **dbcreator** função de servidor
   12. Clique em **OK**
   13. Feche o SQL Server 2015 Management Studio

Certifique-se de que o registo de utilizador e palavra-passe que selecionou.  Terá de atribuir funções de servidor adicionais ou permissões dependendo dos requisitos de base de dados específica.

Leituras de aplicação Node.js a **SQLCONNSTR_MS_TableConnectionString** variável de ambiente para a cadeia de ligação para esta base de dados.  Pode definir esta variável no seu ambiente.  Por exemplo, pode utilizar o PowerShell para definir esta variável de ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Aceder à base de dados através de uma ligação de TCP/IP e fornecer um nome de utilizador e palavra-passe para a ligação.

### <a name="howto-config-localdev"></a>Como: configurar o seu projeto de desenvolvimento local
Mobile Apps do Azure lê um ficheiro de JavaScript chamado *azureMobile.js* do sistema de ficheiros local.  Não utilize este ficheiro para configurar o SDK de aplicações móveis do Azure em produção - utilize as definições de aplicações dentro de [portal do Azure] em vez disso.  O *azureMobile.js* ficheiro deve exportar de um objeto de configuração.  As definições mais comuns são:

* Definições de base de dados
* Definições de registo de diagnóstico
* Definições CORS alternativo

Um exemplo *azureMobile.js* segue ficheiro implementar as definições de base de dados anterior:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Recomendamos que adicione *azureMobile.js* para sua *. gitignore* ficheiro (ou outro controlo de código fonte ignorar ficheiro) para impedir que as palavras-passe a ser armazenados na nuvem.  Configure sempre as definições de produção nas definições de aplicação dentro de [portal do Azure].

### <a name="howto-appsettings"></a>Como: Configurar definições da aplicação para a sua aplicação móvel
A maioria das definições no *azureMobile.js* ficheiro ter uma definição de aplicação equivalente a [portal do Azure].  Utilize a lista seguinte para configurar a sua aplicação nas definições de aplicação:

| Definição de aplicação | *azureMobile.js* definição | Descrição | Valores válidos |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |nome |O nome da aplicação |Cadeia |
| **MS_MobileLoggingLevel** |Logging.level |Nível de registo mínimo de mensagens em fila para iniciar sessão |erro, aviso, informações, verbosa, depuração, silly |
| **MS_DebugMode** |Depuração |Ativar ou desativar o modo de depuração |TRUE, false |
| **MS_TableSchema** |data.Schema |Nome de esquema predefinido para tabelas SQL |cadeia (predefinição: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Ativar ou desativar o modo de depuração |TRUE, false |
| **MS_DisableVersionHeader** |versão (defina a indefinido) |Desativa o cabeçalho X-ZUMO--versão do servidor |TRUE, false |
| **MS_SkipVersionCheck** |skipversioncheck |Desativa a verificação da versão do cliente de API |TRUE, false |

Para definir uma definição de aplicação:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome da sua aplicação móvel.
3. Abre o painel de definições por predefinição. Se não, clique **definições**.
4. Clique em **definições da aplicação** no menu geral.
5. Desloque-se para a secção de definições da aplicação.
6. Se a aplicação definir já existir, clique no valor da definição de aplicação para editar o valor.
7. Se a definição de aplicação não existe, introduza a definição de aplicação na caixa de chave e o valor na caixa de valor.
8. Assim que estiver concluída, clique em **guardar**.

Alterar a maioria das definições de aplicação requer um reinício do serviço.

### <a name="howto-use-sqlazure"></a>Como: utilizar a base de dados de SQL como arquivo de dados de produção
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Utilizar a SQL Database do Azure como um arquivo de dados é idêntico em todos os tipos de aplicações do App Service do Azure. Se ainda não o tiver o feito, siga estes passos para criar um back-end de aplicação móvel.

1. Inicie sessão no [portal do Azure].
2. Na parte superior esquerdo da janela, clique o **+ novo** botão > **Web + móvel** > **aplicação móvel**, em seguida, forneça um nome para o back-end da aplicação móvel.
3. No **grupo de recursos** box, introduza o mesmo nome que a sua aplicação.
4. O plano de serviço de aplicações predefinido foi selecionado.  Se pretender alterar o seu plano de serviço de aplicações, pode fazê-clicando o plano de serviço de aplicações > **+ criar novos**.  Forneça um nome do novo plano de serviço de aplicações e selecione uma localização adequada.  Clique no escalão de preço e selecione um escalão de preço adequado para o serviço. Selecione **ver todos os** para ver mais preços opções, tais como **livres** e **partilhados**.  Assim que tiver selecionado o escalão de preço, clique em de **selecione** botão.  Volta a **plano do App Service** painel, clique em **OK**.
5. Clique em **Criar**. Aprovisionamento de um back-end de aplicação móvel, pode demorar alguns minutos.  Assim que o back-end de aplicação móvel é aprovisionado, o portal abre o **definições** painel para o back-end de aplicação móvel.

Assim que for criado o back-end de aplicação móvel, pode optar por ligar uma base de dados existente do SQL Server ao back-end da aplicação móvel ou criar uma nova base de dados do SQL Server.  Nesta secção, iremos criar uma base de dados do SQL Server.

> [!NOTE]
> Se já tiver uma base de dados na mesma localização que o back-end de aplicação móvel, em vez disso, pode escolher **utilizar uma base de dados existente** e, em seguida, selecione a base de dados. A utilização de uma base de dados numa localização diferente não é recomendada devido a latências.
>
>

1. Na nova aplicação móvel back-end, clique em **definições** > **aplicação móvel** > **dados** > **+ adicionar**.
2. No **adicionar ligação de dados** painel, clique em **base de dados SQL - configurar as definições necessárias** > **criar uma nova base de dados**.  Introduza o nome da nova base de dados a **nome** campo.
3. Clique em **servidor**.  No **novo servidor** painel, introduza um nome de servidor exclusivo no **nome do servidor** campo e fornecer um adequado **início de sessão de administrador de servidor** e **palavra-passe**.  Certifique-se **permitir serviços do azure ao servidor de acesso** está marcada.  Clique em **OK**.

    ![Criar uma base de dados SQL do Azure][6]
4. No **nova base de dados** painel, clique em **OK**.
5. Reverter o **adicionar ligação de dados** painel, selecione **cadeia de ligação**, introduza o início de sessão e palavra-passe que forneceu ao criar a base de dados.  Se utilizar uma base de dados existente, forneça as credenciais de início de sessão para essa base de dados.  Uma vez introduzidas, clique em **OK**.
6. Reverter o **adicionar ligação de dados** painel novamente, clique em **OK** para criar a base de dados.

<!--- END OF ALTERNATE INCLUDE -->

A criação da base de dados pode demorar alguns minutos.  Utilize o **notificações** área para monitorizar o progresso da implementação.  Não avança até que a base de dados foi implementada com êxito.  Depois de implementada com êxito, é criada uma cadeia de ligação para a instância de base de dados SQL no back-end móvel as definições de aplicação.  Pode ver esta definição de aplicação no **definições** > **definições da aplicação** > **cadeias de ligação**.

### <a name="howto-tables-auth"></a>Como: exigir autenticação para aceder a tabelas
Se pretender utilizar a autenticação do serviço de aplicações com o ponto final de tabelas, tem de configurar autenticação do serviço de aplicações no [portal do Azure] primeiro.  Para obter mais detalhes sobre como configurar a autenticação num serviço de aplicações do Azure, reveja o guia de configuração para o fornecedor de identidade que pretende utilizar:

* [Como configurar a autenticação do Active Directory do Azure]
* [Como configurar a autenticação do Facebook]
* [Como configurar a autenticação do Google]
* [Como configurar Authentication do Microsoft]
* [Como configurar a autenticação do Twitter]

Cada tabela tem uma propriedade de acesso que pode ser utilizada para controlar o acesso à tabela.  O exemplo seguinte mostra uma tabela estaticamente definida com a autenticação necessária.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

A propriedade de acesso pode demorar um de três valores

* *anónimo* indica que a aplicação de cliente tem permissão para ler os dados sem autenticação
* *autenticado* indica que a aplicação cliente terá de enviar um token de autenticação válido com o pedido
* *desativado* indica que esta tabela está atualmente desativada

Se a propriedade de acesso não está definida, é permitido o acesso não autenticado.

### <a name="howto-tables-getidentity"></a>Como: utilizar afirmações de autenticação com as tabelas
Pode configurar várias afirmações que são pedidas quando configurar a autenticação.  Estas afirmações não estão normalmente disponíveis através do `context.user` objeto.  No entanto, estes podem ser obtidos utilizando o `context.user.getIdentity()` método.  O `getIdentity()` método devolve uma promessa que é resolvido para um objeto.  O objeto é codificado pelo método de autenticação (facebook, google, twitter, microsoftaccount ou aad).

Por exemplo, se configurar a autenticação da Microsoft Account e pedido que os endereços de e-mail de afirmação, pode adicionar o endereço de e-mail para o registo com o controlador da tabela seguinte:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Para ver quais as afirmações que estão disponíveis, utilize um web browser para ver o `/.auth/me` ponto final do seu site.

### <a name="howto-tables-disabled"></a>Como: desativar o acesso às operações de tabela específica
Para além de apresentação na tabela, a propriedade de acesso pode ser utilizada para controlar operações individuais.  Existem quatro operações:

* *ler* operação obter RESTful na tabela
* *Inserir* é a operação RESTful POST na tabela
* *Atualizar* é a operação de aplicar o PATCH RESTful na tabela
* *eliminar* operação eliminar RESTful na tabela

Por exemplo, pode ser útil fornecer uma tabela só de leitura não autenticada:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Como: ajustar a consulta que é utilizada com operações de tabela
Um requisito para operações de tabela comum consiste em fornecer uma vista restrita dos dados.  Por exemplo, pode fornecer uma tabela que está marcada com o ID de utilizador autenticado que só pode ler ou atualizar os seus próprios registos.  A definição da tabela seguinte fornece esta funcionalidade:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

As operações que normalmente executassem uma consulta tem uma propriedade de consulta que pode ajustar com um onde cláusula. A propriedade de consulta é um [QueryJS] objeto que é utilizado para converter uma consulta de OData para algo que possa processar o back-end de dados.  Para cenários de igualdade simples (como a anterior), pode ser utilizado um mapa. Também pode adicionar cláusulas específicas do SQL Server:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Como: configurar a eliminação de forma recuperável numa tabela
Eliminação de forma recuperável, na verdade, não a eliminar os registos.  Em vez disso, marca-los como eliminados na base de dados ao definir a coluna eliminada como true.  O SDK de aplicações do Azure Mobile remove automaticamente registos eliminados de forma recuperável de resultados, a menos que o SDK do cliente de Mobile utiliza IncludeDeleted().  Para configurar uma tabela para eliminação de forma recuperável, defina o `softDelete` propriedade no ficheiro de definição de tabela:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Deve estabelecer um mecanismo para remover registos - a partir de uma aplicação de cliente, através de um WebJob, função do Azure ou através de uma API personalizada.

### <a name="howto-tables-seeding"></a>Como: a base de dados com dados de Seed
Ao criar uma nova aplicação, poderá pretender efetuar o seeding uma tabela com dados.  Isto pode ser feito no ficheiro de JavaScript de definição de tabela da seguinte forma:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Só é efetuado a seeding dos dados quando a tabela é criada pelo SDK do Azure Mobile Apps.  Se a tabela já existir na base de dados, não existem dados é inseridos na tabela.  Se o esquema dinâmica estiver ativada, em seguida, o esquema é inferido a partir dos dados seeded.

Recomendamos que pode chama explicitamente o `tables.initialize()` método para criar a tabela quando inicia o serviço em execução.

### <a name="Swagger"></a>Como: Ativar o suporte de Swagger
Serviço Mobile Apps para aplicações do Azure vem com incorporado [Swagger] suportar.  Para ativar o suporte do Swagger, instale primeiro a IU do swagger como uma dependência:

    npm install --save swagger-ui

Depois de instalado, pode ativar o suporte de Swagger no construtor Mobile Apps do Azure:

    var mobile = azureMobileApps({ swagger: true });

É provavelmente apenas pretende ativar o suporte de Swagger nas edições de desenvolvimento.  Pode fazê-lo utilizando o `NODE_ENV` definição de aplicação:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

O ponto final de swagger está localizado em http://*yoursite*.azurewebsites.net/swagger.  Pode aceder a IU do Swagger através de `/swagger/ui` ponto final.  Se optar por exigir a autenticação na sua aplicação completa, Swagger produz um erro.  Para obter os melhores resultados, optar por permitir pedidos não autenticados através na autenticação do serviço de aplicações do Azure / definições de autorização, controlar, em seguida, a autenticação utilizando o `table.access` propriedade.

Também pode adicionar a opção de Swagger para sua `azureMobile.js` ficheiro se pretender apenas suporte de Swagger quando desenvolver localmente.

## <a name="a-namepushpush-notifications"></a><a name="push">Notificações Push
As Mobile Apps integra-se com os Hubs de notificação do Azure que lhe permite enviar notificações push direcionadas para milhões de dispositivos em todas as plataformas principais. Ao utilizar os Notification Hubs, pode enviar notificações push para o iOS, dispositivos Android e Windows. Para saber mais sobre tudo o que pode fazer com os Notification Hubs, consulte o artigo [descrição geral dos Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Como: enviar notificações push
O código seguinte mostra como utilizar o objeto de push para enviar uma notificação push de difusão para dispositivos iOS:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Ao criar um registo de push de modelo do cliente, em vez disso, pode enviar uma mensagem de push de modelo para dispositivos em todas as plataformas suportadas. O código seguinte mostra como enviar uma notificação de modelo:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Como: enviar notificações de push para um utilizador autenticado utilizando as etiquetas
Quando um utilizador autenticado regista para notificações push, uma tag de ID de utilizador é adicionada automaticamente para o registo. Ao utilizar esta etiqueta, pode enviar notificações push para todos os dispositivos registados por um utilizador específico. O código seguinte obtém o SID do utilizador que efetua o pedido e envia uma notificação push de modelo para cada registo de dispositivos para que o utilizador:

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Quando a registar notificações push a partir de um cliente autenticado, certifique-se de que a autenticação é concluída antes de repetir o registo.

## <a name="CustomAPI"></a>APIs personalizadas
### <a name="howto-customapi-basic"></a>Como: definir uma API personalizada
Para além de acesso a dados API através do ponto final /tables, Mobile Apps do Azure pode fornecer a cobertura de API personalizada.  Personalizada APIs são definidos de forma semelhante para as definições de tabela o acesso e as mesmas instalações, incluindo a autenticação.

Se pretender utilizar a autenticação do serviço de aplicação com uma API personalizada, tem de configurar autenticação do serviço de aplicações no [portal do Azure] primeiro.  Para obter mais detalhes sobre como configurar a autenticação num serviço de aplicações do Azure, reveja o guia de configuração para o fornecedor de identidade que pretende utilizar:

* [Como configurar a autenticação do Active Directory do Azure]
* [Como configurar a autenticação do Facebook]
* [Como configurar a autenticação do Google]
* [Como configurar Authentication do Microsoft]
* [Como configurar a autenticação do Twitter]

APIs personalizadas definidas muito da mesma forma como a API de tabelas.

1. Criar um **api** diretório
2. Criar um ficheiro de JavaScript de definição de API no **api** diretório.
3. Utilize o método de importação para importar o **api** diretório.

Segue-se a definição da api de protótipo de amostra de aplicação básico que utilizámos anteriormente.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Vejamos um exemplo de API que devolve a data de servidor utilizando o *Date.now()* método.  Segue-se o ficheiro api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Cada parâmetro é um dos verbos padrão RESTful - GET, POST, PATCH ou eliminar.  O método é uma norma [ExpressJS Middleware] função que envia a saída obrigatória.

### <a name="howto-customapi-auth"></a>Como: exigir autenticação para aceder a uma API personalizada
SDK de aplicações móveis do Azure implementa autenticação da mesma forma para o ponto final de tabelas e APIs personalizadas.  Para adicionar autenticação à API desenvolvida na secção anterior, adicione um **acesso** propriedade:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Também pode especificar a autenticação em operações específicas:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

O mesmo token que é utilizado para o ponto final de tabelas tem de ser utilizado para APIs personalizadas que requerem autenticação.

### <a name="howto-customapi-auth"></a>Como: processar carregamentos de ficheiros grandes
SDK de aplicações móveis do Azure utiliza o [middleware de corpo parser](https://github.com/expressjs/body-parser) para aceitar e descodificar o conteúdo do corpo na sua submissão.  Pode configurar previamente parser de corpo para aceitar carregamentos de ficheiros maiores:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

O ficheiro é codificado antes da transmissão de base-64.  Isto aumenta o tamanho do carregamento real (e, por conseguinte, o tamanho que tem em conta).

### <a name="howto-customapi-sql"></a>Como: executar instruções SQL personalizadas
O SDK de aplicações móveis do Azure permite o acesso no contexto através do objeto de pedido, permitindo-lhe executar facilmente instruções parametrizadas do SQL Server para o fornecedor de dados definido:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Depuração, tabelas simples e fácil APIs
### <a name="howto-diagnostic-logs"></a>Como: depurar, diagnosticar e resolver problemas de aplicações móveis do Azure
O App Service do Azure fornece várias depuração e resolução de problemas de técnicas para as aplicações Node.js.
Consulte os artigos seguintes para começar a utilizar no seu back-end do Node.js Mobile de resolução de problemas:

* [Monitorizar um serviço de aplicações do Azure]
* [Ativar o registo de diagnóstico no App Service do Azure]
* [Resolver problemas de um serviço de aplicações do Azure no Visual Studio]

Aplicações node.js têm acesso a uma vasta gama de ferramentas de registo de diagnóstico.  Internamente, utiliza o Azure Mobile Apps Node.js SDK [Winston] para o registo de diagnóstico.  O registo é ativado automaticamente ao ativar o modo de depuração ou definindo o **MS_DebugMode** definição de aplicação como verdadeiro no [portal do Azure]. Registos gerados aparecem nos registos de diagnóstico sobre o [portal do Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Como: trabalhar com tabelas fácil no portal do Azure
Tabelas fácil no portal do permitem-lhe criar e trabalhar com o direito de tabelas no portal. Pode carregar o conjunto de dados para tabelas fácil num formato CSV. Tenha em atenção que não é possível utilizar nomes de propriedades (no seu conjunto de dados CSV) que estão em conflito com nomes de propriedades do sistema de back-end do Mobile Apps do Azure. Os nomes de propriedades do sistema são:
* createdAt
* updatedAt
* eliminar
* Versão

Pode editar mesmo operações de tabela utilizando o Editor de serviço de aplicações. Ao clicar em **tabelas fácil** nas definições de site do back-end, pode adicionar, modificar ou eliminar uma tabela. Também pode ver os dados da tabela.

![Trabalhar com tabelas fácil](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Os comandos seguintes estão disponíveis na barra de comandos para uma tabela:

* **Alterar permissões** - modificar a permissão de leitura, inserir, atualizar e eliminar operações na tabela.
  As opções são permitir o acesso anónimo, para exigir autenticação ou desativar a todos os acessos à operação.
* **Editar o script** -o ficheiro de script para a tabela é aberto no Editor de serviço de aplicações.
* **Gerir o esquema** - adicionar, eliminar colunas ou alterar o índice de tabela.
* **Tabela limpar** -trunca uma tabela existente ser eliminar todas as linhas de dados, mas deixar o esquema inalterado.
* **Eliminar linhas** -eliminar linhas individuais dos dados.
* **Ver registos de transmissão em fluxo** -liga ao serviço de registo transmissão em fluxo para o seu site.

### <a name="work-easy-apis"></a>Como: trabalhar com APIs fácil no portal do Azure
APIs de fácil no portal do permitem-lhe criar e trabalhar com o direito APIs personalizado no portal. Pode editar os scripts de API utilizando o Editor de serviço de aplicações.

Ao clicar em **APIs fácil** nas definições de site do back-end, pode adicionar, modificar ou eliminar um ponto final de API personalizado.

![Trabalhar com APIs fácil](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

No portal, pode alterar as permissões de acesso para uma determinada ação de HTTP, edite o ficheiro de script de API no Editor de serviço de aplicação ou ver os registos de transmissão em fluxo.

### <a name="online-editor"></a>Como: editar o código no Editor de serviço de aplicações
O portal do Azure permite-lhe editar os ficheiros de script de back-end Node.js no App Service Editor sem ter de transferir o projeto para o seu computador local. Para editar ficheiros de script no online editor:

1. No painel de back-end da aplicação móvel, clique em **todas as definições** > ou **tabelas fácil** ou **APIs fácil**, uma tabela ou a API, clique em **Editar script**. O ficheiro de script é aberto no Editor de serviço de aplicações.

    ![Editor de serviço de aplicações](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
2. Efetue as alterações para o ficheiro de código no online editor. As alterações são guardadas automaticamente à medida que escreve.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Guia de introdução do cliente do Android]: app-service-mobile-android-get-started.md
[Guia de introdução do Apache Cordova cliente]: app-service-mobile-cordova-get-started.md
[Guia de introdução do cliente do iOS]: app-service-mobile-ios-get-started.md
[Guia de introdução do cliente do xamarin. IOS]: app-service-mobile-xamarin-ios-get-started.md
[Guia de introdução do cliente do xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
[Guia de introdução do cliente do xamarin. Forms]: app-service-mobile-xamarin-forms-get-started.md
[Início rápido do cliente de loja Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Como configurar a autenticação do Active Directory do Azure]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar a autenticação do Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar a autenticação do Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Como configurar Authentication do Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar a autenticação do Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[guia de implementação de serviço de aplicações do Azure]: ../app-service/app-service-deploy-local-git.md
[Monitorizar um serviço de aplicações do Azure]: ../app-service/web-sites-monitor.md
[Ativar o registo de diagnóstico no App Service do Azure]: ../app-service/web-sites-enable-diagnostic-log.md
[Resolver problemas de um serviço de aplicações do Azure no Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[especifique a versão de nó]: ../nodejs-specify-node-version-azure-apps.md
[utilizar módulos do nó]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[promessa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp exemplo no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo de exemplo no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de exemplos em GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js ferramentas 1.1 para o Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js pacote]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
