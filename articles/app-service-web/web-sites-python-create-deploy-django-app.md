<properties
    pageTitle="Criar Web Apps com o Django no Azure"
    description="Um tutorial que explica como executar uma aplicação Web Python nas Web Apps do App Service do Azure."
    services="app-service\web"
    documentationCenter="python"
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# Criar Web Apps com o Django no Azure

Este tutorial descreve como começar a executar o Python nas [Web Apps do App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). As Web Apps fornecem um alojamento gratuito e uma implementação rápida e pode utilizar o Python! À medida que a sua aplicação cresce, pode mudar para o alojamento pago e também pode integrar com todos os outros serviços do Azure.

Irá criar uma aplicação utilizando a estrutura Web Django (ver versões alternativas deste tutorial para [Balão](web-sites-python-create-deploy-flask-app.md) e [Garrafa](web-sites-python-create-deploy-bottle-app.md)). Irá criar a aplicação Web a partir do Azure Marketplace, configurar a implementação de Git e clonar o repositório localmente. Em seguida, irá executar a aplicação localmente, efetuar alterações, confirmá-las e enviá-las para o Azure. O tutorial mostra como o fazer a partir do Windows ou Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.


## Pré-requisitos

- Windows, Mac ou Linux
- Python 2.7 ou 3.4
- setuptools, pip, virtualenv (apenas no Python 2.7)
- Git
- [Ferramentas do Python para Visual Studio][] (PTVS) – Nota: isto é opcional

**Nota**: de momento, a publicação do TFS não é suportada para projetos Python.

### Windows

Se ainda não tiver o Python 2.7 ou 3.4 instalado (32 bits), recomendamos que instale o [Azure SDK para Python 2.7] ou o [Azure SDK para Python 3.4] utilizando o Instalador de Plataforma Web. Esta ação instala a versão de 32 bits do Python, setuptools, pip, virtualenv, etc. (o Python de 32 bits é o que é instalado nos computadores anfitriões Azure). Em alternativa, pode obter o Python de [python.org].

Para Git, recomendamos o [Git para Windows] ou o [GitHub para Windows]. Se utilizar o Visual Studio, pode utilizar o suporte de Git integrado.

Também recomendamos que instale as [Ferramentas do Python 2.2 para Visual Studio]. Isto é opcional, mas se tiver o [Visual Studio], incluindo o Visual Studio Community 2013 gratuito ou o Visual Studio Express 2013 para a Web, isto irá dar-lhe-á um excelente IDE do Python.

### Mac/Linux

Já deve ter Python e Git já instalado, mas certifique-se de que tem o Python 2.7 ou 3.4.


## Criação de Aplicação Web no Portal

