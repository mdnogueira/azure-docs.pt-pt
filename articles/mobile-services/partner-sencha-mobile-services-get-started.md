<properties
    pageTitle="Introdução ao Mobile Services do Azure e ao Sencha"
    description="Siga este tutorial para começar a desenvolver com os Mobile Services e a estrutura de aplicação móveis Sencha HTML5."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-sencha"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>Introdução aos Mobile Services e ao Sencha Touch

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Descrição geral

Este tutorial mostra como tirar partido dos Mobile Services do Azure na sua aplicação Sencha Touch. Irá criar uma aplicação *Lista de Tarefas* simples com o Sencha Touch, que utiliza um serviço móvel definido através do Portal Clássico do Azure. Este tutorial destina-se a programadores de Web Apps de nível intermédio a avançado que têm uma boa compreensão de JavaScript e que estão familiarizados com a arquitetura do Sencha Touch

Se preferir ver um vídeo, este clip segue os mesmos passos deste tutorial. No vídeo, Arthur Kay explica como criar uma aplicação Sencha Touch utilizando um back-end dos Mobile Services do Azure.

> [AZURE.VIDEO getting-started-with-sencha-touch]


Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

##Requisitos

- Transfira e instale o [Sencha Touch](http://wwww.sencha.com/products/touch/download" target="_blank").

- Transfira e instale a [Ferramenta Sencha Cmd](http://www.sencha.com/products/sencha-cmd/download" target="_blank").

- Java Runtime Environment (JRE) ou Kit de Desenvolvimento Java (se estiver a criar aplicações Android)
- Ruby e SASS gem.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

##Criar uma Tabela TodoItems

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova tabela de base de dados para utilização no seu serviço móvel.

1. No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.

2. No separador de início rápido, clique em **HTML** em **Escolher plataforma** e expanda **Criar uma nova aplicação HTML**.

    ![Guia de introdução html dos Mobile Services](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    Esta ação apresenta os três passos fáceis necessários para criar e alojar uma aplicação HTML ligada ao seu serviço móvel.

    ![Guia de introdução html dos Mobile Services](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. Clique em **Criar tabela de Itens da Lista da Tarefas** para criar uma tabela para armazenar os dados da aplicação.

    > [AZURE.NOTE] NÃO transfira a aplicação HTML a partir do Portal Clássico do Azure. Em vez disso, iremos criar manualmente uma aplicação Sencha Touch na secção abaixo.


1. Tome nota do **appKey** e **appUrl** no Portal Clássico do Azure. Irá utilizá-los noutras secções deste tutorial.

    ![chave da aplicação](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. No separador **Configurar**, certifique-se de que `localhost` já está listado na lista **Permitir pedidos de nomes de anfitrião** em **Partilha de Recursos de Várias Origens (CORS)**. Se não estiver, introduza `localhost` no campo **Nome de anfitrião** e, em seguida, clique em **Guardar**.

    ![Configurar CORS para localhost](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##Gerar a aplicação Touch

A geração de uma aplicação de modelo Sencha Touch é uma tarefa simples que utiliza o Sencha Cmd e é uma excelente forma de deixar uma aplicação operacional muito rapidamente.

A partir do diretório onde instalou a arquitetura de Touch, emita o comando seguinte:

    $ sencha generate app Basic /path/to/application

Deste modo, é gerada uma aplicação Touch de modelo com o nome da aplicação “Basic”. Para iniciar a aplicação, basta fazer o browser apontar para o diretório /path/to/application para que seja apresentado o exemplo de aplicação Touch padrão.

##Instalar as Extensões Sencha Touch para o Azure

A extensão para o Azure é instalada manualmente ou como um Pacote do Sencha. O método utilizado fica completamente ao seu critério.

###Instalação manual

Na maioria das aplicações Touch, se pretender adicionar uma biblioteca externa de classes, basta transferir o pacote, descompactá-lo no diretório da aplicação e configurar o carregador de Touch com a localização da biblioteca.

Pode adicionar manualmente as extensões do Azure à aplicação através dos seguintes passos:

1. Transfira o pacote de extensões do Azure [aqui](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure). (Pode utilizar o ID dos Fóruns do Sencha para aceder a esta área.)

2. Copie o pacote de extensões do Azure do diretório de transferências para o local onde pretende que este resida e descompacte-o:

        $ cd /path/to/application
        $ mv /download-location/azure.zip .
        $ unzip azure.zip

    Isto cria um diretório **azure**que contém toda a origem do pacote, exemplos e documentação. A origem irá residir no diretório **azure/src**.


###Instalação como um pacote do Sencha

> [AZURE.NOTE] Só pode utilizar este método se tiver gerado a sua aplicação através do comando <code>sencha generate app</code>.

Todas as aplicações geradas pelo Sencha Cmd têm uma pasta “packages” na raiz. É possível configurar a localização desta pasta. No entanto, independentemente da respetiva localização, a função da pasta “packages” é servir como o armazenamento de todos os pacotes utilizados pela sua aplicação (ou aplicações, se tiver criado uma Área de Trabalho Sencha).

Como Ext.Azure é um “pacote” do Sencha Cmd, o código fonte pode ser facilmente instalado e incluído na sua aplicação utilizando o Sencha Cmd. (Consulte [Pacotes do Sencha Cmd](http://docs.sencha.com/cmd/6.x/cmd_packages/cmd_packages.html) para obter mais informações.)

Para transferir e instalar o pacote de extensões do Azure a partir do repositório de Pacotes do Sencha, terá de adicionar o nome do pacote ao seu ficheiro **app.json** e criar a sua aplicação:

1. Adicione o pacote do Azure à secção adequada do ficheiro app.json:

        {
            "name": "Basic",
            "requires": [
                "touch-azure"
            ]
        }

2. Reconstrua a sua aplicação utilizando **sencha cmd** para obter e instalar o pacote:

        $ sencha app build

O **sencha app build** e o **sencha app refresh** irão agora executar os passos necessários para integrar o pacote na sua aplicação. Normalmente, depois de alterar os requisitos do pacote, terá de executar **sencha app refresh** para atualizar os metadados necessários para suportar “dev mode”.

Seja qual for o comando executado, o Sencha Cmd irá transferir e expandir o pacote para a pasta “packages”. Depois disto, irá encontrar uma pasta “packages/touch-azure” na sua área de trabalho.

##Incluir e configurar o Azure

**Nome do ficheiro**: app.js

Agora que a extensão do Azure foi transferida e instalada no seu diretório de aplicações, o passo seguinte consiste em indicar à aplicação onde pode encontrar os ficheiros de origem e solicitá-los:

1. Configure o Carregador do Sencha com a localização do código fonte:

        Ext.Loader.setConfig({
            enabled : true,
            paths   : {
                'Ext'       : 'touch/src',
                'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. Solicite os ficheiros de classe do Azure:

        Ext.application({

            requires: [ 'Ext.azure.Azure' ],

            // ...

        });


3. Configurar o Azure

    O pacote do Azure é inicializado ao chamar o método **Ext.Azure.init** na secção de iniciação da aplicação. Este método transmite um objeto de configuração que contém as credenciais do serviço móvel, bem como outras credenciais e funcionalidades que pretende utilizar.

    Embora possa transmitir o objeto de configuração diretamente para o método init, sugerimos que crie uma propriedade de configuração de aplicação Sencha denominada **azure** e coloque todas as informações adequadas na mesma. Em seguida, pode transmitir este valor da propriedade ao método Ext.Azure.init.

    Quando cria um serviço móvel no Azure (consulte [Introdução ao Azure](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)), um URL e uma chave da aplicação são atribuídos a esse serviço. É necessário fornecer estas informações ao seu pacote do Azure, para que este se possa ligar ao seu serviço.

    Este exemplo mostra uma configuração e inicialização do Azure muito simples que fornece apenas o URL e a chave da aplicação:

        Ext.application({
            name: 'Basic',

            requires: [ 'Ext.azure.Azure' ],

            azure: {
                appKey: 'myazureservice-access-key',
                appUrl: 'myazure-service.azure-mobile.net'
            },

            launch: function() {

                // Call Azure initialization

                Ext.Azure.init(this.config.azure);

           }
        });

    Para obter mais informações sobre as opções de configuração do Azure, consulte a documentação da API Ext.Azure.


Parabéns! A aplicação deve agora ter acesso ao seu serviço móvel.

##Criar a aplicação ToDo

Agora que configurou a sua aplicação para incluir a extensão do Azure e lhe forneceu as credenciais do serviço móvel, podemos avançar para a criação de uma aplicação Touch que utiliza o seu serviço móvel para visualizar e editar os dados da lista ToDo armazenados no serviço.

###Configurar o proxy de dados do Azure

**Nome do ficheiro:** app/model/TodoItem.js

A aplicação Touch irá comunicar com o seu serviço móvel através de um proxy de dados. O proxy realiza todo o trabalho de enviar pedidos para o serviço móvel e receber dados do mesmo. Utilizado em combinação com um arquivo e modelo de dados do Touch, todo o trabalho pesado de processamento de dados remotos e de colocação dos mesmos na sua aplicação é removido e processado pelo próprio Touch.

Os modelos do Sencha Touch fornecem a definição dos registos de dados que irá utilizar na sua aplicação. Estes permitem não só definir os campos de dados, mas também fornecer a configuração do proxy que irá processar a comunicação entre a aplicação e o serviço móvel do Azure.

No código abaixo, pode ver que definimos os campos (e os respetivos tipos) do modelo e também fornecemos uma configuração de proxy. Ao configurar o proxy, tem de lhe atribuir um tipo (neste caso, “azure”), o nome da tabela do serviço móvel (ToDoItem) e outros parâmetros opcionais. Neste exemplo, iremos ativar a paginação de proxy para que possamos percorrer para a frente e para trás os itens da lista.

O proxy do Azure definirá automaticamente todos os cabeçalhos HTTP com as operações CRUD adequadas esperadas pela API do Azure (incluindo credenciais de autenticação, caso existam).

    Ext.define('Basic.model.TodoItem', {
        extend : 'Ext.data.Model',

        requires : [
            'Ext.azure.Proxy'
        ],

        config : {
            idProperty : 'id',
            useCache   : false,

            fields     : [
                {
                    name : 'id',
                    type : 'int'
                },
                {
                    name : 'text',
                    type : 'string'
                },
                {
                    name : 'complete',
                    type : 'boolean'
                }
            ],

            proxy : {
                type               : 'azure',
                tableName          : 'TodoItem',
                enablePagingParams : true
            }
        }
    });


###Armazenar os itens ToDo

**Nome do ficheiro**: app/store/TodoItems.js

Os arquivos do Sencha Touch são utilizados para armazenar coleções de registos de dados (modelos), que podem ser utilizadas como origens para componentes do Touch de modo a apresentar os registos de várias formas diferentes. Isto pode incluir Grelhas, Gráficos, Listas e muito mais.

Aqui, definimos um arquivo que será utilizado para armazenar todos os itens da lista ToDo do seu arquivo, que são obtidos a partir do seu serviço móvel do Azure. Repare que a configuração do arquivo contém o nome do tipo de modelo (Basic.model.TodoItem – definido acima). Isto define a estrutura dos registos que serão incluídos na loja.

Também temos algumas opções de configuração adicionais para o arquivo, tal como especificar o tamanho da página (8 registos) e que a ordenação dos registos para este arquivo seja efetuada remotamente pelo serviço móvel do Azure (não é efetuada localmente qualquer ordenação no próprio arquivo).

    Ext.define('Basic.store.TodoItems', {
        extend : 'Ext.data.Store',

        requires : [
            'Basic.model.TodoItem'
        ],

        config : {
            model        : 'Basic.model.TodoItem',
            pageSize     : 8,
            remoteSort   : true,
            remoteFilter : true
        }
    });


###Ver e editar os itens ToDo

**Nome do ficheiro**: app/view/DataItem.js

Agora que definiu a estrutura de cada item ToDo e criou um arquivo para colocar todos os registos, é necessário pensar sobre como pretendemos apresentar estas informações ao utilizador da aplicação. Normalmente, apresentamos informações ao utilizador através da utilização de **Vistas**. Uma vista pode ser qualquer um dos componentes do Touch, individualmente ou combinado com outros.

A vista abaixo é composta por um ListItem que define a forma como cada registo será apresentado juntamente com alguns botões que irão suportar ações para eliminar cada item.

    Ext.define('Basic.view.DataItem', {
        extend : 'Ext.dataview.component.ListItem',
        xtype  : 'basic-dataitem',

        requires : [
            'Ext.Button',
            'Ext.layout.HBox',
            'Ext.field.Checkbox'
        ],

        config : {
            checkbox : {
                docked     : 'left',
                xtype      : 'checkboxfield',
                width      : 50,
                labelWidth : 0
            },

            text : {
                flex : 1
            },

            button : {
                docked   : 'right',
                xtype    : 'button',
                ui       : 'plain',
                iconMask : true,
                iconCls  : 'delete',
                style    : 'color: red;'
            },

            dataMap : {
                getText : {
                    setHtml : 'text'
                },

                getCheckbox : {
                    setChecked : 'complete'
                }
            },

            layout : {
                type : 'hbox',
                align: 'stretch'
            }
        },

        applyCheckbox : function(config) {
            return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
        },

        updateCheckbox : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        },

        applyButton : function(config) {
            return Ext.factory(config, Ext.Button, this.getButton());
        },

        updateButton : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        }

    });


###Criar a vista principal

**Nome do ficheiro**: app/view/Main.js

Agora que definiu o esquema de um item da lista ToDo individual (acima), pretendemos criar uma interface de utilizador completa em torno dessa lista que define a lista efetiva de itens, um título da aplicação e um botão para adicionar uma nova tarefa.

    Ext.define('Basic.view.Main', {
        extend : 'Ext.dataview.List',
        xtype  : 'main',

        requires : [
            'Ext.TitleBar',
            'Ext.dataview.List',
            'Ext.data.Store',
            'Ext.plugin.PullRefresh',
            'Ext.plugin.ListPaging',
            'Basic.view.DataItem'
        ],

        config : {
            store : 'TodoItems',

            useSimpleItems : false,
            defaultType    : 'basic-dataitem',

            plugins : [
                {
                    xclass          : 'Ext.plugin.PullRefresh',
                    pullRefreshText : 'Pull down to refresh!'
                },
                {
                    xclass     : 'Ext.plugin.ListPaging',
                    autoPaging : true
                }
            ],

            scrollable : {
                direction     : 'vertical',
                directionLock : true
            },

            items : [
                {
                    docked : 'top',
                    xtype  : 'titlebar',
                    title  : 'Azure Mobile - Basic Data Example'
                },
                {
                    xtype  : 'toolbar',
                    docked : 'bottom',
                    items  : [
                        {
                            xtype       : 'textfield',
                            placeHolder : 'Enter new task',
                            flex        : 1
                        },
                        {
                            xtype  : 'button',
                            action : 'add',
                            text   : 'Add'
                        }
                    ]
                }
            ]
        }
    });

###Fazer tudo funcionar em conjunto

**Nome do ficheiro**: app/controller/Main.js

O passo final na nossa aplicação consiste em responder a pressões de botões (eliminar, guardar, etc.) e fornecer a lógica por trás de todos estes pedidos. O Sencha Touch utiliza controladores que escutam estes eventos e respondem em conformidade.

    Ext.define('Basic.controller.Main', {
        extend : 'Ext.app.Controller',

        config : {
            refs : {
                todoField : 'main toolbar textfield',
                main      : 'main'
            },

            control : {
                'button[action=add]'    : {
                    tap : 'onAddItem'
                },
                'button[action=reload]' : {
                    tap : 'onReload'
                },

                main : {
                    activate      : 'loadInitialData',
                    itemdoubletap : 'onItemEdit'
                },

                'basic-dataitem checkboxfield' : {
                    change : 'onItemCompleteTap'
                },

                'basic-dataitem button' : {
                    tap : 'onItemDeleteTap'
                }
            }
        },

        loadInitialData : function () {
            Ext.getStore('TodoItems').load();
        },

        onItemDeleteTap : function (button, e, eOpts) {
            var store    = Ext.getStore('TodoItems'),
                dataItem = button.up('dataitem'),
                rec      = dataItem.getRecord();

            rec.erase({
                success: function (rec, operation) {
                    store.remove(rec);
                },
                failure: function (rec, operation) {
                    Ext.Msg.alert(
                        'Error',
                        Ext.util.Format.format('There was an error deleting this task.<br/><br/>    Status Code: {0}<br/>Status Text: {1}',
                        operation.error.status,
                        operation.error.statusText)
                    );
                }
            });
        },

        onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
            var dataItem = checkbox.up('dataitem'),
                rec      = dataItem.getRecord(),
                recVal   = rec.get('complete');

            // this check is needed to prevent an issue where multiple creates get triggered from one create
            if (newVal !== recVal) {
                rec.set('complete', newVal);
                rec.save({
                    success: function (rec, operation) {
                        rec.commit();
                    },
                    failure: function (rec, operation) {
                        // since there was a failure doing the update on the server then silently reject the change
                        rec.reject(true);
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        },

        onItemEdit : function (list, index, target, record, e, eOpts) {
            var rec = list.getSelection()[0];

            Ext.Msg.prompt('Edit', 'Rename task',
                function (buttonId, value) {
                    if (buttonId === 'ok') {
                        rec.set('text', value);
                        rec.save({
                            success: function (rec, operation) {
                                rec.commit();
                            },
                            failure: function (rec, operation) {
                                // since there was a failure doing the update on the server then reject the change
                                rec.reject();
                                Ext.Msg.alert(
                                    'Error',
                                    Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                                    operation.error.status,
                                    operation.error.statusText)
                                );
                            }
                        });
                    }
                },
                null,
                false,
                record.get('text')
            );
        },

        onReload : function () {
            Ext.getStore('TodoItems').load();
        },

        onAddItem : function () {
            var me = this,
                rec,
                store = Ext.getStore('TodoItems'),
                field = me.getTodoField(),
                value = field.getValue();

            if (value === '') {
                Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
            }
            else {
                rec = Ext.create('Basic.model.TodoItem', {
                    complete : false,
                    text     : value
                });
                //store.insert(0, rec); //insert at the top
                //store.sync();
                rec.save({
                    success: function (rec, operation) {
                        store.insert(0, rec); //insert at the top
                        field.setValue('');
                    },
                    failure: function (rec, operation) {
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        }
    });

###Juntar tudo

**Nome do ficheiro**: app.js

O nosso passo final é acabar de editar o ficheiro de aplicação principal e fornecer informações sobre os modelos, os arquivos, as vistas e os controladores que foram definidos. Os ficheiros de origem destes recursos são carregados automaticamente para a aplicação. Por último, é chamado o método de início, que cria e apresenta a vista principal da aplicação “Basic.main.View”.


    Ext.Loader.setConfig({
        enabled : true,
        paths   : {
            'Ext'       : 'touch/src',
            'Ext.azure' : 'packages/azure/src'
        }
    });

    Ext.application({
        name : 'Basic',

        requires : [
            'Ext.MessageBox',
            'Ext.azure.Azure'
        ],

        views : [
            'Main'
        ],

        controllers : [
            'Main'
        ],

        stores : [
            'TodoItems'
        ],

        azure : {
            appUrl : 'YOUR_APP_URL.azure-mobile.net',
            appKey : 'YOUR_APP_KEY'
        },

        icon : {
            '57'  : 'resources/icons/Icon.png',
            '72'  : 'resources/icons/Icon~ipad.png',
            '114' : 'resources/icons/Icon@2x.png',
            '144' : 'resources/icons/Icon~ipad@2x.png'
        },

        isIconPrecomposed : true,

        startupImage : {
            '320x460'   : 'resources/startup/320x460.jpg',
            '640x920'   : 'resources/startup/640x920.png',
            '768x1004'  : 'resources/startup/768x1004.png',
            '748x1024'  : 'resources/startup/748x1024.png',
            '1536x2008' : 'resources/startup/1536x2008.png',
            '1496x2048' : 'resources/startup/1496x2048.png'
        },

        launch : function () {
            // Destroy the #appLoadingIndicator element
            Ext.fly('appLoadingIndicator').destroy();

            // Initialize Azure
            Ext.Azure.init(this.config.azure);

            // Initialize the main view
            Ext.Viewport.add(Ext.create('Basic.view.Main'));
        },

        onUpdated : function () {
            Ext.Msg.confirm(
                "Application Update",
                "This application has just successfully been updated to the latest version. Reload now?",
                function (buttonId) {
                    if (buttonId === 'yes') {
                        window.location.reload();
                    }
                }
            );
        }
    });

###Alojar e executar a aplicação Sencha Touch

A etapa final deste tutorial consiste em alojar e executar a nova aplicação no computador local.

  1. No terminal, navegue até à localização da sua aplicação deszipada.

  2. Utilizando o Sencha Cmd, execute os seguintes comandos:

    * *sencha app refresh* : este comando irá indicar ao Sencha Cmd para localizar todas as dependências da aplicação e transferir quaisquer pacotes necessários (por exemplo, [Extensões Sencha Touch para o Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)).

    * *sencha web start* : esta ação iniciará um servidor Web local para testar a nossa aplicação.

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. Abra o URL listado no terminal num browser para iniciar a aplicação (por exemplo, http://localhost:1841).

  4. Na aplicação, digite um texto significativo, tal como “Concluir o tutorial”, e, em seguida, clique em **Adicionar**.

    ![novo item todo](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas.

  5. No [Portal Clássico do Azure], clique no separador **Dados** e, em seguida, clique na tabela TodoItems.

    ![Tabela Todo Items](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    Esta ação permite procurar os dados inseridos pela aplicação na tabela.

    ![procurar tabela todo](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##Passos Seguintes
Agora que concluiu o Guia de Introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services com o Sencha.

[Transfira](https://github.com/arthurakay/sencha-touch-azure-example) um exemplo de aplicação concluído com estilos e funcionalidades adicionais para ver o que mais pode fazer o Sencha Touch!

Em seguida, obtenha mais informações sobre as Extensões do Sencha Touch para o Azure:

  * [Instruções](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters) do exemplo de aplicação
  * Obter ajuda nos [Fóruns do Sencha](http://www.sencha.com/forum)
  * Consultar a [Documentação do Sencha](http://docs.sencha.com/)
  * Utilizar o Sencha com os Mobile Services do Azure: [(vídeo)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##Recursos Adicionais

  * [Transferir o Sencha Touch](http://pages.sencha.com/touch-for-azure.html)
  * [Extensões do Sencha Touch para o Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)


##Resumo

O exemplo descrito aqui é fornecido na Extensão do Sencha Touch para o pacote do Azure e está localizado no diretório de exemplos como o exemplo Basic Data. São fornecidos mais alguns exemplos que demonstram outras funcionalidades desta extensão, juntamente com comentários e explicações detalhados.

Para obter mais informações sobre como começar a utilizar o Sencha Touch, consulte o conjunto completo de [guias](http://docs.sencha.com/touch/#!/guide)


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png

[Portal Clássico do Azure]: https://manage.windowsazure.com/


<!--HONumber=Aug16_HO1-->


