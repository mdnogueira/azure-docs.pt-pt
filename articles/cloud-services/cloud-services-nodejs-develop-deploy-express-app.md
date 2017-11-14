---
title: "Aplicação rápidas (Node.js) Web | Microsoft Docs"
description: "Um tutorial que cria o tutorial do serviço de nuvem e demonstra como utilizar o módulo de rápida."
services: cloud-services
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: 
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: aa62438732db9e8df129440e6949858d9a61f65f
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="build-a-nodejs-web-application-using-express-on-an-azure-cloud-service"></a>Criar uma aplicação de web do Node.js utilizando rápida num serviço em nuvem do Azure
NODE.js inclui um conjunto mínimo de funcionalidades em tempo de execução principal.
Os programadores utilizam frequentemente 3rd módulos de terceiros para fornecer funcionalidades adicionais quando desenvolver uma aplicação Node.js. Neste tutorial, irá criar uma nova aplicação utilizando o [Express] [ Express] módulo, que fornece uma arquitetura MVC para criar aplicações web do Node.js.

Abaixo é uma captura de ecrã da aplicação concluída:

![Um browser a apresentar bem-vindo ao rápidas no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço em nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute os seguintes passos para criar um novo projeto de serviço de nuvem com o nome 'expressapp':

1. Do **Menu Iniciar** ou **ecrã Iniciar**, procure **do Windows PowerShell**. Por fim, clique com botão direito **do Windows PowerShell** e selecione **executar como administrador**.
   
    ![Ícone do PowerShell do Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Altere os diretórios para o **c:\\nó** diretório e, em seguida, introduza os seguintes comandos para criar uma nova solução com o nome **expressapp** e uma função da web com o nome **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Por predefinição, **Add-AzureNodeWebRole** utiliza uma versão antiga do Node.js. O **conjunto AzureServiceProjectRole** acima da declaração dá instruções ao Azure a utilizar v0.10.21 do nó.  Tenha em atenção de que os parâmetros são maiúsculas e minúsculas.  Pode verificar a versão correta do Node.js tiver sido selecionada, verificando o **motores** propriedade no **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Instalação rápida
1. Instale o gerador do Express através da emissão do comando seguinte:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    O resultado do comando npm deve ter um aspeto semelhante ao resultado abaixo. 
   
    ![Windows PowerShell que apresenta o resultado de npm instalar comando rápido.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o **WebRole1** diretório e utilize o comando rápido para gerar uma nova aplicação:
   
        PS C:\node\expressapp\WebRole1> express
   
    Será solicitado para substituir a aplicação anterior. Introduza **y** ou **Sim** para continuar. Express irá gerar o ficheiro app.js e uma estrutura de pasta para criar a sua aplicação.
   
    ![O resultado do comando rápido](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no ficheiro de Package. JSON, introduza o seguinte comando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![O resultado de npm comando de instalação](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Utilize o seguinte comando para copiar o **bin/www** do ficheiro para **server.js**. Isto é, para que o serviço em nuvem pode encontrar o ponto de entrada para esta aplicação.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Depois de concluída este comando, deve ter um **server.js** ficheiro no diretório WebRole1.
5. Modificar o **server.js** para remover um do '.' carateres da linha seguinte.
   
       var app = require('../app');
   
   Depois de efetuar esta alteração, a linha deverá aparecer da seguinte forma.
   
       var app = require('./app');
   
   Esta alteração é necessária uma vez que o ficheiro foi movido (anteriormente **bin/www**,) para o mesmo diretório que o ficheiro de aplicação que está a ser necessário. Depois de efetuar esta alteração, guarde o **server.js** ficheiro.
6. Utilize o seguinte comando para executar a aplicação no emulador do Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Uma página web que contém bem-vindo ao express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificar a vista
Agora a modificar a vista para apresentar a mensagem "Bem-vindo ao rápidas no Azure".

1. Introduza o seguinte comando para abrir o ficheiro Index. jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![O conteúdo do ficheiro Index. jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade é o motor de vista de predefinido utilizado por aplicações rápida. Para obter mais informações sobre o motor de vista Jade, consulte [http://jade-lang.com][http://jade-lang.com].
2. Modificar a última linha de texto, acrescentando **no Azure**.
   
   ![O ficheiro Index. jade, a última linha lê: p bem-vindo ao \#{title} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Guarde o ficheiro e saia do bloco de notas.
4. Atualize o browser e verá as suas alterações.
   
   ![Uma janela do browser, a página contém bem-vindo ao rápidas no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar a aplicação, utilize o **Stop-AzureEmulator** cmdlet para parar o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicar a aplicação no Azure
Na janela do PowerShell do Azure, utilize o **Publish-AzureServiceProject** cmdlet para implementar a aplicação para um serviço em nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Uma vez concluída a operação de implementação, o seu browser abrir e apresentar a página web.

![Um browser a apresentar a página de rápida. O URL indica que agora está a ser alojado no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](/develop/nodejs/).

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