O primeiro passo para criar a sua aplicação consiste em criar a aplicação Web através de [Portal do Azure](https://portal.azure.com).

1. Inicie sessão no Portal do Azure e clique no botão **NOVO** no canto inferior esquerdo.
3. Na caixa de pesquisa, escreva "python".
4. Nos resultados da pesquisa, selecione **Django** e, em seguida, clique em **Criar**.
5. Configure a nova aplicação Django como, por exemplo, criar um novo plano do App Service e um novo grupo de recursos para o mesmo. Em seguida, clique em **Criar**.
6. Configure a publicação de Git para a sua aplicação Web recentemente criada ao seguir as instruções apresentadas em [Implementação de Git Local para o Serviço de Aplicações do Azure](app-service-deploy-local-git.md).

## Descrição Geral da Aplicação

### Conteúdo do repositório de Git

Eis uma descrição geral dos ficheiros que encontrará no repositório de Git inicial, que iremos clonar na secção seguinte.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Origens principais para a aplicação. Consiste em 3 páginas (índice, acerca de, contacto) com um esquema do modelo global. O conteúdo estático e os scripts incluem arranque, jquery, modernizr e responder.

    \manage.py

Suporte de servidores de desenvolvimento e gestão local. Utilize esta opção para executar a aplicação localmente, sincronizar a base de dados, etc.

    \db.sqlite3

Base de dados predefinida. Inclui as tabelas necessárias para a aplicação ser executada, mas não contêm quaisquer utilizadores (sincronize a base de dados para criar um utilizador).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Os ficheiros de projeto para utilização com as [Ferramentas do Python para Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy do IIS para ambientes virtuais e suporte de depuração remota do PTVS.

    \requirements.txt

Pacotes externos necessários para esta aplicação. O script de implementação irá instalar os pacotes listados neste ficheiro através do pip.

    \web.2.7.config
    \web.3.4.config

Ficheiros de configuração do IIS. O script de implementação irá utilizar o web.x.y.config adequado e copiá-lo como web.config.

### Ficheiros opcionais - personalizar a implementação

[AZURE.INCLUDE [web-sites-python-django-customizing-deployment](../../includes/web-sites-python-django-customizing-deployment.md)]

### Ficheiros opcionais - runtime do Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Ficheiros adicionais no servidor

Alguns ficheiros existem no servidor, mas não foram adicionados ao repositório de git. Estes são criados pelo script de implementação.

    \web.config

Ficheiro de configuração do IIS. Criado a partir de web.x.y.config em todas as implementações.

    \env\

Ambiente virtual do Python. Criado durante a implementação se um ambiente virtual compatível ainda não existir na aplicação Web. Os pacotes listados em requirements.txt são instalados pelo pip, mas o pip saltará a instalação se os pacotes já estiverem instalados.

As 3 secções seguintes descrevem como continuar com o desenvolvimento de Web Apps em 3 ambientes diferentes:

- Windows, com as Ferramentas do Python para Visual Studio
- Windows, com linha de comandos
- Mac/Linux, com linha de comandos


## Desenvolvimento de Web Apps – Windows – Ferramentas do Python para Visual Studio

### Clonar o repositório

Em primeiro lugar, clone o repositório utilizando o URL fornecido no Portal do Azure. Para saber mais, veja o artigo [Implementação de Git Local para o Serviço de Aplicações do Azure](app-service-deploy-local-git.md).

Abra o ficheiro de solução (.sln) que está incluído na raiz do repositório.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Criar ambiente virtual

Agora, vamos criar um ambiente virtual para o desenvolvimento local. Clique com o botão direito do rato em **Ambientes do Python** e selecione **Adicionar Ambiente Virtual...**.

- Certifique-se de que o nome do ambiente é `env`.

- Selecione o interpretador base. Certifique-se de que utiliza a mesma versão do Python selecionada para a sua aplicação Web (no runtime.txt ou no painel **Definições da Aplicação** da sua aplicação Web no Portal do Azure).

- Certifique-se de que a opção para transferir e instalar pacotes está marcada.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Clique em **Criar**. Isto irá criar o ambiente virtual e instalar dependências listadas no requirements.txt.

### Criar um superutilizador

A base de dados incluída com a aplicação não tem qualquer superutilizador definido. Para utilizar a funcionalidade de início de sessão na aplicação ou a interface de administração do Django (se optar por ativá-la), terá de criar um superutilizador.

Execute isto a partir da linha de comandos da pasta do projeto:

    env\scripts\python manage.py createsuperuser

Siga as instruções para definir o nome de utilizador, a palavra-passe, etc.

### Executar com o servidor de desenvolvimento

Prima F5 para iniciar a depuração e o seu browser abrirá automaticamente na página a ser executada localmente.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Pode configurar pontos de interrupção nas origens, utilizar janelas de monitorização, etc. Consulte a [Documentação das Ferramentas do Python para Visual Studio] para mais informações sobre as várias funcionalidades.

### Efetuar alterações

Agora, pode experimentar efetuando alterações aos modelos e/ou às origens das aplicações.

Depois de ter testado as suas alterações, confirme-as no repositório de Git:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Instalar mais pacotes

A aplicação pode ter dependências que ultrapassam o Python e o Django.

É possível instalar pacotes adicionais através do pip. Para instalar um pacote, clique com o botão direito do rato no ambiente virtual e selecione **Instalar Pacote do Python**.

Por exemplo, para instalar o Azure SDK para o Python, que lhe dá acesso ao Storage do Azure, ao Service Bus e a outros serviços do Azure, introduza `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Clique com o botão direito do rato no ambiente virtual e selecione **Gerar requirements.txt** para atualizar o ficheiro requirements.txt.

Em seguida, confirme as alterações ao requirements.txt no repositório de Git.

### Implementar no Azure

Para acionar uma implementação, clique em **Sincronizar** ou em **Enviar**. A sincronização envia e recebe.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

A primeira implementação irá demorar algum tempo, uma vez que irá criar um ambiente virtual, instalar pacotes, etc.

O Visual Studio não mostra o progresso da implementação. Se pretende rever os resultados, consulte a secção sobre [Resolução de problemas - Implementação](#troubleshooting-deployment).

Navegue até ao URL do Azure para ver as suas alterações.


## Implementação da aplicação Web – Windows – linha de comandos

### Clonar o repositório

Em primeiro lugar, clone o repositório utilizando o URL fornecido no Portal do Azure e adicione o repositório do Azure como um remoto. Para saber mais, veja o artigo [Implementação de Git Local para o Serviço de Aplicações do Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Criar ambiente virtual

Vamos criar um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório). Os ambientes virtuais no Python não são relocalizáveis, pelo que cada programador que trabalha na aplicação irá criar os seus próprios localmente.

Certifique-se de que utiliza a mesma versão do Python selecionada para a sua aplicação Web (no runtime.txt ou no painel Definições da Aplicação da sua aplicação Web no Portal do Azure).

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instale quaisquer pacotes externos exigidos pela sua aplicação. Pode utilizar o ficheiro requirements.txt na raiz do repositório para instalar os pacotes num ambiente virtual:

    env\scripts\pip install -r requirements.txt

### Criar um superutilizador

A base de dados incluída com a aplicação não tem qualquer superutilizador definido. Para utilizar a funcionalidade de início de sessão na aplicação ou a interface de administração do Django (se optar por ativá-la), terá de criar um superutilizador.

Execute isto a partir da linha de comandos da pasta do projeto:

    env\scripts\python manage.py createsuperuser

Siga as instruções para definir o nome de utilizador, a palavra-passe, etc.

### Executar com o servidor de desenvolvimento

Pode iniciar a aplicação num servidor de desenvolvimento com o seguinte comando:

    env\scripts\python manage.py runserver

A consola irá apresentar o URL e a porta que o servidor escuta:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Em seguida, abra o browser nesse URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Efetuar alterações

Agora, pode experimentar efetuando alterações aos modelos e/ou às origens das aplicações.

Depois de ter testado as suas alterações, confirme-as no repositório de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

A aplicação pode ter dependências que ultrapassam o Python e o Django.

É possível instalar pacotes adicionais através do pip. Por exemplo, para instalar o Azure SDK para o Python, que lhe dá acesso ao Storage do Azure, ao Service Bus e a outros serviços do Azure, introduza:

    env\scripts\pip install azure

Certifique-se de que atualiza o requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementar no Azure

Para acionar uma implementação, envie as alterações para o Azure:

    git push azure master

Verá os resultados do script de implementação, incluindo a criação do ambiente virtual, a instalação de pacotes e a criação de web.config.

Navegue até ao URL do Azure para ver as suas alterações.


## Implementação da aplicação Web – Mac/Linux – linha de comandos

### Clonar o repositório

Em primeiro lugar, clone o repositório utilizando o URL fornecido no Portal do Azure e adicione o repositório do Azure como um remoto. Para saber mais, veja o artigo [Implementação de Git Local para o Serviço de Aplicações do Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Criar ambiente virtual

Vamos criar um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório). Os ambientes virtuais no Python não são relocalizáveis, pelo que cada programador que trabalha na aplicação irá criar os seus próprios localmente.

Certifique-se de que utiliza a mesma versão do Python selecionada para a sua aplicação Web (no runtime.txt ou no painel Definições da Aplicação da sua aplicação Web no Portal do Azure).

Para Python 2.7:

    python -m virtualenv env

Para Python 3.4:

    python -m venv env

ou

    pyvenv env

Instale quaisquer pacotes externos exigidos pela sua aplicação. Pode utilizar o ficheiro requirements.txt na raiz do repositório para instalar os pacotes num ambiente virtual:

    env/bin/pip install -r requirements.txt

### Criar um superutilizador

A base de dados incluída com a aplicação não tem qualquer superutilizador definido. Para utilizar a funcionalidade de início de sessão na aplicação ou a interface de administração do Django (se optar por ativá-la), terá de criar um superutilizador.

Execute isto a partir da linha de comandos da pasta do projeto:

    env/bin/python manage.py createsuperuser

Siga as instruções para definir o nome de utilizador, a palavra-passe, etc.

### Executar com o servidor de desenvolvimento

Pode iniciar a aplicação num servidor de desenvolvimento com o seguinte comando:

    env/bin/python manage.py runserver

A consola irá apresentar o URL e a porta que o servidor escuta:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Em seguida, abra o browser nesse URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Efetuar alterações

Agora, pode experimentar efetuando alterações aos modelos e/ou às origens das aplicações.

Depois de ter testado as suas alterações, confirme-as no repositório de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

A aplicação pode ter dependências que ultrapassam o Python e o Django.

É possível instalar pacotes adicionais através do pip. Por exemplo, para instalar o Azure SDK para o Python, que lhe dá acesso ao Storage do Azure, ao Service Bus e a outros serviços do Azure, introduza:

    env/bin/pip install azure

Certifique-se de que atualiza o requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementar no Azure

Para acionar uma implementação, envie as alterações para o Azure:

    git push azure master

Verá os resultados do script de implementação, incluindo a criação do ambiente virtual, a instalação de pacotes e a criação de web.config.

Navegue até ao URL do Azure para ver as suas alterações.


## Resolução de problemas - Instalação do Pacote

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Resolução de problemas - Ambiente Virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Resolução de problemas - Ficheiros Estáticos

O Django tem o conceito de recolha de ficheiros estáticos. Esta ação copia todos os ficheiros estáticos da sua respetiva localização original para uma única pasta. Para esta aplicação, são copiados para `/static`.

Isto é feito porque os ficheiros estáticos podem ser provenientes de “aplicações” do Django diferentes. Por exemplo, os ficheiros estáticos das interfaces de administração do Django estão localizados numa subpasta da biblioteca do Django no ambiente virtual. Os ficheiros estáticos definidos por esta aplicação estão localizados em `/app/static`. À medida que utiliza mais “aplicações” do Django, terá ficheiros estáticos localizados em vários locais.

Ao executar a aplicação no modo de depuração, a aplicação serve os ficheiros estáticos a partir da respetiva localização original.

Ao executar a aplicação no modo de libertação, a aplicação **não** serve os ficheiros estáticos. É da responsabilidade do servidor Web servir os ficheiros. Para esta aplicação, o IIS irá servir os ficheiros estáticos a partir de `/static`.

A coleção de ficheiros estáticos é feita automaticamente como parte do script de implementação, limpando ficheiros recolhidos anteriormente. Isto significa que a coleção ocorre em todas as implementações, abrandando a implementação um pouco, mas assegura que os ficheiros obsoletos não estarão disponíveis, evitando um potencial problema de segurança.

Se pretender ignorar a recolha de ficheiros estáticos para a sua aplicação Django:

    \.skipDjango

Terá de fazer a recolha manualmente na sua máquina local:

    env\scripts\python manage.py collectstatic

Em seguida, remova a pasta `\static` de `.gitignore` e adicione-a ao repositório de Git.


## Resolução de Problemas – Definições

Várias definições para a aplicação podem ser alteradas em `DjangoWebProject/settings.py`.

Para a comodidade do programador, o modo de depuração está ativado. Um efeito secundário positivo é que poderá ver imagens e outros conteúdos estáticos ao executar localmente, sem ter de recolher ficheiros estáticos.

Para desativar o modo de depuração:

    DEBUG = False

Quando a depuração está desativada, o valor para `ALLOWED_HOSTS` tem de ser atualizado para incluir o nome do anfitrião do Azure. Por exemplo:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

ou para ativar qualquer:

    ALLOWED_HOSTS = (
        '*',
    )

Na prática, deve fazer algo mais complexo para lidar com a mudança entre o modo de depuração e de libertação e obter o nome do anfitrião.

Pode definir variáveis de ambiente através da página **CONFIGURAR** do Portal do Azure, na secção **definições da aplicação**.  Isto pode ser útil para definir valores que pode não querer que apareçam nas origens (cadeias de ligação, palavras-passe, etc.) ou que pretende definir de forma diferente entre o Azure e a sua máquina local. Em `settings.py`, pode consultar as variáveis do ambiente utilizando `os.getenv`.


## Utilizar uma Base de Dados

A base de dados que está incluída com a aplicação é uma base de dados do sqlite. Esta é uma base de dados predefinida útil e conveniente para utilizar para o desenvolvimento, uma vez que não requer quase nenhuma configuração. A base de dados é armazenada no ficheiro db.sqlite3 na pasta do projeto.

O Azure oferece serviços de base de dados que são fáceis de utilizar a partir de uma aplicação Django. Tutoriais para utilizar a [SQL Database] e [MySQL] a partir de uma aplicação Django que mostram os passos necessários para criar o serviço de base de dados, alterar as definições da base de dados em `DjangoWebProject/settings.py`, e as bibliotecas cuja instalação é necessária.

Obviamente, se preferir gerir os seus próprios servidores de bases de dados, pode fazê-lo utilizando máquinas virtuais Windows ou Linux em execução no Azure.


## Interface de Administração do Django

Assim que começar a construir os seus modelos, será útil preencher a base de dados com alguns dados. Uma forma fácil de adicionar e editar o conteúdo de forma interativa consiste em utilizar a interface de administração do Django.

O código para a interface de administração é comentado nas origens das aplicações, mas está claramente assinalado para que o possa ativar facilmente (pesquise "administração").

Depois de ser ativada, sincronize a base de dados, execute a aplicação e navegue até `/admin`.


## Passos Seguintes

Siga estas ligações para saber mais sobre o Django e as Ferramentas do Python para Visual Studio:

- [Documentação do Django]
- [Documentação das Ferramentas do Python para Visual Studio]

Para obter informações sobre como utilizar a SQL Database e o MySQL:

- [O Django e o MySQL no Azure com Ferramentas do Python para Visual Studio]
- [O Django e a SQL Database no Azure com Ferramentas do Python para Visual Studio]

Para obter mais informações, consulte o [Centro para Programadores do Python](/develop/python/).


## O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o Respetivo Impacto nos Serviços do Azure Existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[O Django e o MySQL no Azure com Ferramentas do Python para Visual Studio]: web-sites-python-ptvs-django-mysql.md
[O Django e a SQL Database no Azure com Ferramentas do Python para Visual Studio]: web-sites-python-ptvs-django-sql.md
[SQL Database]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Ferramentas do Python para Visual Studio]: http://aka.ms/ptvs
[Ferramentas do Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Documentação das Ferramentas do Python para Visual Studio]: http://aka.ms/ptvsdocs
[Documentação do Django]: https://www.djangoproject.com/



<!--HONumber=Aug16_HO1-->


