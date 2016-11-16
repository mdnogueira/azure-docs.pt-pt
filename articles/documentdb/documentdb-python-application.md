---
title: "Desenvolvimento da aplicação Web Python com o DocumentDB | Microsoft Docs"
description: "Reveja um tutorial de base de dados sobre a utilização do DocumentDB para armazenar e aceder a dados a partir de uma aplicação Web Python Flask alojada no Azure. Encontre soluções de desenvolvimento de aplicações."
keywords: "Desenvolvimento de aplicações, tutorial sobre bases de dados, python flask, aplicação web python, desenvolvimento web do python, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: python
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: documentdb
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b55d61071dac6d173f37bdde7f9b60e53ae2485f


---
# <a name="python-flask-web-application-development-with-documentdb"></a>Desenvolvimento da aplicação Web Python com o DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Este tutorial mostra-lhe como utilizar o Azure DocumentDB para armazenar e aceder a dados a partir de uma aplicação Web Python alojada no Azure e pressupõe que tem alguma experiência anterior com a utilização dos web sites Python e Azure.

Este tutorial de base de dados abrange:

1. A criação e o aprovisionamento de uma conta do DocumentDB.
2. A criação de uma aplicação Python MVC.
3. A ligação e a utilização do Azure DocumentDB a partir da sua aplicação Web.
4. Implementar a aplicação Web em web sites do Azure.

Ao seguir este tutorial, irá criar uma aplicação de voto simples que lhe permite votar num inquérito.

![Captura de ecrã da aplicação Web ToDo List criada por este tutorial de base de dados](./media/documentdb-python-application/image1.png)

