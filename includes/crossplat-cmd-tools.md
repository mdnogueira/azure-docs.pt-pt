# <a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Como utilizar as Ferramentas de Linha de Comandos do Azure para Mac e Linux
Este guia descreve como utilizar as Ferramentas de Linha de Comandos do Azure para Mac e Linux para criar e gerir serviços no Azure. Os cenários abordados incluem **instalar as ferramentas**, **importar as definições de publicação**, **criar e gerir Web Sites do Azure** e **criar e gerir Máquinas Virtuais do Azure**. Para obter documentação de referência abrangente, veja [Azure command-line tool for Mac and Linux Documentation (Documentação da ferramenta de linha de comandos do Azure para Mac e Linux)][reference-docs]. 

## <a name="table-of-contents"></a>Índice
* [O que são as Ferramentas de Linha de Comandos do Azure para Mac e Linux](#Overview)
* [Como instalar as Ferramentas de Linha de Comandos do Azure para Mac e Linux](#Download)
* [Como criar uma conta do Azure](#CreateAccount)
* [Como transferir e importar definições de publicação](#Account)
* [Como criar e gerir um Web Site do Azure](#WebSites)
* [Como criar e gerir uma Máquina Virtual do Azure](#VMs)

<h2><a id="Overview"></a>O que são as Ferramentas de Linha de Comandos do Azure para Mac e Linux</h2>

As Ferramentas da Linha de Comandos do Azure para Mac e Linux são um conjunto de ferramentas da linha de comandos para implementação e gestão de serviços do Azure.

As tarefas suportadas incluem o seguinte:

* Importar definições de publicação.
* Criar e gerir Web Sites do Azure.
* Criar e gerir Máquinas Virtuais do Azure.

Para obter uma lista completa dos comandos suportados, escreva `azure -help` na linha de comandos depois de instalar as ferramentas ou veja a [documentação de referência][reference-docs].

<h2><a id="Download">Como instalar as Ferramentas de Linha de Comandos do Azure para Mac e Linux</a></h2>

A lista seguinte contém informações para instalar as ferramentas de linha de comandos, consoante o seu sistema operativo:

* **Mac**: transfira o [Azure SDK Installer][mac-installer]. Abra o ficheiro .pkg transferido e conclua os passos de instalação pedidos.
* **Linux**: instale a versão mais recente de [Node.js][nodejs-org] (veja [Install Node.js via Package Manager [Instalar o Node.js através do Gestor de Pacotes]][install-node-linux]) e execute o comando seguinte:
  
        npm install azure-cli -g
  
    **Nota**: poderá ter de executar este comando com privilégios elevados:
  
        sudo npm install azure-cli -g
* **Windows**: execute o instalador Windows (ficheiro .msi), que está disponível em [Azure Command Line Tools (Ferramentas de Linha de Comandos do Azure)][windows-installer].

Para testar a instalação, escreva `azure` na linha de comandos. Se a instalação tiver sido concluída com êxito, verá uma lista de todos os comandos `azure` disponíveis.

<h2><a id="CreateAccount"></a>Como criar uma conta do Azure</h2>

Para utilizar as Ferramentas de Linha de Comandos do Azure para Mac e Linux, precisa de uma conta do Azure.

Abra um browser, navegue para [http://www.windowsazure.com][windowsazuredotcom] e clique em **conta gratuita**, no canto superior direito.

![Web Site do Azure][Azure Web Site]

Siga as instruções para criar uma conta.

<h2><a id="Account"></a>Como transferir e importar definições de publicação</h2>

Para começar, tem, primeiro, de transferir e importar as definições de publicação. Desta forma, poderá utilizar as ferramentas para criar e gerir os serviços do Azure. Para transferir as definições de publicação, utilize o comando `account download`:

    azure account download

É aberto o seu browser predefinido e é-lhe pedido que inicie sessão no Portal de Gestão. Depois de iniciar sessão, o ficheiro `.publishsettings` é transferido. Aponte a localização onde o ficheiro é guardado.

Em seguida, importe o ficheiro `.publishsettings`. Para tal, execute o comando seguinte, substituindo `{path to .publishsettings file}` pelo caminho para o ficheiro `.publishsettings`:

    azure account import {path to .publishsettings file}

Pode utilizar o comando <code>account clear</code> para remover todas as informações armazenadas pelo comando <code>import</code>:

    azure account clear

Para ver uma lista de opções para comandos de `account`, utilize a opção `-help`:

    azure account -help

Depois de importar as definições de publicação, deve eliminar o ficheiro `.publishsettings`, por motivos de segurança.

> [!NOTE]
> Quando importa as definições de publicação, as credenciais para aceder à sua subscrição do Azure são armazenadas dentro da sua pasta `user`. A pasta `user` é protegida pelo seu sistema operativo. Contudo, recomenda-se que tome medidas adicionais para encriptar esta pasta `user`. Pode fazê-lo das seguintes formas:    
> 
> * No Windows, modifique as propriedades da pasta ou utilize o BitLocker.
> * No Mac, ative o FileVault na pasta.
> * No Ubuntu, utilize a funcionalidade de diretório Encrypted Home. Outras distribuições de Linux oferecem funcionalidades equivalentes.
> 
> 

Está agora pronto para começar a criar e gerir Web Sites e Máquinas Virtuais do Azure.  

<h2><a id="WebSites"></a>Como criar e gerir um Web Site do Azure</h2>

### <a name="create-a-website"></a>Criar um Web Site
Para criar um Web Site do Azure, crie primeiro um diretório vazio, com o nome `MySite`, e navegue para o mesmo.

Depois, execute o comando seguinte:

    azure site create MySite --git

O resultado deste comando vai conter o URL predefinido do Web Site acabado de criar. A opção `--git` permite-lhe utilizar o git para publicar no seu Web Site através da criação de repositórios git, quer no diretório de aplicações local, quer no datacenter do Web Site. Tenha em conta que, se a sua pasta local já for um repositório git, o comando vai adicionar um remoto novo para o repositório existente, apontando para o repositório no datacenter do seu Web Site.

Note que pode executar o comando `azure site create` com qualquer uma destas opções:

* `--location [location name]`. Esta opção permite-lhe especificar a localização do datacenter no qual o Web Site é criado (por exemplo, "E.U.A. Oeste"). Se omitir esta opção, é-lhe pedido que escolha uma localização.
* `--hostname [custom host name]`. Esta opção permite-lhe especificar um nome de anfitrião personalizado para o Web Site.

Depois, pode adicionar conteúdos ao diretório do Web Site. Utilize o fluxo normal do git (`git add`, `git commit`) para consolidar os conteúdos. Utilize o comando do git seguinte para emitir os conteúdos do Web Site para o Azure: 

    git push azure master

### <a name="set-up-publishing-from-github"></a>Configurar a publicação a partir do GitHub
Para configurar a publicação contínua a partir de um repositório do GitHub, utilize a opção `--GitHub` quando estiver a criar um site:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se tiver um clone local de um repositório do GitHub ou um repositório com uma única referência remota para um repositório do GitHub, este comando publica automaticamente código do repositório do GitHub no seu site. A partir daí, todas as alterações emitidas para o repositório do GitHub são publicadas automaticamente no seu Web Site.

Quando configura a publicação a partir do GitHub, o ramo predefinido utilizado é o ramo principal. Para especificar outro ramo, execute o comando seguinte a partir do seu repositório local:

    azure site repository <branch name>

### <a name="configure-app-settings"></a>Configurar as definições da aplicação
As definições da aplicação são pares de chave-valor que estão disponíveis para a aplicação no momento de execução. Quando definidos para um Web Site do Azure, os valores das definições da aplicação substituem as definições que tenham a mesma chave que estão definidas no ficheiro Web.config do Site. Para aplicações Node.js e PHP, as definições da aplicação estão disponíveis como variáveis do ambiente. O exemplo seguinte mostra como configurar um par chave-valor:

    azure site config add <key>=<value> 

Para ver uma lista de todos os pares chaves-valor, utilize o seguinte:

    azure site config list 

Em alternativa, se souber a chave e quiser ver o valor, pode utilizar:

    azure site config get <key> 

Se pretender alterar o valor de uma chave existente, tem, primeiro, de limpar a chave existente e, depois, voltar a adicioná-la. O comando para limpar é:

    azure site config clear <key> 

### <a name="list-and-show-sites"></a>Listar e mostrar Sites
Para listar os seus Web Sites, utilize o comando seguinte:

    azure site list

Para obter informações detalhas sobre um Site, utilize o comando `site show`. O exemplo seguinte mostra detalhes relativos a `MySite`:

    azure site show MySite

### <a name="stop-start-or-restart-a-site"></a>Parar, iniciar ou reiniciar um Site
Pode parar, iniciar ou reiniciar um Site com os comandos `site stop`, `site start` ou `site restart`:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### <a name="delete-a-site"></a>Eliminar um Site
Por fim, pode eliminar um site com o comando `site delete`:

    azure site delete MySite

Tenha em conta que, se estiver a executar um dos comandos anteriores dentro da pasta onde executou `site create`, não tem de especificar o nome do site `MySite` como o último parâmetro.

Para ver uma lista completa de comandos `site`, utilize a opção `-help` :

    azure site -help 

<h2><a id="VMs"></a>Como criar e gerir uma Máquina Virtual do Azure</h2>

As Máquinas Virtuais do Azure são criadas a partir de imagens de máquinas virtuais (ficheiros .vhd) que são fornecidas por si ou que estão disponíveis na Galeria de Imagens. Para ver as imagens disponíveis, utilize o comando `vm image list`:

    azure vm image list

Pode aprovisionar e iniciar uma máquina virtual a partir de uma das imagens disponíveis com o comando `vm create`. O exemplo seguinte mostra como criar uma máquina virtual do Linux (com nome `myVM`) com base numa imagem da Galeria de Imagens (CentOS 6.2). O nome de utilizador e a palavra-passe raiz da máquina virtual são `myusername` e `Mypassw0rd`, respetivamente. (Tenha em atenção que o parâmetro `--location` especifica o datacenter no qual a máquina virtual é criada. Se omitir o parâmetro `--location`, é-lhe pedido que escolha uma localização.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Pode considerar transmitir o sinalizador `--ssh` (Linux) ou o sinalizador `--rdp` (Windows) a `vm create` para permitir ligações remotas à máquina virtual criada recentemente.

Se preferir aprovisionar uma máquina virtual a partir de uma imagem personalizada, pode criar uma imagem a partir de um ficheiro .vhd com o comando `vm image create` e, em seguida, utilizar o comando `vm create` para aprovisionar a máquina. O exemplo seguinte mostra como criar uma imagem do Linux (com o nome `myImage`) a partir de um ficheiro .vhd local. (O parâmetro `--location` especifica os dados nos quais a imagem está armazenada.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Em vez de criar uma imagem a partir de um ficheiro .vhd local, pode criá-la através de um ficheiro .vhd armazenado no Armazenamento de Blobs do Azure. Pode fazê-lo com o parâmetro `blob-url`:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Depois de criar uma imagem, pode aprovisionar uma máquina virtual a partir da imagem com `vm create`. O comando seguinte cria uma máquina virtual com o nome `myVM` a partir da imagem criada acima (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Depois de aprovisionar uma máquina virtual, poderá ser útil criar pontos finais, para permitir o acesso remoto à mesma (por exemplo). O exemplo seguinte utiliza o comando `vm create endpoint` para abrir a porta externa 22 e a porta local 22 em `myVM`:

    azure vm endpoint create myVM 22 22

Pode obter informações detalhadas sobre uma máquina virtual (incluindo o endereço IP, o nome de DNS e as informações do ponto final) com o comando `vm show`:

    azure vm show myVM

Para encerrar, iniciar ou reiniciar a máquina virtual, utilize um dos comandos seguintes:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

E, por fim, para eliminar a VM, utilize o comando `vm delete`:

    azure vm delete myVM

Para obter uma lista completa de comandos para criar e gerir máquinas virtuais, utilize a opção `-h`:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com



<!--HONumber=Jan17_HO5-->


