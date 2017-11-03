---
title: "Tutorial de aplicação Web Python Flask para o Azure Cosmos DB | Microsoft Docs"
description: "Reveja um tutorial de base de dados sobre a utilização do Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação Web Python Flask alojada no Azure. Encontre soluções de desenvolvimento de aplicações."
keywords: "Desenvolvimento de aplicações, python flask, aplicação Web python, desenvolvimento Web python"
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: cosmos-db
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/17/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0629b0aa7e7981cf04330b407604e4612a58b168
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Criar uma aplicação Web Python Flask com o Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [python](documentdb-python-application.md)
> 
> 

Este tutorial mostra como utilizar a base de dados do Azure Cosmos para armazenar e aceder a dados a partir de uma aplicação de web do Python Flask alojada no App Service do Azure. Este tutorial presumes que tem alguma experiência anterior com o Python e Web sites do Azure.

Este tutorial de base de dados abrange:

1. Criar e aprovisionar uma conta de base de dados do Azure Cosmos.
2. Criar uma aplicação Python Flask.
3. Ligar e utilizar a base de dados do Azure Cosmos da sua aplicação web.
4. Implementar a aplicação web no App Service do Azure.

Ao seguir este tutorial, irá criar uma aplicação de voto simples que lhe permite votar num inquérito.

