<properties 
    pageTitle="O Django e o MySQL no Azure com Ferramentas do Python 2.2 para Visual Studio" 
    description="Saiba como utilizar as Ferramentas do Python para Visual Studio para criar uma aplicação Web Django que armazena dados numa instância de base de dados MySQL e implementá-la nas Web Apps do App Service do Azure." 
    services="app-service\web" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python"
    ms.topic="get-started-article" 
    ms.date="07/07/2016"
    ms.author="huvalo"/>

# O Django e o MySQL no Azure com Ferramentas do Python 2.2 para Visual Studio 

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Neste tutorial, irá utilizar as [Ferramentas do Python para Visual Studio] (PTVS) para criar uma aplicação Web de inquérito simples utilizando um dos modelos de exemplo das PTVS. Irá aprender como utilizar um serviço de MySQL alojado no Azure, como configurar a aplicação Web para utilizar o MySQL e como publicar a aplicação Web para as [Web Apps do App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

> [AZURE.NOTE] As informações contidas neste tutorial também estão disponíveis no vídeo seguinte:
> 
> [PTVS 2.1: aplicação Django com MySQL – ][vídeo]

Consulte o [Centro para Programadores do Python] para ver mais artigos que abrangem o desenvolvimento das Aplicações Web do Serviço de Aplicações do Azure com a PTVS utilizando as estruturas Web Bottle, Flask e Django, com os serviços Base de Dados SQL, Armazenamento de Tabelas do Azure e MySQL. Embora este artigo incida no App Service, os passos são semelhantes quando desenvolver os [Cloud Services do Azure].

## Pré-requisitos

 - Visual Studio 2015
 - [Python 2.7 de 32 bits] ou [Python 3.4 de 32 bits]
 - [Ferramentas do Python 2.2 para Visual Studio]
 - [Ferramentas do Python 2.2 para VSIX de Exemplo do Visual Studio]
 - [Ferramentas do Azure SDK para VS 2015]
 - Django 1.9 ou posterior

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [AZURE.NOTE] Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito nem compromissos necessários.

## Criar o Projeto

Nesta secção, irá criar um projeto do Visual Studio através de um modelo de exemplo. Irá criar um ambiente virtual e instalar pacotes necessários. Irá criar uma base de dados local utilizando o sqlite. Em seguida, irá executar a aplicação localmente.

1. No Visual Studio, selecione **Ficheiro**, **Novo Projeto**.

1. Os modelos de projeto do [Ferramentas do Python 2.2 para VSIX de Exemplo do Visual Studio] estão disponíveis em **Python**, **Amostras**. Selecione **Projeto Web Django de Inquérito** e clique em OK para criar o projeto.

    ![Caixa de Diálogo Novo Projeto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1. Ser-lhe-á pedido que instale pacotes externos. Selecione **Instalar num ambiente virtual**.

    ![Caixa de Diálogo de Pacotes Externos](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1. Selecione **Python 2.7** ou **Python 3.4** como o interpretador base.

    ![Caixa de Diálogo Adicionar Ambiente Virtual](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto, selecione **Python** e, em seguida, selecione **Migrar o Django**.  Em seguida, selecione **Criar Superutilizador do Django**.

1. Isto irá abrir uma Consola de Gestão do Django e irá criar uma base de dados do sqlite na pasta do projeto. Siga as instruções para criar um utilizador.

1. Confirme que a aplicação funciona premindo `F5`.

1. Clique em **Iniciar sessão** na barra de navegação na parte superior.

    ![Barra de Navegação do Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1. Introduza as credenciais para o utilizador que criou quando sincronizou a base de dados.

    ![Formulário de Início de Sessão](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1. Clique em **Criar Inquérito de Exemplo**.

    ![Criar Inquérito de Exemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1. Clique num inquérito e vote.

    ![Votar nos Inquéritos de Exemplo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Criar uma Base de Dados MySQL

Para a base de dados, irá criar uma base de dados MySQL ClearDB alojada no Azure.

Como alternativa, pode criar a sua própria Máquina Virtual em execução no Azure e, em seguida, instalar e administrar o MySQL.

Pode criar uma base de dados com um plano gratuito seguindo estes passos.

1. Inicie sessão no [Portal do Azure].

1. Na Parte superior do painel de navegação, clique em **NOVO**, clique em **Dados + Armazenamento** e, em seguida, clique em **Base de Dados MySQL**. 

1. Configure a nova base de dados MySQL ao criar um novo grupo de recursos e ao selecionar a localização apropriada para a mesma.

1. Quando a base de dados MySQL tiver sido criada, clique em **Propriedades** no painel da base de dados.

1. Utilize o botão para copiar para colocar o valor de **CADEIA DE LIGAÇÃO** na área de transferência.

## Configurar o Projeto

Nesta secção, irá configurar a nossa aplicação Web para utilizar a base de dados MySQL que acabou de criar. Irá também instalar pacotes do Python adicionais necessários para utilizar as bases de dados MySQL com o Django. Em seguida, irá executar a aplicação Web localmente.

1. No Visual Studio, abra **settings.py**, na pasta *ProjectName*. Cole temporariamente a cadeia de ligação no editor. A cadeia de ligação está neste formato:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Altere a base de dados predefinida **MOTOR** para utilizar o MySQL e defina os valores para **NOME**, **UTILIZADOR**, **PALAVRA-PASSE** e **ANFITRIÃO** na **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1. No Explorador de Soluções, em **Ambientes do Python**, clique com o botão direito do rato no ambiente virtual e selecione **Instalar Pacote do Python**.

1. Instale o pacote `mysqlclient` utilizando **pip**.

    ![Caixa de Diálogo Instalar Pacote](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto, selecione **Python** e, em seguida, selecione **Migrar o Django**.  Em seguida, selecione **Criar Superutilizador do Django**.

    Isto irá criar as tabelas para a base de dados MySQL que criou na secção anterior. Siga as instruções para criar um utilizador, que não tem de corresponder ao utilizador na base de dados do sqlite criado na primeira secção deste artigo.

1. Execute a aplicação com `F5`. Os inquéritos criados com **Criar Inquéritos de Exemplo** e os dados submetidos por voto serão serializados na base de dados MySQL.

## Publicar a Aplicação Web no App Service do Azure

O SDK .NET do Azure fornece uma forma fácil de implementar a aplicação Web no App Service do Azure.

1. No **Explorador de Soluções**, clique com o botão direito do rato no nó do projeto e selecione **Publicar**.

    ![Caixa de Diálogo Publicar Web](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1. Clique em **Serviço de Aplicações do Microsoft Azure**.

1. Clique em **Novo** para criar uma nova aplicação Web.

1. Preencha os campos seguintes e clique em **Criar**:
    - **Nome da Aplicação Web**
    - **Plano do App Service**
    - **Grupo de recursos**
    - **Região**
    - Deixe **Servidor de bases de dados** definido como **Nenhuma base de dados**

1. Aceite todas as outras predefinições e clique em **Publicar**.

1. O browser abrirá automaticamente na aplicação Web publicada. Deverá ver a aplicação Web a funcionar conforme esperado, utilizando a base de dados **MySQL** alojada no Azure.

    ![Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

    Parabéns! Publicou com êxito a aplicação Web baseada em MySQL no Azure.

## Passos seguintes

Siga estas ligações para saber mais sobre as Ferramentas do Python para Visual Studio, Django e MySQL.

- [Documentação das Ferramentas do Python para Visual Studio]
  - [Projetos Web]
  - [Projetos do Serviço em Nuvem]
  - [Depuração Remota no Microsoft Azure]
- [Documentação do Django]
- [MySQL]

Para obter mais informações, consulte o [Centro para Programadores do Python](/develop/python/).

<!--Link references-->

[Centro para Programadores do Python]: /develop/python/
[Cloud Services do Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->

[Portal do Azure]: https://portal.azure.com
[Ferramentas do Python para Visual Studio]: http://aka.ms/ptvs
[Ferramentas do Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do Python 2.2 para VSIX de Exemplo do Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do Azure SDK para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentação das Ferramentas do Python para Visual Studio]: http://aka.ms/ptvsdocs
[Depuração Remota no Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Projetos Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Projetos do Serviço em Nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentação do Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[vídeo]: http://youtu.be/oKCApIrS0Lo



<!--HONumber=Aug16_HO1-->