## <a name="database-tutorial-prerequisites"></a>Pré-requisitos do tutorial da base de dados
Antes de seguir as instruções deste artigo, deve certificar-se de que os seguintes elementos estão instalados:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio 2013](http://www.visualstudio.com/) ou superior, ou [Visual Studio Express](), que é a versão gratuita. As instruções deste tutorial foram especificamente redigidas para o Visual Studio 2015. 
* Ferramentas do Python para Visual Studio a partir do [GitHub](http://microsoft.github.io/PTVS/). Este tutorial utiliza as Ferramentas do Python para a VS 2015. 
* Azure SDK Python para Visual Studio, versão 2.4 ou superior disponível a partir do [azure.com](https://azure.microsoft.com/downloads/). Utilizámos o SDK do Microsoft Azure para Python 2.7.
* Python 2.7 de [python.org][2]. Utilizámos o Python 2.7.11. 

> [!IMPORTANT]
> Se estiver a instalar o Python 2.7 pela primeira vez, certifique-se de que selecionou **Adicionar python.exe ao Caminho** no ecrã Personalizar Python 2.7.11.
> 
> ![Captura de ecrã do ecrã Personalizar Python 2.7.11, onde tem de selecionar Adicionar python.exe ao Caminho](./media/documentdb-python-application/image2.png)
> 
> 

* Compilador do Microsoft Visual C++ para Python 2.7 a partir do [Centro de Transferências da Microsoft][3].

## <a name="step-1-create-a-documentdb-database-account"></a>Passo 1: Criar uma conta de base de dados no DocumentDB
Comecemos por criar uma conta DocumentDB. Se já tiver uma conta, pode avançar para o [Passo 2: Criar uma nova aplicação Web Python Flask](#step-2:-create-a-new-python-flask-web-application).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

<br/>
Iremos agora guiá-lo na criação de uma nova aplicação Web Python Flask a partir do zero.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Passo 2: Criar uma nova aplicação Web Python Flask
1. No Visual Studio, no menu **Ficheiro**, aponte para **Novo**, e, em seguida, clique em **Projeto**.
   
    Aparece a caixa de diálogo **Novo Projeto**.
2. No painel da esquerda, expanda **Modelos** e **Python**, e, em seguida, clique em **Web**. 
3. Selecione **Flask Web Project** no painel central e, na caixa **Nome**, introduza **Tutorial**, e, em seguida, clique em **OK**. Lembre-se de que os nomes de pacote Python devem ser todos escritos em minúsculas, conforme descrito no [Guia de Estilo para Código Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Para aqueles que dão os primeiros passos com o Python Flask, trata-se de uma estrutura de desenvolvimento de aplicações Web que o ajuda a criar rapidamente aplicações Web no Python.
   
    ![Captura de ecrã da janela Novo Projeto no Visual Studio com o Python realçado à esquerda, Projeto Web do Python Flask selecionado na parte central e o tutorial de nome na caixa Nome](./media/documentdb-python-application/image9.png)
4. Na janela **Ferramentas do Python para o Visual Studio**, clique em **Instalar num ambiente virtual**. 
   
    ![Captura de ecrã do tutorial da base de dados - Janela Ferramentas do Python para o Visual Studio](./media/documentdb-python-application/image10.png)
5. Na janela **Adicionar Ambiente Virtual**, pode aceitar as predefinições e utilizar o Python 2.7 como ambiente de base porque o PyDocumentDB não suporta atualmente o Python 3. x e, em seguida, clique em **Criar**. Esta ação configura o ambiente virtual do Python necessário para o seu projeto.
   
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
   
    ![Captura de ecrã que ilustra o env (Python 2.7) selecionado com Instalar a partir do requirements.txt realçado na lista](./media/documentdb-python-application/image11.png)
   
    Após a instalação com êxito, a janela de saída apresenta o seguinte:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > Em casos raros, poderá ver uma falha na janela de saída. Se isto acontecer, verifique se o erro está relacionado com a limpeza. Por vezes, a limpeza falha, mas a instalação ainda será concluída com êxito (deslocamento para cima na janela de saída para verificar esta situação). Pode verificar a sua instalação ao [Verificar o ambiente virtual](#verify-the-virtual-environment). Se a instalação falhou, mas a verificação for bem sucedida, poderá prosseguir.
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
2. Adicione as seguintes instruções de importação à parte superior do ficheiro **views.py** e, em seguida, guarde o ficheiro. Estas ações importam o PythonSDK do DocumentDB e os pacotes Flask.
   
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

> [!TIP]
> O método **CreateCollection** assume a opção **RequestOptions** como o terceiro parâmetro. Pode aplicar este método para especificar o Tipo de Oferta da coleção. Se não for fornecido nenhum valor de offerType, a coleção será então criada utilizando um Tipo de Oferta predefinido. Para mais informações sobre os Tipos de Oferta do DocumentDB, consulte o artigo [Níveis de desempenho no DocumentDB](documentdb-performance-levels.md)
> 
> 

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
1. No Explorador de Soluções, na pasta **tutorial**, clique com o botão direito do rato na pasta **modelos**, clique em **Adicionar** e, em seguida, clique em **Novo Item**. 
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
6. Adicione o seguinte código ao **vote.html** no `<body`> elemento. Apresenta o inquérito e aceita os votos. Aquando do registo dos votos, o controlo é transmitido ao views.py onde iremos reconhecer a transmissão do voto e acrescentar o documento em conformidade.
   
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
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
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
3. No [Portal do Azure](https://portal.azure.com/), navegue para o painel **Chaves** clicando em **Procurar**, **Contas do DocumentDB**, faça um duplo clique no nome da conta a utilizar e, em seguida, clique no botão **Chaves** na área **Essentials**. No painel **Chaves**, copie o valor **URI** e cole-o no ficheiro **config.py** como o valor para a propriedade **ANFITRIÃO\_DOCUMENTDB**. 
4. De regresso ao Portal do Azure, no painel **Chaves**, copie o valor da **Chave Primária** ou da **Chave Secundária** e cole-o no ficheiro **config.py** ficheiro como o valor para a propriedade **CHAVE\_DOCUMENTDB**.
5. No ficheiro **\_\_init\_\_.py**, adicione a seguinte linha. 
   
        app.config.from_object('config')
   
    Para que o conteúdo do ficheiro seja:
   
    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```
6. Depois de adicionar todos os ficheiros, o Explorador de Soluções deverá ter o seguinte aspeto:
   
    ![Captura de ecrã da janela do Explorador de soluções do Visual Studio](./media/documentdb-python-application/image15.png)

## <a name="step-4-run-your-web-application-locally"></a>Passo 4: Executar a sua aplicação localmente
1. Compile a solução ao premir **Ctrl**+**Shift**+**B**.
2. Assim que a compilação for concluída com êxito, inicie o Web site premindo **F5**. No seu ecrã, deverá ver o seguinte.
   
    ![Captura de ecrã do Python + Aplicação de Voto do DocumentDB apresentada num browser](./media/documentdb-python-application/image16.png)
3. Clique em **Criar/Limpar a Base de dados de Voto** para gerar a base de dados.
   
    ![Captura de ecrã da Página Criar da aplicação Web – detalhes de programação](./media/documentdb-python-application/image17.png)
4. Em seguida, clique em **Votar** e selecione a sua opção.
   
    ![Captura de ecrã da aplicação Web com uma pergunta de voto colocada](./media/documentdb-python-application/image18.png)
5. Cada voto que transmitir será contado no contador adequado.
   
    ![Captura de ecrã dos Resultados da página de votos apresentada](./media/documentdb-python-application/image19.png)
6. Interrompa a depuração do projeto premindo Shift+F5.

## <a name="step-5-deploy-the-web-application-to-azure-websites"></a>Passo 5: Implementar a aplicação Web em Web Sites do Azure
Agora que a sua aplicação funciona correta no DocumentDB, iremos implementá-la em Web Sites do Azure.

1. Clique com o botão direito do rato no Explorador de Soluções (certifique-se de que não continua a executá-lo localmente) e selecione **Publicar**.  
   
     ![Captura de ecrã do tutorial selecionado no Explorador de Soluções com a opção Publicar realçada.](./media/documentdb-python-application/image20.png)
2. Na janela **Publicar na Web**, selecione **Web Apps do Microsoft Azure**, e, em seguida, clique em **Seguinte**.
   
    ![Captura de ecrã da janela Publicar na Web com as Web Apps do Microsoft Azure realçadas](./media/documentdb-python-application/image21.png)
3. Na **Janela Web Apps do Microsoft Azure**, clique em **Novo**.
   
    ![Captura de ecrã da Janela Web das Web Apps do Microsoft Azure](./media/documentdb-python-application/select-existing-website.png)
4. Na janela **Criar site no Microsoft Azure**, introduza um **nome para a Web App**, um **Plano do App Servic**, um **Grupo de Recursos** e uma **Região** E, em seguida, clique em **Criar**.
   
    ![Captura de ecrã do site Criar na janela do Microsoft Azure](./media/documentdb-python-application/create-site-on-microsoft-azure.png)
5. Na janela **Publicar na Web**, clique em **Publicar**.
   
    ![Captura de ecrã do site Criar na janela do Microsoft Azure](./media/documentdb-python-application/publish-web.png)
6. Em alguns segundos, O Visual Studio irá concluir a publicação da sua aplicação Web e iniciar um browser, onde poderá ver o seu trabalho em execução no Azure!

## <a name="troubleshooting"></a>Resolução de problemas
Se esta for a primeira aplicação de Python que executar no seu computador, certifique-se de que as seguintes pastas (ou as localizações de instalação equivalentes) estão incluídas na sua variável de CAMINHO:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Se receber um erro na sua página de voto e se atribuiu outro nome que não **tutorial** ao seu projeto, certifique-se de que o **\_\_init\_\_.py** referencia o nome do projeto correto na linha: `import tutorial.view`.

## <a name="next-steps"></a>Passos seguintes
Parabéns! Acabou de concluir a sua primeira aplicação Web Python com o Azure DocumentDB, que foi publicada em Web sites do Azure.

Atualizamos e melhoramos frequentemente este tópico com base nos seus comentários.  Depois de concluir o tutorial, utilize os botões de votos na parte superior e inferior desta página e não se esqueça de incluir os comentários sobre as melhorias que pretende que sejam feitas. Se preferir que entremos diretamente em contacto consigo, não hesite em indicar o seu endereço de e-mail nos seus comentários.

Para adicionar funcionalidades adicionais à sua aplicação Web, reveja as APIs disponíveis no [SDK Python do DocumentDB](documentdb-sdk-python.md).

Para obter mais informações sobre o Azure, o Visual Studio e o Python, consulte o artigo [Centro de Programadores do Python](https://azure.microsoft.com/develop/python/). 

Para tutoriais adicionais do Python Flask, consulte o artigo [Mega Tutorial Flask, Parte I: Olá, Mundo!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Instalador de Plataforma Web da Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[Portal do Azure]: http://portal.azure.com



<!--HONumber=Nov16_HO2-->