![Captura de ecrã da aplicação de voto criada por este tutorial de base de dados](./media/documentdb-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Pré-requisitos do tutorial da base de dados
Antes de seguir as instruções deste artigo, deve certificar-se de que os seguintes elementos estão instalados:

* [Uma subscrição do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017]((https://www.visualstudio.com/downloads/)) com **programação do Azure** e **desenvolvimento do Python** ativada. Pode verificar se os pré-requisitos estão instalados e instalação-los, abrindo **instalador do Visual Studio** localmente.   
* [Microsoft Azure SDK para Python 2.7](https://azure.microsoft.com/downloads/). 
* [Python 2.7](https://www.python.org/downloads/windows/). Pode utilizar a instalação de 32 bits ou 64 bits.

> [!IMPORTANT]
> Se estiver a instalar o Python 2.7 pela primeira vez, certifique-se de que no ecrã Personalizar Python 2.7.13, seleciona **adicionar python.exe ao caminho**.
> 
> ![Captura de ecrã do ecrã Personalizar Python 2.7.11, onde tem de selecionar Adicionar python.exe ao Caminho](./media/documentdb-python-application/cosmos-db-python-install.png)
> 
> 

* [Microsoft Visual C++ Compiler para o Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Passo 1: Criar uma conta de base de dados do Azure Cosmos DB
Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o Emulador do Azure Cosmos DB para este tutorial, pode avançar para o [Passo 2: Criar uma nova aplicação Web Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Agora vamos orientá-criar uma nova aplicação de web do Python Flask partir do zero cópias de segurança.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Passo 2: Criar uma nova aplicação Web Python Flask
1. No Visual Studio, no menu **Ficheiro**, aponte para **Novo**, e, em seguida, clique em **Projeto**.
   
    Aparece a caixa de diálogo **Novo Projeto**.
2. No painel da esquerda, expanda **Modelos** e **Python**, e, em seguida, clique em **Web**. 
3. Selecione **Flask Web Project** no painel central e, na caixa **Nome**, introduza **Tutorial**, e, em seguida, clique em **OK**. Lembre-se de que os nomes de pacote Python devem ser todos escritos em minúsculas, conforme descrito no [Guia de Estilo para Código Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Para aqueles que dão os primeiros passos com o Python Flask, trata-se de uma estrutura de desenvolvimento de aplicações Web que o ajuda a criar rapidamente aplicações Web no Python.
   
    ![Captura de ecrã da janela Novo Projeto no Visual Studio com o Python realçado à esquerda, Projeto Web do Python Flask selecionado na parte central e o tutorial de nome na caixa Nome](./media/documentdb-python-application/image9.png)
4. Na janela **Ferramentas do Python para o Visual Studio**, clique em **Instalar num ambiente virtual**. 
   
    ![Captura de ecrã do tutorial da base de dados - Janela Ferramentas do Python para o Visual Studio](./media/documentdb-python-application/python-install-virtual-environment.png)
5. No **adicionar ambiente Virtual** janela, selecione o Python 2.7 ou Python 3.5 em selecionar uma caixa interpretador, aceite as outras predefinições e, em seguida, clique em **criar**. Esta ação configura o ambiente virtual do Python necessário para o seu projeto.
   
    ![Captura de ecrã do tutorial da base de dados - Janela Ferramentas do Python para o Visual Studio](./media/documentdb-python-application/image10_A.png)
   
    A janela de saída apresenta `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` quando o ambiente tiver sido instalado com êxito.

## <a name="step-3-modify-the-python-flask-web-application"></a>Passo 3: Alterar a aplicação Web Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Adicione pacotes do Python Flask ao seu projeto
Depois de configurar o seu projeto, terá de adicionar os pacotes Flask necessários ao seu projeto, incluindo o pydocumentdb, o pacote Python para o DocumentDB.

1. No Explorador de Soluções, abra o ficheiro denominado **requirements.txt** e substitua o conteúdo pelo seguinte:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Guarde o ficheiro **requirements.txt**. 
3. No Explorador de Soluções, clique com o botão direito do rato em **env** e clique em **Instalar a partir do requirements.txt**.
   
    ![Captura de ecrã que ilustra o env (Python 2.7) selecionado com Instalar a partir do requirements.txt realçado na lista](./media/documentdb-python-application/cosmos-db-python-install-from-requirements.png)
   
    Após a instalação com êxito, a janela de saída apresenta o seguinte:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > Em casos raros, poderá ver uma falha na janela de saída. Se isto acontecer, verifique se o erro está relacionado com limpar. Por vezes, a limpeza falha, mas a instalação ainda será concluída com êxito (deslocamento para cima na janela de resultados para verificar isto). Pode verificar a sua instalação ao [Verificar o ambiente virtual](#verify-the-virtual-environment). Se a instalação falhou, mas a verificação for bem sucedida, poderá prosseguir.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Verificar o ambiente virtual
Certifiquemo-nos de que tudo foi corretamente instalado.

1. Compile a solução ao premir **Ctrl**+**Shift**+**B**.
2. Assim que a compilação for concluída com êxito, inicie o Web site premindo **F5**. Esta ação irá abrir o servir de desenvolvimento do Flask e iniciar o seu browser. Deverá ver a seguinte página:
   
    ![O projeto Web vazio de desenvolvimento do Python Flask apresentado num browser](./media/documentdb-python-application/image12.png)
3. Interrompa a depuração do web site premindo **Shift**+**F5** no Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Criar uma base de dados, uma coleção e definições do documento
Criemos agora a sua aplicação de voto adicionando novos ficheiros e atualizando outros.

1. No Explorador de Soluções, clique com o botão direito do rato em projeto **tutorial**, clique em **Adicionar** e, em seguida, clique em **Novo Item**. Selecione **Ficheiro Vazio do Python** e atribua o nome **forms.py** ao ficheiro.  
2. Copie o seguinte código para o ficheiro forms.psy e, em seguida, guarde o ficheiro.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Adicionar as importações requeridas ao views.psy
1. No Explorador de soluções, expanda a pasta **tutorial** e abra o ficheiro **views.py**. 
2. Adicione as seguintes instruções de importação à parte superior do ficheiro **views.py** e, em seguida, guarde o ficheiro. Estas ações importam o PythonSDK da BD do Cosmos do Azure e os pacotes Flask.
   
    ```python
    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Criar uma base de dados, uma coleção e um documento
* Ainda no **views.py**, adicione o seguinte código no fim do ficheiro. Tal trata da criação da base de dados utilizada pelo formulário. Não elimine nenhum código existente no **views.py**. Adicione apenas o código no fim.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Ler a base de dados, coleção, documentos e submeter o formulário
* Ainda no **views.py**, adicione o seguinte código no fim do ficheiro. Esta ação trata da configuração do formulário, da leitura da base de dados, da coleção e do documento. Não elimine nenhum código existente no **views.py**. Adicione apenas o código no fim.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Criar os ficheiros HTML
1. No Explorador de soluções, no **tutorial** pasta, faça duplo clique o **modelos** pasta, clique em **adicionar**e, em seguida, clique em **Novo Item**. 
2. Selecione **Página HTML** e, em seguida, na caixa de nome, introduza **create.html**. 
3. Repita os passos 1 e 2 para criar dois ficheiros adicionais de HTML: results.html e vote.html.
4. Adicione o seguinte código em **create.html** no `<body>` elemento. É apresentada uma mensagem com a informação de que foi criada uma nova base de dados, coleção e documento.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Adicione o seguinte código ao **results.html** no `<body`> elemento. Apresenta os resultados do inquérito.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Adicione o seguinte código ao **vote.html** no `<body`> elemento. Apresenta o inquérito e aceita os votos. Os votos aquando do registo, o controlo é transmitido ao views.py onde a base de dados do Azure Cosmos reconhece a transmissão do voto e acrescenta o documento em conformidade.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. Na pasta **modelos**, substitua o conteúdo **index.html** pelo seguinte. Esta página irá servir de página de destino para a sua aplicação.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Adicionar um ficheiro de configuração e alterar o \_\_init\_\_.py
1. No Explorador de Soluções, clique com o botão direito do rato no projeto **tutorial**, clique em **Adicionar**, clique em **Novo Item**, selecione **Ficheiro Vazio do Python** e, sem seguida, atribua o nome **config.py** ao ficheiro. Este ficheiro de configuração é necessário para formulários no Flask. Pode ainda utilizá-lo para fornecer uma chave secreta. No entanto, essa chave não é necessária para este tutorial.
2. Adicione o seguinte código ao config.py; terá de alterar os valores **ANFITRIÃO\_DOCUMENTDB** e **CHAVE\_DOCUMENTDB** no próximo passo.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'
    ```
3. No [portal do Azure](https://portal.azure.com/), navegue para o **chaves** página clicando **procurar**, **contas de base de dados do Azure Cosmos**, faça duplo clique no nome do conta a utilizar e, em seguida, clique em de **chaves** clique no botão no **Essentials** área. No **chaves** página, copie o **URI** valor e cole-o para o **config.py** ficheiro, como o valor para o **DOCUMENTDB\_anfitrião**propriedade. 
4. Crie uma no portal do Azure, do **chaves** página, copie o valor do **chave primária** ou o **chave secundária**e cole-o para o **config.py**ficheiro, como o valor para o **DOCUMENTDB\_chave** propriedade.
5. No  **\_ \_init\_\_. PY** ficheiro, adicione a seguinte linha: 
   
        app.config.from_object('config')
   
    Para que o conteúdo do ficheiro seja:
   
    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```
6. Depois de adicionar todos os ficheiros, o Explorador de Soluções deverá ter o seguinte aspeto:
   
    ![Captura de ecrã da janela do Explorador de soluções do Visual Studio](./media/documentdb-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Passo 4: Executar a sua aplicação localmente
1. Compile a solução ao premir **Ctrl**+**Shift**+**B**.
2. Assim que a compilação for concluída com êxito, inicie o Web site premindo **F5**. No seu ecrã, deverá ver o seguinte.
   
    ![Captura de ecrã do Python + Aplicação de Voto do Azure Cosmos DB apresentada num browser](./media/documentdb-python-application/cosmos-db-pythonr-run-application.png)
3. Clique em **Criar/Limpar a Base de dados de Voto** para gerar a base de dados.
   
    ![Captura de ecrã da Página Criar da aplicação Web – detalhes de programação](./media/documentdb-python-application/cosmos-db-python-run-create-page.png)
4. Em seguida, clique em **Votar** e selecione a sua opção.
   
    ![Captura de ecrã da aplicação Web com uma pergunta de voto colocada](./media/documentdb-python-application/cosmos-db-vote.png)
5. Cada voto que transmitir será contado no contador adequado.
   
    ![Captura de ecrã dos Resultados da página de votos apresentada](./media/documentdb-python-application/cosmos-db-voting-results.png)
6. Interrompa a depuração do projeto premindo Shift+F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Passo 5: Implementar a aplicação web no Azure
Agora que configurou a aplicação completa funciona corretamente em relação a base de dados do Azure Cosmos localmente, vamos criar um ficheiro Web. config, atualize os ficheiros no servidor para fazer corresponder o ambiente local e, em seguida, ver a aplicação concluída no Azure. Este procedimento é específico para Visual Studio 2017. Se estiver a utilizar uma versão diferente do Visual Studio, consulte o artigo [publicação App Service do Azure](/visualstudio/python/publishing-to-azure.md).

1. No Visual Studio **Explorador de soluções**, clique com o botão direito no projeto e selecione **adicionar > Novo Item...** . Na caixa de diálogo que aparece, selecionando o **Azure Web. config (Fast CGI)** modelo e selecione **OK**. Esta ação cria um `web.config` ficheiro na raiz do projeto. 

2. Modificar o `<system.webServer>` secção `web.config` para que o caminho corresponde a instalação do Python. Por exemplo, para o Python 2.7 x64 a entrada deverá aparecer da seguinte forma:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Definir o `WSGI_HANDLER` entrada no `web.config` como para `tutorial.app` para corresponder ao nome do seu projeto. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. No Visual Studio **Explorador de soluções**, expanda o **tutorial** pasta, clique com botão direito do `static` pasta, selecione **adicionar > Novo Item...** , selecione o modelo "Azure estático ficheiros Web. config" e selecione **OK**. Esta ação cria outra `web.config` no `static` pasta que desativa o Python processamento dessa pasta. Esta configuração envia pedidos dos ficheiros estáticos para o servidor de web predefinido, em vez de utilizar a aplicação de Python.

5. Guardar os ficheiros, em seguida, clique com o botão direito no projeto no Explorador de soluções (Certifique-se de que ainda não esteja ainda a executá-lo localmente) e selecione **publicar**.  
   
     ![Captura de ecrã do tutorial selecionado no Explorador de Soluções com a opção Publicar realçada.](./media/documentdb-python-application/image20.png)
6. No **publicar** caixa de diálogo, selecione **App Service do Microsoft Azure**, selecione **criar novo**e, em seguida, clique em **publicar**.
   
    ![Captura de ecrã da janela Publicar na Web com o serviço de aplicações do Microsoft Azure realçadas](./media/documentdb-python-application/cosmos-db-python-publish.png)
7. No **criar App Service** caixa de diálogo, introduza o nome da sua aplicação web juntamente com o **subscrição**, **grupo de recursos**, e **plano do App Service**, em seguida, clique em **criar**.
   
    ![Captura de ecrã da Janela Web das Web Apps do Microsoft Azure](./media/documentdb-python-application/cosmos-db-python-create-app-service.png)
8. Em alguns segundos, o Visual Studio terminar a copiar os ficheiros para o servidor e apresenta "não é possível apresentar a página porque ocorreu um erro interno do servidor." no `http://<your app service>.azurewebsites.net/` página.

9. No portal do Azure, abra a sua nova conta de serviço de aplicações, em seguida, no menu de navegação, desloque para baixo até o **ferramentas de desenvolvimento** secção, selecione **extensões**, em seguida, clique em **+ adicionar**.

10. No **escolha extensão** página, desloque para baixo para a instalação mais recente do Python 2.7 e selecione a opção de bits x86 ou x64, em seguida, clique em **OK** para aceitar os termos legais.  
   
11. Utilize a consola do Kudu, que pode procurar em `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, para instalar os pacotes listados na sua aplicação `requirements.txt` ficheiro. Para tal, na consola de diagnóstico do Kudu, navegue para a pasta de Python `D:\home\Python27` , em seguida, execute o seguinte comando, tal como descrito no [consola Kudu](/visual-studio/python/managing-python-on-azure-app-service.md#azure-app-service-kudu-console) secção:

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Reinicie o serviço de aplicação no portal do Azure depois de instalar os novos pacotes, premindo o **reiniciar** botão. 

    > [!Tip] 
    > Se efetuar alterações à sua aplicação `requirements.txt` de ficheiros, certifique-se novamente utilizar a consola do Kudu para instalar quaisquer pacotes que são agora listadas nesse ficheiro. 

13. Depois de configurar o ambiente de servidor completamente, atualize a página no browser e a aplicação web deve aparecer.

    ![Resultados da publicação de aplicações Bottle, Flask e Django no App Service](./media/documentdb-python-application/python-published-app-services.png)

    > [!Tip] 
    > Se não for apresentada a página web ou continua a aceder a "a página não pode ser apresentada porque ocorreu um erro de servidor interno." mensagem, abra o ficheiro Web. config na Kudo e adicionar ` <httpErrors errorMode="Detailed"></httpErrors>` para a secção System. webServer, em seguida, atualize a página. Esta será fornecido a saída de erro detalhadas para o browser. 

## <a name="troubleshooting"></a>Resolução de problemas
Se esta for a primeira aplicação de Python que executar no seu computador, certifique-se de que as seguintes pastas (ou as localizações de instalação equivalentes) estão incluídas na sua variável de CAMINHO:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Se receber um erro na sua página de voto e se atribuiu outro nome que não **tutorial** ao seu projeto, certifique-se de que o **\_\_init\_\_.py** referencia o nome do projeto correto na linha: `import tutorial.view`.

## <a name="next-steps"></a>Passos seguintes
Parabéns! Tem de concluir a sua primeira aplicação web Python com a base de dados do Azure Cosmos e publicada para o Azure.

Para adicionar funcionalidades adicionais à sua aplicação web, reveja as APIs disponíveis no [SDK Python do Azure Cosmos DB](documentdb-sdk-python.md).

Para obter mais informações sobre o Azure, o Visual Studio e o Python, consulte o artigo [Centro de Programadores do Python](https://azure.microsoft.com/develop/python/). 

Para tutoriais adicionais do Python Flask, consulte o artigo [Mega Tutorial Flask, Parte I: Olá, Mundo!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com
