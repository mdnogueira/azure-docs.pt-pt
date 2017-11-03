---
title: "Especificar uma versão do Node.js"
description: "Saiba como especificar a versão do Node.js utilizados pelo Azure Web Sites e serviços em nuvem"
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Especificar uma versão do Node.js numa aplicação do Azure
Ao alojar uma aplicação Node.js, poderá querer Certifique-se de que a sua aplicação utiliza uma versão específica do Node.js. Existem várias formas de realizar esta tarefa para aplicações alojadas no Azure.

## <a name="default-versions"></a>Versões de predefinido
Versões do Node.js fornecidas pelo Azure são atualizadas constantemente. Salvo especificação em contrário, a versão predefinida que é especificada o `WEBSITE_NODE_DEFAULT_VERSION` variável de ambiente será utilizada. Para substituir este valor predefinido, siga os passos nas secções seguintes deste artigo

> [!NOTE]
> Se estiver a alojar a aplicação num serviço em nuvem do Azure (função web ou de trabalho) e é a primeira vez que implementou a aplicação, Azure tentará utilizar a mesma versão do Node.js que instalou no seu ambiente de desenvolvimento se corresponder a um t versões de predefinição putador disponíveis no Azure.
>
>

## <a name="versioning-with-packagejson"></a>Controlo de versões com Package. JSON
Pode especificar a versão do Node.js a ser utilizada ao adicionar o seguinte procedimento para sua **Package. JSON** ficheiro:

    "engines":{"node":version}

Onde *versão* é o número de versão específica a utilizar. Pode especificar as condições mais complexas para versão, tais como:

    "engines":{"node": "0.6.22 || 0.8.x"}

Uma vez que 0.6.22 não é uma das versões disponíveis no ambiente de alojamento, a versão mais recente do 0.8 série que está disponível será utilizada em vez disso, - 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Controlo de versões sites com as definições de aplicação
Se estiver a alojar a aplicação num Web site, pode definir a variável de ambiente **WEBSITE_NODE_DEFAULT_VERSION** para a versão pretendida.

## <a name="versioning-cloud-services-with-powershell"></a>Serviços de Cloud de controlo de versões com o PowerShell
Se estiver a alojar a aplicação de um serviço em nuvem e estiver a implementar a aplicação com o Azure PowerShell, pode substituir a versão do Node.js predefinida, utilizando o **conjunto AzureServiceProjectRole** cmdlet do PowerShell. Por exemplo:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Tenha em atenção de que os parâmetros na instrução acima são maiúsculas e minúsculas.  Pode verificar a versão correta do Node.js tiver sido selecionada, verificando o **motores** propriedade da função de **Package. JSON**.

Também pode utilizar o **Get-AzureServiceProjectRoleRuntime** para obter uma lista das versões de Node.js disponíveis para aplicações alojadas como um serviço em nuvem.  Verifique sempre a versão do Node.js depende do seu projeto estiver nesta lista.

## <a name="using-a-custom-version-with-azure-websites"></a>Utilizar uma versão personalizada com Web sites do Azure
Enquanto o Azure oferece várias versões de predefinição de Node.js, poderá pretender utilizar uma versão que não é fornecida por predefinição. Se a aplicação será alojada como um Web site do Azure, pode conseguir isto utilizando o **iisnode.yml** ficheiro. Os seguintes passos guiá-lo durante o processo de utilizar uma versão personalizada do Node.Js com um Web site do Azure:

1. Criar um novo diretório e, em seguida, crie um **server.js** ficheiro no diretório. O **server.js** ficheiro deve conter o seguinte:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Esta ação apresenta a versão do Node.js que está a ser utilizada quando o utilizador procura o Web site.
2. Criar um novo Web site e tome nota do nome do site. Por exemplo, a seguinte utiliza o [ferramentas da linha de comandos do Azure] para criar um novo Web site do Azure com o nome **mywebsite**e, em seguida, ative um repositório de Git para o Web site.

        azure site create mywebsite --git
3. Criar um novo diretório designado **bin** como subordinado do diretório que contém o **server.js** ficheiro.
4. Transfira a versão específica do **node.exe** (a versão do Windows) que pretende utilizar com a sua aplicação. Por exemplo, as seguintes utilizações **curl** para transferir a versão 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Guardar o **node.exe** de ficheiros para o **bin** pasta criada anteriormente.
5. Criar um **iisnode.yml** ficheiro no mesmo diretório como o **server.js** de ficheiros e, em seguida, adicione o seguinte conteúdo para o **iisnode.yml** ficheiro:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Este caminho é onde o **node.exe** ficheiros dentro do seu projeto estarão localizados assim que tiver publicado a aplicação para o Web site do Azure.
6. Publica a aplicação. Por exemplo, uma vez que criar um novo Web site com o parâmetro – git anteriormente, os seguintes comandos irão adicionar os ficheiros da aplicação para os meus repositório de Git local e, em seguida, emiti-las para o repositório de Web site:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Depois de ter publicado a aplicação, abra o Web site num browser. Deverá ver uma mensagem a indicar "Olá da versão de nó do Azure em execução: v0.8.1".

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como especificar a versão do Node.js utilizado pela sua aplicação, saiba como [funcionam com módulos], [criar e implementar um Site Web do Node.js](app-service/app-service-web-get-started-nodejs.md), e [como utilizar o Azure Ferramentas de linha de comandos para Mac e Linux].

Para obter mais informações, consulte o [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/).

[como utilizar o Azure Ferramentas de linha de comandos para Mac e Linux]:cli-install-nodejs.md
[ferramentas da linha de comandos do Azure]:cli-install-nodejs.md
[funcionam com módulos]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
