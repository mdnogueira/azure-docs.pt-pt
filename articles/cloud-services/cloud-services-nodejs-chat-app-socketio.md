---
title: "Aplicação node.js com Socket.io | Microsoft Docs"
description: "Saiba como utilizar socket.io numa aplicação node.js alojada no Azure."
services: cloud-services
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tarcher
ms.openlocfilehash: a85d4348a13b79b5b7542362de9956aa3398375a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Criar uma aplicação de Node.js Chat com Socket.IO num serviço em nuvem do Azure
Socket.IO fornece em tempo real comunicação entre o entre o servidor de node.js e clientes. Este tutorial irá guiá-lo através de alojamento um socket. E/s baseado em aplicações de chat no Azure. Para obter mais informações sobre Socket.IO, consulte <http://socket.io/>.

Abaixo é uma captura de ecrã da aplicação concluída:

![Uma janela do browser a apresentar o serviço alojado no Azure][completed-app]  

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que os seguintes produtos e versões estão instaladas para concluir com êxito o exemplo neste artigo:

* Instalar o [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar o [Node. js](https://nodejs.org/download/)
* Instalar [Python versão 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço em nuvem
Os seguintes passos criar o projeto de serviço em nuvem que irá alojar a aplicação de Socket.IO.

1. Do **Menu Iniciar** ou **ecrã Iniciar**, procure **do Windows PowerShell**. Por fim, clique com botão direito **do Windows PowerShell** e selecione **executar como administrador**.
   
    ![Ícone do PowerShell do Azure][powershell-menu]
2. Criar um diretório denominado **c:\\nó**. 
   
        PS C:\> md node
3. Altere os diretórios para o **c:\\nó** diretório
   
        PS C:\> cd node
4. Introduza os seguintes comandos para criar uma nova solução com o nome **chatapp** e uma função de trabalho com o nome **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Verá a seguinte resposta:
   
    ![A saída do novo-azureservice e azurenodeworkerrolecmdlets adicionar](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Transferir o exemplo de Chat
Para este projeto, utilizamos o exemplo de chat do [repositório do Socket.IO GitHub]. Execute os seguintes passos para transferir o exemplo e adicione-ao projeto que criou anteriormente.

1. Criar uma cópia local do repositório utilizando o **Clone** botão. Também pode utilizar o **ZIP** botão para transferir o projeto.
   
   ![Uma janela do browser ver https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de transferência ZIP realçado][chat-example-view]
2. Navegue até a estrutura de diretórios do repositório local até chegar ao **exemplos\\chat** diretório. Copie o conteúdo deste diretório para o **c:\\nó\\chatapp\\WorkerRole1** diretório que criou anteriormente.
   
   ![Explorador, que apresenta o conteúdo dos exemplos\\diretório de chat extraído de arquivo][chat-contents]
   
   Os itens realçados na captura de ecrã acima são os ficheiros copiados de **exemplos\\chat** diretório
3. No **c:\\nó\\chatapp\\WorkerRole1** diretório, eliminar a **server.js** de ficheiros e, em seguida, renomeie o **app.js** do ficheiro para **server.js**. Esta ação remove a predefinição **server.js** ficheiro criado anteriormente pelo **adicionar AzureNodeWorkerRole** cmdlet e substitui-lo com o ficheiro de aplicação do exemplo chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar Server.js e instalar os módulos
Antes de testar a aplicação no emulador do Azure, mas tem de se algumas alterações secundárias. Execute os seguintes passos para o ficheiro de server.js:

1. Abra o **server.js** ficheiro no Visual Studio ou num editor de texto.
2. Localizar o **dependências de módulo** secção no início de server.js e altere a linha que contém **sio = require('..//..lib//socket.IO')** para **sio = require('socket.io')** conforme mostrado abaixo:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Certifique-se de que a aplicação escuta na porta correta, abra server.js no bloco de notas ou o seu editor favorito e, em seguida, altere a linha seguinte, substituindo **3000** com **process.env** conforme mostrado abaixo:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Depois de guardar as alterações ao **server.js**, utilize os seguintes passos para instalar os módulos necessários e, em seguida, testar a aplicação no emulador do Azure:

1. Utilizar **Azure PowerShell**, altere os diretórios para o **c:\\nó\\chatapp\\WorkerRole1** diretório e utilize o seguinte comando para instalar os módulos necessários para esta aplicação:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Esta ação instalará os módulos listados no ficheiro Package. JSON. Depois do comando for concluído, deverá ver um resultado semelhante ao seguinte:
   
   ![O resultado de npm comando de instalação][The-output-of-the-npm-install-command]
2. Uma vez que este exemplo foi originalmente parte do repositório do Socket.IO GitHub e diretamente referenciada biblioteca Socket.IO pelo caminho relativo, Socket.IO não foi referenciado no ficheiro Package. JSON, pelo que iremos deve instalá-lo através da emissão do comando seguinte:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testar e implementar
1. Inicie o emulador ao emitir o comando seguinte:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Se ocorrerem problemas com iniciar emulador, ex.: início AzureEmulator: Ocorreu uma falha inesperada.  Detalhes: Encontrou um erro inesperado o objeto de comunicação, System.ServiceModel.Channels.ServiceChannel, não pode ser utilizado para comunicação porque se encontra no estado falhado.
   
      reinstalar AzureAuthoringTools v 2.7.1 e AzureComputeEmulator v 2.7 - Certifique-se de que a versão corresponde.
   >
   >


2. Abra um browser e navegue para **http://127.0.0.1**.
3. Quando abre a janela do browser, introduza uma alcunha e, em seguida, clique em enter.
   Isto permitirá publicar mensagens como uma alcunha específica. Para testar a funcionalidade de vários utilizador, abra janelas do browser adicionais utilizando o mesmo URL e introduza nicknames diferentes.
   
   ![Duas janelas do browser que apresenta as mensagens de chat de Utilizador1 e o Utilizador2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Após a aplicação de teste, pare o emulador ao emitir o comando seguinte:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Para implementar a aplicação no Azure, utilize o **Publish-AzureServiceProject** cmdlet. Por exemplo:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Certifique-se de utilizar um nome exclusivo, caso contrário, o processo de publicação falhará. Depois de concluída a implementação, o browser abrir e navegue para o serviço implementado.
   > 
   > Se receber um erro a indicar que o nome de subscrição fornecido não existe no perfil de publicação importados, tem de transferir e importar o perfil de publicação para a sua subscrição antes de implementar no Azure. Consulte o **implementar a aplicação no Azure** secção [criar e implementar uma aplicação Node.js num serviço em nuvem do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Uma janela do browser a apresentar o serviço alojado no Azure][completed-app]
   
   > [!NOTE]
   > Se receber um erro a indicar que o nome de subscrição fornecido não existe no perfil de publicação importados, tem de transferir e importar o perfil de publicação para a sua subscrição antes de implementar no Azure. Consulte o **implementar a aplicação no Azure** secção [criar e implementar uma aplicação Node.js num serviço em nuvem do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

A aplicação está agora em execução no Azure e pode reencaminhar mensagens de chat entre diferentes clientes com o Socket.IO.

> [!NOTE]
> De simplicidade, este exemplo está limitado a chatting entre utilizadores ligados à instância do mesma. Isto significa que se o serviço em nuvem cria duas instâncias de função de trabalho, os utilizadores só conseguirão para falar no chat com outros utilizadores ligados à mesma instância de função de trabalho. Dimensionar a aplicação para trabalhar com várias instâncias de função, pode utilizar uma tecnologia, como o Service Bus para partilhar o estado de arquivo Socket.IO em instâncias. Para obter exemplos, consulte os exemplos de utilização de tópicos e filas do Service Bus no [Azure SDK para o repositório do Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu como criar uma aplicação de chat básico alojada num serviço em nuvem do Azure. Para saber como alojar esta aplicação num Web site do Azure, consulte [compilar uma aplicação de Chat Node.js com Socket.IO no Web Site um Azure][chatwebsite].

Para obter mais informações, consulte também o [Centro para programadores do Node.js](/develop/nodejs/).

[chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

[Azure SLA]: http://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[repositório do Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


